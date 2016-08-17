TMCAS Usage Portal
====

# Introduction
This portal is used for present TMCAS internal business information.

# Architect
nginx -> django -> mongodb

![](Drawing1.vsd)

## Nginx
Nginx configuration file, `/etc/nginx/conf.d/`

```
location / {
    uwsgi_pass web:8000;
}
```

## Django
`pip install uwsgi`

configuration file
```
[uwsgi]
socket = 0.0.0.0:8000
chdir = /var/spool/django/
wsgi-file = medivh/wsgi.py
```

## Mongodb
listen on 16160


# Dockerize
Refer `docker-compose.yml`
It will build three containers web, nginx and mongo

## build and start service
docker-compose up

## stop service
docker-compose stop


# Fresh Install
##pip
wget https://bootstrap.pypa.io/ez_setup.py -O -  --no-check-certificate | python
easy_install pip

##django
pip install Django==1.6

mkdir /var/spool/django
django-admin.py startproject medivh

##pymongo
pip install pymongo

Successfully installed pymongo-3.2

##mongo
sudo yum install -y mongodb-org  
mkdir /data/mongodb  
cat /etc/mongod.conf   
service mongod start  

storage:
  dbPath: /data/mongodb

## nginx
yum install nginx

### 配置
```
location / {
    include /etc/nginx/uwsgi_params;
    uwsgi_pass 127.0.0.1:8000;
    uwsgi_param Host $host;
    uwsgi_param X-Real-IP $remote_addr;
    uwsgi_param X-Forwarded-For $proxy_add_x_forwarded_for;
    uwsgi_param X-Forwarded-Proto $http_x_forwarded_proto;
}
```

## uwsgi
pip install uwsgi

uwsgi /etc/django/bms.ini -daemon

### 配置
```
[root@usage medivh]# cat /etc/django/bms.ini 
[uwsgi]
socket = 127.0.0.1:8000
chdir = /var/spool/django/medivh/
wsgi-file = medivh/wsgi.py
processes = 4
threads = 2
```

### 启动
```
# 启动uwsgi --ini uwsgi.ini
# 重启uwsgi --reload /var/run/uwsgi.pid 
# 结束uwsgi --stop /var/run/uwsgi.pid 
```


----
#未完成
##安全性
- 运行权限  
- mongo账户密码

##mongo warning
Server has startup warnings: 
2016-01-06T19:47:01.206-0800 I CONTROL  [initandlisten] 
2016-01-06T19:47:01.206-0800 I CONTROL  [initandlisten] ** WARNING: soft rlimits too low. rlimits set to 1024 processes, 64000 files. Number of processes should be at least 32000 : 0.5 times number of files.
