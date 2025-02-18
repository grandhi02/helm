# Helm Template Functions and Pipelines

## Step-01: Introduction
1. Template Actions `{{ }}`
2. Action Elements `{{ .Release.Name }}`
3. Quote Function
4. Pipeline 
5. default Function
6. lower function
7. Controlling White Spaces `{{-  -}}`
7. indent function
8. nindent function
9. toYaml

## Step-02: Template Action "{{ }}"
- Anything in between Template Action `{{ .Chart.Name }}` is called Action Element
- Anything in between Template Action `{{ .Chart.Name }}` will be rendered by helm template engine and replace necessary values
- Anything outside of the template action will be printed as it is.
- Action elements defined inside the `{{ }}` will help us to retrieve data from other sources (example: `.Chart.Name`).
### Step-02-01: Valid Action Element
```t
# deployment.yaml file
apiVersion: apps/v1
kind: Deployment
metadata:
  # Template Action with Action Elements
  name: {{ .Release.Name }}-{{ .Chart.Name }}

# Change to CHART Directory
cd helmbasics

# Helm Template Command
helm template myapp101 .
1. helm template command helps us to check the output of the chart in fully rendered Kubernetes resource templates. 
2. This will be very helpful when we are developing a new chart, making changes to the chart templates, for debugging etc.
```
### Step-02-02: Invalid Action Element 
```t
# deployment.yaml file
apiVersion: apps/v1
kind: Deployment
metadata:
  # Template Action with Action Elements
  name: {{ something }}-{{ .Chart.Name }}
# Change to CHART Directory
cd helmbasics

# Helm Template Command
helm template myapp101 .  
Observation:
1. Should fail with error
2. In short, inside Action Element we should have 

Error: parse error at (helmbasics/templates/deployment.yaml:10): function "something" not defined
```

## Step-03: Template Function: quote
```t
# Add Quote Function 
  annotations:    
    app.kubernetes.io/managed-by: {{ .Release.Service }}
    # quote function
    app.kubernetes.io/managed-by: {{ quote .Release.Service }}
- with quote, we get the value helm in double quotes
- with squote, we can get value in single quotes
```
![imgur](https://github.com/grandhi02/helm/assets/45489301/eb647a91-381a-411b-bf43-2822a180e138)
```t

# Change to CHART Directory
cd helmbasics

# Helm Template Command
helm template myapp101 .
```

## Step-04: Pipeline
- Pipelines are an efficient way of getting several things done in sequence. 
- Inverting the order is a common practice in templates (.val | quote ), i.e the value before pipe is working as input to function after pipe. so it returns a value in double quotes.9 the value move from left to right )
   
```t
# Add Quote Function with Pipeline
  annotations:    
    app.kubernetes.io/managed-by: {{ .Release.Service }}
    # quote function
    app.kubernetes.io/managed-by: {{ quote .Release.Service }} 
    # quote function with pipeline
    app.kubernetes.io/managed-by: {{ .Release.Service | quote }}               
```
![imgur](https://github.com/grandhi02/helm/assets/45489301/2998f631-5a1c-461f-aa16-ba63313cc107)

```t
# Change to CHART Directory
cd helmbasics

# Helm Template Command
helm template myapp101 .
```

## Step-05: Template Function: default and lower
- [default function](https://helm.sh/docs/chart_template_guide/function_list/#default)
```t
# values.yaml
releaseName: "newrelease101"
replicaCount: 3

# Template Function default
  annotations:
    app.kubernetes.io/managed-by: {{ .Release.Service }}
    # Quote Function
    app.kubernetes.io/managed-by: {{ quote .Release.Service }}        
    # Pipeline
    app.kubernetes.io/managed-by: {{ .Release.Service | quote | upper | lower }}        
    # default Function
    app.kubernetes.io/name: {{ default "MYRELEASE101" .Values.releaseName | lower }}
spec:
  replicas: {{ default 1  .Values.replicaCount }}
```
- In the below image, the release Name is values.yaml is "" or null or empty-value which is considered as empty while using default, so the value MYRELEASE101 is obtained.
  
![imgur](https://github.com/grandhi02/helm/assets/45489301/fe80bdf4-b34b-4a5f-97e9-3d175b1222af)

# Change to CHART Directory
cd helmbasics

# Helm Template Command
helm template myapp101 .
```

## Step-06: Controlling Whitespaces
- **{{- .Chart.name }}:**  If a hyphen is added before the statement, `{{- .Chart.name }}` then the leading whitespace will be ignored during the rendering.There should be no gap b/w ``{{`` and ``-``.

- **{{ .Chart.name -}}:** If a hyphen is added after the statement, `{{ .Chart.name -}}` then the trailing whitespace will be ignored during the rendering
```yaml
  annotations:
    app.kubernetes.io/managed-by: {{ .Release.Service }}
    # Quote Function
    app.kubernetes.io/managed-by: {{ quote .Release.Service }}        
    # Pipeline
    app.kubernetes.io/managed-by: {{ .Release.Service | quote | upper | lower }}        
    # default Function
    app.kubernetes.io/name: {{ default "MYRELEASE101" .Values.releaseName }}
    # Controlling Leading and Trailing White spaces 
    leading-whitespace: "   {{- .Chart.Name }}    manideep"
    trailing-whitespace: "   {{ .Chart.Name -}}    manideep"
    leadtrail-whitespace: "   {{- .Chart.Name -}}  manideep"  
```
- The output can be as follows
![imgur](https://github.com/grandhi02/helm/assets/45489301/661611da-2287-455d-a1f1-e3dbe82a4c90)

```t
```t
# Change to CHART Directory
cd helmbasics

# Helm Template Command
helm template myapp101 .    
```


## Step-07: indent and nindent functions
- **indent:** The [indent function](https://helm.sh/docs/chart_template_guide/function_list/#indent) indents every line in a given string to the specified indent width. It adds the extra specified spaces at front of the value. This is useful when aligning multi-line strings:
- **nindent:** The [nindent function](https://helm.sh/docs/chart_template_guide/function_list/#nindent) is the same as the indent function, but prepends a new line to the beginning of the string.

```yaml
# indent and nindent functions
  annotations:
    app.kubernetes.io/managed-by: {{ .Release.Service }}
    # Quote Function
    app.kubernetes.io/managed-by: {{ quote .Release.Service }}        
    # Pipeline
    app.kubernetes.io/managed-by: {{ .Release.Service | quote | upper | lower }}        
    # default Function
    app.kubernetes.io/name: {{ default "MYRELEASE101" .Values.releaseName | lower }}
    # indent function
    indenttest: "  {{- .Chart.Name | indent 4 -}}  "
    # nindent function
    nindenttest: "  {{- .Chart.Name | nindent 4 -}}  "  
```
- The output can be as follow
- In nindent, with 0 gap, the value is made arranged from first position after a new line.But for nindent with 14, the value is made positioned after new line and 14 spaces.
  
  ![imgur](https://github.com/grandhi02/helm/assets/45489301/7fa80104-2a59-4f7a-82cb-6feaeaa43f69)


```t
# Change to CHART Directory
cd helmbasics

# Helm Template Command
helm template myapp101 .    
```


## Step-08: Template Function: toYaml 
- **toYaml:** 
- We can use [toYaml function](https://helm.sh/docs/chart_template_guide/function_list/#type-conversion-functions) inside the helm template actions to convert an object into YAML.
- Convert list, slice, array, dict, or object to indented yaml. 
```t
# values.yaml
# Resources for testing Template Function: toYaml 
resources: 
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi

# deployment.yaml
    spec:
      containers:
      - name: nginx
        image: ghcr.io/stacksimplify/kubenginx:4.0.0
        ports:
        - containerPort: 80
        resources: 
        {{- toYaml .Values.resources | nindent 4}}
```
- The output as follows : The resources in values.yaml are indented after converting to yaml using toYaml.
![imgur](https://github.com/grandhi02/helm/assets/45489301/0838b509-e7e0-4e7e-9548-27103fec846b)

```t
# Change to CHART Directory
cd helmbasics

# Helm Template Command
helm template myapp101 .

# Helm Install with --dry-run
helm install myapp101 . --dry-run

# Helm Install
helm install myapp101 . --atomic

# List k8s Pods
kubectl get pods 

# Describe Pod
kubectl describe pod <POD-NAME>

# Helm Uninstall
helm uninstall myapp101
```
