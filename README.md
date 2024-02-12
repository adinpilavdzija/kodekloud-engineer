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
