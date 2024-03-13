# Helm Upgrade with set option

## Step-01: Introduction
- We are going to upgrade the HELM RELEASE using `helm upgrade` command in combination with `--set "image.tag=<DOCKER-IMAGE-TAGS>`
- We will use the following Helm Commands in this demo.
- helm repo 
- helm search repo
- helm install
- helm upgrade
- helm history
- helm status

## Step-02: Custom Helm Repo
### Step-02-01: Review our Custom Helm Repo
- [StackSimplify Helm Repo hosted on GitHub](https://stacksimplify.github.io/helm-charts/)
- [GitHub Repository for StackSimplify Helm Repo](https://github.com/stacksimplify/helm-charts)
- [artifacthub.io](https://artifacthub.io): Search for `stacksimplify`
- [mychart1 from artifacthub.io](https://artifacthub.io/packages/helm/stacksimplify/mychart1)


### Step-02-02: Add Custom Helm Repo
```t
# List Helm Repositories
helm repo list

# Add Helm Repository
helm repo add <DESIRED-NAME> <HELM-REPO-URL>
helm repo add stacksimplify https://stacksimplify.github.io/helm-charts/

# List Helm Repositories
helm repo list

# Search Helm Repository
helm search repo <KEY-WORD>
helm search repo mychart1
```

## Step-03: Install Helm Chart from our Custom Helm Repository
```t
# Install myapp1 Helm Chart
helm install <RELEASE-NAME> <repo_name_in_your_local_desktop/chart_name>
helm install myapp1 stacksimplify/mychart1 
```
## Step-04: List Resources and Access Application in Browser
```t
# List Helm Release
helm ls 
or 
helm list

# List Pods
kubectl get pods

# List Services 
kubectl get svc

# Access Application
http://localhost:<NODE-PORT>
http://localhost:31231
```

## Step-04: Helm Upgrade
- [kubenginx Docker Image with 1.0.0, 2.0.0, 3.0.0, 4.0.0](https://github.com/users/stacksimplify/packages/container/package/kubenginx)
```t
# Review the Docker Image Versions we are using
https://github.com/users/stacksimplify/packages/container/package/kubenginx
Image Tags: 1.0.0, 2.0.0, 3.0.0, 4.0.0

# Helm Upgrade
helm upgrade <RELEASE-NAME> <repo_name_in_your_local_desktop/chart_name> --set <OVERRIDE-VALUE-FROM-values.yaml>
helm upgrade myapp1 stacksimplify/mychart1 --set "image.tag=2.0.0"
```
## Step-05: List Resources after helm upgrade
```t
# List Helm Releases
helm list 
Observation: We should see Revision as 2

# Additional List commands
helm list --superseded
helm list --deployed

# List and Describe Pod
kubectl get pods
kubectl describe pod <POD-NAME> 
Observation: In the Pod Events you should find that "ghcr.io/stacksimplify/kubenginx:2.0.0" is pulled or if already exists on desktop it will be used to create this new pod

# Access Application
http://localhost:<NODE-PORT>
http://localhost:31231
Observation: Version 2 of application should be displayed
```

## Step-06: Do two more helm upgrades - For practice purpose
```t
# Helm Upgrade to 3.0.0
helm upgrade myapp1 kalyan-repo/myapp1 --set "image.tag=3.0.0"

# Access Application
http://localhost:<NODE-PORT>
http://localhost:31231

# Helm Upgrade to 4.0.0
helm upgrade myapp1 kalyan-repo/myapp1 --set "image.tag=4.0.0"

# Access Application
http://localhost:<NODE-PORT>
http://localhost:31231
```

## Step-07: Helm History
- History prints historical revisions for a given release.
```t
# helm history
helm history RELEASE_NAME
helm history myapp1
```

## Step-08: Helm Status
- This command shows the status of a named release. 
```t
# Helm Status
helm status RELEASE_NAME
helm status myapp1

# Helm Status - Show Description (display the description message of the named release)
helm status myapp1 --show-desc    

# Helm Status - Show Resources (display the resources of the named release like the deployments,services, pods etc)
helm status myapp  --show-resources
## The output can be
C:\Users\grandhiv\helloworld> helm status myapp --show-resources
NAME: myapp
LAST DEPLOYED: Wed Mar 13 16:24:35 2024
NAMESPACE: default
REVISION: 3
NAME             TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
myapp-mychart1   NodePort   10.106.209.172   <none>        80:31231/TCP   92m

==> v1/Deployment
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
myapp-mychart1   4/4     4            4           92m

==> v1/Pod(related)
NAME                              READY   STATUS    RESTARTS   AGE
myapp-mychart1-787d958499-6hb5s   1/1     Running   0          13m
myapp-mychart1-787d958499-kzd5m   1/1     Running   0          12m
myapp-mychart1-787d958499-qztqv   1/1     Running   0          13m
myapp-mychart1-787d958499-tcb8w   1/1     Running   0          11m


NOTES:
1. Get the application URL by running these commands:
  export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services myapp-mychart1)
  export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT
```
## Helm Status - revision (display the status of the named release with revision)
```
helm status RELEASE_NAME --revision int
helm status myapp1 --revision 2
```

## Step-09: Uninstall Helm Release
```t
# Uninstall Helm Release
helm uninstall myapp1
![image](https://github.com/grandhi02/helm/assets/45489301/090f9763-e363-4622-979c-2155dfe0a0c1)

```



