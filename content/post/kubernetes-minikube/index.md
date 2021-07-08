+++
author = "Anupam Kumar"
title = "Playing Around with Kubernetes in EC2"
date = "2021-07-06"
description = "Setting up a local minikube cluster and using different kubectl commands to create pods and containers"
tags = [
    "kubernetes",
    "minikube",
    "kubectl",
    "pods",
    "containers",
    "ec2",
]
categories = [
    "daily-progress",
]
series = ["Kubernetes", "Kubectl"]
aliases = ["kubernetes-kubectl", "kubernetes-minikube", "k8s-kubectl"]
image = 'kubernetes-logo.png'
+++

&nbsp;

### The Minikube Cluster
It is a local playground to learn or test your code as it would run on a full-fledged Kubernetes Cluster. It creates a small cluster consisting of only one node.\
The command `minikube start` launches the cluster with options for bare-metal, ssh, docker among others. The cluster can be managed with CLI but it also offers a GUI in the web browser, can be launched with `minikube dashboard` command.\
The task of minikube is over here. Now we head on to using the `kubectl` command to manage the cluster.

### Manage the Cluster
Information gathering can be done easily using the command `kubectl get` followed by `pods`, `services`, `deployments`, `replicaset`, etc. A deployment can be easily created (using docker here) by running 
```bash
$ kubectl create deployment <name> --image=<docker-image-name>
deployment.apps/<name> created
```

Now we can view the pods created using command:
```
$ kubectl get pods
NAME                       READY   STATUS    RESTARTS   AGE
<name>-<random-string>     1/1     Running   0          5s
```
There is only one pod and only one replicaset (`kubectl get replicasets`), because that is the default value, can be changed with `--replicas` flag, just like 
```bash
$ kubectl create deployment <name> --image=<docker-image-name> --replicas=3
```

---

Doing things this way would be slightly cumbersome when there were many different pods with different configurations, moreover they are hard to track, hence I will be using an easier way to deploy the cluster - using YAML configuration files.

The following config from [this YouTube video](https://www.youtube.com/watch?v=qmDzcu5uY1I) creates a `nginx` cluster with 3 replicas and exposes the `port 8080`
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-depl
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.16
        ports:
        - containerPort: 8080
```

And it can be deployed using `kubectl apply -f filename.yml`

Above was an example where three replicas were created with one pod each with one container each, we can also create one pod with two containers which have totally different docker images. This YAML configuration from [this helpful website](https://collabnix.github.io/kubelabs/pods101/deploy-your-first-nginx-pod.html) below does exactly that.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  volumes:
  - name: html
    emptyDir: {}
  containers:
  - name: first
    image: nginx
    volumeMounts:
    - name: html
      mountPath: /usr/share/nginx/html
  - name: second
    image: debian
    volumeMounts:
    - name: html
      mountPath: /html
    command: ["/bin/sh", "-c"]
    args:
      - while true; do
          date >> /html/index.html;
          sleep 1;
        done
```

It is a pod configuration that attaches a common volume to both the containers named `html`, which is an empty directory. Then it defines the configuration for the two containers named `first` and `second` with docker images `nginx` and `docker` respectively. In the `first` container, the `html` volume is mounted at `/usr/share/nginx/html` so that nginx would serve content from there, and the `second` container is just a linux destribution that mounts the `html` volume at location `/html` in its file structure and executes the command `/bin/sh -c` with these arguments

```bash
while true; do
  date >> /html/index.html;
  sleep 1;
done
```

This piece of code just appends the `/html/index.html` file and sets it content to be the present datetime.\
Long story short debian writes data to the `index.html` file which is inside the `html` volume, every second, and nginx reads from the same volume

One can create the pod using the YAML file with the following command:
```
$ kubectl create -f nginx-pod.yml
pod/nginx-pod created
```

The pod can be viewed using
```
$ kubectl get pods
NAME        READY   STATUS    RESTARTS   AGE
nginx-pod   2/2     Running   0          6s
```

Now the index.html file can be viewed by running `cat` command inside the container
```bash
$ kubectl exec nginx-pod -c first -- /bin/cat /usr/share/nginx/html/index.html # for the first container
Thu Jul  7 10:41:36 UTC 2021
Thu Jul  7 10:41:37 UTC 2021
Thu Jul  7 10:41:38 UTC 2021
Thu Jul  7 10:41:39 UTC 2021

$ kubectl exec nginx-pod -c second -- /bin/cat /html/index.html # for the second container
Thu Jul  7 10:41:36 UTC 2021
Thu Jul  7 10:41:37 UTC 2021
Thu Jul  7 10:41:38 UTC 2021
Thu Jul  7 10:41:39 UTC 2021
Thu Jul  7 10:41:40 UTC 2021
Thu Jul  7 10:41:41 UTC 2021
Thu Jul  7 10:41:42 UTC 2021
Thu Jul  7 10:41:43 UTC 2021
Thu Jul  7 10:41:44 UTC 2021
Thu Jul  7 10:41:45 UTC 2021
Thu Jul  7 10:41:46 UTC 2021
Thu Jul  7 10:41:47 UTC 2021
Thu Jul  7 10:41:48 UTC 2021
```

`kubectl describe po` command can be used to get detailed information about the pods running in the cluster.

&nbsp;
