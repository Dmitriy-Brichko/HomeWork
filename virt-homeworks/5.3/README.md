### Задача 1
Сценарий выполения задачи:
* создайте свой репозиторий на https://hub.docker.com;
* выберете любой образ, который содержит веб-сервер Nginx;
* создайте свой fork образа;
* реализуйте функциональность: запуск веб-сервера в фоне с индекс-страницей, содержащей HTML-код ниже:
```bash
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I’m DevOps Engineer!</h1>
</body>
</html>
```
Опубликуйте созданный форк в своем репозитории и предоставьте ответ в виде ссылки на 
https://hub.docker.com/username_repo.

### Решение задачи 1
```markdown
[root@centos7 docker]# docker pull dmitriybrichko/devops
[root@centos7 docker]# docker run -d -p 9513:80 dmitriybrichko/devops
[root@centos7 docker]# docker ps -a
[root@centos7 docker]# docker exec -it ae319a27745b bash
root@ae319a27745b:/# ls -l
total 0
drwxr-xr-x   1 root root  26 Nov 15 14:12 bin
drwxr-xr-x   2 root root   6 Apr 24  2018 boot
drwxr-xr-x   5 root root 340 Nov 15 14:33 dev
drwxr-xr-x   1 root root  66 Nov 15 14:33 etc
drwxr-xr-x   2 root root   6 Apr 24  2018 home
drwxr-xr-x   1 root root  45 May 23  2017 lib
drwxr-xr-x   2 root root  34 Sep 30 12:33 lib64
drwxr-xr-x   2 root root   6 Sep 30 12:32 media
drwxr-xr-x   2 root root   6 Sep 30 12:32 mnt
drwxr-xr-x   2 root root   6 Sep 30 12:32 opt
dr-xr-xr-x 247 root root   0 Nov 15 14:33 proc
drwx------   2 root root  37 Sep 30 12:33 root
drwxr-xr-x   1 root root  23 Nov 15 14:33 run
drwxr-xr-x   1 root root  94 Nov 15 14:12 sbin
drwxr-xr-x   2 root root   6 Sep 30 12:32 srv
dr-xr-xr-x  13 root root   0 Nov 15 14:33 sys
drwxrwxrwt   1 root root   6 Nov 15 14:13 tmp
drwxr-xr-x   1 root root  19 Sep 30 12:32 usr
drwxr-xr-x   1 root root  28 Nov 15 14:12 var
root@ae319a27745b:/# cd  /var/www/html/
root@ae319a27745b:/var/www/html# ls -l
total 8
-rw-r--r-- 1 root root 120 Nov 15 14:11 index.html
-rw-r--r-- 1 root root 612 Nov 15 14:13 index.nginx-debian.html
root@ae319a27745b:/var/www/html# cat index.html
<html>
<head>
Hey, Netology
</head>
<body>
<h1>My name is Dmitry Brichko!
<h1>I am DevOps Engineer!</h1>
</body>
</html>

http://51.250.5.69:9513/
https://hub.docker.com/repository/docker/dmitriybrichko/devops
```
---
### Задача 2
Посмотрите на сценарий ниже и ответьте на вопрос: "Подходит ли в этом сценарии использование Docker контейнеров или лучше подойдет виртуальная машина, физическая машина? Может быть возможны разные варианты?"
Детально опишите и обоснуйте свой выбор.

Сценарий:

* Высоконагруженное монолитное java веб-приложение;
* Nodejs веб-приложение;
* Мобильное приложение c версиями для Android и iOS;
* Шина данных на базе Apache Kafka;
* Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana;
* Мониторинг-стек на базе Prometheus и Grafana;
* MongoDB, как основное хранилище данных для java-приложения;
* Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry.
### Решение задачи 2
```markdown
* Высоконагруженное монолитное java веб-приложение - физический сервер, т.к. монолитное, селдовательно в микросерверах 
не реализуемо без изменения кода, и так как высоконагруженное -  то необходим физический доступ к ресурсами, без 
использования гипервизора виртуалки.
* Nodejs веб-приложение - это веб приложение, для таких приложений достаточно докера.
* Мобильное приложение c версиями для Android и iOS - Виртуальная машина, т.к. в докере нет GUI.
* Шина данных на базе Apache Kafka - если критично потерять данные для прода то вируалка, для теста можно исполдьзовать
контейнерную реализацию.
* Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash 
и две ноды kibana - Elasticsearch в виртуальный кластер, ноды в докер, хотя можно и в виртуалки, но в докере легче 
перезаливать сервера во время тестирования.
* Мониторинг-стек на базе Prometheus и Grafana - в докер, лагко разворачивать, перезаливать, масштабировать.
* MongoDB, как основное хранилище данных для java-приложения - Если высоконагруженное, то физический сервер, если нет то
виртуальной машины хватит.
* Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry - на сколько я понимаю, образа 
хранятся в отдельном месте, поэтому докер
```
---
### Задача 3
* Запустите первый контейнер из образа centos c любым тэгом в фоновом режиме, подключив папку /data из текущей рабочей директории на хостовой машине в /data контейнера;
* Запустите второй контейнер из образа debian в фоновом режиме, подключив папку /data из текущей рабочей директории на хостовой машине в /data контейнера;
* Подключитесь к первому контейнеру с помощью docker exec и создайте текстовый файл любого содержания в /data;
* Добавьте еще один файл в папку /data на хостовой машине;
* Подключитесь во второй контейнер и отобразите листинг и содержание файлов в /data контейнера.
### Решение задачи 3
```markdown
[root@centos7 ~]# mkdir /date/
[root@centos7 ~]# docker pull kinogmt/centos-ssh
[root@centos7 ~]# docker run -v /date:/date -d --name centos1 kinogmt/centos-ssh
[root@centos7 ~]# docker exec -it centos1 /bin/bash
[root@df9a670b624e /]# touch /date/file1
[root@df9a670b624e /]# echo centos1>/date/file1
[root@df9a670b624e /]# exit
[root@centos7 ~]# cat /date/file1
centos1
[root@centos7 ~]# echo debian1>file2
[root@centos7 ~]# dir
file1  file2
[root@centos7 ~]# docker pull mdoerges/debian-buster-nginx
[root@centos7 ~]# docker run -v /date:/date -d --name debian1 mdoerges/debian-buster-nginx
[root@centos7 ~]# docker exec -it debian1 /bin/bash
root@904ec8b876da:/# dir /date/
file1  file2
root@904ec8b876da:/# cat /date/file1 && cat /date/file2
centos1
debian1
```
---
Задача 4 (*)
### Воспроизвести практическую часть лекции самостоятельно.

Соберите Docker образ с Ansible, загрузите на Docker Hub и пришлите ссылку вместе с остальными ответами к задачам.
