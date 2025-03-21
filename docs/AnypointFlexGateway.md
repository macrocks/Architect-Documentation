# Anypoint Flex Gateway
Anypoint Flex Gateway is an Envoy-based, ultrafast lightweight API gateway designed to manage and secure APIs running anywhere. 
Built to seamlessly integrate with DevOps and CI/CD workflows, Anypoint Flex Gateway delivers the performance required for the most demanding applications and microservices while providing enterprise security and manageability across any environment

**Flex Gateway vs. Mule Gateway**

Flex Gateway can manage and secure APIs, both Mule and non-Mule, running anywhere.

In contrast, Mule Gateway protects a single Mule API. The key advantage is that it’s easy for Mule app developers to provide basic endpoint protection. You can configure Mule in Anypoint Runtime Manager as a CloudHub proxy application, protecting multiple backends.

Building custom policies on Mule Gateway is similar to building an application with Java using the Mule DSL. Building a custom policy in Flex Gateway is based on Envoy-provided Rust WASM SDKs. A Mule Gateway policy cannot be reused in Flex Gateway and vice versa, because the underlying architectures are fundamentally different.

MuleSoft recommends you choose Flex Gateway for high-availability and high-performance Mule and non-Mule applications.

To protect Mule applications that do not require the management and maintenance of underlying infrastructure, choose Mule Gateway for CloudHub.

| Topic            | Felex Gateway                                                                               | Mule Gateway                                                                                              |
|------------------|---------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| Summary          | Envoy-based API gateway to secure all APIs, Mule and non-Mule, running anywhere             | Java-based API gateway embedded into Mule, to secure only Mule APIs                                       |
| Tech Stack       | Underlying engine built upon Envoy                                                          | Java Spring Application embedded into Mule                                                                |
|                  | Leverages FluentBit for logging                                                             |                                                                                                           |
| Use Case         | High performant and high availability                                                       | Secure a Mule API basic endpoint, or enable a dedicated proxy as an embedded library in a Mule instance.  |
|                  | Secure any API anywhere.                                                                    | Also available as a Mule proxy application in CloudHub                                                    |
| Key Capabilities | Small footprint                                                                             | Same technology as Mule integration applications                                                          |
|                  | Multiple deployment patterns and modes, including as a native Kubernetes Ingress controller | API Autodiscovery for Mule applications                                                                   |


## Flex gateway Architecture
It consist of two components
1. Control Plane   -- it is hosted by mulesoft and help in building, applying policies, monitoring api and configuring api.
2. Runtime  --it recieve command sfrom control plan to route and protects backend apis. built with security in mind.

   ![image](https://github.com/user-attachments/assets/fdd3ccc0-1aa4-4f57-bbe9-31efac5c4431)

**Scaling**

A single Flex Gateway can support up to 1,000 backend APIs. For high availability, it is best to deploy multiple gateways running in parallel. Deploying multiple gateways increases gateway performance and robustness, and is recommended for high-performance applications that must scale.

**API Instances**

Flex Gateway supports HTTP and REST API instances.Flex Gateway does not natively support SOAP APIs and does not provide any schema validation for XML. However, you can publish an HTTP API instance to secure any API that uses HTTP protocol.
**Connected Mode** 
**Local Mode**

In simple terms - Anypoint Gateway you can manage mule or non -mule api's deployed anywhere.  Anypoint flex gatway can be installed in connected mode or local mode.
Connected mode will allow you to manage api's policies and alertys and key metrics on Anypoint platform. While on local mode you need to  manage it using configuration file.

you need to configure your api management under flex gateway setup folder - it will have gateway.confg , .pem and .key file along with that add api.yaml file which has api configurationa nd policy details.
customer url ---> url:port 8081 ---> flex gateway port forwarding to contatiner port 8081->9000 ---> containter port ---forward request to api port 9000->9001

For multiple api's to configure against flex gateway -- we need to run onre more container with diferent port and then setup confi gor this api as well on .yaml file.

