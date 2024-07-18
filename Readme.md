# Local DevSecOps Laboratory
IaC representation of DevSecOps Operations Infrastructure. This projects aims to create a DevSecOps ecosystem by using cloud native applications. Project will follow GitOps and Everything as Code priciples.

# Projects that will be imported in
| Project Name | Version | Status |
| ---- | --- | --- |
| ArgoCD | v2.11.3 | :white_check_mark: |
| Cloud Native Storage Solution (For Local Dynamic Provisioning) | 4.1.0 | :white_check_mark:|
| Metallb to access applications via our local network (For L2 Advertisement) | v0.14.7 | :white_check_mark: |
| Cloud Native Application Proxy (Traefik or Nginx Reverse Proxy) | - | - |
| CI platform like GitLab or Jenkins | - | - |
| Container Image Registery | - | - |
| SAST/SAC/DAST tools | - | - |
| And various configurations to connect all of these projects. | - | - |

# Setup
Configure your kubectl client and then execute the command below;
```shell
until kubectl apply -k https://github.com/umut-akkaya/umutlabs.local/bootstrap/overlays/default/; do sleep 3; done
```

Depending on your system hardware it might take several minutes to be ready for cluster.