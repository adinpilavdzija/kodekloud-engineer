# Jenkins Level 4 (Tasks 4.1-4.5) 

## 4.1 Jenkins Deployment Job

The Nautilus development team had a meeting with the DevOps team where they discussed automating the deployment of one of their apps using Jenkins (the one in `Stratos Datacenter`). They want to auto deploy the new changes in case any developer pushes to the repository. As per the requirements mentioned below configure the required Jenkins job.

Click on the `Jenkins` button on the top bar to access the Jenkins UI. Login using username `admin` and `Adm!n321` password.

Similarly, you can access the `Gitea UI` using `Gitea` button, username and password for Git is `sarah` and `Sarah_pass123` respectively. Under user `sarah` you will find a repository named `web` that is already cloned on the `Storage server` under sarah's home. `sarah` is a developer who is working on this repository.

1. Install `httpd` (whatever version is available in the yum repo by default) and configure it to serve on port `8080` on All app servers. You can make it part of your Jenkins job or you can do this step manually on all app servers.
2. Create a Jenkins job named `nautilus-app-deployment` and configure it in a way so that if anyone pushes any new change to the origin repository in `master` branch, the job should auto build and deploy the latest code on the `Storage server` under `/var/www/html` directory. Since `/var/www/html` on `Storage server` is shared among all apps.
Before deployment, ensure that the ownership of the `/var/www/html` directory is set to user `sarah`, so that Jenkins can successfully deploy files to that directory.
3. SSH into `Storage Server` using `sarah` user credentials mentioned above. Under sarah user's home you will find a cloned Git repository named `web`. Under this repository there is an `index.html` file, update its content to `Welcome to the xFusionCorp Industries`, then push the changes to the `origin` into `master` branch. This push must trigger your Jenkins job and the latest changes must be deployed on the servers, also make sure it deploys the entire repository content not only `index.html` file.

Click on the `App` button on the top bar to access the app, you should be able to see the latest changes you deployed. Please make sure the required content is loading on the main URL `https://<LBR-URL>` i.e there should not be any sub-directory like `https://<LBR-URL>/web` etc.

`Note:`
1. You might need to install some plugins and restart Jenkins service. So, we recommend clicking on `Restart Jenkins when installation is complete and no jobs are running` on plugin installation/update page i.e `update centre`. Also some times Jenkins UI gets stuck when Jenkins service restarts in the back end so in such case please make sure to refresh the UI page.
2. Make sure Jenkins job passes even on repetitive runs as validation may try to build the job multiple times.
3. Deployment related tasks should be done by `sudo` user on the destination server to avoid any permission issues so make sure to configure your Jenkins job accordingly.
4. For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

---

```bash
$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10
$ sudo su -
$ yum install httpd -y
$ sed -i 's/Listen 80/Listen 8080/g' /etc/httpd/conf/httpd.conf 
$ systemctl restart httpd; systemctl enable httpd

$ sshpass -p 'Am3ric@' ssh -o StrictHostKeyChecking=no steve@172.16.238.11
$ sudo su -
$ yum install httpd -y
$ sed -i 's/Listen 80/Listen 8080/g' /etc/httpd/conf/httpd.conf 
$ systemctl restart httpd; systemctl enable httpd

$ sshpass -p 'BigGr33n' ssh -o StrictHostKeyChecking=no banner@172.16.238.12
$ sudo su -
$ yum install httpd -y
$ sed -i 's/Listen 80/Listen 8080/g' /etc/httpd/conf/httpd.conf 
$ systemctl restart httpd; systemctl enable httpd
```

- Click on the Jenkins button and Login, username: admin - password: Adm!n321
- Manage Jenkins - Plugins - Updates - Check all - Update - Download progress - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Plugins - Available plugins - Check `SSH`, `SSH Build Agents`, `Git`, `Git Client` - Install - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Credentials - System - Global credentials - Add credentials - Kind: Username with password - Scope: Global - Username: sarah - Password: Sarah_pass123 - ID: gitea-credentials - Description: Gitea Sarah - Create
- Manage Jenkins - Nodes - New Node - Node name: Storage Server - Type: Permanent Agent - Number of executors: 1 - Remote root directory: /home/sarah/jenkins - Labels: ststor01 - Usage: Only build jobs with label expressions matching this node - Launch method: Launch agents via SSH - Host: 172.16.238.15 - Credentials: sarah/***** - Host Key Verification Strategy: Non verifying Verification Strategy - Save
- Manage Jenkins - Nodes - Storage_server - Launch agent
- New item - Enter an item name: `nautilus-app-deployment` - Select an item type: Freestyle - Restrict where this project can be run: Label Expression: ststor01 - Source Code Management: Git - Repository URL: http://git.stratos.xfusioncorp.com/sarah/web.git - Credentials: sarah/****** - Branch Specifier: */master - Triggers: Poll SCM - Schedule: * * * * * - Build Steps: Execute shell - Command: 

```bash
echo "Current workspace: $WORKSPACE"
rm -rf /var/www/html/*
cp -r $WORKSPACE/* /var/www/html/
echo "Deployed files:"
ls -la /var/www/html/
```

```bash
$ sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@172.16.238.15
$ sudo su -
$ yum install java -y
$ java --version
openjdk 17.0.16 2025-07-15 LTS
OpenJDK Runtime Environment (Red_Hat-17.0.16.0.8-1) (build 17.0.16+8-LTS)
OpenJDK 64-Bit Server VM (Red_Hat-17.0.16.0.8-1) (build 17.0.16+8-LTS, mixed mode, sharing)
$ ls -ld /var/www/html
drwxr-xr-x 3 root root 4096 Aug 18 09:57 /var/www/html
$ chown -R sarah:sarah /var/www/html
$ ls -ld /var/www/html
drwxr-xr-x 3 sarah sarah 4096 Aug 18 09:57 /var/www/html

$ su - sarah
$ cd /home/sarah/web
$ echo "Welcome to the xFusionCorp Industries" > index.html
$ git status
On branch master
Your branch is up to date with 'origin/master'.
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   index.html
$ git add index.html
$ git commit -m "Update welcome message"
$ git push origin master

# After job is done, verify:

$ cat /var/www/html/index.html 
Welcome to the xFusionCorp Industries
```
✅

## 4.2 Jenkins Chained Builds

The DevOps team was looking for a solution where they want to restart Apache service on all app servers if the deployment goes fine on these servers in Stratos Datacenter. After having a discussion, they came up with a solution to use Jenkins chained builds so that they can use a downstream job for services which should only be triggered by the deployment job. So as per the requirements mentioned below configure the required Jenkins jobs.

Click on the `Jenkins` button on the top bar to access the Jenkins UI. Login using username `admin` and `Adm!n321` password.

Similarly you can access `Gitea UI` on port `8090` and username and password for Git is `sarah` and `Sarah_pass123` respectively. Under user `sarah` you will find a repository named `web`.

Apache is already installed and configured on all app server so no changes are needed there. The doc root `/var/www/html` on all these app servers is shared among the Storage server under `/var/www/html` directory.

1. Create a Jenkins job named `nautilus-app-deployment` and configure it to pull change from the `master` branch of `web` repository on `Storage server` under `/var/www/html` directory, which is already a local git repository tracking the origin `web` repository. Since `/var/www/html` on `Storage server` is a shared volume so changes should auto reflect on all apps.
2. Create another Jenkins job named `manage-services` and make it a `downstream job` for `nautilus-app-deployment` job. Things to take care about this job are:
- This job should restart `httpd` service on all app servers.
- Trigger this job only if the `upstream job` i.e `nautilus-app-deployment` is stable.

`LB` server is already configured. Click on the `App` button on the top bar to access the app. You should be able to see the latest changes you made. Please make sure the required content is loading on the main URL `https://<LBR-URL>` i.e there should not be a sub-directory like `https://<LBR-URL>/web` etc.

`Note:`
1. You might need to install some plugins and restart Jenkins service. So, we recommend clicking on `Restart Jenkins when installation is complete and no jobs are running` on plugin installation/update page i.e `update centre`. Also some times Jenkins UI gets stuck when Jenkins service restarts in the back end so in such case please make sure to refresh the UI page.
2. Make sure Jenkins job passes even on repetitive runs as validation may try to build the job multiple times.
3. Deployment related tasks should be done by `sudo` user on the destination server to avoid any permission issues so make sure to configure your Jenkins job accordingly.
4. For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

---

```bash
$ sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@172.16.238.15
$ sudo su -
$ java --version
-bash: java: command not found
$ yum install java -y
$ java --version
openjdk 17.0.16 2025-07-15 LTS
OpenJDK Runtime Environment (Red_Hat-17.0.16.0.8-1) (build 17.0.16+8-LTS)
OpenJDK 64-Bit Server VM (Red_Hat-17.0.16.0.8-1) (build 17.0.16+8-LTS, mixed mode, sharing)
$ ls -l /var/www/html
total 4
-rw-r--r-- 1 natasha natasha 8 Oct 29 17:42 index.html
$ ls -l /var/www
total 4
drwxr-xr-x 3 natasha natasha 4096 Oct 29 17:42 html
```

- Click on the Jenkins button and Login, username: admin - password: Adm!n321
- Manage Jenkins - Plugins - Updates - Check all - Update - Download progress - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Plugins - Available plugins - Check `SSH`, `SSH Build Agent`, `Git` - Install - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Credentials - System - Global credentials 
  - Add credentials - Kind: Username with password - Scope: Global - Username: sarah - Password: Sarah_pass123 - ID: sarah-gitea-credentials - Description: Sarah Gitea - Create
  - Add credentials - Kind: Username with password - Scope: Global - Username: natasha - Password: Bl@kW - ID: natasha-storage-server-credentials - Description: Natasha Storage Server - Create
  - Add credentials - Kind: Username with password - Scope: Global - Username: tony - Password: Ir0nM@n - ID: app-server-1-credentials - Description: Tony App Server 1 - Create
  - Add credentials - Kind: Username with password - Scope: Global - Username: steve - Password: Am3ric@ - ID: app-server-2-credentials - Description: Steve App Server 2 - Create
  - Add credentials - Kind: Username with password - Scope: Global - Username: banner - Password: BigGr33n - ID: app-server-3-credentials - Description: Banner App Server 3 - Create
- Manage Jenkins - Nodes - New Node - Node name: Storage Server - Type: Permanent Agent - Number of executors: 1 - Remote root directory: /home/natasha - Labels: ststor01 - Usage: Only build jobs with label expressions matching this node - Launch method: Launch agents via SSH - Host: 172.16.238.15 - Credentials: natasha/***** - Host Key Verification Strategy: Non verifying Verification Strategy - Save
- Manage Jenkins - Nodes - Storage_server - Launch agent
- New item - Enter an item name: `nautilus-app-deployment` - Select an item type: Freestyle - Restrict where this project can be run: Label Expression: ststor01 - Source Code Management: Git - Repository URL: http://git.stratos.xfusioncorp.com/sarah/web.git - Credentials: sarah/****** - Branch Specifier: */master - Triggers: Poll SCM - Schedule: * * * * * - Build Steps: Execute shell - Command: 
```bash
cd /var/www/html
git pull origin master
```

- New item - Enter an item name: `manage-services` - Select an item type: Freestyle - Build after other projects are built - Projects to watch: nautilus-app-deployment - Trigger only if build is stable - Environment - Use secret text(s) or file(s) - Bindings - Add: Username and password (separated)
  - Username Variable: SSH_APP1 - Password Variable: SSH_APP1_PASS - Credentials: Specific credentials: tony/****** (Tony App Server 1)
  - Username Variable: SSH_APP2 - Password Variable: SSH_APP2_PASS - Credentials: Specific credentials: steve/****** (Steve App Server 2)
  - Username Variable: SSH_APP3 - Password Variable: SSH_APP3_PASS - Credentials: Specific credentials: banner/****** (Banner App Server 3)
  - Build Steps: Execute shell - Command: 
```bash
set +x
sshpass -p "$SSH_APP1_PASS" ssh -o StrictHostKeyChecking=no $SSH_APP1@172.16.238.10 "
    echo '$SSH_APP1_PASS' | sudo -S systemctl restart httpd; sudo systemctl status httpd
  "

sshpass -p "$SSH_APP2_PASS" ssh -o StrictHostKeyChecking=no $SSH_APP2@172.16.238.11 "
    echo '$SSH_APP2_PASS' | sudo -S systemctl restart httpd; sudo systemctl status httpd
  "

sshpass -p "$SSH_APP3_PASS" ssh -o StrictHostKeyChecking=no $SSH_APP3@172.16.238.12 "
    echo '$SSH_APP3_PASS' | sudo -S systemctl restart httpd; sudo systemctl status httpd
  "
```
  - Save

```bash
$ su - natasha
$ cd /var/www/html/
$ cat index.html 
Welcome to KodeKloud!

$ cat index.html 
Welcome to KodeKloud!
Add text

$ git status
On branch master
Your branch is up to date with 'origin/master'.
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   index.html

$ git add index.html 
$ git commit -m "Edit index.html"
$ git push
```
<img width="653" height="175" alt="4-2" src="https://github.com/user-attachments/assets/355d4afd-2dc2-4468-a0d8-db2daeeded62" />

✅

## 4.3 Jenkins MR Jobs

Click on the `Jenkins` button on the top bar to access the Jenkins UI. Login using username `admin` and password `Adm!n321`.  

Similarly, click on the `Gitea` button on the top bar to access the Gitea UI. Login using username `sarah` and password `Sarah_pass123`.  

There is a repository named `sarah/mr_job` in Gitea, which is cloned on the `Storage` server under `/home/natasha/mr_job` directory.  

- Update the `index.html` file under `dev` branch, and change its content from `Welcome to Nautilus Group!` to `Welcome to xFusionCorp Industries!`. Remember to push your changes to the origin repository.  
- After pushing the required changes, login to the `Gitea` server and you will find a pull request with title `My First PR` under `mr_job` repository. Merge this pull request.  
- Create/configure a Jenkins pipeline job named `nginx-container`, configure a pipeline as per details given below and run the pipeline on server `App Server 1`.  
  - The pipeline must have two stages `Build` and `Deploy` (names are case sensitive).  
  - In the `Build` stage, first clone the `sarah/mr_job` repository, then build an image named `stregi01.stratos.xfusioncorp.com:5000/nginx:latest` using the `Dockerfile` present under the root of the repository. `stregi01.stratos.xfusioncorp.com:5000` is the image registry server. After building the image push the same to the image registry server.  
  - In the `Deploy` stage, create a container named `nginx-app` using the image you built in the `Build` stage. Make sure to map container port to the host port `8080` and run the container in detached mode.  
  - Make sure to build a successful job at least once so that you have at least one successful build # in the job history. Further, you can test the app using command `curl http://stapp01:8080` from the `jump host`.  

`Note:`  
1. You might need to install some plugins and restart Jenkins service. So, we recommend clicking on `Restart Jenkins when installation is complete and no jobs are running` on plugin installation/update page i.e `update centre`. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case, please make sure to refresh the UI page.  
2. For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

---

```bash
$ sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@172.16.238.15
$ cd /home/natasha/mr_job
$ ls -la
total 20
drwxr-xr-x 3 natasha natasha 4096 Nov  1 15:38 .
drwx------ 1 natasha natasha 4096 Nov  1 15:38 ..
drwxr-xr-x 8 natasha natasha 4096 Nov  1 15:38 .git
-rw-r--r-- 1 natasha natasha   72 Nov  1 15:38 Dockerfile
-rw-r--r-- 1 natasha natasha   27 Nov  1 15:38 index.html

$ cat index.html 
Welcome to Nautilus Group!

$ git branch
* dev
  master

$ echo 'Welcome to xFusionCorp Industries!' > index.html 

$ cat index.html 
Welcome to xFusionCorp Industries!

$ git add index.html

$ git commit -m "Update welcome message"
[dev e138262] Update welcome message
 1 file changed, 1 insertion(+), 1 deletion(-)
[natasha@ststor01 mr_job]$ git push

$ git push --set-upstream origin dev

`sarah` and password `Sarah_pass123`
- Gitea - Login - Username: sarah, Password: Sarah_pass123 - sarah / mr_job - Pull Requests - My First PR - Merge Pull Request

$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10
$ mkdir /home/tony/jenkins
$ sudo su -
$ yum install java -y
$ java --version
```

- Click on the Jenkins button and Login, username: admin - password: Adm!n321
- Manage Jenkins - Plugins - Updates - Check all - Update - Download progress - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Plugins - Available plugins - Check `SSH`, `SSH Credentials`, `SSH Build Agents`, `Git`, `Pipeline`, `Docker` - Install - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Credentials - System - Global credentials 
    - Add credentials - Kind: Username with password - Scope: Global - Username: tony - Password: Ir0nM@n - ID: app-server-1-ssh - Description: App Server 1 SSH - Create
    - Add credentials - Kind: Username with password - Scope: Global - Username: sarah - Password: Sarah_pass123 - ID: gitea-credentials - Description: Gitea Sarah - Create
- Manage Jenkins - Nodes - New Node - Node name: App Server 1 - Type: Permanent Agent - Number of executors: 1 - Remote root directory: /home/tony/jenkins - Labels: stapp01 - Usage: Only build jobs with label expressions matching this node - Launch method: Launch agents via SSH - Host: 172.16.238.10 - Credentials: tony/***** - Host Key Verification Strategy: Non verifying Verification Strategy - Save
- Manage Jenkins - Nodes - App Server 1 - Launch agent
- New item - Enter an item name: `nginx-container` - Select an item type: Pipeline - Pipeline - Definition: Pipeline script - Check Use Groovy Sandbox - Script (Save after Script):
```
pipeline {
    agent { label 'stapp01' }
    
    stages {
        stage('Build') {
            steps {
                script {
                    git branch: 'master',
                        credentialsId: 'gitea-credentials',
                        url: 'http://git.stratos.xfusioncorp.com/sarah/mr_job.git'
                    
                    sh '''
                        docker build -t stregi01.stratos.xfusioncorp.com:5000/nginx:latest .
                        docker push stregi01.stratos.xfusioncorp.com:5000/nginx:latest
                    '''
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    sh '''
                        docker rm -f nginx-app 2>/dev/null || true
                        docker run -d --name nginx-app -p 8080:80 stregi01.stratos.xfusioncorp.com:5000/nginx:latest
                    '''
                }
            }
        }
    }
}
```
  - Use Groovy Sandbox - Save
- nginx-container - Build Now
- Job built successfully

```bash
[tony@stapp01 ~]$ docker ps
CONTAINER ID   IMAGE                                                COMMAND                  CREATED              STATUS              PORTS                  NAMES
167080097ec9   stregi01.stratos.xfusioncorp.com:5000/nginx:latest   "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:8080->80/tcp   nginx-app

From the jump host, run:
thor@jumphost ~$ curl http://stapp01:8080
Welcome to xFusionCorp Industries!
```
✅

## 4.4 Jenkins Multistage Pipeline

The development team of xFusionCorp Industries is working on to develop a new static website and they are planning to deploy the same on Nautilus App Servers using Jenkins pipeline. They have shared their requirements with the DevOps team and accordingly we need to create a Jenkins pipeline job. Please find below more details about the task:  
  
Click on the `Jenkins` button on the top bar to access the Jenkins UI. Login using username `admin` and password `Adm!n321`.  

Similarly, click on the `Gitea` button on the top bar to access the Gitea UI. Login using username `sarah` and password `Sarah_pass123`.  

There is a repository named `sarah/web` in Gitea that is already cloned on `Storage server` under `/var/www/html` directory.  
  
1. Update the content of the file `index.html` under the same repository to `Welcome to xFusionCorp Industries` and push the changes to the origin into the `master` branch.  
2. Apache is already installed on all app Servers its running on port `8080`.  
3. Create a Jenkins pipeline job named `deploy-job` (it must not be a `Multibranch pipeline` job) and pipeline should have two stages `Deploy` and `Test` ( names are case sensitive ). Configure these stages as per details mentioned below.  
  - The `Deploy` stage should deploy the code from `web` repository under `/var/www/html` on the `Storage Server`, as this location is already mounted to the document root `/var/www/html` of all app servers.  
  - The `Test` stage should just test if the app is working fine and website is accessible. Its up to you how you design this stage to test it out, you can simply add a `curl` command as well to run a curl against the LBR URL (`http://stlb01:8091`) to see if the website is working or not. Make sure this stage fails in case the website/app is not working or if the `Deploy` stage fails.  

Click on the `App` button on the top bar to see the latest changes you deployed. Please make sure the required content is loading on the main URL `http://stlb01:8091` i.e there should not be a sub-directory like `http://stlb01:8091/web` etc.    

`Note:`  
1.  You might need to install some plugins and restart Jenkins service. So, we recommend clicking on `Restart Jenkins when installation is complete and no jobs are running` on plugin installation/update page i.e `update centre`. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case, please make sure to refresh the UI page.  
2.  For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

---

```bash
$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10
$ systemctl status httpd
● httpd.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; preset: disabled)
     Active: active (running) since Sat 2025-11-01 17:49:47 UTC; 27min ago
$ grep -iE '^\s*listen\b' /etc/httpd/conf/httpd.conf
Listen 8080

$ sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@172.16.238.15
$ mkdir /home/natasha/jenkins
$ cd /var/www/html/
$ ls -la
total 16
drwxr-xr-x 3 natasha natasha 4096 Nov  1 17:49 .
drwxr-xr-x 3 natasha natasha 4096 Nov  1 17:49 ..
drwxr-xr-x 8 natasha natasha 4096 Nov  1 17:49 .git
-rw-r--r-- 1 natasha natasha    8 Nov  1 17:49 index.html
$ git branch
* master
$ cat index.html 
Welcome
$ curl http://stlb01:8091
Welcome
$ echo 'Welcome to xFusionCorp Industries' > index.html 
$ cat index.html 
Welcome to xFusionCorp Industries
$ git add index.html 
$ git commit -m "Update welcome message"
$ git push
$ sudo su -
$ yum install java -y
$ java --version
openjdk 17.0.16 2025-07-15 LTS
OpenJDK Runtime Environment (Red_Hat-17.0.16.0.8-1) (build 17.0.16+8-LTS)
OpenJDK 64-Bit Server VM (Red_Hat-17.0.16.0.8-1) (build 17.0.16+8-LTS, mixed mode, sharing)
```

- Click on the Jenkins button and Login, username: admin - password: Adm!n321
- Manage Jenkins - Plugins - Updates - Check all - Update - Download progress - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Plugins - Available plugins - Check `SSH`, `SSH Credentials`, `SSH Build Agents`, `Git`, `Pipeline`, `Docker` - Install - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Credentials - System - Global credentials 
    - Add credentials - Kind: Username with password - Scope: Global - Username: natasha - Password: Bl@kW - ID: natasha-storage-server-credentials - Description: Natasha Storage Server - Create
    - Add credentials - Kind: Username with password - Scope: Global - Username: sarah - Password: Sarah_pass123 - ID: gitea-credentials - Description: Gitea Sarah - Create
- Manage Jenkins - Nodes - New Node - Node name: Storage Server - Type: Permanent Agent - Number of executors: 1 - Remote root directory: /home/natasha/jenkins - Labels: ststor01 - Usage: Only build jobs with label expressions matching this node - Launch method: Launch agents via SSH - Host: 172.16.238.15 - Credentials: natasha/***** - Host Key Verification Strategy: Non verifying Verification Strategy - Save
- Manage Jenkins - Nodes - Storage Server - Launch agent
- New item - Enter an item name: `deploy-job` - Select an item type: Pipeline - Pipeline - Definition: Pipeline script - Check Use Groovy Sandbox - Script (Save after Script):
```
pipeline {
    agent { label 'ststor01' }
    
    stages {
        stage('Deploy') {
            steps {
                script {
                    sh 'rm -rf /var/www/html/*'
                    
                    git branch: 'master',
                        credentialsId: 'gitea-credentials',
                        url: 'http://git.stratos.xfusioncorp.com/sarah/web.git'
                    
                    sh '''
                        cp -r * /var/www/html/                      
                        echo 'Deployment completed successfully'
                    '''
                }
            }
        }
        
        stage('Test') {
            steps {
                script {
                    sh '''
                        echo "Testing website accessibility..."
                        response=$(curl -s -w "\\n%{http_code}" http://stlb01:8091)
                        http_code=$(echo "$response" | tail -n1)
                        
                        if [ "$http_code" != "200" ]; then
                            echo "ERROR: HTTP status code is $http_code, expected 200"
                            echo "Website is not accessible through load balancer"
                            exit 1
                        fi
                        
                        echo "SUCCESS: Website is accessible with HTTP 200 status"
                        echo "Test stage completed successfully"
                    '''
                }
            }
        }
    }
}
```
  - Use Groovy Sandbox - Save
- deploy-job - Build Now
- Job built successfully

```bash
$ ls -l /var/www/html/
total 4
-rw-r--r-- 1 tony tony 34 Nov  1 18:31 index.html
$ curl http://stlb01:8091
Welcome to xFusionCorp Industries
```
✅

## 4.5 Jenkins Setup Node App

The Nautilus application development team is working on to develop a Node app. They are still in the development phase however they want to deploy and test their app on a containerized environment and using a Jenkins pipeline. Please find below more details to complete this task.

Click on the `Jenkins` button on the top bar to access the Jenkins UI. Login using username `admin` and password `Adm!n321`.  

Similarly, click on the `Gitea` button on the top bar to access the Gitea UI. Login using username `sarah` and password `Sarah_pass123`.  
  
There is a repository named `sarah/web` in Gitea, which is cloned on the `Storage` server under `/home/sarah/web` directory.  

- A `Dockerfile` is already present under the git repository, please push the same to the origin repo if not pushed already.  
  - Create a jenkins pipeline job named `node-app` and configure it as below:  
    - Configure it to deploy the app on `App Server 3`
    - The pipeline must have two stages `Build` and `Deploy` (names are case sensitive)  
    - In the `Build` stage, build an image named `stregi01.stratos.xfusioncorp.com:5000/node-app:latest` using the `Dockerfile` present under the Git repository. `stregi01.stratos.xfusioncorp.com:5000` is the image registry server. After building the image push the same to the image registry server.  
    - In the `Deploy` stage, create a container named `node-app` using the image you build it the `Build` stage. Make sure to map the container port with host port `8080`.  
              
`Note:`  
1.  You might need to install some plugins and restart Jenkins service. So, we recommend clicking on `Restart Jenkins when installation is complete and no jobs are running` on plugin installation/update page i.e `update centre`. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case, please make sure to refresh the UI page.  
2.  For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

---

```bash
$ sshpass -p 'Sarah_pass123' ssh -o StrictHostKeyChecking=no sarah@172.16.238.15
$ cd web/
$ ls -la
total 48
drwxr-xr-x  5 sarah sarah  4096 Nov  2 08:05 .
drwx------  3 sarah sarah  4096 Nov  2 08:05 ..
drwxr-xr-x  8 sarah sarah  4096 Nov  2 08:05 .git
-rw-r--r--  1 sarah sarah   240 Nov  2 08:05 Dockerfile
-rw-r--r--  1 sarah sarah   535 Nov  2 08:05 app.js
drwxr-xr-x 52 sarah sarah  4096 Nov  2 07:46 node_modules
-rw-r--r--  1 sarah sarah 14292 Nov  2 08:05 package-lock.json
-rw-r--r--  1 sarah sarah   300 Nov  2 08:05 package.json
drwxr-xr-x  3 sarah sarah  4096 Nov  2 07:46 views
$ git status
On branch master
Your branch is up to date with 'origin/master'.
nothing to commit, working tree clean

$ sshpass -p 'BigGr33n' ssh -o StrictHostKeyChecking=no banner@172.16.238.12
$ mkdir /home/banner/jenkins
$ sudo yum install java -y
$ java --version
openjdk 17.0.16 2025-07-15 LTS
OpenJDK Runtime Environment (Red_Hat-17.0.16.0.8-1) (build 17.0.16+8-LTS)
OpenJDK 64-Bit Server VM (Red_Hat-17.0.16.0.8-1) (build 17.0.16+8-LTS, mixed mode, sharing)
```

- Click on the Jenkins button and Login, username: admin - password: Adm!n321
- Manage Jenkins - Plugins - Updates - Check all - Update - Download progress - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Plugins - Available plugins - Check `SSH`, `SSH Credentials`, `SSH Build Agents`, `Git`, `Pipeline`, `Docker` - Install - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Credentials - System - Global credentials 
    - Add credentials - Kind: Username with password - Scope: Global - Username: banner - Password: BigGr33n - ID: banner-app-server3-credentials - Description: Banner App Server 3 - Create
    - Add credentials - Kind: Username with password - Scope: Global - Username: sarah - Password: Sarah_pass123 - ID: gitea-credentials - Description: Gitea Sarah - Create
- Manage Jenkins - Nodes - New Node - Node name: App Server 3 - Type: Permanent Agent - Number of executors: 1 - Remote root directory: /home/banner/jenkins - Labels: stapp03 - Usage: Only build jobs with label expressions matching this node - Launch method: Launch agents via SSH - Host: 172.16.238.12 - Credentials: banner/***** - Host Key Verification Strategy: Non verifying Verification Strategy - Save
- Manage Jenkins - Nodes - Storage Server - Launch agent
- New item - Enter an item name: `node-app` - Select an item type: Pipeline - Pipeline - Definition: Pipeline script - Check Use Groovy Sandbox - Script (Save after Script):
```bash
pipeline {
    agent { label 'stapp03' }
    
    stages {
        stage('Build') {
            steps {
                script {
                    git branch: 'master',
                        credentialsId: 'gitea-credentials',
                        url: 'http://git.stratos.xfusioncorp.com/sarah/web.git'
                    
                    sh '''
                        docker build -t stregi01.stratos.xfusioncorp.com:5000/node-app:latest .
                        docker push stregi01.stratos.xfusioncorp.com:5000/node-app:latest
                    '''
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    sh '''
                        docker rm -f node-app || true
                        docker run -d --name node-app -p 8080:8080 stregi01.stratos.xfusioncorp.com:5000/node-app:latest
                    '''
                }
            }
        }
    }
}
```
  - Use Groovy Sandbox - Save

- node-app - Build Now
- Job built successfully

```bash
$ docker images
REPOSITORY                                       TAG       IMAGE ID       CREATED          SIZE
stregi01.stratos.xfusioncorp.com:5000/node-app   latest    4b10a8f28a82   13 minutes ago   87.4MB

$ curl -X GET http://stregi01.stratos.xfusioncorp.com:5000/v2/_catalog
{"repositories":["node-app"]}
$ curl -X GET http://stregi01.stratos.xfusioncorp.com:5000/v2/node-app/tags/list
{"name":"node-app","tags":["latest"]}

$ docker ps
CONTAINER ID   IMAGE                                                   COMMAND                  CREATED          STATUS          PORTS                    NAMES
9d3fd29bc7e1   stregi01.stratos.xfusioncorp.com:5000/node-app:latest   "docker-entrypoint.s…"   13 minutes ago   Up 13 minutes   0.0.0.0:8080->8080/tcp   node-app
```
✅

<img width="1122" height="433" alt="jenkins4" src="https://github.com/user-attachments/assets/1d7c4f88-68aa-4e91-b948-d79b36fcb3cc" />

✅
