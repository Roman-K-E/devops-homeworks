### Задача 1.
Сценарий выполнения задачи:  
+ создайте свой репозиторий на https://hub.docker.com;
+ выберите любой образ, который содержит веб-сервер Nginx;
+ создайте свой fork образа;
+ реализуйте функциональность: запуск веб-сервера в фоне с индекс-страницей, содержащей HTML-код ниже.
```
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I’m DevOps Engineer!</h1>
</body>
</html>
```

### Решение.
Репозиторий создан.   https://hub.docker.com/r/romanke/image_nginx_5-virt-3-task-1  
Выбран образ nginx и скачан в Ubuntu 20.04 с установленным Docker. 
Собран свой образ, с учетом поставленной задачи, и выгружен в репозиторий:  
**romanke/image_nginx_5-virt-3-task-1**  
Срабатывание показано на скриншоте:


Проверить срабатывание можно его скачиванием и запуском с открытием 80 порта:  
**docker run -d -p 80:80 romanke/image_nginx_5-virt-3-task-1** 

---
### Задача 2. 
*Посмотрите на сценарий ниже и ответьте на вопрос: «Подходит ли в этом сценарии использование Docker-контейнеров или лучше подойдёт виртуальная машина, физическая машина? Может быть, возможны разные варианты?»*

### Решение.
**Высоконагруженное монолитное Java веб-приложение.** Подходят все перечисленные варианты виртуализации.  
**Nodejs веб-приложение.**  Контейнеры. Проще развертывать приложения и использовать микросервисную архитектуру.  
**Мобильное приложение c версиями для Android и iOS.**  Только виртуальные машины или аппаратные серверы.  
**Шина данных на базе Apache Kafka.**  Виртуальные машины.  
**Elasticsearch-кластер для реализации логирования продуктивного веб-приложения — три ноды elasticsearch, два logstash и две ноды kibana.** Виртуальные машины или контейнеры.  
**Мониторинг-стек на базе Prometheus и Grafana.** Виртуальные машины или контейнеры.  
**MongoDB как основное хранилище данных для Java-приложения.** Все способы виртуализации.  
**Gitlab-сервер для реализации CI/CD-процессов и приватный (закрытый) Docker Registry.**   Gitlab в любом варианте. Docker registry поставляется в контейнере.  

---
### Задача 3. 
+ Запустите первый контейнер из образа centos c любым тегом в фоновом режиме, подключив папку /data из текущей рабочей директории на хостовой машине в /data контейнера.
+ Запустите второй контейнер из образа debian в фоновом режиме, подключив папку /data из текущей рабочей директории на хостовой машине в /data контейнера.
+ Подключитесь к первому контейнеру с помощью docker exec и создайте текстовый файл любого содержания в /data.
+ Добавьте ещё один файл в папку /data на хостовой машине.
+ Подключитесь во второй контейнер и отобразите листинг и содержание файлов в /data контейнера.

### Решение.
Привожу полный ход выполнения (file1 пустой, file2 содержит данные).
```
root@ub1:/home/r/docker# ls -lha data
total 8.0K
drwxr-xr-x 2 root root 4.0K Apr  7 19:03 .
drwxr-xr-x 3 root root 4.0K Apr  7 19:03 ..
root@ub1:/home/r/docker# docker run -it --rm -d --name 1_cont_centos_for_task3 -v /data/:/data/ centos
Unable to find image 'centos:latest' locally
latest: Pulling from library/centos
a1d0c7532777: Pull complete
Digest: sha256:a27fd8080b517143cbbbab9dfb7c8571c40d67d534bbdee55bd6c473f432b177
Status: Downloaded newer image for centos:latest
c5e9ab22096b25c2fceb14ef3bcc7916fcafcf2c3956c34ec2929e3eae3fc1fc
root@ub1:/home/r/docker# docker run -it --rm -d --name 2_cont_debian_for_task3 -v /data:/data debian
Unable to find image 'debian:latest' locally
latest: Pulling from library/debian
3e440a704568: Pull complete
Digest: sha256:7b991788987ad860810df60927e1adbaf8e156520177bd4db82409f81dd3b721
Status: Downloaded newer image for debian:latest
6867343e6cf77e17d1e2ffd0bd6b3576c0d7324902084009bfd1afef75502634
root@ub1:/home/r/docker# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
6867343e6cf7   debian    "bash"        2 minutes ago   Up 2 minutes             2_cont_debian_for_task3
c5e9ab22096b   centos    "/bin/bash"   2 minutes ago   Up 2 minutes             1_cont_centos_for_task3
root@ub1:/home/r/docker# docker exec -it 1_cont_centos_for_task3 bash
[root@c5e9ab22096b /]# touch /data/file1
[root@c5e9ab22096b /]# ls -lha /data/file1
-rw-r--r-- 1 root root 0 Apr  8 09:05 /data/file1
[root@c5e9ab22096b /]# exit
exit
root@ub1:/home/r/docker# echo "Содержимое файла 2" > /data/file2
root@ub1:/home/r/docker# docker exec -it 2_cont_debian_for_task3 bash
root@6867343e6cf7:/# ls -lha /data
total 12K
drwxr-xr-x 2 root root 4.0K Apr  8 09:09 .
drwxr-xr-x 1 root root 4.0K Apr  8 09:01 ..
-rw-r--r-- 1 root root    0 Apr  8 09:05 file1
-rw-r--r-- 1 root root   34 Apr  8 09:09 file2
root@6867343e6cf7:/# cat file1 file2
cat: file1: No such file or directory
cat: file2: No such file or directory
root@6867343e6cf7:/# cat data/file1 data/file2
Содержимое файла 2
root@6867343e6cf7:/#
```
---
### Задача 4. 
*Воспроизведите практическую часть лекции самостоятельно.  
Соберите Docker-образ с Ansible, загрузите на Docker Hub и пришлите ссылку вместе с остальными ответами к задачам.*

### Решение.
Собран образ ***romanke/image_for_task4:v1.0*** с использованием dockerfile к заданию.  
Выгружен в репозиторий docker:   https://hub.docker.com/r/romanke/image_for_task4 

Команды при выполнении.
```
docker build -t romanke/image_for_task4:v1.0 .
docker login
docker push romanke/image_for_task4:v1.0
docker logout
```
