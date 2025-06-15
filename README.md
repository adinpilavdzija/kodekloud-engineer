<h1 align="center">KodeKloud Engineer</h1>

<p align="center">
  <img name="KodeKloud Engineer Logo" src="https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/12dc8758-f485-4471-83a1-75dadde8ed6c" />
</p>

KodeKloud provides online hands-on trainings on trending Cloud and DevOps technologies like Docker, Kubernetes, OpenShift, Ansible, Puppet, Chef, Linux and more.

This repository contains solutions for KodeKloud Labs.

> For the server credentials, check out the [Project Nautilus documentation](https://kodekloudhub.github.io/kodekloud-engineer/docs/projects/nautilus).  
> Most tasks include step of logging into a designated server (one of the servers listed below or other):
> - App Server 1: `$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10`
> - App Server 2: `$ sshpass -p 'Am3ric@' ssh -o StrictHostKeyChecking=no steve@172.16.238.11`
> - App Server 3: `$ sshpass -p 'BigGr33n' ssh -o StrictHostKeyChecking=no banner@172.16.238.12`

<details>
<summary>Infrastructure Details</summary>

| Server Name | IP            | Hostname                          | User    | Password   | Purpose                        |
| ----------- | ------------- | --------------------------------- | ------- | ---------- | ------------------------------ |
| stapp01     | 172.16.238.10 | stapp01.stratos.xfusioncorp.com   | tony    | Ir0nM@n    | Nautilus App 1                 |
| stapp02     | 172.16.238.11 | stapp02.stratos.xfusioncorp.com   | steve   | Am3ric@    | Nautilus App 2                 |
| stapp03     | 172.16.238.12 | stapp03.stratos.xfusioncorp.com   | banner  | BigGr33n   | Nautilus App 3                 |
| stlb01      | 172.16.238.14 | stlb01.stratos.xfusioncorp.com    | loki    | Mischi3f   | Nautilus HTTP LBR              |
| stdb01      | 172.16.239.10 | stdb01.stratos.xfusioncorp.com    | peter   | Sp!dy      | Nautilus DB Server             |
| ststor01    | 172.16.238.15 | ststor01.stratos.xfusioncorp.com  | natasha | Bl@kW      | Nautilus Storage Server        |
| stbkp01     | 172.16.238.16 | stbkp01.stratos.xfusioncorp.com   | clint   | H@wk3y3    | Nautilus Backup Server         |
| stmail01    | 172.16.238.17 | stmail01.stratos.xfusioncorp.com  | groot   | Gr00T123   | Nautilus Mail Server           |
| jump_host   | Dynamic       | jump_host.stratos.xfusioncorp.com | thor    | mjolnir123 | Jump Server to Access Stork DC |
| jenkins     | 172.16.238.19 | jenkins.stratos.xfusioncorp.com   | jenkins | j@rv!s     | Jenkins Server for CI/CD       |
</details>

## Docker 

<p align="center">
  <img width=400 name="Docker Logo" src="https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/4e6fd02b-0755-43df-8a9d-915c12394480"/>
</p>

- [Docker Level 1 (Tasks 1.1-1.5)](./docker_level_1.md)
  - [1.1 Install Docker Package](./docker_level_1.md#11-install-docker-package)
  - [1.2 Run a Docker Container](./docker_level_1.md#12-run-a-docker-container)
  - [1.3 Docker Delete Container](./docker_level_1.md#13-docker-delete-container)
  - [1.4 Docker Copy Operations](./docker_level_1.md#14-docker-copy-operations)
  - [1.5 Docker Container Issue](./docker_level_1.md#15-docker-container-issue)
- [Docker Level 2 (Tasks 2.1-2.5)](./docker_level_2.md)
  - [2.1 Pull Docker Image](./docker_level_2.md#21-pull-docker-image)
  - [2.2 Docker Update Permissions](./docker_level_2.md#22-docker-update-permissions)
  - [2.3 Create a Docker Image From Container](./docker_level_2.md#23-create-a-docker-image-from-container)
  - [2.4 Docker EXEC Operations](./docker_level_2.md#24-docker-exec-operations)
  - [2.5 Write a Docker File](./docker_level_2.md#25-write-a-docker-file)
- [Docker Level 3 (Tasks 3.1-3.5)](./docker_level_3.md)
  - [3.1 Create a Docker Network](./docker_level_3.md#31-create-a-docker-network)
  - [3.2 Docker Volumes Mapping](./docker_level_3.md#32-docker-volumes-mapping)
  - [3.3 Docker Ports Mapping](./docker_level_3.md#33-docker-ports-mapping)
  - [3.4 Save, Load and Transfer Docker Image](./docker_level_3.md#34-save-load-and-transfer-docker-image)
  - [3.5 Write a Docker Compose File](./docker_level_3.md#35-write-a-docker-compose-file)
- [Docker Level 4 (Tasks 4.1-4.5)](./docker_level_4.md)
  - [4.1 Resolve Dockerfile Issues](./docker_level_4.md#41-resolve-dockerfile-issues)
  - [4.2 Resolve Docker Compose Issues](./docker_level_4.md#42-resolve-docker-compose-issues)
  - [4.3 Deploy an App on Docker Containers](./docker_level_4.md#43-deploy-an-app-on-docker-containers)
  - [4.4 Docker Node App](./docker_level_4.md#44-docker-node-app)
  - [4.5 Docker Python Apps](./docker_level_4.md#45-docker-python-app)

## Linux

<p align="center">
  <img width=400 name="linux-logo" src="https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/62722f74-ab95-4c26-846e-a662b5f9359a"/>
</p>

- [Linux Level 1 (Tasks 1.1-1.19)](./linux_level_1.md)
  - [1.1 Create a user](./linux_level_1.md#11-create-a-user)
  - [1.2 Create a group](./linux_level_1.md#12-create-a-group)
  - [1.3 Create a Linux User with non-interactive shell](./linux_level_1.md#13-create-a-linux-user-with-non-interactive-shell)
  - [1.4 Linux User Without Home](./linux_level_1.md#14-linux-user-without-home)
  - [1.5 Linux User Expiry](./linux_level_1.md#15-linux-user-expiry)
  - [1.6 Linux User Files](./linux_level_1.md#16-linux-user-files)
  - [1.7 Disable Root Login](./linux_level_1.md#17-disable-root-login)
  - [1.8 Linux Archives](./linux_level_1.md#18-linux-archives)
  - [1.9 Linux File Permissions](./linux_level_1.md#19-linux-file-permissions)
  - [1.10 Linux Access Control List](./linux_level_1.md#110-linux-access-control-list)
  - [1.11 Linux String Substitute](./linux_level_1.md#111-linux-string-substitute)
  - [1.12 Linux Remote Copy](./linux_level_1.md#112-linux-remote-copy)
  - [1.13 Cron schedule deny to users](./linux_level_1.md#113-cron-schedule-deny-to-users)
  - [1.14 Linux Run Levels](./linux_level_1.md#114-linux-run-levels)
  - [1.15 Linux Time Zones Setting](./linux_level_1.md#115-linux-time-zones-setting)
  - [1.16 Linux NTP Setup](./linux_level_1.md#116-linux-ntp-setup)
  - [1.17 Linux Firewalld Rules](./linux_level_1.md#117-linux-firewalld-rules)
  - [1.18 Linux Resource Limits](./linux_level_1.md#118-linux-resource-limits)
  - [1.19 Selinux Installation](./linux_level_1.md#119-selinux-installation)
- [Linux Level 2 (Tasks 2.1-1.24)](./linux_level_2.md)
  - [2.1 Create a Cron Job](./linux_level_2.md#21-create-a-cron-job)
  - [2.2 Linux Banner](./linux_level_2.md#22-linux-banner)
  - [2.3 Linux Collaborative Directories](./linux_level_2.md#23-linux-collaborative-directories)
  - [2.4 Linux String Substitute (sed)](./linux_level_2.md#24-linux-string-substitute-sed)
  - [2.5 Linux SSH Authentication](./linux_level_2.md#25-linux-ssh-authentication)
  - [2.6 Linux Find Command](./linux_level_2.md#26-linux-find-command)
  - [2.7 Install a package](./linux_level_2.md#27-install-a-package)
  - [2.8 Install Ansible](./linux_level_2.md#28-install-ansible)
  - [2.9 Configure Local Yum repos](./linux_level_2.md#29-configure-local-yum-repos)
  - [2.10 Linux Services](./linux_level_2.md#210-linux-services)
  - [2.11 Linux Configure sudo](./linux_level_2.md#211-linux-configure-sudo)
  - [2.12 DNS Troubleshooting](./linux_level_2.md#212-dns-troubleshooting)
  - [2.13 Linux Firewalld Setup](./linux_level_2.md#213-linux-firewalld-setup)
  - [2.14 Linux Postfix Mail Server](./linux_level_2.md#214-linux-postfix-mail-server)
  - [2.15 Linux Postfix Troubleshooting](./linux_level_2.md#215-linux-postfix-troubleshooting)
  - [2.16 Install and Configure Ha Proxy LBR](./linux_level_2.md#216-install-and-configure-ha-proxy-lbr)
  - [2.17 Haproxy LBR Troubleshooting](./linux_level_2.md#217-haproxy-lbr-troubleshooting)
  - [2.18 Maria DB Troubleshooting](./linux_level_2.md#218-maria-db-troubleshooting)
  - [2.19 Linux Bash Scripts](./linux_level_2.md#219-linux-bash-scripts)
  - [2.20 Add Response Headers in Apache](./linux_level_2.md#220-add-response-headers-in-apache)
  - [2.21 Apache Troubleshooting](./linux_level_2.md#221-apache-troubleshooting)
  - [2.22 Linux GPG Encryption](./linux_level_2.md#222-linux-gpg-encryption)
  - [2.23 Linux Log Rotate](./linux_level_2.md#223-linux-log-rotate)
  - [2.24 Application Security](./linux_level_2.md#224-application-security)

## Kubernetes 

<p align="center">
  <img width=400 name="kubernetes-logo" src="https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/f03157dc-87be-456e-b06e-877f7a4cdc78"/>
</p>

- [Kubernetes Level 1 (Tasks 1.1-1.14)](./k8s_level_1.md)
  - [1.1 Create Pods in Kubernetes Cluster](./k8s_level_1.md#11-create-pods-in-kubernetes-cluster)
  - [1.2 Create Deployments in Kubernetes Cluster](./k8s_level_1.md#12-create-deployments-in-kubernetes-cluster)
  - [1.3 Create Namespaces in Kubernetes Cluster](./k8s_level_1.md#13-create-namespaces-in-kubernetes-cluster)
  - [1.4 Set Limits for Resources in Kubernetes](./k8s_level_1.md#14-set-limits-for-resources-in-kubernetes)
  - [1.5 Rolling Updates in Kubernetes](./k8s_level_1.md#15-rolling-updates-in-kubernetes)
  - [1.6 Rollback a Deployment in Kubernetes](./k8s_level_1.md#16-rollback-a-deployment-in-kubernetes)
  - [1.7 Create Replicaset in Kubernetes Cluster](./k8s_level_1.md#17-create-replicaset-in-kubernetes-cluster)
  - [1.8 Create Cronjobs in Kubernetes](./k8s_level_1.md#18-create-cronjobs-in-kubernetes)
  - [1.9 Countdown job in Kubernetes](./k8s_level_1.md#19-countdown-job-in-kubernetes)
  - [1.10 Kubernetes Time Check Pod](./k8s_level_1.md#110-kubernetes-time-check-pod)
  - [1.11 Troubleshoot Issue With Pods](./k8s_level_1.md#111-troubleshoot-issue-with-pods)
  - [1.12 Update an Existing Deployment in Kubernetes](./k8s_level_1.md#112-update-an-existing-deployment-in-kubernetes)
  - [1.13 Replication Controller in Kubernetes](./k8s_level_1.md#113-replication-controller-in-kubernetes)
  - [1.14 Fix Issue with Volume Mounts in Kubernetes](./k8s_level_1.md#114-fix-issue-with-volume-mounts-in-kubernetes)

- [Kubernetes Level 2 (Tasks 2.1-2.11)](./k8s_level_2.md)
  - [2.1 Kubernetes Shared Volumes](./k8s_level_2.md#21-kubernetes-shared-volumes)
  - [2.2 Kubernetes Sidecar Containers](./k8s_level_2.md#22-kubernetes-sidecar-containers)
  - [2.3 Deploy Nginx Web Server on Kubernetes Cluster](./k8s_level_2.md#23-deploy-nginx-web-server-on-kubernetes-cluster)
  - [2.4 Print Environment Variables](./k8s_level_2.md#24-print-environment-variables)
  - [2.5 Rolling Updates And Rolling Back Deployments in Kubernetes](./k8s_level_2.md#25-rolling-updates-and-rolling-back-deployments-in-kubernetes)
  - [2.6 Deploy Jenkins on Kubernetes](./k8s_level_2.md#26-deploy-jenkins-on-kubernetes)
  - [2.7 Deploy Grafana on Kubernetes Cluster](./k8s_level_2.md)
  - [2.8 Deploy Tomcat App on Kubernetes](./k8s_level_2.md#27-deploy-grafana-on-kubernetes-cluster)
  - [2.9 Deploy Node App on Kubernetes](./k8s_level_2.md#28-deploy-tomcat-app-on-kubernetes)
  - [2.10 Troubleshoot Deployment issues in Kubernetes](./k8s_level_2.md#210-troubleshoot-deployment-issues-in-kubernetes)
  - [2.11 Fix issue with LAMP Environment in Kubernetes](./k8s_level_2.md#211-fix-issue-with-lamp-environment-in-kubernetes)

- [Kubernetes Level 3 (Tasks 3.1-3.10)](./k8s_level_3.md)
  - [3.1 Deploy Apache Web Server on Kubernetes Cluster](./k8s_level_3.md#31-deploy-apache-web-server-on-kubernetes-cluster)
  - [3.2 Deploy Lamp Stack on Kubernetes Cluster](./k8s_level_3.md#32-deploy-lamp-stack-on-kubernetes-cluster)
  - [3.3 Init Containers in Kubernetes](./k8s_level_3.md#33-init-containers-in-kubernetes)
  - [3.4 Persistent Volumes in Kubernetes](./k8s_level_3.md#34-persistent-volumes-in-kubernetes)
  - [3.5 Manage Secrets in Kubernetes](./k8s_level_3.md#35-manage-secrets-in-kubernetes)
  - [3.6 Environment Variables in Kubernetes](./k8s_level_3.md#36-environment-variables-in-kubernetes)
  - [3.7 Kubernetes LEMP Setup](./k8s_level_3.md#37-kubernetes-lemp-setup)
  - [3.8 Kubernetes Troubleshooting](./k8s_level_3.md#38-kubernetes-troubleshooting)
  - [3.9 Deploy Iron Gallery App on Kubernetes](./k8s_level_3.md#39-deploy-iron-gallery-app-on-kubernetes)
  - [3.10 Fix Python App Deployed on Kubernetes Cluster](./k8s_level_3.md#310-fix-python-app-deployed-on-kubernetes-cluster)

- [Kubernetes Level 4 (Tasks 4.1-4.5)](./k8s_level_4.md)
  - [4.1 Deploy Redis Deployment on Kubernetes](./k8s_level_4.md#41-deploy-redis-deployment-on-kubernetes)
  - [4.2 Deploy My SQL on Kubernetes](./k8s_level_4.md#42-deploy-my-sql-on-kubernetes)
  - [4.3 Kubernetes Nginx and Php FPM Setup](./k8s_level_4.md#43-kubernetes-nginx-and-php-fpm-setup)
  - [4.4 Deploy Drupal App on Kubernetes](./k8s_level_4.md#44-deploy-drupal-app-on-kubernetes)
  - [4.5 Deploy Guest Book App on Kubernetes](./k8s_level_4.md#45-deploy-guest-book-app-on-kubernetes)
 
## Git 

<p align="center">
  <img width=400 name="git-logo" src="https://github.com/user-attachments/assets/884c52b8-fa4e-4f8e-8284-84e1e28d5396"/>
</p>

- [Git Level 1 (Tasks 1.1-1.5)](./git_level_1.md)
  - [1.1 Set Up Git Repository on Storage Server](./git-level-1.md#11-set-up-git-repository-on-storage-server)
  - [1.2 Clone Git Repository on Storage Server](./git-level-1.md#12-clone-git-repository-on-storage-server)
  - [1.3 Fork a Git Repository](./git-level-1.md#13-fork-a-git-repository)
  - [1.4 Update Git Repository with Sample HTML File](./git-level-1.md#14-update-git-repository-with-sample-html-file)
  - [1.5 Delete Git Branch](./git-level-1.md#15-delete-git-branch)

## Jenkins

<p align="center">
  <img width=400 name="jenkins-logo" src="https://github.com/user-attachments/assets/637d8b20-87c0-4825-9f4e-a93b521138bd"/>
</p>

- [Jenkins Level 1 (Tasks 1.1-1.5)](./jenkins_level_1.md)
  - [1.1 Set Up Jenkins Server](./jenkins_level_1.md#11-set-up-jenkins-server)
  - [1.2 Install Jenkins Plugins](./jenkins_level_1.md#12-install-jenkins-plugins)
  - [1.3 Configure Jenkins User Access](./jenkins_level_1.md#13-configure-jenkins-user-access)
  - [1.4 Organize Jenkins Jobs with Folders](./jenkins_level_1.md#14-organize-jenkins-jobs-with-folders)
  - [1.5 Configure Jenkins Job for Package Installation](./jenkins_level_1.md#15-configure-jenkins-job-for-package-installation)
