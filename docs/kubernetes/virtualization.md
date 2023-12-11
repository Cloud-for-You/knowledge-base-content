+++
title="Virtualizace"
weight=1
+++

Pro virtualizace je využito nástroje __multipass__, který je možné do MacOS doinstalovat pomocí __HomeBrew__. Navíc je možnost využívat cloud-init pro úpravu takto vytvářených virtuálních serverů.

### Instalace virtualizačního prostředí
```sh
brew install multipass
```

### Příprava cloud-init
Vytvoříme soubor s uživatelem definovanými parametry pro cloud-init
```yaml
#cloud-config
bootcmd:
  - printf "overlay" >> /etc/modules-load.d/k8s.conf
  - printf "br_netfilter" >> /etc/modules-load.d/k8s.conf
  - [sudo, modprobe, overlay]
  - [sudo, modprobe, br_netfilter]
  - printf "net.bridge.bridge-nf-call-iptables  = 1" > /etc/sysctl.d/k8s.conf
  - printf "net.bridge.bridge-nf-call-ip6tables = 1" > /etc/sysctl.d/k8s.conf
  - printf "net.ipv4.ip_forward                 = 1" > /etc/sysctl.d/k8s.conf
  - [sudo, sysctl, --system]

packages:
  - software-properties-common
  - apt-transport-https
  - ca-certificates
  - curl
  - gnupg-agent
  - gpg

runcmd:
  # Install CRI-O
  - /usr/local/bin/install_prerequisites.sh
  
write_files:
  - path: /usr/local/bin/install_prerequisites.sh
    owner: root:root
    permissions: '0755'
    content: |
      #!/bin/bash
      
      osfile="/etc/os-release"
      osname=$(awk -F= '$1 == "NAME" {print $2}' $osfile | tr -d '"' | tr 'A-Z' 'a-z' | tr ' ' '_')
      osrelease=$(awk -F= '$1 == "VERSION_ID" {print $2}' $osfile | tr -d '"' | tr '.' '_')
      
      function install_packages() {
        echo "Install CRI-O ......" >&2
        OS=xUbuntu_22.04
        CRIO_VERSION=1.24
        K8S_VERSION=v1.28
         
        # CRI-O
        echo "deb [signed-by=/usr/share/keyrings/libcontainers-archive-keyring.gpg] https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/${OS}/ /" > /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list
        echo "deb [signed-by=/usr/share/keyrings/libcontainers-crio-archive-keyring.gpg] https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/${CRIO_VERSION}/${OS}/ /" > /etc/apt/sources.list.d/devel:kubic:libcontainers:stable:cri-o:${CRIO_VERSION}.list
        curl -L https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/${OS}/Release.key | gpg --dearmor -o /usr/share/keyrings/libcontainers-archive-keyring.gpg
        curl -L https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/${CRIO_VERSION}/${OS}/Release.key | gpg --dearmor -o /usr/share/keyrings/libcontainers-crio-archive-keyring.gpg
        apt update
        apt install -y cri-o cri-o-runc containernetworking-plugins cri-tools
      
        # KUBEADM
        curl -fsSL https://pkgs.k8s.io/core:/stable:/${K8S_VERSION}/deb/Release.key | sudo gpg --dearmor -o /usr/share/keyrings/kubernetes-apt-keyring.gpg
        echo "deb [signed-by=/usr/share/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/${K8S_VERSION}/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
        apt update
        apt install -y kubelet kubeadm kubectl
        apt-mark hold kubelet kubeadm kubectl
      }
      
      function configure_runtime() {
        cat > /etc/crio/crio.conf.d/02-cgroup-manager.conf <<EOF
      [crio.runtime]
        cgroup_manager = "systemd"
      EOF
        cat > /etc/crio/crio.conf.d/03-network.conf <<EOF
      network_dir = "/etc/cni/net.d/"
      plugin_dirs = [
        "/opt/cni/bin/",
        "/usr/lib/cni",
      ]
      EOF
      
        chown root:root /etc/crio/crio.conf.d/02-cgroup-manager.conf /etc/crio/crio.conf.d/03-network.conf
        chmod 0644 /etc/crio/crio.conf.d/02-cgroup-manager.conf /etc/crio/crio.conf.d/03-network.conf
        systemctl enable crio
        systemctl start crio
      }
      
      if [ ! -f $osfile ]; then
        echo "cannot open $osfile for reading to determine if on supported OS" >&2
        exit 1
      else
        if [ "$osname" != "ubuntu" ]; then
          echo "The script supported only Ubuntu OS" >&2
          exit 1
        fi
        osfull=${osname}_${osrelease}
      fi
      
      install_packages
      configure_runtime
```

### Start VM serverů pro běh Kubernetes clusteru s upraveným cloud-init
```sh
multipass launch lunar --name k8s-master --cpus 2 --disk 20G --memory 2G --cloud-init <path_file>
multipass launch lunar --name k8s-worker01 --cpus 2 --disk 20G --memory 2G --cloud-init <path_file>
multipass launch lunar --name k8s-worker02 --cpus 2 --disk 20G --memory 2G --cloud-init <path_file>
```

