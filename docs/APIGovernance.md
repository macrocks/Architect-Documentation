API Governance in MuleSoft ensures that APIs are designed, developed, deployed, and managed in a secure, standardized, and scalable way. The key topics under API Governance include:

## API Design Governance
-API Design Best Practices (RESTful, RAML, OAS)  - 
for RAML and Best use cases follow 
https://macrocks.github.io/Architect-Documentation/APIDesigning.html

-API Naming Conventions


-Versioning Strategy
API versioning is crucial for managing changes and ensuring backward compatibility in MuleSoft applications. Here’s a brief overview of an effective API versioning strategy:
```
1. Semantic Versioning
Use semantic versioning to clearly communicate the nature of changes:

Major Versions (X.0.0): Introduce breaking changes that are not backward compatible.
Minor Versions (1.X.0): Add new features in a backward-compatible manner.
Patch Versions (1.0.X): Implement backward-compatible bug fixes1.
2. Deprecation Policy
Establish a clear deprecation policy to manage the lifecycle of API versions:

Announcement: Notify consumers about the deprecation of an API version well in advance.
Support Period: Provide a support period during which both the old and new versions are available.
Sunset: Clearly communicate the sunset date when the deprecated version will no longer be supported2.
3. Documentation and Communication
Maintain comprehensive and up-to-date documentation for each API version:

Changelog: Include a changelog that details the changes in each version.
Migration Guides: Provide guides to help consumers migrate from one version to another3.
4. Versioning in URLs
Incorporate version numbers in the API URLs to differentiate between versions:

Example: /api/v1/customers and /api/v2/customers.
5. Backward Compatibility
Ensure that minor and patch versions are backward compatible to minimize disruptions for API consumers.

6. Testing and Validation
Thoroughly test new API versions to ensure they meet quality standards and do not introduce regressions.
```
By following these strategies, you can effectively manage API versions, minimize disruptions for consumers, and maintain a high level of service quality. 

-Consistency in URI Structures
Follow and be consistent URI structure to make it more readable, concise and easy to understand. 
e.g.  /api/v1/customers
/api/{versio}/{domain}/{resource}  e.g. /api/v2/pay/accounts

## Security & Compliance
-Authentication & Authorization (OAuth 2.0, JWT, Basic Auth)

-API Policies (Rate Limiting, IP Whitelisting, CORS)

Message Logging -
  The Message Logging policy logs custom messages using information from incoming requests, responses from the backend, or 
  information from other policies applied to the same API endpoint.
  
  For Mule Gateway this policy works with both Apache log4j and Apache log4j2.

HTTP Caching , IP Whitelist, IP BlockList, JSON Threat protection, Header Remover, Header Injection,ClientId Enforcement,
Basic Authentication

OAUTH 2.o



JWT Validator
JSON Web Token (JWT) is a URL-secure method of representing claims to be transferred between two parties. The claims in a JWT are encoded as a JSON object that is used as the payload of a JSON Web Signature (JWS), or as a JSON web encryption (JWE) structure in plain text. This enables the claims to be digitally signed and integrity protected with a message authentication code (MAC). Because the token is signed, you can trust the information and its source.

The JWT Validation policy validates the signature of the token and asserts the values of the claims of all incoming requests by using a JWT with JWS format. The policy does not validate JWT that uses JWE.


  
CORS
  CORS (Cross-Origin Resource Sharing) is a security mechanism that allows or restricts web applications running on different origins (domains) from accessing your API resources.
  
  Why is CORS Needed?
  By default, web browsers enforce a same-origin policy, which blocks requests from a different domain, protocol, or port. CORS allows controlled access to your API from different origins by specifying which domains, headers, and methods are allowed.
  
  How CORS Policy Works in MuleSoft?
  MuleSoft provides a CORS Policy that can be applied to APIs through API Manager in Anypoint Platform. This policy:
  
  Defines allowed origins (e.g., https://example.com).
  Specifies allowed HTTP methods (e.g., GET, POST, PUT).
  Controls which headers can be sent in requests.
  Determines whether credentials (cookies, authorization headers) can be included.

below is difference between Spike control and Rate limiting policy(sla based)
```
In MuleSoft, both Spike Control Policy and Rate Limiting Policy are used to control API traffic, but they serve slightly different purposes. Here’s a breakdown of the differences:

Feature	Spike Control Policy	Rate Limiting Policy
Purpose	Prevents sudden traffic spikes that can overwhelm the system.	Limits the number of requests a client can make within a specific time window.
Use Case	Protects APIs from sudden bursts of traffic by allowing only a certain number of requests per unit of time.	Controls API consumption by enforcing a quota over a defined time period.
Behavior	Implements a sliding window approach to gradually allow traffic instead of an abrupt limit.	Enforces a fixed limit on the number of requests allowed per time window (e.g., 1000 requests per hour).
Handling Excess Requests	Excess requests are delayed and processed gradually.	Excess requests are rejected with a 429 Too Many Requests error.
Best For	Handling sudden surges in traffic while maintaining system stability.	Enforcing contractual usage limits on API consumers.
When to Use Which?
Use Spike Control when you want to smooth out bursts of traffic and prevent system overload.
Use Rate Limiting when you want to enforce usage quotas and ensure fair consumption among API users.
```
-Data Privacy & Compliance (GDPR, HIPAA)

-Secure Data Transmission (TLS, Encryption)
1. data at rest
You can use PGP or XML or GCE encryption modules to encrypt data while storeing or at rest. E.g. while sending data to Anypoint MQ you can use below algo /module to encrypt data so no one can data in queue.
During PGP encryption, the sender of the message must encrypt its content using the receiver’s public key. So, whenever you want to encrypt messages in your Mule app using someone else’s public key, you must add the public key to your key ring. When adding a new PGP configuration to your Mule app, you need to provide your key ring file so the encryption module can get the public key from it to encrypt the message.

Use a tool such as GPG Suite to import the other party’s public key. See below for details.

Using the same tool, export the public key, selecting binary as the output format. This produces a key ring file with a .gpg extension.

Ensure that the key ring (.gpg) file is stored where the Mule app can access it during runtime.

Example: PGP Configuration
```
<crypto:pgp-config name="encrypt-conf" publicKeyring="pgp/pubring.gpg">
    <crypto:pgp-key-infos>
        <crypto:pgp-asymmetric-key-info keyId="myself" fingerprint="DE3F10F1B6B7F221"/>
    </crypto:pgp-key-infos>
</crypto:pgp-config>
```
Decrypt
During PGP decryption, the receiver of the message must use its private key to decrypt the contents of a message that was encrypted using a public key. Therefore, the receiver must distribute its public key to those who will use it to send encrypted messages.

Example: PGP Configuration
  ```
<crypto:pgp-config name="decrypt-conf" privateKeyring="pgp/secring.gpg">
    <crypto:pgp-key-infos>
        <crypto:pgp-asymmetric-key-info keyId="myself" fingerprint="DE3F10F1B6B7F221" passphrase="mule1234"/>
    </crypto:pgp-key-infos>
</crypto:pgp-config>
```
https://docs.mulesoft.com/mule-runtime/latest/cryptography-pgp

2. data at transit
Onw Way SSL
In one way SSL, only client validates the server to ensure that it receives data from the intended server. For implementing one-way SSL, server shares its public certificate with the clients. 

![image](https://github.com/user-attachments/assets/c2726c1e-8276-499f-a3e4-2ef37c38e3f3)
1. Client requests for some protected data from the server on HTTPS protocol. This initiates SSL/TLS handshake process. 
2. Server returns its public certificate to the client along with server hello message.
3. Client validates/verifies the received certificate. Client verifies the certificate through certification authority (CA) for CA signed certificates.
4. SSL/TLS client sends the random byte string that enables both the client and the server to compute the secret key to be used for encrypting subsequent message data. The random byte string itself is encrypted with the server’s public key.
5. After agreeing on this secret key, client and server communicate further for actual data transfer by encrypting/decrypting data using this key. 

Client (Truststore)   --->  server (keystore)
create a keystore using keytool
keytool -genkey -alias *.localhost -keyalg RSA -keystore muledemo.pfx -storetype PKCS12 -storepass 123456
it will generate muledemo.pfx  -- import this keystore into server api keystore section and deploy api with HTTPS config
get public certificate out of this keystrore  or insecure option.
keytool -export -alias *.localhost -file muledemo_pubcert.cer -keystore muledemo.pfx
we can conigure this to another api(client) as truststore who will be calling server api which has above keystore in its keystore configuration

http://client-url/api/check  --> it will call server api on https://server-api/api/check


Mutual (Two Way) SSL
Contrary to one-way SSL; in case of two-way SSL, both client and server authenticate each other to ensure that both parties involved in the communication are trusted. Both parties share their public certificates to each other and then verification/validation is performed based on that.
![image](https://github.com/user-attachments/assets/b3f0cd67-4272-4f4b-bd1e-2c2c8b2238cc)
1.Client requests a protected resource over HTTPS protocol and the SSL/TSL handshake process begins.
2 Server returns its public certificate to the client along with server hello. 
3. Client validates/verifies the received certificate. Client verifies the certificate through certification authority (CA) for CA signed certificates.
4. If Server certificate was validated successfully, client will provide its public certificate to the server.
5. Server validates/verifies the received certificate. Server verifies the certificate through certification authority (CA) for CA signed certificates.
6. After completion of handshake process, client and server communicate and transfer data with each other encrypted with the secret keys shared between the two during handshake. 

Client (Truststore, keystore)  -----> server (truststore, keystore)
create a keystore using keytool
keytool -genkey -alias serverkeystore_ks -keyalg RSA -keystore serverkeystore_ks.pfx -storetype PKCS12 -storepass 123456
it will generate muledemo.pfx  -- import this keystore into server api keystore section 
keytool -genkey -alias servertruststore_ts -keyalg RSA -keystore servertruststore_ts.pfx -storetype PKCS12 -storepass 123456
Also generate truststore certificate which needs to add public certificate of client keystore

lets generate client keystore
keytool -genkey -alias clientkeystore_ts -keyalg RSA -keystore clientkeystore_ts.pfx -storetype PKCS12 -storepass 123456
it will generate muledemo.pfx  -- import this keystore into server api keystore section 
lets get public certificate from client keystore now.
get public certificate out of this keystrore  or insecure option.
keytool -export -alias client_public -file mclient_pubcert.cer -keystore clientkeystore_ts.pfx


now import public certificate of client into truststore of server 
keystool -import -trustcacerts -keystore servertruststore_ts.pfx -alias client_pubcer -file /clients/mclient_pubcert.cer

//check all certificate on client truststore
>keytool -list -keystore servertruststore_ts.pfx  it will show 2 entries, server trustrore and client pub cert
>



http://client-url/api/check  --> it will call server api on https://server-api/api/check
Reference - https://tutorialspedia.com/an-overview-of-one-way-ssl-and-two-way-ssl/

4. secure properties or configurations
Secure Configuration Properties
You can encrypt configuration properties as another security level for your applications. To create secure configuration properties, review the following process:

Create a secure configuration properties file.
Define secure properties in the file by enclosing the encrypted values between the sequence ![value].
Configure the file in the project with the Mule Secure Configuration Properties Extension module. The file must point to or include the decryption key.
https://docs.mulesoft.com/mule-runtime/latest/secure-configuration-properties
```
<secure-properties:config key="${runtime.property}"
  file="file1.yaml" name="test">
    <secure-properties:encrypt/>
</secure-properties:config>

<global-property name="prop" value="my-${secure::property.key1}"/>
```

## API Lifecycle Management
-API Discovery & Cataloging

-API Versioning & Deprecation Strategy

-API Retirement & Sunsetting Policies

## API Governance Policies in Anypoint Platform
-Applying API Policies via API Manager

-Custom Policies & Policy Enforcement

-API Rate Limiting, Throttling & SLA Tiers

-API Auto-discovery & Monitoring

## API Quality & Standardization
-API Contract Testing

-Automated API Validation (Schema Validation, Response Codes)

-API Mocking & Simulation

-Error Handling & Standardized Responses

## API Analytics & Monitoring
-Anypoint Monitoring & Alerts

-API Usage Analytics

-Log Management & Observability

## Developer Experience & Documentation
-API Portal & Exchange Best Practices

-Self-service API Documentation

-API SDKs & Client Libraries
