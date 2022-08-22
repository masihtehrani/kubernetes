# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.define "master" do |master|
    master.vm.network "private_network", ip: "192.168.56.100"
    config.vm.hostname = "master"
  end
  1.time do |i|
    config.vm.define "worker-#{i+1}" do |node|
      node.vm.network "private_network", ip: "192.168.56.#{i+101}"
      node.vm.hostname = "worker-#{i+1}"
    end
  end
  config.vm.provision "shell", inline: <<-SHELL
  echo '192.168.56.100  master
  192.168.56.101  worker-1
  192.168.56.102  worker-2' > /etc/hosts
    apt-get update
    apt-get -y upgrade
    apt-get install -y ca-certificates curl apt-transport-https
    curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
    echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
    apt-get update
    apt-get install -y kubelet kubectl kubeadm containerd
    apt-mark hold kubelet kubectl kubeadm containerd
    cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF
  modprobe overlay
  modprobe br_netfilter
  cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward = 1
EOF
  sysctl --system
  mkdir -p /etc/containerd
  containerd config default | sudo tee /etc/containerd/config.toml
  systemctl restart containerd
  swapoff -a
  SHELL
end
