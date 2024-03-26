# k8s components

![k8s-components](https://user-images.githubusercontent.com/108318918/218641410-1f708fdc-bab7-4eeb-aa5e-e0c2bb4c71e2.jpg)

# K8S cluster set-up with containerd
System Specification:

OS: Ubuntu 20.04 LTS server <br>
RAM: 4GB or more <br>
Disk: 30GB+ <br>
CPU: 2 core or more <br>
Swap Memory: Diable <br>
Two nodes: VirtualBox was used to launch both nodes(Master Hostname: controlplane and Worker Hostname: computeplaneone) <br>
Network: Bridge was used to have internal and external access (if not we can use 2 interface, one for NAT and one as host-only adaptor) <br>
common tasks/commands to execute on all the nodes <br>
Docker and Containerd containerdv2.0 <br>

# Execute below commands on both "Master" and "Worker"
### disable swap
sudo swapoff -a

### Create the .conf file to load the modules at bootup
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf <br>
overlay <br>
br_netfilter <br>
EOF <br>

sudo modprobe overlay <br>
sudo modprobe br_netfilter <br>

### sysctl params required by setup, params persist across reboots
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf <br>
net.bridge.bridge-nf-call-iptables  = 1 <br>
net.bridge.bridge-nf-call-ip6tables = 1 <br>
net.ipv4.ip_forward                 = 1 <br>
EOF

### Apply sysctl params without reboot
sudo sysctl --system

### Install Containerd
sudo mkdir -p /etc/apt/keyrings <br>
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg <br>
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null <br>
sudo apt-get update <br>
sudo apt-get install containerd.io -y <br>

### Create containerd configuration
sudo mkdir -p /etc/containerd <br>
sudo containerd config default | sudo tee /etc/containerd/config.toml <br>

### Edit /etc/containerd/config.toml
sudo nano /etc/containerd/config.toml <br>
SystemdCgroup = true <br>
sudo systemctl restart containerd <br>

### Add Kubernetes APT repository and install required packages
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg <br>
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list <br>

sudo apt-get update -y <br>
sudo apt-get install -y kubelet="1.29.0-*" kubectl="1.29.0-*" kubeadm="1.29.0-*" <br>
sudo apt-get update -y <br>
sudo apt-get install -y jq <br>

sudo systemctl enable --now kubelet <br>
sudo systemctl start kubelet <br>

# Commands Only on Master
sudo kubeadm config images pull <br>

sudo kubeadm init <br>

mkdir -p "$HOME"/.kube <br>
sudo cp -i /etc/kubernetes/admin.conf "$HOME"/.kube/config <br>
sudo chown "$(id -u)":"$(id -g)" "$HOME"/.kube/config <br>

### Nwtwork plugin = calico
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.0/manifests/calico.yaml <br>

kubeadm token create --print-join-command <br>

Copy Token <br>


# Commands on Worker

sudo kubeadm reset pre-flight checks <br>

paste token
