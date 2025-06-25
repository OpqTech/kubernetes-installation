# KUBERNETES INSTALLATION STEPS

## STEP 1: MASTER NODE INSTALLATION

- Create EC2 Instance from UBUNTU AMI with type t2.medium (2 core CPU and 4GB Ram)
- Github URL: https://github.com/OpqTech/kubernetes-installation

### COMMANDS:
```
git clone https://github.com/OpqTech/kubernetes-installation.git 
cd k8sinstall
sudo sh installk8s-1.30.sh
```

## STEP 2: Kubernetes node template is now ready create an AMI from this instance to create worker nodes

### To create an AMI from an instance
1. Right-click on the instance you want to use as the basis for your AMI or Click-on Actions button.
2. Action --> Image --> Create Image

Once the Ami is available (usually it takes 2-8 minutes to get ready), create instances with t2.micro to
create worker nodes.

## STEP 3: Login back to Master instance created in STEP 1

### Initializing Master Server [root user]

```
sudo su
kubeadm init
```
>Note: Copy the command along with token generated and keep it in a separate file, we need to run this command on worker nodes

### Configuring Kube [ubuntu user]

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### Installing a POD network on master node [ubuntu user]
```
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```

## STEP 4: Initialize WORKER NODES [ssh to worker nodes created from STEP 2]

```
sudo su --> switch to root user

echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.conf && \
sysctl -w net.ipv4.ip_forward=1 && \
sysctl -p

kubeadm join <TOKEN> [Command from STEP 3] --> To connect worker node to Master
```

## STEP 5: Login back to Master instance created in STEP 1

```
kubectl get nodes
```
