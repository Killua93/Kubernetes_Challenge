
## K8s setup master and slave 

in this project we will setup k8s master-slave cluster

1. Kubernetes-master
launch an ec2 instance with ami 24.02 and size t2.medium also open port 22 and http allow traffic anywhere
![image1](https://github.com/user-attachments/assets/1c48c264-7a05-48e7-901c-2a708e0cf2af)
![image2](https://github.com/user-attachments/assets/95c119cc-fc0c-4111-889c-3f296e155b21)
![image3](https://github.com/user-attachments/assets/88178f12-3440-4898-ae85-b4bb7467623d)
Done!!

2. kubernetes-slave
Launch an ec2 instance with ami 24.02 and size t2.micro also open port 22 and http
wait for both instance to get ready then connect
![image4](https://github.com/user-attachments/assets/0525953c-a29e-4b6a-bacf-c2b13f8d3db4)

3. kubeadm installation
login to master node and create a script called k8sinstall.sh

##########

sudo apt update

-#install docker
sudo apt install docker.io -y

sudo apt-get update

-# apt-transport-https may be a dummy package; if so, you can skip that package
sudo apt-get install -y apt-transport-https ca-certificates curl gpg

-# creating directory
sudo mkdir -p -m 755 /etc/apt/keyrings

-# downloading pacakges and copying it to above created directory
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

-# This overwrites any existing configuration in /etc/apt/sources.list.d/kubernetes.list
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

-# installing components of k8s
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl

###########

Run script 

sudo bash k8sinstall.sh

Then login to slave instance and install the same script
![image6](https://github.com/user-attachments/assets/f60adb5d-073d-432a-9491-4a1a60bd41fc)

4. kubeadm join 

Then go to master instance and run below commands

sudo kubeadm init --ingore-preflight-errors
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:
export KUBECONFIG=/etc/kubernetes/admin.conf

![image5](https://github.com/user-attachments/assets/31f37f69-6894-4656-b9b6-30d927ca0cd6)

now go to slave node and run below commands

sudo kubeadm join 172.31.18.202:6443 --token bc695q.z5lxs773skehzjjm --discovery-token-ca-cert-hash sha256:0c2bc90ad867bca166cbec8f94095ae150761af74245d4a79e2ef53dee2c7c36 --ignore-preflight-errors=all

![image7](https://github.com/user-attachments/assets/dacee16c-ee78-4f8d-805a-84442a8de23b)

5. now test setup by running kubectl get nodes all nodes should be running

(note: if nodes are not running run below commands)
# Network Plugin = calico
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.0/manifests/calico.yaml
> sudo swapoff -a
> exit
!!Also make sure u unable all traffic/ all ports to master node!!

![image8](https://github.com/user-attachments/assets/ce6fc348-edca-4618-bf7d-c3153fa3cb77)



