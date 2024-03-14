# Helm Builtin Objects

## Step-01: Introduction
- Objects are passed into a template from the template engine. 
- Objects can be simple, and have just one value or they can contain other objects or functions. 
- For example: the Release object contains several objects (like .Release.Name) and the Files object has a few functions.
### Helm Builtin Objects
- Release 
- Chart 
- Values 
- Capabilities 
- Template 
- Files 

## Step-02: Create a simple chart and clean-up NOTES.txt
```t
# Create Helm Chart
helm create CHART-NAME
helm create builtinobjects

# Remove all content from NOTES.txt
cd builtinobjects/templates
>NOTES.txt

# Change to Chart Directory
cd builtinobjects

# helm install --dry-run
helm install myapp1 . --dry-run
```

## Step-03: Helm Object: Root or dot or Period (.)
```t
# Update NOTES.txt with below content
{{/* Root or Dot or Period Object */}}
Root Object: {{ . }}

# Change to Chart Directory
cd builtinobjects

# helm install with --dry-run
helm install myapp101 . --dry-run
```
- The output of root object can be as follows
```t
PS C:\Users\grandhiv\builtinobject> helm install myapp101 .
NAME: myapp101
LAST DEPLOYED: Thu Mar 14 17:25:05 2024
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
Root Object: map[
Capabilities:0xc0001b2000
Chart:{{builtinobject  [] 0.1.0 A Helm chart for Kubernetes [] []  v2   1.16.0 false map[]  [] application} true}
 Files:map[.helmignore:[35 32 80 97 116 116 101 114 110 115 32 116 111 32 105 103 110 111 114 101 32 119 104 101 110 32 98 117 105 108 100 105 110 103 32 112 97 99 107 97 103 101 115 46 10 35 32 84 104 105 115 32 115 117 112 112 111 114 116 115 32 115 104 101 108 108 32 103 108 111 98 32 109 97 116 99 104 105 110 103 44 32 114 101 108 97 116 105 118 101 32 112 97 116 104 32 109 97 116 99 104 105 110 103 44 32 97 110 100 10 35 32 110 101 103 97 116 105 111 110 32 40 112 114 101 102 105 120 101 100 32 119 105 116 104 32 33 41 46 32 79 110 108 121 32 111 110 101 32 112 97 116 116 101 114 110 32 112 101 114 32 108 105 110 101 46 10 46 68 83 95 83 116 111 114 101 10 35 32 67 111 109 109 111 110 32 86 67 83 32 100 105 114 115 10 46 103 105 116 47 10 46 103 105 116 105 103 110 111 114 101 10 46 98 122 114 47 10 46 98 122 114 105 103 110 111 114 101 10 46 104 103 47 10 46 104 103 105 103 110 111 114 101 10 46 115 118 110 47 10 35 32 67 111 109 109 111 110 32 98 97 99 107 117 112 32 102 105 108 101 115 10 42 46 115 119 112 10 42 46 98 97 107 10 42 46 116 109 112 10 42 46 111 114 105 103 10 42 126 10 35 32 86 97 114 105 111 117 115 32 73 68 69 115 10 46 112 114 111 106 101 99 116 10 46 105 100 101 97 47 10 42 46 116 109 112 114 111 106 10 46 118 115 99 111 100 101 47 10]] Release:map[IsInstall:true IsUpgrade:false
Name:myapp101 Namespace:default
Revision:1 Service:Helm]

Subcharts:map[] Template:map[BasePath:builtinobject/templates Name:builtinobject/templates/NOTES.txt]

Values:map[affinity:map[] autoscaling:map[enabled:false maxReplicas:100 minReplicas:1 targetCPUUtilizationPercentage:80] fullnameOverride: image:map[pullPolicy:IfNotPresent repository:nginx tag:] imagePullSecrets:[] ingress:map[annotations:map[]
className: enabled:false hosts:[map[host:chart-example.local paths:[map[path:/ pathType:ImplementationSpecific]]]] tls:[]] nameOverride: nodeSelector:map[] podAnnotations:map[] podLabels:map[] podSecurityContext:map[] replicaCount:1 resources:map[] securityContext:map[] service:map[port:80 type:ClusterIP] serviceAccount:map[annotations:map[] automount:true create:true name:] tolerations:[] volumeMounts:[] volumes:[]]]
PS C:\Users\grandhiv\builtinobject>
```

## Step-04: Helm Object: Release
- This object describes the Helm release. 
- It has several objects inside it related to Helm Release.
- Put the below in `NOTES.txt` and test it
```t
{{/* Release Object */}}
Release Name: {{ .Release.Name }}
Release Namespace: {{ .Release.Namespace }}
Release IsUpgrade: {{ .Release.IsUpgrade }}
Release IsInstall: {{ .Release.IsInstall }}
Release Revision: {{ .Release.Revision }}
Release Service: {{ .Release.Service }}
```
- The output can be as follows
```t
PS C:\Users\grandhiv\builtinobject> cat .\templates\NOTES.txt
Release: {{ .Release }}
Release IsUpgrade: {{ .Release.IsUpgrade }}

PS C:\Users\grandhiv\builtinobject>
PS C:\Users\grandhiv\builtinobject> helm upgrade myapp101 .
Release "myapp101" has been upgraded. Happy Helming!
NAME: myapp101
LAST DEPLOYED: Thu Mar 14 17:57:12 2024
NAMESPACE: default
STATUS: deployed
REVISION: 4
NOTES:
Release: map[IsInstall:false IsUpgrade:true Name:myapp101 Namespace:default Revision:4 Service:Helm]
Release IsUpgrade: true
PS C:\Users\grandhiv\builtinobject>
PS C:\Users\grandhiv\builtinobject>

```
## Step-05: Helm Object: Chart
- Any data in Chart.yaml will be accessible using Chart Object. 
- For example {{ .Chart.Name }}-{{ .Chart.Version }} will print out the builtinobjects-0.1.0.
- [Complte Chart.yaml Objects for reference](https://helm.sh/docs/topics/charts/#the-chartyaml-file)
- Put the below in `NOTES.txt` and test it
  
```t
{{/* Chart Objet */}}
Chart Name: {{ .Chart.Name }}
Chart Version: {{ .Chart.Version }}
Chart AppVersion: {{ .Chart.AppVersion }}
Chart Type: {{ .Chart.Type }}
Chart Name and Version: {{ .Chart.Name }}-{{ .Chart.Version }}


# Change to CHART Directory 
cd builtinobjects



# Helm Install 
helm install myapp101 .
```
- The output can be
```t
PS C:\Users\grandhiv\builtinobject> cat .\templates\NOTES.txt
{{/* Chart Object */}}
Chart: {{ .Chart }}
Chart Name: {{ .Chart.Name }}
Chart Version: {{ .Chart.Version }}
Chart AppVersion: {{ .Chart.AppVersion }}
Chart Type: {{ .Chart.Type }}
Chart Name and Version: {{ .Chart.Name }}-{{ .Chart.Version }}
PS C:\Users\grandhiv\builtinobject>
PS C:\Users\grandhiv\builtinobject>
PS C:\Users\grandhiv\builtinobject>
PS C:\Users\grandhiv\builtinobject> helm upgrade myapp101 .
Release "myapp101" has been upgraded. Happy Helming!
NAME: myapp101
LAST DEPLOYED: Thu Mar 14 18:05:57 2024
NAMESPACE: default
STATUS: deployed
REVISION: 8
NOTES:
Chart: {{builtinobject  [] 0.1.0 A Helm chart for Kubernetes [] []  v2   1.16.0 false map[]  [] application} true}
Chart Name: builtinobject
Chart Version: 0.1.0
Chart AppVersion: 1.16.0
Chart Type: application
Chart Name and Version: builtinobject-0.1.0
PS C:\Users\grandhiv\builtinobject>
```


## Step-06: Helm Objects: Values, Capabilities, Template
- **Values Object:** Values passed into the template from the values.yaml file and from user-supplied files. By default, Values is empty.
- **Capabilities Object:** This provides information about what capabilities the Kubernetes cluster supports
- **Template Object:** Contains information about the current template that is being executed
    - The template gives output wrt to template which it is in. If the template object is          in deployment.yaml, the output can be
      
      Template Name: builtinobjects/templates/deployment.yaml
      
      Template Base Path: builtinobjects/templates
  
- Put the below in `NOTES.txt` and test it
```t
{{/* Values Object */}}
Replica Count: {{ .Values.replicaCount }}
Image Repository: {{ .Values.image.repository }}
Service Type: {{ .Values.service.type }}

{{/* Capabilities Object */}}
Kubernetes Cluster Version Major: {{ .Capabilities.KubeVersion.Major }}
Kubernetes Cluster Version Minor: {{ .Capabilities.KubeVersion.Minor }}
Kubernetes Cluster Version: {{ .Capabilities.KubeVersion }} and {{ .Capabilities.KubeVersion.Version }}
Helm Version: {{ .Capabilities.HelmVersion }}
Helm Version Semver: {{ .Capabilities.HelmVersion.Version }}

{{/* Template Object */}}
Template Name: {{ .Template.Name }} 
Template Base Path: {{ .Template.BasePath }}

# Change to CHART Directory 
cd builtinobjects 

# Helm Install with --dry-run
helm install myapp101 . --dry-run

# Sample Output
Replica Count: 1
Image Repository: ghcr.io/stacksimplify/kubenginxhelm
Service Type: NodePort

Kubernetes Cluster Version Major: 1
Kubernetes Cluster Version Minor: 27
Kubernetes Cluster Version: v1.27.2 and v1.27.2
Helm Version: {v3.12.1 f32a527a060157990e2aa86bf45010dfb3cc8b8d clean go1.20.5}
Helm Version Semver: v3.12.1

- The template gives output wrt to template which it is in. If the template object is in deployment.yaml, the output can be
Template Name: builtinobjects/templates/deployment.yaml
Template Base Path: builtinobjects/templates

### The output for template in NOTES.txt

The Template Name is template path .
Template Name: builtinobjects/templates/NOTES.txt
Template Base Path: builtinobjects/templates
```

## Step-07: Helm Objects: Files
- **Files Object:**
-  sometimes it is desirable to import a file that is not a template and inject its contents without sending the contents through the template renderer. Helm provides access to files through the .Files object. 
- We cant use it to access templates but only the non-special files(custom added files ).

- Put the below in `NOTES.txt` and test it
- [Additional Reference: Access Files Inside Templates](https://helm.sh/docs/chart_template_guide/accessing_files/)

- The config-files is a folder and ``.Files.Glob "config-files/*")`` denotes that considering the folder config-files and ``.AsConfig`` provides the output in yaml format
- ``.AsSecrets`` provides the value in base64 encoded format for each file in a folder.
- ``.Files.Glob`` return the list of files which are matched to pattern in non-human readable format.
```t

{{/* File Object */}}
File Get: {{ .Files.Get "myconfig1.toml" }}
File Glob as Config: {{ (.Files.Glob "config-files/*").AsConfig }}
File Glob as Secret: {{ (.Files.Glob "config-files/*").AsSecrets }}
File Lines: {{ .Files.Lines "myconfig1.toml" }}
File Lines: {{ .Files.Lines "config-files/myconfig2.toml" }}
File Glob: {{ .Files.Glob "config-files/*" }}

# Change to CHART Directory 
cd builtinobjects 

# Helm Install with --dry-run
helm install myapp101 . --dry-run

# Sample Output
File Get: message1 = Hello from config 1 line1
message2 = Hello from config 1 line2
message3 = Hello from config 1 line3

File Glob as Config: myconfig2.toml: |-
  appName: myapp2
  appType: db
  appConfigEnable: true
myconfig3.toml: |-
  appName: myapp3
  appType: app
  appConfigEnable: false
File Glob as Secret: myconfig2.toml: YXBwTmFtZTogbXlhcHAyCmFwcFR5cGU6IGRiCmFwcENvbmZpZ0VuYWJsZTogdHJ1ZQ==
myconfig3.toml: YXBwTmFtZTogbXlhcHAzCmFwcFR5cGU6IGFwcAphcHBDb25maWdFbmFibGU6IGZhbHNl
File Lines: [message1 = Hello from config 1 line1 message2 = Hello from config 1 line2 message3 = Hello from config 1 line3 ]
File Lines: [appName: myapp2 appType: db appConfigEnable: true]
File Glob: map[config-files/myconfig2.toml:[97 112 112 78 97 109 101 58 32 109 121 97 112 112 50 10 97 112 112 84 121 112 101 58 32 100 98 10 97 112 112 67 111 110 102 105 103 69 110 97 98 108 101 58 32 116 114 117 101] config-files/myconfig3.toml:[97 112 112 78 97 109 101 58 32 109 121 97 112 112 51 10 97 112 112 84 121 112 101 58 32 97 112 112 10 97 112 112 67 111 110 102 105 103 69 110 97 98 108 101 58 32 102 97 108 115 101]]
```

## Additional Reference
- [Helm Built-In Objects](https://helm.sh/docs/chart_template_guide/builtin_objects/)
- [Helm Chart.yaml Fields](https://helm.sh/docs/chart_template_guide/builtin_objects/)


