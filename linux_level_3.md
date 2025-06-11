# Linux Level 3 (Tasks 3.1-3.10) 

## 3.1 Apache Redirects

The Nautilus devops team got some requirements related to some Apache config changes. They need to setup some redirects for some URLs. There might be some more changes need to be done. Below you can find more details regarding that:

1. `httpd` is already installed on `app server 1`. Configure Apache to listen on port `3000`.
2. Configure Apache to add some redirects as mentioned below:
  - Redirect `http://stapp01.stratos.xfusioncorp.com:<Port>/` to `http://www.stapp01.stratos.xfusioncorp.com:<Port>/` i.e `non www` to `www`. This must be a permanent redirect i.e `301`
  - Redirect `http://www.stapp01.stratos.xfusioncorp.com:<Port>/blog/` to `http://www.stapp01.stratos.xfusioncorp.com:<Port>/news/`. This must be a temporary redirect i.e `302`.

---

```bash
$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10

$ sudo su -

$ systemctl start httpd

$ curl http://localhost:8080
Welcome to the Nautilus Group!
$ curl http://localhost:8080/blog/
Welcome to the Nautilus Group Blog!
$ curl http://localhost:8080/news/
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>404 Not Found</title>
</head><body>
<h1>Not Found</h1>
<p>The requested URL /news/ was not found on this server.</p>
</body></html>
[root@stapp01 ~]#

$ grep -n Listen /etc/httpd/conf/httpd.conf 
...
42:Listen 8080

$ grep -n Listen /etc/httpd/conf/httpd.conf 
...
42:Listen 3000

$ cat /etc/httpd/conf.d/main.conf
<VirtualHost *:3000>
    ServerName stapp01.stratos.xfusioncorp.com
    Redirect 301 / http://www.stapp01.stratos.xfusioncorp.com:3000/
</VirtualHost>

<VirtualHost *:3000>
    ServerName www.stapp01.stratos.xfusioncorp.com
    Redirect 302 /blog/ http://www.stapp01.stratos.xfusioncorp.com:3000/news/
</VirtualHost>

$ systemctl restart httpd

$ curl http://stapp01.stratos.xfusioncorp.com:3000
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>301 Moved Permanently</title>
</head><body>
<h1>Moved Permanently</h1>
<p>The document has moved <a href="http://www.stapp01.stratos.xfusioncorp.com:3000/">here</a>.</p>
</body></html>

$ curl http://www.stapp01.stratos.xfusioncorp.com:3000
Welcome to the Nautilus Group!

$ curl http://www.stapp01.stratos.xfusioncorp.com:3000/blog/
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>302 Found</title>
</head><body>
<h1>Found</h1>
<p>The document has moved <a href="http://www.stapp01.stratos.xfusioncorp.com:3000/news/">here</a>.</p>
</body></html>

$ curl http://www.stapp01.stratos.xfusioncorp.com:3000/news/
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>404 Not Found</title>
</head><body>
<h1>Not Found</h1>
<p>The requested URL /news/ was not found on this server.</p>
</body></html>
```
✅

## 3.2 Install And Configure SFTP

Some of the developers from Nautilus project team have asked for SFTP access to at least one of the app server in Stratos DC. After going through the requirements, the system admins team has decided to configure the SFTP server on `App Server 2` server in Stratos Datacenter. Please configure it as per the following instructions:
- Create a SFTP user `siva` and set its password to `YchZHRcLkL`. There is already a group called `ftp`, you can utilise the same.
- Password authentication should be enabled for this user.
- SFTP user should only be allowed to make SFTP connections.

---

```bash
$ sshpass -p 'Am3ric@' ssh -o StrictHostKeyChecking=no steve@172.16.238.11

$ sudo su -

$ cat /etc/group | grep ftp
ftp:x:50:

$ command -v nologin
/usr/sbin/nologin

$ useradd -m -G ftp -s /usr/sbin/nologin siva
$ passwd siva

$ mkdir -p /home/siva/uploads

$ ls -l /home/siva/
total 4
drwxr-xr-x 2 root root 4096 May 29 09:49 uploads

$ chown root:root /home/siva
$ chmod 755 /home/siva
$ ls -l /home/|grep siva
drwxr-xr-x 3 root    root    4096 May 29 09:49 siva
$ chown siva:ftp /home/siva/uploads
$ ls -l /home/siva
total 4
drwxr-xr-x 2 siva ftp 4096 May 29 09:49 uploads

$ vi /etc/ssh/sshd_config
# add at the end
Match User siva
    ChrootDirectory /home/siva
    ForceCommand internal-sftp
    AllowTcpForwarding no
    X11Forwarding no
    PasswordAuthentication yes

$ systemctl restart sshd

$ exit
$ sftp siva@172.16.238.11
siva@172.16.238.11's password: 
Connected to 172.16.238.11.
sftp> 
```
✅

## 3.3 Install and Configure Tomcat Server

The `Nautilus` application development team recently finished the beta version of one of their Java-based applications, which they are planning to deploy on one of the app servers in `Stratos DC`. After an internal team meeting, they have decided to use the `tomcat` application server. Based on the requirements mentioned below complete the task:
- Install `tomcat` server on `App Server 2`.
- Configure it to run on port `6200`.
- There is a `ROOT.war` file on `Jump host` at location `/tmp`.
Deploy it on this tomcat server and make sure the webpage works directly on base URL i.e curl `http://stapp02:6200`

---

```bash
$ scp /tmp/ROOT.war steve@172.16.238.11:/tmp/
ROOT.war                                                                                     100% 4529    10.8MB/s   00:00 

$ sshpass -p 'Am3ric@' ssh -o StrictHostKeyChecking=no steve@172.16.238.11

$ sudo su -

$ yum install tomcat -y 

$ systemctl start tomcat
$ systemctl status tomcat
● tomcat.service - Apache Tomcat Web Application Container
     Loaded: loaded (/usr/lib/systemd/system/tomcat.service; disabled; preset: disabled)
     Active: active (running) since Fri 2025-05-30 07:59:32 UTC; 1s ago

$ curl localhost:8080
<!doctype html><html lang="en"><head><title>HTTP Status 404 – Not Found</title><style type="text/css">body {font-family:Tahoma,Arial,sans-serif;} h1, h2, h3, b {color:white;background-color:#525D76;} h1 {font-size:22px;} h2 {font-size:16px;} h3 {font-size:14px;} p {font-size:12px;} a {color:black;} .line {height:1px;background-color:#525D76;border:none;}</style></head><body><h1>HTTP Status 404 – Not Found</h1><hr class="line" /><p><b>Type</b> Status Report</p><p><b>Description</b> The origin server did not find a current representation for the target resource or is not willing to disclose that one exists.</p><hr class="line" /><h3>Apache Tomcat/9.0.87</h3></body></html>

$ vi /etc/tomcat/server.xml 
#change 8080 to 6200
    <Connector port="8080" protocol="HTTP/1.1"
    <Connector port="6200" protocol="HTTP/1.1"

$ curl localhost:6200
<!doctype html><html lang="en"><head><title>HTTP Status 404 – Not Found</title><style type="text/css">body {font-family:Tahoma,Arial,sans-serif;} h1, h2, h3, b {color:white;background-color:#525D76;} h1 {font-size:22px;} h2 {font-size:16px;} h3 {font-size:14px;} p {font-size:12px;} a {color:black;} .line {height:1px;background-color:#525D76;border:none;}</style></head><body><h1>HTTP Status 404 – Not Found</h1><hr class="line" /><p><b>Type</b> Status Report</p><p><b>Description</b> The origin server did not find a current representation for the target resource or is not willing to disclose that one exists.</p><hr class="line" /><h3>Apache Tomcat/9.0.87</h3></body></html>

$ mv /tmp/ROOT.war /usr/share/tomcat/webapps/
$ ls /usr/share/tomcat/webapps/
ROOT.war

$ curl http://stapp02:6200
<!DOCTYPE html>
<!--
To change this license header, choose License Headers in Project Properties.
To change this template file, choose Tools | Templates
and open the template in the editor.
-->
<html>
    <head>
        <title>SampleWebApp</title>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
    </head>
    <body>
        <h2>Welcome to xFusionCorp Industries!</h2>
        <br>
    
    </body>
</html>
```
✅

## 3.4 Linux Network Services

Our monitoring tool has reported an issue in `Stratos Datacenter`. One of our app servers has an issue, as its Apache service is not reachable on port `8084` (which is the Apache port). The service itself could be down, the firewall could be at fault, or something else could be causing the issue.

Use tools like `telnet`, `netstat`, etc. to find and fix the issue. Also make sure Apache is reachable from the jump host without compromising any security settings.

Once fixed, you can test the same using command `curl http://stapp01:8084` command from jump host.

---

```bash
$ curl http://stapp01:8087
curl: (7) Failed to connect to stapp01 port 8087: No route to host

sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10

$ systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
   Active: failed (Result: exit-code) since Sat 2025-05-31 16:43:22 UTC; 6min ago
     Docs: man:httpd.service(8)
  Process: 512 ExecStart=/usr/sbin/httpd $OPTIONS -DFOREGROUND (code=exited, status=1/FAILURE)
 Main PID: 512 (code=exited, status=1/FAILURE)
   Status: "Reading configuration..."

May 31 16:43:22 stapp01.stratos.xfusioncorp.com httpd[512]: (98)Address already in use: AH00072: make_sock: could not bind to a
ddress 0.0.0.0:8087
May 31 16:43:22 stapp01.stratos.xfusioncorp.com httpd[512]: no listening sockets available, shutting down
May 31 16:43:22 stapp01.stratos.xfusioncorp.com httpd[512]: AH00015: Unable to open logs
May 31 16:43:22 stapp01.stratos.xfusioncorp.com systemd[1]: httpd.service: Child 512 belongs to httpd.s
ervice.
May 31 16:43:22 stapp01.stratos.xfusioncorp.com systemd[1]: httpd.service: Main process exited, code=ex
ited, status=1/FAILURE
May 31 16:43:22 stapp01.stratos.xfusioncorp.com systemd[1]: httpd.service: Failed with result 'exit-code'.
May 31 16:43:22 stapp01.stratos.xfusioncorp.com systemd[1]: httpd.service: Changed start -> failed
May 31 16:43:22 stapp01.stratos.xfusioncorp.com systemd[1]: httpd.service: Job httpd.service/start fini
shed, result=failed
May 31 16:43:22 stapp01.stratos.xfusioncorp.com systemd[1]: Failed to start The Apache HTTP Server.
May 31 16:43:22 stapp01.stratos.xfusioncorp.com systemd[1]: httpd.service: Unit entered failed state.

$ sudo netstat -tulpn | grep :8087
tcp        0      0 127.0.0.1:8087          0.0.0.0:*               LISTEN      451/sendmail: accep 

$ ps -p 451 -f
UID          PID    PPID  C STIME TTY          TIME CMD
root         451       1  0 16:43 ?        00:00:00 sendmail: accepting connections

$ which sendmail
/usr/sbin/sendmail
$ rpm -qf /usr/sbin/sendmail
sendmail-8.15.2-34.el8.x86_64

$ systemctl status sendmail
● sendmail.service - Sendmail Mail Transport Agent
   Loaded: loaded (/usr/lib/systemd/system/sendmail.service; disabled; vendor preset: disabled)
   Active: active (running) since Sat 2025-05-31 16:43:22 UTC; 9min ago

$ systemctl stop sendmail
$ systemctl status sendmail
● sendmail.service - Sendmail Mail Transport Agent
   Loaded: loaded (/usr/lib/systemd/system/sendmail.service; disabled; vendor preset: disabled)

$ systemctl restart httpd
$ systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
   Active: active (running) since Sat 2025-05-31 16:58:29 UTC; 1s ago

$ sudo iptables -I INPUT -p tcp -m tcp --dport 8087 -j ACCEPT && sudo iptables-save > /etc/sysconfig/iptables && cat /etc/sysconfig/iptables

thor@jumphost ~$ curl http://172.16.238.10:8087
```
✅

## 3.5 IPtables Installation And Configuration

We have one of our websites up and running on our `Nautilus` infrastructure in `Stratos DC`. Our security team has raised a concern that right now Apache’s port i.e `3004` is open for all since there is no firewall installed on these hosts. So we have decided to add some security layer for these hosts and after discussions and recommendations we have come up with the following requirements:
- Install `iptables` and all its dependencies on each app host.
- Block incoming port `3004` on all apps for everyone except for LBR host.
- Make sure the rules remain, even after system reboot.

---

```bash
$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10

$ sudo su -
$ yum install iptables iptables-services -y

$ systemctl start iptables && systemctl enable iptables && systemctl status iptables
Created symlink /etc/systemd/system/multi-user.target.wants/iptables.service → /usr/lib/systemd/system/iptables.service.
● iptables.service - IPv4 firewall with iptables
     Loaded: loaded (/usr/lib/systemd/system/iptables.service; enabled; preset: disabled)
     Active: active (exited) since Sun 2025-06-01 19:35:46 UTC; 45s ago

$ iptables -A INPUT -p tcp --dport 3004 -s 172.16.238.14 -j ACCEPT
$ iptables -A INPUT -p tcp --dport 3004 -j DROP
$ iptables -R INPUT 5 -p icmp -j REJECT

$ iptables -L -n --line-numbers
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination         
1    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0            state RELATED,ESTABLISHED
2    ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0           
3    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           
4    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:22
5    REJECT     icmp --  0.0.0.0/0            0.0.0.0/0            reject-with icmp-port-unreachable
6    ACCEPT     tcp  --  172.16.238.14        0.0.0.0/0            tcp dpt:3004
7    DROP       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:3004

Chain FORWARD (policy ACCEPT)
num  target     prot opt source               destination         
1    REJECT     all  --  0.0.0.0/0            0.0.0.0/0            reject-with icmp-host-prohibited

Chain OUTPUT (policy ACCEPT)
num  target     prot opt source               destination

$ service iptables save
iptables: Saving firewall rules to /etc/sysconfig/iptables: [  OK  ]

$ systemctl restart iptables && systemctl status iptables

$ exit
$ curl http://172.16.238.12:3004
^C
$ sshpass -p 'Mischi3f' ssh -o StrictHostKeyChecking=no loki@172.16.238.14
$ curl http://172.16.238.10:3004
```
Repeat for Servers App1 and App2.

✅

## 3.6 Linux Nginx as Reverse Proxy

`Nautilus` system admin's team is planning to deploy a front end application for their backup utility on `Nautilus Backup Server`, so that they can manage the backups of different websites from a graphical user interface. They have shared requirements to set up the same; please accomplish the tasks as per detail given below:
- Install `Apache` Server on `Nautilus Backup Server` and configure it to use `5002` port (do not bind it to `127.0.0.1` only, keep it default i.e let Apache listen on server's IP, hostname, localhost, 127.0.0.1 etc).
- Install `Nginx` webserver on `Nautilus Backup Server` and configure it to use `8093`.
- Configure `Nginx` as a reverse proxy server for `Apache`.
- There is a sample index file `/home/thor/index.html` on `Jump Host`, copy that file to `Apache's` document root.
- Make sure to start `Apache` and `Nginx` services.
- You can test final changes using `curl` command, e.g `curl http://<backup server IP or Hostname>:8093`.

---

```bash
$ cat /home/thor/index.html 
Welcome to xFusionCorp Industries!

$ scp /home/thor/index.html clint@172.16.238.16:/tmp/
index.html                                                                                   100%   34   125.2KB/s   00:00 

$ sshpass -p 'H@wk3y3' ssh -o StrictHostKeyChecking=no clint@172.16.238.16

$ sudo su -

$ yum install httpd nginx -y

$ vi /etc/httpd/conf/httpd.conf
# change 80 to 5002
Listen 80
Listen 5002

$ vi /etc/nginx/conf.d/reverse-proxy.conf
# add this config
server {
    listen 8093;

    location / {
        proxy_pass http://localhost:5002;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}

$ systemctl start nginx httpd

$ mv /tmp/index.html /var/www/html/ && ls /var/www/html/
index.html

$ curl localhost:5002
Welcome to xFusionCorp Industries!
$ curl localhost:8093
Welcome to xFusionCorp Industries!

$ exit
$ curl http://172.16.238.16:8093
Welcome to xFusionCorp Industries!
```
✅

## 3.7 Configure protected directories in Apache

xFusionCorp Industries has hosted several static websites on Nautilus Application Servers in Stratos DC. There are some confidential directories in the document root that need to be password protected. Since they are using Apache for hosting the websites, the production support team has decided to use .htaccess with basic auth. There is a website that needs to be uploaded to `/var/www/html/data` on `Nautilus App Server 2`. However, we need to set up the authentication before that.
- Create `/var/www/html/devops` direcotry if doesn't exist.
- Add a user `ammar` in htpasswd and set its password to `GyQkFRVNr3`.
- There is a file `/tmp/index.html` present on Jump Server. Copy the same to the directory you created, please make sure default document root should remain `/var/www/html`. Also website should work on URL `http://<app-server-hostname>:8080/devops/`

---

```bash
$ scp /tmp/index.html steve@172.16.238.11:/tmp/
index.html                                                                                   100%   52   269.0KB/s   00:00 

$ sshpass -p 'Am3ric@' ssh -o StrictHostKeyChecking=no steve@172.16.238.11

$ sudo su -

$ yum install httpd-tools -y

$ htpasswd -c /etc/httpd/.htpasswd ammar

$ vi /etc/httpd/conf/httpd.conf 
<Directory "/var/www/html">
,,,
    AllowOverride None

$ systemctl restart httpd

$ mkdir /var/www/html/devops && cd /var/www/html/devops
$ vi .htaccess
AuthType Basic
AuthName "Password Required"
Require valid-user
AuthUserFile /etc/httpd/.htpasswd

$ mv /tmp/index.html /var/www/html/devops/
$ ls -la /var/www/html/devops/
total 16
drwxr-xr-x 2 root  root  4096 Jun  3 21:27 .
drwxr-xr-x 1 root  root  4096 Jun  3 21:27 ..
-rw-r--r-- 1 root  root    97 Jun  3 21:27 .htaccess
-rw-r--r-- 1 steve steve   52 Jun  3 21:24 index.html

$ curl localhost:8080/devops/
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>401 Unauthorized</title>
</head><body>
<h1>Unauthorized</h1>
<p>This server could not verify that you
are authorized to access the document
requested.  Either you supplied the wrong
credentials (e.g., bad password), or your
browser doesn't understand how to supply
the credentials required.</p>
</body></html>

$ curl -u ammar localhost:8080/devops/
This is xFusionCorp Industries Protected Directory!
```
✅

## 3.8 Linux Process Troubleshooting

The production support team of xFusionCorp Industries has deployed some of the latest monitoring tools to keep an eye on every service, application, etc. running on the systems. One of the monitoring systems reported about Apache service unavailability on one of the app servers in `Stratos DC`.

Identify the faulty app host and fix the issue. Make sure Apache service is up and running on all app hosts. They might not hosted any code yet on these servers so you need not to worry about if Apache isn't serving any pages or not, just make sure service is up and running. Also, make sure Apache is running on port `5002` on all app servers.

---

```bash
$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10

$ sudo su -

$ systemctl status httpd -l
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
   Active: failed (Result: exit-code) since Wed 2025-06-04 20:18:40 UTC; 16s ago
...
Jun 04 20:18:40 stapp01.stratos.xfusioncorp.com httpd[813]: (98)Address already in use: AH00072: make_sock: could not bind to address 0.0.0.0:5002
Jun 04 20:18:40 stapp01.stratos.xfusioncorp.com httpd[813]: no listening sockets available, shutting down
...

$ netstat -tulpn | grep :5002
tcp        0      0 127.0.0.1:5002          0.0.0.0:*               LISTEN      724/sendmail: accep 

$ ps -p 724 -f
UID          PID    PPID  C STIME TTY          TIME CMD
root         724       1  0 19:50 ?        00:00:00 sendmail: accepting connections

$ systemctl stop sendmail && systemctl status sendmail
● sendmail.service - Sendmail Mail Transport Agent
   Loaded: loaded (/usr/lib/systemd/system/sendmail.service; enabled; vendor preset: disabled)
   Active: inactive (dead) since Wed 2025-06-04 20:21:32 UTC; 1s ago

$ systemctl restart httpd && systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
   Active: active (running) since Wed 2025-06-04 20:22:34 UTC; 4s ago

$ grep Listen /etc/httpd/conf/httpd.conf 
...
Listen 5002
```
Check other App Servers.

✅

## 3.9 PAM Authentication For Apache

We have a requirement where we want to password protect a directory in the Apache web server document root. We want to password protect `http://<website-url>:<apache_port>/protected/` URL as per the following requirements (you can use any `website-url` for it like `localhost` since there are no such specific requirements as of now). Setup the same on `App server 3` as per below mentioned requirements:
- We want to use basic authentication.
- We do not want to use `htpasswd` file based authentication. Instead, we want to use `PAM` authentication, i.e `Basic Auth + PAM` so that we can authenticate with a Linux user.
- We already have a user `ammar` with password `B4zNgHA7Ya` which you need to provide access to.
- You can test the same using a curl command from jump host `curl http://<website-url>:<apache_port>/protected/`.

---

```bash
$ sshpass -p 'BigGr33n' ssh -o StrictHostKeyChecking=no banner@172.16.238.12

$ sudo su -

$ yum --enablerepo=epel -y install mod_authnz_external pwauth

$ command -v pwauth
/usr/bin/pwauth

$ id ammar
uid=1001(ammar) gid=1001(ammar) groups=1001(ammar)

$echo "LoadModule authnz_external_module modules/mod_authnz_external.so" | tee /etc/httpd/conf.modules.d/00-authnz-external.conf

$ cat /var/www/html/protected/index.html 
This is KodeKloud Protected Directory

$ cat /etc/httpd/conf.d/authnz_external.conf
DefineExternalAuth pwauth pipe /usr/bin/pwauth
<Directory /var/www/html/protected> 
        AuthType Basic
        AuthName "PAM Authentication"
        AuthBasicProvider external
        AuthExternal pwauth
        require user ammar 
</Directory>

$ systemctl restart httpd

$ curl -u ammar:B4zNgHA7Ya localhost:8080/protected/
This is KodeKloud Protected Directory

$ curl localhost:8080/protected/
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>401 Unauthorized</title>
</head><body>
<h1>Unauthorized</h1>
<p>This server could not verify that you
are authorized to access the document
requested.  Either you supplied the wrong
credentials (e.g., bad password), or your
browser doesn't understand how to supply
the credentials required.</p>
</body></html>
```
✅

## 3.10 Setup SSL for Nginx

The system admins team of `xFusionCorp Industries` needs to deploy a new application on `App Server 2` in `Stratos Datacenter`. They have some pre-requites to get ready that server for application deployment. Prepare the server as per requirements shared below:
1. Install and configure `nginx` on `App Server 2`.
2. On `App Server 2` there is a self signed SSL certificate and key present at location `/tmp/nautilus.crt` and `/tmp/nautilus.key`. Move them to some appropriate location and deploy the same in Nginx.
3. Create an `index.html` file with content `Welcome!` under Nginx document root.
4. For final testing try to access the `App Server 2` link (either hostname or IP) from `jump host` using curl command. For example `curl -Ik https://<app-server-ip>/`.

---

```bash
$ sshpass -p 'Am3ric@' ssh -o StrictHostKeyChecking=no steve@172.16.238.11

$ sudo su -

$ yum install nginx -y

$ ls -l /tmp/nautilus*
-rw-r--r-- 1 root root 2170 Jun 11 11:25 /tmp/nautilus.crt
-rw-r--r-- 1 root root 3267 Jun 11 11:25 /tmp/nautilus.key

$ mv /tmp/nautilus.crt /etc/ssl/certs/
$ mv /tmp/nautilus.key /etc/ssl/private/
$ chmod 600 /etc/ssl/private/nautilus.key
$ ls -l /etc/ssl/certs/nautilus.crt 
-rw-r--r-- 1 root root 2170 Jun 11 11:25 /etc/ssl/certs/nautilus.crt
$ ls -l /etc/ssl/private/nautilus.key 
-rw------- 1 root root 3267 Jun 11 11:25 /etc/ssl/private/nautilus.key

$ cd /usr/share/nginx/html/ 
$ echo 'Welcome!' > index.html 
$ cat index.html 
Welcome!

$ systemctl start nginx
$ systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; preset: disabled)
     Active: active (running) since Wed 2025-06-11 11:50:08 UTC; 1s ago

$ vi /etc/nginx/nginx.conf
# Settings for a TLS enabled server.
#
    server {
        listen       443 ssl http2;
        listen       [::]:443 ssl http2;
        server_name  _;
        root         /usr/share/nginx/html;

        ssl_certificate "/etc/ssl/certs/nautilus.crt";
        ssl_certificate_key "/etc/ssl/private/nautilus.key";
        ssl_session_cache shared:SSL:1m;
        ssl_session_timeout  10m;
        ssl_ciphers PROFILE=SYSTEM;
        ssl_prefer_server_ciphers on;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
}

$ systemctl reload nginx

thor@jumphost ~$ curl -Ik https://172.16.238.11/
HTTP/2 200 
server: nginx/1.20.1
date: Wed, 11 Jun 2025 11:56:54 GMT
content-type: text/html
content-length: 9
last-modified: Wed, 11 Jun 2025 11:49:24 GMT
etag: "68496d44-9"
accept-ranges: bytes

thor@jumphost ~$ curl -k https://172.16.238.11/
Welcome!
```
✅

![LINUX3](https://github.com/user-attachments/assets/baef13a6-c7c6-4ff2-90ff-67f435e51c54)

