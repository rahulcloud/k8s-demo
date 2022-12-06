# k8s-demo
k8s-demo

# Installing Kubernetes on Ubuntu 20.04 running on AWS EC2 Instances and deploying an Nginx server on Kubernetes

##In this tutorial, I will guide you through installing Kubernetes on Ubuntu instances on AWS and then demonstrate how to deploy a simple Nginx server on Kubernetes.

##In order to do this, we will spin up two Ubuntu instances from the AWS Console. One of them will be configured as the Master node, while the other will be the worker node.

##When configuring the instances, we should choose at least `2 CPU Core` and `2GB RAM` at minimum to get the system working efficiently. In terms of instance type,`t2.medium` does the job so we will use it to satisfy the minimum infrastructure requirement.

**Ports for the Control-plane (Master) Node(s)

1. TCP 6443      → For Kubernetes API server
2. TCP 2379–2380 → For etcd server client API
3. TCP 10250     → For Kubelet API
4. TCP 10259     → For kube-scheduler
5. TCP 10257     → For kube-controller-manager
6. TCP 22        → For remote access with ssh
7. UDP 8472      → Cluster-Wide Network Comm. — Flannel VXLAN
