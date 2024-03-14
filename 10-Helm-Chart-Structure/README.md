# Understand Helm Chart Folder Structure

## Step-01: Introduction
- Understand Helm Chart Folder Structure

## Step-02: Helm Create Chart
```t
# Helm Create Chart
helm create <CHART-NAME>
helm create basechart
Observation: 
1. It will create a Helm Chart template 
2. We can call it like a helm chart created from a default starter chart
```

## Step-03: Helm Chart Structure
```
└── basechart
    ├── .helmignore
    ├── Chart.yaml
    ├── LICENSE
    ├── README.md
    ├── charts
    ├── templates
    │   ├── NOTES.txt
    │   ├── _helpers.tpl
    │   ├── deployment.yaml
    │   ├── hpa.yaml
    │   ├── ingress.yaml
    │   ├── service.yaml
    │   ├── serviceaccount.yaml
    │   └── tests
    │       └── test-connection.yaml
    └── values.yaml
```
![Imgur](https://github.com/grandhi02/helm/assets/45489301/5c7d6ce3-a94a-4756-a871-a4fce4fab7b0)

![Imgur](https://github.com/grandhi02/helm/assets/45489301/e4fe2e77-665d-4ef4-a32c-71645fc2f4cf)

![Imgur](https://github.com/grandhi02/helm/assets/45489301/5f41f4b5-b2b4-4191-aed4-89c6347f0d54)

![Imgur](https://github.com/grandhi02/helm/assets/45489301/30ebe715-523d-43d4-9399-5a8b8c3154ba)

![Imgur](https://github.com/grandhi02/helm/assets/45489301/2dfb482b-927e-40dc-acd4-cb1f02a7c0d5)

