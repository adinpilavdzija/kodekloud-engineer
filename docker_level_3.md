# Docker Level 3 (Tasks 3.1-3.5)

## 3.1 Create a Docker Network

The Nautilus DevOps team needs to set up several docker environments for different applications. One of the team members has been assigned a ticket where he has been asked to create some docker networks to be used later. Complete the task based on the following ticket description:
1. Create a docker network named as `official` on App Server `1` in `Stratos DC`.
2. Configure it to use `bridge` drivers.
3. Set it to use subnet `192.168.0.0/24` and iprange `192.168.0.1/24`.

---

List Docker networks:
```bash
$ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
daba0dbfa635        bridge              bridge              local
de6960d4f29a        host                host                local
a33cd8ab75ca        none                null                local
```

New network created based on the requirements, then verified by listing the networks and more details about the new network:
```bash
$ docker network create --driver=bridge --subnet=192.168.0.0/24 --ip-range=192.168.0.1/24 official
481a56a9bc0895a1fa70756a0363644d0b14ed4e636b254cb26f81a14cc26955
$ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
daba0dbfa635        bridge              bridge              local
de6960d4f29a        host                host                local
a33cd8ab75ca        none                null                local
481a56a9bc08        official            bridge              local
$ docker network inspect official | head -15
[
    {
        "Name": "official",
        "Id": "481a56a9bc0895a1fa70756a0363644d0b14ed4e636b254cb26f81a14cc26955",
        "Created": "2023-11-22T12:21:57.875736338Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/24",
                    "IPRange": "192.168.0.1/24"
```
✅

## 3.2 Docker Volumes Mapping

The Nautilus DevOps team is testing applications containerization, which issupposed to be migrated on docker container-based environments soon. In today's stand-up meeting one of the team members has been assigned a task to create and test a docker container with certain requirements. Below are more details:
1. On `App Server 2` in `Stratos DC` pull `nginx` image (preferably `latest` tag but others should work too).
2. Create a new container with name `media` from the image you just pulled.
3. Map the host volume `/opt/dba` with container volume `/usr/src/`. There is an `sample.txt` file present on same server under `/tmp`; copy that file to `/opt/dba`. Also please keep the container in running state.

---

Check the existing docker images:
```bash
$ sudo su -
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
```

Pull the required image:
```bash
$ docker pull nginx:latest
```

Copy sample.txt file to the /opt/dba directory:
```bash
$ cp /tmp/sample.txt /opt/dba
```

Run the container with volume mapping:
```bash
$ docker run -d -v /opt/dba/:/usr/src/ --name media nginx:latest
```

Check the active containers and verify:
```bash
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
52dadb402184        nginx:latest        "/docker-entrypoint.…"   5 seconds ago       Up 1 second         80/tcp              media
$ docker exec -it 52dadb402184 ls -la /usr/src
total 12
drwxr-xr-x  2 root root 4096 Jun 25 14:42 .
drwxr-xr-x 14 root root 4096 Jun 12 00:00 ..
-rw-r--r--  1 root root   23 Jun 25 14:42 sample.txt
```
✅

## 3.3 Docker Ports Mapping

The Nautilus DevOps team is planning to host an application on a nginx-based container. There are number of tickets already been created for similar tasks. One of the tickets has been assigned to set up a nginx container on `Application Server 1` in `Stratos Datacenter`. Please perform the task as per details mentioned below:
1. Pull `nginx:alpine` docker image on `Application Server 1`.
2. Create a container named `official` using the image you pulled.
3. Map host port `5000` to container port `80`. Please keep the container in running state.

---

Pull down required NGINX image:
```bash
$ docker pull nginx:alpine
```

Run the container and verify:
```bash
$ docker run -d -p 5000:80 --name official nginx:alpine
ebf2b5390f7dc68b44bbd090d8232fd58b4b6561ec579815ce4c47b68d8e07a0
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
ebf2b5390f7d        nginx:alpine        "/docker-entrypoint.…"   8 seconds ago       Up 5 seconds        0.0.0.0:5000->80/tcp   official
```
✅

## 3.4 Save, Load and Transfer Docker Image

One of the DevOps team members was working on to create a new custom docker image on `App Server 1` in `Stratos DC`. He is done with his changes and image is saved on same server with name `media:datacenter`. Recently a requirement has been raised by a team to use that image for testing, but the team wants to test the same on `App Server 3`. So we need to provide them that image on `App Server 3` in `Stratos DC`.
1. On `App Server 1` save the image `media:datacenter` in an archive.
2. Transfer the image archive to `App Server 3`.
3. Load that image archive on `App Server 3` with same name and tag which was used on `App Server 1`.
`Note`: Docker is already installed on both servers; however, if its service is down please make sure to start it.

---

Check available docker images:
```bash
[tony@stapp01 ~]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
media               datacenter          af4fee324abd        2 minutes ago       124MB
ubuntu              latest              e4c58958181a        7 weeks ago         77.8MB
```

Save the image in an archive file and verify:
```bash
docker save -o /tmp/media.tar media:datacenter

[tony@stapp01 tmp]$ ls -la /tmp/ | grep media.tar
-rw------- 1 tony tony 126504448 Nov 25 10:54 media.tar
```

Copy the file to app server 3 using scp command:
```bash
[tony@stapp01 tmp]$ scp /tmp/media.tar banner@stapp03:/tmp
media.tar                                                                                         100%  121MB 156.2MB/s   00:00
```

Open a second tab, login to the app server 3 and verify that the file is there:
```bash
[banner@stapp03 ~]$ ls -la /tmp | grep media.tar
-rw------- 1 banner banner 126504448 Nov 25 11:26 media.tar
```

Load the image from the archive file and verify:
```bash
[banner@stapp03 ~]$ docker load -i /tmp/media.tar
256d88da4185: Loading layer [==================================================>]  80.35MB/80.35MB
05b67b73acca: Loading layer [==================================================>]  46.14MB/46.14MB
Loaded image: media:datacenter

[banner@stapp03 ~]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
media               datacenter          af4fee324abd        13 minutes ago      124MB
```
✅

## 3.5 Write a Docker Compose File

The Nautilus application development team shared static website content that needs to be hosted on the `httpd` web server using a containerised platform. The team has shared details with the DevOps team, and we need to set up an environment according to those guidelines. Below are the details:
1. On `App Server 2` in `Stratos DC` create a container named `httpd` using a docker compose file `/opt/docker/docker-compose.yml` (please use the exact name for file).
2. Use `httpd` (preferably `latest` tag) image for container and make sure container is named as `httpd`; you can use any name for service.
3. Map `80` number port of container with port `3003` of docker host.
4. Map container's `/usr/local/apache2/htdocs` volume with `/opt/itadmin` volume of docker host which is already there. (please do not modify any data within these locations).

---

Switch to root, change directory to opt/docker/ and create docker-compose.yml file:
```bash
$ sudo su -
$ cd opt/docker/
$ cat > docker-compose.yml
version: '3'
services:
  httpd-service:
    image: httpd:latest
    container_name: httpd
    ports:
      - "3003:80"
    volumes:
      - /opt/itadmin:/usr/local/apache2/htdocs
```

Start the container using docker compose:
```bash
$ docker compose up -d
[+] Running 6/6
 ✔ httpd-service 5 layers [⣿⣿⣿⣿⣿]      0B/0B      Pulled                                                                      11.8s 
   ✔ 1f7ce2fa46ab Pull complete                                                                                                4.5s 
   ✔ 424de2a10000 Pull complete                                                                                                5.0s 
   ✔ 6d9a0131505f Pull complete                                                                                                6.0s 
   ✔ 5728e491734b Pull complete                                                                                                9.9s 
   ✔ 20d3235e84ad Pull complete                                                                                               11.3s 
[+] Building 0.0s (0/0)                                                                                                             
[+] Running 2/2
 ✔ Network docker_default  Created                                                                                             0.1s 
 ✔ Container httpd         Started                                                                                             4.2s 
```

Verify:
```bash
$ docker ps
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                  NAMES
84eb33da61a2        httpd:latest        "httpd-foreground"   17 seconds ago      Up 12 seconds       0.0.0.0:3003->80/tcp   httpd
```
✅

![docker_level_3](https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/0f3a4976-d883-4dcb-ab3e-282689471683)