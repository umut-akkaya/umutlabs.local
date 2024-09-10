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
| CI platform like GitLab or Jenkins | Gitlab | v17.2.0-ee | :white_check_mark: |
| Monitoring | Prometheus | v2.54.1 | :white_check_mark: |
| Visualization | Grafana | 11.1.5 | :white_check_mark: |
| Container Image Registry | - | - | - |
| SAST/SAC/DAST tools | - | - | - |
| And various configurations to connect all of these projects. | - | - | - |

# Setup
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
Update kubeproxy configmap.
```shell
kubectl get configmap kube-proxy -n kube-system -o yaml | \
sed -e "s/strictARP: false/strictARP: true/" | \
kubectl apply -f - -n kube-system
```
Clone the repository

```shell
git clone https://github.com/umut-akkaya/umutlabs.local.git
```
```shell
until kubectl apply -k umutlabs.local/bootstrap/overlays/default/; do sleep 3; done
```

Depending on your system specs it might take several minutes for the cluster to be ready

## DNS Issues
You will encounter an error on gitlab-runner. The workload will try to connect "gitlab.umutlabs.local" but cannot resolve the domain. To overcome this problem an DNS entry must be recorded. There are multiple ways to accomplish this based on your setup. The easiest way adding manual entry to coredns configmap.

First edit the coredns configmap

```shell
kubectl edit cm coredns -n kube-system
```

Then record the IP address under the hosts section

```yaml
apiVersion: v1
data:
  Corefile: |
    .:53 {
        errors
        health {
           lameduck 5s
        }
        ready
        kubernetes cluster.local in-addr.arpa ip6.arpa {
           pods insecure
           fallthrough in-addr.arpa ip6.arpa
           ttl 30
        }
        prometheus :9153
        forward . /etc/resolv.conf {
           max_concurrent 1000
        }
        cache 30
        loop
        reload
        loadbalance
        hosts {
          IP_ADDRESS_OF_LOAD_BALANCER_SERVICE gitlab.umutlabs.local
        }
    }
kind: ConfigMap
metadata:
  name: coredns
  namespace: kube-system
```
