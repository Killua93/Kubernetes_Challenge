
# Kubernetes Architecture

Following diagram shows actual kubernetes cluster with all components

![Alt text](https://i.postimg.cc/RhFV9P5Z/kubernetes-architecture.png)

Master Node:

1. Api Server
Api server is basically a front door of kubernetes cluster. whenever we want to interract with components of cluster for example if we want to create pod will run kubectl command which first goes to api server as a request it will validate and then transfer the request to the required component.

2. Etcd
Etcd is a database of your kubernetes all (master and worker) cluster. which stores information such as Pods IP, Nodes, networking configs, etc. it will stores value in database in the form of key-value pair

3. Scheduler
The "event planner" for your containers. When you ask for a container to be run, the Scheduler decides which machine (Node) in your cluster should run it. It considers resource availability and other constraints while making this decision.

4. Controller Manager
Imagine a bunch of small robots that continuously monitor the cluster to make sure everything is running smoothly. If something goes wrong (e.g., a Pod crashes), they work to fix it, ensuring the cluster state matches your desired state.

Worker Node:

1. Kubelet
kubelete is the primary component of the Worker Node which manages the Pods and regularly checks whether the pod is running or not. If pods are not working properly, then kubelet creates a new pod and replaces it with the previous one because the failed pod can’t be restarted hence, the IP of the pod might be changed. Also,
kubelet gets the details related to pods from the API Server which exists on the Master Node.

2. Kube-proxy
 kube-proxy contains all the network configuration of the entire cluster such as pod IP, etc. Kube-proxy takes care of the load balancing and routing which comes under networking configuration
Think of this as the "traffic cop" for network communication either between Pods or from external clients to Pods.

3. Pods
A pod is a very small unit that contains a container or multiple containers where the application is deployed. Pod has a Public or Private IP range that distributes the proper IP to the containers. It’s good to have one container under each pod.

4. Container Engine
To provide the runtime environment to the container, Container Engine is used. In Kubernetes, the Container engine directly interacts with container runtime which is responsible for creating and managing the containers. There are a lot of Container engines present in the market such as CRI-O, containerd, rkt(rocket), etc. But Docker is one of the most used and trusted Container Engine. So, we will use that in our
upcoming day while setting up the Kubernetes cluster.










