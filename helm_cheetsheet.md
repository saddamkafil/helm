# Helm Cheetsheet


### Signup or signin in to Jfrog Artifactory 
https://cloudinfra.jfrog.io/ui/login/

# login into Linux machine where you can manage you kubernetes Cluster

1. Downloading helm

        $ wget https://get.helm.sh/helm-v3.8.2-linux-amd64.tar.gz
        Saving to: ‘helm-v3.8.2-linux-amd64.tar.gz’
        2022-04-21 02:03:07 (88.7 MB/s) - ‘helm-v3.8.2-linux-amd64.tar.gz’ saved [13633605/13633605]

        $ ls -l
        total 13316
        -rw-r--r-- 1 root root 13633605 Apr 13 17:57 helm-v3.8.2-linux-amd64.tar.gz
        
1. how to extarct Helm zip file
        
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
        
# Creating Demo helm Charts

1. Creating Demo charts with the name diamond

        $ helm create diamond
        Creating diamond

        $ ls -l
        drwxr-xr-x 4 root root 93 Apr 21 02:04 diamond

        $ cd diamond/

        $ ls -l
        total 8
        drwxr-xr-x 2 root root    6 Apr 21 02:04 charts
        -rw-r--r-- 1 root root 1143 Apr 21 02:04 Chart.yaml
        drwxr-xr-x 3 root root  162 Apr 21 02:04 templates
        -rw-r--r-- 1 root root 1874 Apr 21 02:04 values.yaml

## Installing  Diamond Chart with the name dragon release

        $ helm install dragon diamond
        NAME: dragon
        LAST DEPLOYED: Thu Apr 21 02:05:23 2022
        NAMESPACE: default
        STATUS: deployed
        REVISION: 1
        NOTES:
        Get the application URL by running these commands:
          export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=diamond,app.kubernetes.io/instance=dragon" -o jsonpath="{.items[0].metadata.name}")
          export CONTAINER_PORT=$(kubectl get pod --namespace default $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
          echo "Visit http://127.0.0.1:8080 to use your application"
          kubectl --namespace default port-forward $POD_NAME 8080:$CONTAINER_PORT

1. Checking newly deployed diamond pods

        $ kubectl get pods
        NAME                              READY   STATUS    RESTARTS   AGE
        dragon-diamond-5c8df5bc97-7s498   1/1     Running   0          9s


1. how to check newly deployed dragon release

        $ helm list
        NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
        dragon  default         1               2022-04-21 02:05:23.334441334 +0000 UTC deployed        diamond-0.1.0   1.16.0

1. How to add new Helm repo

        $ helm repo add stable https://charts.helm.sh/stable
        "stable" has been added to your repositories
        
1. How to list all repos

        $ helm repo list
        NAME    URL
        stable  https://charts.helm.sh/stable


1. How to search remote repo charts from stable

        $ helm search repo nginx
        NAME                            CHART VERSION   APP VERSION     DESCRIPTION
        stable/nginx-ingress            1.41.3          v0.34.1         DEPRECATED! An nginx Ingress controller that us...
        stable/nginx-ldapauth-proxy     0.1.6           1.13.5          DEPRECATED - nginx proxy with ldapauth
        stable/nginx-lego               0.3.1                           Chart for nginx-ingress-controller and kube-lego
        stable/gcloud-endpoints         0.1.2           1               DEPRECATED Develop, deploy, protect and monitor...


1. How to add new jfrog repo 

        $  helm repo add dragon https://cloudinfra.jfrog.io/artifactory/api/helm/dragon --username xxxxxxx@gmail.com --password api_xxxxxxxxxxxxxxxxxx
        "dragon" has been added to your repositories

1. How to List all existing helm repos

        $  helm repo list
        NAME    URL
        stable  https://charts.helm.sh/stable
        dragon  https://cloudinfra.jfrog.io/artifactory/api/helm/dragon

1. How to update local repos yaml from remote repo
 
        $  helm repo update
        Hang tight while we grab the latest from your chart repositories...
        ...Successfully got an update from the "stable" chart repository
        ...Successfully got an update from the "dragon" chart repository
        Update Complete. ⎈Happy Helming!⎈



1. How to search remote chart from newly added helm repos

        $ helm search repo tomcat
        NAME                    CHART VERSION   APP VERSION     DESCRIPTION
        dragon/bitnami/tomcat   9.2.10          10.0.6          Chart for Apache Tomcat
        dragon/stable/tomcat    0.4.3           7.0             DEPRECATED - Deploy a basic tomcat application ...
        stable/tomcat           0.4.3           7.0             DEPRECATED - Deploy a basic tomcat application ...





1. How to search new charts from stable remote repo 

        $ helm install tomcat stable/tomcat
        WARNING: This chart is deprecated
        NAME: tomcat
        LAST DEPLOYED: Thu Apr 21 02:26:37 2022
        NAMESPACE: default
        STATUS: deployed
        REVISION: 1
        TEST SUITE: None
        NOTES:
        Get the application URL by running these commands:
             NOTE: It may take a few minutes for the LoadBalancer IP to be available.
                   You can watch the status of by running 'kubectl get svc -w tomcat'
          export SERVICE_IP=$(kubectl get svc --namespace default tomcat -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')
          echo http://$SERVICE_IP:


        $ kubectl get pods
        NAME                              READY   STATUS    RESTARTS   AGE
        dragon-diamond-5c8df5bc97-7s498   1/1     Running   0          22m
        tomcat-bf4bc978c-rpnxj            1/1     Running   0          48s



## Removing / Uninstalling helm charts 

1. How to Uninstall deployed charts

        $ helm list
        NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
        dragon  default         1               2022-04-21 02:05:23.334441334 +0000 UTC deployed        diamond-0.1.0   1.16.0
        tomcat  default         1               2022-04-21 02:26:37.619094814 +0000 UTC deployed        tomcat-0.4.3    7.0

        $ helm uninstall tomcat
        release "tomcat" uninstalled

        $ helm list
        NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
        dragon  default         1               2022-04-21 02:05:23.334441334 +0000 UTC deployed        diamond-0.1.0   1.16.0

        $ kubectl get pods
        NAME                              READY   STATUS    RESTARTS   AGE
        dragon-diamond-5c8df5bc97-7s498   1/1     Running   0          24m


1. How to delete dragon repo fron local

        $ helm repo remove dragon
        "dragon" has been removed from your repositories

        $ helm repo list
        NAME    URL
        stable  https://charts.helm.sh/stable




## Pushing Helm charts to Jfrog repo
 Note: Please create remote repository in Jfrog


1. Creating two charts with version 1.0 and 1.1 

        $ helm package mychart
        Successfully packaged chart and saved it to: /root/mychart-0.1.0.tgz

        $ helm package mychart
        Successfully packaged chart and saved it to: /root/mychart-0.1.1.tgz

1. Pushing 1.0 and 1.1 charts to jfrog remote repos

        $ curl -H "X-JFrog-Art-Api:37tKDAKNjDLfqcvzozWmy" -T mychart-0.1.0.tgz "https://cloudinfra.jfrog.io/artifactory/todayhelm-helm/mychart-0.1.0.tgz"
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

        $  curl -H "X-JFrog-Art-Api:37tKDAKNjDLfqcvzozWmy" -T mychart-0.1.1.tgz "https://cloudinfra.jfrog.io/artifactory/todayhelm-helm/mychart-0.1.1.tgz"
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


1. Now update your local repo using helm repo update

        $ helm repo update
        Hang tight while we grab the latest from your chart repositories...
        ...Successfully got an update from the "dragon" chart repository
        Update Complete. ⎈Happy Helming!⎈

1. How to search your pushed charts from remote repo

        $ helm search repo dragon/mychart
        NAME            CHART VERSION   APP VERSION     DESCRIPTION
        dragon/mychart  0.1.1           1.16.0          A Helm chart for Kubernetes

1. How to search all pushed release from remote repo charts

        $ helm search repo dragon/mychart --versions
        NAME            CHART VERSION   APP VERSION     DESCRIPTION
        dragon/mychart  0.1.1           1.16.0          A Helm chart for Kubernetes
        dragon/mychart  0.1.0           1.16.0          A Helm chart for Kubernetes


1. How to install remote repo specific charts version

        $ helm install demo dragon/mychart --version 0.1.0
        NAME: demo
        LAST DEPLOYED: Sat Apr 23 01:53:14 2022
        NAMESPACE: default
        STATUS: deployed
        REVISION: 1
        NOTES:
        Get the application URL by running these commands:
          export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=mychart,app.kubernetes.io/instance=demo" -o jsonpath="{.items[0].metadata.name}")
          export CONTAINER_PORT=$(kubectl get pod --namespace default $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
          echo "Visit http://127.0.0.1:8080 to use your application"
          kubectl --namespace default port-forward $POD_NAME 8080:$CONTAINER_PORT

1. Validating 

        $ kubectl get pods
        NAME                            READY   STATUS    RESTARTS   AGE
        demo-mychart-6df56fb8df-6pzdq   1/1     Running   0          9s


# Upgrading Charts
Note: modify required chnages in values.yaml and charts.yaml now create package and push it into remote repo.  

1. How to upgrade existing charts from jfrog repo

        $ helm upgrade demo dragon/mychart --version 0.1.1
        Release "demo" has been upgraded. Happy Helming!
        NAME: demo
        LAST DEPLOYED: Sat Apr 23 01:54:07 2022
        NAMESPACE: default
        STATUS: deployed
        REVISION: 2
        NOTES:
        Get the application URL by running these commands:
          export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=mychart,app.kubernetes.io/instance=demo" -o jsonpath="{.items[0].metadata.name}")
          export CONTAINER_PORT=$(kubectl get pod --namespace default $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
          echo "Visit http://127.0.0.1:8080 to use your application"
          kubectl --namespace default port-forward $POD_NAME 8080:$CONTAINER_PORT
  
 1. Validation 
 
        $ helm list
        NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
        demo    default         2               2022-04-23 01:54:07.035631876 +0000 UTC deployed        mychart-0.1.1   1.16.0


        $ helm upgrade demo dragon/mychart --version 0.1.1
        Release "demo" has been upgraded. Happy Helming!
        NAME: demo
        LAST DEPLOYED: Sat Apr 23 01:58:28 2022
        NAMESPACE: default
        STATUS: deployed
        REVISION: 4
        NOTES:
        Get the application URL by running these commands:
          export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=mychart,app.kubernetes.io/instance=demo" -o jsonpath="{.items[0].metadata.name}")
          export CONTAINER_PORT=$(kubectl get pod --namespace default $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
          echo "Visit http://127.0.0.1:8080 to use your application"
          kubectl --namespace default port-forward $POD_NAME 8080:$CONTAINER_PORT


        $ helm list
        NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
        demo    default         4               2022-04-23 01:58:28.187773903 +0000 UTC deployed        mychart-0.1.1   latest

1. How to rollback chart to specific revision

        $ helm rollback demo 2
        Rollback was a success! Happy Helming!

