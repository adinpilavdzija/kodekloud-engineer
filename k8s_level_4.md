# Kubernetes Level 4 (Tasks 4.1-4.5) 

`Note:` The `kubectl` utility on `jump_host` has been configured to work with the kubernetes cluster.

## 4.1 Deploy Redis Deployment on Kubernetes

The Nautilus application development team observed some performance issues with one of the application that is deployed in Kubernetes cluster. After looking into number of factors, the team has suggested to use some in-memory caching utility for DB service. After number of discussions, they have decided to use Redis. Initially they would like to deploy Redis on kubernetes cluster for testing and later they will move it to production. Please find below more details about the task:

Create a redis deployment with following parameters:

1. Create a `config map` called `my-redis-config` having `maxmemory 2mb` in `redis-config`.
2. Name of the `deployment` should be `redis-deployment`, it should use `redis:alpine` image and container name should be `redis-container`. Also make sure it has only `1` replica.
3. The container should request for `1` CPU.
4. Mount `2` volumes:
  - An Empty directory volume called `data` at path `/redis-master-data`.
  - A configmap volume called `redis-config` at path `/redis-master`.
  - The container should expose the port `6379`.
5. Finally, `redis-deployment` should be in an up and running state.

---

Create the configmap, apply and verify:
```bash
$ cat my-redis-config.yaml
apiVersion: v1
kind: ConfigMap
metadata: 
  name: my-redis-config
data:
  maxmemory: 2mb

$ kubectl apply -f my-redis-config.yaml
configmap/my-redis-config created

$ kubectl get cm
NAME               DATA   AGE
kube-root-ca.crt   1      30m
my-redis-config    1      8s

$ kubectl describe cm my-redis-config 
Name:         my-redis-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
maxmemory:
----
2mb

BinaryData
====

Events:  <none>
```

Create the deployment manifest, apply and verify:
```bash
$ cat redis-deployment.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: redis-deployment
  name: redis-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis-deployment
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: redis-deployment
    spec:
      containers:
      - image: redis:alpine
        name: redis-container
        ports: 
        - containerPort: 6379
        resources:
          requests: 
            cpu: "1"
        volumeMounts:
          - name: data
            mountPath: /redis-master-data
          - name: redis-config
            mountPath: /redis-master
      volumes:
        - name: data
          emptyDir:
            sizeLimit: 100Mi
        - name: redis-config
          configMap:
            name: my-redis-config

$ kubectl apply -f redis-deployment.yaml 
deployment.apps/redis-deployment created

$ kubectl get deployments
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
redis-deployment   1/1     1            1           9s

$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
redis-deployment-794bbd74d5-j5bgp   1/1     Running   0          15s
```     
✅

## 4.2 Deploy My SQL on Kubernetes

A new MySQL server needs to be deployed on Kubernetes cluster. The Nautilus DevOps team was working on to gather the requirements. Recently they were able to finalize the requirements and shared them with the team members to start working on it. Below you can find the details:

1. Create a PersistentVolume `mysql-pv`, its capacity should be `250Mi`, set other parameters as per your preference.
2. Create a PersistentVolumeClaim to request this PersistentVolume storage. Name it as `mysql-pv-claim` and request a `250Mi` of storage. Set other parameters as per your preference.
3. Create a deployment named `mysql-deployment`, use any mysql image as per your preference. Mount the PersistentVolume at mount path `/var/lib/mysql`.
4. Create a `NodePort` type service named `mysql` and set nodePort to `30007`.
5. Create a secret named `mysql-root-pass` having a key pair value, where key is `password` and its value is `YUIidhb667`, create another secret named `mysql-user-pass` having some key pair values, where frist key is `username` and its value is `kodekloud_sam`, second key is `password` and value is `dCV3szSGNA`, create one more secret named `mysql-db-url`, key name is `database` and value is `kodekloud_db8`
6. Define some Environment variables within the container:
  - `name: MYSQL_ROOT_PASSWORD`, should pick value from secretKeyRef `name: mysql-root-pass` and `key: password`
  - `name: MYSQL_DATABASE`, should pick value from secretKeyRef `name: mysql-db-url` and `key: database`
  - `name: MYSQL_USER`, should pick value from secretKeyRef n`ame: mysql-user-pass` key `key: username`
  - `name: MYSQL_PASSWORD`, should pick value from secretKeyRef `name: mysql-user-pass` and `key: password`

---

Create PersistentVolume, apply and verify:
```bash
$ cat pv.yaml 
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
spec:
  capacity:
    storage: 250Mi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: standard
  hostPath: 
    path: /var/lib/mysql

$ k apply -f pv.yaml 
persistentvolume/mysql-pv created

$ k get pv
NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
mysql-pv   250Mi      RWO            Recycle          Available           standard                4s
```

Create PersistentVolumeClaim, apply and verify:
```bash
$ cat pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  storageClassName: standard
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 250Mi

$ k apply -f pvc.yaml 
persistentvolumeclaim/mysql-pv-claim created

$ k get pvc
NAME             STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
mysql-pv-claim   Pending                                      standard       5s

$ k describe pvc mysql-pv-claim |grep Events -A 10
Events:
  Type    Reason                Age               From                         Message
  ----    ------                ----              ----                         -------
  Normal  WaitForFirstConsumer  7s (x2 over 18s)  persistentvolume-controller  waiting for first consumer to be created before bindingg
```

Create the secrets and verify:
```bash
$ k create secret generic mysql-root-pass --from-literal=password=YUIidhb667
secret/mysql-root-pass created
thor@jump_host ~$ k create secret generic mysql-user-pass --from-literal=username=kodekloud_sam --from-literal=password=dCV3szSGNA
secret/mysql-user-pass created
thor@jump_host ~$ k create secret generic mysql-db-url --from-literal=database=kodekloud_db8
secret/mysql-db-url created

$ k get secret
NAME              TYPE     DATA   AGE
mysql-db-url      Opaque   1      9s
mysql-root-pass   Opaque   1      19s
mysql-user-pass   Opaque   2      14s
```

Create the deployment, apply and verify:
```bash
$ cat deployment.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: mysql-deployment
  name: mysql-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql-deployment
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: mysql-deployment
    spec:
      containers:
      - image: mysql:latest
        name: mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-root-pass
              key: password
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: mysql-db-url
              key: database
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: mysql-user-pass
              key: username
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-user-pass
              key: password
        volumeMounts:
          - name: mysql-pv-claim
            mountPath: /var/lib/mysql
      volumes:
      - name: mysql-pv-claim
        persistentVolumeClaim: 
          claimName: mysql-pv-claim

$ k apply -f deployment.yaml 
deployment.apps/mysql-deployment created
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
mysql-deployment   1/1     1            1           35s

$ k get pods
NAME                                READY   STATUS    RESTARTS   AGE
mysql-deployment-678665c57f-58j7w   1/1     Running   0          33s
```

Expose the deployment and edit, then verify:
```bash
$ kubectl expose deployment mysql-deployment --name=mysql --type=NodePort --port=8080
service/mysql exposed

$ k edit svc mysql
service/mysql edited
  - nodePort: 31396
    - nodePort: 30007

$ k get svc
NAME         TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)          AGE
kubernetes   ClusterIP   10.96.0.1     <none>        443/TCP          80m
mysql        NodePort    10.96.95.44   <none>        8080:30007/TCP   72s

$ k get pvc
NAME             STATUS   VOLUME     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
mysql-pv-claim   Bound    mysql-pv   250Mi      RWO            standard       4m9s
```
✅

## 4.3 Kubernetes Nginx and Php FPM Setup

The Nautilus Application Development team is planning to deploy one of the php-based applications on Kubernetes cluster. As per the recent discussion with DevOps team, they have decided to use nginx and phpfpm. Additionally, they also shared some custom configuration requirements. Below you can find more details. Please complete this task as per requirements mentioned below:

1. Create a service to expose this app, the service type must be `NodePort`, nodePort should be `30012`.
2. Create a config map named `nginx-config` for `nginx.conf` as we want to add some custom settings in nginx.conf.
- Change the default port `80` to `8093` in `nginx.conf`.
- Change the default document root `/usr/share/nginx` to `/var/www/html` in `nginx.conf`.
- Update the directory index to `index  index.html index.htm index.php` in `nginx.conf`.
3. Create a pod named `nginx-phpfpm`.
- Create a shared volume named `shared-files` that will be used by both containers (nginx and phpfpm) also it should be a `emptyDir` volume.
- Map the ConfigMap we declared above as a volume for nginx container. Name the volume as `nginx-config-volume`, mount path should be `/etc/nginx/nginx.conf` and `subPath` should be `nginx.conf`
- Nginx container should be named as `nginx-container` and it should use `nginx:latest` image. PhpFPM container should be named as `php-fpm-container` and it should use `php:7.3-fpm-alpine` image.
- The shared volume `shared-files` should be mounted at `/var/www/html` location in both containers. Copy `/opt/index.php` from `jump host` to the nginx document root inside the `nginx` container, once done you can access the app using `App` button on the top bar.

Before clicking on `finish` button always make sure to check if all pods are in `running` state.

`You can use any labels as per your choice.`

---

Create the ConfigMap, apply and verify:
```bash
$ cat configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-config
data:
  nginx.conf: |
    events {} 
    http {
      server {
        listen 8093;
        index index.html index.htm index.php;
        root  /var/www/html;
        location ~ \.php$ {
          include fastcgi_params;
          fastcgi_param REQUEST_METHOD $request_method;
          fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
          fastcgi_pass 127.0.0.1:9000;
        }
      }
    } 

$ k apply -f configmap.yaml 
configmap/nginx-config created

$ k get cm
NAME               DATA   AGE
kube-root-ca.crt   1      74m
nginx-config       1      12s
```

Create the manifest for Pod and Service, apply and verify:
```bash
$ cat nginx-phpfpm.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-phpfpm
spec:
  type: NodePort
  selector:
    app: nginx-phpfpm
  ports:
    - port: 8093
      targetPort: 8093
      nodePort: 30012
---       
apiVersion: v1
kind: Pod
metadata:
  name: nginx-phpfpm
  labels:
     app: nginx-phpfpm
spec:
  volumes:
    - name: shared-files
      emptyDir: {}
    - name: nginx-config-volume
      configMap:
        name: nginx-config
  containers:
    - name: nginx-container
      image: nginx:latest
      volumeMounts:
        - name: shared-files
          mountPath: /var/www/html
        - name: nginx-config-volume
          mountPath: /etc/nginx/nginx.conf
          subPath: nginx.conf
    - name: php-fpm-container
      image: php:8.2-fpm-alpine
      volumeMounts:
        - name: shared-files
          mountPath: /var/www/html

$ k apply -f nginx-phpfpm.yaml 
service/nginx-phpfpm created
pod/nginx-phpfpm created

$ k get pods
NAME           READY   STATUS    RESTARTS   AGE
nginx-phpfpm   2/2     Running   0          29s

$ k get svc
NAME           TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)          AGE
kubernetes     ClusterIP   10.96.0.1     <none>        443/TCP          77m
nginx-phpfpm   NodePort    10.96.91.95   <none>        8093:30012/TCP   37s
```

Copy file into the container and verify:
```bash
$ cat /opt/index.php 
It works!

$ kubectl cp /opt/index.php nginx-phpfpm:/var/www/html/ -c nginx-container 

$ kubectl exec -it nginx-phpfpm -c nginx-container -- ls -l /var/www/html
total 4
-rw-r--r-- 1 root root 9 Mar 12 12:12 index.php

$ kubectl exec -it nginx-phpfpm -c nginx-container -- cat /var/www/html/index.php
It works!
```

![k8s43](https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/7fc7701d-c93b-47ac-b62b-2199cde173e8)
✅

## 4.4 Deploy Drupal App on Kubernetes

We need to deploy a Drupal application on Kubernetes cluster. The Nautilus application development team want to setup a fresh Drupal as they will do the installation on their own. Below you can find the requirements, they have shared with us.

1. Configure a persistent volume `drupal-mysql-pv` with `hostPath = /drupal-mysql-data` (`/drupal-mysql-data` directory already exists on the worker Node i.e jump host), `5Gi` of storage and `ReadWriteOnce` access mode.
2. Configure one PersistentVolumeClaim named `drupal-mysql-pvc` with storage request of `3Gi` and `ReadWriteOnce` access mode.
3. Create a deployment `drupal-mysql` with `1` replica, use `mysql:5.7` image. Mount the claimed PVC at ˙`.
4. Create a deployment `drupal` with `1` replica and use `drupal:8.6` image.
5. Create a `NodePort` type service which should be named as `drupal-service` and nodePort should be `30095`.
6. Create a service `drupal-mysql-service` to expose mysql deployment on port `3306`.
7. Set rest of the configration for deployments, services, secrets etc as per your preferences. At the end you should be able to access the Drupal installation page by clicking on `App` button.

---

Create one manifest for all - PersistentVolume, PersistentVolumeClaim, 2 Deployments and 2 Services:
```bash 
$ cat all.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: drupal-mysql-pv
spec:
  capacity:
    storage: 5Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  hostPath:
    path: /drupal-mysql-data 
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: drupal-mysql-pvc
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 3Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: drupal-mysql
  name: drupal-mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: drupal-mysql
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: drupal-mysql
    spec:
      containers:
      - image: mysql:5.7
        name: drupal-mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: "r00tp4ss*1"
        - name: MYSQL_DATABASE
          value: "database"
        - name: MYSQL_USER
          value: "dbuser"
        - name: MYSQL_PASSWORD
          value: "passgphpo3219"
        volumeMounts:
          - mountPath: /var/lib/mysql
            name: drupal-mysql-pvc
      volumes: 
        - name: drupal-mysql-pvc
          persistentVolumeClaim: 
            claimName: drupal-mysql-pvc
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: drupal
  name: drupal
spec:
  replicas: 1
  selector:
    matchLabels:
      app: drupal
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: drupal
    spec:
      containers:
      - image: drupal:8.6
        name: drupal
---
apiVersion: v1
kind: Service
metadata:
  name: drupal-service
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30095
  selector:
    app: drupal
--- 
apiVersion: v1
kind: Service
metadata:
  name: drupal-mysql-service
spec:
  ports:
    - port: 3306
  selector:
    app: drupal-mysql
```

Apply and verify:
```bash 
$ k apply -f all.yaml 
persistentvolume/drupal-mysql-pv created
persistentvolumeclaim/drupal-mysql-pvc created
deployment.apps/drupal-mysql created
deployment.apps/drupal created
service/drupal-service created
service/drupal-mysql-service created

$ k get all
NAME                                READY   STATUS    RESTARTS   AGE
pod/drupal-789b56557f-5rbsd         1/1     Running   0          18s
pod/drupal-mysql-6779b589c5-nnjkn   1/1     Running   0          18s

NAME                           TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/drupal-mysql-service   ClusterIP   10.96.60.102    <none>        3306/TCP       18s
service/drupal-service         NodePort    10.96.202.130   <none>        80:30095/TCP   18s
service/kubernetes             ClusterIP   10.96.0.1       <none>        443/TCP        86m

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/drupal         1/1     1            1           18s
deployment.apps/drupal-mysql   1/1     1            1           18s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/drupal-789b56557f         1         1         1       18s
replicaset.apps/drupal-mysql-6779b589c5   1         1         1       18s
``` 

![k8s44](https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/2ed5e074-fdc2-4fe2-8453-d1982acd9760)
✅

## 4.5 Deploy Guest Book App on Kubernetes

The Nautilus Application development team has finished development of one of the applications and it is ready for deployment. It is a guestbook application that will be used to manage entries for guests/visitors. As per discussion with the DevOps team, they have finalized the infrastructure that will be deployed on Kubernetes cluster. Below you can find more details about it.

`BACK-END TIER`
1. Create a deployment named `redis-master` for Redis master.
  - Replicas count should be `1`.
  - Container name should be `master-redis-datacenter` and it should use image `redis`.
  - Request resources as `CPU` should be `100m` and Memory should be `100Mi`.
  - Container port should be redis default port i.e `6379`.
2. Create a service named `redis-master` for Redis master. Port and targetPort should be Redis default port i.e `6379`.
3. Create another deployment named `redis-slave` for Redis slave.
  - Replicas count should be `2`.
  - Container name should be `slave-redis-datacenter` and it should use `gcr.io/google_samples/gb-redisslave:v3` image.
  - Requests resources as `CPU` should be `100m` and `Memory` should be `100Mi`.
  - Define an environment variable named `GET_HOSTS_FROM` and its value should be `dns`.
  - Container port should be Redis default port i.e `6379`.
4. Create another service named `redis-slave`. It should use Redis default port i.e `6379`.

`FRONT END TIER`
1. Create a deployment named `frontend`.
  - Replicas count should be `3`.
  - Container name should be `php-redis-datacenter` and it should use `gcr.io/google-samples/gb-frontend@sha256:cbc8ef4b0a2d0b95965e0e7dc8938c270ea98e34ec9d60ea64b2d5f2df2dfbbf` image.
  - Request resources as `CPU` should be `100m` and `Memory` should be `100Mi`.
  - Define an environment variable named as `GET_HOSTS_FROM` and its value should be `dns`.
  - Container port should be `80`.
2. Create a service named `frontend`. Its `type` should be `NodePort`, port should be `80` and its `nodePort` should be `30009`.

Finally, you can check the `guestbook app` by clicking on `App` button.

`You can use any labels as per your choice.`

---

Create manifest for backend - 2 Deployments and 2 Services:
```bash
$ cat backend.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: redis-master
  name: redis-master
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis-master
  strategy: {}
  template:
    metadata:
      labels:
        app: redis-master
    spec:
      containers:
      - image: redis
        name: master-redis-datacenter
        ports:
        - containerPort: 6379
        resources: 
          requests:
            cpu: "100m"
            memory: "100Mi"
---
apiVersion: v1
kind: Service
metadata:
  name: redis-master
spec:
  selector:
    app.kubernetes.io/name: MyApp
  ports:
    - protocol: TCP
      port: 6379
      targetPort: 6379
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: redis-slave
  name: redis-slave
spec:
  replicas: 2
  selector:
    matchLabels:
      app: redis-slave
  strategy: {}
  template:
    metadata:
      labels:
        app: redis-slave
    spec:
      containers:
      - image: gcr.io/google_samples/gb-redisslave:v3
        name: slave-redis-datacenter
        ports:
        - containerPort: 6379
        env:
          - name: GET_HOSTS_FROM
            value: dns
        resources: 
          requests:
            cpu: "100m"
            memory: "100Mi"
---
apiVersion: v1
kind: Service
metadata:
  name: redis-slave
spec:
  selector:
    app.kubernetes.io/name: MyApp
  ports:
    - protocol: TCP
      port: 6379
      targetPort: 6379
```

Apply and verify:
```bash
$ k apply -f backend.yaml 
deployment.apps/redis-master created
service/redis-master created
deployment.apps/redis-slave created
service/redis-slave created

$ k get pods,svc
NAME                                READY   STATUS    RESTARTS   AGE
pod/redis-master-686856dd49-4ljv9   1/1     Running   0          40s
pod/redis-slave-668f85fd-nwvfw      1/1     Running   0          40s
pod/redis-slave-668f85fd-ttgwz      1/1     Running   0          40s

NAME                   TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
service/kubernetes     ClusterIP   10.96.0.1      <none>        443/TCP    91m
service/redis-master   ClusterIP   10.96.66.218   <none>        6379/TCP   40s
service/redis-slave    ClusterIP   10.96.70.116   <none>        6379/TCP   40s
```

Create manifest for frontend - Deployment and Service:
```bash
$ cat frontend.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: frontend
  name: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: frontend
  strategy: {}
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - image: gcr.io/google-samples/gb-frontend@sha256:cbc8ef4b0a2d0b95965e0e7dc8938c270ea98e34ec9d60ea64b2d5f2df2dfbbf
        name: php-redis-datacenter
        ports:
        - containerPort: 80
        env:
          - name: GET_HOSTS_FROM
            value: dns
        resources: 
          requests:
            cpu: "100m"
            memory: "100Mi"
---
apiVersion: v1
kind: Service
metadata:
  name: frontend
spec:
  type: NodePort
  selector:
    app: frontend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30009
```

Apply and verify:
```bash
$ k apply -f frontend.yaml 
deployment.apps/frontend created
service/frontend created

$ k get pods,svc| grep frontend
pod/frontend-b6847b798-7mz4x        1/1     Running   0          38s
pod/frontend-b6847b798-g7n4j        1/1     Running   0          38s
pod/frontend-b6847b798-khk42        1/1     Running   0          38s
service/frontend       NodePort    10.96.3.231    <none>        80:30009/TCP   38s
```

![k8s45](https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/4c010f29-7de5-4e57-b41a-99c2555de3a1)
✅

![k8s4](https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/0ee0a27f-9bd4-499f-8d38-99b98add16b2)