# Jenkins Level 2 (Tasks 2.1-2.5) 

## 2.1 Jenkins Views

The DevOps team of xFusionCorp Industries is planning to create a number of Jenkins jobs for different tasks. So to easily manage the jobs within Jenkins UI they decided to create different views for all Jenkins jobs based on usage/nature of these jobs, - for example `xfusion-crons` view for all cron jobs. Based on the requirements shared below please perform the below mentioned task:

Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username `admin` and password `Adm!n321`.

1. Create a Jenkins job named `xfusion-test-job`.
2. Configure this job to run a simple bash command i.e `echo "hello world!!"`.
3. Create a view named `xfusion-crons` (it must be a `global` view of type `List View`) and make sure `xfusion-test-job` and `xfusion-cron-job` (which is already present on Jenkins) jobs are listed under this new view.
4. Schedule this newly created job to build periodically at `every minute` i.e `* * * * *` (`please make sure to use the cron expression exactly same how it is mentioned here`)
5. Make sure the job builds successfully.

`Note:`
1. You might need to install some plugins and restart Jenkins service. So, we recommend clicking on `Restart Jenkins when installation is complete and no jobs are running` on plugin installation/update page i.e `update centre`. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case please make sure to refresh the UI page.
2. For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

---

- Click on the Jenkins button and Login, username: admin - password: Adm!n321
- New item - Enter an item name: `xfusion-test-job` - Select an item type: Freestyle project
- `xfusion-test-job` - Build steps - Execute shell - Command: `echo "hello world!!"` - Triggers - Build periodically - Schedule: `* * * * *` - Save
- New view - Name: `xfusion-crons` - Type: List View - Create
- `xfusion-crons` - Edit View - Jobs: check `xfusion-cron-job` and `xfusion-test-job` - Save

<img width="920" height="642" alt="1" src="https://github.com/user-attachments/assets/3dd8597d-67f1-4892-be22-54e76fc616ec" />

✅

## 2.2 Jenkins Parameterized Builds

A new DevOps Engineer has joined the team and he will be assigned some Jenkins related tasks. Before that, the team wanted to test a simple parameterized job to understand basic functionality of parameterized builds. He is given a simple parameterized job to build in Jenkins. Please find more details below:

Click on the `Jenkins` button on the top bar to access the Jenkins UI. Login using username `admin` and password `Adm!n321`.

1. Create a `parameterized` job which should be named as `parameterized-job`
2. Add a `string` parameter named `Stage`; its default value should be `Build`.
3. Add a `choice` parameter named `env`; its choices should be `Development`, `Staging` and `Production`.
4. Configure job to execute a shell command, which should echo both parameter values (you are passing in the job).
5. Build the Jenkins job at least once with choice parameter value `Staging` to make sure it passes.

`Note:`
1. You might need to install some plugins and restart Jenkins service. So, we recommend clicking on `Restart Jenkins when installation is complete and no jobs are running` on plugin installation/update page i.e `update centre`. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case, please make sure to refresh the UI page.
2. For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

---

- Click on the Jenkins button and Login, username: admin - password: Adm!n321
- New item - Enter an item name: `parameterized-job` - This project is parameterized - Add Parameter - String parameter - Name: Stage - Default value: Build 
- Add Parameter - Choice parameter - Name: env - Choices: Development, Staging and Production 
- Build steps - Execute shell - Command: 
```bash
echo $Stage
echo $env
```
- `parameterized-job` - Build with Parameters - Stage: Build - env: Staging - Build

<img width="929" height="385" alt="2" src="https://github.com/user-attachments/assets/96286206-f852-4d07-87c0-cb135f25a5ce" />

✅

## 2.3 Jenkins Workspaces

Some developers are working on a common repository where they are testing some features for an application. They are having three branches (excluding the master branch) in this repository where they are adding changes related to these different features. They want to test these changes on Stratos DC app servers so they need a Jenkins job using which they can deploy these different branches as per requirements. Configure a Jenkins job accordingly.

Click on the `Jenkins` button on the top bar to access the Jenkins UI. Login using username `admin` and password `Adm!n321`.

Similarly, click on `Gitea` button to access the Gitea page. Login to `Gitea` server using username `sarah` and password `Sarah_pass123`.

1. There is a Git repository named `web_app` on Gitea where developers are pushing their changes. It has three branches `version1`, `version2` and `version3` (excluding the `master` branch). You need not to make any changes in the repository.
2. Create a Jenkins job named `app-job`.
3. Configure this job to have a choice parameter named `Branch` with choices as given below:
`version1`
`version2`
`version3`
4. Configure the job to fetch changes from above mentioned Git repository and make sure it should fetches the changes from the respective branch which you are passing as a choice in the choice parameter while building the job. For example if you choose `version1` then it must fetch and deploy the changes from branch `version1`.
5. Configure this job to use custom workspace rather than a default workspace and custom workspace directory should be created under `/var/lib/jenkins` (for example `/var/lib/jenkins/version1`) location rather than under any sub-directory etc. The job should use a workspace as per the value you will pass for `Branch` parameter while building the job. For example if you choose `version1` while building the job then it should create a workspace directory called `version1` and should fetch Git repository etc within that directory only.
6. Configure the job to deploy code (fetched from Git repository) on storage server (in Stratos DC) under `/var/www/html` directory. Since its a shared volume.
7. You can access the website by clicking on `App` button.

`Note:`

1. You might need to install some plugins and restart Jenkins service. So, we recommend clicking on `Restart Jenkins when installation is complete and no jobs are running` on plugin installation/update page i.e `update centre`. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case please make sure to refresh the UI page.
2. For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

---

- Click on the Jenkins button and Login, username: admin - password: Adm!n321
- Manage Jenkins - Plugins - Updates - Check all - Update - Download progress - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Plugins - Available plugins - Check `Gitea 250.v76a_0b_d4fef5b_`, `SSH 158.ve2a_e90fb_7319`, `Publish Over SSH 387.vec3df0f668cd` - Install - Check `Restart Jenkins when installation is complete and no jobs are running`
- New item - Enter an item name: `app-job` - Select an item type: Freestyle project - Add Parameter - String parameter - Name: Stage - Default value: Build 
- `app-job` - This project is parameterized - Add Parameter - Choice parameter - Name: Branch - Choices:
```
version1
version2
version3
```
- Manage Jenkins - Credentials - System - Global credentials - Add credentials - Kind: Username with password - Scope: Global - Username: sarah - Password: Sarah_pass123 - ID: Sarah - Create
- Manage Jenkins - System - Publish over SSH - SSH Servers - Add - Name: ststor01 - Hostname: ststor01.stratos.xfusioncorp.com - Username: natasha - Remote directory: /var/www/html - Advanced - Use password authentication, or use a different key - Passphrase/Password: Bl@kW - Test configuration - Save
- `app-job` - Configuration - Source Code Management - Git - Repository URL: http://git.stratos.xfusioncorp.com/sarah/web_app.git - Credentials: sarah/******* - Branch Specifier: */$Branch - Save
- `app-job` - Configuration - Send files or execute commands over SSH after the build runs - SSH Server - Name: ststor01 - Source files: **/* - Save 
- `app-job` - Configuration - General - Advanced - Use custom workspace - Directory: /var/lib/jenkins/${Branch} - Save

```bash
$ sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@172.16.238.15
Warning: Permanently added '172.16.238.15' (ED25519) to the list of known hosts.
$ sudo su -
$ ls -ld /var/www/html
drwxr-xr-x 3 natasha natasha 4096 Jun 25 13:12 /var/www/html
$ chmod 777 /var/www/html
$ ls -ld /var/www/html
drwxrwxrwx 3 natasha natasha 4096 Jun 25 13:12 /var/www/html
```

<img width="1902" height="817" alt="jenkins23" src="https://github.com/user-attachments/assets/872a490c-b295-4c7d-ab29-b5cce3653589" />

✅

## 2.4 Jenkins Database Backup Job

There is a requirement to create a Jenkins job to automate the database backup. Below you can find more details to accomplish this task:

Click on the `Jenkins` button on the top bar to access the Jenkins UI. Login using username `admin` and password `Adm!n321`.

1. Create a Jenkins job named `database-backup`.
2. Configure it to take a database dump of the `kodekloud_db01` database present on the Database server in `Stratos Datacenter`, the database user is `kodekloud_roy` and password is `asdfgdsd`.
3. The dump should be named in `db_$(date +%F).sql` format, where `date +%F` is the current date.
4. Copy the `db_$(date +%F).sql` dump to the `Backup Server` under location `/home/clint/db_backups`.
5. Further, schedule this job to run periodically at `*/10 * * * *` (please use this exact schedule format).

`Note:`
1. You might need to install some plugins and restart Jenkins service. So, we recommend clicking on `Restart Jenkins when installation is complete and no jobs are running` on plugin installation/update page i.e `update centre`. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case please make sure to refresh the UI page.
2. Please make sure to define you cron expression like this `*/10 * * * *` (this is just an example to run job every 10 minutes).
3. For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

---

- Click on the Jenkins button and Login, username: admin - password: Adm!n321
- Manage Jenkins - Plugins - Updates - Check all - Update - Download progress - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Plugins - Available plugins - Check `SSH Credentials`, `SSH`, `SSH Build Agents` - Install - Check `Restart Jenkins when installation is complete and no jobs are running` 
- Manage Jenkins - Credentials - System - Global credentials - Add credentials - Kind: Username with password - Scope: Global - Username: peter - Password: Sp!dy - ID: Peter - Create
- Manage Jenkins - System - SSH remote hosts - Add - Hostname: stdb01.stratos.xfusioncorp.com - Port: 22 - Credentials: peter - Check connection - Save
- New item - Enter an item name: `database-backup` - Select an item type: Freestyle project - Build steps - Execute shell script on remote host using ssh - Command: 
```bash
mysqldump -u kodekloud_roy -pasdfgdsd > db_$(date +%F).sql

scp -o StrictHostKeyChecking=no db_$(date +%F).sql clint@stbkp01:/home/clint/db_backups/
```
- Triggers - Build periodically - Schedule: `*/10 * * * *` - Save

```bash
$ sshpass -p 'Sp!dy' ssh -o StrictHostKeyChecking=no peter@172.16.239.10
$ ssh-keygen -t rsa 
$ ssh-copy-id clint@stbkp01
Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'clint@stbkp01'"
and check to make sure that only the key(s) you wanted were added.
```

<img width="501" height="57" alt="4-1" src="https://github.com/user-attachments/assets/6369440c-61f7-4193-bc7c-9bdf46c4bbe0" />
<img width="741" height="466" alt="4-2" src="https://github.com/user-attachments/assets/612394e9-a83b-48fd-88ab-9d59cdf86b63" />

✅

## 2.5 Jenkins Scheduled Jobs

The devops team of xFusionCorp Industries is working on to setup centralised logging management system to maintain and analyse server logs easily. Since it will take some time to implement, they wanted to gather some server logs on a regular basis. At least one of the app servers is having issues with the Apache server. The team needs Apache logs so that they can identify and troubleshoot the issues easily if they arise. So they decided to create a Jenkins job to collect logs from the server. Please create/configure a Jenkins job as per details mentioned below:

Click on the `Jenkins` button on the top bar to access the Jenkins UI. Login using username `admin` and password `Adm!n321`

1. Create a Jenkins jobs named `copy-logs`.
2. Configure it to periodically build `every 12 minutes` to copy the Apache logs (`both access_log and error_logs`) from `App Server 1` (`from default logs location`) to location `/usr/src/security` on `Storage Server`.

`Note:`

1. You might need to install some plugins and restart Jenkins service. So, we recommend clicking on `Restart Jenkins when installation is complete and no jobs are running` on plugin installation/update page i.e `update centre`. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case please make sure to refresh the UI page.
2. Please make sure to define you cron expression like this `*/10 * * * *` (this is just an example to run job every 10 minutes).
3. For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

---

- Click on the Jenkins button and Login, username: admin - password: Adm!n321
- Manage Jenkins - Plugins - Updates - Check all - Update - Download progress - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Plugins - Available plugins - Check `SSH Credentials`, `SSH`, `SSH Build Agents` - Install - Check `Restart Jenkins when installation is complete and no jobs are running`

- Manage Jenkins - Credentials - System - Global credentials - Add credentials - Kind: Username with password - Scope: Global - Username: tony - Password: Ir0nM@n - ID: Tony - Create
- Manage Jenkins - System - SSH remote hosts - Add - Hostname: stapp01.stratos.xfusioncorp.com - Port: 22 - Credentials: tony - Check connection - Save
- New item - Enter an item name: `copy-logs` - Select an item type: Freestyle project - Build steps - Execute shell script on remote host using ssh - Command: 
```
scp -o StrictHostKeyChecking=no /var/log/httpd/access_log natasha@ststor01:/usr/src/security/

scp -o StrictHostKeyChecking=no /var/log/httpd/error_log natasha@ststor01:/usr/src/security/
```
- Triggers - Build periodically - Schedule: `*/12 * * * *` - Save

```bash
$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10
$ ssh-keygen -t rsa
$ ssh-copy-id natasha@ststor01
```

✅

<img width="1117" height="434" alt="2" src="https://github.com/user-attachments/assets/8b0b9d8f-b044-4d2c-af45-b7d8d6fa00f5" />

✅
