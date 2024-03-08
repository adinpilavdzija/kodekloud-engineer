# Kubernetes Level 3 (Tasks 3.1-3.10) 

`Note:` The `kubectl` utility on `jump_host` has been configured to work with the kubernetes cluster.

## 3.1 Deploy Apache Web Server on Kubernetes CLuster

There is an application that needs to be deployed on Kubernetes cluster under Apache web server. The Nautilus application development team has asked the DevOps team to deploy it. We need to develop a template as per requirements mentioned below:

1. Create a `namespace` named as `httpd-namespace-xfusion`.
2. Create a `deployment` named as `httpd-deployment-xfusion` under newly created namespace. For the deployment use `httpd` image with `latest` tag only and remember to mention the tag i.e `httpd:latest`, and make sure replica counts are `2`.
3. Create a `service` named as `httpd-service-xfusion` under same namespace to expose the deployment, nodePort should be `30004`.

---

Create a namespace:
```bash
$ kubectl create namespace httpd-namespace-xfusion
namespace/httpd-namespace-xfusion created
```

Create a deployment, apply and verify:
```bash
$ cat >httpd-deployment-xfusion.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd-deployment-xfusion
  namespace: httpd-namespace-xfusion
spec:
  replicas: 2
  selector:
    matchLabels:
      app: httpd-xfusion
  template:
    metadata:
      labels:
        app: httpd-xfusion
    spec:
      containers:
      - name: httpd
        image: httpd:latest
        ports:
        - containerPort: 80

$ kubectl apply -f httpd-deployment-xfusion.yaml 
deployment.apps/httpd-deployment-xfusion created

$ kubectl get deployments -n httpd-namespace-xfusion
NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
httpd-deployment-xfusion   2/2     2            2           24s
$ kubectl get pods -n httpd-namespace-xfusion
NAME                                       READY   STATUS    RESTARTS   AGE
httpd-deployment-xfusion-849c8ddfd-2frvb   1/1     Running   0          35s
httpd-deployment-xfusion-849c8ddfd-mfp9t   1/1     Running   0          35s
```

Create a service, apply and verify:
```bash
$ cat >httpd-service-xfusion.yaml
apiVersion: v1
kind: Service
metadata:
  name: httpd-service-xfusion
  namespace: httpd-namespace-xfusion
spec:
  type: NodePort
  selector:
    app: httpd-xfusion
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30004

$ kubectl apply -f httpd-service-xfusion.yaml 
service/httpd-service-xfusion created

$ kubectl get services -n httpd-namespace-xfusion
NAME                    TYPE       CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
httpd-service-xfusion   NodePort   10.96.14.80   <none>        80:30004/TCP   62s
```

![k8s-3-1](https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/39fc446f-c90c-4ec4-bdf6-dec9a726dbe6)
✅

## 3.2 Deploy Lamp Stack on Kubernetes Cluster

The Nautilus DevOps team want to deploy a PHP website on Kubernetes cluster. They are going to use Apache as a web server and Mysql for database. The team had already gathered the requirements and now they want to make this website live. Below you can find more details:

1. Create a config map `php-config` for `php.ini` with `variables_order = "EGPCS"` data.
2. Create a deployment named `lamp-wp`.
3. Create two containers under it. First container must be `httpd-php-container` using image `webdevops/php-apache:alpine-3-php7` and second container must be `mysql-container` from image `mysql:5.6`. Mount `php-config` configmap in httpd container at `/opt/docker/etc/php/php.ini` location.
4. Create kubernetes generic secrets for mysql related values like myql root password, mysql user, mysql password, mysql host and mysql database. Set any values of your choice.
5. Add some environment variables for both containers:
a) `MYSQL_ROOT_PASSWORD`, `MYSQL_DATABASE`, `MYSQL_USER`, `MYSQL_PASSWORD` and `MYSQL_HOST`. Take their values from the secrets you created. Please make sure to use `env` field (do not use `envFrom`) to define the name-value pair of environment variables.

6. Create a node port type service `lamp-service` to expose the web application, nodePort must be `30008`.
7. Create a service for mysql named `mysql-service` and its port must be `3306`.
8. We already have `/tmp/index.php` file on `jump_host` server.
a) Copy this file into httpd container under Apache document root i.e `/app` and replace the dummy values for mysql related variables with the environment variables you have set for mysql related parameters. Please make sure you do not hard code the mysql related details in this file, you must use the environment variables to fetch those values.
b) You must be able to access this `index.php` on node port `30008` at the end, please note that you should see `Connected successfully` message while accessing this page.

---

Create the configmap from specified file:
```bash
$ cat php.ini 
variables_order = "EGPCS"

$ kubectl create configmap php-config --from-file=php.ini
configmap/php-config created
```

Create the secret:
```bash
$ kubectl create secret generic my-secret \
--from-literal=MYSQL_ROOT_PASSWORD=admin123 \
--from-literal=MYSQL_DATABASE=kodekloud \
--from-literal=MYSQL_USER=adin \
--from-literal=MYSQL_PASSWORD=pass123 \
--from-literal=MYSQL_HOST=mysql-service
secret/my-secret created
```

Create the manifest, apply and verify:
```bash
$ cat lamp-stack.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: lamp-wp
  name: lamp-wp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: lamp-wp
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: lamp-wp
    spec:
      containers:
      - name: httpd-php-container
        image: webdevops/php-apache:alpine-3-php7
        resources: {}
        ports:
        - containerPort: 80
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: MYSQL_DATABASE
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: MYSQL_USER
        - name: MYSQL_HOST
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: MYSQL_HOST
        volumeMounts:
        - name: php-ini
          mountPath: /opt/docker/etc/php/php.ini
          subPath: php.ini
      
      - name: mysql-container
        image: mysql:5.6
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: MYSQL_DATABASE
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: MYSQL_USER
        - name: MYSQL_HOST
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: MYSQL_HOST
        ports:
        - containerPort: 3306
        resources: {}
        
      volumes:
      - name: php-ini
        configMap:
          name: php-config
status: {}
---
apiVersion: v1
kind: Service
metadata:
  name: lamp-service
spec:
  type: NodePort
  selector:
    app: lamp-wp
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30008
---
apiVersion: v1
kind: Service
metadata:
  name: mysql-service
spec:
  selector:
    app: lamp-wp
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306

$ kubectl apply -f lamp-stack.yaml 
deployment.apps/lamp-wp created
service/lamp-service created
service/mysql-service created

$ kubectl get all
NAME                           READY   STATUS    RESTARTS   AGE
pod/lamp-wp-5484f5875f-pfrl8   2/2     Running   0          2m4s

NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/kubernetes      ClusterIP   10.96.0.1       <none>        443/TCP        67m
service/lamp-service    NodePort    10.96.38.159    <none>        80:30008/TCP   2m4s
service/mysql-service   ClusterIP   10.96.128.121   <none>        3306/TCP       2m4s

NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/lamp-wp   1/1     1            1           2m4s

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/lamp-wp-5484f5875f   1         1         1       2m4s
```

Check the index.php file:
```bash
$ cat /tmp/index.php 
<?php
$dbname = 'dbname';
$dbuser = 'dbuser';
$dbpass = 'dbpass';
$dbhost = 'dbhost';

$connect = mysqli_connect($dbhost, $dbuser, $dbpass) or die("Unable to Connect to '$dbhost'");

$test_query = "SHOW TABLES FROM $dbname";
$result = mysqli_query($test_query);

if ($result->connect_error) {
   die("Connection failed: " . $conn->connect_error);
}
  echo "Connected successfully";
```

Edit following lines:
```php
$dbname = $_ENV["MYSQL_DATABASE"];
$dbuser = $_ENV["MYSQL_USER"];
$dbpass = $_ENV["MYSQL_PASSWORD"];
$dbhost = $_ENV["MYSQL_HOST"];
```

Copy the file and verify:
```bash 
$ kubectl cp /tmp/index.php lamp-wp-5484f5875f-pfrl8:/app -c httpd-php-container

$ kubectl exec -it lamp-wp-5484f5875f-pfrl8 -c httpd-php-container -- sh
/ # ls -l app/
total 4
-rw-r--r--    1 root     root           434 Feb 23 19:11 index.php
``` 

![k8s-3-2](https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/608e6080-d49c-4bf1-a789-adf52b30fb99)
✅

## 3.3 Init Containers in Kubernetes

There are some applications that need to be deployed on Kubernetes cluster and these apps have some pre-requisites where some configurations need to be changed before deploying the app container. Some of these changes cannot be made inside the images so the DevOps team has come up with a solution to use init containers to perform these tasks during deployment. Below is a sample scenario that the team is going to test first.

Create a Deployment named as `ic-deploy-datacenter`.

Configure spec as replicas should be `1`, labels app should be `ic-datacenter`, template's metadata lables app should be the same `ic-datacenter`.

The initContainers should be named as `ic-msg-datacenter`, use image `debian`, preferably with `latest` tag and use command `'/bin/bash', '-c' and 'echo Init Done - Welcome to xFusionCorp Industries > /ic/ecommerce'`. The volume mount should be named as `ic-volume-datacenter` and mount path should be `/ic`.

Main container should be named as `ic-main-datacenter`, use image `debian`, preferably with `latest` tag and use command `'/bin/bash', '-c' and 'while true; do cat /ic/ecommerce; sleep 5; done'`. The volume mount should be named as `ic-volume-datacenter` and mount path should be `/ic`.

Volume to be named as `ic-volume-datacenter` and it should be an `emptyDir` type.

---

Create the manifest and apply:
```bash
$ cat ic-deploy-datacenter.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ic-deploy-datacenter
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ic-datacenter
  template:
    metadata:
      labels:
        app: ic-datacenter
    spec:
      initContainers:
      - name: ic-msg-datacenter
        image: debian:latest
        command: ["/bin/bash", "-c", "echo 'Init Done - Welcome to xFusionCorp Industries' > /ic/ecommerce"]
        volumeMounts:
        - name: ic-volume-datacenter
          mountPath: /ic
      containers:
      - name: ic-main-datacenter
        image: debian:latest
        command: ["/bin/bash", "-c", "while true; do cat /ic/ecommerce; sleep 5; done"]
        volumeMounts:
        - name: ic-volume-datacenter
          mountPath: /ic
      volumes:
      - name: ic-volume-datacenter
        emptyDir: {}

$ kubectl apply -f ic-deploy-datacenter.yaml 
deployment.apps/ic-deploy-datacenter created
```

Verify:
```bash
$ kubectl get all
NAME                                       READY   STATUS    RESTARTS   AGE
pod/ic-deploy-datacenter-8dbc5f689-prs42   1/1     Running   0          31s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   14m

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/ic-deploy-datacenter   1/1     1            1           31s

NAME                                             DESIRED   CURRENT   READY   AGE
replicaset.apps/ic-deploy-datacenter-8dbc5f689   1         1         1       31s

$ kubectl logs -f deployments/ic-deploy-datacenter 
Defaulted container "ic-main-datacenter" out of: ic-main-datacenter, ic-msg-datacenter (init)
Init Done - Welcome to xFusionCorp Industries
Init Done - Welcome to xFusionCorp Industries
Init Done - Welcome to xFusionCorp Industries
Init Done - Welcome to xFusionCorp Industries
```
✅

## 3.4 Persistent Volumes in Kubernetes

The Nautilus DevOps team is working on a Kubernetes template to deploy a web application on the cluster. There are some requirements to create/use persistent volumes to store the application code, and the template needs to be designed accordingly. Please find more details below:

1. Create a `PersistentVolume` named as `pv-xfusion`. Configure the `spec` as storage class should be `manual`, set capacity to `5Gi`, set access mode to `ReadWriteOnce`, volume type should be `hostPath` and set path to `/mnt/devops` (this directory is already created, you might not be able to access it directly, so you need not to worry about it).
2. Create a `PersistentVolumeClaim` named as `pvc-xfusion`. Configure the `spec` as storage class should be `manual`, request `1Gi` of the storage, set access mode to `ReadWriteOnce`.
3. Create a `pod` named as `pod-xfusion`, mount the persistent volume you created with claim name `pvc-xfusion` at document root of the web server, the container within the pod should be named as `container-xfusion` using image `httpd` with `latest` tag only (remember to mention the tag i.e `httpd:latest`).
4. Create a node port type service named `web-xfusion` using node port `30008` to expose the web server running within the pod.

---

Create the pv.yaml, apply and verify:
```bash
$ cat pv.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-xfusion
spec:
  capacity:
    storage: 5Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  storageClassName: manual
  hostPath:
    path: /mnt/devops

$ kubectl apply -f pv.yaml 
persistentvolume/pv-xfusion created

$ kubectl get pv
NAME         CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
pv-xfusion   5Gi        RWO            Retain           Available           manual                  16s

$ k describe pv pv-xfusion 
Name:            pv-xfusion
Labels:          <none>
Annotations:     <none>
Finalizers:      [kubernetes.io/pv-protection]
StorageClass:    manual
Status:          Available
Claim:           
Reclaim Policy:  Retain
Access Modes:    RWO
VolumeMode:      Filesystem
Capacity:        5Gi
Node Affinity:   <none>
Message:         
Source:
    Type:          HostPath (bare host directory volume)
    Path:          /mnt/devops
    HostPathType:  
Events:            <none>
```

Create the pvc.yaml, apply and verify:
```bash
$ cat pvc.yaml 
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-xfusion
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 1Gi
  storageClassName: manual

$ kubectl apply -f pvc.yaml 
persistentvolumeclaim/pvc-xfusion created

$ k get pvc
NAME          STATUS   VOLUME       CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-xfusion   Bound    pv-xfusion   5Gi        RWO            manual         5s

$ k describe pvc pvc-xfusion 
Name:          pvc-xfusion
Namespace:     default
StorageClass:  manual
Status:        Bound
Volume:        pv-xfusion
Labels:        <none>
Annotations:   pv.kubernetes.io/bind-completed: yes
               pv.kubernetes.io/bound-by-controller: yes
Finalizers:    [kubernetes.io/pvc-protection]
Capacity:      5Gi
Access Modes:  RWO
VolumeMode:    Filesystem
Used By:       <none>
Events:        <none>
```

Create the pod.yaml, apply and verify:
```bash
$ cat pod.yaml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: pod-xfusion
  name: pod-xfusion
spec:
  containers:
  - image: httpd:latest
    name: container-xfusion
    volumeMounts:
      - mountPath: "/var/www/html"
        name: my-volume
  volumes:
    - name: my-volume
      persistentVolumeClaim:
        claimName: pvc-xfusion  

$ kubectl apply -f pod.yaml 
pod/pod-xfusion created

$ kubectl get pods
NAME          READY   STATUS    RESTARTS   AGE
pod-xfusion   1/1     Running   0          23s

$ k describe pod pod-xfusion
Name:             pod-xfusion
...
Labels:           run=pod-xfusion
```

Create the service.yaml, apply and verify:
```bash
$ cat service.yaml 
apiVersion: v1
kind: Service
metadata:
  name: web-xfusion
spec:
  type: NodePort
  selector:
    run: pod-xfusion
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30008
thor@jump_host ~$ k apply -f service.yaml 
service/web-xfusion created

$ k get svc
NAME          TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
kubernetes    ClusterIP   10.96.0.1     <none>        443/TCP        87m
web-xfusion   NodePort    10.96.39.10   <none>        80:30008/TCP   25s
```

![k8s-3-4](https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/97ccff8a-ed7c-4826-97fd-96726217b83c)
✅

## 3.5 Manage Secrets in Kubernetes

The Nautilus DevOps team is working to deploy some tools in Kubernetes cluster. Some of the tools are licence based so that licence information needs to be stored securely within Kubernetes cluster. Therefore, the team wants to utilize Kubernetes secrets to store those secrets. Below you can find more details about the requirements:

1. We already have a secret key file `news.txt` under `/opt` location on `jump host`. Create a `generic secret` named `news`, it should contain the password/license-number present in `news.txt` file.
2. Also create a `pod` named `secret-nautilus`.
3. Configure pod's `spec` as container name should be `secret-container-nautilus`, image should be `fedora` preferably with `latest` tag (remember to mention the tag with image). Use `sleep` command for container so that it remains in running state. Consume the created secret and mount it under `/opt/games` within the container.
4. To verify you can exec into the container `secret-container-nautilus`, to check the secret key under the mounted path `/opt/games`. Before hitting the `Check` button please make sure pod/pods are in running state, also validation can take some time to complete so keep patience.

---

Create the secret and verify:
```bash
$ kubectl create secret generic news --from-file=/opt/news.txt
secret/news created
$ k get secret
NAME   TYPE     DATA   AGE
news   Opaque   1      12s
$ k describe secret news 
Name:         news
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
news.txt:  7 bytes
```

Create pod.yaml, apply and verify:
```bash
$ cat pod.yaml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: secret-nautilus
  name: secret-nautilus
spec:
  containers:
  - image: fedora:latest
    name: secret-container-nautilus
    command: ["sleep", "1000"]
    volumeMounts: 
      - name: secret-volume
        mountPath: "/opt/games"
  volumes:
    - name: secret-volume
      secret: 
        secretName: news

$ kubectl apply -f pod.yaml 
pod/secret-nautilus created

$ kubectl get pods
NAME              READY   STATUS    RESTARTS   AGE
secret-nautilus   1/1     Running   0          18s

$ kubectl exec -it secret-nautilus -c secret-container-nautilus -- cat /opt/games/news.txt
5ecur3
```
✅

## 3.6 Environment Variables in Kubernetes

There are a number of parameters that are used by the applications. We need to define these as environment variables, so that we can use them as needed within different configs. Below is a scenario which needs to be configured on Kubernetes cluster. Please find below more details about the same.

1. Create a `pod` named `envars`.
2. Container name should be `fieldref-container`, use image `busybox` preferable `latest` tag, use command `'sh', '-c'` and args should be

```
'while true; do
      echo -en '/n';
                                  printenv NODE_NAME POD_NAME;
                                  printenv POD_IP POD_SERVICE_ACCOUNT;
              sleep 10;
         done;'
```
`(Note: please take care of indentations)`

3. Define Four environment variables as mentioned below:
- The first `env` should be named as `NODE_NAME`, set valueFrom fieldref and fieldPath should be `spec.nodeName`.
- The second `env` should be named as `POD_NAME`, set valueFrom fieldref and fieldPath should be `metadata.name`.
- The third `env` should be named as `POD_IP`, set valueFrom fieldref and fieldPath should be `status.podIP`.
- The fourth `env` should be named as `POD_SERVICE_ACCOUNT`, set valueFrom fieldref and fieldPath shoulbe be `spec.serviceAccountName`.

4. Set restart policy to `Never`.
5. To check the output, exec into the pod and use `printenv` command.

---

Create manifest, apply and verify:
```bash
$ cat envars.yaml 
apiVersion: v1
kind: Pod
metadata:
  name: envars
spec:
  containers:
  - name: fieldref-container
    image: busybox:latest
    command: ["sh", "-c"]
    args:
    - |
      while true; do
        echo -en '\n';
        printenv NODE_NAME POD_NAME;
        printenv POD_IP POD_SERVICE_ACCOUNT;
        sleep 10;
      done;
    env:
    - name: NODE_NAME
      valueFrom:
        fieldRef:
          fieldPath: spec.nodeName
    - name: POD_NAME
      valueFrom:
        fieldRef:
          fieldPath: metadata.name
    - name: POD_IP
      valueFrom:
        fieldRef:
          fieldPath: status.podIP
    - name: POD_SERVICE_ACCOUNT
      valueFrom:
        fieldRef:
          fieldPath: spec.serviceAccountName
  restartPolicy: Never

$ kubectl apply -f envars.yaml 
pod/envars created

$ kubectl get pods
NAME     READY   STATUS    RESTARTS   AGE
envars   1/1     Running   0          5s
```

Verify the environment variables by usign the exec command:
```bash
$ kubectl exec -it envars -- printenv
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=envars
NODE_NAME=kodekloud-control-plane
POD_NAME=envars
POD_IP=10.244.0.5
POD_SERVICE_ACCOUNT=default
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
TERM=xterm
HOME=/root
```
✅

## 3.7 Kubernetes LEMP Setup

The Nautilus DevOps team want to deploy a static website on Kubernetes cluster. They are going to use Nginx, phpfpm and MySQL for the database. The team had already gathered the requirements and now they want to make this website live. Below you can find more details:

1. Create some secrets for MySQL.
- Create a secret named `mysql-root-pass` wih key/value pairs as below:
```
name: password
value: R00t
```

- Create a secret named `mysql-user-pass` with key/value pairs as below:
```
name: username
value: kodekloud_pop

name: password
value: LQfKeWWxWD
```

- Create a secret named `mysql-db-url` with key/value pairs as below:
```
name: database
value: kodekloud_db6
```

- Create a secret named `mysql-host` with key/value pairs as below:
```
name: host
value: mysql-service
```

2. Create a config map `php-config` for `php.ini` with `variables_order = "EGPCS"` data.
3. Create a deployment named `lemp-wp`.
4. Create two containers under it. First container must be `nginx-php-container` using image `webdevops/php-nginx:alpine-3-php7` and second container must be `mysql-container` from image `mysql:5.6`. Mount `php-config` configmap in nginx container at `/opt/docker/etc/php/php.ini` location.
5. Add some environment variables for both containers:
- `MYSQL_ROOT_PASSWORD`, `MYSQL_DATABASE`, `MYSQL_USER`, `MYSQL_PASSWORD` and `MYSQL_HOST`. Take their values from the secrets you created. Please make sure to use env field (do not use envFrom) to define the name-value pair of environment variables.
6. Create a node port type service `lemp-service` to expose the web application, nodePort must be `30008`.
7. Create a service for mysql named `mysql-service` and its port must be `3306`.
8. We already have a `/tmp/index.php` file on `jump_host` server.
- Copy this file into the `nginx` container under document root i.e `/app` and replace the dummy values for mysql related variables with the environment variables you have set for mysql related parameters. Please make sure you do not hard code the mysql related details in this file, you must use the environment variables to fetch those values.
- Once done, you must be able to access this website using `Website` button on the top bar, please note that you should see `Connected successfully` message while accessing this page.

---

Create the secrets and verify:
```bash
$ kubectl create secret generic mysql-root-pass --from-literal=password=R00t
secret/mysql-root-pass created
$ kubectl create secret generic mysql-user-pass --from-literal=username=kodekloud_pop --from-literal=password=LQfKeWWxWD
secret/mysql-user-pass created
$ kubectl create secret generic mysql-db-url --from-literal=database=kodekloud_db6
secret/mysql-db-url created
$ kubectl create secret generic mysql-host --from-literal=host=mysql-service
secret/mysql-host created

$ kubectl get secret
NAME              TYPE     DATA   AGE
mysql-db-url      Opaque   1      59s
mysql-host        Opaque   1      54s
mysql-root-pass   Opaque   1      70s
mysql-user-pass   Opaque   2      66s
```

Create the configmap from php.ini:
```bash
$ cat php.ini
variables_order = "EGPCS"

$ kubectl create cm php-config --from-file=php.ini
configmap/php-config created

$ k get cm
NAME               DATA   AGE
kube-root-ca.crt   1      15m
php-config         1      9s

$ k describe cm php-config 
Name:         php-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
php.ini:
----
variables_order = "EGPCS"


BinaryData
====

Events:  <none>
```

Create the deployment.yaml file, apply and verify:
```bash
$ cat deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: lemp-wp
  name: lemp-wp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: lemp-wp
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: lemp-wp
    spec:
      containers:
      - image: webdevops/php-nginx:alpine-3-php7
        name: nginx-php-container
        volumeMounts:
          - mountPath: /opt/docker/etc/php/php.ini
            name: php-ini
            subPath: php.ini
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
        - name: MYSQL_HOST
          valueFrom: 
            secretKeyRef: 
              name: mysql-host
              key: host
      - image: mysql:5.6
        name: mysql-container
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
        - name: MYSQL_HOST
          valueFrom: 
            secretKeyRef: 
              name: mysql-host
              key: host
      volumes:
        - name: php-ini
          configMap: 
            name: php-config

$ kubectl apply -f deployment.yaml
deployment.apps/lemp-wp created

$ k get pods
NAME                       READY   STATUS    RESTARTS   AGE
lemp-wp-5cc5566d69-662r8   2/2     Running   0          35s
```

Create services.yaml, apply and verify:
```bash
$ cat services.yaml 
apiVersion: v1
kind: Service
metadata:
  name: lemp-service
spec:
  type: NodePort
  selector:
    app: lemp-wp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
    nodePort: 30008
---
apiVersion: v1
kind: Service
metadata:
  name: mysql-service
spec:
  selector:
    app: lemp-wp
  ports:
  - protocol: TCP
    port: 3306
    targetPort: 3306

$ k apply -f services.yaml 
service/lemp-service created
service/mysql-service created

$ k get svc
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes      ClusterIP   10.96.0.1       <none>        443/TCP        17m
lemp-service    NodePort    10.96.106.199   <none>        80:30008/TCP   9s
mysql-service   ClusterIP   10.96.82.49     <none>        3306/TCP       9s
```

Copy the file into specified path inside the container:
```bash
$ kubectl cp /tmp/index.php lemp-wp-5cc5566d69-662r8:/app -c nginx-php-container
```

Check the file:
```bash
$ kubectl exec -it lemp-wp-5cc5566d69-662r8 -- sh
/ # vi app/index.php
<?php
$dbname = 'dbname';
$dbuser = 'dbuser';
$dbpass = 'dbpass';
$dbhost = 'dbhost';

$connect = mysqli_connect($dbhost, $dbuser, $dbpass) or die("Unable to Connect to '$dbhost'");

$test_query = "SHOW TABLES FROM $dbname";
$result = mysqli_query($test_query);

if ($result->connect_error) {
   die("Connection failed: " . $conn->connect_error);
}
  echo "Connected successfully";
```

Edit following lines:
```php
$dbname = $_ENV['MYSQL_DATABASE'];
$dbuser = $_ENV['MYSQL_USER'];
$dbpass = $_ENV['MYSQL_PASSWORD'];
$dbhost = $_ENV['MYSQL_HOST'];
```

![k8s-3-7](https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/db41444e-ad7a-4843-a92d-6433280c8b08)
✅

## 3.8 Kubernetes Troubleshooting

One of the Nautilus DevOps team members was working on to update an existing Kubernetes template. Somehow, he made some mistakes in the template and it is failing while applying. We need to fix this as soon as possible, so take a look into it and make sure you are able to apply it without any issues. Also, do not remove any component from the template like pods/deployments/volumes etc.

`/home/thor/mysql_deployment.yml` is the template that needs to be fixed.

---

Try to apply manifest to see where is the problem:
```bash
$ k apply -f mysql_deployment.yml 
[resource mapping not found for name: "mysql-pv" namespace: "" from "mysql_deployment.yml": no matches for kind "Persistentvolume" in version "apps/v1"
ensure CRDs are installed first, resource mapping not found for name: "mysql-pv-claim" namespace: "" from "mysql_deployment.yml": no matches for kind "Persistentvolumeclaim" in version "apps/v1"
ensure CRDs are installed first, error parsing mysql_deployment.yml: error converting YAML to JSON: yaml: line 28: mapping values are not allowed in this context]
Error from server (BadRequest): error when creating "mysql_deployment.yml": Service in version "v1" cannot be handled as a Service: strict decoding error: unknown field "metadata.app"
```

Incorrect version:
<details>
<summary>Incorrect version</summary>

```bash
$ cat mysql_deployment.yml 
apiVersion: apps/v1 
kind: Persistentvolume 
metadata:
  name: mysql-pv
  labels:
    type: local
spec:
  storageClassName: standard       
  capacity:
    storage: 250Mi
  accessModes: ReadWriteOnce 
  hostPath:                       
    path: "/mnt/data"
  persistentVolumeReclaimPolicy: 
    - Retain   
---    
apiVersion: apps/v1 
kind: Persistentvolumeclaim       
metadata:                          
  name: mysql-pv-claim
  labels:
    app: mysql-app 
spec:                              
  storageClassName: standard       
  accessModes: ReadWriteOnce             
  resources:
    requests:
      storage: 250MB 
---
apiVersion: v1                    
kind: Service                      
metadata:
  name: mysql         
  labels:             
  app: mysql-app  
spec:
  type: NodePort
  ports:
    - targetPort: 3306
      port: 3306
      nodePort: 30011
  selector:                       
    app: mysql_app
    tier: mysql
---
apiVersion: app/v1 
kind: Deployment                    
metadata:
  name: mysql-deployment           
  labels:                         
  app: mysql-app   
spec:
  selector:
    matchlabels:                  
    app: mysql-app 
    tier: mysql 
  strategy:
    type: Recreate 
  template:         
    metadata:
      labels:        
        app: mysql-app
        tier: mysql
    spec:            
      containers:
      - image: mysql:5.6 
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
        ports:
        - containerPort: 3306              
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage  
          mountPath: /var/lib/mysql
      volumes:                        
      - name: mysql-persistent-storage
          persistentVolumeClaim: 
          claimName: mysql-pv-claim
```
</details>

Correct version:
```bash
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
  labels:
    type: local
spec:
  storageClassName: standard
  capacity:
    storage: 250Mi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
  persistentVolumeReclaimPolicy: Retain
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
  labels:
    app: mysql-app
spec:
  storageClassName: standard
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 250Mi
---
apiVersion: v1
kind: Service
metadata:
  name: mysql
  labels:
    app: mysql-app
spec:
  type: NodePort
  ports:
    - port: 3306
      targetPort: 3306
      nodePort: 30011
  selector:
    app: mysql-app
    tier: mysql
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-deployment
  labels:
    app: mysql-app
spec:
  selector:
    matchLabels:
      app: mysql-app
      tier: mysql
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: mysql-app
        tier: mysql
    spec:
      containers:
        - name: mysql
          image: mysql:5.6
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
          ports:
            - containerPort: 3306
          volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
      volumes:
        - name: mysql-persistent-storage
          persistentVolumeClaim:
            claimName: mysql-pv-claim
```

Apply and verify:
```bash
$ k apply -f mysql_deployment.yml 
persistentvolume/mysql-pv created
persistentvolumeclaim/mysql-pv-claim created
service/mysql created
deployment.apps/mysql-deployment created

$ k get all
NAME                                    READY   STATUS    RESTARTS   AGE
pod/mysql-deployment-7fd8bcd754-5qwqt   1/1     Running   0          20s

NAME                 TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)          AGE
service/kubernetes   ClusterIP   10.96.0.1     <none>        443/TCP          24m
service/mysql        NodePort    10.96.16.53   <none>        3306:30011/TCP   20s

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/mysql-deployment   1/1     1            1           20s

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/mysql-deployment-7fd8bcd754   1         1         1       20s
```
✅

## 3.9 Deploy Iron Gallery App on Kubernetes

There is an iron gallery app that the Nautilus DevOps team was developing. They have recently customized the app and are going to deploy the same on the Kubernetes cluster. Below you can find more details:

1. Create a namespace `iron-namespace-devops`
2. Create a deployment `iron-gallery-deployment-devops` for `iron gallery` under the same namespace you created.
  - Labels `run` should be `iron-gallery`.
  - Replicas count should be `1`.
  - Selector's matchLabels `run` should be `iron-gallery`.
  - Template labels `run` should be `iron-gallery` under metadata.
  - The container should be named as `iron-gallery-container-devops`, use `kodekloud/irongallery:2.0` image ( use exact image name / tag ).
  - Resources limits for memory should be `100Mi` and for CPU should be `50m`.
  - First volumeMount name should be `config`, its mountPath should be `/usr/share/nginx/html/data`.
  - Second volumeMount name should be `images`, its mountPath should be `/usr/share/nginx/html/uploads`.
  - First volume name should be `config` and give it `emptyDir` and second volume name should be `images`, also give it `emptyDir`.
3. Create a deployment `iron-db-deployment-devops` for `iron db` under the same namespace.
  - Labels `db` should be `mariadb`.
  - Replicas count should be `1`.
  - Selector's matchLabels `db` should be `mariadb`.
  - Template labels `db` should be `mariadb` under metadata.
  - The container name should be `iron-db-container-devops`, use `kodekloud/irondb:2.0` image ( use exact image name / tag ).
  - Define environment, set `MYSQL_DATABASE` its value should be `database_blog`, set `MYSQL_ROOT_PASSWORD` and `MYSQL_PASSWORD` value should be with some complex passwords for DB connections, and `MYSQL_USER` value should be any custom user ( except root ).
  - Volume mount name should be `db` and its mountPath should be `/var/lib/mysql`. Volume name should be `db` and give it an `emptyDir`.
4. Create a service for `iron db` which should be named `iron-db-service-devops` under the same namespace. Configure spec as selector's db should be `mariadb`. Protocol should be `TCP`, port and targetPort should be `3306` and its type should be `ClusterIP`.
5. Create a service for `iron gallery` which should be named `iron-gallery-service-devops` under the same namespace. Configure spec as selector's run should be `iron-gallery`. Protocol should be `TCP`, port and targetPort should be `80`, nodePort should be `32678` and its type should be `NodePort`.

---

Create the namespace:
```bash
$ kubectl create namespace iron-namespace-devops
namespace/iron-namespace-devops created
```

Create the depl1.yaml and apply:
```bash
$ cat depl1.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    run: iron-gallery
  name: iron-gallery-deployment-devops
  namespace: iron-namespace-devops
spec:
  replicas: 1
  selector:
    matchLabels:
      run: iron-gallery
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        run: iron-gallery
    spec:
      containers:
      - image: kodekloud/irongallery:2.0
        name: iron-gallery-container-devops
        resources:
          limits: 
            cpu: "50m"
            memory: "100Mi"
        volumeMounts: 
        - mountPath: /usr/share/nginx/html/data
          name: config
        - mountPath: /usr/share/nginx/html/uploads
          name: images
      volumes:
      - name: config
        emptyDir: 
          sizeLimit: 500Mi
      - name: images
        emptyDir: 
          sizeLimit: 500Mi

$ k apply -f depl1.yaml 
deployment.apps/iron-gallery-deployment-devops created
```

Create depl2.yaml, apply and verify: 
```bash
$ cat depl2.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    db: mariadb
  name: iron-db-deployment-devops
  namespace: iron-namespace-devops
spec:
  replicas: 1
  selector:
    matchLabels:
      db: mariadb
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        db: mariadb
    spec:
      containers:
      - image: kodekloud/irondb:2.0
        name: iron-db-container-devops
        env: 
        - name: MYSQL_DATABASE 
          value: database_blog
        - name: MYSQL_ROOT_PASSWORD 
          value: gljh!oho174814
        - name: MYSQL_PASSWORD 
          value: 890q27dgi*mf9
        - name: MYSQL_USER 
          value: adin
        volumeMounts: 
        - mountPath: /var/lib/mysql
          name: db
      volumes:
      - name: db
        emptyDir: 
          sizeLimit: 500Mi

$ kubectl apply -f depl2.yaml 
deployment.apps/iron-db-deployment-devops created

$ kubectl get pods -n iron-namespace-devops
NAME                                              READY   STATUS    RESTARTS   AGE
iron-db-deployment-devops-c68999797-kx62l         1/1     Running   0          98s
iron-gallery-deployment-devops-7b867b75d7-v9zkd   1/1     Running   0          9m6s
```

Expose the deployment and verify:
```bash
$ kubectl expose deployment iron-db-deployment-devops -n iron-namespace-devops --name=iron-db-service-devops --port=3306
service/iron-db-service-devops exposed

$ kubectl describe service iron-db-service-devops -n iron-namespace-devops
Name:              iron-db-service-devops
Namespace:         iron-namespace-devops
Labels:            db=mariadb
Annotations:       <none>
Selector:          db=mariadb
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.96.75.233
IPs:               10.96.75.233
Port:              <unset>  3306/TCP
TargetPort:        3306/TCP
Endpoints:         10.244.0.6:3306
Session Affinity:  None
Events:            <none>
```

Create iron-gallery-service.yaml, apply and verify:
```bash
$ cat iron-gallery-service.yaml 
apiVersion: v1
kind: Service
metadata:
  name: iron-gallery-service-devops
  namespace: iron-namespace-devops
spec:
  selector:
    run: iron-gallery
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 32678
  type: NodePort

$ kubectl apply -f iron-gallery-service.yaml 
service/iron-gallery-service-devops created

$ k get services -n iron-namespace-devops
NAME                          TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
iron-db-service-devops        ClusterIP   10.96.75.233   <none>        3306/TCP       2m5s
iron-gallery-service-devops   NodePort    10.96.60.158   <none>        80:32678/TCP   23s

$ kubectl describe service iron-gallery-service-devops -n iron-namespace-devops
Name:                     iron-gallery-service-devops
Namespace:                iron-namespace-devops
Labels:                   <none>
Annotations:              <none>
Selector:                 run=iron-gallery
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.96.60.158
IPs:                      10.96.60.158
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32678/TCP
Endpoints:                10.244.0.5:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```
✅

## 3.10 Fix Python App Deployed on Kubernetes Cluster

One of the DevOps engineers was trying to deploy a python app on Kubernetes cluster. Unfortunately, due to some mis-configuration, the application is not coming up. Please take a look into it and fix the issues. Application should be accessible on the specified nodePort.

1. The deployment name is `python-deployment-devops`, its using `poroko/flask-demo-app` image. The deployment and service of this app is already deployed.
2. nodePort should be `32345` and targetPort should be python flask app's default port.

---

Check the status of resources:
```bash
$ kubectl get deployments
NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
python-deployment-nautilus   0/1     1            0           60s

$ kubectl get pods
NAME                                         READY   STATUS         RESTARTS   AGE
python-deployment-nautilus-94c6999b7-8zbq4   0/1     ErrImagePull   0          63s
```

See the events of the pod:
```bash
$ kubectl describe pod python-deployment-nautilus-94c6999b7-8zbq4 | grep Events -A 20
Events:
  Type     Reason     Age                    From               Message
  ----     ------     ----                   ----               -------
  Normal   Scheduled  5m14s                  default-scheduler  Successfully assigned default/python-deployment-nautilus-94c6999b7-8zbq4 to kodekloud-control-plane
  Normal   Pulling    3m41s (x4 over 5m13s)  kubelet            Pulling image "poroko/flask-app-demo"
  Warning  Failed     3m41s (x4 over 5m13s)  kubelet            Failed to pull image "poroko/flask-app-demo": rpc error: code = Unknown desc = failed to pull and unpack image "docker.io/poroko/flask-app-demo:latest": failed to resolve reference "docker.io/poroko/flask-app-demo:latest": pull access denied, repository does not exist or may require authorization: server message: insufficient_scope: authorization failed
  Warning  Failed     3m41s (x4 over 5m13s)  kubelet            Error: ErrImagePull
  Warning  Failed     3m29s (x6 over 5m13s)  kubelet            Error: ImagePullBackOff
  Normal   BackOff    5s (x20 over 5m13s)    kubelet            Back-off pulling image "poroko/flask-app-demo"
```

Edit the image, `- image: poroko/flask-demo-app` instead of `- image: poroko/flask-app-demo`, then verify:
```bash
$ k edit deployment python-deployment-nautilus 
deployment.apps/python-deployment-nautilus edited

$ k get pods
NAME                                         READY   STATUS    RESTARTS   AGE
python-deployment-nautilus-784b84b9f-kmmfb   1/1     Running   0          77s
```

Check the services:
```bash
$ k get svc
NAME                      TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
kubernetes                ClusterIP   10.96.0.1      <none>        443/TCP          16m
python-service-nautilus   NodePort    10.96.254.70   <none>        8080:32345/TCP   9m15s
```

Change port and targetPort from `8080` to `5000`: 
```bash
$ k edit service python-service-nautilus 
service/python-service-nautilus edited
```

![k8s3-10](https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/dfb0e036-4519-4b8b-bbf8-320984444b6f)
✅

![k8s3s](https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/f7f6ec0b-d350-48d3-a377-f18a8f30f9ac)