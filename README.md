# 3D Model Shop
## Prerequisites
For the project to be deployed locally some tools are needed to be installed.
```
Docker 20.10.21
kind 0.17.0
helm 3.10.1
kubectx 0.9.4
kubens 0.9.4
k9s 0.26.3
skaffold 2.0.2
kubectl 1.25.3
```
## Installing prerequisites (Windows)
The following command needs to be run as administrator in PowerShell.
``` 
choco install packages.config --confirm 
```
## Deploy a registry for the Docker images
```
docker run -d --restart=always -p "127.0.0.1:5000:5000" --name "registry" registry:2
```
## Deploy Kubernetes in Docker with kind
Create and set the context to point to the newly created cluster:
```
kind create cluster --image="kindest/node:v1.25.3" --config="infrastructure\kind\nodes_3.yml"
kubectl cluster-info --context kind-3d-model-shop
kubectl create ns 3d-model-shop
kubens 3d-model-shop
kubectl apply -f .\infrastructure\registry\config_map.yml
docker network connect "kind" registry
```
## Setting up the code for the microservice (Windows)
```
git submodule init
git submodule update
```
### Updating submodules
```
git submodule update --init --recursive --remote
```
## Deploy ingress controller
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
kubectl wait --namespace ingress-nginx --for=condition=ready pod --selector=app.kubernetes.io/component=controller --timeout=90s
```
## Deploy the microservice to Kubernetes
```
skaffold dev
```
## Setting up and populating the database (DO THIS ONLY ONCE)
```
kubectl get po | wsl -e grep 'polybase-be*' | wsl -e awk '{print $1}' | Tee-Object -Variable POD_NAME
kubectl wait --namespace 3d-model-shop --for=condition=ready pod --selector=app.kubernetes.io/name=postgresql --timeout=120s
kubectl cp .\infrastructure\database_export\data.sql.gz ${POD_NAME}:/tmp/data.sql.gz
kubectl exec -i deploy/polybase-be -- gunzip /tmp/data.sql.gz
kubectl exec -i deploy/polybase-be -- php bin/console doctrine:migrations:migrate
kubectl exec -it ${POD_NAME} -- sh
```
Run the foollowing command inside the pod
```
apk add postgresql-client
psql -U postgres -h postgresql.3d-model-shop.svc.cluster.local < /tmp/data.sql
```
**NOTE: The password for the database is password**