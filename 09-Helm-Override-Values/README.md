# Helm Override default values from values.yaml

## Step-01: Introduction
- We will learn the following in this section
    - helm install --set 
    - helm upgrade -f myvalues.yaml
    - **--dry-run:**
    - **--debug:** 
    - helm get values
    - helm get values --revision
    - helm get manifest
    - helm get manifest --revision
    - helm get all
- Discuss about Values Hierarchy

## Step-02: Override default NodePort 31231 with --set
### Step-02-01: Review our mychart1 Helm Chart values.yaml
- [mychart1 values.yaml](https://github.com/stacksimplify/helm-charts/blob/main/mychart1/values.yaml)

### Step-02-02: Learn about --dry-run and --debug flags for helm install command
- Install Helm Chart by overriding NodePort 31231 with 31232
- Helm Install with --dry-run command
This step doesnt create any revision but returns the modified yamls under templates after set operation(here service.nodeport) .We can notice that satatus is pending-install as we are checking with dry-run

helm install myapp2 stacksimplify/mychart1 --set service.nodePort=31232 --dry-run

```t
PS C:\Users\grandhiv> microk8s helm install myapp2 stacksimplify/mychart1 --set service.nodePort=31232 --dry-run
NAME: myapp2
LAST DEPLOYED: Thu Mar 14 11:54:32 2024
NAMESPACE: default
STATUS: pending-install
REVISION: 1
HOOKS:
---
# Source: mychart1/templates/tests/test-connection.yaml
apiVersion: v1
kind: Pod
metadata:
  name: "myapp2-mychart1-test-connection"
  labels:
    helm.sh/chart: mychart1-0.1.0
    app.kubernetes.io/name: mychart1
    app.kubernetes.io/instance: myapp2
    app.kubernetes.io/version: "1.0.0"
    app.kubernetes.io/managed-by: Helm
  annotations:
    "helm.sh/hook": test
spec:
  containers:
    - name: wget
      image: busybox
      command: ['wget']
      args: ['myapp2-mychart1:80']
  restartPolicy: Never
MANIFEST:
---
# Source: mychart1/templates/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp2-mychart1
  labels:
    helm.sh/chart: mychart1-0.1.0
    app.kubernetes.io/name: mychart1
    app.kubernetes.io/instance: myapp2
    app.kubernetes.io/version: "1.0.0"
    app.kubernetes.io/managed-by: Helm
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: http
      protocol: TCP
      nodePort: 31232
      name: http
  selector:
    app.kubernetes.io/name: mychart1
    app.kubernetes.io/instance: myapp2
---
# Source: mychart1/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp2-mychart1
  labels:
    helm.sh/chart: mychart1-0.1.0
    app.kubernetes.io/name: mychart1
    app.kubernetes.io/instance: myapp2
    app.kubernetes.io/version: "1.0.0"
    app.kubernetes.io/managed-by: Helm
spec:
  replicas: 1
  selector:
    matchLabels:
      app.kubernetes.io/name: mychart1
      app.kubernetes.io/instance: myapp2
  template:
    metadata:
      labels:
        app.kubernetes.io/name: mychart1
        app.kubernetes.io/instance: myapp2
    spec:
      containers:
        - name: mychart1
          image: "ghcr.io/stacksimplify/kubenginx:1.0.0"
          imagePullPolicy: IfNotPresent
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http

NOTES:
1. Get the application URL by running these commands:
  export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services myapp2-mychart1)
  export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT
PS C:\Users\grandhiv> 

```
### Helm Install with --dry-run and --debug command

- With debug option, it gives the user supplied details(from set option) and compute supplied details (from values.yaml and chart.yaml).
- The below output is shown for reference.
  
helm install myapp2 stacksimplify/mychart1 --set service.nodePort=31240 --dry-run --debug  

```t
## THE BELOW IS THE SAMPLE OUTPUT WITH DEBUG ADDED
NAME: myapp2
NAMESPACE: default
STATUS: pending-install
REVISION: 1
	USER-SUPPLIED VALUES:
service:
  nodePort: 31240
COMPUTED VALUES:
fullnameOverride: ""
image:
  pullPolicy: IfNotPresent
  repository: ghcr.io/stacksimplify/kubenginx
  tag: ""
nameOverride: ""
podAnnotations: {}
replicaCount: 1
service:
  nodePort: 31240
  port: 80
  type: NodePort
```

### Step-02-03: helm install with --set and test
```t
# Helm Install 
helm install myapp2 stacksimplify/mychart1 --set service.nodePort=31240 

# helm status --show-resources
helm status myapp2 --show-resources
Observation:
We can see that our NodePort service is running on port 31240

# Access Application
http://localhost:31240
```

## Step-03: Review myvalues.yaml
- **myvalues.yaml file location:** 09-Helm-Override-Values/myvalues.yaml
```yaml
# Change-1: change replicas from 1 to 2
replicaCount: 2

# Change-2: Add tag as "2.0.0" which will override the default appversion "1.0.0" from our mychart1
image:
  repository: ghcr.io/stacksimplify/kubenginx
  pullPolicy: IfNotPresent
  # Overrides the image tag whose default is the chart appVersion.
  tag: "2.0.0"

# Change-3: Change nodePort from 31240 to 31250
service:
  type: NodePort
  port: 80
  nodePort: 31250
```

## Step-04: Override NodePort 31240 with -f myvalues.yaml with 
- We can either `-f  myvalues.yaml` or `--values myvalues.yaml`.  Both are valid inputs
```t
# Verify if myvalues.yaml
cd 09-Helm-Override-Values
cat myvalues.yaml

# helm upgrade with --dry-run and --debug commands
helm upgrade myapp2 stacksimplify/mychart1 -f myvalues.yaml --dry-run --debug

# helm upgrade
helm upgrade myapp2 stacksimplify/mychart1 -f myvalues.yaml

# helm status
helm status myapp901 --show-resources
Observation: 
1. Two pods will be running as we changed replicacount to 2
2. Service Node Port will be 31250 

# Access Application
http://localhost:31250
Observation: 
1. We should see V2 application because we have used the "image tag as 2.0.0"
```

## Step-05: helm get values command
- **helm get values:** This command provides the user supplied values( supplied through set opern or the -f flag) file for a given release
```t
# helm get values
helm get values RELEASE_NAME
helm get values myapp901
Observation:
1. Provides the values from current/latest release version 2 from Release myapp901

## Sample Output
$ helm get values myapp2
USER-SUPPLIED VALUES:
image:
  pullPolicy: IfNotPresent
  repository: ghcr.io/stacksimplify/kubenginx
  tag: 2.0.0
replicaCount: 2
service:
  nodePort: 31250
  port: 80
  type: NodePort


# helm history (History prints historical revisions for a given release.)
helm history myapp901


# helm get values with --revision
helm get values RELEASE-NAME --revision int
helm get values myapp2 --revision 1

## Sample Output
Kalyans-MacBook-Pro:09-Helm-Override-Values kalyan$ helm get values myapp901 --revision 1
USER-SUPPLIED VALUES:
service:
  nodePort: 31240
```

## Step-06: helm get manifest command 
- **helm get manifest:** when we install a chart,the updated templates of resource yamls are submitted to Kubernetes API server. So this command fetches those submitted yaml as manifest for a given release of chart.
```t
# helm get manifest
helm get manifest RELEASE-NAME
helm get manifest myapp2

# helm get manifest --revision
helm get manifest RELEASE-NAME --revision int
helm get manifest myapp2 --revision 1
```
- The output can be as below
  ```t
  PS C:\Users\grandhiv> microk8s helm get manifest myapp23
---
```t
# Source: mychart1/templates/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp23-mychart1
  labels:
    helm.sh/chart: mychart1-0.1.0
    app.kubernetes.io/name: mychart1
    app.kubernetes.io/instance: myapp23
    app.kubernetes.io/version: "1.0.0"
    app.kubernetes.io/managed-by: Helm
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: http
      protocol: TCP
      nodePort: 31232
      name: http
  selector:
    app.kubernetes.io/name: mychart1
    app.kubernetes.io/instance: myapp23
---
# Source: mychart1/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp23-mychart1
  labels:
    helm.sh/chart: mychart1-0.1.0
    app.kubernetes.io/name: mychart1
    app.kubernetes.io/instance: myapp23
    app.kubernetes.io/version: "1.0.0"
    app.kubernetes.io/managed-by: Helm
spec:
  replicas: 1
  selector:
    matchLabels:
      app.kubernetes.io/name: mychart1
      app.kubernetes.io/instance: myapp23
  template:
    metadata:
      labels:
        app.kubernetes.io/name: mychart1
        app.kubernetes.io/instance: myapp23
    spec:
      containers:
        - name: mychart1
          image: "ghcr.io/stacksimplify/kubenginx:1.0.0"
          imagePullPolicy: IfNotPresent
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http

PS C:\Users\grandhiv>
```

## Step-07: helm get all command
- **helm get all:** This command prints a human readable collection of information about the notes, hooks, supplied values, and generated manifest file of the given release.
- This is a good way to see what templates are installed on the kubernetes cluster server.
- **helm get notes and helm get hooks:** These two commmands we will explore when we are discussing about helm chart development. 
```t
# helm get all
helm get all RELEASE-NAME
helm get all myapp901
```

## Step-08: Uninstall Helm Release
```t
# Uninstall Helm Release
helm uninstall myapp901

# List Helm Releases
helm list
```

## Step-09: Values Hierarchy
1. Sub chart `values.yaml` can be overriden by parents chart `values.yaml`
2. Parent charts `values.yaml` can be overriden by user-supplied value file `(-f myvalues.yaml)`
3. User-supplied value file `(-f myvalues.yaml)` can be overriden by `--set` parameters

## Step-10: Deleting a default Key by passing null
- If you need to delete a key from the default values, you may override the value of the key to be null, in which case Helm will remove the key from the overridden values merge.
```t
# Release: myapp901
helm install myapp901 stacksimplify/mychart1 --atomic
helm list
helm status myapp901 --show-resources
http://localhost:31231

# Release: myapp902
helm install myapp902 stacksimplify/mychart1 --atomic
helm list

# Option-1: Give desired port other than 31231
helm install myapp902 stacksimplify/mychart1 --set service.nodePort=31232 

# Option-2: Pass null value to nodePort (service.nodePort=null)
helm install myapp902 stacksimplify/mychart1 --set service.nodePort=null --dry-run --debug
helm install myapp902 stacksimplify/mychart1 --set service.nodePort=null 

# Additional Notes for understanding
1. We will choose option-2 to demonstrate the concept "Deleting a default Key by passing null"
2. For NodePort Service, if we dont define the "nodePort" argument, it by default assigns a port dynamically from the port range 30000-32767. 
3. In our case already 31231 is used, other than that port it will allocate someother port when we pass null. 
4. In short, if we dont want to pass the default values present in values.yaml as-is, we dont need to change the complete chart with a new version, we can just pass null.

# Uninstall Releases
helm uninstall myapp901
helm uninstall myapp902
helm list
```
