+++
title="Cert-manager"
weight=1
+++

Controller, který sleduje Kubernetes API a na základě změn provádí definované akce.

![Cert-manager diagram](../../../images/acme-for-kubernetes/cert-manager-diagram.svg "Cert-manager diagram")

Jeho základní funkcí je sledovat objekty __Certificate.cert-manager.io/v1__, které obsahují informace pro CSR a následně zajistit podepsání této žádosti u definované certifikační autority. Objekt __Certificate.cert-manager.io/v1__ je pouze strukturovaně zapsaná informace, ze které bude vytvořena plnohodnotná žádost ve tvaru CSR a uložena do objektu __CertificateRequests.cert-manager.io/v1__.

Jako druhou funkcionalitu má za úkol sledovat __Ingress.networking.k8s.io/v1__ a jeho annotace. Na základě dohledaných annotací v Ingress založí __Certificate.cert-manager.io/v1__ a provede patch do Ingress se jménem Secret, ve kterém bude obsažen certifikát a privátní klíč vystavený pomocí ACME protokolu.

Cert-manager je možné používat jako certifikační autoritu, která má k dispozici certifikát pro vydávání a nebo je možné jej integrovat s certifikační autoritou třetí strany.