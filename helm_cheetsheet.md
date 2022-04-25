# Helm Cheetsheet


### Signup or signin in to Jfrog Artifactory 
https://cloudinfra.jfrog.io/ui/login/

# login  into Linux machine where you can manage you kubernetes Cluster

1. Downloading helm

        $ wget https://get.helm.sh/helm-v3.8.2-linux-amd64.tar.gz
        Saving to: ‘helm-v3.8.2-linux-amd64.tar.gz’
        2022-04-21 02:03:07 (88.7 MB/s) - ‘helm-v3.8.2-linux-amd64.tar.gz’ saved [13633605/13633605]

        $ ls -l
        total 13316
        -rw-r--r-- 1 root root 13633605 Apr 13 17:57 helm-v3.8.2-linux-amd64.tar.gz
        
1. Extrating Helm gz file
        
        $ tar -xvf helm-v3.8.2-linux-amd64.tar.gz
        linux-amd64/
        linux-amd64/helm
        llinux-amd64/LICENSE
        linux-amd64/README.md

        $ ls -l
        total 13316
        -rw-r--r-- 1 root root 13633605 Apr 13 17:57 helm-v3.8.2-linux-amd64.tar.gz
        drwxr-xr-x 2 3434 3434       50 Apr 13 17:56 linux-amd64

        $ cd linux-amd64/

        $ ls -l
        total 44036
        -rwxr-xr-x 1 3434 3434 45076480 Apr 13 17:41 helm
        -rw-r--r-- 1 3434 3434    11373 Apr 13 17:56 LICENSE
        -rw-r--r-- 1 3434 3434     3367 Apr 13 17:56 README.md
        

1. Copying executable Helm binary to bin directory
        
        $ cp helm /usr/bin/
        
1. Checking Helm Version 
        
        $ helm version
        version.BuildInfo{Version:"v3.8.2", GitCommit:"6e3701edea09e5d55a8ca2aae03a68917630e91b", GitTreeState:"clean", GoVersion:"go1.17.5"}
        

[root@ip-172-31-4-56 ~]# helm repo list
Error: no repositories to show


[root@ip-172-31-4-56 ~]# helm create diamond
Creating diamond

[root@ip-172-31-4-56 ~]# ls -l
total 0
drwxr-xr-x 4 root root 93 Apr 21 02:04 diamond

[root@ip-172-31-4-56 ~]# cd diamond/


[root@ip-172-31-4-56 diamond]# ls -l
total 8
drwxr-xr-x 2 root root    6 Apr 21 02:04 charts
-rw-r--r-- 1 root root 1143 Apr 21 02:04 Chart.yaml
drwxr-xr-x 3 root root  162 Apr 21 02:04 templates
-rw-r--r-- 1 root root 1874 Apr 21 02:04 values.yaml

[root@ip-172-31-4-56 diamond]# vi values.yaml


[root@ip-172-31-4-56 diamond]# cd ..

[root@ip-172-31-4-56 ~]# ls -l
total 0
drwxr-xr-x 4 root root 93 Apr 21 02:04 diamond


[root@ip-172-31-4-56 ~]# helm install dragon diamond
NAME: dragon
LAST DEPLOYED: Thu Apr 21 02:05:23 2022
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=diamond,app.kubernetes.io/instance=dragon" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace default $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace default port-forward $POD_NAME 8080:$CONTAINER_PORT


[root@ip-172-31-4-56 ~]# kubectl get pods
NAME                              READY   STATUS              RESTARTS   AGE
dragon-diamond-5c8df5bc97-7s498   0/1     ContainerCreating   0          4s


[root@ip-172-31-4-56 ~]# kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
dragon-diamond-5c8df5bc97-7s498   1/1     Running   0          9s

[root@ip-172-31-4-56 ~]# kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
dragon-diamond-5c8df5bc97-7s498   1/1     Running   0          10s

[root@ip-172-31-4-56 ~]# kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
dragon-diamond-5c8df5bc97-7s498   1/1     Running   0          10s


[root@ip-172-31-4-56 ~]# helm list
NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
dragon  default         1               2022-04-21 02:05:23.334441334 +0000 UTC deployed        diamond-0.1.0   1.16.0

[root@ip-172-31-4-56 ~]# ls -l
total 0
drwxr-xr-x 4 root root 93 Apr 21 02:04 diamond

[root@ip-172-31-4-56 ~]# helm list
NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
dragon  default         1               2022-04-21 02:05:23.334441334 +0000 UTC deployed        diamond-0.1.0   1.16.0


[root@ip-172-31-4-56 ~]# helm repo add stable https://charts.helm.sh/stable
"stable" has been added to your repositories

[root@ip-172-31-4-56 ~]# helm repo list
NAME    URL
stable  https://charts.helm.sh/stable


[root@ip-172-31-4-56 ~]# helm search repo nginx
NAME                            CHART VERSION   APP VERSION     DESCRIPTION
stable/nginx-ingress            1.41.3          v0.34.1         DEPRECATED! An nginx Ingress controller that us...
stable/nginx-ldapauth-proxy     0.1.6           1.13.5          DEPRECATED - nginx proxy with ldapauth
stable/nginx-lego               0.3.1                           Chart for nginx-ingress-controller and kube-lego
stable/gcloud-endpoints         0.1.2           1               DEPRECATED Develop, deploy, protect and monitor...


[root@ip-172-31-4-56 ~]# helm repo list
NAME    URL
stable  https://charts.helm.sh/stable


[root@ip-172-31-4-56 ~]# helm repo list
NAME    URL
stable  https://charts.helm.sh/stable


[root@ip-172-31-4-56 ~]# helm repo add dragon https://cloudinfra.jfrog.io/artifactory/api/helm/dragon --username xxxxxxx@gmail.com --password api_xxxxxxxxxxxxxxxxxx
"dragon" has been added to your repositories

[root@ip-172-31-4-56 ~]# helm repo list
NAME    URL
stable  https://charts.helm.sh/stable
dragon  https://cloudinfra.jfrog.io/artifactory/api/helm/dragon


[root@ip-172-31-4-56 ~]# ls -l
total 0
drwxr-xr-x 4 root root 93 Apr 21 02:04 diamond


[root@ip-172-31-4-56 ~]# helm repo list
NAME    URL
stable  https://charts.helm.sh/stable
dragon  https://cloudinfra.jfrog.io/artifactory/api/helm/dragon


[root@ip-172-31-4-56 ~]# ls -l
total 0
drwxr-xr-x 4 root root 93 Apr 21 02:04 diamond


[root@ip-172-31-4-56 ~]# helm repo update
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "stable" chart repository
...Successfully got an update from the "dragon" chart repository
Update Complete. ⎈Happy Helming!⎈



[root@ip-172-31-4-56 ~]# helm search repo tomcat
NAME                    CHART VERSION   APP VERSION     DESCRIPTION
dragon/bitnami/tomcat   9.2.10          10.0.6          Chart for Apache Tomcat
dragon/stable/tomcat    0.4.3           7.0             DEPRECATED - Deploy a basic tomcat application ...
stable/tomcat           0.4.3           7.0             DEPRECATED - Deploy a basic tomcat application ...


[root@ip-172-31-4-56 ~]# ls -l
total 0
drwxr-xr-x 4 root root 93 Apr 21 02:04 diamond


[root@ip-172-31-4-56 ~]# helm repo list
NAME    URL
stable  https://charts.helm.sh/stable
dragon  https://cloudinfra.jfrog.io/artifactory/api/helm/dragon



[root@ip-172-31-4-56 ~]# helm install tomcat stable/tomcat
WARNING: This chart is deprecated
NAME: tomcat
LAST DEPLOYED: Thu Apr 21 02:26:37 2022
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
1. Get the application URL by running these commands:
     NOTE: It may take a few minutes for the LoadBalancer IP to be available.
           You can watch the status of by running 'kubectl get svc -w tomcat'
  export SERVICE_IP=$(kubectl get svc --namespace default tomcat -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')
  echo http://$SERVICE_IP:



[root@ip-172-31-4-56 ~]# ls -l
total 0
drwxr-xr-x 4 root root 93 Apr 21 02:04 diamond


[root@ip-172-31-4-56 ~]# kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
dragon-diamond-5c8df5bc97-7s498   1/1     Running   0          22m
tomcat-bf4bc978c-rpnxj            0/1     Running   0          48s


[root@ip-172-31-4-56 ~]# helm repo list
NAME    URL
stable  https://charts.helm.sh/stable
dragon  https://cloudinfra.jfrog.io/artifactory/api/helm/dragon


[root@ip-172-31-4-56 ~]# helm search repo tomcat
NAME                    CHART VERSION   APP VERSION     DESCRIPTION
dragon/bitnami/tomcat   9.2.10          10.0.6          Chart for Apache Tomcat
dragon/stable/tomcat    0.4.3           7.0             DEPRECATED - Deploy a basic tomcat application ...
stable/tomcat           0.4.3           7.0             DEPRECATED - Deploy a basic tomcat application ...


[root@ip-172-31-4-56 ~]# helm repo list
NAME    URL
stable  https://charts.helm.sh/stable
dragon  https://cloudinfra.jfrog.io/artifactory/api/helm/dragon


[root@ip-172-31-4-56 ~]# helm repo remove dragon
"dragon" has been removed from your repositories


[root@ip-172-31-4-56 ~]# helm repo list
NAME    URL
stable  https://charts.helm.sh/stable


[root@ip-172-31-4-56 ~]# helm search repo tomcat
NAME            CHART VERSION   APP VERSION     DESCRIPTION
stable/tomcat   0.4.3           7.0             DEPRECATED - Deploy a basic tomcat application ...


[root@ip-172-31-4-56 ~]# helm list
NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
dragon  default         1               2022-04-21 02:05:23.334441334 +0000 UTC deployed        diamond-0.1.0   1.16.0
tomcat  default         1               2022-04-21 02:26:37.619094814 +0000 UTC deployed        tomcat-0.4.3    7.0

[root@ip-172-31-4-56 ~]# helm uninstall tomcat
release "tomcat" uninstalled


[root@ip-172-31-4-56 ~]# helm list
NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
dragon  default         1               2022-04-21 02:05:23.334441334 +0000 UTC deployed        diamond-0.1.0   1.16.0

[root@ip-172-31-4-56 ~]# kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
dragon-diamond-5c8df5bc97-7s498   1/1     Running   0          24m


[root@ip-172-31-4-56 ~]# helm search repo tomcat
NAME            CHART VERSION   APP VERSION     DESCRIPTION
stable/tomcat   0.4.3           7.0             DEPRECATED - Deploy a basic tomcat application ...

[root@ip-172-31-4-56 ~]# helm install sai stable/tomcat
WARNING: This chart is deprecated
NAME: sai
LAST DEPLOYED: Thu Apr 21 02:31:04 2022
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
1. Get the application URL by running these commands:
     NOTE: It may take a few minutes for the LoadBalancer IP to be available.
           You can watch the status of by running 'kubectl get svc -w sai-tomcat'
  export SERVICE_IP=$(kubectl get svc --namespace default sai-tomcat -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')
  echo http://$SERVICE_IP:
  
  
[root@ip-172-31-4-56 ~]# kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
dragon-diamond-5c8df5bc97-7s498   1/1     Running   0          25m
sai-tomcat-6788bdcd84-dftrb       0/1     Running   0          5s
[root@ip-172-31-4-56 ~]#
[root@ip-172-31-4-56 ~]# ls -l
total 0
drwxr-xr-x 4 root root 93 Apr 21 02:04 diamond


[root@ip-172-31-4-56 ~]# helm search repo tomcat
NAME            CHART VERSION   APP VERSION     DESCRIPTION
stable/tomcat   0.4.3           7.0             DEPRECATED - Deploy a basic tomcat application ...




=============================================
  Day4
============================================

[root@ip-172-31-4-56 ~]# helm package mychart
Successfully packaged chart and saved it to: /root/mychart-0.1.0.tgz

[root@ip-172-31-4-56 ~]# helm package mychart
Successfully packaged chart and saved it to: /root/mychart-0.1.1.tgz


[root@ip-172-31-4-56 ~]# curl -H "X-JFrog-Art-Api:37tKDAKNjDLfqcvzozWmy" -T mychart-0.1.0.tgz "https://cloudinfra.jfrog.io/artifactory/todayhelm-helm/mychart-0.1.0.tgz"
{
  "repo" : "todayhelm-helm-local",
  "path" : "/mychart-0.1.0.tgz",
  "created" : "2022-04-23T01:47:36.319Z",
  "createdBy" : "xxxxxx@gmail.com",
  "downloadUri" : "https://cloudinfra.jfrog.io/artifactory/todayhelm-helm-local/mychart-0.1.0.tgz",
  "mimeType" : "application/x-gzip",
  "size" : "3753",
  "checksums" : {
    "sha1" : "xxxxxxxxxxxxxxxx",
    "md5" : "xxxxxxxxxxxx",
    "sha256" : "xxxxxxxxxxxxxxxx"
  },
  "originalChecksums" : {
    "sha256" : "xxxxxxxxxxxxxxxxxxxxxxxxxx"
  },
  "uri" : "https://cloudinfra.jfrog.io/artifactory/todayhelm-helm-local/mychart-0.1.0.tgz"
}



[root@ip-172-31-4-56 ~]# curl -H "X-JFrog-Art-Api:37tKDAKNjDLfqcvzozWmy" -T mychart-0.1.1.tgz "https://cloudinfra.jfrog.io/artifactory/todayhelm-helm/mychart-0.1.1.tgz"
{
  "repo" : "todayhelm-helm-local",
  "path" : "/mychart-0.1.1.tgz",
  "created" : "2022-04-23T01:48:07.835Z",
  "createdBy" : "xxxxxxx@gmail.com",
  "downloadUri" : "https://cloudinfra.jfrog.io/artifactory/todayhelm-helm-local/mychart-0.1.1.tgz",
  "mimeType" : "application/x-gzip",
  "size" : "3755",
  "checksums" : {
    "sha1" : "xxxxxxx",
    "md5" : "xxxxxxxxxxxxxxxx",
    "sha256" : "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
  },
  "originalChecksums" : {
    "sha256" : "2c63008b252acab1a305ca033fd712c6a8dde6cf9a2c596a9d6df7fe54b8f8b7"
  },
  "uri" : "https://cloudinfra.jfrog.io/artifactory/todayhelm-helm-local/mychart-0.1.1.tgz"



[root@ip-172-31-4-56 ~]# helm repo update
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "dragon" chart repository
Update Complete. ⎈Happy Helming!⎈

[root@ip-172-31-4-56 ~]# helm search repo dragon/mychart
NAME            CHART VERSION   APP VERSION     DESCRIPTION
dragon/mychart  0.1.1           1.16.0          A Helm chart for Kubernetes

[root@ip-172-31-4-56 ~]# helm search repo dragon/mychart --versions
NAME            CHART VERSION   APP VERSION     DESCRIPTION
dragon/mychart  0.1.1           1.16.0          A Helm chart for Kubernetes
dragon/mychart  0.1.0           1.16.0          A Helm chart for Kubernetes


[root@ip-172-31-4-56 ~]# helm install demo dragon/mychart --version 0.1.0
NAME: demo
LAST DEPLOYED: Sat Apr 23 01:53:14 2022
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=mychart,app.kubernetes.io/instance=demo" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace default $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace default port-forward $POD_NAME 8080:$CONTAINER_PORT


[root@ip-172-31-4-56 ~]# kubectl get pods
NAME                            READY   STATUS              RESTARTS   AGE
demo-mychart-6df56fb8df-6pzdq   0/1     ContainerCreating   0          7s

[root@ip-172-31-4-56 ~]# kubectl get pods
NAME                            READY   STATUS    RESTARTS   AGE
demo-mychart-6df56fb8df-6pzdq   1/1     Running   0          9s


[root@ip-172-31-4-56 ~]# helm upgrade demo dragon/mychart --version 0.1.1
Release "demo" has been upgraded. Happy Helming!
NAME: demo
LAST DEPLOYED: Sat Apr 23 01:54:07 2022
NAMESPACE: default
STATUS: deployed
REVISION: 2
NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=mychart,app.kubernetes.io/instance=demo" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace default $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace default port-forward $POD_NAME 8080:$CONTAINER_PORT
  
  
[root@ip-172-31-4-56 ~]# helm list
NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
demo    default         2               2022-04-23 01:54:07.035631876 +0000 UTC deployed        mychart-0.1.1   1.16.0


[root@ip-172-31-4-56 ~]# helm upgrade demo dragon/mychart --version 0.1.1
Release "demo" has been upgraded. Happy Helming!
NAME: demo
LAST DEPLOYED: Sat Apr 23 01:58:28 2022
NAMESPACE: default
STATUS: deployed
REVISION: 4
NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=mychart,app.kubernetes.io/instance=demo" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace default $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace default port-forward $POD_NAME 8080:$CONTAINER_PORT


[root@ip-172-31-4-56 ~]# helm list
NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
demo    default         4               2022-04-23 01:58:28.187773903 +0000 UTC deployed        mychart-0.1.1   latest

[root@ip-172-31-4-56 ~]# helm rollback demo 2
Rollback was a success! Happy Helming!


[root@ip-172-31-4-56 ~]# helm list
NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
demo    default         5               2022-04-23 02:02:16.603430405 +0000 UTC deployed        mychart-0.1.1   1.16.0

[root@ip-172-31-4-56 ~]# helm search repo dragon/mychart --versions
NAME            CHART VERSION   APP VERSION     DESCRIPTION
dragon/mychart  0.1.1           1.16.0          A Helm chart for Kubernetes
dragon/mychart  0.1.0           1.16.0          A Helm chart for Kubernetes

