# KubernetesForDevOps
Book Kubernetes For DevOps

######запуск докер демона
`sudo /etc/init.d/docker start`

#####запускаем приложение hello стр. 38
https://github.com/cloudnativedevops/demo/tree/master/hello

`docker image build -t myhello .`

`docker container run -p 9999:8888 myhello`

###Загрузка своего образа в dockerhub
######регистрируюсь https://hub.docker.com/ стр.41
`docker login` - команда для локального докера на вход в dockerhub

######переименновываю контейнер
`docker image tag myhello sevod/myhello`

######Отправляю все в dockerhub
docker image push sevod/myhello

######Запуск из dockerhub
docker container run -p 9999:8888 sevod/myhello


###Устанавливаю minikube стр.42
https://kubernetes.io/ru/docs/tasks/tools/install-minikube/

######Запуск minikube
`minikube start --vm-driver=docker`

######Проверка состояния
`minikube status`

######Запускаем контейнер уже в minikube
`kubectl run demo --image=sevod/myhello --port=9999 --labels app=demo`

######создаем деплоймент (оригинальная команад из книги не сработала, это дополнительно)
`kubectl create deployment demo --image=sevod/myhello`

######Пробрасываем порт
`kubectl port-forward deploy/demo 9999:8888`

###deployment 
объект который следит за работой контейнеров, запускает, добавляет, удаляет. Работает не на прямую, а через ReplicaSet

`kubectl get deployments` посмотреть деплойменты 

`kubectl describe deployments/demo` информация о конкретном деплойменте

######ReplicaSet контроллер который непосредственно управляет pod's
Отвечает за группу идентичных pod-оболочек (или реплик).

###ручные операции с pod
######Узнать запущенные поды
`kubectl get pods`

`kubectl get pods --selector app=demo`

#####Удалить поды
`kubectl delete pods --selector app=demo`

#####Удалить все по селектору
`kubectl delete all --selector app=demo`

###Манифесты ресурсов в формате YAML
#####Запуск с YAML файла. 
Файлы тут https://github.com/cloudnativedevops/demo/tree/master/hello-k8s

`kubectl apply -f k8s/deployment.yaml` это мы запустили deploy

`kubectl apply -f k8s/service.yaml`   а это service

`kubectl port-forward service/demo 9999:8888`   пробрасываем порт, иначе не заработает

`kubectl delete -f k8s/`    удаляем все. Удаляются только то, что находится в конфигах в папке k8s


###Ресурсы
#####Пример запроса ресурсов (memory cpu)
````
spec:
containers:
- name: demo
  image: cloudnatived/demo:hello
  ports:
- containerPort: 8888
  resources:
  requests:
  memory: "10Mi"
  cpu: "100m"
````


###Проверки работоспособности
#####HTTP контрейнер
````
livenessProbe:
    httpGet:
        path: /healthz
        port: 8888
    initialDelaySeconds: 3
    periodSeconds: 3
````

#####проверка по TCP
````
livenessProbe:
    tcpSocket:
        port: 8888
````

#####Выполнение произвольной команды (должен вернуть нулевой статус)
````
readinessProbe:
    exec:
        command:
        - cat
        - /tmp/healthy
````

#####grpc-health-probe для проверки приложений на gRPC


###Проверки готовности

#####в данном случае полностью аналогично проверки работоспособности
````
readinessProbe:
    httpGet:
        path: /healthz
        port: 8888
    initialDelaySeconds: 3
    periodSeconds: 3
````

#####Проверки готовности на основе файла
Приложение может создать файл когда будет готово, а кубернетис проверить это через `exec`

#####Поле minReadySeconds

#####Ресурс PodDisruptionBudget
можно указать, сколько pod-оболочек заданного приложения допустимо к потере в любой момент времени.
######minAvailable
минимальное количество подов, которе оставит кубернетис при выселениее
````
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
	name: demo-pdb
spec:
	minAvailable: 3
	selector:
		matchLabels:
 			app: demo
````
######maxUnavailable
максимальное разрешенное количество для выселения
````
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
	name: demo-pdb
spec:
	maxUnavailable: 10%
	selector:
	matchLabels:
		app: demo
````