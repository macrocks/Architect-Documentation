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

