# Helm Upgrade with Chart Versions

## Step-01: Introduction
- We are going to learn some additional flags for `helm search repo` command
- We are going to Install and Upgrade Helm Releases using Chart Versions
- In addition, we are going to learn about Helm Rollback 
- helm install
- helm search repo
- helm status
- helm upgrade
- helm rollback
- helm history

## Step-02: Search Helm Repo for mychart2
- [Review mychart2 in Github Repo](https://github.com/stacksimplify/helm-charts/tree/main)
- mychart2 has 4 chart versions (0.1.0, 0.2.0, 0.3.0, 0.4.0)
- mychart2 Chart Versions -> App Version
- 0.1.0 -> 1.0.0
- 0.2.0 -> 2.0.0
- 0.3.0 -> 3.0.0
- 0.4.0 -> 4.0.0
- [Review Artifacthub.io](https://artifacthub.io/packages/helm/stacksimplify/mychart2/)
```t
# Search Helm Repo
helm search repo mychart2
Observation: Should display latest version of mychart2 from stacksimplify helm repo

PS C:\Users\grandhiv\helloworld> helm search repo mychart2
NAME                    CHART VERSION   APP VERSION     DESCRIPTION
stacksimplify/mychart2  0.4.0           4.0.0           A Helm chart for Kubernetes
PS C:\Users\grandhiv\helloworld>



# Search Helm Repo with --versions
helm search repo mychart2 --versions
Observation: Should display all versions of mychart2

PS C:\Users\grandhiv\helloworld> helm search repo mychart2 --versions
NAME                    CHART VERSION   APP VERSION     DESCRIPTION
stacksimplify/mychart2  0.4.0           4.0.0           A Helm chart for Kubernetes
stacksimplify/mychart2  0.3.0           3.0.0           A Helm chart for Kubernetes
stacksimplify/mychart2  0.2.0           2.0.0           A Helm chart for Kubernetes
stacksimplify/mychart2  0.1.0           1.0.0           A Helm chart for Kubernetes
PS C:\Users\grandhiv\helloworld>


# Search Helm Repo with --version
helm search repo mychart2 --version "CHART-VERSIONS"
helm search repo mychart2 --version "0.2.0"
Observation: Should display specified version of helm chart

PS C:\Users\grandhiv\helloworld> helm search repo mychart2 --version 0.2.0
NAME                    CHART VERSION   APP VERSION     DESCRIPTION
stacksimplify/mychart2  0.2.0           2.0.0           A Helm chart for Kubernetes
PS C:\Users\grandhiv\helloworld> 

```

## Step-03: Install Helm Chart by specifying Chart Version
```t
# Install Helm Chart by specifying Chart Version
helm install myapp101 stacksimplify/mychart2 --version "CHART-VERSION"
helm install myapp101 stacksimplify/mychart2 --version "0.1.0"
```
##### if u dont specify the chart-version while installing, it installs the latest version

```t
PS C:\Users\grandhiv\helloworld> helm install myapp2 stacksimplify/mychart2 
NAMESPACE: default
REVISION: 1
1. Get the application URL by running these commands:
  export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services myapp2-mychart2)
  export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT
PS C:\Users\grandhiv\helloworld>
PS C:\Users\grandhiv\helloworld> helm history myapp2
REVISION        UPDATED                         STATUS          CHART           APP VERSION     DESCRIPTION     
1               Wed Mar 13 17:10:28 2024        deployed        mychart2-0.4.0  4.0.0           Install complete
PS C:\Users\grandhiv\helloworld> 
````
#### But if have specified the version, it installs the specified version
```t
PS C:\Users\grandhiv\helloworld> helm install myapp2 stacksimplify/mychart2 --version 0.2.0
NAME: myapp2
LAST DEPLOYED: Wed Mar 13 17:18:48 2024
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services myapp2-mychart2)
  export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT
PS C:\Users\grandhiv\helloworld>
PS C:\Users\grandhiv\helloworld>
PS C:\Users\grandhiv\helloworld>
PS C:\Users\grandhiv\helloworld> helm history myapp2
REVISION        UPDATED                         STATUS          CHART           APP VERSION     DESCRIPTION     
1               Wed Mar 13 17:18:48 2024        deployed        mychart2-0.2.0  2.0.0           Install complete
PS C:\Users\grandhiv\helloworld> 
```
```t
# List Helm Release
helm list 

# List Kubernetes Resources Deployed as part of this Helm Release
helm status myapp2 --show-resources

# Access Application
http://localhost:31232

# View Pod logs
kubectl get pods
kubectl logs -f POD-NAME
```

## Step-04: Helm Upgrade using Chart Version
```t
# Helm Upgrade using Chart Version
helm upgrade myapp2 stacksimplify/mychart2 --version "0.4.0"

# The output : 
PS C:\Users\grandhiv\helloworld> helm upgrade myapp2 stacksimplify/mychart2 --version "0.4.0"
Release "myapp2" has been upgraded. Happy Helming!
NAME: myapp2
LAST DEPLOYED: Wed Mar 13 17:27:49 2024
NAMESPACE: default
STATUS: deployed
REVISION: 2
NOTES:
1. Get the application URL by running these commands:
  export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services myapp2-mychart2)
  export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT
PS C:\Users\grandhiv\helloworld> 

# List Helm Release
helm list

PS C:\Users\grandhiv\helloworld> helm list
NAME       NAMESPACE       REVISION        UPDATED                         STATUS          CHART      APP VERSION
myapp2      default         2   2024-03-13 17:27:49.367344 +0530 IST    deployed        mychart2-0.4.0  4.0.0

PS C:\Users\grandhiv\helloworld> 

# List Kubernetes Resources Deployed as part of this Helm Release
helm status myapp101 --show-resources

# Access Application
http://localhost:31232

# List Release History
helm history myapp101
```

## Step-05: Helm Upgrade without Chart Version

This will upgrade the charts to latest version

```t
# Helm Upgrade using Chart Version
helm upgrade myapp101 stacksimplify/mychart2

# List Helm Release
helm list 

# List Kubernetes Resources Deployed as part of this Helm Release
helm status myapp101 --show-resources

# Access Application
http://localhost:31232
Observation: Should take the latest release which is Appversion 4.0.0, Chart Version 0.4.0 (Which is default or latest Chart version)

# List Release History
helm history myapp101
```

## Step-06: Helm Rollback
- Roll back a release to a previous revision or a specific revision
```t
# Rollback to previous version
helm rollback RELEASE-NAME 
helm rollback myapp101

# List Helm Release
helm list 

# List Kubernetes Resources Deployed as part of this Helm Release
helm status myapp101 --show-resources

# Access Application
http://localhost:31232
Observation: Should see V2 version of Application (Chart Version 0.2.0, AppVersion 2.0.0)

# List Release History
helm history myapp101
```

## Step-07: Helm Rollback to specific Revision
- Roll back a release to a previous revision chart version from current revision chart version if the version is not specified or a specific revision
- 
```t
# Rollback to previous version
helm rollback RELEASE-NAME REVISION
helm rollback myapp101 1

# List Helm Release
helm list 

# List Kubernetes Resources Deployed as part of this Helm Release
helm status myapp101 --show-resources

# Access Application
http://localhost:31232
Observation: Should see V1 version of Application (Chart Version 0.1.0, AppVersion 1.0.0)

# List Release History
helm history myapp101
```



