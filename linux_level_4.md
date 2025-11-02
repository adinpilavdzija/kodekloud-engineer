# Linux Level 4 (Tasks 4.1-4.9) 

## 4.1 Install and Configure Nginx as an LBR

Day by day traffic is increasing on one of the websites managed by the `Nautilus` production support team. Therefore, the team has observed a degradation in website performance. Following discussions about this issue, the team has decided to deploy this application on a high availability stack i.e on `Nautilus` infra in `Stratos DC`. They started the migration last month and it is almost done, as only the LBR server configuration is pending. Configure LBR server as per the information given below:
- Install `nginx` on `LBR` (load balancer) server.
- Configure load-balancing with the an `http` context making use of all `App Servers`.
- Make sure you do not update the apache port that is already defined in the apache configuration on all app servers, also make sure apache service is up and running on all app servers.
- Once done, you can access the website using `StaticApp` button on the top bar.

---

```bash
# { Repeat for App Servers 2 and 3. Open every App Server in separate tab simultaneously and run these commands
$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10
$ sudo su -
$ systemctl status httpd
● httpd.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; preset: disabled)
     Active: active (running) since Wed 2025-06-11 13:58:22 UTC; 4min 49s ago
$ grep Listen /etc/httpd/conf/httpd.conf 
...
Listen 8086

$ curl localhost:8086
Welcome to xFusionCorp Industries!

$ tail -f /var/log/httpd/access_log
# }

$ sshpass -p 'Mischi3f' ssh -o StrictHostKeyChecking=no loki@172.16.238.14
$ sudo su -
$ yum install nginx -y
$ vi /etc/nginx/nginx.conf
    include /etc/nginx/conf.d/*.conf;

    # { add this
    upstream app_servers {
        server 172.16.238.10:8086;
        server 172.16.238.11:8086;
        server 172.16.238.12:8086;
    }
    # }

    server {
        listen       80;
        listen       [::]:80;
        server_name  _;
        root         /usr/share/nginx/html;

        # { add this
        location / {
            proxy_pass http://app_servers;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
        # }
...
    }

$ systemctl start nginx
$ systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; preset: disabled)
     Active: active (running) since Thu 2025-09-25 20:00:58 UTC; 20min ago

# Click on StaticApp button and watch logs from `tail -f /var/log/httpd/access_log` from each App Server
```
✅

## 4.2 LEMP Troubleshooting

We have LEMP stack configured on apps and database server in `Stratos` DC. Its using Nginx + php-fpm, for now we have deployed a sample php page on these apps. Due to some misconfiguration the php page is not loading on the web server. Seems like at least two app servers are having issues. Find below more details and make sure website works on LBR URL and locally on each app as well.
1. Nginx is supposed to run on port `80` on all app servers.
2. Nginx document root is `/var/www/html/`
3. Test the webpage on LBR URL (use `LBR` button on the top bar) and locally on each app server to make sure it works. It must not display any error message or nginx default page.

---

```bash
# Open 3 separate terminals and in each ssh into one App Server
$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10
$ sshpass -p 'Am3ric@' ssh -o StrictHostKeyChecking=no steve@172.16.238.11
$ sshpass -p 'BigGr33n' ssh -o StrictHostKeyChecking=no banner@172.16.238.12

# on all 3 App Servers
$ sudo su - 
$ systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; preset: disabled)
    Drop-In: /etc/systemd/system/nginx.service.d
             └─php74-php-fpm.conf
     Active: active (running) since Tue 2025-09-23 20:34:05 UTC; 12min ago
$ curl localhost:80 | less

[root@stapp03 ~]# vi /etc/nginx/nginx.conf
        # root         /usr/share/nginx/html/;
        root         /var/www/html/; # change /usr/share/nginx/html/ to /var/www/html/
[root@stapp03 ~]# systemctl reload nginx
[root@stapp03 ~]# curl localhost:80
App is able to connect to the database using user kodekloud_aim

[root@stapp01 ~]# vi /etc/nginx/nginx.conf
    server {
        # listen       8084 default_server;
        listen       80 default_server; # change to 80
        # listen       [::]:8084 default_server;
        listen       [::]:80 default_server; # change to 80
        server_name  _;
        root         /var/www/html/;
        # index index.html index.htm;
        index index.php index.html index.htm; # add index.php
[root@stapp01 ~]# systemctl reload nginx
[root@stapp01 ~]# curl localhost:80
App is able to connect to the database using user kodekloud_aim

[root@stapp02 ~]# vi /etc/nginx/nginx.conf
       location ~ .php$ {
           try_files $uri =404;
           # fastcgi_pass unix:/var/opt/remi/php74/run/php-fpm/www;
           fastcgi_pass unix:/var/opt/remi/php74/run/php-fpm/www.sock; # change www to www.sock
[root@stapp02 ~]# systemctl reload nginx
[root@stapp02 ~]# curl localhost:80
App is able to connect to the database using user kodekloud_aim
```
<img width="689" height="287" alt="4.2" src="https://github.com/user-attachments/assets/6e5c4f7d-1c32-4356-9a65-c1e078ce80ba" />

✅

## 4.3 Install and Configure Postgre SQL

The `Nautilus` application development team has shared that they are planning to deploy one newly developed application on `Nautilus` infra in `Stratos DC`. The application uses PostgreSQL database, so as a pre-requisite we need to set up PostgreSQL database server as per requirements shared below:

PostgreSQL database server is already installed on the `Nautilus` database server.
1. Create a database user `kodekloud_gem` and set its password to `ksH85UJjhb`.
2. Create a database `kodekloud_db10` and grant full permissions to user `kodekloud_gem` on this database.

`Note:` Please do not try to restart PostgreSQL server service.

---

```bash
$ sshpass -p 'Sp!dy' ssh -o StrictHostKeyChecking=no peter@172.16.239.10
$ sudo su -
$ systemctl status postgresql
● postgresql.service - PostgreSQL database server
     Loaded: loaded (/usr/lib/systemd/system/postgresql.service; disabled; preset: disabled)
     Active: active (running) since Thu 2025-09-25 20:32:41 UTC; 8min ago

$ su - postgres
$ psql
psql (13.14)
Type "help" for help.

postgres=# CREATE USER kodekloud_gem WITH ENCRYPTED PASSWORD 'ksH85UJjhb';
CREATE ROLE
postgres=# CREATE DATABASE kodekloud_db10;
CREATE DATABASE

postgres=# GRANT ALL PRIVILEGES ON DATABASE kodekloud_db10 TO kodekloud_gem;
GRANT
postgres=# \c kodekloud_db10
You are now connected to database "kodekloud_db10" as user "postgres".
kodekloud_db10=# GRANT ALL PRIVILEGES ON SCHEMA public TO kodekloud_gem;
GRANT
kodekloud_db10=# GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO kodekloud_gem;
GRANT
kodekloud_db10=# GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public TO kodekloud_gem;
GRANT
kodekloud_db10=# ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO kodekloud_gem;
ALTER DEFAULT PRIVILEGES
kodekloud_db10=# ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON SEQUENCES TO kodekloud_gem;
ALTER DEFAULT PRIVILEGES
```
✅

## 4.4 Bash scripts if/else statements

The Nautilus DevOps team is working on to develop a bash script to automate some tasks. As per the requirements shared with the team database related tasks needed to be automated. Below you can find more details about the same:
1. Write a bash script named `/opt/scripts/database.sh` on `Database Server`. The mariadb database server is already installed on this server.
2. Add code in the script to perform some database related operations as per conditions given below:
- Create a new database named `kodekloud_db01`. If this database already exists on the server then script should print a message `Database already exists` and if the database does not exist then create the same and script should print `Database kodekloud_db01 has been created`. Further, create a user named `kodekloud_roy` and set its password to `asdfgdsd`, also give full access to this user on newly created database (remember to use wildcard host while creating the user).
- Now check if the database (if it was already there) already contains some data (tables) `if so then script should print 'database is not empty` otherwise import the database dump `/opt/db_backups/db.sql` and print `imported database dump into kodekloud_db01 database`.
- Take a mysql dump which should be named as `kodekloud_db01.sql` and save it under `/opt/db_backups/` directory.

---

```bash
$ sshpass -p 'Sp!dy' ssh -o StrictHostKeyChecking=no peter@172.16.239.10

$ sudo su -

$ systemctl status mariadb
● mariadb.service - MariaDB 10.5 database server
     Loaded: loaded (/usr/lib/systemd/system/mariadb.service; enabled; preset: disabled)
     Active: active (running) since Sun 2025-09-28 16:27:42 UTC; 1min 15s ago

$ mysql
> CREATE USER 'script_admin'@'localhost' IDENTIFIED BY 'Password123!';
> GRANT ALL PRIVILEGES ON kodekloud_db01.* TO 'script_admin'@'localhost';
> GRANT CREATE USER, GRANT OPTION ON *.* TO 'script_admin'@'localhost';
> GRANT RELOAD ON *.* TO 'script_admin'@'localhost';
> FLUSH PRIVILEGES;
> exit;

$ touch /opt/scripts/database.sh
$ ls -l /opt/scripts/database.sh
-rw-r--r-- 1 root root 0 Sep 28 16:31 /opt/scripts/database.sh
$ chmod +x /opt/scripts/database.sh
$ ls -l /opt/scripts/database.sh
-rwxr-xr-x 1 root root 0 Sep 28 16:31 /opt/scripts/database.sh

$ vi /opt/scripts/database.sh
#!/bin/bash

ADMIN_USER="script_admin"
ADMIN_PASS="Password123!"
DB_NAME="kodekloud_db01"
DB_USER="kodekloud_roy"
DB_PASS="asdfgdsd"
DB_DUMP_FILE="/opt/db_backups/db.sql"
FINAL_DUMP_FILE="/opt/db_backups/${DB_NAME}.sql"

# Check if database exists
DB_EXISTS=$(mysql -u$ADMIN_USER -p$ADMIN_PASS -N -B -e "SHOW DATABASES LIKE '${DB_NAME}';")
if [ -n "$DB_EXISTS" ]; then
    #echo "Database ${DB_NAME} exists"
    echo "Database already exists"
else
    mysql -u$ADMIN_USER -p$ADMIN_PASS -e "CREATE DATABASE ${DB_NAME};"
    #echo "Database ${DB_NAME} has been created"
    echo "Database kodekloud_db01 has been created"
fi

# Create/alter user and grant privileges
mysql -u$ADMIN_USER -p$ADMIN_PASS -e "CREATE USER IF NOT EXISTS '${DB_USER}'@'%' IDENTIFIED BY '${DB_PASS}';"
mysql -u$ADMIN_USER -p$ADMIN_PASS -e "ALTER USER '${DB_USER}'@'%' IDENTIFIED BY '${DB_PASS}';"
mysql -u$ADMIN_USER -p$ADMIN_PASS -e "GRANT ALL PRIVILEGES ON ${DB_NAME}.* TO '${DB_USER}'@'%';"
mysql -u$ADMIN_USER -p$ADMIN_PASS -e "FLUSH PRIVILEGES;"
echo "User ${DB_USER} has been created/updated"

# Check if database has any tables
TABLE_COUNT=$(mysql -u$ADMIN_USER -p$ADMIN_PASS -D ${DB_NAME} -e "SHOW TABLES;" | wc -l)
if [ "$TABLE_COUNT" -gt 0 ]; then
    echo "database is not empty"
else
    if [ -f "$DB_DUMP_FILE" ]; then
        mysql -u$ADMIN_USER -p$ADMIN_PASS ${DB_NAME} < "$DB_DUMP_FILE"
        echo "imported database dump into kodekloud_db01 database"
    else
        echo "Dump file $DB_DUMP_FILE not found, skipping import."
    fi
fi

# Take a backup dump
mysqldump -u$ADMIN_USER -p$ADMIN_PASS ${DB_NAME} > "$FINAL_DUMP_FILE"
if [ $? -eq 0 ]; then
    echo "Backup created at $FINAL_DUMP_FILE"
else
    echo "Failed to create backup"
fi


$ /opt/scripts/database.sh
Database already exists
User kodekloud_roy has been created/updated
imported database dump into kodekloud_db01 database
Backup created at /opt/db_backups/kodekloud_db01.sql

$ ls -l /opt/db_backups/kodekloud_db01.sql
-rw-r--r-- 1 root root 44958 Sep 28 16:32 /opt/db_backups/kodekloud_db01.sql
```
✅

## 4.5 Configure LAMP server

xFusionCorp Industries is planning to host a `WordPress` website on their infra in `Stratos Datacenter`. They have already done infrastructure configuration—for example, on the storage server they already have a shared directory `/vaw/www/html` that is mounted on each app host under `/var/www/html` directory. Please perform the following steps to accomplish the task:
1. Install httpd, php and its dependencies on all app hosts.
2. Apache should serve on port `5004` within the apps.
3. Install/Configure `MariaDB server` on DB Server.
4. Create a database named `kodekloud_db8` and create a database user named `kodekloud_tim` identified as password `ksH85UJjhb`. Further make sure this newly created user is able to perform all operation on the database you created.
5. Finally you should be able to access the website on LBR link, by clicking on the `App` button on the top bar. You should see a message like `App is able to connect to the database using user kodekloud_tim`

---

```bash
$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10
$ sudo su -

$ cat /var/www/html/index.php 
<?php

$dbname = 'kodekloud_db8';
$dbuser = 'kodekloud_tim';
$dbpass = 'ksH85UJjhb';
$dbhost = 'stdb01';

$link = mysqli_connect($dbhost, $dbuser, $dbpass) or die("Unable to Connect to '$dbhost'");
echo "App is able to connect to the database using user $dbuser";
?>

$ yum install httpd php php-gd php-mysqlnd -y
$ sed -i 's/Listen 80/Listen 5004/g' /etc/httpd/conf/httpd.conf
$ systemctl enable httpd php-fpm ; systemctl restart httpd php-fpm ; systemctl status httpd php-fpm

Repeat for App Servers 2 and 3.

$ sshpass -p 'Sp!dy' ssh -o StrictHostKeyChecking=no peter@172.16.239.10
$ sudo su -
$ yum install -y mariadb-server mariadb

$ systemctl enable mariadb ; systemctl restart mariadb ; systemctl status mariadb
Created symlink /etc/systemd/system/mysql.service → /usr/lib/systemd/system/mariadb.service.
Created symlink /etc/systemd/system/mysqld.service → /usr/lib/systemd/system/mariadb.service.
Created symlink /etc/systemd/system/multi-user.target.wants/mariadb.service → /usr/lib/systemd/system/mariadb.service.
● mariadb.service - MariaDB 10.5 database server
     Loaded: loaded (/usr/lib/systemd/system/mariadb.service; enabled; preset: disabled)
     Active: active (running) since Thu 2025-10-02 20:57:59 UTC; 47ms ago

$ mysql -e "CREATE DATABASE kodekloud_db8;"
$ mysql -e "CREATE USER IF NOT EXISTS 'kodekloud_tim' IDENTIFIED BY 'ksH85UJjhb';"
$ mysql -e "GRANT ALL PRIVILEGES ON kodekloud_db8.* TO 'kodekloud_tim'@'%';"
$ mysql -e "FLUSH PRIVILEGES;"

$ mysql -e "show databases;"
+--------------------+
| Database           |
+--------------------+
| information_schema |
| kodekloud_db8      |
| mysql              |
| performance_schema |
+--------------------+

$ mysql -e "select user,host from mysql.user;"
+---------------+-----------+
| User          | Host      |
+---------------+-----------+
| kodekloud_tim | %         |
| mariadb.sys   | localhost |
| mysql         | localhost |
| root          | localhost |
+---------------+-----------+
```
<img width="633" height="302" alt="4.5" src="https://github.com/user-attachments/assets/9fa23bdd-e83d-4b98-aaa9-7462cc13848c" />

✅

## 4.6 Install and Configure DB Server

We need to setup a database server on `Nautilus DB Server` in `Stratos Datacenter`. Please perform the below given steps on DB Server:
1. Install/Configure MariaDB server.
2. Create a database named `kodekloud_db10`.
3. Create a user called `kodekloud_tim` and set its password to `YchZHRcLkL`.
4. Grant full permissions to user `kodekloud_tim` on database `kodekloud_db10`.

---

```bash
$ sshpass -p 'Sp!dy' ssh -o StrictHostKeyChecking=no peter@172.16.239.10
$ sudo su -
$ yum install -y mariadb-server mariadb

$ systemctl enable mariadb ; systemctl restart mariadb ; systemctl status mariadb
Created symlink /etc/systemd/system/mysql.service → /usr/lib/systemd/system/mariadb.service.
Created symlink /etc/systemd/system/mysqld.service → /usr/lib/systemd/system/mariadb.service.
Created symlink /etc/systemd/system/multi-user.target.wants/mariadb.service → /usr/lib/systemd/system/mariadb.service.
● mariadb.service - MariaDB 10.5 database server
     Loaded: loaded (/usr/lib/systemd/system/mariadb.service; enabled; preset: disabled)
     Active: active (running) since Sat 2025-10-04 12:57:44 UTC; 43ms ago

$ mysql -e "CREATE DATABASE kodekloud_db10;"
$ mysql -e "CREATE USER IF NOT EXISTS 'kodekloud_tim' IDENTIFIED BY 'YchZHRcLkL';"
$ mysql -e "GRANT ALL PRIVILEGES ON kodekloud_db10.* TO 'kodekloud_tim'@'%';"
$ mysql -e "FLUSH PRIVILEGES;"

$ mysql -e "show databases;"
+--------------------+
| Database           |
+--------------------+
| information_schema |
| kodekloud_db10     |
| mysql              |
| performance_schema |
+--------------------+

$ mysql -e "select user,host from mysql.user;"
+---------------+-----------+
| User          | Host      |
+---------------+-----------+
| kodekloud_tim | %         |
| mariadb.sys   | localhost |
| mysql         | localhost |
| root          | localhost |
+---------------+-----------+
```
✅

## 4.7 Install and Configure Web Application

xFusionCorp Industries is planning to host two static websites on their infra in `Stratos Datacenter`. The development of these websites is still in-progress, but we want to get the servers ready. Please perform the following steps to accomplish the task:

a. Install `httpd` package and dependencies on `app server 3`.
b. Apache should serve on port `8089`.
c. There are two website's backups `/home/thor/blog` and `/home/thor/cluster` on `jump_host`. Set them up on Apache in a way that `blog` should work on the link `http://localhost:8089/blog/` and `cluster` should work on link `http://localhost:8089/cluster/` on the mentioned app server.
d. Once configured you should be able to access the website using `curl` command on the respective app server, i.e `curl http://localhost:8089/blog/` and `curl http://localhost:8089/cluster/`

---

```bash
$ scp -r /home/thor/blog/ /home/thor/cluster/ banner@172.16.238.12:/tmp/
index.html                                                                                   100%  117   340.9KB/s   00:00    
index.html                                                                                   100%  120   383.9KB/s   00:00

$ sshpass -p 'BigGr33n' ssh -o StrictHostKeyChecking=no banner@172.16.238.12
$ sudo su -
$ yum install httpd -y

$ systemctl enable httpd ; systemctl restart httpd ; systemctl status httpd
Created symlink /etc/systemd/system/multi-user.target.wants/httpd.service → /usr/lib/systemd/system/httpd.service.
● httpd.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; preset: disabled)
     Active: active (running) since Sat 2025-10-04 13:13:15 UTC; 90ms ago

$ sed -i 's/Listen 80/Listen 8089/g' /etc/httpd/conf/httpd.conf

$ grep DocumentRoot /etc/httpd/conf/httpd.conf 
# DocumentRoot: The directory out of which you will serve your
DocumentRoot "/var/www/html"

$ mv /tmp/blog /var/www/html/
$ mv /tmp/cluster /var/www/html/
$ ls /var/www/html/
blog  cluster

$ ls /var/www/html/blog /var/www/html/clustercluster
/var/www/html/blog:
total 4
-rw-r--r-- 1 banner banner 117 Oct  4 13:30 index.html

/var/www/html/cluster:
total 4
-rw-r--r-- 1 banner banner 120 Oct  4 13:30 index.html

$ id apache
uid=48(apache) gid=48(apache) groups=48(apache)

$ chown -R apache:apache /var/www/html/blog /var/www/html/cluster

$ ls -l /var/www/html/blog /var/www/html/cluster
/var/www/html/blog:
total 4
-rw-r--r-- 1 apache apache 117 Oct  4 13:30 index.html

/var/www/html/cluster:
total 4
-rw-r--r-- 1 apache apache 120 Oct  4 13:30 index.html

$ systemctl restart httpd

$ curl http://localhost:8089/blog/
<!DOCTYPE html>
<html>
<body>

<h1>KodeKloud</h1>

<p>This is a sample page for our blog website</p>

</body>
</html>

$ curl http://localhost:8089/cluster/
<!DOCTYPE html>
<html>
<body>

<h1>KodeKloud</h1>

<p>This is a sample page for our cluster website</p>

</body>
</html>
```
✅

## 4.8 Install and Configure PHP-FPM

The `Nautilus` application development team is planning to launch a new PHP-based application, which they want to deploy on `Nautilus` infra in `Stratos DC`. The development team had a meeting with the production support team and they have shared some requirements regarding the infrastructure. Below are the requirements they shared:
1. Install `nginx` on `app server 3`, configure it to use port `8094` and its document root should be `/var/www/html`.
2. Install `php-fpm` version `8.3` on `app server 3`, it should listen on port `9000`.
3. Configure `php-fpm` and `nginx` to work together.
4. Once configured correctly, you can test the website using `curl http://stapp03:8094/index.php` command from `jump host`. Please note that if the URL is displaying `index.php` in plain text, meaning you see `<?php` etc in the output, it indicates that either PHP-FPM is not installed or Nginx is not properly configured to work with PHP-FPM.

---

```bash
$ sshpass -p 'BigGr33n' ssh -o StrictHostKeyChecking=no banner@172.16.238.12
$ sudo su -

$ dnf install nginx -y
$ dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
$ dnf install https://rpms.remirepo.net/enterprise/remi-release-9.rpm
$ dnf module list php
Remi's Modular repository for Enterprise Linux 9 - x86_64                                      600 kB/s | 878 kB     00:01    
Safe Remi's RPM repository for Enterprise Linux 9 - x86_64                                     920 kB/s | 1.4 MB     00:01    
Last metadata expiration check: 0:00:01 ago on Sun Oct  5 13:09:24 2025.
CentOS Stream 9 - AppStream
Name                 Stream                   Profiles                                   Summary                               
php                  8.1                      common [d], devel, minimal                 PHP scripting language                
php                  8.2                      common [d], devel, minimal                 PHP scripting language                
php                  8.3                      common, devel, minimal                     PHP scripting language                
Remi's Modular repository for Enterprise Linux 9 - x86_64
Name                 Stream                   Profiles                                   Summary                               
php                  remi-7.4                 common [d], devel, minimal                 PHP scripting language                
php                  remi-8.0                 common [d], devel, minimal                 PHP scripting language                
php                  remi-8.1                 common [d], devel, minimal                 PHP scripting language                
php                  remi-8.2                 common [d], devel, minimal                 PHP scripting language                
php                  remi-8.3                 common [d], devel, minimal                 PHP scripting language                
php                  remi-8.4                 common [d], devel, minimal                 PHP scripting language                
php                  remi-8.5                 common [d], devel, minimal                 PHP scripting language                
Hint: [d]efault, [e]nabled, [x]disabled, [i]nstalled

$ dnf module install php:remi-8.3
$ php -v
PHP 8.3.26 (cli) (built: Sep 23 2025 17:57:26) (NTS gcc x86_64)
Copyright (c) The PHP Group
Zend Engine v4.3.26, Copyright (c) Zend Technologies

$ grep root /etc/nginx/nginx.conf | grep -v '#'
        root         /usr/share/nginx/html;
$ grep listen /etc/nginx/nginx.conf | grep -v '#'
        listen       80;
        listen       [::]:80;
$ sed -i 's/80;/8094;/g' /etc/nginx/nginx.conf
$ sed -i 's|/usr/share/nginx/html|/var/www/html|g' /etc/nginx/nginx.conf
$ grep listen /etc/nginx/nginx.conf | grep -v '#'
        listen       8094;
        listen       [::]:8094;
$ grep root /etc/nginx/nginx.conf | grep -v '#'
        root         /var/www/html;

$ grep 'listen =' /etc/php-fpm.d/www.conf
listen = /run/php-fpm/www.sock
$ sed -i 's|listen = /run/php-fpm/www.sock|listen = 127.0.0.1:9000|g' /etc/php-fpm.d/www.conf
$ grep 'listen =' /etc/php-fpm.d/www.conf
listen = 127.0.0.1:9000

$ grep 'server' /etc/nginx/conf.d/php-fpm.conf | grep -v '#'
        server unix:/run/php-fpm/www.sock;
$ sed -i 's|unix:/run/php-fpm/www.sock;|127.0.0.1:9000;|g' /etc/nginx/conf.d/php-fpm.conf
$ grep 'server' /etc/nginx/conf.d/php-fpm.conf | grep -v '#'
        server 127.0.0.1:9000;

$ grep 'user =' /etc/php-fpm.d/www.conf
user = apache
$ grep 'group =' /etc/php-fpm.d/www.conf | grep -v ';'
group = apache
$ sed -i 's|user = apache|user = nginx|g' /etc/php-fpm.d/www.conf
$ sed -i 's|group = apache|group = nginx|g' /etc/php-fpm.d/www.conf
$ grep 'user =' /etc/php-fpm.d/www.conf
user = nginx
$ grep 'group =' /etc/php-fpm.d/www.conf | grep -v ';'
group = nginx

$ systemctl enable nginx php-fpm ; systemctl restart nginx php-fpm ; systemctl status nginx php-fpm
Created symlink /etc/systemd/system/multi-user.target.wants/nginx.service → /usr/lib/systemd/system/nginx.service.
Created symlink /etc/systemd/system/multi-user.target.wants/php-fpm.service → /usr/lib/systemd/system/php-fpm.service.
● nginx.service - The nginx HTTP and reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; preset: disabled)
    Drop-In: /etc/systemd/system/nginx.service.d
             └─php-fpm.conf
     Active: active (running) since Sun 2025-10-05 19:25:09 UTC; 156ms ago
...
● php-fpm.service - The PHP FastCGI Process Manager
     Loaded: loaded (/usr/lib/systemd/system/php-fpm.service; enabled; preset: disabled)
     Active: active (running) since Sun 2025-10-05 19:25:09 UTC; 115ms ago

$ nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful

$ cat /var/www/html/index.php 
<?php
echo "Welcome to xFusionCorp Industries!";
?>

Switch to jumphost.
thor@jumphost ~$ curl http://stapp03:8094/index.php
Welcome to xFusionCorp Industries!
```
✅

## 4.9 Configure Nginx + PHP-FPM Using Unix Sock

The `Nautilus` application development team is planning to launch a new PHP-based application, which they want to deploy on `Nautilus` infra in `Stratos DC`. The development team had a meeting with the production support team and they have shared some requirements regarding the infrastructure. Below are the requirements they shared:

1. Install `nginx` on `app server 2`, configure it to use port `8096` and its document root should be `/var/www/html`.
2. Install `php-fpm` version `8.1` on `app server 2`, it must use the unix socket `/var/run/php-fpm/default.sock` (create the parent directories if don't exist).
3. Configure `php-fpm` and `nginx` to work together.
4. Once configured correctly, you can test the website using `curl http://stapp02:8096/index.php` command from `jump host`.

NOTE: We have copied two files, `index.php` and `info.php`, under `/var/www/html` as part of the `PHP-based application` setup. Please do not modify these files.

---

```bash
$ sshpass -p 'Am3ric@' ssh -o StrictHostKeyChecking=no steve@172.16.238.11
$ sudo su -

$ dnf install nginx -y
$ dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm -y
$ dnf install https://rpms.remirepo.net/enterprise/remi-release-9.rpm -y
$ dnf module list php
...
Name                 Stream                   Profiles                                   Summary                                       
php                  remi-8.1                 common [d], devel, minimal                 PHP scripting language                
...

$ dnf module install php:remi-8.1 -y
$ php -v
PHP 8.1.33 (cli) (built: Jul  1 2025 21:17:52) (NTS gcc x86_64)
Copyright (c) The PHP Group
Zend Engine v4.1.33, Copyright (c) Zend Technologies

$ cat default.d/php.conf 
# pass the PHP scripts to FastCGI server
#
# See conf.d/php-fpm.conf for socket configuration
#
index index.php index.html index.htm;

location ~ \.php$ {
    try_files $uri =404;
    fastcgi_intercept_errors on;
    fastcgi_index  index.php;
    include        fastcgi_params;
    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
    fastcgi_pass   php-fpm;
}

$ cat conf.d/php-fpm.conf 
# PHP-FPM FastCGI server
# network or unix domain socket configuration

upstream php-fpm {
        server unix:/run/php-fpm/www.sock;
}

$ grep root /etc/nginx/nginx.conf | grep -v '#'
        root         /usr/share/nginx/html;
$ grep listen /etc/nginx/nginx.conf | grep -v '#'
        listen       80;
        listen       [::]:80;
$ sed -i 's/80;/8096;/g' /etc/nginx/nginx.conf
$ sed -i 's|/usr/share/nginx/html|/var/www/html|g' /etc/nginx/nginx.conf
$ grep listen /etc/nginx/nginx.conf | grep -v '#'
        listen       8096;
        listen       [::]:8096;
$ grep root /etc/nginx/nginx.conf | grep -v '#'
        root         /var/www/html;

$ grep 'listen =' /etc/php-fpm.d/www.conf
listen = /run/php-fpm/www.sock
$ sed -i 's|listen = /run/php-fpm/www.sock|listen = /var/run/php-fpm/default.sock|g' /etc/php-fpm.d/www.conf
$ grep 'listen =' /etc/php-fpm.d/www.conf
listen = /var/run/php-fpm/default.sock

$ grep 'server' /etc/nginx/conf.d/php-fpm.conf | grep -v '#'
        server unix:/run/php-fpm/www.sock;
$ sed -i 's|unix:/run/php-fpm/www.sock;|unix:/var/run/php-fpm/default.sock;|g' /etc/nginx/conf.d/php-fpm.conf
$ grep 'server' /etc/nginx/conf.d/php-fpm.conf | grep -v '#'
        server unix:/var/run/php-fpm/default.sock;

$ grep 'user =' /etc/php-fpm.d/www.conf
user = apache
$ grep 'group =' /etc/php-fpm.d/www.conf | grep -v ';'
group = apache
$ sed -i 's|user = apache|user = nginx|g' /etc/php-fpm.d/www.conf
$ sed -i 's|group = apache|group = nginx|g' /etc/php-fpm.d/www.conf
$ grep 'user =' /etc/php-fpm.d/www.conf
user = nginx
$ grep 'group =' /etc/php-fpm.d/www.conf | grep -v ';'
group = nginx

$ systemctl enable nginx php-fpm ; systemctl restart nginx php-fpm ; systemctl status nginx php-fpm
Created symlink /etc/systemd/system/multi-user.target.wants/nginx.service → /usr/lib/systemd/system/nginx.service.
Created symlink /etc/systemd/system/multi-user.target.wants/php-fpm.service → /usr/lib/systemd/system/php-fpm.service.
● nginx.service - The nginx HTTP and reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; preset: disabled)
    Drop-In: /etc/systemd/system/nginx.service.d
             └─php-fpm.conf
     Active: active (running) since Mon 2025-10-06 18:59:11 UTC; 272ms ago
...
● php-fpm.service - The PHP FastCGI Process Manager
     Loaded: loaded (/usr/lib/systemd/system/php-fpm.service; enabled; preset: disabled)
     Active: active (running) since Mon 2025-10-06 18:59:11 UTC; 178ms ago

$ nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful

$ ss -xl | grep php-fpm
u_str LISTEN 0      511                 /var/run/php-fpm/default.sock 213125737            * 0 

$ cat /var/www/html/index.php 
<?php
echo "Welcome to xFusionCorp Industries!";
?>

Switch to jumphost.
thor@jumphost ~$ curl http://stapp03:8094/index.php
Welcome to xFusionCorp Industries!
```
✅

<img width="1128" height="653" alt="4" src="https://github.com/user-attachments/assets/c6c168a4-62a4-48a4-8ec6-7a0891ae313d" />

✅
