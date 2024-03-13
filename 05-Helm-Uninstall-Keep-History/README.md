# Helm Uninstall Keep History 

## Step-01: Introduction
- We will learn to uninstall Helm Release in a most effective way (best practice) so that we don't loose the history of our Helm Release
- **Important Note:** This demo is in continuation to previous release demo

## Step-02: Uninstall Helm Release with --keep-history Flag
```t
# List Helm Releases
helm list
helm list --superseded
helm list --deployed

# List Release History
helm history myapp101

# Uninstall Helm Release with --keep-history Flag
helm uninstall <RELEASE-NAME> --keep-history
helm uninstall myapp101 --keep-history
```

```t
PS C:\Users\grandhiv\helloworld> helm uninstall myapp2 --keep-history
PS C:\Users\grandhiv\helloworld> helm list
# No output
PS C:\Users\grandhiv\helloworld>
PS C:\Users\grandhiv\helloworld> helm history myapp2
REVISION        UPDATED                         STATUS          CHART           APP VERSION     DESCRIPTION
1               Wed Mar 13 17:18:48 2024        superseded      mychart2-0.2.0  2.0.0           Install complete
2               Wed Mar 13 17:27:49 2024        superseded      mychart2-0.4.0  4.0.0           Upgrade complete
3               Wed Mar 13 17:30:21 2024        superseded      mychart2-0.2.0  2.0.0           Upgrade complete
4               Wed Mar 13 17:44:05 2024        uninstalled     mychart2-0.4.0  4.0.0        Uninstallation complete
PS C:\Users\grandhiv\helloworld>
PS C:\Users\grandhiv\helloworld>
```

# Rollback Helm Uninstalled Release
```t
helm rollback <RELEASE> [REVISION] [flags]
helm rollback myapp2 3
Observation: It should rollback to specific revision number from revision history
```
Example:
```t
PS C:\Users\grandhiv\helloworld> helm rollback myapp2 3
Rollback was a success! Happy Helming!
PS C:\Users\grandhiv\helloworld> helm history myapp2   
REVISION        UPDATED                         STATUS          CHART           APP VERSION     DESCRIPTION
1               Wed Mar 13 17:18:48 2024        superseded      mychart2-0.2.0  2.0.0           Install complete
2               Wed Mar 13 17:27:49 2024        superseded      mychart2-0.4.0  4.0.0           Upgrade complete
3               Wed Mar 13 17:30:21 2024        superseded      mychart2-0.2.0  2.0.0           Upgrade complete
4               Wed Mar 13 17:44:05 2024        uninstalled     mychart2-0.4.0  4.0.0         Uninstallation complete
5               Wed Mar 13 18:06:01 2024        deployed        mychart2-0.2.0  2.0.0           Rollback to 3
PS C:\Users\grandhiv\helloworld> 

```

# List Helm Releases which are uninstalled
helm list --uninstalled
Observation:
We should see uninstalled release
```t
PS C:\Users\grandhiv\helloworld> helm list --uninstalled
NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
myapp2  default         7               2024-03-13 18:06:01.4968148 +0530 IST   uninstalled     mychart2-0.2.0  2.0.0
PS C:\Users\grandhiv\helloworld>
```

# helm status command
helm status myapp2
Observation:
1. works only when we use --keep-history flag
2. We can see all the details of release with "Status: Uninstalled"
```t
PS C:\Users\grandhiv\helloworld> helm status myapp2
NAME: myapp2
LAST DEPLOYED: Wed Mar 13 18:06:01 2024
NAMESPACE: default
STATUS: uninstalled
REVISION: 7
NOTES:
1. Get the application URL by running these commands:
  export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services myapp2-mychart2)
  export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT
PS C:\Users\grandhiv\helloworld>
```

## Step-03: Rollback Uninstalled Release
```t
# List Release History
helm history myapp2


# List Helm Releases
helm list

# List Kubernetes Resources
kubectl get pods
kubectl get svc

# List Kubernetes Resources Deployed as part of this Helm Release
helm status myapp101 --show-resources

# Access Application 
http://localhost:31232
```

## Step-04: Uninstall Helm Release - NO FLAGS
```t
# List Helm Releases
helm list

# Uninstall Helm Release
helm uninstall <RELEASE-NAME>
helm uninstall myapp101

# List Helm Releases which are uninstalled
helm list --uninstalled
Observation:
We should not see uninstalled release, this command will completely remove the release and its all references

# helm status command
helm status myapp101
Observation:
As the release is permanently removed, we dont get an error "Error: release: not found"

# List Helm History
helm history myapp101
```

## Step-05: Rollback Uninstalled Release
```t
# Rollback Helm Uninstalled Release
helm rollback <RELEASE> [REVISION] [flags]
helm rollback myapp101 1 
Observation: 
Should throw error "Error: release: not found"
```

## Step-06: Best Practice for Helm Uninstall
- It is recommended to always use `--keep-history Flag` for following reasons
- Keeping Track of uninstalled releases
- Quick Rollback if that Release is required
