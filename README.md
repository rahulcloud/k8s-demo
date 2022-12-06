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



