# What is Cloud hub 2.0
Apli Deploymying and running in a containerized environment based on kubernetes. It is ia fully managed,containerized service for deploying and running mulesoft applicationn

easily deploy n run application in a resilitent and scalable env.
contained based isolation and not vm level isolation.
Application can deployed in prviate and shared space 
Private-
shared space- deployeing in multitenant and shared space. data is not shared but infrastructure is shared.

Cloudhub 2.0 available in 12 regions globally
can deploy application on multiple replicas 
more granual vCore options.Maz vCore allocation per replica is 4 vCore
can be deployed on clustered mode. min. 2 replicas for clustering.
support for rolling update n recreate functionality.
easy/dynamic scalable.horizonta- no of replicas and vertical scaling is increase no of vCore size
support intelligent healing and zero downitme updates
stores upto 200mb log data per config upto 30 days

#Cloudhub 2.0 Architecture
## private sapce-
support vanity domain
support for TLS contenxt
allow to connect priavre or corporate datacenter and cloud using vpn and trasit gateway
last mile security and ssl forward supported

##shared space - No vanity domain supported.
no customer certifiats supported
multitenant space
last mile security and ssll forwarding supported

Features -> application isolation/fully managed , intelligent healing/zero downtime / mTLS supported Last mile secuirtry. available in 12 regions, dynamic scalabilty and HA,
Inbound/outbound firewall rule configuration conatiner based deployment , certificate management, fault tolerance, more granula vCore options


# Shared Space
When you want to run your application in multitenant environment. on shared space in each region is provided by Mulesoft
## When to Use
-no custome certificates requirement
-no vanity custom domain needed.
- application on shared space cannot aaccess on prem data centre or private or public cloud 
-no requirement to run application in isolation from public cloud
## when not to use shared space
- you want to run application in isolation or single tenant env
-req of vanity domain
-req of custom certificates
- you need to connect resorces on on prem or on public or private cloud

Architecture
appln url -> http://myapp-unique-id.shard.usa-w2.cloudhub.io
![image](https://github.com/user-attachments/assets/be6fadb3-a28b-4683-b45d-e4488b224282)


# Private space
When you want to run application in single tenant or isolation. it is virtual prviate cloud in cloudhub . you can create mulitple pracates space in single or multiple regions
## when to use 
 -connect with resource to on prem data centre or public or private cloud. you can use AnypoitVPN or transit gateway.
- no csupport for AWS direct connect or VPC perring in cloudhub 2.0 
-config custom certifgicates
-custom vanity domain
-configur inbound n outbound firewall rule

Architecture
apply rule- >  https://myapp.api.example.com   --> VPN --> VPN IPSec Tunnel --? on prem datacentre
                                               --> Transit Gatweay  -> Tsansit gteway AWS VPC -> cloud database
Features nOt supported -
-Anypoint Datagraph
- VPC peering and AWS Direct connect
-persistent VM queue
-TLS 1.0
-Built in and custom notification(cloudhub connector)
-URL Reqriting

#Why Cloudhub 2.0 
-application is deployed on kuberneter containered env. so lightweight conatiner, wasy to scaleup n scale down
- no need of managing DLB as it comes with inbuilt ingress load balancer with auto scaling capability
- option to configure inbound an doutbound firewall rules
-provide in built secuirt policies that secured the services and sensive data with encrypted secrets


----------------------------------------------------------------------------------------------------------------------------------------
# Deployment on Cloudhub 2.0
Option 
1. shared space

Below is for shared space
choose app name -> select jar file -> selct runtime configuration 
runtime version 4.4.0  - 4.3.0 is also supported
replica size (vCores) 0.1vCore 0.2 0.5 1, 1.5, 2, 2.5 ,3, 3.5, 4vcore options
replicas (no ) 2  -we can put into clustering if moore than 1

deployment model- rolling update, recreate 

Ingress -> public endpoint
TLS setting -> last mile security (forwad ssl -is diabled)

#What is last mile security->
Get one applicatio with https 8081 -> custom certificate is added on listener  if last mile security is enabled then you cant access because ingress be defualt send request on http listnener
to make it to https listnere we need to enable last mile security enable

Runtime fabric or ingress allows only 8081 port for http or https  -- so we need last mile security to send requst on https listnere

 http://myapp-unique-id.shard.usa-w2.cloudhub.io:8080  -> ingress  -> go to HTTP 8081 listener
 https://myapp-unique-id.shard.usa-w2.cloudhub.io:443 -> ingress (last mile security disable )  > go to http 8081 listner   will not work
                                                      -> ingress (last mile security enable )  > go to https 8081 listner   will work

#Deployment Model on Cloudhub 2.0
1. rolling update  ->zero downtime   -> when you deploying new version of application, no need of downtime.
requests are handled by existing model till new model comes live and can accept request for processing. so zero downtime.(Default)

2. recreate
-application will be unavailable during update and request won't be processed. needed in a situation where existing application is not working as expected and do not want new requests to go to old applications.

#Scaling
1.Horizontal  -> adding more replicas, workers, servers
2.Vertical -> increasing RAM or cpu power, memory  ->performing in memory processing , file or database rows transformation or calculation in memory


#Mule Maven Plugin
Get connected apps first. allow runtime manager and exchange and design centere access
min mule maven version needed is 3.7.0 mule version 4.4.0 
Plugin details- https://docs.mulesoft.com/mule-runtime/latest/deploy-to-cloudhub-2
check blog - https://www.practicewithme.co.uk/post/cloudhub-2-0-deployment-using-mule-maven-plugin
first publish it to exchange - excahngeurl?type=all&show=all

2. private space
deploy to private space
Netwrok -- create private network  -give CIDR range  (how many applicationa re you going to deploy) you get private dns, inbound sttia i, outbound stati ip config and public DNS default value

Domains & TLS
- > upload PEM file

## Firewall rules- 
Inbound Traffic  -
Outbound traffic

## Static IPs- >
all traffic goes from application to ingress load balancer and then go out of network. so statis ip is assigned on ingress level
limit = 2x of production vcores

Advance - >direct HTTP request -> redirect  to https, accept http, drop http
response timeout 300sec
ingress load balancer logs
enable aws service role -enable

create connection- setup transit gateway or VPN

remove public url to restrict access from public url-- you cab keep private url or remove it and app to app connection on private dns still works

## Difference Between Cloudhub 1.0 and Cloudhub 2.0
Topics	                Cloudhub 1.0	             Cloudhub 2.0
Isolation	            VPC(Virtual Private Cloud)	Private Space(EKS Cluster)
Worker               	EC2 Server Instance(VM)	   Replica(Container instance)
Load Balancer        	Dedicated Load Balancer	   Ingress Load Balancer
Shared/Public cloud  	Public Worker Cloud	       Shared Space(Multitenant)
Scaling/Proving      	Supported	                  Supported
URL Rewriting	        Supported(DLB)	             Supported(APP)
Load Balancer Lods    	Not Supported	             Supported
Multiple Custome endpoints	Not Supported         	Supported
Multple truststores	   Not Supported	             Supported
Direct Connect/VPC Peering	Supported	              Not Supported
VPC/VON.Transit Gateway	Supported	                Supported(Private space)
Outbound firewall rules	Not Supported            	Supported
Log Forwarding	          Supported                	Supported
Static IP              	supported(APP)           	Supported(Ingress Balancer)-small set
Persistent Queue	        Supported	               Not Supported(use Object store v2)
Runtime support         	3.6.x onwords            	From 4.3/4.4.x onwords
Datagraph	              Supported                 	Not Supported
API Proxy	              Supported                 	Not Supported
Flex Gateway	           Supported                 	Not Supported
