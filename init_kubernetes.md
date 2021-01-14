# Init master node
the number of available CPUs is at least 2
This guild based on ubuntu server.
reference: https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

### Letting iptables see bridged traffic
```
sudo modprobe br_netfilter  # enable br_netfilter module 
lsmod | grep br_netfilter  # check
```

```
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sudo sysctl --system
```

### Install container runtime

kubernetes version: v1.20.2, support docker version: 19.03
reference: https://docs.docker.com/engine/install/ubuntu/
```
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker <your-user>  # log out and log in
```

### Installing kubeadm, kubelet and kubectl

kubeadm: the command to bootstrap the cluster.

kubelet: the component that runs on all of the machines in your cluster and does things like starting pods and containers.

kubectl: the command line util to talk to your cluster.

```
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

# Creating a cluster with kubeadm

reference: https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/

### Initializing your control-plane node

disable swap
```
sudo swapoff -a
```


log in as root:
```
sudo su -
```

```
kubeadm init
```

after success
To start using your cluster, you need to run the following as a regular user:
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
Alternatively, if you are the root user, you can run:
```
export KUBECONFIG=/etc/kubernetes/admin.conf
```

# Joining your nodes

Become root (e.g. sudo su -)
```
kubeadm join 192.168.2.53:6443 --token 94tqyr.eut4j144ntbvd49o     --discovery-token-ca-cert-hash sha256:c7d119e739a8969974c9f5a186e244a922b183224477f6e978cd0260b02c5471
```