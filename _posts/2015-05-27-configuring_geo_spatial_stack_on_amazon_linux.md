---
layout: post
title: "Configuring a Geo-spatial Stack on Amazon Linux"
date: 2015-05-27 12:35
category: techy
tags: Linux Amazon EC2 AMI Apache PostgreSQL PostGIS GeoServer Tomcat
---

This is a walk-through installing PostgreSQL, PostGIS and GeoServer on Amazon Linux. This post is written in May, 2015. The version of Amazon Linux AMI is 2015.03.

## Update the System
First of all, update the system after creating a new instance.

```
sudo yum update
```

## Install PostgreSQL
Install postgresql, postgresql-devel, and postgresql-libs, and configure data directory and start the service (as the postgres user).

```
sudo yum install postgresql postgresql-server postgresql-devel
sudo mkdir /usr/local/pgsql/
sudo mkdir /usr/local/pgsql/data
sudo chown postgres /usr/local/pgsql/data
sudo su postgres
initdb -D /usr/local/pgsql/data
postgres -D /usr/local/pgsql/data &
exit
```

## Install PostGIS
Install PostGIS. First we install some build tools and the GEOS and PROJ libraries. Then we install PostGIS. After all of them are installed, we update our libraries, so the server knows where to find them. Finally we create a template database for PostGIS.

### Install build tools

```
sudo yum install gcc make gcc-c++ libtool libxml2-devel
```

### Make a directory for PostGIS
```
cd /home/ec2-user/
mkdir postgis
cd postgis
```

### Install GEOS
```
wget http://download.osgeo.org/geos/geos-3.4.2.tar.bz2
tar xjvf geos-3.4.2.tar.bz2
cd geos-3.4.2
./configure
make
sudo make install
```

### Install PROJ
```
cd /home/ec2-user/postgis/
wget http://download.osgeo.org/proj/proj-4.9.1.tar.gz
wget http://download.osgeo.org/proj/proj-datumgrid-1.5.zip
tar zxvf proj-4.9.1.tar.gz
cd proj-4.9.1/nad
unzip ../../proj-datumgrid-1.5.zip
cd ..
./configure  
make
sudo make install
```

### Install PostGIS
```
cd /home/ec2-user/postgis/
wget http://download.osgeo.org/postgis/source/postgis-2.1.7.tar.gz
tar zxvf postgis-2.1.7.tar.gz 
cd postgis-2.1.7
# if we do not specify --without-raster parameter, the configure process will fail
# because we do not have GDAL installed.
./configure --with-geosconfig=/usr/local/bin/geos-config --without-raster
make
sudo make install
```

### Update libraries
```
sudo su
echo /usr/local/lib >> /etc/ld.so.conf
exit
sudo ldconfig
```

### Create a template database
```
createdb -U postgres template_postgis
createlang -U postgres plpgsql template_postgis
psql -U postgres -d template_postgis < /usr/share/pgsql92/contrib/postgis-2.1/postgis.sql
psql -U postgres -d template_postgis < /usr/share/pgsql92/contrib/postgis-2.1/spatial_ref_sys.sql
```

## Install JDK
In this step, we are supposed to install Oracle JDK 7. However, GeoServer works with OpenJDK, which is pre-installed in Amazon Linux, so this step can be skipped.

You can use `java -version` command to check if the version is correct and whether your instance is using OpenJDK or Oracle JDK. Be default it should be using OpenJDK:

```
java version "1.7.0_79"
OpenJDK Runtime Environment (amzn-2.5.5.1.59.amzn1-x86_64 u79-b14)
OpenJDK 64-Bit Server VM (build 24.79-b02, mixed mode)
```

## Install Apache HTTP Server and Apache Tomcat
Install Apache HTTP Server and Apache Tomcat is very simple. Just one line of command.

```
sudo yum install httpd httpd-devel tomcat6
```

## Install GeoServer
Download the GeoServer web archive and move it to Tomcat’s webapps directory. You can find more about installing GeoServer as web archive in [GeoServer documentation](http://docs.geoserver.org/stable/en/user/installation/war.html).

```
cd /home/ec2-user/
wget http://sourceforge.net/projects/geoserver/files/GeoServer/2.7.1/geoserver-2.7.1-war.zip
unzip geoserver-2.7.1-war.zip
sudo chown tomcat:tomcat geoserver.war
sudo mv geoserver.war /var/lib/tomcat6/webapps/
```

## Install mod_jk
Mod_jk is an Apache module for connecting Apache web server to Tomcat. This allows us to access web applications in Tomcat (i.e. GeoServer) through Apache HTTP Server over port 80. If we skip this, we have to configure security group to allow access over the port on which tomcat is listening (default is 8080). However, we will probably still meet CORS issue. So this step is highly recommended.

Once mod_jk is installed, we need to update the Apache HTTP Server config and create a workers.properties file so that Apache HTTP Server and Apache Tomcat can communicate via mod_jk. This is a mediocre configuration; it is barely more than the minimum required (as documented by Apache [here](http://tomcat.apache.org/connectors-doc/generic_howto/quick.html)).


### Install mod_jk
```
cd /home/ec2-user
mkdir mod_jk
cd mod_jk
wget http://mirror.nexcess.net/apache/tomcat/tomcat-connectors/jk/tomcat-connectors-1.2.40-src.tar.gz
tar xzf tomcat-connectors-1.2.40-src.tar.gz
cd tomcat-connectors-1.2.40-src/native
./configure --with-apxs=/usr/sbin/apxs
make
sudo make install
```

### Configure Apache HTTP Server
```
sudo vim /etc/httpd/conf/httpd.conf
```

In Vim, add following code to bottom:

```
# Load mod_jk module
# Update this path to match your modules location
LoadModule    jk_module  /usr/lib64/httpd/modules/mod_jk.so
# Where to find workers.properties
# Update this path to match your conf directory location (put workers.properties next to httpd.conf)
JkWorkersFile /etc/httpd/conf/workers.properties
# Where to put jk shared memory
# Update this path to match your local state directory or logs directory
JkShmFile     /var/log/httpd/mod_jk.shm
# Where to put jk logs
# Update this path to match your logs directory location (put mod_jk.log next to access_log)
JkLogFile     /var/log/httpd/mod_jk.log
# Set the jk log level [debug/error/info]
JkLogLevel    info
# Select the timestamp log format
JkLogStampFormat "[%a %b %d %H:%M:%S %Y] "
# Send everything for context /geoserver and /geoserver/* to worker named geoserver_worker (ajp13)
JkMount  /geoserver/* geoserver_worker
JkMount  /geoserver   geoserver_worker
```

### Create workers.properties
```
sudo vim /etc/httpd/conf/workers.properties
```

In Vim, add following code:


```
# Define the list of workers that will be used
worker.list=geoserver_worker
# Define geoserver_worker
worker.geoserver_worker.port=8009
worker.geoserver_worker.host=localhost
worker.geoserver_worker.type=ajp13
```

## Start Apache HTTP Server and Apache Tomcat

At this time, everything is installed and configured, and we are ready to start Apache HTTP Server and Apache Tomcat.

```
sudo /sbin/service httpd start
sudo /sbin/service tomcat6 start
```

Go to [your ip address] to see if Apache HTTP Server is running properly. You should see a Apache welcome page.

Go to [your ip address]/geoserver to see if GeoServer is running properly. You should see GeoServer admin page.

## Reference
I referred [this post](http://imperialwicket.com/aws-configuring-a-geo-spatial-stack-in-amazon-linux/) to set up my server. Note that there is some more text about storing data in PostgreSQL and fetching it from GeoServer at the end of this post. If you need guidance for this, go check that post.

