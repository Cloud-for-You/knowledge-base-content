+++
title="SmallStep integration"
weight=2
+++

### Infrastruktura

![Cert-manager with SmallStep](../../../images/acme-for-kubernetes/cert-manager-with-smallstep.png "Cert-manager with SmallStep")

### Základní komponenty

#### SmallStep CA

Komponenta SmallStep je certifikační autorita, která podepisuje žádosti o certifikáty. Poskytuje definovaný protokol ACME. Definice tohoto protokolu je v RFC 8555. SmallStep je možné pomocí pluginu integrovat na nadřazenou certifikační autoritu. Pokud není tato integrace, je nutné, aby samotný cert manager disponoval certifikátem, pomocí kterého je možné vydávat další certifikáty. Může se jednat i o Intermediate certifikát vydaný pro tyto účely jinou nadřazenou certifikační autoritou. Jediná podmínka pro takový certifikát je, aby disponoval flagem __CA: True__.

```yaml
[params]
...
mobileNavigation = "left" # Mobile nav menu will open to the left of the screen.
...
```

### Konfigurace SmallStep

Jedná se o externí komponentu, která může a nemusí být konfigurována přímo v Kubernetes clusteru. Je možné uvažovat i s vzužitím veřejné autority Let's Encrypt. Mi použijeme interní SmallStep s definovanou SelfSign CA, pro demonstraci celého řešení.

#### Issuer / ClusterIssuer

```yaml
[params]
...
mobileNavigation = "left" # Mobile nav menu will open to the left of the screen.
...
```
