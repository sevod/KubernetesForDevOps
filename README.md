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
