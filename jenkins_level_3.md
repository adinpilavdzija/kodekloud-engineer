# Jenkins Level 3 (Tasks 3.1-3.5) 

## 3.1 Jenkins Slave Nodes

The Nautilus DevOps team has installed and configured new Jenkins server in Stratos DC which they will use for CI/CD and for some automation tasks. There is a requirement to add all app servers as slave nodes in Jenkins so that they can perform tasks on these servers using Jenkins. Find below more details and accomplish the task accordingly.

Click on the `Jenkins` button on the top bar to access the Jenkins UI. Login using username `admin` and password `Adm!n321`.

1. Add all app servers as SSH build agent/slave nodes in Jenkins. Slave node name for `app server 1`, `app server 2` and `app server 3` must be `App_server_1`, `App_server_2`, `App_server_3` respectively.
2. Add labels as below:
`App_server_1 : stapp01`
`App_server_2 : stapp02`
`App_server_3 : stapp03`
3. Remote root directory for `App_server_1` must be `/home/tony/jenkins`, for `App_server_2` must be `/home/steve/jenkins` and for `App_server_3` must be `/home/banner/jenkins`.
4. Make sure slave nodes are online and working properly.

`Note:`
1. You might need to install some plugins and restart Jenkins service. So, we recommend clicking on `Restart Jenkins when installation is complete and no jobs are running` on plugin installation/update page i.e `update centre`. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case, please make sure to refresh the UI page.
2. For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

---

- Click on the Jenkins button and Login, username: admin - password: Adm!n321
- Manage Jenkins - Plugins - Updates - Check all - Update - Download progress - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Plugins - Available plugins - Check `SSH` and `SSH Build Agents` - Install - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Credentials - System - Global credentials 
    - Add credentials - Kind: Username with password - Scope: Global - Username: tony - Password: Ir0nM@n - ID: Tony - Create
    - Add credentials - Kind: Username with password - Scope: Global - Username: steve - Password: Am3ric@ - ID: Steve - Create
    - Add credentials - Kind: Username with password - Scope: Global - Username: banner - Password: BigGr33n - ID: Banner - Create
- Manage Jenkins - Nodes
    - New Node - Node name: App_server_1 - Type: Permanent Agent - Number of executors: 1 - Remote root directory: /home/tony/jenkins - Labels: stapp01 - Launch method: Launch agents via SSH - Host: 172.16.238.10 - Credentials: tony/***** - Host Key Verification Strategy: Non verifying Verification Strategy - Save
    - New Node - Node name: App_server_2 - Type: Permanent Agent - Number of executors: 1 - Remote root directory: /home/steve/jenkins - Labels: stapp02 - Launch method: Launch agents via SSH - Host: 172.16.238.11 - Credentials: steve/***** - Host Key Verification Strategy: Non verifying Verification Strategy - Save
    - New Node - Node name: App_server_3 - Type: Permanent Agent - Number of executors: 1 - Remote root directory: /home/banner/jenkins - Labels: stapp03 - Launch method: Launch agents via SSH - Host: 172.16.238.12 - Credentials: banner/***** - Host Key Verification Strategy: Non verifying Verification Strategy - Save

- Manage Jenkins - Nodes - App_server_1 - Launch agent
```
[10/14/25 20:49:21] [SSH] Starting sftp client.
[10/14/25 20:49:21] [SSH] Copying latest remoting.jar...
Source agent hash is A2E96D08000E53966B4B8F68E4999483. Installed agent hash is A2E96D08000E53966B4B8F68E4999483
Verified agent jar. No update is necessary.
Expanded the channel window size to 4MB
[10/14/25 20:49:21] [SSH] Starting agent process: cd "/home/tony/jenkins" && java  -jar remoting.jar -workDir /home/tony/jenkins -jar-cache /home/tony/jenkins/remoting/jarCache
bash: line 1: java: command not found
Agent JVM has terminated. Exit code=127
[10/14/25 20:49:21] Launch failed - cleaning up connection
[10/14/25 20:49:21] [SSH] Connection closed.
```

- Manage Jenkins - System - SSH remote hosts
    - Add - Hostname: stapp01 - Port: 22 - Credentials: tony - Check Pty - Check connection
    - Add - Hostname: stapp02 - Port: 22 - Credentials: steve - Check Pty - Check connection
    - Add - Hostname: stapp03 - Port: 22 - Credentials: banner - Check Pty - Check connection
    - Save

- New item - Enter an item name: `Java` - Select an item type: Freestyle project - Build steps - Execute shell script on remote host using ssh - SSH site: tony@stapp01:22 - Command: `echo Ir0nM@n | sudo -S sudo yum install -y java` - Save
- Dashboard - `Java` - Build Now
- Dashboard - `Java` - Configuration - Build steps - Execute shell script on remote host using ssh - SSH site: steve@stapp02:22 - Command: `echo Am3ric@ | sudo -S sudo yum install -y java` - Save
- Dashboard - `Java` - Build Now
- Dashboard - `Java` - Configuration - Build steps - Execute shell script on remote host using ssh - SSH site: banner@stapp03:22 - Command: `echo BigGr33n | sudo -S sudo yum install -y java` - Save
- Dashboard - `Java` - Build Now

- Manage Jenkins - Nodes - App_server_1 - Launch agent
- Manage Jenkins - Nodes - App_server_2 - Launch agent
- Manage Jenkins - Nodes - App_server_3 - Launch agent

<img width="1914" height="547" alt="3.1" src="https://github.com/user-attachments/assets/afd32722-e601-430c-8fd3-a96aab189486" />

✅

## 3.2 Jenkins Project Security

The xFusionCorp Industries has recruited some new developers. There are already some existing jobs on Jenkins and two of these new developers need permissions to access those jobs. The development team has already shared those requirements with the DevOps team, so as per details mentioned below grant required permissions to the developers.

Click on the `Jenkins` button on the top bar to access the Jenkins UI. Login using username `admin` and password `Adm!n321`.
1. There is an existing Jenkins job named `Packages`, there are also two existing Jenkins users named `sam` with password `sam@pass12345` and `rohan` with password `rohan@pass12345`.
2. Grant permissions to these users to access `Packages` job as per details mentioned below:
- Make sure to select `Inherit permissions from parent ACL` under `inheritance strategy` for granting permissions to these users.
- Grant mentioned permissions to `sam` user: `build`, `configure` and `read`.
- Grant mentioned permissions to `rohan` user: `build`, `cancel`, `configure`, `read`, `update` and `tag`.

`Note:`
1. Please do not modify/alter any other existing job configuration.
2. You might need to install some plugins and restart Jenkins service. So, we recommend clicking on `Restart Jenkins when installation is complete and no jobs are running` on plugin installation/update page i.e `update centre`. Also Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case, please make sure to refresh the UI page.
3. For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

---

- Click on the Jenkins button and Login, username: admin - password: Adm!n321
- Manage Jenkins - Plugins - Available plugins - Check `Matrix Authorization Strategy` - Install
- Manage Jenkins - Plugins - Updates - Check all - Update - Download progress - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Security - Authorization: Project-based Matrix Authorization Strategy - Save
- `Packages` - Configure - Enable project-based security - Inheritance Strategy: Inherit permissions from parent ACL - 
  - Add user - User ID: sam - Check: Build, Configure, Read
  - Add user - User ID: sam - Check: Build, Cancel, Configure, Read, Update, Tag
  - Save

✅

## 3.3 Jenkins Build Images

One of the DevOps engineers was working on to create a Dockerfile for Nginx. We need to build an image using that Dockerfile. The deployment must be done using a Jenkins pipeline. Below you can find more details about the same.

Click on the `Jenkins` button on the top bar to access the Jenkins UI. Login using username `admin` and password `Adm!n321`.

Similarly, click on the `Gitea` button on the top bar to access the Gitea UI. Login using username `sarah` and password `Sarah_pass123`. There is a repository named `sarah/web` in Gitea.

Create/configure a Jenkins pipeline job named `nginx-container`, configure it to run on server `App Server 2`.
- The pipeline can have just one stage named `Build`. (name is case sensitive)
- In the `Build` stage, build an image named `stregi01.stratos.xfusioncorp.com:5000/nginx:latest` using the Dockerfile present under the Git repository. `stregi01.stratos.xfusioncorp.com:5000` is the image registry server. After building the image push the same to the image registry server.

`Note:`

You might need to install some plugins and restart Jenkins service. So, we recommend clicking on `Restart Jenkins when installation is complete and no jobs are running` on plugin installation/update page i.e `update centre`. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case, please make sure to refresh the UI page.

For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

---

- Click on the Jenkins button and Login, username: admin - password: Adm!n321
- Manage Jenkins - Plugins - Updates - Check all - Update - Download progress - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Plugins - Available plugins - Check `SSH`, `SSH Credentials`, `Credentials`, `Git`, `Pipeline` - Install - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Credentials - System - Global credentials 
    - Add credentials - Kind: Username with password - Scope: Global - Username: steve - Password: Am3ric@ - ID: app-server-2-ssh - Description: App Server 2 SSH - Create
    - Add credentials - Kind: Username with password - Scope: Global - Username: sarah - Password: Sarah_pass123 - ID: gitea-credentials - Description: Gitea Sarah - Create
- New item - Enter an item name: `nginx-container` - Select an item type: Pipeline - Pipeline - Definition: Pipeline script - Check Use Groovy Sandbox - Script (Save after Script):

```groovy
pipeline {
    agent any
    
    environment {
        REGISTRY = 'stregi01.stratos.xfusioncorp.com:5000'
        IMAGE_NAME = 'nginx'
        IMAGE_TAG = 'latest'
        APP_SERVER = 'stapp02.stratos.xfusioncorp.com'
        GITEA_REPO = 'git.stratos.xfusioncorp.com/sarah/web.git'
    }
    
    stages {
        stage('Build') {
            steps {
                script {
                    withCredentials([
                        usernamePassword(credentialsId: 'app-server-2-ssh', 
                                       usernameVariable: 'SSH_USER', 
                                       passwordVariable: 'SSH_PASS'),
                        usernamePassword(credentialsId: 'gitea-credentials', 
                                       usernameVariable: 'GIT_USER', 
                                       passwordVariable: 'GIT_PASS')
                    ]) {
                        sh '''
                            sshpass -p "${SSH_PASS}" ssh -o StrictHostKeyChecking=no ${SSH_USER}@${APP_SERVER} "
                                cd /tmp && rm -rf web
                                git clone http://${GIT_USER}:${GIT_PASS}@${GITEA_REPO}
                                cd web
                                echo '${SSH_PASS}' | sudo -S docker build -t ${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG} .
                                echo '${SSH_PASS}' | sudo -S docker push ${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}
                            "
                        '''
                    }
                }
            }
        }
    }
}
```

- nginx-container - Build Now

```bash
$ sshpass -p 'Am3ric@' ssh -o StrictHostKeyChecking=no steve@172.16.238.11

$ docker images
REPOSITORY                                    TAG       IMAGE ID       CREATED         SIZE
stregi01.stratos.xfusioncorp.com:5000/nginx   latest    78b2bc6f95ec   2 minutes ago   11.5MB

$ curl -X GET http://stregi01.stratos.xfusioncorp.com:5000/v2/_catalog
{"repositories":["nginx"]}

$ curl -X GET http://stregi01.stratos.xfusioncorp.com:5000/v2/nginx/tags/list
{"name":"nginx","tags":["latest"]}

$ curl http://stregi01.stratos.xfusioncorp.com:5000/v2/nginx/manifests/latest
{
   "schemaVersion": 1,
   "name": "nginx",
   "tag": "latest",
   "architecture": "amd64",
   "fsLayers": [
```

<img width="1918" height="953" alt="3-1" src="https://github.com/user-attachments/assets/3cc00004-7bd9-4052-9d78-f2faa9d2f971" />
<img width="1919" height="948" alt="3-2" src="https://github.com/user-attachments/assets/140b1890-2df5-4f47-b5b8-b31c4ef75cca" />

✅

## 3.4 Jenkins Deploy Pipeline

The development team of xFusionCorp Industries is working on to develop a new static website and they are planning to deploy the same on Nautilus App Servers using Jenkins pipeline. They have shared their requirements with the DevOps team and accordingly we need to create a Jenkins pipeline job. Please find below more details about the task:

Click on the `Jenkins` button on the top bar to access the Jenkins UI. Login using username `admin` and password `Adm!n321`.

Similarly, click on the `Gitea` button on the top bar to access the Gitea UI. Login using username `sarah` and password `Sarah_pass123`. There under user `sarah` you will find a repository named `web_app` that is already cloned on `Storage server` under `/var/www/html`. sarah is a developer who is working on this repository.

1. Add a slave node named `Storage Server`. It should be labeled as `ststor01` and its remote root directory should be `/var/www/html`.
2. We have already cloned repository on `Storage Server` under `/var/www/html`.
3. Apache is already installed on all app Servers its running on port `8080`.
4. Create a Jenkins pipeline job named `xfusion-webapp-job` (it must not be a `Multibranch pipeline`) and configure it to:
- Deploy the code from `web_app` repository under `/var/www/html` on `Storage Server`, as this location is already mounted to the document root `/var/www/html` of app servers. The pipeline should have a single stage named `Deploy` ( which is case sensitive ) to accomplish the deployment.

LB server is already configured. You should be able to see the latest changes you made by clicking on the `App` button. Please make sure the required content is loading on the main URL `https://<LBR-URL>` i.e there should not be a sub-directory like `https://<LBR-URL>/web_app` etc.

`Note:`
1. You might need to install some plugins and restart Jenkins service. So, we recommend clicking on `Restart Jenkins when installation is complete and no jobs are running` on plugin installation/update page i.e `update centre`. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case, please make sure to refresh the UI page.
2. For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

---

```bash
$ sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@172.16.238.15
$ ls -l /var/www/
total 4
drwxr-xr-x 3 sarah sarah 4096 Oct 19 16:21 html
$ ls -l /var/www/html/
total 4
-rw-r--r-- 1 sarah sarah 35 Oct 19 16:21 index.html
$ sudo chown -R natasha:natasha /var/www/html/.git
$ sudo chown natasha:natasha /var/www/html
$ java --version
-bash: java: command not found
$ sudo yum install -y java
$ java --version
openjdk 17.0.16 2025-07-15 LTS
OpenJDK Runtime Environment (Red_Hat-17.0.16.0.8-1) (build 17.0.16+8-LTS)
OpenJDK 64-Bit Server VM (Red_Hat-17.0.16.0.8-1) (build 17.0.16+8-LTS, mixed mode, sharing)

$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10
$ systemctl status httpd
● httpd.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; preset: disabled)
     Active: active (running) since Sun 2025-10-19 16:21:59 UTC; 4min 25s ago
     ...
```

- Click on the Jenkins button and Login, username: admin - password: Adm!n321
- Manage Jenkins - Plugins - Updates - Check all - Update - Download progress - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Plugins - Available plugins - Check `SSH`, `SSH Build Agents`, `Git`, `Pipeline` - Install - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Credentials - System - Global credentials 
    - Add credentials - Kind: Username with password - Scope: Global - Username: natasha - Password: Bl@kW - ID: storage-server - Description: Storage Server SSH - Create
    - Add credentials - Kind: Username with password - Scope: Global - Username: sarah - Password: Sarah_pass123 - ID: gitea-credentials - Description: Gitea Sarah - Create
- Manage Jenkins - Nodes - New Node - Node name: Storage Server - Type: Permanent Agent - Number of executors: 1 - Remote root directory: /var/www/html - Labels: ststor01 - Usage: Only build jobs with label expressions matching this node - Launch method: Launch agents via SSH - Host: 172.16.238.15 - Credentials: natasha/***** - Host Key Verification Strategy: Non verifying Verification Strategy - Save
- Manage Jenkins - Nodes - Storage_server - Launch agent
- New item - Enter an item name: `xfusion-webapp-job` - Select an item type: Pipeline - Pipeline - Definition: Pipeline script - Check Use Groovy Sandbox - Script (Save after Script):
```groovy
pipeline {
    agent {
        label 'ststor01'
    }
    
    stages {
        stage('Deploy') {
            steps {
                sh '''
                    git config --global --add safe.directory /var/www/html
                    cd /var/www/html
                    git fetch origin master
                    git checkout origin/master -- .
                '''
            }
        }
    }
}
```
- xfusion-webapp-job - Build Now

```bash
$ ls -la /var/www/html/
total 1392
drwxr-xr-x 6 natasha natasha    4096 Oct 19 17:15 .
drwxr-xr-x 3 sarah   sarah      4096 Oct 19 16:21 ..
drwxr-xr-x 8 natasha natasha    4096 Oct 19 17:15 .git
drwxr-xr-x 3 natasha natasha    4096 Oct 19 17:06 caches
-rw-r--r-- 1 natasha natasha      35 Oct 19 17:15 index.html
drwxr-xr-x 4 natasha natasha    4096 Oct 19 17:02 remoting
-rw-r--r-- 1 natasha natasha 1395562 Oct 19 17:02 remoting.jar
drwxr-xr-x 4 natasha natasha    4096 Oct 19 17:06 workspace
```

<img width="1919" height="651" alt="4-1" src="https://github.com/user-attachments/assets/24b6d8df-d6f8-4124-b6db-af8ba4c58cad" />
<img width="602" height="157" alt="4-2" src="https://github.com/user-attachments/assets/05d80256-5725-4ec8-88bf-b18628f01d68" />

✅

## 3.5 Jenkins Conditional Pipeline

The development team of xFusionCorp Industries is working on to develop a new static website and they are planning to deploy the same on Nautilus App Servers using Jenkins pipeline. They have shared their requirements with the DevOps team and accordingly we need to create a Jenkins pipeline job. Please find below more details about the task:

Click on the `Jenkins` button on the top bar to access the Jenkins UI. Login using username `admin` and password `Adm!n321`.

Similarly, click on the `Gitea` button on the top bar to access the Gitea UI. Login using username `sarah` and password `Sarah_pass123`. There under user `sarah` you will find a repository named `web_app` that is already cloned on `Storage server` under `/var/www/html`. sarah is a developer who is working on this repository.

1. Add a slave node named `Storage Server`. It should be labeled as `ststor01` and its remote root directory should be `/var/www/html`.
2. We have already cloned repository on `Storage Server` under `/var/www/html`.
3. Apache is already installed on all app Servers its running on port `8080`.
4. Create a Jenkins pipeline job named `nautilus-webapp-job` (it must not be a `Multibranch pipeline`) and configure it to:
  - Add a string parameter named `BRANCH`.
  - It should conditionally deploy the code from `web_app` repository under `/var/www/html` on `Storage Server`, as this location is already mounted to the document root `/var/www/html` of app servers. The pipeline should have a single stage named `Deploy` (which is case sensitive) to accomplish the deployment.
  - The pipeline should be conditional, if the value `master` is passed to the `BRANCH` parameter then it must deploy the `master` branch, on the other hand if the value `feature` is passed to the `BRANCH` parameter then it must deploy the `feature` branch.

LB server is already configured. You should be able to see the latest changes you made by clicking on the `App` button. Please make sure the required content is loading on the main URL `https://<LBR-URL>` i.e there should not be a sub-directory like `https://<LBR-URL>/web_app` etc.

`Note:`
1. You might need to install some plugins and restart Jenkins service. So, we recommend clicking on `Restart Jenkins when installation is complete and no jobs are running` on plugin installation/update page i.e `update centre`. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case, please make sure to refresh the UI page.
2. For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

---

```bash
$ sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@172.16.238.15
$ ls -l /var/www/
total 4
drwxr-xr-x 3 sarah sarah 4096 Oct 20 18:03 html
$ ls -la /var/www/html/
total 20
drwxr-xr-x 3 sarah sarah 4096 Oct 20 18:03 .
drwxr-xr-x 3 sarah sarah 4096 Oct 20 18:02 ..
drwxr-xr-x 8 sarah sarah 4096 Oct 20 18:03 .git
-rw-r--r-- 1 sarah sarah   18 Oct 20 18:03 feature.html
-rw-r--r-- 1 sarah sarah    8 Oct 20 18:03 index.html

$ sudo chown -R natasha:natasha /var/www/html/.git
$ sudo chown natasha:natasha /var/www/html
$ java --version
-bash: java: command not found
$ sudo yum install java -y
$ java --version
openjdk 17.0.16 2025-07-15 LTS
OpenJDK Runtime Environment (Red_Hat-17.0.16.0.8-1) (build 17.0.16+8-LTS)
OpenJDK 64-Bit Server VM (Red_Hat-17.0.16.0.8-1) (build 17.0.16+8-LTS, mixed mode, sharing)

$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10
$ systemctl status httpd
● httpd.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; preset: disabled)
     Active: active (running) since Mon 2025-10-20 18:03:15 UTC; 11min ago
     ...
```

- Click on the Jenkins button and Login, username: admin - password: Adm!n321
- Manage Jenkins - Plugins - Updates - Check all - Update - Download progress - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Plugins - Available plugins - Check `SSH`, `SSH Build Agents`, `Git`, `Pipeline` - Install - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Credentials - System - Global credentials 
    - Add credentials - Kind: Username with password - Scope: Global - Username: natasha - Password: Bl@kW - ID: storage-server - Description: Storage Server SSH - Create
    - Add credentials - Kind: Username with password - Scope: Global - Username: sarah - Password: Sarah_pass123 - ID: gitea-credentials - Description: Gitea Sarah - Create
- Manage Jenkins - Nodes - New Node - Node name: Storage Server - Type: Permanent Agent - Number of executors: 1 - Remote root directory: /var/www/html - Labels: ststor01 - Usage: Only build jobs with label expressions matching this node - Launch method: Launch agents via SSH - Host: 172.16.238.15 - Credentials: natasha/***** - Host Key Verification Strategy: Non verifying Verification Strategy - Save
- Manage Jenkins - Nodes - Storage_server - Launch agent
- New item - Enter an item name: `nautilus-webapp-job` - Select an item type: Pipeline - Pipeline - This project is parameterized: Choice Parameter, Name: BRANCH, Description: Branch to deploy (master or feature), Choices: 
```
master
feature
```
    - Definition: Pipeline script - Check Use Groovy Sandbox - Script (Save after Script):
```groovy
pipeline {
    agent {
        label 'ststor01'
    }
    
    parameters {
        string(name: 'BRANCH', defaultValue: 'master', description: 'Branch to deploy (master or feature)')
    }
    
    stages {
        stage('Deploy') {
            steps {
                sh """
                    git config --global --add safe.directory /var/www/html
                    cd /var/www/html
                    git fetch origin ${params.BRANCH}
                    git checkout -f origin/${params.BRANCH}
                """
            }
        }
    }
}
```
- xfusion-webapp-job - Build with Parameters

```bash
# after executing with parameter BRANCH=master:
$ ls -la /var/www/html/
total 1392
drwxr-xr-x 6 natasha natasha    4096 Oct 20 18:39 .
drwxr-xr-x 3 sarah   sarah      4096 Oct 20 18:02 ..
drwxr-xr-x 8 natasha natasha    4096 Oct 20 18:39 .git
drwxr-xr-x 3 natasha natasha    4096 Oct 20 18:36 caches
-rw-r--r-- 1 natasha natasha      35 Oct 20 18:39 index.html
drwxr-xr-x 4 natasha natasha    4096 Oct 20 18:27 remoting
-rw-r--r-- 1 natasha natasha 1395562 Oct 20 18:27 remoting.jar
drwxr-xr-x 4 natasha natasha    4096 Oct 20 18:36 workspace

# after executing with parameter BRANCH=feature:
$ ls -la /var/www/html/
total 1396
drwxr-xr-x 6 natasha natasha    4096 Oct 20 18:42 .
drwxr-xr-x 3 sarah   sarah      4096 Oct 20 18:02 ..
drwxr-xr-x 8 natasha natasha    4096 Oct 20 18:42 .git
drwxr-xr-x 3 natasha natasha    4096 Oct 20 18:36 caches
-rw-r--r-- 1 natasha natasha      18 Oct 20 18:42 feature.html
-rw-r--r-- 1 natasha natasha       8 Oct 20 18:42 index.html
drwxr-xr-x 4 natasha natasha    4096 Oct 20 18:27 remoting
-rw-r--r-- 1 natasha natasha 1395562 Oct 20 18:27 remoting.jar
drwxr-xr-x 4 natasha natasha    4096 Oct 20 18:36 workspace
```

<img width="570" height="172" alt="5-1" src="https://github.com/user-attachments/assets/ea17c4e6-620e-47e1-9360-4cd076e8681d" />
<img width="608" height="220" alt="5-2" src="https://github.com/user-attachments/assets/c04b6e62-8aa7-40e7-9222-588a683dae11" />

✅

<img width="1123" height="447" alt="jenkins3" src="https://github.com/user-attachments/assets/29848370-6d7a-410b-b576-79b063af5212" />

✅
