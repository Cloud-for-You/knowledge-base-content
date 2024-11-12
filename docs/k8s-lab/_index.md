---
Title: K8S Lab
weight: 1
draft: false
---

Jednotlivé kapitoly je možné provozovat i v produkčním prostředí za předpokladu, že využijeme malé úpravu, které nemusí být v jednotlivých kapitolách popisovány. Zde se budeme věnovat prostředí pro testovací účely. Jednotlivé komponenty budeme stavět na virtuálních strojích. Jako základ pro virtualizaci slouží __Mac Mini M1__ a všechny virtuální servery jsou stavěny na architektuře arm64.

### Parametry virtuálních strojů na kterých je kubernetes cluster postaven

Hostname | Typ | CPU | Memory | Disk
------------- | --------------- | ------ | -------- | -----
load-balancer | load-balancer   | 1 core | 1 GB Ram | 10 GB
k8s-master    | control plane   | 2 core | 2 GB Ram | 20 GB
k8s-workerXX  | compute         | 2 core | 2 GB Ram | 20 GB

#### Load balancer
Server, který je vystaven před Kubernetes clusterem a provádí balancování provozu na API samotného clusteru. Pokud by jsme balancer nevystavovali. Bude obtížné provést upgrade na HA řešení, které nám umožní provozovat vysoce dostupné řešení s počtem tří serverů v control plane. Jako balancer je možné používat opensource řešení __haproxy__ případně komerční produkty F5 nebo balancery dostupné v public cloudech.

#### Control plane
Jedná se o servery na kterých poběží základní core celého kubernetes clusteru. Komponenty pro cluster jsou ETCD databáze, API server, Controller a Scheduller. O jednotlivých komponentách a jejich nastavení bude pojednáváno v samostatných kapitolách. Pro základní testování nám bude stačit mít controlplane pouze v jedné instanci.

#### Compute plane
Servery obstarávající běžný workload deployovaných aplikací. Pro jednotlivé položky budou servery deployovány v počtu dvou kusů.

### Zdroje ze kterých je čerpáno
[Kubernetes dokumentace](https://kubernetes.io/docs/home/)