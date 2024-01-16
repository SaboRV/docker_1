## 1. Установите Docker на хост машину https://docs.docker.com/engine/install/ubuntu/
## 2. Установите Docker Compose - как плагин, или как отдельное приложение
## 3. Создайте свой кастомный образ nginx на базе alpine. После запуска nginx должен отдавать кастомную страницу (достаточно изменить дефолтную страницу nginx)
## 4. Определите разницу между контейнером и образом
## 5. Вывод опишите в домашнем задании.
## 6. Ответьте на вопрос: Можно ли в контейнере собрать ядро?

## Собранный образ необходимо запушить в docker hub и дать ссылку на ваш репозиторий.

Решение

Установим в VirtualBox ОС Сервер Ubuntu.
root@serverub:~# uname -a
Linux serverub 5.15.0-91-generic #101-Ubuntu SMP Tue Nov 14 13:30:08 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux

#### 1 и 2. Установим Docker и Docker compose.
root@serverub:~# sudo apt-get update
Hit:1 http://de.archive.ubuntu.com/ubuntu jammy InRelease
Hit:2 http://de.archive.ubuntu.com/ubuntu jammy-updates InRelease
Hit:3 http://de.archive.ubuntu.com/ubuntu jammy-backports InRelease
Hit:4 http://de.archive.ubuntu.com/ubuntu jammy-security InRelease
Reading package lists... Done
root@serverub:~# sudo apt-get install ca-certificates curl gnupg
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
ca-certificates is already the newest version (20230311ubuntu0.22.04.1).
ca-certificates set to manually installed.
curl is already the newest version (7.81.0-1ubuntu1.15).
curl set to manually installed.
gnupg is already the newest version (2.2.27-3ubuntu2.1).
gnupg set to manually installed.
0 upgraded, 0 newly installed, 0 to remove and 47 not upgraded.
root@serverub:~# sudo install -m 0755 -d /etc/apt/keyrings
root@serverub:~# curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
root@serverub:~# echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
root@serverub:~# sudo apt-get update
Hit:1 http://de.archive.ubuntu.com/ubuntu jammy InRelease                      
Get:2 https://download.docker.com/linux/ubuntu jammy InRelease [48.8 kB]       
Hit:3 http://de.archive.ubuntu.com/ubuntu jammy-updates InRelease
Hit:4 http://de.archive.ubuntu.com/ubuntu jammy-backports InRelease
Hit:5 http://de.archive.ubuntu.com/ubuntu jammy-security InRelease
Get:6 https://download.docker.com/linux/ubuntu jammy/stable amd64 Packages [23.1 kB]
Fetched 72.0 kB in 1s (65.8 kB/s)   
Reading package lists... Done

#### Запустим установку docker командой:
#### sudp apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

Проведем необходимые настройки:
root@serverub:~# sudo usermod -aG docker seru
root@serverub:~# sudo systemctl enable docker.service
sudo systemctl enable containerd.service
Synchronizing state of docker.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable docker

Перезагрузим сервер.
Проверим версию установленных docker и docker-compose:
root@serverub:~# docker version
Client: Docker Engine - Community
 Version:           24.0.7
 API version:       1.43
 Go version:        go1.20.10
 Git commit:        afdd53b
 Built:             Thu Oct 26 09:07:41 2023
 OS/Arch:           linux/amd64
 Context:           default

Server: Docker Engine - Community
 Engine:
  Version:          24.0.7
  API version:      1.43 (minimum version 1.12)
  Go version:       go1.20.10
  Git commit:       311b9ff
  Built:            Thu Oct 26 09:07:41 2023
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.27
  GitCommit:        a1496014c916f9e62104b33d1bb5bd03b0858e59
 runc:
  Version:          1.1.11
  GitCommit:        v1.1.11-0-g4bccb38
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0

root@serverub:~# docker compose version
Docker Compose version v2.21.0

#### 3.

Подготовим два файла Dockerfile и index.html.

В файл Dockerfile скопируем последнюю версию nginx:1.25.3-alpine-slim с сайта git.hub
В файл index.html добавим следующее:
<!DOCTYPE html>
<html>
<head>
<title>Welcome to Otus!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to Otus!</h1>
<p>OTUS</p>

<p>For online documentation and support please refer to
<a href="https://otus.ru/"></a>https://otus.ru.<br/>

<p><em>Thank you for using Otus.</em></p>
</body>

Запускаем:
root@serverub:~/dock# docker build -t nginx-custom .
root@serverub:~/dock# docker run -p 80:80 -dt nginx-custom

Проверяем: http://192.168.56.125
См. Pic 1

#### 4. Определите разницу между контейнером и образом

Контейнер - это, в конечном счете, просто образ. При создании контейнера поверх образа добавляет слой, доступный для записи, что позволяет менять его по своему усмотрению. Образ - это шаблон, на основе которого создается контейнер, существует отдельно и не может быть изменен.

#### 6. Можно ли в контейнере собрать ядро?
В Docker-контейнере можно собрать ядро с произвольными патчами, флагами конфигурации и тегом, например, repository на сайте Docker Hub для сборки ядра Debian по ссылкам:
hub https://hub.docker.com/r/tomzo/buildkernel
git https://github.com/tomzo/docker-kernel-ide

## Собранный образ необходимо запушить в docker hub и дать ссылку на ваш репозиторий.

Заходим на сайт https://hub.docker.com/signup и регистрируемся.
Далее запускаем команды:

#### root@serverub:~/dock# docker tag bb0 saborv/my-repo:nginx-custom-image
#### root@serverub:~/dock# docker images
REPOSITORY       TAG                  IMAGE ID       CREATED             SIZE
nginx-custom     latest               bb0d133ba90c   About an hour ago   47.6MB
saborv/my-repo   nginx-custom-image   bb0d133ba90c   About an hour ago   47.6MB
<none>           <none>               b54b62e0afac   About an hour ago   45.8MB
nginx            latest               a8758716bb6a   2 months ago        187MB

Надо залогинится:

#### root@serverub:~/dock# docker login -u saborv -p XXXXXXXXX
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded

Потом можно настроить правильную идентификацию на сайт hub.docker.com.

#### root@serverub:~/dock# docker push saborv/my-repo:nginx-custom-image
The push refers to repository [docker.io/saborv/my-repo]
833b201aa9bc: Pushed
3c32735036fa: Pushed
9770295d804c: Mounted from library/nginx
62e59aa00d24: Mounted from library/nginx
769b844042ad: Mounted from library/nginx
4c6b2fc6378f: Mounted from library/nginx
c612d245f985: Mounted from library/nginx
3d49ee199a5c: Mounted from library/nginx
9fe9a137fd00: Mounted from library/nginx
nginx-custom-image: digest: sha256:fb9a3413fa2664b038a18383272cfde37da667fc176922e70ed484515fd20958 size: 2196

В итоге видим на сайте: Pic 2.
