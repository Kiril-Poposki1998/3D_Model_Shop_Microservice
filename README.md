# 3D Model Shop
## Prerequisites
For the project to be deployed locally some tools are needed to be installed.
```
Docker 20.10.21
kind 0.17.0
helm 3.10.1
kubectx 0.9.4
k9s 0.26.3
```
## Installing prerequisites (Windows)
The following command needs to be run as administrator in PowerShell.
``` 
choco install packages.config --confirm 
```
## Deploy Kubernetes in Docker
Run the following command in Powershell:
```kind create cluster --image="kindest/node:v1.25.3" --config=.\infrastructure\kind\node_3.yml ```
Set the context to point to the newly created cluster:
``` 
kubectl cluster-info --context kind-3d-model-shop 
kubectl create ns 3d-model-shop
kubens 3d-model-shop
```
## Setting up the code for the microservice (Windows)
```
cd .\src\
git submodule add https://github.com/simonpankovski/polybase-vue.git
```
```
cd ..
git submodule update
```