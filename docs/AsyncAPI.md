Anypoint platform does support Event-Driven API specification (Async API) in below two API lifecycle phases on Anypoint platform.

Design (Anypoint Design Center)

Publish (Anypoint Exchange)

Problem Statement
Solution
Implementation
Implementation Steps
Practical Example
Things to Remember
Async API in JSON format
Multiple Messages in same Channel
RAML Datatype for common fragments in Async API
Best Example for Developer
Problem Statement

Current Event-driven api documentation in terms of spec, information is not available on Anypoint exchange and have to depend on confluence and Project experience.

Solution

Anypoint platform does support Async API which is open-source specification for Event-Driven API’s. Using Async API we can implement spec and documentation for Event Based API implementation. Anypoint platform support Async API specification in either JSON or YAML language.

More information on Async API can be found on - AsyncAPI Initiative for event-driven APIs

MuleSoft Async API support and release notes - Event-Driven API (AsyncAPI) Support Release Notes | MuleSoft Documentation

Implementation

Let's define Async API as below. 



asyncapi: 2.0.0
info:
  title: Hello world application
  version: '0.1.0'
channels:
  hello:
    publish:
      message:
        payload:
          type: string
          pattern: '^hello .+$'
 

Let's walk through this.

asyncapi: 2.0.0  → First Line depicts type of document (asyncapi) and version of the document type. (Mulesoft support asycnapi api document 2.0.0 version)

 



info:
  title: Hello world application
  version: '0.1.0'
In Info section which is not mandatory but provides more information about application

title: - this should be your application name e.g., e-ev-poll-cms

version - provide info on versioning of api



channels:
  hello:
channels section will define broker name e.g.  mq-event-sales   This is name of your MQ on which application is publishing messages or subscribed to recieve new messages



    publish:  
      message:
        payload:
          type: string
          pattern: '^hello .+$'
This is the payload of the message that the Hello world application is subscribed to. You can publish the message to the hello channel and the Hello world application will receive it.

message section will define structure of your message to be exchanged. As Async api is also supported by open api spec so all data types are defined in open Api spec all can defined n supported here as well.

another example of payload 



        payload:
          type: object
          properties:
            trade-id:
              type: integer
              minimum: 0
              description: the order id of the message coming
            trade-symbol:
              type: string
              minimum: 0
              Description: ticker symbol of the stock.
Now let’s implement Async API on Anypoint  platform 

Implementation Steps

Open Any point Design Center → click on Create → select New AsyncAPI

Open image-20230613-112623.png

 

provide project name as your API name ands select AsyncAPI(YAML) lang. Make sure here Project Name is same as your actual runtime api name.

Open image-20230613-112756.png

Similar to RAML specification, define api specification sas below and click on publish.

Open image-20230613-113615.png

Make sure you publish in Development State and setup a design review with C4E before going forward for development.

Once asset is published on exchange you can perform below activities to make it more readable, searchable and documented.

Add documentations on home/summary page regarding API functionalitry, source, target system and in which project this api will be involved.

add categories defined in Anypoint Exchange API Categories & Tags - Enterprise Integration Centre of Excellence - Confluence (atlassian.net) confluence page.

add tags to provide more info in short form.

 

Practical Example

p-ev-poll-prepay is an Event Driven API which is subscribed to Anypoint MQ(polling Messages) and after response from system API, it is publishing message on Anypoint MQ for next processing. We will take this example and write Async API for those events. API Integration Diagram.

Open image-20230523-083638-20230620-124807.png

 

Subscribed Queue (Polling Message) 

 



asyncapi: '2.0.0'
info:
  title: p-ev-poll-prepay
  version: '1.0.0'
  description: This service is performing stop payment processing and publishing response on LAP system
channels: 
  q.lap.stoppayments.prepay.in:
    subscribe:
      message:
        payload:
          type: object
          properties:
            specversion:
              type: string
              description: specification version .e.g 1.0.0
            type:
              type: string
            source:
              type: string
            subject:
              type: string
              format: integer
            id:
              type: string
              format: integer
            keyId:
              type: string
            time:
              type: string
            datacontenttype:
              type: string
            dataschema:
              type: string
            remoteApplication:
              type: string
            payloadVersion:
              type: string
          Data:
            type: object
            additionalProperties: false
            properties:
              stopId:
                type: integer
              csrId:
                type: integer
              sourceApplicationId:
                type: string
                format: integer
              academicYear:
                type: integer
              issueDate:
                type: string
                format: date
              stopReasonCode:
                type: string
              stopMaintenanceLoan:
                type: string
              stopMaintenanceGrant:
                type: string
              stopFeeGrant:
                type: string
              stopFeeLoan:
                type: string        
 

 

Publishing Queue (push message)



asyncapi: '2.0.0'
info:
  title: p-ev-poll-prepay
  version: '1.0.0'
  description: This is a service which publishes resposne from payment service to LAP system
channels:
  fq.lap.stoppayments.sfd.in:
    publish:
      message:
        payload:
          type: object
          additionalProperties: false
          properties:
            specversion:
              type: string
            type:
              type: string
            source:
              type: string
            subject:
              type: string
            id:
              type: string
            key:
              type: string
            time:
              type: string
              format: date-time
            datacontenttype:
              type: string
            dataschema:
              type: string
            remoteapplication:
              type: string
            payloadversion:
              type: string
          Data:
            type: object
            additionalProperties: false
            properties:
              stopId:
                type: integer
              csrId:
                type: integer
              sourceApplicationId:
                type: string
                format: integer
              academicYear:
                type: string
                format: integer
              status:
                type: string
 

Exchange Asset sample is present on below link - p-ev-poll-prepay (mulesoft.com)

Things to Remember

As Async API is involved in only two phases of API life cycle on Anypoint platform - below things are not possible

Manage API Instances cannot be assigned to this asset.

API Manager -API cannot be created and assigned to API Instance

Governance through Policies cannot be achieved. as API Manager API is not available.

This Async API cannot be imported on Anypoint Studio to implement similar way as REST API.

We cannot have example for payload message for a channel in Async API.

Async API doesn’t have any impact on Runtime Manger API.  Runtime Manager API are viewed on Visualizer. 

As there is no API Manage API so alert triggering will not possible

 

Async API in JSON format

 in case anyone is interested to create AsyncApi in JSON format.


JSON Format Async API Example



RAML Datatype for common fragments in Async API

There are scenario’s that we have common fragments of datatypes which can be used across different channels or payload. We can take help of RAM datatype and define them to avoid duplicate code/specification.


Cloudevent specification- https://github.com/cloudevents/spec/blob/v1.0.2/cloudevents/spec.md 
