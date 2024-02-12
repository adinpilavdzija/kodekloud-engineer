# Kubernetes Level 1 (Tasks 1.1-1.14) 

For all tasks: `Note:` The `kubectl` utility on `jump_host` has been configured to work with the kubernetes cluster.

## 1.1 Create Pods in Kubernetes Cluster

The Nautilus DevOps team has started practicing some pods and services deployment on Kubernetes platform as they are planning to migrate most of their applications on Kubernetes platform. Recently one of the team members has been assigned a task to create a pod as per details mentioned below:
1. Create a pod named `pod-nginx` using `nginx` image with `latest` tag only and remember to mention the tag i.e `nginx:latest`.
2. Labels `app` should be set to `nginx_app`, also container should be named as `nginx-container`.

---

Create the pod.yml:
```bash
$ cat > pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: pod-nginx
  labels:
    app: nginx_app
spec:
  containers:
  - name: nginx-container
    image: nginx:latest
```

Apply and verify:
```bash
$ kubectl apply -f pod.yml
pod/pod-nginx created
$ kubectl get pods
NAME        READY   STATUS    RESTARTS   AGE
pod-nginx   1/1     Running   0          18s
```
✅

## 1.2 Create Deployments in Kubernetes Cluster

The Nautilus DevOps team has started practicing some pods and services deployment on Kubernetes platform, as they are planning to migrate most of their applications on Kubernetes. Recently one of the team members has been assigned a task to create a deployment as per details mentioned below:

Create a deployment named `httpd` to deploy the application `httpd` using the image `httpd:latest` (remember to mention the tag as well)

---

Create the deployment and verify:
```bash
$ kubectl create deployment httpd --image httpd:latest
deployment.apps/httpd created
$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
httpd-69545969bd-qzvcr   1/1     Running   0          22s
$ kubectl get deployment
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
httpd   1/1     1            1           4m17s
thor@jump_host ~$ kubectl describe deployment/httpd
Name:                   httpd
Namespace:              default
CreationTimestamp:      Fri, 22 Dec 2023 22:47:20 +0000
Labels:                 app=httpd
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=httpd
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=httpd
  Containers:
   httpd:
    Image:        httpd:latest
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   httpd-69545969bd (1/1 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  4m31s  deployment-controller  Scaled up replica set httpd-69545969bd to 1
```
✅

## 1.3 Create Namespaces in Kubernetes Cluster

The Nautilus DevOps team is planning to deploy some micro services on Kubernetes platform. The team has already set up a Kubernetes cluster and now they want set up some namespaces, deployments etc. Based on the current requirements, the team has shared some details as below:

Create a namespace named `dev` and create a POD under it; name the pod `dev-nginx-pod` and use `nginx` image with `latest` tag only and remember to mention tag i.e `nginx:latest`.

---

Create a namespace named `dev` and create a pod `dev-nginx-pod`, then apply:
```bash
$ cat dev-nginx-pod.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
---
apiVersion: v1
kind: Pod
metadata:
  name: dev-nginx-pod
  namespace: dev
spec:
  containers:
    - name: nginx-container
      image: nginx:latest

$ kubectl apply -f .
namespace/dev created
pod/dev-nginx-pod created
```

Verify:
```bash
$ kubectl get ns
NAME                 STATUS   AGE
default              Active   61m
dev                  Active   80s
kube-node-lease      Active   61m
kube-public          Active   61m
kube-system          Active   61m
local-path-storage   Active   61m

$ kubectl get all -n dev
NAME                READY   STATUS    RESTARTS   AGE
pod/dev-nginx-pod   1/1     Running   0          98s

$ kubectl describe -n dev pod dev-nginx-pod | grep Image:
    Image:          nginx:latest
```
✅

## 1.4 Set Limits for Resources in Kubernetes

Recently some of the performance issues were observed with some applications hosted on Kubernetes cluster. The Nautilus DevOps team has observed some resources constraints, where some of the applications are running out of resources like memory, cpu etc., and some of the applications are consuming more resources than needed. Therefore, the team has decided to add some limits for resources utilization. Below you can find more details.

Create a pod named `httpd-pod` and a container under it named as `httpd-container`, use `httpd` image with `latest` tag only and remember to mention tag i.e `httpd:latest` and set the following limits:

Requests: Memory: `15Mi`, CPU: `100m`
Limits: Memory: `20Mi`, CPU: `100m`

---

Create the pod.yml and apply:

```bash
$ cat >httpd-pod.yml 
apiVersion: v1
kind: Pod
metadata: 
  name: httpd-pod
spec:
  containers:
  - name: httpd-container
    image: httpd:latest
    resources:
      requests: 
        memory: "15Mi"
        cpu: "100m"
      limits: 
        memory: "20Mi"
        cpu: "100m"

$ kubectl apply -f httpd-pod.yml
pod/httpd-pod created
```

Verify:
```bash
$ kubectl get pods
NAME        READY   STATUS              RESTARTS   AGE
httpd-pod   0/1     ContainerCreating   0          9s
thor@jump_host ~$ kubectl get pods
NAME        READY   STATUS    RESTARTS   AGE
httpd-pod   1/1     Running   0          1m

$ kubectl describe pods httpd-pod | grep Containers: -A 20
Containers:
  httpd-container:
    Container ID:   containerd://91b468aa06af89d6256fa18394b51819bfcb1ef8e11b455651b2f0b4be8d8374
    Image:          httpd:latest
    Image ID:       docker.io/library/httpd@sha256:7765977cf2063fec486b63ddea574faf8fbed285f2b17020fa7ef70a4926cdec
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Sun, 14 Jan 2024 09:14:22 +0000
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     100m
      memory:  20Mi
    Requests:
      cpu:        100m
      memory:     15Mi
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-dj8tv (ro)
```
✅

## 1.5 Rolling Updates in Kubernetes

We have an application running on Kubernetes cluster using nginx web server. The Nautilus application development team has pushed some of the latest changes and those changes need be deployed. The Nautilus DevOps team has created an image `nginx:1.17` with the latest changes.

Perform a rolling update for this application and incorporate `nginx:1.17` image. The deployment name is `nginx-deployment`. Make sure all pods are up and running after the update.

---

Check all the running resources:
```bash
$ kubectl get all
NAME                                   READY   STATUS    RESTARTS   AGE
pod/nginx-deployment-989f57c54-5kf58   1/1     Running   0          5m44s
pod/nginx-deployment-989f57c54-cfz7n   1/1     Running   0          5m44s
pod/nginx-deployment-989f57c54-ddnx8   1/1     Running   0          5m44s

NAME                    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
service/kubernetes      ClusterIP   10.96.0.1      <none>        443/TCP        92m
service/nginx-service   NodePort    10.96.85.237   <none>        80:30008/TCP   5m44s

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deployment   3/3     3            3           5m44s

NAME                                         DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-deployment-989f57c54   3         3         3       5m44s
```

Check the details of deployment in yaml format:
```bash
$ kubectl get deployment nginx-deployment -o yaml
...
spec:
      containers:
      - image: nginx:1.16
        imagePullPolicy: IfNotPresent
        name: nginx-container
```

We need to change image:
```bash
$ kubectl set image deployment/nginx-deployment nginx-container=nginx:1.17
deployment.apps/nginx-deployment image updated
```

Check the status of the rollout update:
```bash
$ kubectl rollout status deployment nginx-deployment
deployment "nginx-deployment" successfully rolled out
```

Check the image tag:
```bash
$ kubectl describe deployment nginx-deployment
Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Thu, 18 Jan 2024 22:44:23 +0000
Labels:                 app=nginx-app
                        type=front-end
Annotations:            deployment.kubernetes.io/revision: 2
Selector:               app=nginx-app
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx-app
  Containers:
   nginx-container:
    Image:        nginx:1.17
```
✅

## 1.6 Rollback a Deployment in Kubernetes

This morning the Nautilus DevOps team rolled out a new release for one of the applications. Recently one of the customers logged a complaint which seems to be about a bug related to the recent release. Therefore, the team wants to rollback the recent release.

There is a deployment named `nginx-deployment`; roll it back to the previous revision.

---

Check the running resources:
```bash
$ kubectl get deployment
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           1m

$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-665bf769f5-78gs6   1/1     Running   0          2m
nginx-deployment-665bf769f5-f25p5   1/1     Running   0          2m
nginx-deployment-665bf769f5-s6p5x   1/1     Running   0          2m
```

Rollback to a previous realease and verify:
```bash
$ kubectl rollout undo deployment nginx-deployment 
deployment.apps/nginx-deployment rolled back

$ kubectl rollout status deployment nginx-deployment
deployment "nginx-deployment" successfully rolled out
```
✅

## 1.7 Create Replicaset in Kubernetes Cluster

The Nautilus DevOps team is going to deploy some applications on kubernetes cluster as they are planning to migrate some of their existing applications there. Recently one of the team members has been assigned a task to write a template as per details mentioned below:

Create a ReplicaSet using `nginx` image with `latest` tag only and remember to mention tag i.e `nginx:latest` and name it as `nginx-replicaset`.

Labels `app` should be `nginx_app`, labels `type` should be `front-end`.

The container should be named as `nginx-container`; also make sure replicas counts are `4`.

---

Create the yml file, apply and verify:
```bash
$ cat >nginx.yml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replicaset
  labels:
    app: nginx_app
    type: front-end
spec:
  replicas: 4
  selector:
    matchLabels:
      app: nginx_app
      type: front-end
  template:
    metadata:
      labels:
        app: nginx_app
        type: front-end
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest

$ kubectl apply -f nginx.yml
replicaset.apps/nginx-replicaset created

$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
nginx-replicaset-gmjk2   1/1     Running   0          36s
nginx-replicaset-mrtlz   1/1     Running   0          36s
nginx-replicaset-qs54p   1/1     Running   0          36s
nginx-replicaset-zf59f   1/1     Running   0          36s
```
✅

## 1.8 Create Cronjobs in Kubernetes

There are some jobs/tasks that need to be run regularly on different schedules. Currently the Nautilus DevOps team is working on developing some scripts that will be executed on different schedules, but for the time being the team is creating some cron jobs in Kubernetes cluster with some dummy commands (which will be replaced by original scripts later). Create a cronjob as per details given below:

1. Create a cronjob named `devops`.
2. Set Its schedule to something like `*/9 * * * *`, you set any schedule for now.
3. Container name should be `cron-devops`.
4. Use `nginx` image with `latest tag` only and remember to mention the tag i.e `nginx:latest`.
5. Run a dummy command `echo Welcome to xfusioncorp!`.
6. Ensure restart policy is `OnFailure`.

---

Check if there are any running cronjobs:
```bash
$ kubectl get cronjob -A
No resources found
```

Create the cronjob and apply:
```bash
$ cat >devops-cronjob.yml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: devops
spec:
  schedule: "*/9 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: cron-devops
            image: nginx:latest
            command: ["echo", "Welcome to xfusioncorp!"]
          restartPolicy: OnFailure

$ kubectl apply -f devops-cronjob.yml 
cronjob.batch/devops created
```

Verify:
```bash
$ kubectl get pod
NAME                    READY   STATUS      RESTARTS   AGE
devops-28430880-nh9gr   0/1     Completed   0          2m29s

$ kubectl get jobs
NAME              COMPLETIONS   DURATION   AGE
devops-28430880   1/1           31s        2m41s

$ kubectl logs devops-28430880-nh9gr
Welcome to xfusioncorp!
```
✅

## 1.9 Countdown job in Kubernetes

The Nautilus DevOps team is working on to create few jobs in Kubernetes cluster. They might come up with some real scripts/commands to use, but for now they are preparing the templates and testing the jobs with dummy commands. Please create a job template as per details given below:

- Create a job `countdown-xfusion`.
- The spec template should be named as `countdown-xfusion` (under metadata), and the container should be named as `container-countdown-xfusion`
- Use image `ubuntu` with `latest` tag only and remember to mention tag i.e `ubuntu:latest`, and restart policy should be `Never`.
- Use command `sleep 5`

---

Check the running resources:
```bash
$ kubectl get all 
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   4h31m
```

Create the manifest and apply:
```bash
$ cat >countdown-xfusion.yml
apiVersion: batch/v1
kind: Job
metadata:
  name: countdown-xfusion
spec:
  template:
    metadata:
      name: countdown-xfusion
    spec:
      containers:
      - name: container-countdown-xfusion
        image: ubuntu:latest
        command: ["sleep", "5"]
      restartPolicy: Never

$ kubectl apply -f countdown-xfusion.yml 
job.batch/countdown-xfusion created
```

Verify:
```bash
$ kubectl get job
NAME                COMPLETIONS   DURATION   AGE
countdown-xfusion   1/1           14s        16s

$ kubectl get pods
NAME                      READY   STATUS      RESTARTS   AGE
countdown-xfusion-bcwnx   0/1     Completed   0          3m3s
```
✅

## 1.10 Kubernetes Time Check Pod

The Nautilus DevOps team want to create a time check pod in a particular Kubernetes namespace and record the logs. This might be initially used only for testing purposes, but later can be implemented in an existing cluster. Please find more details below about the task and perform it.

- Create a pod called `time-check` in the `datacenter` namespace. This pod should run a container called `time-check`, container should use the `busybox` image with `latest tag` only and remember to mention tag i.e `busybox:latest`.
- Create a config map called `time-config` with the data `TIME_FREQ=10` in the same namespace.
- The `time-check` container should run the command: `while true; do date; sleep $TIME_FREQ;done` and should write the result to the location `/opt/sysops/time/time-check.log`. Remember you will also need to add an environmental variable `TIME_FREQ` in the container, which should pick value from the config map `TIME_FREQ` key.
- Create a volume `log-volume` and mount the same on `/opt/sysops/time` within the container.

---

Check the namespaces and running resources:
```bash
$ kubectl get namespace
NAME                 STATUS   AGE
default              Active   47m
kube-node-lease      Active   47m
kube-public          Active   47m
kube-system          Active   47m
local-path-storage   Active   47m

$ kubectl get all
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   47m
```

Create the namespace datacenter:
```bash
$ kubectl create namespace datacenter
namespace/datacenter created
```

Create the time-config.yaml:
```bash
$ cat >time-config.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: time-config
  namespace: datacenter
data:
  TIME_FREQ: "10"
```

Create the time-check.yaml:
```bash
$ cat >time-check.yaml
apiVersion: v1
kind: Pod
metadata:
  name: time-check
  namespace: datacenter
spec:
  containers:
  - name: time-check
    image: busybox:latest
    command:
    - sh
    - -c
    - "while true; do date; sleep $TIME_FREQ; done >> /opt/sysops/time/time-check.log"
    env:
    - name: TIME_FREQ
      valueFrom:
        configMapKeyRef:
          name: time-config
          key: TIME_FREQ
    volumeMounts:
    - name: log-volume
      mountPath: /opt/sysops/time
  volumes:
  - name: log-volume
```

Apply:
```bash
$ kubectl apply -f time-config.yaml 
configmap/time-config created
thor@jump_host ~$ kubectl apply -f time-check.yaml
pod/time-check created
```

Verify:
```bash
$ kubectl get pods -n datacenter
NAME         READY   STATUS    RESTARTS   AGE
time-check   1/1     Running   0          26s

thor@jump_host ~$ kubectl get configmaps -n datacenter
NAME               DATA   AGE
kube-root-ca.crt   1      4m52s
time-configmap     1      71s

$ kubectl exec -n datacenter -it time-check -- ls -lrt /opt/sysops/time
total 4
-rw-r--r--    1 root     root           319 Feb  6 15:10 time-check.log

$ kubectl exec -n datacenter -it time-check -- tail -10 /opt/sysops/time/time-check.log
Tue Feb  6 15:09:44 UTC 2024
Tue Feb  6 15:09:54 UTC 2024
Tue Feb  6 15:10:04 UTC 2024
Tue Feb  6 15:10:14 UTC 2024
Tue Feb  6 15:10:24 UTC 2024
Tue Feb  6 15:10:34 UTC 2024
Tue Feb  6 15:10:44 UTC 2024
Tue Feb  6 15:10:54 UTC 2024
Tue Feb  6 15:11:04 UTC 2024
Tue Feb  6 15:11:14 UTC 2024
```
✅

## 1.11 Troubleshoot Issue With Pods

One of the junior DevOps team members was working on to deploy a stack on Kubernetes cluster. Somehow the pod is not coming up and its failing with some errors. We need to fix this as soon as possible. Please look into it.

- There is a pod named `webserver` and the container under it is named as `httpd-container`. It is using image `httpd:latest`
- There is a sidecar container as well named `sidecar-container` which is using `ubuntu:latest` image.
- Look into the issue and fix it, make sure pod is in `running` state and you are able to access the app.

---

Check the running pods:
```bash
$ kubectl get pods
NAME        READY   STATUS             RESTARTS   AGE
webserver   1/2     ImagePullBackOff   0          105s
```

Check more details about the pod webserver:
```bash
$ kubectl describe pod webserver
...
Events:
  Type     Reason     Age                  From               Message
  ----     ------     ----                 ----               -------
  Normal   Scheduled  2m21s                default-scheduler  Successfully assigned default/webserver to kodekloud-control-plane
  Normal   Pulling    2m20s                kubelet            Pulling image "ubuntu:latest"
  Normal   Pulled     2m16s                kubelet            Successfully pulled image "ubuntu:latest" in 4.24364794s (4.243664523s including waiting)
  Normal   Created    2m16s                kubelet            Created container sidecar-container
  Normal   Started    2m16s                kubelet            Started container sidecar-container
  Normal   Pulling    96s (x3 over 2m21s)  kubelet            Pulling image "httpd:latests"
  Warning  Failed     95s (x3 over 2m20s)  kubelet            Failed to pull image "httpd:latests": rpc error: code = NotFound desc = failed to pull and unpack image "docker.io/library/httpd:latests": failed to resolve reference "docker.io/library/httpd:latests": docker.io/library/httpd:latests: not found
  Warning  Failed     95s (x3 over 2m20s)  kubelet            Error: ErrImagePull
  Normal   BackOff    56s (x6 over 2m15s)  kubelet            Back-off pulling image "httpd:latests"
  Warning  Failed     56s (x6 over 2m15s)  kubelet            Error: ImagePullBackOff
```

Problem is the image tag 'latests' which we need to change:
```bash
$ kubectl get pod webserver -o yaml > webserver.yaml
$ sed -i 's/latests/latest/g' webserver.yaml
```

Update the pod:
```bash
$ kubectl apply -f webserver.yaml 
Error from server (Conflict): error when applying patch:
...
error when patching "webserver.yaml": Operation cannot be fulfilled on pods "webserver": the object has been modified; please apply your changes to the latest version and try again
```

When updating the pod, we cannot have generated fields such as `creationTimestamp`, `resourceVersion`, `selfLink`, and `uid`, so we need to clean it up:
```bash
$ grep "creationTimestamp\|resourceVersion\|selfLink\|uid" webserver.yaml 
  creationTimestamp: "2024-02-07T16:18:07Z"
  resourceVersion: "1473"
  uid: 30d325a0-1cc7-451c-a127-5ff29db33b3c
$ sed -i '/creationTimestamp:/d; /resourceVersion:/d; /uid:/d' webserver.yaml
$ grep "creationTimestamp\|resourceVersion\|selfLink\|uid" webserver.yaml 

```

Update the pod and verify:
```bash
$ kubectl apply -f webserver.yaml 
pod/webserver configured

$ kubectl get pods
NAME        READY   STATUS    RESTARTS   AGE
webserver   2/2     Running   0          21m
```

![k8s-1-11](https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/f207b60e-84c6-44b5-aebe-90243ff2ba3c)

✅

## 1.12 Update an Existing Deployment in Kubernetes

There is an application deployed on Kubernetes cluster. Recently, the Nautilus application development team developed a new version of the application that needs to be deployed now. As per new updates some new changes need to be made in this existing setup. So update the deployment and service as per details mentioned below:

We already have a deployment named `nginx-deployment` and service named `nginx-service`. Some changes need to be made in this deployment and service, make sure not to delete the deployment and service.

1. Change the service nodeport from `30008` to `32165`
2. Change the replicas count from `1` to `5`
3. Change the image from `nginx:1.19` to `nginx:latest`

---

Check the running resources:
```bash
$ kubectl get all
NAME                                   READY   STATUS    RESTARTS   AGE
pod/nginx-deployment-dc49f85cc-dwl46   1/1     Running   0          76s

NAME                    TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
service/kubernetes      ClusterIP   10.96.0.1     <none>        443/TCP        43m
service/nginx-service   NodePort    10.96.16.52   <none>        80:30008/TCP   76s

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deployment   1/1     1            1           76s

NAME                                         DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-deployment-dc49f85cc   1         1         1       76s
```

Edit the deployment (replicas: 5) (- image: nginx:latest):
```bash
$ kubectl edit deployment nginx-deployment
deployment.apps/nginx-deployment edited
```

Verify:
```bash
$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-854ff588b7-glvff   1/1     Running   0          92s
nginx-deployment-854ff588b7-hqk5n   1/1     Running   0          78s
nginx-deployment-854ff588b7-jpsrp   1/1     Running   0          92s
nginx-deployment-854ff588b7-mj82z   1/1     Running   0          92s
nginx-deployment-854ff588b7-nbxbr   1/1     Running   0          79s
```

Edit the service (- nodePort: 32165):
```bash
$ kubectl edit service nginx-service
service/nginx-service edited
```

Verify:
```bash
$ kubectl get services
NAME            TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
kubernetes      ClusterIP   10.96.0.1     <none>        443/TCP        48m
nginx-service   NodePort    10.96.16.52   <none>        80:32165/TCP   6m11s
```
✅

## 1.13 Replication Controller in Kubernetes

The Nautilus DevOps team wants to create a `ReplicationController` to deploy several pods. They are going to deploy applications on these pods, these applications need highly available infrastructure. Below you can find exact details, create the `ReplicationController` accordingly.

1. Create a `ReplicationController` using `httpd` image, preferably with `latest` tag, and name it as `httpd-replicationcontroller`.
2. Labels `app` should be `httpd_app`, and labels `type` should be `front-end`. The container should be named as `httpd-container` and also make sure replica counts are `3`.

All `pods` should be running state after deployment.

---

Create the ReplicationController and apply:
```bash
$ cat >httpd-replicationcontroller.yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: httpd-replicationcontroller
  labels:
    app: httpd_app
    type: front-end
spec:
  replicas: 3
  selector:
    app: httpd_app
    type: front-end
  template:
    metadata:
      labels:
        app: httpd_app
        type: front-end
    spec:
      containers:
      - name: httpd-container
        image: httpd:latest

$ kubectl apply -f httpd-replicationcontroller.yaml
replicationcontroller/httpd-replicationcontroller created
```

Verify:
```bash
$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
httpd-replicationcontroller-2t25m   1/1     Running   0          45s
httpd-replicationcontroller-g4rzq   1/1     Running   0          45s
httpd-replicationcontroller-tr4l2   1/1     Running   0          45s
```
✅

## 1.14 Fix Issue with Volume Mounts in Kubernetes

We deployed a Nginx and PHP-FPM based setup on Kubernetes cluster last week and it had been working fine so far. This morning one of the team members made a change somewhere which caused some issues, and it stopped working. Please look into the issue and fix it:

The pod name is `nginx-phpfpm` and configmap name is `nginx-config`. Figure out the issue and fix the same.

Once issue is fixed, copy `/home/thor/index.php` file from the `jump host` to the `nginx-container` under nginx document root and you should be able to access the website using `Website` button on top bar.

---

Check the running and existing resources:
```bash
$ kubectl get all
NAME               READY   STATUS    RESTARTS   AGE
pod/nginx-phpfpm   2/2     Running   0          4m4s

NAME                    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes      ClusterIP   10.96.0.1      <none>        443/TCP          14m
service/nginx-service   NodePort    10.96.211.73   <none>        8099:30008/TCP   4m4s

$ kubectl get configmaps
NAME               DATA   AGE
kube-root-ca.crt   1      36m
nginx-config       1      26m
```

Generate the YAML file from the running pod and from the, then review it:
```bash
$ kubectl get pod nginx-phpfpm -o yaml > nginx-phpfpm.yaml
$ kubectl get cm nginx-config -o yaml > nginx-config.yml  
```

Problem is in the root directory in nginx-phpfpm and we need to change it in YAML file then apply:
```bash
$ sed -i 's/\/usr\/share\/nginx\/html/\/var\/www\/html/g' nginx-phpfpm.yaml

$ kubectl apply -f nginx-phpfpm.yaml --force
pod/nginx-phpfpm configured
```

Verify:
```bash
$ kubectl get pods
NAME           READY   STATUS    RESTARTS   AGE
nginx-phpfpm   2/2     Running   0          10s
```

Copy the /home/thor/index.php onto the root directory in the container:
```bash
$ kubectl cp /home/thor/index.php nginx-phpfpm:/var/www/html -c nginx-container  
```

![k8s-1-14](https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/990248b8-4202-41f9-b6ab-1b2f6988426b)

✅

![k8s_level_1](https://github.com/adinpilavdzija/kodekloud-engineer/assets/65655945/4c1887f7-d6d6-453b-afd7-6e8a748159ad)