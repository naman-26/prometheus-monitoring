# Monitoring with Prometheus 
Prometheus is a free software application used for event monitoring and alerting.
In this project, I have monitored google demo-microservice-app deployed on a AWS EKS cluster.

## Deploy microservices on EKS cluster
```bash
eksctl create cluster
kubectl create namespace online-shop
kubectl apply -f E:/Demo-projects/monitoring/config-microservices.yaml -n online-shop
```
## Deploy Prometheus Operator Stack
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
kubectl create namespace monitoring
helm install monitoring prometheus-community/kube-prometheus-stack -n monitoring
helm ls
```
[Link to the chart: https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack]

## Check Prometheus Stack Pods
```bash
kubectl get all -n monitoring
```

## Access Prometheus UI
```bash
kubectl port-forward svc/monitoring-kube-prometheus-prometheus 9090:9090 -n monitoring &
```

## Access Grafana
```bash
kubectl port-forward svc/monitoring-grafana 8080:80 -n monitoring &
```
```bash
user: admin
pwd: prom-operator
```

## Trigger CPU spike with many requests
Deploy a busybox pod, so we can curl our application
```bash
kubectl run curl-test --image=radial/busyboxplus:curl -i --tty --rm
```
## Access Alert manager UI
```bash
kubectl port-forward -n monitoring svc/monitoring-kube-prometheus-alertmanager 9093:9093 &
```
### Create cpu stress
```bash
kubectl delete pod cpu-test 
kubectl run cpu-test --image=containerstack/cpustress -- --cpu 4 --timeout 60s --metrics-brief
```
## Deploy Redis Exporter
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add stable https://charts.helm.sh/stable
helm repo update

helm install redis-exporter prometheus-community/prometheus-redis-exporter -f redis-values.yaml
```

 
