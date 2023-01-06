# Fullstack Helm Chart

This is to test a fullstack helm setup with Helm and Gloo Ingress Controller

## Required Software

* DNSMasq
  * `brew install dnsmasq`
* Docker Desktop
  * [Download](https://www.docker.com/products/docker-desktop/)
* Minikube
  * `brew install minikube`
* Helm
  * `brew install helm`
* Gloo
  * `brew install glooctl`


## Verification Steps

* Configure DNSMasq
  * If not already setup, we will setup DNSMasq to manage `.internal` domain names.
    * `mkdir -p $(brew --prefix)/etc/`
    * `echo 'address=/.internal/127.0.0.1' >> $(brew --prefix)/etc/dnsmasq.conf`
    * `brew services start dnsmasq`
    * `sudo mkdir -v /etc/resolver`
    * `sudo bash -c 'echo "nameserver 127.0.0.1" > /etc/resolver/internal'`
* Clone or Create Project:
  * `helm create fullstack-helm` <- This repo.
* Start Minikube:
  * `minikube start`
* Enable Minikube Ingress
  * `minikube addons enable ingress`
* Allow Minikube to Pull Private Packages
  * `minikube addons configure registry-creds`
  * `kubectl create secret docker-registry private-registry-login-secret --docker-server=${SERVER} --docker-username=${USERNAME} --docker-password=${PASSWORD} --docker-email=${EMAIL}`
  * `minikube addons enable registry-creds`
* Turn on Minikube Tunnel for Local Testing:
  * `minikube tunnel`
* Install Gloo:
  * `helm repo add gloo https://storage.googleapis.com/solo-public-helm`
  * `helm repo update`
  * `helm install gloo gloo/gloo --namespace gloo-system --create-namespace --set gateway.enabled=false,ingress.enabled=true`
    * _You may be asked to enter a password for the `minikube tunnel` you currently have running_
* Verify Gloo is Running:
  * `kubectl get all -n gloo-system`
    * _Make sure nothing is stuck in <Pending>._
* Install Helm Chart
  * `helm install . --generate-name`
    * _You may be asked to enter a password for the `minikube tunnel` you currently have running_
