# Docker 

<p align="center">
  <img name="Docker Logo" src="https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/7f61bc69-fa42-4ebc-aa5e-47915b3d95ea"/>
</p>

For the server credentials, check out the [Project Nautilus documentation](https://kodekloudhub.github.io/kodekloud-engineer/docs/projects/nautilus).

Each task includes step of logging into a designated server (one of the servers listed below):
- App Server 1: `$ sshpass -p 'Ir0nM@n' ssh -o StrictHostKeyChecking=no tony@172.16.238.10`
- App Server 2: `$ sshpass -p 'Am3ric@' ssh -o StrictHostKeyChecking=no steve@172.16.238.11`
- App Server 3: `$ sshpass -p 'BigGr33n' ssh -o StrictHostKeyChecking=no banner@172.16.238.12`

## Level 1 (Tasks 1.1-1.5) 

<details>
<summary>Level 1</summary>

### 1.1 Install Docker Package

Last week the Nautilus DevOps team met with the application development team and decided to containerize several of their applications. The DevOps team wants to do some testing per the following:
1. Install `docker-ce` and `docker-compose` packages on `App Server 2`.
2. Start `docker` service.

---

Update `yum` and install `yum-utils`. Download and install `docker-ce`:
```bash
sudo yum update
yum install yum-utils
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo  
yum install -y docker-ce docker-ce-cli containerd.io
```

Download the docker-compose binary and make it executable:
```bash
curl -SL https://github.com/docker/compose/releases/download/v2.23.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose 
```

Verify the successful installation and check the Docker version:
```bash
$ rpm -qa |grep docker 
docker-ce-cli-24.0.7-1.el8.x86_64
docker-ce-24.0.7-1.el8.x86_64
docker-compose-plugin-2.21.0-1.el8.x86_64
docker-buildx-plugin-0.11.2-1.el8.x86_64
docker-ce-rootless-extras-24.0.7-1.el8.x86_64

$ ls -la  /usr/local/bin/docker-compose
-rwxr-xr-x 1 root root 12212176 Nov 15 09:00 /usr/local/bin/docker-compose

$ docker --version
Docker version 24.0.7, build afdd53b

$ docker-compose --version
Docker Compose version v2.23.0
```

Enable and start the service:
```bash
systemctl enable docker  
systemctl start docker
```
```bash
$ systemctl status docker
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; vendor preset: disabled)
   Active: active (running) since Wed 2023-11-15 09:10:19 UTC; 7s ago
```
✅

### 1.2 Run a Docker Container

Nautilus DevOps team is testing some applications deployment on some of the application servers. They need to deploy a nginx container on `Application Server 3`. Please complete the task as per details given below:
- On `Application Server 3` create a container named `nginx_3` using image `nginx` with `alpine` tag and make sure container is in `running` state.

---

Create the container:
```bash
docker run -d --name nginx_3 nginx:alpine
```

Verify that the container is running:
```bash
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
0a1a72385a63        nginx:alpine        "/docker-entrypoint.…"   11 seconds ago      Up 9 seconds        80/tcp              nginx_3
```
✅

### 1.3 Docker Delete Container

One of the Nautilus project developers created a container on `App Server 2`. This container was created for testing only and now we need to delete it. Accomplish this task as per details given below:  
- Delete a container named `kke-container`, its running on `App Server 2` in Stratos DC.

---

Check the `running containers`:
```bash
$ docker ps
CONTAINER ID        IMAGE               COMMAND               CREATED             STATUS              PORTS               NAMES
3de0a0c8ff42        busybox             "tail -f /dev/null"   2 minutes ago       Up 2 minutes                           kke-container
```

`Stop` the container, then `delete` it and `verify` at the end:
```bash
$ docker stop kke-container
kke-container
$ docker rm kke-container
kke-container
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

```
✅

### 1.4 Docker Copy Operations

The Nautilus DevOps team has some confidential data present on `App Server 2` in `Stratos Datacenter`. There is a container `ubuntu_latest` running on the same server. We received a request to copy some of the data from the docker host to the container. Below are more details about the task:
- On `App Server 2` in `Stratos Datacenter` copy an encrypted file `/tmp/nautilus.txt.gpg` from docker host to `ubuntu_latest` container (running on same server) in `/home/` location. Please do not try to modify this file in any way.

---

Check the running containers:
```bash
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
0f4f044a7830        ubuntu              "/bin/bash"         10 minutes ago      Up 10 minutes                           ubuntu_latest
```

Copy the specified file from docker host to the container's `/home/` directory:
```bash
docker cp /tmp/nautilus.txt.gpg ubuntu_latest:/home
```

Verify that the file is copied:
```bash
$ docker exec ubuntu_latest ls -l /home
total 4
-rw-r--r-- 1 root root 105 Nov 16 07:35 nautilus.txt.gpg
```
✅

### 1.5 Docker Container Issue

There is a static website running within a container named `nautilus`, this container is running on `App Server 1`. Suddenly, we started facing some issues with the static website on `App Server 1`. Look into the issue to fix the same, you can find more details below:
1. Container's volume `/usr/local/apache2/htdocs` is mapped with the host's volume `/var/www/html`.
2. The website should run on host port `8080` on `App Server 1` i.e command `curl http://localhost:8080/` should work on `App Server 1`.

---

Check all containers:
```bash
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS                     PORTS               NAMES
5ff7a4ece89a        httpd               "httpd-foreground"   2 minutes ago       Exited (0) 2 minutes ago                       nautilus
```

Check the logs for `nautilus` container:
```bash
$ docker logs nautilus
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.18.0.2. Set the 'ServerName' directive globally to suppress this message
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.18.0.2. Set the 'ServerName' directive globally to suppress this message
[Fri Nov 17 08:33:53.157385 2023] [mpm_event:notice] [pid 1:tid 140022114920320] AH00489: Apache/2.4.58 (Unix) configured -- resuming normal operations
[Fri Nov 17 08:33:53.157520 2023] [core:notice] [pid 1:tid 140022114920320] AH00094: Command line: 'httpd -D FOREGROUND'
[Fri Nov 17 08:33:53.287935 2023] [mpm_event:notice] [pid 1:tid 140022114920320] AH00492: caught SIGWINCH, shutting down gracefully
```

Delete the container:
```bash
docker rm nautilus
```

Run the new container with the exposed ports and correct volume mapping, then verify it's running:
```bash
$ docker run -d -p 8080:80 -v /var/www/html:/usr/local/apache2/htdocs --name nautilus httpd:latest
0f0bb1e24e29c961a1790ca3eb0ee9a257b7a07d45dc4d9d0075dee0e352a3e6
$ docker ps
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                  NAMES
0f0bb1e24e29        httpd:latest        "httpd-foreground"   12 seconds ago      Up 10 seconds       0.0.0.0:8080->80/tcp   nautilus
$ curl http://localhost:8080/
Welcome to xFusionCorp Industries![root@stapp01 html]#
```
✅

![Level 1 completed](https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/c7b8830d-8913-4397-93fd-efd7d00b5b6b)
</details>

## Level 2 (Tasks 2.1-2.5)

<details>
<summary>Level 2</summary>

### 2.1 Pull Docker Image

Nautilus project developers are planning to start testing on a new project. As per their meeting with the DevOps team, they want to test containerized environment application features. As per details shared with DevOps team, we need to accomplish the following task:
- Pull `busybox:musl` image on `App Server 2` in Stratos DC and re-tag (create new tag) this image as `busybox:media`.

---

Pull `busybox:musl` image:
```bash
$ docker pull busybox:musl
musl: Pulling from library/busybox
d2dce026a06e: Pull complete 
Digest: sha256:f553b7484625f0c73bfa3888e013e70e99ec6ae1c424ee0e8a85052bd135a28a
Status: Downloaded newer image for busybox:musl
docker.io/library/busybox:musl
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
busybox             musl                46425a867adf        4 months ago        1.41MB
```

Re-tag the image and verify:
```bash
$ docker tag busybox:musl busybox:media

$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
busybox             media               46425a867adf        4 months ago        1.41MB
busybox             musl                46425a867adf        4 months ago        1.41MB
```
✅

### 2.2 Docker Update Permissions

One of the Nautilus project developers need access to run docker commands on `App Server 2`. This user is already created on the server. Accomplish this task as per details given below:
- User `kirsty` is not able to run docker commands on `App Server 2` in Stratos DC, make the required changes so that this user can run docker commands without `sudo`.

---

Verify the existence of the docker group and identify the users enlisted as its members:
```bash
$ sudo getent group docker
docker:x:995:steve
```

Add user `kirsty` to the group `docker`: 
```bash
sudo usermod -a -G docker kirsty
```

> [!TIP]
> The `-a` flag tells usermod to add a user to a group. The `-G` flag specifies the name of the secondary group to which you want to add the user.

Verify that user is added to the group:
```bash
$ sudo getent group docker
docker:x:995:steve,kirsty
```

Switch to `kirsty` and verify that user can run docker commands without `sudo`:
```bash
$ su kirsty
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
```
✅

### 2.3 Create a Docker Image From Container

One of the Nautilus developer was working to test new changes on a container. He wants to keep a backup of his changes to the container. A new request has been raised for the DevOps team to create a new image from this container. Below are more details about it:
- Create an image `cluster:devops` on `Application Server 3` from a container `ubuntu_latest` that is running on same server.

---

Check the running containers:
```bash
[banner@stapp03 ~]$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
8f80ea74f6bc        ubuntu              "/bin/bash"         5 minutes ago       Up 5 minutes                            ubuntu_latest
```

Create a new Docker image named `cluster` with tag `devops` from a running container `ubuntu_latest`:
```bash
docker commit -a "Adin" -m "Createn an image from a running container" ubuntu_latest cluster:devops
```

> [!TIP]
> It's considered best practice to use the `-a` flag to sign the image with an author and include a commit message using the `-m` flag.

Verify:
```bash
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
cluster             devops              560bac620394        21 seconds ago      124MB
ubuntu              latest              e4c58958181a        6 weeks ago         77.8MB
```
✅

### 2.4 Docker EXEC Operations

One of the Nautilus DevOps team members was working to configure services on a `kkloud` container that is running on `App Server 1` in `Stratos Datacenter`. Due to some personal work he is on PTO for the rest of the week, but we need to finish his pending work ASAP. Please complete the remaining work as per details given below:
1. Install `apache2` in `kkloud` container using `apt` that is running on `App Server 1` in `Stratos Datacenter`.
2. Configure Apache to listen on port `8084` instead of default `http` port. Do not bind it to listen on specific IP or hostname only, i.e it should listen on localhost, 127.0.0.1, container ip, etc.
3. Make sure Apache service is up and running inside the container. Keep the container in running state at the end.

---

Check the running containers:
```bash
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
07953415c8c5        ubuntu:18.04        "/bin/bash"         2 minutes ago       Up 2 minutes                            kkloud
```

Enter into container's shell:
```bash
docker exec -it kkloud sh
```

Inside the container, install apache2 and configure required settings:
```bash
apt install -y apache2
cd etc/apache2 && ls
apt-get -y install nano
nano ports.conf
```

Edit `ports.conf`: `Listen 80` -> `Listen 8084`.

```bash
$ cd sites-enabled && ls -l
total 0
lrwxrwxrwx 1 root root 35 Nov 20 19:07 000-default.conf -> ../sites-available/000-default.conf
$ nano 000-default.conf
```

Edit `000-default.conf`: `<VirtualHost *:80>` -> `<VirtualHost *:8084>`.

```bash
echo "ServerName 127.0.0.1" >> apache2.conf
```

Still inside the container, start the service and verify it's running:
```bash
$ service apache2 start
$ service apache2 status 
 * apache2 is running
```
✅

### 2.5 Write a Docker File

As per recent requirements shared by the Nautilus application development team, they need custom images created for one of their projects. Several of the initial testing requirements are already been shared with DevOps team. Therefore, create a docker file `/opt/docker/Dockerfile` (please keep `D` capital of Dockerfile) on `App server 2` in `Stratos DC` and configure to build an image with the following requirements:
1. Use `ubuntu` as the base image.
2. Install `apache2` and configure it to work on `5004` port. (do not update any other Apache configuration settings like document root etc).

---

Create `Dockerfile` in `/opt/docker/`:
```bash
sudo su -
vi /opt/docker/Dockerfile
```

Dockerfile:
```Dockerfile
#Use ubuntu as the base image
FROM ubuntu

#Install apache2 and configure it to work on port 5004
RUN apt-get update && apt-get install -y apache2
RUN echo "Listen 5004" >> /etc/apache2/ports.conf
RUN sed -i 's/Listen 80/Listen 5004/g' /etc/apache2/sites-available/000-default.conf
EXPOSE 5004
CMD ["apachectl", "-D", "FOREGROUND"]
```

Build the image and verify:
```bash
$ docker build -t apache2-image /opt/docker/

$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
apache2-image       latest              1acd70f14d63        About a minute ago   232MB
ubuntu              latest              e4c58958181a        6 weeks ago          77.8MB
```

Run the container and verify:
```bash
$ docker run -d -p 5004:5004 --name apache2-container apache2-image

$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
f2e72b99ca7e        apache2-image       "apachectl -D FOREGR…"   7 seconds ago       Up 2 seconds        0.0.0.0:5004->5004/tcp   apache2-container

$ curl -I http://localhost:5004/
HTTP/1.1 200 OK
Date: Tue, 21 Nov 2023 17:45:58 GMT
Server: Apache/2.4.52 (Ubuntu)
Last-Modified: Tue, 21 Nov 2023 17:41:08 GMT
ETag: "29af-60aad1b8b5500"
Accept-Ranges: bytes
Content-Length: 10671
Vary: Accept-Encoding
Content-Type: text/html
```
✅

![Level 2 completed](https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/29ca2447-d9aa-4dec-aef5-bb36025737c7)
</details>

## Level 3 (Tasks 3.1-3.5) 
