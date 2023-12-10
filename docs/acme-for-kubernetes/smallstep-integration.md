+++
title="SmallStep integration"
weight=3
+++

### Postup pro vydání certifikátu

![Cert-manager with SmallStep](../../../images/acme-for-kubernetes/cert-manager-with-smallstep-ca.png "Cert-manager with SmallStep")

### SmallStep CA

Certifikační autorita, která podepisuje žádosti o certifikáty. Poskytuje definovaný protokol ACME. Definice tohoto protokolu je v RFC 8555. SmallStep je možné pomocí pluginu integrovat na nadřazenou certifikační autoritu. Jedná se o externí komponentu, která může a nemusí být konfigurována přímo v Kubernetes clusteru. Je možné uvažovat i s využitím veřejné autority Let's Encrypt.

```yaml
[params]
...
mobileNavigation = "left" # Mobile nav menu will open to the left of the screen.
...
```

### Konfigurace Issuer / ClusterIssuer

```yaml
[params]
...
mobileNavigation = "left" # Mobile nav menu will open to the left of the screen.
...
```
