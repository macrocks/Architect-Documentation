## What is RAML?

RAML stands for Restful API Modelling language
RAML is a powerful language as a contract for APIs
RAML is a YAML based language for describing RESTful APIs
Highly readable by both humans and Computers
It focuses on cleanly describing resources,method,parameters,responses,media types

## RAML API Specifications in Mulesoft
Simple and Structured way of representing APIs
API structure is easily understood by everyone,i.e developers,partners and other API Consumers
RAML Files can be used to auto generate documentation,mocked endpoints, interface for API implementations
RAML is based on YAML and json

E.g. 
```
/books:  --resource
  get:  --http method
    queryParameters:  --queryparameter
      author:
          displayName: Author
          type: string
          description: An author's full name
          example: Mary Roach
          required: false
      responses:   --this section is to define responses expected as part of this resource
        200:  --http response status code
          body:  
            application/json:  -body type
              example: |   -example
                {
                  "data": {
                    "id": "SbBGk",
                    "author": "Mary Roach",
                    "link": "http://e-bookmobile.com/books/Stiff"
                  },
                  "success": true,
                  "status": 200
                }
  /{bookId}:  -paramterised subresource
    post:
      body:
        application/json:  //body type can json, xml, text or multipart
          type: |
            {
              "type": "object",
              "$schema": "http://json-schema.org/draft-03/schema",
              "id": "http://jsonschema.net",
              "required": true,
              "properties": {
                "songTitle": {
                  "type": "string",
                  "required": true,
                  "minLength": 36,
                  "maxLength": 36
                }
              }
            }
          example: |
            {
              "songTitle": "Get Lucky"
            }
 ```     

## what is RAML Fragment
API fragments are reusable component of RAML to make the design and build of a reusable API even quicker and easier.
Another advantage of building an API spec out of reusable API fragments is that consistency of definitions reduces the effort of implementing APIs.

Fragment Type can DataType, Example, Trait, ResourceTypes, Library, User documentation, Annotation Type
## Security Scheme
This is one the RAML fragement which RAML support differenct built in security scheme defiantion in RAML API defination.
Security Scheme supports OAuth 1.0, OAuth 2.0, Basic Authentication, Digest Authentication, Pass Through, x-{other} Security Scheme types.
We will create ClientId Enforcement Security Scheme as follows- 
create new api fragment
Type- security Scheme
```
#%RAML 1.0 SecurityScheme
type: x-clientId-enforcement
description: clientId enforcement security fragement for all API's
describedBy:
  headers:
    client_id:
      description: client id provided during access on api process.
      type: string
      required: true
      example: dsjdd21jns9342knds943
    client_secret:
      description: client_secret provided during access on api process.
      type: string
      example: fsh328ds832lnkds094dl
      required: true
  responses:
    401:
      body:
        application/json:
          properties:
            error:
              type: string
              description: A descruption of error
              example: Invalid client credentials.
          example: 
            {
              "error": "Invalid Client Credentials."
            }
```

### DataTypes
You can define custom data type which can be reused across multiple api's and endpoints.

```
#%RAML 1.0 DataType
type: object
description: reusable data type defined for products resource
displayName: proucts data type
properties:
  name:
    type: string
    example: "Dettol Cleaner"
    required: true
  sin:
    type: string
    example: "1234-ds43-ds"
    required: true
    description: product serial no.
  weight:
    type: number
    example: 100
    required: true
  weighScale:
    type: string
    example: "gram"
    required: true

```

How to use this into RAML - as below 
```
types:
  productDataType: !include /exchange_modules/de970fd2-54aa-4845-a9d6-c26eee37ca0d/products-datatype/1.0.0/products-datatype.raml

/products:
  get:
    is: [common-header-fragment,common-response-fragment]
    description: get products details
    responses:
      200:
        body:
          application/json:
            type: productDataType
```

### Traits 
Traits are the smallest reusable component that can define common characteristics across resource methods and resources.They can be a part of the resource (query or URI parameters).
You can define traits for common headers, common query parameters, responses. 
A Trait, like a method, can provide method-level nodes such as description, headers, query string parameters, and responses. Methods that use one or more Traits inherit nodes of those Traits. Usage of Traits is optional but is highly encouraged since it simplifies the API design and improves readability. Traits should be created as separate files and included at the start of the API RAML file.
example of trait can be response or common header as below :
```
#%RAML 1.0 Trait

headers:
  x-correlation-Id:
    type: string
    description: unique transaction id/request id to identify request/trasnsaction
    example: 3idso342kndso423kdnsln
    required: true
  x-source-system:
    type: string
    description: system name from request has originated
    example: peg_oracle
```

Add into main raml file as below
```
traits:
  common-header-fragment: !include /exchange_modules/de970fd2-54aa-4845-a9d6-c26eee37ca0d/hascommonheader/1.0.0/hascommonheader.raml
  common-response-fragment: !include /exchange_modules/de970fd2-54aa-4845-a9d6-c26eee37ca0d/responses-fragments/1.0.0/responses-fragments.raml

types:
  productDataType: !include /exchange_modules/de970fd2-54aa-4845-a9d6-c26eee37ca0d/products-datatype/1.0.0/products-datatype.raml

/products:
  get:
    is: [common-header-fragment,common-response-fragment]
    description: get products details
    responses:
      200:
        body:
          application/json:
            type: productDataType
```
### Examples
Example API fragment is reusable asset to define example for given resource request or response parameters. in below example we will define
productResponse example which will be referred on main raml
you can define multiple examples in a single api fragment example.
```
#%RAML 1.0 NamedExample
productsExample:
    "name": "Lever Clutch Box"
    "sin": "skhsk29sks"
    "weight": 100
    "weighScale": "KG"

productResponses:
    name: "Ionios Box"
    sin: "dskj32klds"
    weight: 203
    weighScale: "gm"
```
you can use as an example for a product response body as below
```
/products:
  get:
    is: [common-header-fragment,common-response-fragment]
    description: get products details
    responses:
      200:
        body:
          application/json:
            type: productDataType
            examples: !include /exchange_modules/de970fd2-54aa-4845-a9d6-c26eee37ca0d/productsresponse/1.0.3/productsExample.raml
            //this will show both product response json objects are example (use case - if any element is null or optional, conditional response comes from endpoint)
```

examples define as map which has key :value while example is a single entity defines. example and examples are exclusive so cannot used together on same endpoint.

### Documentation
API fragment documentation is useful component which talks more about api and its fucntionality, e.g. from where is getting pulled, is there any rate limiting stratergy on this api.  Contact detials can be added on documentation for support.
```
#%RAML 1.0 DocumentationItem
title: "API Overview"
content: |
  ## Introduction
  This API provides access to customer data, allowing retrieval, creation, and updates.
  
  ## Authentication
  - The API uses OAuth 2.0 for authentication.
  - Include a Bearer token in the `Authorization` header.

  ## Rate Limits
  - Each API key is limited to 1000 requests per hour.

  ## Contact Support
  If you need assistance, please contact [support@example.com](mailto:support@example.com).
```
how to use on raml 
```
#%RAML 1.0
title: Customer API
version: v1
documentation:
  - !include docs/api-overview.raml
```

### 
