# k8s components

![k8s-components](https://user-images.githubusercontent.com/108318918/218641410-1f708fdc-bab7-4eeb-aa5e-e0c2bb4c71e2.jpg)

# K8S cluster set-up with containerd
System Specification:

OS: Ubuntu 20.04 LTS server
RAM: 4GB or more
Disk: 30GB+
CPU: 2 core or more
Swap Memory: Diable
Two nodes: VirtualBox was used to launch both nodes(Master Hostname: controlplane and Worker Hostname: computeplaneone)
Network: Bridge was used to have internal and external access (if not we can use 2 interface, one for NAT and one as host-only adaptor)
common tasks/commands to execute on all the nodes
Docker and Containerd containerdv2.0
