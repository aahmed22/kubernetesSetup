# Quick setup of Kubernetes 
This was implemented on AWS using instance type "t2.medium" using Ubuntu images

## Prerequistes for seting up Kubernetes for both the Master and node-agent
Below contains a code snippet of things needed to be done on the Ubuntu systems

```sh
# The best approach would be to run this commands as the "root" user.
sudo su
apt-get update -y

# 2. Install docker on the Ubuntu systems 
apt-get install docker.io -y

# 3. These commands below are prerequisites for setting up Kubeadm on Ubuntu
apt-get update && sudo apt-get install -y apt-transport-https curl

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF

# 4. Update the systems again
apt-get update -y

# 5. Install kubeadm
apt-get install -y kubelet kubeadm kubectl
```

## Initializing Kubernetes 
At this point we will need to initialize Kubernetes on our master server. 

```sh
# Include Private IP address of master server
# Adding Calico project to act as our pod network 
kubeadm init --apiserver-advertise-address=PrivateIPaddress --pod-network-cidr=192.168.0.0/16 --ignore-preflight-errors=NumCPU
```

## Setting up "eksctl" command line utility via Windows environment 
This utility is used for creating and managing Kubernetes clusters via AWS Elastic Kubernetes Service.
Note: Make sure you have your AWS access keys configured on your machine. (Windows users: *"C:\Users\username\.aws\credentials"*)

**1. First we will begin with installing "Chocolatey" Make sure to launch powershell as admin**
```ps1
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

**2. Install eksctl on your local machine.**
```ps1
choco install -y eksctl

# If you need to simply upgrade the utility run:
choco upgrade -y eksctl
```
**3. Confirm that the installation was sucessful**
```ps1
eksctl version
```

**4. Now we will run a simple execution of creating a cluster. The following snippet can be saved in a ps1 file.**

```ps1
eksctl create cluster `
--name test-cluster `
--version 1.17 `
--region us-east-1 `
--nodegroup-name linux-nodes `
--node-type t2.micro `
--nodes 2 
```
