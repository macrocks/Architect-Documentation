## JSON Logger
Drop-in replacement for default Mule Logger that outputs a JSON structure based on a predefined JSON schema.
Better Approach towards logging using JSON Logger component.

**Features**
1. Content field data parsing: To parse data in content fields as part of the output JSON structure (instead of the current “stringify” behavior).
2. Dataweave functions: To accommodate desired log formatting.
3. External destinations: To send logs to external systems.
4. Data masking: To obfuscate sensitive content.
5. Scope Logger: For in-scope calculation of elapsed times

JSON Logger - Configurations
1. Message - you can provide any message with payload, variable or mulemessage, attributes etc.
2. Content - you can log Payload in JSON format, if not a json then it will log as string message. you can put conditions while logging content
3. Trace Point-  Values - START, END< AFTER_REQUEST, BEFORE_REQUEST, BEFORE_TRANSOFRM, AFTER_TRANSFORM, EXCEPTION, FLOW
4. Priority - INFO, DEBUG, WARN, ERROR
5. category - COM.DEMO  OR EXCEPTION.DATA
6. iN CONFIRGURATION YOU CAN SET APPLICATION NAME, VERSION,  ENVIRONMENT NAME, DISABLE FIELD, MASK FIELDS, PREETY OUTPUT, LOG LOCATION, LOG OUTPUT IN JSON


1. **Content Masking** -
IN A CONFIGURATION YOU HAVE OPTION CONTENT FIELD DATA MASKING- PASS FIELD NAMES AS COMMA SEPERATED.
AS name - top level fields , $.payload.age  - inside list/array and $..age, $..salary - any child element of this name

2. **Destinations** -
you can send logs to active mq, anypoint mq, jms using destination field configuration

3.  **Disable Field**
If somehow we are able to point our development team to the right direction and have them log valuable non-sensitive data (a.k.a. PII or PCI data) in the message field,
and any raw troubleshooting data into the content field, then we can leverage the “disabled fields” feature to filter certain log fields from being printed (e.g. content):
![image](https://github.com/user-attachments/assets/2bf19485-fc63-4bd3-bc19-35e54f3babc7)
The above example will effectively tell the JSON logger that it needs to skip any field defined here (you can add many comma-separated fields, e.g. content, message, etc.). Taking this a step further, we could assign an environment variable (e.g. ${logger.disabled.fields}), which in lower environments could be null, but then on stage and production could be set to “content.”
4.  **Scope logger**
How many of you have done performance testing and while looking at the logs started to think: “I can do 2317ms, take away 1383ms and figure out how long the API request took based on the elapsed times… but is that the only way?” Well, for those scenarios where we are particularly interested in measuring the performance of an outbound call,
 the Scope Logger introduces the notion of an “scope elapsed time” (scopeElapsed) which is a calculation specific to whatever was executed inside the scope (e.g. calling an external API).

5,Category : -

Installation Blog- https://blogs.mulesoft.com/dev-guides/how-to-tutorials/json-logging-mule-4/

Features Blog- https://blogs.mulesoft.com/dev-guides/json-logging-in-mule-4/



Reference - https://github.com/mulesoft-consulting/json-logger
