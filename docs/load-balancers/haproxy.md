+++
title="HAProxy"
weight=1
+++

Instalace se bude skládat ze dvou hlavních kroků.
- instalace master serverů
- instalace compute serverů

### Příprava konfiguračního souboru pro kubeadm

defaulní konfiguraci je možné přípravit zadáním
```sh
kubeadm config print init-defaults > kubeadm-config.yaml
```

Popis API nalezneme [zde](https://kubernetes.io/docs/reference/config-api/kubeadm-config.v1beta3/). Níže je uveden konfigurační soubor, který je modifikován pro prostředí na kterém je možno provozovat plnohodnotný Kubernetes cluster.

```yaml
---
apiVersion: kubeadm.k8s.io/v1beta3
kind: InitConfiguration
bootstrapTokens:
- groups:
  - system:bootstrappers:kubeadm:default-node-token
  token: abcdef.0123456789abcdef
  ttl: 24h0m0s
  usages:
  - signing
  - authentication
localAPIEndpoint:
  advertiseAddress: 1.2.3.4
  bindPort: 6443
nodeRegistration:
  criSocket: unix:///var/run/crio/crio.sock
  imagePullPolicy: IfNotPresent
  name: node
  taints: null

---
apiVersion: kubeadm.k8s.io/v1beta3
kind: ClusterConfiguration
apiServer:
  timeoutForControlPlane: 4m0s
certificatesDir: /etc/kubernetes/pki
clusterName: kubernetes
controllerManager: {}
dns: {}
etcd:
  local:
    dataDir: /var/lib/etcd
imageRepository: registry.k8s.io
kubernetesVersion: 1.28.0
networking:
  dnsDomain: cluster.local
  serviceSubnet: 10.96.0.0/12
scheduler: {}
```
