# KubernetesForDevOps
Book Kubernetes For DevOps

#####запускаем приложение hello стр. 38
https://github.com/cloudnativedevops/demo/tree/master/hello

`docker image build -t myhello .`

`docker container run -p 9999:8888 myhello`


####Загрузка своего образа в dockerhub
######регистрируюсь https://hub.docker.com/ стр.41
`docker login` - команда для локального докера на вход в dockerhub

######переименновываю контейнер
`docker image tag myhello sevod/myhello`

######Отправляю все в dockerhub
docker image push sevod/myhello

######Запуск из dockerhub
docker container run -p 9999:8888 sevod/myhello


####Устанавливаю minikube стр.42
https://kubernetes.io/ru/docs/tasks/tools/install-minikube/

######Запуск minikube
`minikube start --vm-driver=docker`

######Проверка состояния
`minikube status`

######Запускаем контейнер уже в minikube
kubectl run demo --image=sevod/myhello --port=9999 --labels app=demo

######создаем деплоймент (оригинальная команад из книги не сработала, это дополнительно)
kubectl create deployment demo --image=sevod/myhello

######Пробрасываем порт
kubectl port-forward deploy/demo 9999:8888
