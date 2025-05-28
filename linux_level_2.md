# Linux Level 2 (Tasks 2.1-2.24) 

## 2.1 Create a Cron Job

The `Nautilus` system admins team has prepared scripts to automate several day-to-day tasks. They want them to be deployed on all app servers in `Stratos DC` on a set schedule. Before that they need to test similar functionality with a sample cron job. Therefore, perform the steps below:
1. Install `cronie` package on all `Nautilus` app servers and start `crond` service.
2. Add a cron `*/5 * * * * echo hello > /tmp/cron_text` for `root` user.

---

```bash
$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10
$ sudo su -

$ sudo yum install -y cronie
$ sudo systemctl start crond

$ sudo crontab -e # then add this to the file: */5 * * * * echo hello > /tmp/cron_text

$ sudo crontab -l -u root
*/5 * * * * echo hello > /tmp/cron_text

$ sudo systemctl status crond
Warning: The unit file, source configuration file or drop-ins of crond.service changed on disk. Run 'systemctl daem
on-reload' to reload units.
● crond.service - Command Scheduler
   Loaded: loaded (/usr/lib/systemd/system/crond.service; enabled; vendor preset: enabled)
   Active: active (running) since Sat 2024-05-11 16:33:25 UTC; 1min 26s ago

$ sudo systemctl restart crond
$ sudo ls /var/spool/cron
$ sudo cat /var/spool/cron/root  
```

Repeat the same steps for App Server 2 and 3.

✅

## 2.2 Linux Banner

During the monthly compliance meeting, it was pointed out that several servers in the `Stratos DC` do not have a valid banner. The security team has provided serveral approved templates which should be applied to the servers to maintain compliance. These will be displayed to the user upon a successful login.

Update the `message of the day` on all application and db servers for `Nautilus`. Make use of the approved template located at `/home/thor/nautilus_banner` on jump host

---

```bash
$ ls /home/thor/nautilus_banner
/home/thor/nautilus_banner

$ scp /home/thor/nautilus_banner tony@172.16.238.10:/home/tony
nautilus_banner                                                                             100% 2530    10.6MB/s   00:00    
$ scp /home/thor/nautilus_banner steve@172.16.238.11:/home/steve 
nautilus_banner                                                                             100% 2530     5.0MB/s   00:00    
$ scp /home/thor/nautilus_banner banner@172.16.238.12:/home/banner
nautilus_banner                                                                             100% 2530     6.1MB/s   00:00    
$ scp /home/thor/nautilus_banner peter@172.16.239.10:/home/peter
nautilus_banner                                                                             100% 2530     4.9MB/s   00:00

$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10
$ cat /etc/motd

$ sudo mv /home/tony/nautilus_banner /etc/motd
$ cat /etc/motd
################################################################################################
  .__   __.      ___      __    __  .___________. __   __       __    __       _______.        # 
       |  \ |  |     /   \    |  |  |  | |           ||  | |  |     |  |  |  |     /       |   #
       |   \|  |    /  ^  \   |  |  |  | `---|  |----`|  | |  |     |  |  |  |    |   (----`   #
       |  . `  |   /  /_\  \  |  |  |  |     |  |     |  | |  |     |  |  |  |     \   \       #
       |  |\   |  /  _____  \ |  `--'  |     |  |     |  | |  `----.|  `--'  | .----)   |      #
       |__| \__| /__/     \__\ \______/      |__|     |__| |_______| \______/  |_______/       #
                                                                                               #
                                                                                               #
                                                                                               # 
                                                                                               #
                                 # #  ( )                                                      #
                                  ___#_#___|__                                                 #
                              _  |____________|  _                                             #
                       _=====| | |            | | |==== _                                      #
                 =====| |.---------------------------. | |====                                 #
   <--------------------'   .  .  .  .  .  .  .  .   '--------------/                          #
     \                                                             /                           #
      \_______________________________________________WWS_________/                            #
  wwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwww                        #
wwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwww                       # 
   wwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwww                         #
                                                                                               #
                                                                                               #
################################################################################################
Warning! All Nautilus systems are monitored and audited. Logoff immediately if you are not authorized![tony@stapp01 ~]
```

Repeat the same steps for other servers.

✅

## 2.3 Linux Collaborative Directories

The Nautilus team doesn't want its data to be accessed by any of the other groups/teams due to security reasons and want their data to be strictly accessed by the `sysadmin` group of the team.

Setup a collaborative directory `/sysadmin/data` on `app server 1` in `Stratos Datacenter`.

The directory should be group owned by the group `sysadmin` and the group should own the files inside the directory. The directory should be `read/write/execute` to the user and group owners, and `others` should not have any access.

---

```bash
$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10

$ sudo mkdir -p /sysadmin/data

$ ls -l /sysadmin/
total 4
drwxr-xr-x 2 root root 4096 May 13 19:40 data

$ sudo chown :sysadmin /sysadmin/data

$ ls -l /sysadmin/
total 4
drwxr-xr-x 2 root sysadmin 4096 May 13 19:40 data

$ sudo chmod 770 /sysadmin/data

$ ls -l /sysadmin/
total 4
drwxrwx--- 2 root sysadmin 4096 May 13 19:40 data

$ sudo chmod g+s /sysadmin/data

$ ls -l /sysadmin/
total 4
drwxrws--- 2 root sysadmin 4096 May 13 19:40 data
```
✅

## 2.4 Linux String Substitute (sed)

There is some data on `Nautilus App Server 1` in `Stratos DC`. Data needs to be altered in several of the files. On `Nautilus App Server 1`, alter the `/home/BSD.txt` file as per details given below:
- Delete all lines containing word `copyright` and save results in `/home/BSD_DELETE.txt` file. (Please be aware of case sensitivity)
- Replace all occurrence of word `from` to `is` and save results in `/home/BSD_REPLACE.txt` file.

`Note:` Let's say you are asked to replace word `to` with `from`. In that case, make sure not to alter any words containing the string itself; for example upto, contributor etc.

---

```bash
$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10

$ ls -l /home/BSD.txt 
-rw-r--r-- 1 tony tony 9919 May 14 11:35 /home/BSD.txt

$ grep -e copyright /home/BSD.txt | wc -l
18

$ sed '/\<copyright\>/d' /home/BSD.txt > /home/BSD_DELETE.txt

$ grep -e copyright /home/BSD_DELETE.txt | wc -l
0

$ grep -w 'from' /home/BSD.txt | wc -l
6
$ grep -w 'is' /home/BSD.txt | wc -l
0

$ sed 's/\bfrom\b/is/g' /home/BSD.txt > /home/BSD_REPLACE.txt

$ grep -w 'from' /home/BSD_REPLACE.txt | wc -l
0
$ grep -w 'is' /home/BSD_REPLACE.txt | wc -l
6
```
✅

## 2.5 Linux SSH Authentication

The system admins team of `xFusionCorp Industries` has set up some scripts on `jump host` that run on regular intervals and perform operations on all app servers in `Stratos Datacenter`. To make these scripts work properly we need to make sure the `thor` user on jump host has password-less SSH access to all app servers through their respective sudo users (i.e `tony` for app server 1). Based on the requirements, perform the following:

Set up a password-less authentication from user `thor` on jump host to all app servers through their respective sudo users.

---

```bash
$ ssh-keygen

$ ssh-copy-id tony@stapp01
$ ssh-copy-id steve@stapp02
$ ssh-copy-id banner@stapp03

$ ssh tony@stapp01 #try logging with other servers also
```

## 2.6 Linux Find Command

During a routine security audit, the team identified an issue on the Nautilus App Server. Some malicious content was identified within the website code. After digging into the issue they found that there might be more infected files. Before doing a cleanup they would like to find all similar files and copy them to a safe location for further investigation. Accomplish the task as per the following requirements:
1. On `App Server 2` at location `/var/www/html/beta` find out all files (not directories) having `.js` extension.
2. Copy all those files along with their `parent directory structure` to location `/beta` on same server.
3. Please make sure not to copy the entire `/var/www/html/beta` directory content.

---

```bash
$ sshpass -p 'Am3ric@' ssh -o StrictHostKeyChecking=no steve@172.16.238.11

$ ls -l /var/www/html/beta
total 224
-rw-r--r--  1 root root   405 Feb  6  2020 index.php
-rw-r--r--  1 root root 19915 Jan  1  2022 license.txt
-rw-r--r--  1 root root  7389 Sep 16  2022 readme.html
-rw-r--r--  1 root root  7205 Sep 16  2022 wp-activate.php
drwxr-xr-x  9 root root  4096 Nov 15  2022 wp-admin
-rw-r--r--  1 root root   351 Feb  6  2020 wp-blog-header.php
-rw-r--r--  1 root root  2338 Nov  9  2021 wp-comments-post.php
-rw-r--r--  1 root root  3001 Dec 14  2021 wp-config-sample.php
drwxr-xr-x  4 root root  4096 Nov 15  2022 wp-content
-rw-r--r--  1 root root  5543 Sep 20  2022 wp-cron.php
drwxr-xr-x 27 root root 12288 Nov 15  2022 wp-includes
-rw-r--r--  1 root root  2494 Mar 19  2022 wp-links-opml.php
-rw-r--r--  1 root root  3985 Sep 19  2022 wp-load.php
-rw-r--r--  1 root root 49135 Sep 19  2022 wp-login.php
-rw-r--r--  1 root root  8522 Oct 17  2022 wp-mail.php
-rw-r--r--  1 root root 24587 Sep 26  2022 wp-settings.php
-rw-r--r--  1 root root 34350 Sep 17  2022 wp-signup.php
-rw-r--r--  1 root root  4914 Oct 17  2022 wp-trackback.php
-rw-r--r--  1 root root  3236 Jun  8  2020 xmlrpc.php
$ ls -l /beta
total 0

$ find /var/www/html/beta -type f -name '*.js' -exec cp --parents \{\} /beta \;

$ ls -l /beta
total 4
drwxr-xr-x 3 root root 4096 May 17 11:47 var
```
✅

## 2.7 Install a package

As per new application requirements shared by the Nautilus project development team, serveral new packages need to be installed on all app servers in Stratos Datacenter. Most of them are completed except for git.

Therefore, install the `git` package on all app-servers.

---

```bash
$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10
$ sudo yum update -y
$ sudo yum install git -y
$ git --version
git version 2.43.0
```

Repeat the same steps for App Server 2 and 3.
✅

## 2.8 Install Ansible

During the weekly meeting, the Nautilus DevOps team discussed about the automation and configuration management solutions that they want to implement. While considering several options, the team has decided to go with `Ansible` for now due to its simple setup and minimal pre-requisites. The team wanted to start testing using Ansible, so they have decided to use `jump host` as an Ansible controller to test different kind of tasks on rest of the servers.

Install `ansible` version `4.9.0` on `Jump host` using `pip3` only. Make sure Ansible binary is available globally on this system, i.e all users on this system are able to run Ansible commands.

---

```bash
$ python3 --version
Python 3.9.18
$ pip3 --version
pip 24.0 from /usr/local/lib/python3.9/site-packages/pip (python 3.9)

$ sudo su -

$ pip3 install --upgrade pip
Requirement already satisfied: pip in /usr/local/lib/python3.9/site-packages (24.0)
Collecting pip
  Downloading pip-25.1.1-py3-none-any.whl.metadata (3.6 kB)
Downloading pip-25.1.1-py3-none-any.whl (1.8 MB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 1.8/1.8 MB 3.7 MB/s eta 0:00:00
Installing collected packages: pip
  Attempting uninstall: pip
    Found existing installation: pip 24.0
    Uninstalling pip-24.0:
      Successfully uninstalled pip-24.0
Successfully installed pip-25.1.1

$ pip3 install ansible==4.9.0

$ pip3 show ansible
Name: ansible
Version: 4.9.0
Summary: Radically simple IT automation
Home-page: https://ansible.com/
Author: Ansible, Inc.
Author-email: info@ansible.com
License: GPLv3+
Location: /usr/local/lib/python3.9/site-packages
Requires: ansible-core
Required-by: 
```
✅

## 2.9 Configure Local Yum repos

The Nautilus production support team and security team had a meeting last month in which they decided to use local yum repositories for maintaing packages needed for their servers. For now they have decided to configure a local yum repo on Nautilus Backup Server. This is one of the pending items from last month, so please configure a local yum repository on Nautilus Backup Server as per details given below.
1. We have some packages already present at location /packages/downloaded_rpms/ on Nautilus Backup Server.
2. Create a yum repo named epel_local and make sure to set Repository ID to epel_local. Configure it to use package's location /packages/downloaded_rpms/.
3. Install package vim-enhanced from this newly created repo.

---

```bash
sshpass -p 'H@wk3y3' ssh -o StrictHostKeyChecking=no clint@172.16.238.16

sudo vi /etc/yum.repos.d/epel_local.repo

# create /etc/yum.repos.d/epel_local.repo
$ cat /etc/yum.repos.d/epel_local.repo
[epel_local]
name=Local EPEL Repository
baseurl=file:///packages/downloaded_rpms/
enabled=1
gpgcheck=0

$ sudo yum clean all
33 files removed

$ sudo yum repolist
repo id                                                   repo name
epel_local                                                Local EPEL Repository

$ sudo yum install vim-enhanced

$ vim --version
VIM - Vi IMproved 8.0 (2016 Sep 12, compiled Jun 27 2022 13:47:02)
```
✅


## 2.10 Linux Services

As per details shared by the development team, the new application release has some dependencies on the back end. There are some packages/services that need to be installed on all app servers under `Stratos Datacenter`. As per requirements please perform the following steps:
1. Install `cups` package on all the application servers.
2. Once installed, make sure it is enabled to start during boot.

---

```bash
$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10

$ sudo yum install -y cups
$ sudo systemctl enable cups
$ sudo systemctl start cups
$ sudo systemctl status cups
● cups.service - CUPS Scheduler
   Loaded: loaded (/usr/lib/systemd/system/cups.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2024-05-23 09:12:34 UTC; 4s ago
```

Repeat the same steps for App Server 2 and 3.
✅

## 2.11 Linux Configure sudo

We have some users on all app servers in `Stratos Datacenter`. Some of them have been assigned some new roles and responsibilities, therefore their users need to be upgraded with sudo access so that they can perform admin level tasks.
1. Provide sudo access to user `mariyam` on all app servers.
2. Make sure you have set up password-less sudo for the user.

---

```bash
$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10

$ sudo su -

$ echo "mariyam ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers

$ su - mariyam
$ sudo whoami
```
Repeat the same steps for App Server 2 and 3.
✅

## 2.12 DNS Troubleshooting

The system admins team of xFusionCorp Industries has noticed intermittent issues with DNS resolution in several apps. `App Server 3` in `Stratos Datacenter` is having some DNS resolution issues, so we want to add some additional DNS nameservers on this server.

As a temporary fix we have decided to go with Google public DNS (ipv4). Please make appropriate changes on this server.

---

```bash
$ sshpass -p 'BigGr33n' ssh -o StrictHostKeyChecking=no banner@172.16.238.12

$ sudo vi /etc/resolv.conf 

# add following lines
nameserver 8.8.8.8
nameserver 8.8.4.4

$ cat /etc/resolv.conf
nameserver 8.8.8.8
nameserver 8.8.4.4
search stratos.xfusioncorp.com
nameserver 127.0.0.11
options ndots:0

$ ping google.com -c 4
PING google.com (142.251.171.100) 56(84) bytes of data.
64 bytes from ny-in-f100.1e100.net (142.251.171.100): icmp_seq=1 ttl=113 time=0.842 ms
64 bytes from ny-in-f100.1e100.net (142.251.171.100): icmp_seq=2 ttl=113 time=0.336 ms
64 bytes from ny-in-f100.1e100.net (142.251.171.100): icmp_seq=3 ttl=113 time=0.342 ms
64 bytes from ny-in-f100.1e100.net (142.251.171.100): icmp_seq=4 ttl=113 time=0.379 ms

--- google.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3025ms
rtt min/avg/max/mdev = 0.336/0.474/0.842/0.214 ms
```
✅

## 2.13 Linux Firewalld Setup

To secure our `Nautilus` infrastructure in `Stratos Datacenter` we have decided to install and configure `firewalld` on all app servers. We have Apache and Nginx services running on these apps. Nginx is running as a reverse proxy server for Apache. We might have more robust firewall settings in the future, but for now we have decided to go with the given requirements listed below:
1. Allow all incoming connections on Nginx port, i.e `80`.
2. Block all incoming connections on Apache port, i.e `8080`.
3. All rules must be permanent.
4. Zone should be public.
5. If Apache or Nginx services aren't running already, please make sure to start them.

---

```bash
$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10

$ sudo su -

$ yum install firewalld -y
$ systemctl enable firewalld
$ systemctl start firewalld
$ systemctl status firewalld

$ systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
   Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; vendor preset: disabled)
   Active: active (running) since Sun 2024-05-26 11:36:51 UTC; 19min ago

$ systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
   Active: active (running) since Sun 2024-05-26 11:36:51 UTC; 18min ago

$ sudo firewall-cmd --zone=public --list-all
[sudo] password for tony: 
public
  target: default
  icmp-block-inversion: no
  interfaces: 
  sources: 
  services: cockpit dhcpv6-client ssh
  ports: 
  protocols: 
  forward: no
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules: 
$ sudo firewall-cmd --zone=public --permanent --add-port=80/tcp
success
$ sudo firewall-cmd --zone=public --permanent --add-rich-rule='rule family="ipv4" port port="8080" protocol="tcp" reject'
success
$ sudo firewall-cmd --reload
success
$ sudo firewall-cmd --zone=public --list-all
public
  target: default
  icmp-block-inversion: no
  interfaces: 
  sources: 
  services: cockpit dhcpv6-client ssh
  ports: 80/tcp
  protocols: 
  forward: no
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules: 
        rule family="ipv4" port port="8080" protocol="tcp" reject
```
✅

## 2.14 Linux Postfix Mail Server

`xFusionCorp Industries` has planned to set up a common email server in `Stork DC`. After several meetings and recommendations they have decided to use `postfix` as their `mail transfer agent` and `dovecot` as an `IMAP/POP3 server`. We would like you to perform the following steps:
1. Install and configure `postfix` on `Stork DC` mail server.
2. Create an email account `mariyam@stratos.xfusioncorp.com` identified by `Rc5C9EyvbU`.
3. Set its mail directory to `/home/mariyam/Maildir`.
4. Install and configure `dovecot` on the same server.

---

```bash
$ sshpass -p 'Gr00T123' ssh -o StrictHostKeyChecking=no groot@172.16.238.17
$ sudo su -

$ yum install postfix -y

$ vi /etc/postfix/main.cf
myhostname = stmail01.stratos.xfusioncorp.com	
mydomain = stratos.xfusioncorp.com	
myorigin = $mydomain
inet_interfaces = all
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
mynetworks = 172.16.238.0/24, 127.0.0.0/8
home_mailbox = Maildir/
# inet_interfaces = localhost

$ systemctl start postfix
$ systemctl status postfix
$ useradd mariyam
$ passwd mariyam
$ cat /etc/passwd |grep mariyam
mariyam:x:1002:1002::/home/mariyam:/bin/bash

$ yum install dovecot -y

$ vi /etc/dovecot/dovecot.conf
protocols = imap pop3 

$ vi /etc/dovecot/conf.d/10-mail.conf
mail_location = maildir:~/Maildir

$ vi /etc/dovecot/conf.d/10-auth.conf
disable_plaintext_auth = yes
auth_mechanisms = plain login

$ vi /etc/dovecot/conf.d/10-master.conf
service auth {
...
  unix_listener auth-userdb {
    mode = 0660
    user = postfix
    group = postfix
  }
  ...
}

$ systemctl start dovecot
$ systemctl status dovecot
```
✅

## 2.15 Linux Postfix Troubleshooting

Some users of the monitoring app have reported issues with `xFusionCorp Industries` mail server. They have a mail server in `Stork DC` where they are using `postfix` mail transfer agent. `Postfix` service seems to fail. Try to identify the root cause and fix it.

---

```bash
$ sshpass -p 'Gr00T123' ssh -o StrictHostKeyChecking=no groot@172.16.238.17

$ sudo systemctl status postfix
● postfix.service - Postfix Mail Transport Agent
   Loaded: loaded (/usr/lib/systemd/system/postfix.service; disabled; vendor preset: disabled)
   Active: inactive (dead)
May 30 07:20:32 stmail01.stratos.xfusioncorp.com systemd[1]: postfix.service: Collecting.

$ vi /etc/postfix/main.cf
inet_interfaces = localhost #comment this line
#inet_interfaces = localhost

$ sudo systemctl start postfix

$ sudo systemctl status postfix
● postfix.service - Postfix Mail Transport Agent
   Loaded: loaded (/usr/lib/systemd/system/postfix.service; disabled; vendor preset: disabled)
   Active: active (running) since Thu 2024-05-30 07:30:58 UTC; 26s ago

thor@jump_host ~$ telnet stmail01 25
Trying 172.16.238.17...
Connected to stmail01.
Escape character is '^]'.
220 stmail01.stratos.xfusioncorp.com ESMTP Postfix
quit
221 2.0.0 Bye
Connection closed by foreign host.
```
✅

## 2.16 Install and Configure Ha Proxy LBR

There is a static website running in `Stratos Datacenter`. They have already configured the app servers and code is already deployed there. To make it work properly, they need to configure `LBR` server. There are number of options for that, but team has decided to go with `HAproxy`. FYI, apache is running on port `6000` on all app servers. Complete this task as per below details.
1. Install and configure `HAproxy` on `LBR` server using `yum` only and make sure all app servers are added to `HAproxy` load balancer. `HAproxy` must serve on default `http` port (`Note:` Please do not remove `stats socket /var/lib/haproxy/stats` entry from haproxy default config.).
2. Once done, you can access the website using `StaticApp` button on the top bar.

---

```bash
$ sshpass -p 'Mischi3f' ssh -o StrictHostKeyChecking=no loki@172.16.238.14

$ sudo yum install haproxy -y

$ sudo vi /etc/haproxy/haproxy.cfg

frontend main
    bind *:5000 

frontend main
    bind *:80
...
backend app
    balance     roundrobin
    server  app1 127.0.0.1:5001 check
    server  app2 127.0.0.1:5002 check
    server  app3 127.0.0.1:5003 check
    server  app4 127.0.0.1:5004 check

backend app
    balance     roundrobin
    server  stapp01 172.16.238.10:6000 check
    server  stapp02 172.16.238.11:6000 check
    server  stapp03 172.16.238.12:6000 check

$ sudo vi /etc/haproxy/haproxy.cfg

$ sudo systemctl start haproxy

$ sudo systemctl enable haproxy
Created symlink /etc/systemd/system/multi-user.target.wants/haproxy.service → /usr/lib/systemd/system/haproxy.service.

$ sudo systemctl status haproxy
● haproxy.service - HAProxy Load Balancer
   Loaded: loaded (/usr/lib/systemd/system/haproxy.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2024-05-31 16:40:09 UTC; 9s ago


# return to the jumphost and verify:
thor@jump_host ~$ curl http://172.16.238.14:80
Welcome to xFusionCorp Industries!
$ curl http://172.16.238.10:6000
Welcome to xFusionCorp Industries!
$ curl http://172.16.238.11:6000
Welcome to xFusionCorp Industries!
$ curl http://172.16.238.12:6000
Welcome to xFusionCorp Industries!
```
✅

## 2.17 Haproxy LBR Troubleshooting

`xFusionCorp Industries` has an application running on `Nautlitus` infrastructure in `Stratos Datacenter`. The monitoring tool recognised that there is an issue with the `haproxy` service on `LBR` server. That needs to fixed to make the application work properly.

Troubleshoot and fix the issue, and make sure `haproxy` service is running on `Nautilus LBR` server. Once fixed, make sure you are able to access the website using `StaticApp` button on the top bar.

---

```bash
$ sshpass -p 'Mischi3f' ssh -o StrictHostKeyChecking=no loki@172.16.238.14
$ sudo su -

$ systemctl status haproxy
● haproxy.service - HAProxy Load Balancer
   Loaded: loaded (/usr/lib/systemd/system/haproxy.service; disabled; vendor preset: disabled)
   Active: inactive (dead)

Jun 01 07:35:00 stlb01.stratos.xfusioncorp.com systemd[1]: Collecting haproxy.service

$ systemctl restart haproxy
$ systemctl status haproxy
● haproxy.service - HAProxy Load Balancer
   Loaded: loaded (/usr/lib/systemd/system/haproxy.service; disabled; vendor preset: disabled)
   Active: failed (Result: exit-code) since Sat 2024-06-01 07:43:53 UTC; 2s ago
  Process: 382 ExecStart=/usr/sbin/haproxy-systemd-wrapper -f /etc/haproxy/haproxy.cfg -p /run/haproxy.pid $OPTIONS (code=exited, status=1/FAILURE)
 Main PID: 382 (code=exited, status=1/FAILURE)

Jun 01 07:43:53 stlb01.stratos.xfusioncorp.com haproxy-systemd-wrapper[382]: [ALERT] 152/074353 (383) : parsing [/etc/hap...s)
Jun 01 07:43:53 stlb01.stratos.xfusioncorp.com haproxy-systemd-wrapper[382]: [ALERT] 152/074353 (383) : Error(s) found in...fg
Jun 01 07:43:53 stlb01.stratos.xfusioncorp.com haproxy-systemd-wrapper[382]: [ALERT] 152/074353 (383) : Fatal errors foun...n.
Jun 01 07:43:53 stlb01.stratos.xfusioncorp.com haproxy-systemd-wrapper[382]: haproxy-systemd-wrapper: exit, haproxy RC=1
Jun 01 07:43:53 stlb01.stratos.xfusioncorp.com systemd[1]: Child 382 belongs to haproxy.service
Jun 01 07:43:53 stlb01.stratos.xfusioncorp.com systemd[1]: haproxy.service: main process exited, code=exited, status=1/FAILURE
Jun 01 07:43:53 stlb01.stratos.xfusioncorp.com systemd[1]: haproxy.service changed running -> failed
Jun 01 07:43:53 stlb01.stratos.xfusioncorp.com systemd[1]: Unit haproxy.service entered failed state.
Jun 01 07:43:53 stlb01.stratos.xfusioncorp.com systemd[1]: haproxy.service failed.
Jun 01 07:43:53 stlb01.stratos.xfusioncorp.com systemd[1]: haproxy.service: cgroup is empty
Hint: Some lines were ellipsized, use -l to show in full.

$ haproxy -c -f /etc/haproxy/haproxy.cfg
[ALERT] 152/074545 (399) : parsing [/etc/haproxy/haproxy.cfg:7] : cannot find user id for 'haprox' (0:Success)
[ALERT] 152/074545 (399) : Error(s) found in configuration file : /etc/haproxy/haproxy.cfg
[ALERT] 152/074545 (399) : Fatal errors found in configuration.

# fix typo
$ sudo vi /etc/haproxy/haproxy.cfg
      7     user        haprox
      7     user        haproxy

$ haproxy -c -f /etc/haproxy/haproxy.cfg
Configuration file is valid

$ systemctl restart haproxy
$ systemctl status haproxy
● haproxy.service - HAProxy Load Balancer
   Loaded: loaded (/usr/lib/systemd/system/haproxy.service; disabled; vendor preset: disabled)
   Active: active (running) since Sat 2024-06-01 07:48:51 UTC; 1s ago
```
✅

## 2.18 Maria DB Troubleshooting

There is a critical issue going on with the `Nautilus` application in `Stratos DC`. The production support team identified that the application is unable to connect to the database. After digging into the issue, the team found that mariadb service is down on the database server.

Look into the issue and fix the same.

---

```bash
$ sshpass -p 'Sp!dy' ssh -o StrictHostKeyChecking=no  peter@172.16.239.10
$ sudo su 

$ systemctl status mariadb
● mariadb.service - MariaDB 10.3 database server
   Loaded: loaded (/usr/lib/systemd/system/mariadb.service; enabled; vendor preset: disabled)
   Active: inactive (dead) since Sun 2024-06-02 08:46:23 UTC; 43min ago
     Docs: man:mysqld(8)
           https://mariadb.com/kb/en/library/systemd/
  Process: 774 ExecStartPost=/usr/libexec/mysql-check-upgrade (code=exited, status=0/SUCCESS)
  Process: 731 ExecStart=/usr/libexec/mysqld --basedir=/usr $MYSQLD_OPTS $_WSREP_NEW_CLUSTER (code=exited, status=0/SUCCESS)
  Process: 569 ExecStartPre=/usr/libexec/mysql-prepare-db-dir mariadb.service (code=exited, status=0/SUCCESS)
  Process: 488 ExecStartPre=/usr/libexec/mysql-check-socket (code=exited, status=0/SUCCESS)
 Main PID: 731 (code=exited, status=0/SUCCESS)
   Status: "MariaDB server is down"

Jun 02 08:46:22 stdb01.stratos.xfusioncorp.com systemd[1]: mariadb.service: Got notification message from PID 731 (STATUS=ensuring dirty buffer pool are written to log, EXTEND_TIMEOUT_USEC=30000000)
Jun 02 08:46:22 stdb01.stratos.xfusioncorp.com systemd[1]: mariadb.service: Got notification message from PID 731 (STATUS=Free innodb buffer pool, EXTEND_TIMEOUT_USEC=30000000)
Jun 02 08:46:23 stdb01.stratos.xfusioncorp.com systemd[1]: mariadb.service: Got notification message from PID 731 (STATUS=MariaDB server is down)
Jun 02 08:46:23 stdb01.stratos.xfusioncorp.com systemd[1]: mariadb.service: Got notification message from PID 731 (STATUS=MariaDB server is down)
Jun 02 08:46:23 stdb01.stratos.xfusioncorp.com systemd[1]: mariadb.service: Child 731 belongs to mariadb.service.
Jun 02 08:46:23 stdb01.stratos.xfusioncorp.com systemd[1]: mariadb.service: Main process exited, code=exited, status=0/SUCCESS
Jun 02 08:46:23 stdb01.stratos.xfusioncorp.com systemd[1]: mariadb.service: Succeeded.
Jun 02 08:46:23 stdb01.stratos.xfusioncorp.com systemd[1]: mariadb.service: Changed stop-sigterm -> dead
Jun 02 08:46:23 stdb01.stratos.xfusioncorp.com systemd[1]: mariadb.service: Job mariadb.service/stop finished, result=done
Jun 02 08:46:23 stdb01.stratos.xfusioncorp.com systemd[1]: Stopped MariaDB 10.3 database server.

$ ls -l /var/lib/ | grep mysql
drwxr-xr-x 1 root    mysql   4096 Jun  2 08:46 mysql

$ sudo chown -R mysql:mysql /var/lib/mysql

$ sudo chown -R mysql:mysql /var/lib/mysql
$ ls -l /var/lib/ | grep mysql
drwxr-xr-x 1 mysql   mysql   4096 Jun  2 08:46 mysql

$ sudo systemctl restart mariadb
$ systemctl status mariadb
● mariadb.service - MariaDB 10.3 database server
   Loaded: loaded (/usr/lib/systemd/system/mariadb.service; enabled; vendor preset: disabled)
   Active: active (running) since Sun 2024-06-02 09:40:53 UTC; 6s ago
```
✅

## 2.19 Linux Bash Scripts

The production support team of `xFusionCorp Industries` is working on developing some bash scripts to automate different day to day tasks. One is to create a bash script for taking websites backup. They have a static website running on `App Server 3` in `Stratos Datacenter`, and they need to create a bash script named `media_backup.sh` which should accomplish the following tasks. (Also remember to place the script under `/scripts` directory on `App Server 3`).
- Create a zip archive named `xfusioncorp_media.zip` of `/var/www/html/media` directory.
- Save the archive in `/backup/` on `App Server 3`. This is a temporary storage, as backups from this location will be clean on weekly basis. Therefore, we also need to save this backup archive on `Nautilus Backup Server`.
- Copy the created archive to `Nautilus Backup Server` server in `/backup/` location.
- Please make sure script won't ask for password while copying the archive file. Additionally, the respective server user (for example, `tony` in case of `App Server 1`) must be able to run it.

---

```bash
$ sshpass -p 'BigGr33n' ssh -o StrictHostKeyChecking=no banner@172.16.238.12

$ ssh-keygen -t rsa -b 4096

$ ssh-copy-id clint@172.16.238.16
Are you sure you want to continue connecting (yes/no)? yes
Now try logging into the machine, with:   "ssh 'clint@172.16.238.16'"

[banner@stapp03 ~]$ ssh 'clint@172.16.238.16'
[clint@stbkp01 ~]$ exit

$ cat /scripts/media_backup.sh
#!/bin/bash
echo "Creating zip archive of /var/www/html/media"
zip -r /backup/xfusioncorp_media.zip /var/www/html/media
echo "Copying zip archive to Backup server"
scp /backup/xfusioncorp_media.zip clint@172.16.238.16:/backup/

$ chmod u+x /scripts/media_backup.sh

$ ./scripts/media_backup.sh
Creating zip archive of /var/www/html/media
  adding: var/www/html/media/ (stored 0%)
  adding: var/www/html/media/index.html (stored 0%)
  adding: var/www/html/media/.gitkeep (stored 0%)
Copying zip archive to Backup server
xfusioncorp_media.zip                                                                        100%  595     1.6MB/s   00:00    

$ ssh 'clint@172.16.238.16'
[clint@stbkp01 ~]$ ls -l /backup/
[clint@stbkp01 backup]$ 
total 4
-rw-r--r-- 1 clint clint 595 May 10 13:05 xfusioncorp_media.zip
$ unzip /backup/xfusioncorp_media.zip 
Archive:  xfusioncorp_media.zip
   creating: var/www/html/media/
 extracting: var/www/html/media/index.html  
 extracting: var/www/html/media/.gitkeep 
```

✅

## 2.20 Add Response Headers in Apache

We are working on hardening Apache web server on all app servers. As a part of this process we want to add some of the Apache response headers for security purpose. We are testing the settings one by one on all app servers. As per details mentioned below enable these headers for Apache:
1. Install `httpd` package on `App Server 3` using yum and configure it to run on `6002` port, make sure to start its service.
2. Create an `index.html` file under Apache's default document root i.e `/var/www/html` and add below given content in it.  
`Welcome to the xFusionCorp Industries!`
3. Configure Apache to enable below mentioned headers:
`X-XSS-Protection` header with value `1; mode=block`  
`X-Frame-Options` header with value `SAMEORIGIN`  
`X-Content-Type-Options` header with value `nosniff`

`Note:` You can test using curl on the given app server as LBR URL will not work for this task.

---

```bash
$ sshpass -p 'BigGr33n' ssh -o StrictHostKeyChecking=no banner@172.16.238.12

$ sudo yum install -y httpd

$ sudo vi /etc/httpd/conf/httpd.conf 
Listen 80
Listen 6200
...
<IfModule mod_headers.c>
    Header always set X-XSS-Protection "1; mode=block"
    Header always set X-Frame-Options "SAMEORIGIN"
    Header always set X-Content-Type-Options "nosniff"
</IfModule>

$ sudo systemctl start httpd
$ sudo systemctl enable httpd

$ echo "Welcome to the xFusionCorp Industries!" | sudo tee /var/www/html/index.html

$ systemctl restart httpd
$ systemctl status httpd
● httpd.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; preset: disabled)
     Active: active (running) since Sun 2025-05-18 17:03:08 UTC; 2min 16s ago

$ curl http://localhost:6200/
Welcome to the xFusionCorp Industries!
$ curl -I http://localhost:6200/
HTTP/1.1 200 OK
Date: Sun, 18 May 2025 17:03:14 GMT
Server: Apache/2.4.62 (CentOS Stream)
X-XSS-Protection: 1; mode=block
X-Frame-Options: SAMEORIGIN
X-Content-Type-Options: nosniff
Last-Modified: Sun, 18 May 2025 16:48:50 GMT
ETag: "27-6356bca2ec737"
Accept-Ranges: bytes
Content-Length: 39
Content-Type: text/html; charset=UTF-8
```
✅

## 2.21 Apache Troubleshooting

`xFusionCorp Industries` uses some monitoring tools to check the status of every service, application, etc running on the systems. Recently, the monitoring system identified that Apache service is not running on some of the `Nautilus Application Servers` in `Stratos Datacenter`.

1. Identify the faulty `Nautilus Application Server` and fix the issue. Also, make sure Apache service is up and running on all `Nautilus Application Servers`. Do not try to stop any kind of firewall that is already running.
2. Apache is running on `8085` port on all `Nautilus Application Servers` and its document root must be `/var/www/html` on all app servers.
3. Finally you can test from `jump host` using curl command to access Apache on all app servers and it should be reachable and you should get some static page. E.g. `curl http://172.16.238.10:8085/`.

---

```bash
$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10

$ sudo systemctl start httpd
$ systemctl status httpd.service
× httpd.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; preset: disabled)
     Active: failed (Result: exit-code) since Sat 2025-05-24 21:41:39 UTC; 5s ago
       Docs: man:httpd.service(8)
    Process: 2512 ExecStart=/usr/sbin/httpd $OPTIONS -DFOREGROUND (code=exited, status=1/FAILURE)
   Main PID: 2512 (code=exited, status=1/FAILURE)
     Status: "Reading configuration..."

May 24 21:41:39 stapp01.stratos.xfusioncorp.com httpd[2512]: Invalid command 'Listen 8085', perhaps misspelled or defined by a module not included in the server configuration

$ sudo vi /etc/httpd/conf/httpd.conf
`"Listen 8085"` -> `Listen 8085`
`DocumentRoot /var/www/html;` -> `DocumentRoot "/var/www/html"`

$ sudo systemctl restart httpd
$ systemctl status httpd.service
● httpd.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; preset: disabled)
     Active: active (running) since Sat 2025-05-24 21:47:52 UTC; 3s ago

$ sshpass -p 'Am3ric@' ssh -o StrictHostKeyChecking=no steve@172.16.238.11

$ sudo systemctl start httpd
$ systemctl status httpd.service
● httpd.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; preset: disabled)
     Active: active (running) since Sat 2025-05-24 21:40:17 UTC; 3s ago

$ sshpass -p 'BigGr33n' ssh -o StrictHostKeyChecking=no banner@172.16.238.12

$ sudo systemctl start httpd
$ systemctl status httpd.service
httpd.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; preset: disabled)
     Active: failed (Result: exit-code) since Sat 2025-05-24 21:35:04 UTC; 11s ago
       Docs: man:httpd.service(8)
    Process: 2400 ExecStart=/usr/sbin/httpd $OPTIONS -DFOREGROUND (code=exited, status=1/FAILURE)
   Main PID: 2400 (code=exited, status=1/FAILURE)

May 24 21:35:04 stapp03.stratos.xfusioncorp.com systemd[2400]: httpd.service: Executing: /usr/sbin/httpd -DFOREGROUND
May 24 21:35:04 stapp03.stratos.xfusioncorp.com httpd[2400]: httpd: Syntax error on line 34 of /etc/httpd/conf/httpd.conf: Ser>

$ sudo vi /etc/httpd/conf/httpd.conf
`ServerRoot "/etc/httpd;"` -> `ServerRoot "/etc/httpd"`

$ sudo systemctl restart httpd
$ systemctl status httpd.service
● httpd.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; preset: disabled)
     Active: active (running) since Sat 2025-05-24 21:37:11 UTC; 2s ago

thor@jumphost ~$ curl http://172.16.238.10:8085/
Welcome to xFusionCorp Industries!
thor@jumphost ~$ curl http://172.16.238.11:8085/
Welcome to xFusionCorp Industries!
thor@jumphost ~$ curl http://172.16.238.12:8085/
Welcome to xFusionCorp Industries!
```
✅

## 2.22 Linux GPG Encryption

We have confidential data that needs to be transferred to a remote location, so we need to encrypt that data.We also need to decrypt data we received from a remote location in order to understand its content.

On `storage server` in `Stratos Datacenter` we have private and public keys stored at `/home/*_key.asc`. Use these keys to perform the following actions.
- Encrypt `/home/encrypt_me.txt` to `/home/encrypted_me.asc`.
- Decrypt `/home/decrypt_me.asc` to `/home/decrypted_me.txt`. (Passphrase for decryption and encryption is `kodekloud`).
- The user ID you can use is `kodekloud@kodekloud.com`.

---

```bash
$ sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@172.16.238.15

$ sudo su -

$ cd /home/
$ ls -l
total 32
drwx------ 1 ansible ansible 4096 Jun  5  2024 ansible
-rw-r--r-- 1 root    root     155 May 25 15:39 decrypt_me.asc
-rw-r--r-- 1 root    root      99 May 25 17:27 encrypt_me.txt
drwx------ 1 natasha natasha 4096 Jun  6  2024 natasha
-rw-r--r-- 1 root    root    3589 May 25 17:27 private_key.asc
-rw-r--r-- 1 root    root    1722 May 25 17:27 public_key.asc

$ gpg --import public_key.asc
gpg: directory '/root/.gnupg' created
gpg: keybox '/root/.gnupg/pubring.kbx' created
gpg: /root/.gnupg/trustdb.gpg: trustdb created
gpg: key 8F17F26ECCE3AF51: public key "kodekloud <kodekloud@kodekloud.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1

$ gpg --import private_key.asc
gpg: key 8F17F26ECCE3AF51: "kodekloud <kodekloud@kodekloud.com>" not changed
gpg: key 8F17F26ECCE3AF51: secret key imported
gpg: Total number processed: 1
gpg:              unchanged: 1
gpg:       secret keys read: 1
gpg:   secret keys imported: 1

$ gpg --list-keys
/root/.gnupg/pubring.kbx
------------------------
pub   rsa2048 2020-01-19 [SC]
      FEA85011C456B5E9AE5A516F8F17F26ECCE3AF51
uid           [ unknown] kodekloud <kodekloud@kodekloud.com>
sub   rsa2048 2020-01-19 [E]

$ gpg --list-secret-keys
/root/.gnupg/pubring.kbx
------------------------
sec   rsa2048 2020-01-19 [SC]
      FEA85011C456B5E9AE5A516F8F17F26ECCE3AF51
uid           [ unknown] kodekloud <kodekloud@kodekloud.com>
ssb   rsa2048 2020-01-19 [E]

$ gpg --encrypt -r kodekloud@kodekloud.com --armor < encrypt_me.txt  -o encrypted_me.asc

$ gpg --decrypt decrypt_me.asc > decrypted_me.txt

$ ls -l *ed_*
-rw-r--r-- 1 root root  80 May 25 17:33 decrypted_me.txt
-rw-r--r-- 1 root root 634 May 25 17:32 encrypted_me.asc

$ cat decrypted_me.txt 
Welcome to xFusionCorp Industries. This is KodeKloud System Administration Lab
```
✅

## 2.23 Linux Log Rotate

The `Nautilus` DevOps team is ready to launch a new application, which they will deploy on app servers in `Stratos Datacenter`. They are expecting significant traffic/usage of `squid` on app servers after that. This will generate massive logs, creating huge log files. To utilise the storage efficiently, they need to compress the log files and need to rotate old logs. Check the requirements shared below:
- In all app servers install `squid` package.
- Using `logrotate` configure `squid` logs rotation to monthly and keep only 3 rotated logs.
(If by default log rotation is set, then please update configuration as needed)

---

```bash
$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10

$ sudo su - 

$ yum install squid -y

$ systemctl start squid
$ systemctl status squid
● squid.service - Squid caching proxy
     Loaded: loaded (/usr/lib/systemd/system/squid.service; disabled; preset: disabled)
     Active: active (running) since Mon 2025-05-26 16:28:24 UTC; 1s ago

$ cat /etc/logrotate.d/squid 
/var/log/squid/*.log {
    weekly
    rotate 5
    compress
    delaycompress
    notifempty
    missingok
    nocreate
    sharedscripts
    postrotate
      # Asks squid to reopen its logs. (logfile_rotate 0 is set in squid.conf)
      # errors redirected to make it silent if squid is not running
      /usr/sbin/squid -k rotate 2>/dev/null
    endscript
}

$ cat /etc/logrotate.d/squid 
/var/log/squid/*.log {
    monthly 
    rotate 3
    compress
    delaycompress
    notifempty
    missingok
    nocreate
    sharedscripts
    postrotate
      # Asks squid to reopen its logs. (logfile_rotate 0 is set in squid.conf)
      # errors redirected to make it silent if squid is not running
      /usr/sbin/squid -k rotate 2>/dev/null
    endscript
}

$ logrotate --debug /etc/logrotate.d/squid
WARNING: logrotate in debug mode does nothing except printing debug messages!  Consider using verbose mode (-v) instead if this is not what you want.

reading config file /etc/logrotate.d/squid
Reading state from file: /var/lib/logrotate/logrotate.status
error: error opening state file /var/lib/logrotate/logrotate.status: No such file or directory
Allocating hash table for state file, size 64 entries

Handling 1 logs

rotating pattern: /var/log/squid/*.log  monthly (3 rotations)
empty log files are not rotated, old logs are removed
considering log /var/log/squid/access.log
Creating new state
  Now: 2025-05-26 16:37
  Last rotated at 2025-05-26 16:00
  log does not need rotating (log has already been rotated)
considering log /var/log/squid/cache.log
Creating new state
  Now: 2025-05-26 16:37
  Last rotated at 2025-05-26 16:00
  log does not need rotating (log has already been rotated)
not running postrotate script, since no logs were rotated
```

Repeat the same steps for App Server 2 and 3.

✅

## 2.24 Application Security

We have a backup management application UI hosted on `Nautilus's` backup server in `Stratos DC`. That backup management application code is deployed under Apache on the backup server itself, and Nginx is running as a reverse proxy on the same server. Apache and Nginx ports are `8088` and `8093`, respectively. We have iptables firewall installed on this server. Make the appropriate changes to fulfill the requirements mentioned below:

We want to open all incoming connections to Nginx's port and block all incoming connections to Apache's port. Also make sure rules are permanent.

---

```bash
$ sshpass -p 'H@wk3y3' ssh -o StrictHostKeyChecking=no clint@172.16.238.16

$ sudo su -

$ iptables -A INPUT -p tcp --dport 8093 -j ACCEPT

$ iptables -A INPUT -p tcp --dport 8088 -j DROP

$ iptables -L -v -n --line-numbers
Chain INPUT (policy ACCEPT 0 packets, 0 bytes)
num   pkts bytes target     prot opt in     out     source               destination         
...
8        0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:8093
9        0     0 DROP       tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:8088
...

$ service iptables save
iptables: Saving firewall rules to /etc/sysconfig/iptables:[  OK  ]

$ systemctl enable iptables
$ systemctl restart iptables

$ iptables -L -v -n --line-numbers # to verify
```
✅
![linux2](https://github.com/user-attachments/assets/67893065-0642-41a6-9361-cee7fe90e127)
