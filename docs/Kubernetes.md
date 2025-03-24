# What is Kubernetes and why it is so popular
It is an open source platform designed to automate deployment ,scaling and management of contanierized application. Developed by google . it is developed to mange complex distributed application across various environments from physical to vitrual, from on prem to cloud.
For microservices architecture it is a bst platform to manage application running in an containers.
increase in containers, its important to have a orchestrator or manager.Thats when kubernetes comes into picture.


## Befnefits/Features- 
1. High Availabiltiy -Kubernetes provided HA to application withminial interruption
2. self healing , auto scaling with no interruptions.etcd - store all real time data to enable kubernetes to manager nodes, scale, heal and schedule nodes
4. disaster recovery- backup and restore in case of DR.


## Architecture

![image](https://github.com/user-attachments/assets/08e4d331-5b5a-4bea-ac19-c5efea1daa98)

## kubernetes cluster -- 
atleast one master nod(control plane) and connected with no of worder node. 
each worker node has a kublet (kubernetes running agent) which communicates with master node and control plane.
each worker node also contains no of container whcih are running applcations/microservices within thecontainer.
master node (control plane) has API server(entry point for k8) , controller manager(repair,scale), scheculer(scheduler workser node based on process), etcd(real time workser nodes data, key value storage, current time status of kubernetes cluster).

Virtual Network - imp and manage nodes running .as a sigle unit of network.always have backup of cluster(master node) always have a replica of it as control plane is more imp that running nodes

## Kubernetes COmponents
POd
Services
Ingress
ConfigMap
Secret
Deployment
statefulset
daemonset

Node- it is a physical or virtaul machine. 
Pod is smallest unit in kubernetes, it is an abstraction over container. POD contain container. pod is one application container ususally. in kubernetes each pod get its own IP address.

![image](https://github.com/user-attachments/assets/52d67483-3495-4710-855f-306e55805533)

as you can see from diag. each pod get its own IP which is not exposed or used by external world. reason is it gets changed if
pod die due to heavy load or healing , IP gets changed so IP are not exposed outside.

Service -->  can attach permanent IP address to POD> even if pOD die, service and IP address remains same.
internal service to hid pod while external service is exposed and can be accessded from outside.

Ingress - is a load balancer which manages DNS and service access via URL and port.

Config service -- how to config mongdb and application service url. in config map- we put database url and put external configuration so pod can get external service url. config-map is for only non-confedential 
while secrets - is used to store confidential data like username, password, certificate, password,authentication

data storage(vol) - if db pod goes down, how to recover and heal from this. so kubernetes uses volumne storage so we can recover db pod in case if it is down or healing.this is a external physical data storage in a node.

Deployment and stateful data -> when application is getting upgraded to new version, there can be downtime, so have a replication 
blueprint for pods is called deployment - we define no of replicas

db can not be replicated as it is data so statefulsets is used to create replica for DB.it is advisable to have DB outside k cluster while applications inside kubernetes.

## Kubernetes COnfiguration

Either it is kubernetes dashboard or CLI or API , they all talk to control plan API server (this is only entrypoint to kubernetes)



