# Local DevSecOps Laboratory
IaC representation of DevSecOps Operations Infrastructure. This projects aims to create a DevSecOps ecosystem by using cloud native applications. Project will follow GitOps and Everything as Code priciples.

# Projects that will be imported in
| Solution | Project Name | Version | Status |
| ---- | --- | --- | --- |
| Continuous Delivery Tool | ArgoCD | v2.11.3 | :white_check_mark: |
| Container Network Interface | calico | v3.28.0 | :white_check_mark: |
| Cloud Native Storage Solution (For Local Dynamic Provisioning) | openebs | 4.1.0 | :white_check_mark:|
| LoadBalancer to access applications via our local network (For L2 Advertisement) | metallb | v0.14.7 | :white_check_mark: |
| Cloud Native Application Proxy | Traefik | v3.0.4 | :white_check_mark: |
| Automatic Certificate Signing and Distribute | cert-manager | v1.15.1 | :white_check_mark:  |
| CI platform like GitLab or Jenkins | - | - | - |
| Container Image Registery | - | - | - |
| SAST/SAC/DAST tools | - | - | - |
| And various configurations to connect all of these projects. | - | - | - |

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
until kubectl apply -k umutlabs.local/bootstrap/overlays/minikube/; do sleep 3; done
```

Depending on your system specs it might take several minutes for the cluster to be ready.

## On-Prem Installation
Configure your kubectl client and then execute the commands below;

First create gitlab and cert-manager namespaces
```shell
kubectl create ns gitlab
kubectl create ns cert-manager
```
Create secrets for our local CA.
```shell
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 365 -nodes
```
Then encode these keys by using base64 command

```shell
cat cert.pem |  base64 -w 0
cat key.pem |  base64 -w 0
```
Create a new secret file by using these values

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: umutlabs-ca-secret
  namespace: cert-manager
data:
  tls.crt: BASE64 ENCODED cert.pem
  tls.key: BASE64 ENCODED key.pem
```
Also create a secret called secret-custom-ca in gitlab namespace for trusting out CA

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: secret-custom-ca
  namespace: gitlab
data:
  gitlab.umutlabs.local.crt: BASE64 ENCODED cert.pem
```
Clone the repository

```shell
git clone https://github.com/umut-akkaya/umutlabs.local.git
```
```shell
until kubectl apply -k umutlabs.local/bootstrap/overlays/default/; do sleep 3; done
```

Depending on your system specs it might take several minutes for the cluster to be ready
