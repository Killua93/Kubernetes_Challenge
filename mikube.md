
## How to setup Minikube Kubernetes cluster

Prerequisites
To follow along with this tutorial, you’ll need the following:

● An AWS account (if you’re setting up on an AWS instance).

● Basic knowledge of AWS and Linux terminal commands.


Setting Up Minikube on AWS Instance:

1. Create an ec2 instance as shown below
Go to ec2 click on create an instance give name as Minikube
select ami as ubuntu 24
![image1](https://github.com/user-attachments/assets/6836c3c6-66ed-4834-a7bb-f9ad4cfe48dd)

2. Then use t2.medium instance size and create key-pair with name 
Minikube
![image2](https://github.com/user-attachments/assets/1bdb8513-e804-4416-bb77-7cf7dee94202)

3. open port 22 http allow traffic and click on create instance....done
![image3](https://github.com/user-attachments/assets/c4f5e139-9de1-4327-860d-7850e8fb441b)

4. once instance is ready connect to it and run below commands 
sudo apt update .......(update packages)
![image4](https://github.com/user-attachments/assets/4ee5b7f5-256d-4f7b-a73a-25248bba7b48)

![image5](https://github.com/user-attachments/assets/22a98b61-53cb-4187-83a9-ccf0711f590d)

5. install docker and ad user to docker group by below commands
sudo apt install docker.io -y   ...(install docker)
sudo usermod -aG docker $USER && newgrp docker ....(to run docker as root)
![image6](https://github.com/user-attachments/assets/0f233465-16bb-41dc-9196-1986f5d19fef)

6. Now its time to install minikube and kubectl
sudo apt install -y curl wget apt-transport-https  (downloading prerequisites package for minikube)
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64    ..(downloading package for minikube)
chmod +x minikube
sudo mv minikube /usr/local/bin/    ....(Make it executable and move it into your path)

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"  ....(downloading packages of kubectl)
chmod +x kubectl
sudo mv kubectl /usr/local/bin/  ....(Make it executable and move it into your path)

minikube start --driver=docker --vm=true  (starting minikube by docker)
minikube status  (to check if installed properly)

![image7](https://github.com/user-attachments/assets/b670d5bd-151c-4f59-b896-887072deddc7)

7. minikube has been install now you can test by running below commands

kubectl get nodes

![image8](https://github.com/user-attachments/assets/dbc9a906-7c78-406a-9587-9f65a7d2b239)





