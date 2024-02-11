# Docker Level 1 (Tasks 1.1-1.5) 

## 1.1 Install Docker Package

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

## 1.2 Run a Docker Container

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

## 1.3 Docker Delete Container

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

## 1.4 Docker Copy Operations

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

## 1.5 Docker Container Issue

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

![Level 1 completed](https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/72aca159-fa42-494d-a114-ca71902259bb)