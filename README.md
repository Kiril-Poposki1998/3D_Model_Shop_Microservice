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
cd .\src\
git submodule add https://github.com/simonpankovski/polybase-vue.git
```
```
cd ..
git submodule update
```