# k8s-demo
k8s-demo

# Installing Kubernetes on Ubuntu 20.04 running on AWS EC2 Instances and deploying an Nginx server on Kubernetes

##In this tutorial, I will guide you through installing Kubernetes on Ubuntu instances on AWS and then demonstrate how to deploy a simple Nginx server on Kubernetes.

##In order to do this, we will spin up two Ubuntu instances from the AWS Console. One of them will be configured as the Master node, while the other will be the worker node.

##When configuring the instances, we should choose at least `2 CPU Core` and `2GB RAM` at minimum to get the system working efficiently. In terms of instance type,`t2.medium` does the job so we will use it to satisfy the minimum infrastructure requirement.

**Ports for the Control-plane (Master) Node(s)
```
1. TCP 6443      → For Kubernetes API server
2. TCP 2379–2380 → For etcd server client API
3. TCP 10250     → For Kubelet API
4. TCP 10259     → For kube-scheduler
5. TCP 10257     → For kube-controller-manager
6. TCP 22        → For remote access with ssh
7. UDP 8472      → Cluster-Wide Network Comm. — Flannel VXLAN
```
**Ports for the Worker Node(s)
```
1. TCP 10250       → For Kubelet API
2. TCP 30000–32767 → NodePort Services†
3. TCP 22          → For remote access with ssh
4. UDP 8472        → Cluster-Wide Network Comm. — Flannel VXLAN
```
**Part 2 Creating instances

1. Go to the AWS Console, EC2 Service and hit on `Launch instance`.

2. Name your instance `kube-master` and choose Ubuntu 20.4 as AMI

3. Choose `t2.medium`as instance type, choose your own key, select existing security groups and choose the master security group that we just created and finally hit on `launch instance`

4. Repeat the steps above to create the worker instance. Keep the same configuration and just name it `kube-worker-1`and choose the `K8-Worker-SG` security group.

5. Wait for the instances to boot up. Then ssh into both of them.

**Part 3 Working with instances

```sudo hostnamectl set-hostname master```

  Then write `bash`and hit enter to see the new hostname.
  
**Before installing Kubernetes packages, we should update the system.

```sudo apt-get update``` 

**Now, we can install helper packages for Kubernetes.

```sudo apt-get install -y apt-transport-https gnupg2```

**Continue installing the helper packages

```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
```

**We need to update the system again after we are done with the helper packages.

```sudo apt-get update```

**Continue with the Kubernetes installation including Docker

```sudo apt-get install -y kubectl kubeadm kubelet kubernetes-cni docker.io```

**Now we have to start and enable Docker service.
```
sudo systemctl start docker
sudo systemctl enable docker
```
**For the Docker group to work smoothly without using sudo command, we should add the current user to the `Docker group`.

```sudo usermod -aG docker $USER```

**Now we run the following command so that the changes take affect immediately.

```newgrp docker```

**As a requirement, update the `iptables` of Linux Nodes to enable them to see bridged traffic correctly. Thus, you should ensure `net.bridge.bridge-nf-call-iptables` is set to `1` in your `sysctl` config and activate `iptables` immediately.

```
cat << EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sudo sysctl — system
```
# We are done with the initial configuration of the master node. Now we will repeat all 10 steps for the worker node. The only difference would be the name of the node.

**Change the terminal to the worker node instance and change the host name

```sudo hostnamectl set-hostname worker```

**Then write `bash`and hit enter to see the new hostname.

**Before installing Kubernetes packages, we should update the system.

```sudo apt-get update```

**Now, we can install helper packages for Kubernetes.

```sudo apt-get install -y apt-transport-https gnupg2```

**Continue installing the helper packages
```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
```

**We need to update the system again after we are done with the helper packages.

```sudo apt-get update```

**Continue with the Kubernetes installation including Docker

```sudo apt-get install -y kubectl kubeadm kubelet kubernetes-cni docker.io```

**Now we have to start and enable Docker service.
```
sudo systemctl start docker
sudo systemctl enable docker
```

**For the Docker group to work smoothly without using sudo command, we should add the current user to the `Docker group`.

```sudo usermod -aG docker $USER```

**Now we run the following command so that the changes take affect immediately.

```newgrp docker```

**As a requirement, update the `iptables` of Linux Nodes to enable them to see bridged traffic correctly. Thus, you should ensure `net.bridge.bridge-nf-call-iptables` is set to `1` in your `sysctl` config and activate `iptables` immediately.

```
cat << EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sudo sysctl — system
```

# Part 4 — Setting Up Master Node for Kubernetes

**In this part, we will execute commands of on Master Node only.

**1. We will pull the packages for Kubernetes beforehand

```sudo kubeadm config images pull```

**By default, the Kubernetes cgroup driver is set to system, but docker is set to systemd. We need to change the Docker cgroup driver by creating a configuration file `/etc/docker/daemon.json` and adding the following line then restart deamon, docker and kubelet:

```
echo '{"exec-opts": ["native.cgroupdriver=systemd"]}' | sudo tee /etc/docker/daemon.json
sudo systemctl daemon-reload
sudo systemctl restart docker
sudo systemctl restart kubelet
```

**At this stage, the `kubeadm` will prepare the environment for us. For this we need the private IP of the master node.

```
sudo kubeadm init — apiserver-advertise-address=172.31.21.161 — pod-network-cidr=10.244.0.0/16 # Use your master node’s private IP
```

**If you did everything correctly, you should see the following screen.

![image](https://user-images.githubusercontent.com/32533015/205841074-43fe82ec-38f2-40c0-84f0-5fda55efc8ab.png)



