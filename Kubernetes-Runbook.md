<div align="center">
<img src=https://static.wixstatic.com/media/1c706c_a5df0ad56f894928bf858a74ba744b32~mv2.png/v1/fit/w_2500,h_1330,al_c/1c706c_a5df0ad56f894928bf858a74ba744b32~mv2.png width="400" height="200">
 </div>

# <div align="center"> KUBERNETES RUNBOOK </p>

# <div align="center"> DevOps Workshop Training </div>

# <div align="right"> $`\textcolor{brown}{\text{Contact us: }}`$  &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; </div>

<div align="right"> T O A C C E L E R A T E Y O U R C A R E E R G R O W T H </div>

### <div align="right"> For questions and more details: </div>

<div align="right"> <img src=https://w7.pngwing.com/pngs/759/922/png-transparent-telephone-logo-iphone-telephone-call-smartphone-phone-electronics-text-trademark-thumbnail.png width="20" height="20"> +91 98712 72900 </div>

<div align="right"> <img src=https://pbs.twimg.com/profile_images/1450734615946219520/jmBHQRRa_400x400.jpg width="20" height="20"> https://www.thecloudtrain.com </div>

<div align="right"> <img src=https://icons.iconarchive.com/icons/martz90/circle/512/email-icon.png width="20" height="20"> support@thecloudtrain.com </div>

<div align="right"> <img src=https://png.pngtree.com/png-vector/20221018/ourmid/pngtree-whatsapp-icon-png-image_6315990.png width="20" height="20"> +91 98712 72900 </div>

#
</br>

## $`\textcolor{red}{\text{NOTE: USE UBUNTU 22.04 VIRTUAL MACHINES FOR ALL THE LABS}}`$

## _Find solutions to your assingment below:_

## Exercise 1: Accomplish below task to complete this exercise:

a) Create two Ubuntu 22.04 compute instances, one for Kubernetes master and other for slave

### _Solution:_

Setup two GCP compute instances using same steps as previous exercises

b) Install prerequisites, Kubeadm, Kubectl and Kubelet on both the servers

### _Solution:_

**Run below Steps on Both Master and Slave server**

i. Run the following commands one by one for installing kubeadm as **root** user

```
sudo su – 

echo '{"exec-opts": ["native.cgroupdriver=systemd"]}' >> /etc/docker/daemon.json

systemctl daemon-reload && systemctl restart docker.service

apt-get update && apt-get install -y apt-transport-https curl

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -

cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF

apt-get update

apt install -y kubeadm=1.23.1-00 kubelet=1.23.1-00 kubectl=1.23.1-00
```

**use below command to check kubectl and kubeadm versions**

`kubectl version --client`

`kubeadm version`

![image](https://github.com/vistasunil/CT_DevOps_WS_Module6/assets/37858762/4ea256da-89e4-4d72-a5e3-6a00dbae2fcc)

c) Install Kubernetes on master Ubuntu GCP instance using kubeadm

### _Solution:_

**Run below Step on Kubernetes Master only**

i. Initialize kubeadm using the following command:

```
sudo su -

kubeadm init --apiserver-advertise-address=<ip-address-master> --pod-network-cidr=192.168.0.0/16 --ignore-preflight-errors=NumCPU
```

![image](https://github.com/vistasunil/CT_DevOps_WS_Module6/assets/37858762/6725cd8c-d9dd-4b90-b0a2-6069cbfbb586)

![image](https://github.com/vistasunil/CT_DevOps_WS_Module6/assets/37858762/57d30d9b-0de6-4176-85c7-d5924cc77ad0)

**Run below Steps on Kubernetes Slave only that you want to connect to cluster**

ii. In the output of the previous command, you will get a command, take this command, and run it in the slave as root user

_ **Note:** If you face any issue with port while connecting slave to master, then open that port on master server by updating the firewall attached to it._

```
sudo su –

<kubeadm join command from kubeadm init output>
```

![image](https://github.com/vistasunil/CT_DevOps_WS_Module6/assets/37858762/9b4b73d1-fb8d-43c6-b409-4b4e859c6e11)

**Run below Steps on Kubernetes Master**

iii. On the master node, exit to the normal user say ubuntu, and execute the following commands:

```
sudo su – ubuntu
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

![image](https://github.com/vistasunil/CT_DevOps_WS_Module6/assets/37858762/7cec1d82-937f-4af0-98aa-c85f0b8308d4)

iv. Your cluster is ready. You can see cluster info using below command:

`kubectl cluster-info`

![image](https://github.com/vistasunil/CT_DevOps_WS_Module6/assets/37858762/71dddd1e-d6ab-4d12-bea9-ffd7dca52b02)

d) Install calico network plugin to establish network communication between nodes.

### _Solution:_

i. Let's install the network plugin. This will help cluster to communicate. Execute the following commands on master node:

```
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/tigera-operator.yaml
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/custom-resources.yaml
```

e) Check the status of both the nodes using relevant kubectl command.

### _Solution:_

i. The previous command will take some time to take effect. After 4-5 mins, try the following command, if both the nodes are in the ready state, Installation is successful!
ii. Run below command on master node:

`kubectl get nodes`

![image](https://github.com/vistasunil/CT_DevOps_WS_Module6/assets/37858762/82d0d558-579e-4dd8-be53-9e1569a93e46)

## Exercise 2: Accomplish below task to complete this exercise:

a) Create a Kubernetes deployment to run a nginx pod with below params:
  * Name - \<Any\>
  * Labels – app: nginx
  * Replicas – 3
  * Image – nginx: latest
  * Container Port – 80

### _Solution:_

**Run below steps on master node:**

i. Create a file, nginx.yaml using vim editor and enter the following content in it:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

ii. Create the deployment using the following command:

`kubectl create –f nginx.yaml`

![image](https://github.com/vistasunil/CT_DevOps_WS_Module6/assets/37858762/558c31d4-b935-44bc-bbb7-b46dcf4e3b82)

iii. Verify the procedure, by listing the pods:

`kubectl get pods`

![image](https://github.com/vistasunil/CT_DevOps_WS_Module6/assets/37858762/41b2d4a1-512f-4923-9114-e412d3e73985)

You have successfully deployed nginx on Kubernetes!!

b) Create a Kubernetes Nodeport Service with any name to expose this nginx deployment and access it outside the cluster using _**http://\<Server\_Public\_IP\>:\<Nodeport\_Port\>**_

### _Solution:_

**Run below steps on Master node**

i. We need to create a nodeport service for our nginx deployment, run the following command:

`kubectl create service nodeport nginx --tcp=80:80`

![image](https://github.com/vistasunil/CT_DevOps_WS_Module6/assets/37858762/6502ea0d-7d30-4284-9a15-648453dc4d9b)

ii. Note down the Nodeport port, in this case 32256 for this service by running the command. In your case it will be different:

`kubectl get svc nginx`

![image](https://github.com/vistasunil/CT_DevOps_WS_Module6/assets/37858762/ed697fb3-08fe-4c9f-9388-44ee282970c2)

iii. Get the public IP of the master or slave node from GCP console. Verify the working of the service opening _**http://\<Master\_or\_Slave\_Public\_IP\>:\<node\_port\>**_ address on the browser as below:

_ **Note:** You will need to open the nodeport (e.g. 32256 here) in master or slave server firewall where pod is running_

![image](https://github.com/vistasunil/CT_DevOps_WS_Module6/assets/37858762/5ec91d38-758a-4350-bd81-d178ecd1ffc8)
