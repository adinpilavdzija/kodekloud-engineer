# Jenkins Level 1 (Tasks 1.1-1.5) 

## 1.1 Set Up Jenkins Server

The DevOps team at xFusionCorp Industries is initiating the setup of CI/CD pipelines and has decided to utilize Jenkins as their server. Execute the task according to the provided requirements:
1. Install `jenkins` on jenkins server using `yum` utility only, and start its `service`. You might face timeout issue while starting the Jenkins service, please refer [this](https://www.jenkins.io/doc/book/system-administration/systemd-services/#starting-services) link for help.
2. Jenkin's admin user name should be `theadmin`, password should be `Adm!n321`, full name should be `Siva` and email should be `siva@jenkins.stratos.xfusioncorp.com`.
`Note:`
1. For this task, access the Jenkins server by SSH using the `root` user and password `S3curePass` from the `jump host`.
2. After Jenkins server installation, click the `Jenkins` button on the top bar to access the Jenkins UI and follow on-screen instructions to create an admin user.

---

```bash
$ sshpass -p 'S3curePass' ssh -o StrictHostKeyChecking=no root@jenkins

$ yum update -y  
$ yum install epel-release java-17-openjdk wget -y
$ wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo --no-check-certificate
$ rpm --import http://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
$ yum update -y
$ yum install jenkins -y

$ systemctl start jenkins
$ systemctl status jenkins
● jenkins.service - Jenkins Continuous Integration Server
     Loaded: loaded (/usr/lib/systemd/system/j
enkins.service; disabled; preset: disabled)
     Active: active (running) since Thu 2025-06-12 17:49:05 UTC; 6s ago

$ cat /var/lib/jenkins/secrets/initialAdminPassword 
58c238e6b1c84a20a7d9381e4503ed80
```

Click the Jenkins button at the top right to open the Jenkins page in a new tab.
Enter the copied admin password.

Install suggested plugins. If the some of the plugins fail to install, hit retry.

Username: theadmin
Password: Adm!n321
Full name: Jim
Email: jim@jenkins.stratos.xfusioncorp.com

Jenkins URL: https://8080-port-e5b6euqyojhyadvq.labs.kodekloud.com/

Start using Jenkins

✅

## 1.2 Install Jenkins Plugins

The Nautilus DevOps team has recently setup a Jenkins server, which they want to use for some CI/CD jobs. Before that they want to install some plugins which will be used in most of the jobs. Please find below more details about the task
1. Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username `admin` and `Adm!n321` password.
2. Once logged in, install the `Git` and `GitLab` plugins. Note that you may need to restart Jenkins service to complete the plugins installation, If required, opt to `Restart Jenkins when installation is complete and no jobs are running` on plugin installation/update page i.e `update centre`.

`Note:`
1. After restarting the Jenkins service, wait for the Jenkins login page to reappear before proceeding.
2. For tasks involving web UI changes, capture screenshots to share for review or consider using screen recording software like loom.com for documentation and sharing.

---

- Click on the Jenkins button and Login, username: admin - password: Adm!n321
- Updates - Check all - Update - Click on Download Progress - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Plugins - Available plugins - Check Git (5.7.0) and GitLab (1.9.8) - Install
- Some plugins including GitLab failed - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Plugins - Available plugins - Check GitLab (1.9.8) - Install - Check `Restart Jenkins when installation is complete and no jobs are running`

![jenkins1 2-1](https://github.com/user-attachments/assets/d08b74d7-8710-43a4-b5a7-a3847275f146)
✅

## 1.3 Configure Jenkins User Access

The Nautilus team is integrating Jenkins into their CI/CD pipelines. After setting up a new Jenkins server, they're now configuring user access for the development team, Follow these steps:
1. Click on the `Jenkins` button on the top bar to access the Jenkins UI. Login with username `admin` and password `Adm!n321`.
2. Create a jenkins user named `mariyam` with the password `8FmzjvFU6S`. Their full name should match `Mariyam`.
3. Utilize the `Project-based Matrix Authorization Strategy` to assign `overall read` permission to the `mariyam` user.
4. Remove all permissions for `Anonymous` users (if any) ensuring that the `admin` user retains overall `Administer` permissions.
5. For the existing job, grant `mariyam` user only `read` permissions, disregarding other permissions such as Agent, SCM etc.

`Note:`
1. You may need to install plugins and restart Jenkins service. After plugins installation, select `Restart Jenkins when installation is complete and no jobs are running` on plugin installation/update page.
2. After restarting the Jenkins service, wait for the Jenkins login page to reappear before proceeding. Avoid clicking `Finish` immediately after restarting the service.
3. Capture screenshots of your configuration for review purposes. Consider using screen recording software like `loom.com` for documentation and sharing.

---

- Click on the Jenkins button and Login, username: admin - password: Adm!n321
- Dashboard - Manage Jenkins - Users - Create User - username: mariyam - password: Adm!8FmzjvFU6S - full name: Mariyam
- Manage Jenkins - Plugins - Updates - Check all - Update - Download progress - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Plugins - Available plugins - Check `Matrix Authorization StrategyVersion 3.2.6` - Install - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Security - Authorization - Choose `Project-based Matrix Authorization Strategy` - There are no given permissions for Anonymous so that is ok - Add User... - User ID: mariyam - Check permission `Overall - Read` and `Job - Read` for Mariyam - Save
- Log out - Log in as mariyam:8FmzjvFU6S - Check existing project to verify

![jenkins1 3-1](https://github.com/user-attachments/assets/3fb9b307-41b8-4904-8be3-98d73c4c874b)
![jenkins1 3-2](https://github.com/user-attachments/assets/04d4b270-9d36-48c3-88e0-3630299bff8c)

✅

## 1.4 Organize Jenkins Jobs with Folders

xFusionCorp Industries' DevOps team aims to streamline the management of Jenkins jobs by organizing them into distinct folders based on their purpose. Complete the task following the provided requirements:
1. Access the Jenkins UI by clicking on the `Jenkins` button in the top bar. Log in using the credentials: username `admin` and password `Adm!n321`.
2. Create a new folder named `Apache` within the Jenkins UI.
3. Move the existing jobs `httpd-php` and `services` under the newly created `Apache` folder.

`Note:`
1. Ensure to install any required plugins and restart the Jenkins service if necessary. Opt for `Restart Jenkins when installation is complete and no jobs are running` on the plugin installation/update page.
2. Be aware that Jenkins UI may experience temporary unresponsiveness during the service restart. Refresh the UI page if needed.
3. Capture screenshots of your work for documentation and review purposes. Alternatively, utilize screen recording software like loom.com for detailed documentation and sharing.

---

- Click on the Jenkins button and Login, username: admin - password: Adm!n321
- Manage Jenkins - Plugins - Updates - Check all - Update - Download progress - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Plugins - Available plugins - Check `Folders 6.1023.v4fcb_72152519` - Install - Check `Restart Jenkins when installation is complete and no jobs are running`
- New Item - Enter an item name: Apache - Select an item type: Folder - OK - Save
- Click on `httpd-php` - Move - Choose `Jenkins >> Apache` - Move - Repeat the same for `services`

![jenkins1 4-1](https://github.com/user-attachments/assets/a5e6c882-5608-4835-81c7-b0cc5bc33777)

✅

## 1.5 Configure Jenkins Job for Package Installation

Some new requirements have come up to install and configure some packages on the Nautilus infrastructure under Stratos Datacenter. The Nautilus DevOps team installed and configured a new Jenkins server so they wanted to create a Jenkins job to automate this task. Find below more details and complete the task accordingly:
1. Access the Jenkins UI by clicking on the `Jenkins` button in the top bar. Log in using the credentials: username `admin` and password `Adm!n321`.
2. Create a new Jenkins job named `install-packages` and configure it with the following specifications:
- Add a string parameter named `PACKAGE`.
- Configure the job to install a package specified in the `$PACKAGE` parameter on the `storage server` within the `Stratos Datacenter`.

`Note`:
1. Ensure to install any required plugins and restart the Jenkins service if necessary. Opt for `Restart Jenkins when installation is complete and no jobs are running` on the plugin installation/update page. Refresh the UI page if needed after restarting the service.
2. Verify that the Jenkins job runs successfully on repeated executions to ensure reliability.
3. Capture screenshots of your configuration for documentation and review purposes. Alternatively, use screen recording software like `loom.com` for comprehensive documentation and sharing.

---

- Click on the Jenkins button and Login, username: admin - password: Adm!n321
- Manage Jenkins - Plugins - Updates - Check all - Update - Download progress - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Plugins - Available plugins - Check `SSH 158.ve2a_e90fb_7319`, `SSH Credentials 355.v9b_e5b_cde5003`, `SSH Build Agents 3.1031.v72c6b_883b_869` - Install - Check `Restart Jenkins when installation is complete and no jobs are running`
- Manage Jenkins - Credentials - System - Global credentials - Add credentials - Kind: Username with password - Scope: Global - Username: natasha - Password: Bl@kW - ID: storage - Create
- Manage Jenkins - System - SSH remote hosts - Add - Hostname: ststor01.stratos.xfusioncorp.com - Port: 22 - Credentials: natasha - Check connection - (if Successfull connection) Save
- Start building your software project: Create a job - Enter an item name: install-packages - Select an item type: Freestyle project - OK
- install-packages - Configure - General - This project is parameterized? - String Parameter - Name: PACKAGE - Default Value: package_install_name - Save
- install-packages - Configure - Build Steps - Execute shell script on remote host using ssh - command: `echo 'Bl@kW' | sudo -S yum install -y $PACKAGE` - Save
- install-packages - Build with parameters - PACKAGE: httpd - Build

$ sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@172.16.238.15
$ httpd -v
Server version: Apache/2.4.62 (CentOS Stream)
Server built:   Jan 29 2025 00:00:00

![jenkins1 5-1](https://github.com/user-attachments/assets/abe5118c-6ee9-4220-9c76-059c5451495e)

✅

![jenkins1](https://github.com/user-attachments/assets/4332be31-c982-423b-b022-56e2c8566652)
