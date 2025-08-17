

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
