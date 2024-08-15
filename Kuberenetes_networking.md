
## KUBERNETES NETWORKING

# Services

The service file defines how networking will be handled on the cluster
Service files are used by developers and administrators to expose to internet and connect applications within the Kubernetes cluster. 
Services can also be used for internal communication between Pods within the cluster, not just for exposing applications externally.

Types of Services

a. Cluster IP:

apiVersion: v1           # use v1

kind: Service             # Service

metadata:

  name: my-service      #name of your service

spec:

  type: ClusterIP              # please select type of service as mentioned below

   selector:

    app : my-app

  ports:

    - name: http  

      protocol: TCP  

      port: 80

      targetPort: 9376         #where u want to expose ur app


b. NodePort: This is the next stage of the ClusterIP where you want to deploy your
application or service that should be accessible to the world without any interruption.
In this Service, the node port exposes the service or application through the static
port on each node’s IP.
You can view the Service YML file and see how to use this service.

apiVersion: v1

kind: Service

metadata:

name: httpd-service

spec:

ports:

- port: 80

targetPort: 80

selector:

name: DevOps

type: NodePort


c. LoadBalancer

Load balancers are used to distribute the traffic between the multiple
pods. With the help of this service object, the services will be exposed via the cloud’s
load balancer.
You can view the Service YML file and see how to use this service.

apiVersion: v1

kind: Service

metadata:

name: svc-lb

spec:

type: LoadBalancer

selector:

tag: DevOps

ports:

- name: port-lb

protocol: TCP

port: 80

targetPort: 80

if u are using aws internal load balancer

metadata:

  name: my-service

  annotations:

    service.beta.kubernetes.io/aws-load-balancer-internal: "true"

If u are using azure internal load balancer:

metadata:

  name: my-service

  annotations:

    service.beta.kubernetes.io/azure-load-balancer-internal: "true"

d. External name

ExternalName
Maps the Service to the contents of the externalName field (for example, to the hostname api.foo.bar.example). The mapping configures your cluster's DNS server to return a CNAME record with that external hostname value. No proxying of any kind is set up.

apiVersion: v1

kind: Service

metadata:

  name: my-service

  namespace: prod

spec:

  type: ExternalName

  externalName: my.database.example.com


e. External IP

External IP:

If there are external IPs that route to one or more cluster nodes, Kubernetes Services can be exposed on those externalIPs. When network traffic arrives into the cluster, with the external IP (as destination IP) and the port matching that Service, rules and routes that Kubernetes has configured ensure that the traffic is routed to one of the endpoints for that Service.

apiVersion: v1

kind: Service

metadata:

  name: my-service

spec:

  selector:

    app.kubernetes.io/name: MyApp

  ports:

    - name: http

      protocol: TCP

      port: 80

      targetPort: 49152

  externalIPs:

    - 198.51.100.32

# Ingress 

In the world of Kubernetes, Ingress is your ticket to managing external traffic to services
within the cluster. Before we dive into the details, let’s recap what we’ve learned so far.
Before Ingress, the Service provides a Load balancer, which is used to distribute the traffic
between multiple applications or pods.
Ingress helps to expose the HTTP and HTTPS routes from outside of the cluster.
Ingress enables Path-based and Host-based routing.
Ingress supports Load balancing and SSL termination

Why do we use Ingress because the load balancer supports the same thing?
Ingress is used to manage the external traffic to the services within the cluster which
provides features like host-based routing, path-based routing, SSL termination, and more.
Where a Load balancer is used to manage the traffic but the load balancer does not provide
the fine-grained access control like Ingress.

![image1](https://github.com/user-attachments/assets/5b6e03ce-4a2f-45aa-b8b5-7c243f5bef72)

1. Ingress backed by a single Service 
There are existing Kubernetes concepts that allow you to expose a single Service (see alternatives). You can also do this with an Ingress by specifying a default backend with no rules.

apiVersion: networking.k8s.io/v1

kind: Ingress

metadata:

  name: test-ingress

spec:

  defaultBackend:

    service:

      name: test

      port:

        number: 80

2. path based routing

apiVersion: networking.k8s.io/v1

kind: Ingress

metadata:

name: ingress-deployment

annotations:

nginx.ingress.kubernetes.io/rewrite-target: /$1

spec:

rules:

- host: example.devops.in

http:

paths:

- path: /

pathType: Prefix

backend:

service:

name: nginx-service

port:

number: 80

- path: /menu

pathType: Prefix

backend:

service:

name: nginx-service2

port:

number: 80

- path: /reviews

pathType: Prefix

backend:

service:

143

name: nginx-service3

port:

number: 80


3. Host based routing

apiVersion: networking.k8s.io/v1

kind: Ingress

metadata:

name: ingress-deployment

annotations:

nginx.ingress.kubernetes.io/rewrite-target: /$1

spec:

rules:

- host: example.devops.in

http:

paths:

- path: /

pathType: Prefix

backend:

service:

name: nginx-service

port:

number: 80

- path: /menu

pathType: Prefix

backend:

service:

name: nginx-service2

port:

number: 80

- path: /reviews

pathType: Prefix

backend:

service:

145

name: nginx-service3

port:

number: 80

- host: example2.devops.in

http:

paths:

- path: /

pathType: Prefix

backend:

service:

name: nginx-service4

port:

number: 80


#  Ingress Controller

# Kubernetes Ingress Controller on Minikube Cluster

### In this demo, we will see how to use ingress controller to route the traffic on different services.

### Pre-requisites to implement this project:
-  Create 1 virtual machine on AWS with 2 CPU, 4GB of RAM (t2.medium)
- Setup minikube on it  href="https://github.com/LondheShubham153/kubestarter/blob/main/minikube_installation.md" Minikube setup.

#

### What we are going to implement:
- In this demo, we will create two deployment and services i.e nginx and apache and with the help of ingress, we will route the traffic between the services

#
## Steps to implement ingress:

1) Create minikube cluster as mentioned in pre-requisites :

#
2) Check minikube cluster status and nodes :
```bash
minikube status
kubectl get nodes
```
#
3) Create one yaml file for apache deployment and service :
```bash
# apache-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: apache-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: apache
  template:
    metadata:
      labels:
        app: apache
    spec:
      containers:
      - name: apache
        image: httpd:2.4
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: apache-service
spec:
  selector:
    app: apache
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

#
4) Apply apache deployment :
```bash
kubectl apply -f apache-deployment.yaml
```

#
5) Create one more yaml file for nginx deployment and service :
```bash
# nginx-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
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
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP

```

#
6) Apply nginx deployment :
```bash
kubectl apply -f nginx-deployment.yaml
```

#
7) Enable the Ingress Controller :
```bash
minikube addons enable ingress
```

#
8) Now create an Ingress resource that routes traffic to the Apache and NGINX services based on the URL path.
```bash
# ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: apache-nginx-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: "tws.com"
    http:
      paths:
      - path: /apache
        pathType: Prefix
        backend:
          service:
            name: apache-service
            port:
              number: 80
      - path: /nginx
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 80
```

#
9) Apply the Ingress resource :
```bash
kubectl apply -f ingress.yaml
```

#
10) To test the Ingress, map the hostname to the Minikube IP in your */etc/hosts* file :
```bash
echo "$(minikube ip) tws.com" | sudo tee -a /etc/hosts
```
OR
Open /etc/hosts file and add your minikube ip and domain name at the last.

#
11) Now, test the routing :

  - curl http://tws.com/apache to access the Apache service.
  ```bash
  curl http://tws.com/apache
  ```
  - curl http://tws.com/nginx to access the NGINX service.
  ```bash
  curl http://tws.com/nginx
  ```
OR


- port forward to access the Apache service on browser.
  ```bash
  kubectl port-forward svc/apache-service 8081:80 --address 0.0.0.0 &
  ```
- port forward to access the NGINX service on browser.
  ```bash
  kubectl port-forward svc/nginx-service 8082:80 --address 0.0.0.0 &
  ```

#

#  Network Policy

By default, a pod can communicate with any other pods whether it’s present in any
namespaces. But if you want to secure your pod by providing access to only known pods or
authorized pods then Kubernetes has the richest feature known as Network Policy. Network
Policy will help you to protect your pod by accessing only authorized pods. So, this way the
pod’s security will be enhanced.
Network Policy allows us to define the rules to communicate between the pods. With the
help of Networking Policy, Kubernetes provides fine-grained controls over what traffic is
allowed or denied which leads to enhancing the security and isolation of your applications.


Policy Rules: Network Policies consist of a set of rules in which you define how the traffic is
allowed or denied. You can specify these rules by pod labels, namespaces, or particular IPs.


Pod Selectors: If you want to apply the Network Policy to the particular pod then you can
use Pod Selector which will select the particular pod and apply the Network Policy on that
pod.


Ingress and Egress: Network Policies allow you to define the Ingress and Egress rules.
Ingress means incoming traffic on the pod from the outside whereas Egress means outgoing
traffic to the internet(anywhere) from the pod itself.

apiVersion: networking.k8s.io/v1

kind: NetworkPolicy

metadata:

  name: default-deny-all

spec:

  podSelector: {}

  policyTypes:

  - Ingress

  - Egress


Default deny all ingress traffic
You can create a "default" ingress isolation policy for a namespace by creating a NetworkPolicy that selects all pods but does not allow any ingress traffic to those pods.
 
---
apiVersion: networking.k8s.io/v1

kind: NetworkPolicy

metadata:

  name: default-deny-ingress

spec:

  podSelector: {}

  policyTypes:

  - Ingress


Allow all ingress traffic
If you want to allow all incoming connections to all pods in a namespace, you can create a policy that explicitly allows that.
 
---
apiVersion: networking.k8s.io/v1

kind: NetworkPolicy

metadata:

  name: allow-all-ingress

spec:

  podSelector: {}

  ingress:

  - {}

  policyTypes:

  - Ingress



Default deny all egress traffic
You can create a "default" egress isolation policy for a namespace by creating a NetworkPolicy that selects all pods but does not allow any egress traffic from those pods.

apiVersion: networking.k8s.io/v1

kind: NetworkPolicy

metadata:

  name: default-deny-egress

spec:

  podSelector: {}

  policyTypes:

  - Egress


Allow all egress traffic
If you want to allow all connections from all pods in a namespace, you can create a policy that explicitly allows all outgoing connections from pods in that namespace. 
---
apiVersion: networking.k8s.io/v1

kind: NetworkPolicy

metadata:

  name: allow-all-egress

spec:

  podSelector: {}

  egress:

  - {}

  policyTypes:

  - Egress


Default deny all ingress and all egress traffic
You can create a "default" policy for a namespace which prevents all ingress AND egress traffic by creating the following NetworkPolicy in that namespace. 
---
apiVersion: networking.k8s.io/v1

kind: NetworkPolicy

metadata:

  name: default-deny-all

spec:

  podSelector: {}

  policyTypes:

  - Ingress

  - Egress


Targeting a range of ports

Namespaces: If you want to apply your Network Policy to the group of pod which is present
in the particular namespace then you can namespaceSelector which will help you to invoke
the Network Policy on all pods within the particular namespace.

...
  ingress:

  - from:

    - namespaceSelector:

        matchLabels:

          user: alice

      podSelector:

        matchLabels:

          role: client
  ...


Targeting multiple namespaces by label
In this scenario, your Egress NetworkPolicy targets more than one namespace using their label names. For this to work, you need to label the target namespaces. For example:
kubectl label namespace frontend namespace=frontend
kubectl label namespace backend namespace=backend
Add the labels under namespaceSelector in your NetworkPolicy document. For example:

apiVersion: networking.k8s.io/v1

kind: NetworkPolicy

metadata:

  name: egress-namespaces

spec:

  podSelector:

    matchLabels:

      app: myapp

  policyTypes:

  - Egress

  egress:

  - to:

    - namespaceSelector:

        matchExpressions:

        - key: namespace

          operator: In

          values: ["frontend", "backend"]

Priority: Network Policy also provides the priority feature in which you define the priority of
the rules which helps you to get fine-grained control over the traffic rules of your application


#CNI plugins

What is CNI? CNI stands for Container Network Interface. As the name suggests, CNI works on the networking level where CNI takes care of the pods and other things in Kubernetes. CNI ensures how the containers and pods should connect to the network. There are many CNIs available in the market but today we will discuss Calico CNI. What is Calico CNI? Calico is an open-source network and network security solution designed for Kubernetes. Calico is one of the most popular CNIs which used to manage the networking between containers, pods, nodes, or multiple clusters. Calico works only on networking to provide fine-grained control over the containers, pods, nodes, or multiple clusters where Services

Why you should use Calico CNI over other CNIs(Features) 
● Advanced Networking Policies : With Calico CNI, we can define fine-grained networking policies over the containers or pods such as which pod can communicate to which other pod and apply rules based on labels, ports, and more. This level of control is not possible through Kubernetes Native Networking.
● Scalability : Calico is known for its Scalability where it can handle large clusters with ease and efficiently manage network traffic which makes it suitable for enterprise-level applications with multiple pods. 
● Cross-Cluster Networking : Calico can be used to connect multiple Kubernetes clusters together, which can be beneficial in hybrid or multi-cluster scenarios. 67 
● Border Gateway Protocol(BGP) routing : Calico supports BGP for routing which is quite good if you want to integrate with on-premises data centers or public cloud environments. ● Security : Calico supports a very good level of security over the network traffic where Calico encrypts the network traffic so only authorized pods can communicate with the respective pods.


Key Concepts and Real-time Example
● IP Address Management : Calico supports managing the IP address for each pod where each pod is assigned to a unique IP address from the cluster’s IP address range. 
● Routing and Network Policy : Calico enables routing for the network traffic between pods. The Network policies can be applied to control traffic between pods. So, you can allow or deny communications between specific pods. 
● Load Balancing : Calico handles load balancing in which it distributes the traffic between multiple pods.
● Security and Encryption : Calico provides security features to protect your Kubernetes clusters. It encrypts the network traffic so that you can ensure only authorized pods can communicate. 
Think of Calico as a traffic control system in a city, where every vehicle(pod) gets a unique plate number and license and follows the traffic rules. The Traffic lights(network policies) ensure safe and fully controlled movement. Police officers(security) check for unauthorized actions and keep the movement controlled.

kubectl get pods -n kube-system

kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml


#DNS in Kubernetes

DNS IN K8S Networking:

DNS for Services and Pods
Kubernetes creates DNS records for Services and Pods. You can contact Services with consistent DNS names instead of IP addresses.
Kubernetes publishes information about Pods and Services which is used to program DNS. Kubelet configures Pods' DNS so that running containers can lookup Services by name rather than IP.
Services defined in the cluster are assigned DNS names. By default, a client Pod's DNS search list includes the Pod's own namespace and the cluster's default domain.
What objects get DNS records?
1.	Services
2.	Pods
3.	apiVersion: v1
4.	kind: Service
5.	metadata:
6.	  name: busybox-subdomain
7.	spec:
8.	  selector:
9.	    name: busybox
10.	  clusterIP: None
11.	  ports:
12.	  - name: foo # name is not required for single-port Services
13.	    port: 1234
14.	---
15.	apiVersion: v1
16.	kind: Pod
17.	metadata:
18.	  name: busybox1
19.	  labels:
20.	    name: busybox
21.	spec:
22.	  hostname: busybox-1
23.	  subdomain: busybox-subdomain
24.	  containers:
25.	  - image: busybox:1.28
26.	    command:
27.	      - sleep
28.	      - "3600"
29.	    name: busybox
30.	---
31.	apiVersion: v1
32.	kind: Pod
33.	metadata:
34.	  name: busybox2
35.	  labels:
36.	    name: busybox
37.	spec:
38.	  hostname: busybox-2
39.	  subdomain: busybox-subdomain
40.	  containers:
41.	  - image: busybox:1.28
42.	    command:
43.	      - sleep
44.	      - "3600"
45.	    name: busybox

Pod's DNS Policy
DNS policies can be set on a per-Pod basis. Currently Kubernetes supports the following Pod-specific DNS policies. These policies are specified in the dnsPolicy field of a Pod Spec.

apiVersion: v1

kind: Pod

metadata:

  name: busybox

  namespace: default

spec:

  containers:

  - image: busybox:1.28

    command:

      - sleep

      - "3600"

    imagePullPolicy: IfNotPresent

    name: busybox

  restartPolicy: Always

  hostNetwork: true

  dnsPolicy: ClusterFirstWithHostNet

Pod's DNS Config
FEATURE STATE: Kubernetes v1.14 [stable]
Pod's DNS Config allows users more control on the DNS settings for a Pod.
The dnsConfig field is optional and it can work with any dnsPolicy settings. However, when a Pod's dnsPolicy is set to "None", the dnsConfig field has to be specified.

apiVersion: v1

kind: Pod

metadata:

  namespace: default

  name: dns-example

spec:

  containers:

    - name: test

      image: nginx

  dnsPolicy: "None"

  dnsConfig:

    nameservers:

      - 192.0.2.1 # this is an example

    searches:

      - ns1.svc.cluster-domain.example

      - my.dns.search.suffix

    options:

      - name: ndots

        value: "2"

      - name: edns0


















