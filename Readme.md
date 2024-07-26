# Local DevSecOps Laboratory
IaC representation of DevSecOps Operations Infrastructure. This projects aims to create a DevSecOps ecosystem by using cloud native applications. Project will follow GitOps and Everything as Code priciples.

# Projects that will be imported in
| Project Name | Version | Status |
| ---- | --- | --- |
| ArgoCD | v2.11.3 | :white_check_mark: |
| Container Network Inteface | v3.28.0 | :white_check_mark: |
| Cloud Native Storage Solution (For Local Dynamic Provisioning) | 4.1.0 | :white_check_mark:|
| Metallb to access applications via our local network (For L2 Advertisement) | v0.14.7 | :white_check_mark: |
| Cloud Native Application Proxy (Traefik or Nginx Reverse Proxy) | v3.0.4 | :white_check_mark: |
| CI platform like GitLab or Jenkins | - | - |
| Container Image Registery | - | - |
| SAST/SAC/DAST tools | - | - |
| And various configurations to connect all of these projects. | - | - |

# Setup
## Minikube Installation
Start your cluster
```shell
minikube start -p profile_name -n 2
```
Enable storage provisioner addon
```shell
minikube addons -p profile_name enable storage-provisioner
```
```shell
until kubectl apply -k https://github.com/umut-akkaya/umutlabs.local/bootstrap/overlays/minikube/; do sleep 3; done
```

Depending on your system specs it might take several minutes for the cluster to be ready.

## On-Prem Installation
Configure your kubectl client and then execute the commands below;

```shell
until kubectl apply -k https://github.com/umut-akkaya/umutlabs.local/bootstrap/overlays/default/; do sleep 3; done
```

Depending on your system specs it might take several minutes for the cluster to be ready