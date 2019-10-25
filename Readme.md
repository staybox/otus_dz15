# OTUS ДЗ 15 Docker  (Centos 7)
-----------------------------------------------------------------------
### Домашнее задание

1.Создайте свой кастомный образ nginx на базе alpine. После запуска nginx должен отдавать кастомную страницу (достаточно изменить дефолтную страницу nginx)

2.Определите разницу между контейнером и образом. Вывод опишите в домашнем задании.

3.Ответьте на вопрос: Можно ли в контейнере собрать ядро?

4.Собранный образ необходимо запушить в docker hub и дать ссылку на ваш репозиторий.

5.Создайте кастомные образы nginx и php, объедините их в docker-compose.

6.После запуска nginx должен показывать php info.

7.Все собранные образы должны быть в docker hub.

#### Установка Docker

- Устаналиваем пререквизиты ```yum install -y yum-utils device-mapper-persistent-data lvm2```
- Добавляем репозиторий ```yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo```
- Устанавливаем Docker и дополнительные компоненты ```yum install docker-ce docker-ce-cli containerd.io```
- Устаналиваем в автозагрузку и стартуем Docker ```systemctl enable docker; systemctl start docker```

Данный алгоритм установки взять с официального сайта Docker.

#### Установка Docker-compose

- ```curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose```
- ```chmod +x /usr/local/bin/docker-compose```
- ```docker-compose --version```

#### Теоретические вопросы

1. Определите разницу между контейнером и образом. Вывод опишите в домашнем задании.
- Есть несколько объяснений, а) Образ это аналог образа виртуальной машины, а контейнер это экземпляр этого образа, б) Образ это как исполняемый файл, а контейнер как процесс 

2. Ответьте на вопрос: Можно ли в контейнере собрать ядро?
- Можно собрать ядро, но загрузиться с него нельзя. [Пример сборки ядра в докере]


#### Практическая часть

1. Создаем свой кастомный образ на базе alpine

- Создаем отдельную директорию и кладем туда два файла:

а) Создаем файл Dockerfile (во вложении)

б) Создаем файл index.html с измененным содержимым (этот файл будет импортирован в наш образ при создании) (во вложении)

- После чего собираем наш образ командой ```docker build -t staybox/myimages:nginx_v1 .```

- Далее можем запустить наш контейнер командой ```docker run -d -p 5555:80 staybox/myimages:nginx_v1```

- Видим наш образы и наши контейнеры:
```
[root@borg-server docker]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx/staybox_v1    latest              33178a165a8f        2 hours ago         196MB
staybox/dev-nginx   latest              33178a165a8f        2 hours ago         196MB
staybox/myimages    latest              33178a165a8f        2 hours ago         196MB
staybox/myimages    nginx_v1            33178a165a8f        2 hours ago         196MB
nginx               staybox             33178a165a8f        2 hours ago         196MB
nginx_staybox_v1    latest              33178a165a8f        2 hours ago         196MB
staybox_v1          latest              33178a165a8f        2 hours ago         196MB
alpine              latest              965ea09ff2eb        3 days ago          5.55MB
```
```
[root@borg-server docker]# docker ps
CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS              PORTS                           NAMES
c14f19a0e8bc        staybox/myimages:nginx_v1   "nginx -g 'daemon of…"   45 seconds ago      Up 44 seconds       443/tcp, 0.0.0.0:5555->80/tcp   fervent_stonebraker
e8d1db428791        nginx:staybox               "nginx -g 'daemon of…"   2 hours ago         Up 2 hours          80/tcp, 0.0.0.0:1235->443/tcp   fervent_gould
b2c430780169        nginx:staybox               "nginx -g 'daemon of…"   2 hours ago         Up 2 hours          443/tcp, 0.0.0.0:1234->80/tcp   objective_montalcini
77495a69c344        nginx:staybox               "nginx -g 'daemon of…"   2 hours ago         Up 2 hours          80/tcp, 443/tcp                 infallible_khayyam
```

2. Собранный образ необходимо запушить в docker hub и дать ссылку на ваш репозиторий

- Выполняем команду ```docker login```, и вводим логин и пароль от нашего зарегистрированного аккаунта из docker-hub:
```
[root@borg-server docker]# docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: staybox
Password: 
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```
- Далее делаем push ```docker push staybox/myimages:nginx_v1```:
```
[root@borg-server docker]# docker push staybox/myimages:nginx_v1
The push refers to repository [docker.io/staybox/myimages]
935b644ef156: Layer already exists 
f66ed9d831c9: Layer already exists 
ebba2f381e58: Layer already exists 
9b232e9f1f45: Layer already exists 
77cae8ab23bf: Layer already exists 
nginx_v1: digest: sha256:7168817441492feee8733350a83ad1cd8322ebb60601c52687439f254ecd6159 size: 1365
```

3. Создайте кастомные образы nginx и php, объедините их в docker-compose (файлы во вложении)

- Переходим туда где у нас храниться файл docker-compose.yml и выполняем:
```
docker-compose up -d
curl localhost:8080
```


[Пример сборки ядра в докере]:https://github.com/moul/docker-kernel-builder