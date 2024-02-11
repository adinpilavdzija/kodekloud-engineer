# Docker Level 4 (Tasks 4.1-4.5) 

## 4.1 Resolve Dockerfile Issues

The Nautilus DevOps team is working to create new images per requirements shared by the development team. One of the team members is working to create a `Dockerfile` on `App Server 1` in `Stratos DC`. While working on it she ran into issues in which the docker build is failing and displaying errors. Look into the issue and fix it to build an image as per details mentioned below:
1. The `Dockerfile` is placed on `App Server 1` under `/opt/docker` directory.
2. Fix the issues with this file and make sure it is able to build the image.
3. Do not change base image, any other valid configuration within Dockerfile, or any of the data been used — for example, index.html.
`Note`: Please note that once you click on `FINISH` button all existing images, the containers will be destroyed and new image will be built from your `Dockerfile`.

---

Switch to root, change directory to /opt/docker/ and list files:
```bash
$ sudo su -
$ cd /opt/docker/ && ls
Dockerfile  certs  html
```

Run docker build to detect the error:
```bash
$ docker build -t web-server .
Sending build context to Docker daemon  9.216kB
Step 1/8 : FROM httpd:2.4.43
2.4.43: Pulling from library/httpd
bf5952930446: Pull complete 
3d3fecf6569b: Pull complete 
b5fc3125d912: Pull complete 
3c61041685c0: Pull complete 
34b7e9053f76: Pull complete 
Digest: sha256:cd88fee4eab37f0d8cd04b06ef97285ca981c27b4d685f0321e65c5d4fd49357
Status: Downloaded newer image for httpd:2.4.43
 ---> f1455599cc2e
Step 2/8 : RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf
 ---> Running in 9757805a37b1
Removing intermediate container 9757805a37b1
 ---> 50491ccd8e8a
Step 3/8 : RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' conf/httpd.conf
 ---> Running in 646acf674feb
Removing intermediate container 646acf674feb
 ---> c0bf4b465a2c
Step 4/8 : RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' conf/httpd.conf
 ---> Running in 90696539eda4
Removing intermediate container 90696539eda4
 ---> c49da80dafad
Step 5/8 : RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' conf/httpd.conf
 ---> Running in 63dfee23cb8a
Removing intermediate container 63dfee23cb8a
 ---> 409e062ca179
Step 6/8 : RUN cp certs/server.crt /usr/local/apache2/conf/server.crt
 ---> Running in ffb2bd6292f7
cp: cannot stat 'certs/server.crt': No such file or directory
The command '/bin/sh -c cp certs/server.crt /usr/local/apache2/conf/server.crt' returned a non-zero code: 1
```

Change the Dockerfile:
```bash
$ cat > Dockerfile
FROM httpd:2.4.43
RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf
RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' conf/httpd.conf
RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' conf/httpd.conf
RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' conf/httpd.conf
COPY certs/server.crt /usr/local/apache2/conf/server.crt
COPY certs/server.key /usr/local/apache2/conf/server.key
COPY html/index.html /usr/local/apache2/htdocs/
```

Build the image with new Dockerfile and verify:
```bash
$ docker build -t web-server .
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
web-server          latest              a9854def7374        21 seconds ago      166MB
httpd               2.4.43              f1455599cc2e        3 years ago         166MB
```
✅

### 4.2 Resolve Docker Compose Issues

The Nautilus DevOps team is working to deploy one of the applications on `App Server 3` in `Stratos DC`. Due to a misconfiguration in the docker compose file, the deployment is failing. We would like you to take a look into it to identify and fix the issues. More details can be found below:
1. `docker-compose.yml` file is present on `App Server 3` under `/opt/docker` directory.
2. Try to run the same and make sure it works fine.
3. Please do not change the `container names` being used. Also, do not update or alter any other valid config settings in the compose file or any other relevant data that can cause app failure.
`Note`: Please note that once you click on `FINISH` button all existing running/stopped containers will be destroyed, and your compose will be run.

---

Switch to root, change directory to /opt/docker/ and list files:
```bash
$ sudo su -
$ cd /opt/docker/ && ls -l
total 8
drwxrwxrwx 2 root root 4096 Nov 27 18:45 app
-rw-r--r-- 1 root root  265 Nov 27 18:43 docker-compose.yml
```

Show the docker-compose.yml file:
```bash
$ cat docker-compose.yml 
version: '1'
services:
    web:
        build: ./app
        container_name: python
        ports:
            - "5000:5000"
        volume:
            - ./app:/code
        depends:
            - redis
    redis:
        build: redis
        container_name: redis
```

Run containers using docker-compose up command:
```bash
$ docker-compose up 
ERROR: Version in "./docker-compose.yml" is invalid. You might be seeing this error because you're using the wrong Compose file version. Either specify a supported version (e.g "2.2" or "3.3") and place your service definitions under the `services` key, or omit the `version` key and place your service definitions at the root of the file to use version 1.
For more on the Compose file format versions, see https://docs.docker.com/compose/compose-file/
```

Fix the docker-compose.yml file:
```bash
$ cat > docker-compose.yml 
version: '3'
services:
    web:
        build: ./app
        container_name: python
        ports:
            - "5000:5000"
        volumes:
            - ./app:/code
        depends_on:
            - redis
    redis:
        image: redis
        container_name: redis
```

Run containers using docker-compose up command and new file, then verify:
```bash
$ docker-compose up -d
$ docker ps
CONTAINER ID   IMAGE        COMMAND                  CREATED              STATUS              PORTS                    NAMES
d4b2aa8d4177   docker_web   "/bin/sh -c 'python …"   About a minute ago   Up About a minute   0.0.0.0:5000->5000/tcp   python
262c2c2f805b   redis        "docker-entrypoint.s…"   About a minute ago   Up About a minute   6379/tcp                 redis
```
✅

## 4.3 Deploy an App on Docker Containers

The Nautilus Application development team recently finished development of one of the apps that they want to deploy on a containerized platform. The Nautilus Application development and DevOps teams met to discuss some of the basic pre-requisites and requirements to complete the deployment. The team wants to test the deployment on one of the app servers before going live and set up a complete containerized stack using a docker compose fie. Below are the details of the task:

On `App Server 3` in `Stratos Datacenter` create a docker compose file `/opt/security/docker-compose.yml` (should be named exactly).

The compose should deploy two services (web and DB), and each service should deploy a container as per details below:

1. `For web service:`
  - Container name must be `php_host`.
  - Use image `php` with any `apache` tag. Check [here](https://hub.docker.com/_/php?tab=tags/) for more details.
  - Map `php_host` container's port `80` with host port `8083`
  - Map `php_host` container's `/var/www/html` volume with host volume `/var/www/html`.
2. `For DB service:`
  - Container name must be `mysql_host`.
  - Use image `mariadb` with any tag (preferably `latest`). Check [here](https://hub.docker.com/_/mariadb?tab=tags/) for more details.
  - Map `mysql_host` container's port `3306` with host port `3306`
  - Map `mysql_host` container's `/var/lib/mysql` volume with host volume `/var/lib/mysql`.
  - Set MYSQL_DATABASE=`database_host` and use any custom user (except root) with some complex password for DB connections.
3. After running docker-compose up you can access the app with curl command `curl <server-ip or hostname>:8083/`

For more details check [here](https://hub.docker.com/_/mariadb?tab=description/).

`Note`: Once you click on `FINISH` button, all currently running/stopped containers will be destroyed and stack will be deployed again using your compose file.

---

Switch to root, change directory to /opt/security/ and create docker-compose.yml:
```bash
$ sudo su -
$ cd /opt/security/
$ cat > docker-compose.yml
version: '3'
services:
  web:
    container_name: php_host
    image: php:apache
    ports:
      - "8083:80"
    volumes:
      - /var/www/html:/var/www/html
  DB:
    container_name: mysql_host
    image: mariadb:latest
    ports:
      - "3306:3306"
    volumes:
      - /var/lib/mysql:/var/lib/mysql
    environment:
      MYSQL_DATABASE: database_host
      MYSQL_USER: adin
      MYSQL_PASSWORD: pass123!
      MYSQL_ROOT_PASSWORD: rootpass123!
```

Run the containers using docker compose up command and verify:
```bash
$ docker-compose up -d
$ docker ps
CONTAINER ID   IMAGE            COMMAND                  CREATED              STATUS          PORTS                    NAMES
84dee3715cf6   php:apache       "docker-php-entrypoi…"   About a minute ago   Up 11 seconds   0.0.0.0:8083->80/tcp     php_host
d07acd1297a9   mariadb:latest   "docker-entrypoint.s…"   About a minute ago   Up 31 seconds   0.0.0.0:3306->3306/tcp   mysql_host
$ curl localhost:8083
<html>
    <head>
        <title>Welcome to xFusionCorp Industries!</title>
    </head>

    <body>
        Welcome to xFusionCorp Industries!    </body>
</html>
```
✅

### 4.4 Docker Node App

There is a requirement to Dockerize a Node app and to deploy the same on `App Server 1`. Under `/node_app` directory on `App Server 1`, we have already placed a `package.json` file that describes the app dependencies and `server.js` file that defines a web app framework.

1. Create a `Dockerfile` (name is case sensitive) under `/node_app` directory:
  - Use any `node` image as the base image.
  - Install the dependencies using `package.json` file.
  - Use `server.js` in the `CMD`.
  - Expose port `3001`.
2. The build image should be named as `nautilus/node-web-app`.
3. Now run a container named `nodeapp_nautilus` using this image.
  - Map the container port `3001` with the host port `8099`.

Once deployed, you can test the app using a `curl` command on `App Server 1`:
`curl http://localhost:8099`

---

Switch to root, change directory to /node_app/ and create Dockerfile:
```bash
$ sudo su -
$ cd /node_app/
$ cat > Dockerfile
FROM node:21-alpine
WORKDIR /app
EXPOSE 3001
COPY package.json .
RUN npm install
COPY . .
CMD [ "node", "server.js" ]
```

Build the image from Dockerfile and verify:
```bash
$ docker build -t nautilus/node-web-app .
$ docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
nautilus/node-web-app   latest              04c4cfbaf90b        8 seconds ago       149MB
node                    21-alpine           336ee465c313        2 weeks ago         142MB
```

Run the container and verify:
```bash
$ docker run -d -p 8099:3001 --name nodeapp_nautilus nautilus/node-web-app
$ docker ps
CONTAINER ID        IMAGE                   COMMAND                  CREATED              STATUS              PORTS                    NAMES
4fbe013c90d7        nautilus/node-web-app   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:8099->3001/tcp   nodeapp_nautilus
$ curl http://localhost:8099
Welcome to xFusionCorp Industries!
```
✅

## 4.5 Docker Python App

A python app needed to be Dockerized, and then it needs to be deployed on `App Server 3`. We have already copied a `requirements.txt` file (having the app dependencies) under `/python_app/src/` directory on `App Server 3`. Further complete this task as per details mentioned below:

1. Create a `Dockerfile` under `/python_app` directory:
  - Use any `python` image as the base image.
  - Install the dependencies using `requirements.txt` file.
  - Expose the port `3001`.
  - Run the `server.py` script using `CMD`.
2. Build an image named `nautilus/python-app` using this Dockerfile.
3. Once image is built, create a container named `pythonapp_nautilus`:
  - Map port `8085` of the container to the host port `8097`.
4. Once deployed, you can test the app using curl command on App Server 3:
`curl http://localhost:8097/`

---

Switch to root, change directory to /python_app/ and create Dockerfile:
```bash
$ sudo su -
$ cd /python_app/
$ cat > Dockerfile
FROM python:3.13.0a2-alpine
WORKDIR /app
EXPOSE 3001
COPY src/requirements.txt .
RUN pip install -r requirements.txt
COPY src/ .
CMD ["python", "server.py"]
```

Build the image from Dockerfile and verify:
```bash
$ docker build -t nautilus/python-app .
$ docker images
REPOSITORY            TAG                 IMAGE ID            CREATED              SIZE
nautilus/python-app   latest              bdebde086d3e        About a minute ago   63.9MB
python                3.13.0a2-alpine     3cde44c24114        8 days ago           47.3MB
```

Run the container and verify:
```bash
$ docker run -d -p 8097:3001 --name pythonapp_nautilus nautilus/python-app
$ docker ps 
CONTAINER ID        IMAGE                 COMMAND              CREATED              STATUS              PORTS                    NAMES
da1b693a3dc4        nautilus/python-app   "python server.py"   About a minute ago   Up 58 seconds       0.0.0.0:8097->3001/tcp   pythonapp_nautilus
$ curl http://localhost:8097/
Welcome to xFusionCorp Industries!
```
✅

![docker-level-4](https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/663c35ac-5901-4f7e-88ef-6a359a78355f)