# Kubernetes configurations
Commands for Kubernetes

## Installation
```bash
sudo swapoff -asudo sed -i.bak -r 's/(.+ swap .+)/#\1/' /etc/fstabsudo modprobe overlaysudo modprobe br_netfiltersudo tee /etc/sysctl.d/kubernetes.conf<<EOFnet.bridge.bridge-nf-call-ip6tables = 1net.bridge.bridge-nf-call-iptables = 1net.ipv4.ip_forward = 1EOF
sudo apt updatesudo apt install -y apt-transport-httpssudo apt install -y kubelet kubeadm kubectl cri-o cri-o-runcsudo systemctl daemon-reloadsudo systemctl restart criosudo systemctl enable criosystemctl status criosudo systemctl enable kubeletsudo cp -f /etc/kubernetes/admin.conf ~/.kube/configsudo chown $(id -u):$(id -g) ~/.kube/configkubectl cluster-info```

## Setup Control Plane and Worker nodes

### Control Plane
```bash
sudo kubeadm config images pull --cri-socket unix:/var/run/crio/crio.socksudo kubeadm init --pod-network-cidr=10.234.0.0/16 --cri-socket unix:/var/run/crio/crio.sock

curl https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/tigera-operator.yaml -o calico--tigera-operator.yamlcurl https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/custom-resources.yaml -o calico--custom-resources.yamlvi calico--tigera-operator.yamlkubectl create -f calico--tigera-operator.yamlvi calico--custom-resources.yaml kubectl create -f calico--custom-resources.yaml
```

### Worker
```bash
sudo kubeadm -v=5 join 10.234.15.164:6443 --token a6q747.gromq5fytbmwj3fv --discovery-token-ca-cert-hash sha256:edcba9876543210edcba9876543210edcba9876543210edcba9876543210edcb

```
