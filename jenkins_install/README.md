### Jenkins docker install

### debug docker conteiner
```bash
docker exec -it DOCKER_NAME bash
```

### Jenkins official script
- не работает на сервере, выдает ошибку **client sent an http request to an https server**. вроде как ее можно [пофиксить выставив правильные сертификаты](https://davelms.medium.com/run-jenkins-in-a-docker-container-part-1-docker-in-docker-7ca75262619d), но у меня сейчас нет времени с этим возиться.
```bash
docker run --name jenkins-docker --rm --detach \
  --privileged --network jenkins --network-alias docker \
  --env DOCKER_TLS_CERTDIR=/certs \
  --volume jenkins-docker-certs:/certs/client \
  --volume jenkins-data:/var/jenkins_home \
  --publish 8080:2376 \
  docker:dind --storage-driver overlay2
```


### Рабочий скрипт установки Jenkins из книги
- оказалось что он имеет недостатки, так как мы не можем с такой конфигурацией запускать докер контейнер из докер контейнера.
```bash
mkdir $HOME/jenkins_home
sudo chown -R 1000:1000 $HOME/jenkins_home
docker run -d -p 8080:8080 -v $HOME/jenkins_home:/var/jenkins_home --name jenkins jenkins/jenkins
```

### Создание jenkins образа который способен запускать докер изнутри докера
- https://www.tiuweehan.com/blog/2020-09-10-docker-in-jenkins-in-docker/
- теперь используется более удобный вариант установки докера - через docker compose
- с ним возникает следующая ошибка https://stackoverflow.com/questions/72990497/getting-glibc-2-32-and-glibc-2-34-not-found-in-jenkins-docker-with-dind-on - решением на stack overflow решением не является
- docker: /lib/x86_64-linux-gnu/libc.so.6: version `GLIBC_2.32' not found (required by docker)
- данный код выполнялся на ubuntu 22.04, у него GLIBC версии 2.35, причем версии для ubuntu (сам образ jenkins собран на образе debian, вероятно он пытается найти 2.32 для него, но не может найти в самой системе). Видел на форумах что может помочь установка ubuntu 18.04.
- в общем 5 часов дебага и переустановок с разных форумов ни к чему не привела.
```bash
mkdir $HOME/jenkins_home
sudo chown -R 1000:1000 $HOME/jenkins_home
docker-compose up -d
```
apt-get install libc-bin=2.31 libc6=2.31