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

## INSTALLING KUBERNETES

## _Steps for Both Master and Slave_

_**Note: Install container runtime Containerd on both master and slave following below link:**_

[**Docker official installation page for ubuntu**](https://docs.docker.com/engine/install/ubuntu/)

_**This will install docker and containerd both. Kubernetes is going to use containerd for cluster, as docker is not supported from 1.23 version onwards.**_

i. Create containerd configuration on both master and slave.

```
sudo mkdir -p /etc/containerd
sudo containerd config default | sudo tee /etc/containerd/config.toml

## Edit /etc/containerd/config.toml and set SystemdCgroup = true

sudo vim /etc/containerd/config.toml 
sudo systemctl restart containerd
```

ii. Run the following commands for installing kubeadm and other components as root:

```
mkdir -p -m 755 /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update
sudo apt install -y kubeadm=1.28.1-1.1 kubelet=1.28.1-1.1 kubectl=1.28.1-1.1

# use below command to check kubectl and kubeadm versions
kubectl version --client
kubeadm version
```

c) Install Kubernetes on master Ubuntu GCP instance using kubeadm

### _Solution:_

**Run below Step on Kubernetes Master only**

## _Steps for Kubernetes Master_

i. Initialize kubeadm using the following command:

`kubeadm init --apiserver-advertise-address=<control-plane-internal-ip> --pod-network-cidr=192.168.0.0/16 --ignore-preflight-errors=NumCPU --kubernetes-version 1.28.1`

<img width="938" alt="image" src="https://github.com/vistasunil/CT_DevOps_live_Module9/assets/37858762/f0c83fbc-22f7-479a-89b7-38a580533d02">

<img width="727" alt="image" src="https://github.com/vistasunil/CT_DevOps_live_Module9/assets/37858762/28775f4a-b0eb-45d6-8172-4b236d2352a0">

**Run below Steps on Kubernetes Slave only that you want to connect to cluster**

ii. In the output of the previous command, you will get a command, take this command, and run it in the slave as root user

_ **Note:** If you face any issue with port while connecting slave to master, then open that port on master server by updating the firewall attached to it._

<img width="739" alt="image" src="https://github.com/vistasunil/CT_DevOps_live_Module9/assets/37858762/fa6b48c6-b25b-408c-a27f-2f01c97c338e">

iii. On the master node, exit to the normal user say ubuntu, and execute the following commands:

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

![image](https://user-images.githubusercontent.com/37858762/236329750-ef0d3114-ce34-4745-8d53-f793f2496bb0.png)

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

**Enable metrics server:
Create a YAML file `metrics-server.yaml` with below content:

```
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  labels:
    k8s-app: metrics-server
    rbac.authorization.k8s.io/aggregate-to-admin: "true"
    rbac.authorization.k8s.io/aggregate-to-edit: "true"
    rbac.authorization.k8s.io/aggregate-to-view: "true"
  name: system:aggregated-metrics-reader
rules:
- apiGroups:
  - metrics.k8s.io
  resources:
  - pods
  - nodes
  verbs:
  - get
  - list
  - watch
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  labels:
    k8s-app: metrics-server
  name: system:metrics-server
rules:
- apiGroups:
  - ""
  resources:
  - pods
  - nodes
  - nodes/stats
  - namespaces
  - configmaps
  verbs:
  - get
  - list
  - watch
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server-auth-reader
  namespace: kube-system
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: extension-apiserver-authentication-reader
subjects:
- kind: ServiceAccount
  name: metrics-server
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server:system:auth-delegator
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:auth-delegator
subjects:
- kind: ServiceAccount
  name: metrics-server
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  labels:
    k8s-app: metrics-server
  name: system:metrics-server
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:metrics-server
subjects:
- kind: ServiceAccount
  name: metrics-server
  namespace: kube-system
---
apiVersion: v1
kind: Service
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server
  namespace: kube-system
spec:
  ports:
  - name: https
    port: 443
    protocol: TCP
    targetPort: https
  selector:
    k8s-app: metrics-server
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server
  namespace: kube-system
spec:
  selector:
    matchLabels:
      k8s-app: metrics-server
  strategy:
    rollingUpdate:
      maxUnavailable: 0
  template:
    metadata:
      labels:
        k8s-app: metrics-server
    spec:
      containers:
      - args:
        - --cert-dir=/tmp
        - --secure-port=4443
        - --kubelet-preferred-address-types=InternalIP
        - --kubelet-insecure-tls
        image: k8s.gcr.io/metrics-server/metrics-server:v0.4.1
        imagePullPolicy: IfNotPresent
        livenessProbe:
          failureThreshold: 3
          httpGet:
            path: /livez
            port: https
            scheme: HTTPS
          periodSeconds: 10
        name: metrics-server
        ports:
        - containerPort: 4443
          name: https
          protocol: TCP
        readinessProbe:
          failureThreshold: 3
          httpGet:
            path: /readyz
            port: https
            scheme: HTTPS
          periodSeconds: 10
        securityContext:
          readOnlyRootFilesystem: true
          runAsNonRoot: true
          runAsUser: 1000
        volumeMounts:
        - mountPath: /tmp
          name: tmp-dir
      nodeSelector:
        kubernetes.io/os: linux
      priorityClassName: system-cluster-critical
      serviceAccountName: metrics-server
      volumes:
      - emptyDir: {}
        name: tmp-dir
---
apiVersion: apiregistration.k8s.io/v1
kind: APIService
metadata:
  labels:
    k8s-app: metrics-server
  name: v1beta1.metrics.k8s.io
spec:
  group: metrics.k8s.io
  groupPriorityMinimum: 100
  insecureSkipTLSVerify: true
  service:
    name: metrics-server
    namespace: kube-system
  version: v1beta1
  versionPriority: 100
```

Deploy this file using kubectl:

```
kubectl apply -f metrics-server.yaml
```

You should see below components getting deployed:
![Screenshot 2024-07-07 at 14 33 43](https://github.com/vistasunil/CT_DevOps_WS_Module6/assets/37858762/c185fef1-1b80-4a5f-bf0c-992dab002c7a)


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
