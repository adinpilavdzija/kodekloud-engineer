# Linux Level 1 (Tasks 1.1-1.19) 

## 1.1 Create a user

For security reasons the `xFusionCorp Industries` security team has decided to use custom Apache users for each web application hosted, rather than its default user. This will be the Apache user, so it shouldn't use the default home directory. Create the user as per requirements given below:

1. Create a user named `rose` on the `App server 1` in Stratos Datacenter.  
2. Set its UID to `1992` and home directory to `/var/www/rose`.

---

Switch to root, create user as specified and verify:
```bash
$ sudo su -
$ useradd -u 1992 -d /var/www/rose rose
$ getent passwd | grep rose
rose:x:1992:1992::/var/www/rose:/bin/bash
```
✅

## 1.2 Create a group

There are specific access levels for users defined by the `xFusionCorp Industries` system admin team. Rather than providing access levels to every individual user, the team has decided to create groups with required access levels and add users to that groups as needed. See the following requirements:

1. Create a group named `nautilus_developers` in all App servers in `Stratos Datacenter`.
2. Add the user `sonya` to `nautilus_developers` group in all App servers. (create the user if doesn't exist).

---

Switch to root, create a group as specified and verify:
```bash
$ sudo su -
$ groupadd nautilus_developers
$ getent group nautilus_developers
nautilus_developers:x:1002:
```

Create user in newly created group and verify:
```bash
$ useradd -G nautilus_developers sonya
$ getent group nautilus_developers
nautilus_developers:x:1002:sonya
```

Repeat the process in all App servers.
✅

## 1.3 Create a Linux User with non-interactive shell

The System admin team of `xFusionCorp Industries` has installed a backup agent tool on all app servers. As per the tool's requirements they need to create a user with a non-interactive shell.

Therefore, create a user named `ravi` with a non-interactive shell on the `App Server 2`.

---

Switch to root, create a user with a non-interactive shell and verify:
```bash
$ sudo su -
$ useradd -s /sbin/nologin ravi
$ getent passwd | grep ravi
ravi:x:1002:1002::/home/ravi:/sbin/nologin
$ su ravi
This account is currently not available.
```
✅

## 1.4 Linux User Without Home

The system admins team of `xFusionCorp Industries` has set up a new tool on all app servers, as they have a requirement to create a service user account that will be used by that tool.

Create a user named `james` in `App Server 2` without a home directory.

---

Switch to root, create a user without a home directory and verify:
```bash
$ sudo su -
$ useradd -M james
$ grep james /etc/passwd
james:x:1002:1002::/home/james:/bin/bash
$ su - james
su: warning: cannot change directory to /home/james: No such file or directory
```
✅

## 1.5 Linux User Expiry

A developer named siva has been assigned `Nautilus` project temporarily as a backup resource. As a temporary resource for this project, we need a temporary user for siva. It’s a good idea to create a user with an expiration date so that the user won't be able to access servers beyond that point.

Therefore, create a user named `siva` on the `App Server 3` in `Stratos Datacenter`. Set `expiry date` to `2021-03-28`. Make sure the user is created as per standard and is in lowercase.

---

Create a user as specified and verify:
```bash
$ sudo useradd -e 2021-03-28 siva
$ chage -l siva
Last password change                                    : Dec 05, 2023
Password expires                                        : never
Password inactive                                       : never
Account expires                                         : Mar 28, 2021
Minimum number of days between password change          : 0
Maximum number of days between password change          : 99999
Number of days of warning before password expires       : 7
```
✅

## 1.6 Linux User Files

There was some users data copied on `Nautilus App Server 2` at `/home/usersdata` location by the `Nautilus` production support team in `Stratos DC`. Later they found that they mistakenly mixed up different user data there. Now they want to filter out some user data and copy it to another location. Find the details below:

On `App Server 2` find all files (not directories) owned by user `anita` inside `/home/usersdata` directory and copy them all `while keeping the folder structure` (preserve the directories path) to `/ecommerce` directory.

---

Switch to root and list all:
```bash
$ sudo su -
$ ls -la /home/usersdata
total 232
drwxr-xr-x  5 root  root  4096 Dec  6 11:07 .
drwxr-xr-x  1 root  root  4096 Dec  6 11:07 ..
-rw-r--r--  1 anita root   405 Dec  6 11:07 index.php
-rw-r--r--  1 root  root 19915 Dec  6 11:07 license.txt
-rw-r--r--  1 root  root  7389 Dec  6 11:07 readme.html
-rw-r--r--  1 anita root  7205 Dec  6 11:07 wp-activate.php
drwxr-xr-x  9 root  root  4096 Dec  6 11:07 wp-admin
-rw-r--r--  1 anita root   351 Dec  6 11:07 wp-blog-header.php
-rw-r--r--  1 anita root  2338 Dec  6 11:07 wp-comments-post.php
-rw-r--r--  1 anita root  3001 Dec  6 11:07 wp-config-sample.php
drwxr-xr-x  4 root  root  4096 Dec  6 11:07 wp-content
-rw-r--r--  1 anita root  5543 Dec  6 11:07 wp-cron.php
drwxr-xr-x 27 root  root 12288 Dec  6 11:07 wp-includes
-rw-r--r--  1 anita root  2494 Dec  6 11:07 wp-links-opml.php
-rw-r--r--  1 anita root  3985 Dec  6 11:07 wp-load.php
-rw-r--r--  1 anita root 49135 Dec  6 11:07 wp-login.php
-rw-r--r--  1 anita root  8522 Dec  6 11:07 wp-mail.php
-rw-r--r--  1 anita root 24587 Dec  6 11:07 wp-settings.php
-rw-r--r--  1 anita root 34350 Dec  6 11:07 wp-signup.php
-rw-r--r--  1 anita root  4914 Dec  6 11:07 wp-trackback.php
-rw-r--r--  1 anita root  3236 Dec  6 11:07 xmlrpc.php
```

`find` is used to search for files and directories within a specified location (`/home/usersdata/`). We specified only files (`-type f`) from user anita (`-user anita`). `-exec` allows you to execute a command on the files found. `cp` is the copy command, `--parents` preserves the directory structure, `{}` is a placeholder for the found file (it gets replaced by the path of each file found by find), `/ecommerce` is the destination directory where the files will be copied, and `\;` is used to terminate the -exec command.
```bash
find /home/usersdata/ -user anita -type f -exec cp --parents {} /ecommerce \;
```

Verify:
```bash
$ ls -la /ecommerce/
total 12
drwxrwxrwx 3 root root 4096 Dec  6 11:44 .
drwxr-xr-x 1 root root 4096 Dec  6 11:07 ..
drwxr-xr-x 3 root root 4096 Dec  6 11:44 home
$ ls -la /ecommerce/home/
total 12
drwxr-xr-x 3 root root 4096 Dec  6 11:44 .
drwxrwxrwx 3 root root 4096 Dec  6 11:44 ..
drwxr-xr-x 5 root root 4096 Dec  6 11:44 usersdata
$ ls -la /ecommerce/home/usersdata/
total 208
drwxr-xr-x  5 root root  4096 Dec  6 11:44 .
drwxr-xr-x  3 root root  4096 Dec  6 11:44 ..
-rw-r--r--  1 root root   405 Dec  6 11:44 index.php
-rw-r--r--  1 root root  7205 Dec  6 11:44 wp-activate.php
drwxr-xr-x  7 root root  4096 Dec  6 11:44 wp-admin
-rw-r--r--  1 root root   351 Dec  6 11:44 wp-blog-header.php
-rw-r--r--  1 root root  2338 Dec  6 11:44 wp-comments-post.php
-rw-r--r--  1 root root  3001 Dec  6 11:44 wp-config-sample.php
drwxr-xr-x  4 root root  4096 Dec  6 11:44 wp-content
-rw-r--r--  1 root root  5543 Dec  6 11:44 wp-cron.php
drwxr-xr-x 23 root root 16384 Dec  6 11:44 wp-includes
-rw-r--r--  1 root root  2494 Dec  6 11:44 wp-links-opml.php
-rw-r--r--  1 root root  3985 Dec  6 11:44 wp-load.php
-rw-r--r--  1 root root 49135 Dec  6 11:44 wp-login.php
-rw-r--r--  1 root root  8522 Dec  6 11:44 wp-mail.php
-rw-r--r--  1 root root 24587 Dec  6 11:44 wp-settings.php
-rw-r--r--  1 root root 34350 Dec  6 11:44 wp-signup.php
-rw-r--r--  1 root root  4914 Dec  6 11:44 wp-trackback.php
-rw-r--r--  1 root root  3236 Dec  6 11:44 xmlrpc.php
```
✅

## 1.7 Disable Root Login

After doing some security audits of servers, `xFusionCorp Industries` security team has implemented some new security policies. One of them is to disable direct root login through SSH.

Disable direct SSH root login on all app servers in `Stratos Datacenter`.

---

Edit sshd_config and change value for PermitRootLogin from yes to no, then verify:
```bash
$ sudo vi /etc/ssh/sshd_config
...
PermitRootLogin no
...
$ grep PermitRootLogin /etc/ssh/sshd_config
PermitRootLogin no
# the setting of "PermitRootLogin without-password".
```

Enable sshd, restart and verify status, then verify root login:
```bash
$ systemctl enable sshd
$ systemctl restart sshd
$ systemctl status sshd
● sshd.service - OpenSSH server daemon
   Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2023-12-07 21:50:30 UTC; 8s ago
$ ssh root@172.16.238.10
root@172.16.238.12's password: 
Permission denied, please try again.
```

Repeat the same steps on App server 2 and 3.
✅

## 1.8 Linux Archives

On `Nautilus` storage server in `Stratos DC`, there is a storage location named `/data`, which is used by different developers to keep their data (non confidential data). One of the developers named `kareem` has raised a ticket and asked for a copy of their data present in `/data/kareem` directory on storage server. `/home` is a FTP location on storage server itself where developers can download their data. Below are the instructions shared by the system admin team to accomplish this task.

a. Make a `kareem.tar.gz` compressed archive of `/data/kareem` directory and move the archive to `/home` directory on Storage Server.

---

Login to specified server and create .tar.gz file:
```bash
$ sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@172.16.238.15
$ tar -czvf kareem.tar.gz /data/kareem
tar: Removing leading `/' from member names
/data/kareem/
/data/kareem/nautilus2.txt
/data/kareem/nautilus3.txt
/data/kareem/nautilus1.txt
$ ls
kareem  kareem.tar.gz
```

Move the archive to /home directory:
```bash
$ sudo mv /data/kareem.tar.gz /home/
$ ls /home/
ansible  kareem.tar.gz  natasha
```
✅

## 1.9 Linux File Permissions

There are new requirements to automate a backup process that was performed manually by the `xFusionCorp Industries` system admins team earlier. To automate this task, the team has developed a new bash script `xfusioncorp.sh`. They have already copied the script on all required servers, however they did not make it executable on one the app server i.e `App Server 3` in `Stratos Datacenter`.

Please give executable permissions to `/tmp/xfusioncorp.sh` script on `App Server 3`. Also make sure every user can execute it.

---

Check permissions, give executable permissions and verify:
```bash
$ ls -l /tmp/xfusioncorp.sh 
---------- 1 root root 40 Dec  9 09:46 /tmp/xfusioncorp.sh
$ sudo chmod +rx /tmp/xfusioncorp.sh
$ ls -l /tmp/xfusioncorp.sh 
-r-xr-xr-x 1 root root 40 Dec  9 09:46 /tmp/xfusioncorp.sh
$ sh /tmp/xfusioncorp.sh 
Welcome To KodeKloud
```
✅

## 1.10 Linux Access Control List

The `Nautilus` security team performed an audit on all servers present in `Stratos DC`. During the audit some critical data/files were identified which were having the wrong permissions as per security standards. Once the report was shared with the production support team, they started fixing the issues one by one. It has been identified that one of the files named `/etc/hosts` on `Nautilus App 1` server has wrong permissions, so that needs to be fixed and the correct ACLs needs to be set.
1. The user owner and group owner of the file should be `root` user.
2. `Others` must have `read only` permissions on the file.
3. User `ravi` must not have any permission on the file.
4. User `eric` should have `read only` permission on the file.

---

Display the access permissions:
```bash
$ ls -l /etc/hosts
-rw-r--r-- 1 root root 374 Dec 10 08:28 /etc/hosts
$ getfacl /etc/hosts
getfacl: Removing leading '/' from absolute path names
# file: etc/hosts
# owner: root
# group: root
user::rw-
group::r--
other::r--
```

Set file access control list and verify:
```bash
$ sudo setfacl -m u:ravi:-,eric:r /etc/hosts
$ getfacl /etc/hosts
getfacl: Removing leading '/' from absolute path names
# file: etc/hosts
# owner: root
# group: root
user::rw-
user:ravi:---
user:eric:r--
group::r--
mask::r--
other::r--
```
✅

## 1.11 Linux String Substitute

The backup server in the `Stratos DC` contains several template XML files used by the Nautilus application. However, these template XML files must be populated with valid data before they can be used. One of the daily tasks of a system admin working in the xFusionCorp industries is to apply string and file manipulation commands!

Replace all occurances of the string `Text` to `Cloud` on the XML file /root/nautilus.xml located in the backup server.

---

Login to the backup server and switch to root.
```bash
$ sshpass -p 'H@wk3y3' ssh -o StrictHostKeyChecking=no clint@172.16.238.16
$ sudo su -
```

Check how many occurances of the string `Text` are in the `/root/nautilus.xml` file. 
```bash
$ grep -e Text /root/nautilus.xml | wc -l
66
```

Replace them with `sed` (Stream EDitor) command. The `-i` tells to update the file. The `s` is the substitute command of sed for find and replace. The `/` is the default delimiter. The `g/` means global replace i.e. find all occurrences of foo and replace with bar using sed.
```bash
$ sed -i 's/Text/Cloud/g' /root/nautilus.xml
```

Verify:
```bash
$ grep -e Text /root/nautilus.xml | wc -l
0
$ grep -e Cloud /root/nautilus.xml | wc -l
66
```
✅

## 1.12 Linux Remote Copy

One of the `Nautilus` developers has copied confidential data on the jump host in `Stratos DC`. That data must be copied to one of the app servers. Because developers do not have access to app servers, they asked the system admins team to accomplish the task for them.

Copy `/tmp/nautilus.txt.gpg` file from jump server to `App Server 1` at location `/home/web`.

---

Switch to root and copy /tmp/nautilus.txt.gpg from jump server to /home/web on App Server 1:
```bash
$ sudo su -
[root@jump_host ~]# scp -r /tmp/nautilus.txt.gpg tony@172.16.238.10:/home/web
nautilus.txt.gpg                                                                                  100%  105   211.7KB/s   00:00    
```

Login to App Server and verify:
```bash
[root@jump_host ~]# sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10
[tony@stapp01 ~]$ ls -l /home/web/nautilus.txt.gpg
-rw-r--r-- 1 tony tony 105 Dec 12 14:47 /home/web/nautilus.txt.gpg
```
✅

## 1.13 Cron schedule deny to users

To stick with the security compliances, the Nautilus project team has decided to apply some restrictions on crontab access so that only allowed users can create/update the cron jobs. Limit crontab access to below specified users on `App Server 3`.

Allow crontab access to `john` user and deny the same to `eric` user.

---

Switch to root. Add john to the cron.allow and eric to the cron.deny file.
```bash
$ sudo su -
$ echo "john" > /etc/cron.allow
$ echo "eric" > /etc/cron.deny
```

Verify:
```bash
$ cat /etc/cron.allow
john
$ cat /etc/cron.deny
eric
```

Switch to users john and eric to test:
```bash
$ su - john
[john@stapp03 ~]$ crontab -l
no crontab for john
[root@stapp03 ~]# su - eric
[eric@stapp03 ~]$ crontab -l
You (eric) are not allowed to use this program (crontab)
See crontab(1) for more information
```
✅

## 1.14 Linux Run Levels

New tools have been installed on the app servers in `Stratos Datacenter`. Some of these tools can only be managed from the graphical user interface. Therefore, there are some requirements for these app servers as below.

On all App servers in `Stratos Datacenter`, change the `default runlevel` so that they can boot in `GUI (graphical user interface)` by default. Please do not try to `reboot` these servers after completing this task.

---

```bash
$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10
$ sudo su -
$ systemctl list-units | grep -i target
basic.target                                      loaded active active    Basic System                                         
local-fs.target                                   loaded active active    Local File Systems                                   
multi-user.target                                 loaded active active    Multi-User System                                    
network-online.target                             loaded active active    Network is Online                                    
paths.target                                      loaded active active    Paths                                                
remote-fs.target                                  loaded active active    Remote File Systems                                  
slices.target                                     loaded active active    Slices                                               
sockets.target                                    loaded active active    Sockets                                              
sshd-keygen.target                                loaded active active    sshd-keygen.target                                   
swap.target                                       loaded active active    Swap                                                 
sysinit.target                                    loaded active active    System Initialization                                
timers.target                                     loaded active active    Timers  

$ systemctl get-default
multi-user.target

$ systemctl set-default graphical.target
Removed /etc/systemd/system/default.target.
Created symlink /etc/systemd/system/default.target → /usr/lib/systemd/system/graphical.target.

$ systemctl get-default
graphical.target

$ systemctl start graphical.target
$ systemctl status graphical.target
● graphical.target - Graphical Interface
   Loaded: loaded (/usr/lib/systemd/system/graphical.target; indirect; vendor preset: disabled)
   Active: active since Fri 2023-12-15 08:56:47 UTC; 6s ago
     Docs: man:systemd.special(7)

Dec 15 08:56:47 stapp01.stratos.xfusioncorp.com systemd[1]: graphical.target: Enqueued job graphical.target/ start as 91
Dec 15 08:56:47 stapp01.stratos.xfusioncorp.com systemd[1]: graphical.target: Job graphical.target/start fin ished, result=done
Dec 15 08:56:47 stapp01.stratos.xfusioncorp.com systemd[1]: Reached target Graphical Interface.
```

Repeat on App server 2 and App server 3.
✅

## 1.15 Linux Time Zones Setting

During the daily standup, it was pointed out that the timezone across `Nautilus Application Servers` in `Stratos Datacenter` doesn't match with that of the local datacenter's timezone, which is `America/Kentucky/Louisville`.

Correct the mismatch.

---

Login to the App server 1 and check the timedate:
```bash
$ timedatectl
               Local time: Sat 2023-12-16 11:47:01 UTC
           Universal time: Sat 2023-12-16 11:47:01 UTC
                 RTC time: n/a
                Time zone: Etc/UTC (UTC, +0000)
System clock synchronized: yes
              NTP service: n/a
          RTC in local TZ: no
```

Set new timezone and verify:
```bash
$ sudo timedatectl set-timezone America/Kentucky/Louisville
$ timedatectl
               Local time: Sat 2023-12-16 06:49:27 EST
           Universal time: Sat 2023-12-16 11:49:27 UTC
                 RTC time: n/a
                Time zone: America/Kentucky/Louisville (EST, -0500)
System clock synchronized: yes
              NTP service: n/a
          RTC in local TZ: no
```

Repeat on App server 2 and App server 3.
✅

## 1.16 Linux NTP Setup

The system admin team of `xFusionCorp Industries` has noticed an issue with some servers in `Stratos Datacenter` where some of the servers are not in sync w.r.t time. Because of this, several application functionalities have been impacted. To fix this issue the team has started using common/standard NTP servers. They are finished with most of the servers except `App Server 1`. Therefore, perform the following tasks on this server:
1. Install and configure NTP server on `App Server 1`.
2. Add NTP `server 2.north-america.pool.ntp.org` in NTP configuration on `App Server 1`.
3. Please do not try to start/restart/stop `ntp` service, as we already have a restart for this service scheduled for tonight and we don't want these changes to be applied right now.

The system admin team of `xFusionCorp Industries` has noticed an issue with some servers in Stratos Datacenter where some of the servers are not in sync w.r.t time. Because of this, several application functionalities have been impacted. To fix this issue the team has started using common/standard NTP servers. They are finished with most of the servers except `App Server 1`. Therefore, perform the following tasks on this server:
1. Install and configure NTP server on `App Server 1`.
2. Add NTP `server 1.africa.pool.ntp.org` in NTP configuration on `App Server 1`.
3. Please do not try to start/restart/stop ntp service, as we already have a restart for this service scheduled for tonight and we don't want these changes to be applied right now.

---

Check existing firewalld rules.

Install NTP and verify:
```bash
sudo yum install -y ntp

$ rpm -qa |grep ntp
ntp-4.2.6p5-29.el7.centos.2.x86_64
ntpdate-4.2.6p5-29.el7.centos.2.x86_64
```

Add the NTP server on the config file:
```bash
$ sudo vi /etc/ntp.conf 
...
#add this below
server 1.africa.pool.ntp.org 
...
```
✅

## 1.17 Linux Firewalld Rules

The `Nautilus` system admins team recently deployed a web UI application for their backup utility running on the `Nautilus backup server` in `Stratos Datacenter`. The application is running on port `3001`. They have `firewalld` installed on that server. The requirements that have come up include the following:

Open all incoming connection on `3001/tcp` port. Zone should be `public`.

---

Login to the backup server and check existing firewalld rules:
```bash
$ sshpass -p 'H@wk3y3' ssh -o StrictHostKeyChecking=no clint@172.16.238.16
$ sudo firewall-cmd --zone=public --list-all
public
  target: default
  icmp-block-inversion: no
  interfaces: 
  sources: 
  services: dhcpv6-client ssh
  ports: 
  protocols: 
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules: 
```

Add the port and reload, then verify:
```bash
$ sudo firewall-cmd --zone=public --permanent --add-port=3001/tcp
success
$ sudo firewall-cmd --reload
success
$ sudo firewall-cmd --zone=public --list-all
public
  target: default
  icmp-block-inversion: no
  interfaces: 
  sources: 
  services: dhcpv6-client ssh
  ports: 3001/tcp
  protocols: 
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules:
```
✅

## 1.18 Linux Resource Limits

On our `Storage` server in `Stratos Datacenter` we are having some issues where `nfsuser` user is holding hundred of processes, which is degrading the performance of the server. Therefore, we have a requirement to limit its maximum processes. Please set its maximum process limits as below:
1. soft limit = `1027`
2. hard_limit = `2027`

---

Login to the storage server and edit the limits.conf file adding following:
```bash
$ sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@172.16.238.15
$ sudo vi /etc/security/limits.conf 
...
@nfsuser         soft    nproc           1027
@nfsuser         hard    nproc           2027
...
```
✅

## 1.19 Selinux Installation

The xFusionCorp Industries security team recently did a security audit of their infrastructure and came up with ideas to improve the application and server security. They decided to use SElinux for an additional security layer. They are still planning how they will implement it; however, they have decided to start testing with app servers, so based on the recommendations they have the following requirements:

Install the required packages of SElinux on `App server 1` in `Stratos Datacenter` and disable it permanently for now; it will be enabled after making some required configuration changes on this host. Don't worry about rebooting the server as there is already a reboot scheduled for tonight's maintenance window. Also ignore the status of SElinux command line right now; the final status after reboot should be `disabled`.

---

Switch to root and install selinux:
```bash
$ sudo su -
$ yum -y install selinux*
$ vi /etc/selinux/config
...
SELINUX=disabled
...
```
✅