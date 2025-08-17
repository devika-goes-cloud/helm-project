

## Helm is just a package manager.

### It helps you install apps into Kubernetes (like Prometheus & Grafana).

```yaml
so before that follow this workflow: -
Create cluster →

Configure kubectl →

Use Helm to install Prometheus/Grafana  ( This requires a live cluster and kubeconfig.) hence Using Helm (install/upgrade/uninstall)
```


# Install Helm
```bash
# Download Helm install script
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3

# Make it executable
chmod 700 get_helm.sh

# Install Helm
./get_helm.sh

# Verify installation
helm version
```

# two choices when setting up monitoring:

- Option 1: Install Prometheus + Grafana separately
- Option 2: Install kube-prometheus-stack (recommended in real DevOps)


# Add Helm Repositories

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```


# Create a Monitoring Namespace

```bash
kubectl create namespace monitoring

```



# Install kube-prometheus-stack
```bash

# Correct format to install any tool:- 

# helm install <release-name> <repo/chart> [flags]


helm install monitoring prometheus-community/kube-prometheus-stack --namespace monitoring




# Here:

# monitoring → Helm release name
# prometheus-community/kube-prometheus-stack → chart
# -n monitoring → namespace
```


# 🔹 Access Grafana
```bash
kubectl get pods -n monitoring   # check pods
kubectl get svc -n monitoring
```


So: you will see something like this:-

```yaml
Prometheus service → monitoring-kube-prometheus-sta-prometheus

Grafana service → monitoring-grafana

Change:-

spec:
  type: LoadBalancer
```
- To know access as the prometheus & Grafana by default their service is clusterIP so make it LoadBalancer


# > Edit Prometheus Service: -
```bash
kubectl edit svc monitoring-kube-prometheus-sta-prometheus -n monitoring

```


# Access Grafana UI in the browser

Default credentials:

user: admin
</br>
pass: prom-operator


# > Edit grafana Service: - 
```bash
kubectl edit svc monitoring-grafana -n monitoring

```



```yaml
#🔹 Benefits of kube-prometheus-stack:

✅ Prometheus + Grafana integrated
✅ Auto dashboards for Kubernetes cluster, nodes, pods, deployments
✅ Alertmanager included
✅ Node Exporter & Kube-State-Metrics installed automatically

```


now you are ready to monitor your K8S application but before that ensure your service object has annotations in its service.yaml ?

if not then first add that then only prometheus will scrape you app metrics


define something like this: -

```yaml
apiVersion: v1
kind: Service
metadata:
  name: springboot-app-service
  labels:
    app: springboot-app
  annotations:
    prometheus.io/scrape: "true"
    prometheus.io/port: "8080"
    prometheus.io/path: "/actuator/prometheus"
```


# we have two ways to define :
```yaml
method 1:- create ServiceMonitor (recommended for kube-prometheus-stack) or 
method 2: - by adding Prometheus annotations to the Service.
( but it also requires, You need to enable this in application.properties , means Expose Prometheus Endpoint for lets say springboot app
```


and below is the order of execution:-

```yaml
🔑 Summary of Order

kubectl apply -f deployment.yaml

kubectl apply -f service.yaml

kubectl apply -f servicemonitor.yaml (or add annotations to Service)

```




# STEPS to create Cluster:
```bash
eksctl create cluster --name mycluster --region ap-south-1 --nodegroup-name mynodegrp --version 1.30 --node-type t3.medium --nodes 2 --managed
```
