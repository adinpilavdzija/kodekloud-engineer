# Docker Level 2 (Tasks 2.1-2.5)

## 2.1 Pull Docker Image

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

## 2.2 Docker Update Permissions

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

## 2.3 Create a Docker Image From Container

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

## 2.4 Docker EXEC Operations

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

## 2.5 Write a Docker File

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

![Level 2 completed](https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/e49efb27-f685-42a5-b0a9-284cc45f3cc3)