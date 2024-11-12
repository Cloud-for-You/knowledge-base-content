---
title: Konfigurace Kubernetes clusteru
weight: 3
---

### Výměna API certifikátu
Instalovaný cluster má vygenerovanou self-sign CA pro API a v produkčním prostředí toto není vhodné řešení. Ideální je tento certifikát nahradit certifikátem veřejné autority a nebo alespoň certifikátem, kterému důvěřujeme. Jelikož se jedná o celkem komplexní změnu, není úplně jednoduché tuto certifikační autoritu změnit a vhodnější je vložit před API L7 proxy a na ní provést SSL terminaci pod vlastním certifikátem a dále přesměrovat provoz jako Reencrypt a k nové navázané komunikaci využít již certifikát z deployovaného clusteru.

### OAuth autorizace
Pro autorizaci k API je možné využívat OAuth2. Pro inspiraci použijeme providera Google, který umožnuje poskytnout OpenID pro ověřování uživatelů.

Upravíme soubor **/etc/kubernetes/manifests/kube-apiserver.yaml** na master serveru a vložíme do něj následující obsah
```yaml
- --oidc-issuer-url=https://accounts.google.com
- --oidc-client-id=<CLIENT_ID>
- --oidc-username-claim=sub
- --oidc-groups-claim=groups
- --oidc-username-prefix=oidc:
```

