---
title: Instalace Kubernetes clusteru
weight: 2
---

Instalace clusteru se skádá z několika částí, které není možné vynechat 
- instalace prvního master serveru
- deployment síťového stacku například Calico, Cilium, OVN-Kubernetes nebo jiný
- instalace compute serverů

### Instalace prvního master serveru
Pro instalaci budeme používat **kubeadm** utilitu.

###### Příprava konfiguračního souboru pro kubeadm
Pro testovací účely nemusíme konfigurační soubory připravovat. Příprava konfiguračních souborů je vhodná pro production ready prostředí.

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
### Deployment síťového stacku

Instalace Calico overlay network

### Instalace compute serverů

Pro přidání compute nodů do clusteru stačí na tomto nodu spustit kubeadm utilitu
```sh
kubeadm join 10.211.57.20:6443 --token 1whle5.chnrsua2z9lsjolv --discovery-token-ca-cert-hash sha256:afeb03bd5f647c5144fe722c8e103cf62c8723a0e2e74d83a3f7fb4388b32652
```
Token má platnost pouze 24 hodin. Pokud bude potřeba přidat nový token, je možné jej vygenerovat
```sh
kubeadm token create --print-join-command
```