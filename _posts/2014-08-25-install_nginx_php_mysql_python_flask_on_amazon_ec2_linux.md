---
layout: post
title: "Install Nginx PHP MySQL Python Flask on Amazon EC2 Linux AMI"
date: 2014-02-17 13:00
category: Techy
tags: Linux Amazon EC2 AMI Nginx PHP MySQL Python Flask
---

## Assumptions:
* you have a brande new EC2 instance running Amazon Linux AMI
* your time zone is `America/Chicago`

## Install Nginx PHP-FPM and MySQL

### optional
```
sudo /bin/rpm -ivh http://ftp.uninett.no/linux/epel/6/i386/epel-release-6-8.noarch.rpm
sudo /bin/rpm -ivh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
sudo /bin/rpm -ivh http://nginx.org/packages/centos/6/noarch/RPMS/nginx-release-centos-6-0.el6.ngx.noarch.rpm
```

### install nginx php mysql memcache
```
sudo yum -y update
sudo ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime
sudo yum install -y gcc make nginx php-fpm php-devel php-mysql php-pdo php-pear php-mbstring php-cli php-odbc php-imap php-gd php-xml php-soap php-pecl-apc pcre-devel memcached php-pecl-memcached openssl-devel mysql-server mysql

sudo pecl install memcache

sudo chkconfig nginx on
sudo chkconfig mysqld on
sudo chkconfig php-fpm on
sudo chkconfig memcached on
```
 
### create files
**create fcgi configuration file**
```
sudo vi /etc/nginx/fcgi.conf
```

in vim:
```
fastcgi_param GATEWAY_INTERFACE CGI/1.1;
fastcgi_param SERVER_SOFTWARE nginx/$nginx_version;
 
fastcgi_param QUERY_STRING $query_string;
fastcgi_param REQUEST_METHOD $request_method;
fastcgi_param CONTENT_TYPE $content_type;
fastcgi_param CONTENT_LENGTH $content_length;
 
fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
fastcgi_param SCRIPT_NAME $fastcgi_script_name;
fastcgi_param REQUEST_URI $request_uri;
fastcgi_param DOCUMENT_URI $document_uri;
fastcgi_param DOCUMENT_ROOT $document_root;
fastcgi_param SERVER_PROTOCOL $server_protocol;
 
fastcgi_param REMOTE_ADDR $remote_addr;
fastcgi_param REMOTE_PORT $remote_port;
fastcgi_param SERVER_ADDR $server_addr;
fastcgi_param SERVER_PORT $server_port;
fastcgi_param SERVER_NAME $server_name;
 
# PHP only, required if PHP was built with --enable-force-cgi-redirect
fastcgi_param REDIRECT_STATUS 200;
```

**create nginx configuration file**
```
sudo vi /etc/nginx/conf.d/default.conf
```

in vim:
```
server {
        listen       80;
        server_name  localhost;
        root         /var/www; # this has to be /var/www, not /var/www/html, or static files in flask projects won’t load
        index index.php index.html index.htm;
 
        location ~ .*\\.(php|php5)?$
        {
 
            fastcgi_pass  unix:/var/run/php-fpm/php-fpm.sock;
            fastcgi_index index.php;
            include fcgi.conf;
        }
 
        location ~ .*\\.(gif|jpg|jpeg|png|bmp|swf)$
        {
            expires      30d;
        }
        
        location ~ .*\\.(js|css)?$
        {
            expires      12h;
        }
 
        access_log off;
}
```

**set php-fpm port**
```
sudo vi /etc/php-fpm.d/www.conf
```

in vim:
```
;listen = 127.0.0.1:9000
listen = /var/run/php-fpm/php-fpm.sock
;listen.owner = nobody
listen.owner = nginx
;listen.group = nobody
listen.group = nginx
;listen.mode = 0666
listen.mode = 0664
user = nginx
group = nginx
```

### enable memcached
```
sudo vi /etc/php.d/memcached.ini
```

in vim:
```
; Enable memcache extension module
extension=memcache.so
```

### start service
```
sudo service php-fpm start
sudo service nginx start
sudo service mysqld start
sudo service memcached start
```

### MILESTONE #1
Browse to your server and **you should get Nginx greeting page**  
`http://ec2-54-208-30-174.compute-1.amazonaws.com:8080/`

### reference
[1] http://nizhishuai.com/2014/04/19/article-148.html  
[2] http://codingsteps.com/install-php-fpm-nginx-mysql-on-ec2-with-amazon-linux-ami/

## Install Python 2.7.8

### install dependencies
```
sudo yum -y groupinstall "development tools"
sudo yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel 
```

### download and install python
```
wget http://python.org/ftp/python/2.7.8/Python-2.7.8.tar.xz
tar xf Python-2.7.8.tar.xz
cd Python-2.7.8
./configure --prefix=/usr/local  --enable-shared LDFLAGS="-Wl,-rpath /usr/local/lib"
make && make altinstall
```
**note:**
* `python 2.7.8` will be installed at `/usr/local`
* It is critical that you use `make altinstall` when you install your custom version of Python. If you use the normal `make install` you will end up with two different versions of Python in the filesystem both named `python`. This can lead to problems that are very hard to diagnose.

### download and install setuptools and pip
```
# get the setup script for setuptools:
sudo wget https://bitbucket.org/pypa/setuptools/raw/bootstrap/ez_setup.py

# install setuptools for python 2.7
sudo /usr/local/python2.7 ez_setup.py

# insall
sudo /usr/local/easy_install-2.7 pip
```

### install virtual environment
```
sudo /usr/local/pip2.7 install virtualenv
```

### reference
[3] http://toomuchdata.com/2014/02/16/how-to-install-python-on-centos/ 

## Install Flask and testing app

### install flask
```
sudo mkdir /var/www # optional, for Amazon Linux AMI, this is created by default
sudo mkdir /var/www/demoapp

# set the owner of demoapp directory to ec2-user
sudo chown -R ec2-user:ec2-user /var/www/demoapp/

cd /var/www/demoapp

# make sure this virtualenv is for python2.7.8 we just installed, if not, use virtualenv-2.7 instead

# create a new virtual environment called venv
virtualenv venv

# activate venv
. venv/bin/activate

# install flask in venv
pip install flask
```

### install testing app
**create main python script**
```
vi hello.py
```

in vim:
```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=8080)
```

### MILESTONE #2
Run the script of the testing app
```
python hello.py
```

Browse to your server's port 8080 and see the app in action  
`http://ec2-54-208-30-174.compute-1.amazonaws.com:8080/`

**You should see 'Hello World!'**

## Install and configure uWSGI

### install uwsgi
```
pip install uwsgi

# back up current nginx conf
sudo mv /etc/nginx/conf.d/default.conf /etc/nginx/conf.d/default.conf.bak
```

### create demoapp configuration file
```
vi demoapp_nginx.conf
```

in vim:
```
server {
    listen      80;
    server_name localhost;
    charset     utf-8;
    client_max_body_size 75M;

    location / { try_files $uri @yourapplication; }
    location @yourapplication {
        include uwsgi_params;
        uwsgi_pass unix:/var/www/demoapp/demoapp_uwsgi.sock;
    }
} 
```

### symlink demoapp configuration file to nginx configuration file
```
sudo ln -s /var/www/demoapp/demoapp_nginx.conf /etc/nginx/conf.d/
sudo /etc/init.d/nginx restart
```

### MILESTONE #3
Browse to your server's public ip address and you will see **502 Bad Gateway** error  
`http://ec2-54-208-30-174.compute-1.amazonaws.com/`

This is what we expected. It means that Nginx has an issue connecting to uWSGI, our Python application host.

In `demoapp_nginx.conf` we have this line:
```
uwsgi_pass unix:/var/www/demoapp/demoapp_uwsgi.sock;
```
It says that we would like Nginx and uWSGI to communicate using this socket file. But we haven't configured uWSGI yet, and this file doesn't exist, for now.

### configure demoapp uwsgi configuration file
``` 
vi demoapp_uwsgi.ini # note that we are still in /var/www/demoapp
```

in vim:
```
[uwsgi]
#application's base folder
base = /var/www/demoapp

#python module to import
app = hello
module = %(app)

home = %(base)/venv
pythonpath = %(base)

#socket file's location
socket = /var/www/demoapp/%n.sock

#permissions for the socket file
chmod-socket = 666
#chown-socket = nginx:nginx
# if chown-socket, uwsgi needs to be run using sudo, which will make the log's owner root

#the variable that holds a flask application inside the module imported at line #6
callable = app

#location of log files
logto = /var/log/uwsgi/%n.log 
```
**note:**
*  Nginx versions from other repositories may use a user `www-data` instead of `nginx`

### create directory for uwsgi log
```
sudo mkdir -p /var/log/uwsgi
sudo chown -R ec2-user:ec2-user /var/log/uwsgi
```

### MILESTONE #4
Execute uWSGI and pass it the newly created configuration file
```
uwsgi --ini /var/www/demoapp/demoapp_uwsgi.ini
```

Browse to your server. Now nginx should be able to connect to uWSGI process  
`http://ec2-54-208-30-174.compute-1.amazonaws.com/`

**You should see 'Hello World!'**

### configure uwsgi emperor
**note:**
* uWSGI Emperor is for reading configuration files and spawning uWSGI processes.

**create emperor configuration file**
```
sudo vi /etc/init/uwsgi.conf
```

in vim:
```
description "uWSGI"
start on runlevel [2345]
stop on runlevel [06]
respawn

env UWSGI=/var/www/demoapp/venv/bin/uwsgi
env LOGTO=/var/log/uwsgi/emperor.log

exec $UWSGI --master --emperor /etc/uwsgi/vassals --die-on-term --uid nginx --gid nginx --logto $LOGTO
```

**create necessary folders**
```
sudo mkdir /etc/uwsgi && sudo mkdir /etc/uwsgi/vassals
sudo ln -s /var/www/demoapp/demoapp_uwsgi.ini /etc/uwsgi/vassals 
```

**set the owner of the application and log folders**
```
sudo chown -R nginx:nginx /var/www/demoapp/
sudo chown -R nginx:nginx /var/log/uwsgi/ 
```

**modify demoapp uwsgi configuration file**
```
vi /var/www/demoapp/demoapp_uwsgi.ini
```

in vim:
```
...
#permissions for the socket file
chmod-socket    = 644 
```

**start uwsgi**
```
sudo start uwsgi 
```

### reference
[4] http://vladikk.com/2013/09/12/serving-flask-with-nginx-on-ubuntu/ 

==========

## Add A Second Application
### Assumptions
* the name of the app to be added is `twist`

### configure demoapp

**remove demoapp configuration file**
```
sudo rm /etc/nginx/conf.d/demo_uwsgi.conf
```

**restore previously back-uped configuration file**
```
sudo mv /etc/nginx/conf.d/default.conf.bak /etc/nginx/conf.d/default.conf
```

**modify default configuration file**
```
sudo vi /etc/nginx/conf.d/default.conf
```

in vim:
```
...
location = /demoapp { rewrite ^ /demoapp/; }
location /demoapp { try_files $uri @demoapp; }
location @demoapp {
  include uwsgi_params;
  uwsgi_param SCRIPT_NAME /demoapp;
  uwsgi_modifier1 30;
  uwsgi_pass unix:/var/www/demoapp/demoapp_uwsgi.sock;
...
```

### MILESTONE #5
Browse to your server to check if demoapp has correct url prefix `demoapp`  
`http://ec2-54-208-30-174.compute-1.amazonaws.com/demoapp`

### add the new app

**upload twist to /home/ec2-user/**

**move it to /var/www/**
```
sudo mv /home/ec2-user/twist /var/www/twist
cd /var/www/twist 
```

**modify nginx configuration**
```
sudo vi /etc/nginx/conf.d/default.conf
```

in vim:
```
...
location = /twist { rewrite ^ /twist/; }
location /twist { try_files $uri @twist; }
location @twist {
  include uwsgi_params;
  uwsgi_param SCRIPT_NAME /twist;
  uwsgi_modifier1 30;
  uwsgi_pass unix:/var/www/twist/twist_uwsgi.sock;
...
```

**create twist_uwsgi.ini**
```
vi twist_uwsgi.ini
```

in vim:
```
[uwsgi]
#application's base folder
base = /var/www/twist

#python module to import
app = twist
module = %(app)

home = /var/www/demoapp/venv # home should still be the path of virtual environment
pythonpath = %(base)

#socket file's location
socket = /var/www/twist/%n.sock

#permissions for the socket file
chmod-socket    = 644

#the variable that holds a flask application inside the module imported at line #6
callable = app

#location of log files
logto = /var/log/uwsgi/%n.log 
```

**symlink it to /var/uwsgi/vassals**
```
sudo ln -s /var/www/twist/twist_uwsgi.ini /etc/uwsgi/vassals 
```

**set owner of twist nginx**
```
sudo chown -R nginx:nginx /var/www/twist/ 
```

### MILESTONE #6
Browse to your server's public ip address with prefix `twist` and see **Internal Server Error** message  
`http://ec2-54-208-30-174.compute-1.amazonaws.com/twist/`

This is absolutely OK. The issue here is that tweep haven't been installed. You can see more details about the error message in `twist_uswgi.log`

Unfortunately, it is not convenient to insall tweepy, because the virtual environment is in `demoapp`, which has been set owner nginx, you (as ec2-user) cannot do play with it.

At this point, you have two options.

### option 1:
**change the owner of the virtual environment**
```
sudo chown -R ec2-user:ec2-user /var/www/demoapp/venv
```

### option 2: 
**install a new virtualenv and change configurations accordingly**

Note that you don’t need to change settings for nginx.  
You need to change settings for [appname]_uwsgi.ini and for uwsgi emperor

**install new virtualenv**
```
cd ~
virtualenv venv
. ./venv/bin/activate
```

**install tweepy**
```
pip install flask uwsgi tweepy
```

**modify demoapp uwsgi configuration file**
```
cd /var/www/demoapp
sudo vi demoapp_uwsgi.ini
```

in vim:
```
home = /home/ec2-user/venv/
```

**modify twist uwsgi configuration file**
```
cd ../twist
sudo vi twist_uwsgi.ini
```

in vim:
```
home = /home/ec2-user/venv/
```

**modify emperor configuration file**
```
sudo vi /etc/init/uwsgi.conf
```

in vim:
```
env UWSGI=/home/ec2-user/venv/bin/uwsgi
```

### now we have a problem; permission denied when starting emperor
```
execvp(): Permission denied [core/emperor.c line 1434]
```

**fix 1:** run emperor as root (works)  
**fix 2:** set owner of newly installed venv nginx (not work)  
**fix 3:** set permission of newly installed venv 777 (not work)

### MILESTONE #7
Browse to your server's with prefix `twist` and see twist app in action  
`http://ec2-54-208-30-174.compute-1.amazonaws.com/twist/`


