+++
title="Cert-manager"
weight=1
+++

Controller, který sleduje Kubernetes API a na základě změn provádí definované akce.

![Cert-manager diagram](../../../images/acme-for-kubernetes/cert-manager-diagram.svg "Cert-manager diagram")

Jeho základní funkcí je sledovat objekty __Certificate.cert-manager.io/v1__, které obsahují informace pro CSR a následně zajistit podepsání této žádosti u definované certifikační autority. Objekt __Certificate.cert-manager.io/v1__ je pouze strukturovaně zapsaná informace, ze které bude vytvořena plnohodnotná žádost ve tvaru CSR a uložena do objektu __CertificateRequests.cert-manager.io/v1__.

Jako druhou funkcionalitu má za úkol sledovat __Ingress.networking.k8s.io/v1__ a jeho annotace. Na základě dohledaných annotací v Ingress založí __Certificate.cert-manager.io/v1__ a provede patch do Ingress se jménem Secret, ve kterém bude obsažen certifikát a privátní klíč.

Cert-manager je možné používat jako certifikační autoritu, která má k dispozici certifikát pro vydávání a nebo je možné jej integrovat s certifikační autoritou třetí strany.

### Deployment do Kubernetes clusteru

Přidáme si helm repository do lokélního nastaveního nastavení, aby jsme jej mohli instalovat.
```sh
helm repo add jetstack https://charts.jetstack.io
```

Pokud nepotřebujeme měnit nastavení je možné instalaci provést následovně.
```sh
DST_NAMESPACE="cert-manager"
VERSION="v1.13.2"
RELEASE="${VERSION}-1"
helm install ${RELEASE} --namespace ${DST_NAMESPACE} --version ${VERSION} jetstack/cert-manager
```

V případě změn je vhodné vytvořit soubor values.yaml a proměnné, které požadujeme předefinovat uvedeme v tomto souboru.
Následně pužijeme předešlý command pro instalaci a rozšíříme jej o parametr __-f values.yaml__.
Informace o proměnných je možné získat [zde](https://artifacthub.io/packages/helm/cert-manager/cert-manager#configuration).

Mnou definované proměnné pro deployment do Kubernetes clusteru
```yaml
---

```