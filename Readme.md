Configure Prometheus & Grafana on AKS Cluster


Pre-Req
------
       1. Create AKS cluster either from Portal or Terraform
       2. Create a server for connecting AKS cluster (ubuntu prefferred)
       3. Install kubectl & AZ Cli on ubuntu
       4. Install Helm

Install kubectl
---------------

You can refere the below link or execute below commands on the ubuntu vm

https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/ 


curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
   
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check

sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

kubectl version --client



Setup Grafana & Prometheus
---------------------------

Once the AKS cluster is up & running, its time to install grafana & prometheus with helm

Install Helm
-------------

url -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh

Add the Prometheus repo to the helm
------------------------------------

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm repo update

helm install prometheus \
  prometheus-community/kube-prometheus-stack \
  --namespace grafana \
  --create-namespace



kubectl get all -n grafana

once you see the below services 

prometheus-grafana  & prometheus-kube-prometheus-prometheus


Log into Grafana & Prometheus

The above services are ClusterIP as of now, to log into the Grafana, we need to make the service type from ClusterIP to LoadBalancer

Once those are changed in the cluster, you will be able to see the external ip in the services

Click on the particular external ip, It will take you to the Grafana Dashboard

by default username & password for Grafana is admin/prom-operator


Deploy any APP
--------------

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginxdeployment
  replicas: 2
  template:
    metadata:
      labels:
        app: nginxdeployment
    spec:
      containers:
      - name: nginxdeployment
        image: nginx:latest
        ports:
        - containerPort: 80
