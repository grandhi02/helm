# Helm Install with Generate Name Flag

## Step-01: Introduction
- `--generate-name` flag for `helm install` is very very important option
- This is one of the good to know option from `helm install` perspective
- When we are implementing DevOps Pipelines, if we want to generate the names of our releases without throwing duplicate release errors we can use this setting. 

## Step-02: Install helm with --generate-name flag
```t
# Install helm with --generate-name flag
helm install <repo_name_in_your_local_desktop/chart_name> --generate-name
helm install stacksimplify/mychart1 --generate-name
```
#### Example
# Generate chart and list Helm Releases
```t
PS C:\Users\grandhiv\helloworld> helm install stacksimplify/mychart3 --generate-name
NAME: mychart3-1710334532
LAST DEPLOYED: Wed Mar 13 18:25:40 2024
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services mychart3-1710334532)      
  export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT
PS C:\Users\grandhiv\helloworld>
PS C:\Users\grandhiv\helloworld> helm list
NAME                    NAMESPACE       REVISION        UPDATED     STATUS          CHART                APP VERSION
mychart3-1710334532     default         1         2024-03-13 18:25:40.931795 +0530 IST    deployed     mychart3-1.0.0 
PS C:\Users\grandhiv\helloworld> 

# Helm Status
helm status mychart1-1689683948 
helm status mychart1-1689683948 --show-resources

# Access Application
http://localhost:31231
```
## Step-03: Uninstall Helm Release
```t
# Uninstall Helm Release
helm uninstall <RELEASE-NAME>
helm uninstall mychart1-1689683948
```

