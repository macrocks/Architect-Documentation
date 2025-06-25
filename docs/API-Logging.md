# **MuleSoft JSON Logger Component**

## **1. Logging in MuleSoft**

Logging is essential in any integration or API implementation to monitor runtime behavior, diagnose issues, trace transactions, and ensure observability. MuleSoft provides built-in logging capabilities using the `Logger` component in Mule applications. These logs can be routed to Anypoint Monitoring, external tools (like Splunk, CloudWatch), or standard log files.

However, the standard logger is limited in formatting and does not enforce a structured output, making log analysis harder in large-scale integrations. To overcome this, MuleSoft provides the **JSON Logger** component.

---

## **2. What is JSON Logger**

The **JSON Logger** is a DataWeave-based reusable component that logs messages in **JSON format**. It provides a consistent, structured, and easily parsable log output, enhancing visibility in logs, especially when integrated with log aggregators or monitoring tools.

### Benefits:

* Outputs logs in key-value JSON format.
* Supports dynamic and custom fields.
* Allows masking sensitive data (e.g., passwords, tokens).
* Enhances searchability in logging tools like Splunk, ELK stack, etc.

---

## **3. How to Use It**

### Step-by-Step Usage:

1. **Import JSON Logger Module**: If you're using MuleSoft Accelerator or reusable assets from Exchange, import the `json-logger` module. Otherwise, create a custom DataWeave script.

2. **Configure Logger Component**:
   Use a `Logger` component in your flow and configure it like this:

   ```xml
   <logger level="INFO" doc:name="JSON Logger">
       <logger-message><![CDATA[%dw 2.0
   output application/json
   ---
   {
     message: "User created successfully",
     flow: flow.name,
     traceId: correlationId,
     userId: payload.userId,
     timestamp: now()
   }]]></logger-message>
   </logger>
   ```

3. **Use a Shared JSON Logger Function**:
   Create a reusable `.dwl` file with a JSON logging function. Call it throughout the project for consistency.

   Example `log-helper.dwl`:

   ```dw
   %dw 2.0
   fun buildJsonLog(message, data) =
   {
     message: message,
     traceId: correlationId,
     timestamp: now(),
     details: data
   }
   ```

   Use it in your Logger:

   ```xml
   <logger level="INFO" doc:name="Structured Logger">
       <logger-message><![CDATA[%dw 2.0
   import * from dw::core::Strings
   import buildJsonLog from dw::log-helper

   output application/json
   ---
   buildJsonLog("User created", {
     userId: payload.userId,
     email: payload.email
   })]]></logger-message>
   </logger>
   ```

---

## **4. How to Mask or Disable Fields in JSON Logger**

To protect sensitive data like passwords, tokens, etc., you can:

### 1. **Mask Specific Fields:**

Update the field values in the DataWeave script to hide actual data:

```dw
{
  userId: payload.userId,
  password: "***MASKED***",
  accessToken: payload.accessToken as String {class: "private"}
}
```

### 2. **Conditional Logging or Disabling Fields:**

Use conditional logic in DataWeave to include/exclude fields.

```dw
---
{
  userId: payload.userId,
  token: if (attributes.environment == "prod") "***MASKED***" else payload.token
}
```

### 3. **Use MuleSoft Accelerator JSON Logger (Optional)**:

If using MuleSoft Accelerators, the JSON Logger comes with `maskFields` and `disableFields` parameters in `jsonLogger:log` operation, which allows masking or disabling fields via config.

Example:

```xml
<jsonLogger:log
   doc:name="Log Request"
   level="INFO"
   category="application"
   message="Logging request"
   maskFields="['password', 'creditCard']"
   disableFields="['headers']" />
```

---

## **5. Examples**

### Example 1: Basic JSON Log

```xml
<logger level="INFO" doc:name="JSON Log">
    <logger-message><![CDATA[%dw 2.0
output application/json
---
{
  event: "Payment Initiated",
  amount: payload.amount,
  transactionId: payload.transactionId,
  timestamp: now()
}]]></logger-message>
</logger>
```

### Example 2: Masked Log Fields

```xml
<logger level="INFO" doc:name="Masked Log">
    <logger-message><![CDATA[%dw 2.0
output application/json
---
{
  event: "Login Attempt",
  username: payload.username,
  password: "***MASKED***"
}]]></logger-message>
</logger>
```

### Example 3: Reusable JSON Logger via Module

```xml
<jsonLogger:log
   doc:name="Log Order Details"
   level="INFO"
   message="Order processed"
   category="order-service"
   payload="#[payload]"
   maskFields="['cardNumber']"
   disableFields="['headers']" />
```

---
### 6. Logging Levels in MuleSoft
Available Logging Levels
MuleSoft follows the standard logging levels defined by SLF4J / Log4j, which are:

Level	Description
TRACE	Most detailed logs (low-level internal operations)
DEBUG	Developer-level diagnostics
INFO	Key business events (e.g., flow started, request received)
WARN	Something unexpected happened, but app can continue
ERROR	Errors that may require immediate attention
FATAL	Severe errors that may cause application to crash (rare in Mule)

How to Set Logging Level
A. Design Time (Static Configuration)
You can set the logging level in the log4j2.xml file located at:
src/main/resources/log4j2.xml

Example:

xml
Copy
Edit
<Loggers>
  <Logger name="org.mule.runtime.core.internal.processor.LoggerMessageProcessor" level="INFO"/>
  <Root level="INFO">
    <AppenderRef ref="Console"/>
  </Root>
</Loggers>
You can also set levels for specific categories or flows by naming the logger.

### 7. Changing Logging Level at Runtime
Changing the logging level without restarting the application is useful in production environments to debug issues dynamically.

Approaches:
A. Using Anypoint Runtime Manager (ARM)
If your app is deployed to CloudHub, you can change the logging level from the Runtime Manager UI:

Go to Anypoint Platform > Runtime Manager.

Select the app and go to Logs > Log Levels.

Click Manage log levels.

Add or update the logger (e.g., com.yourcompany.flows) and set the desired level (DEBUG, INFO, etc.).

Changes take effect immediately without restarting the app.

B. Using REST API (CloudHub or RTF)
MuleSoft provides a Monitoring REST API to change log levels programmatically:

bash
Copy
Edit
curl -X PATCH \
  https://anypoint.mulesoft.com/monitoring/organizations/{orgId}/environments/{envId}/applications/{appId}/log-levels \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "logLevels": [
      {
        "loggerName": "com.mycompany.flows.OrderFlow",
        "level": "DEBUG"
      }
    ]
  }'
-> Requires a valid Anypoint Platform access token and the right organization/environment/application IDs.

C. Runtime Config via External Properties (e.g., log4j2.xml)
While runtime config via externalized properties is possible for toggling logger categories, it typically requires application restart unless handled by external tooling (like JMX or APIs).

### 8. Best Practices for Logging
Use INFO for key events and DEBUG for variable logging during development.

Avoid logging sensitive information (mask or omit).

Use TRACE sparingly—it’s very verbose.

Leverage loggerName categorization to fine-tune log control.

For reusable modules or connectors, provide custom logger names (e.g., com.myapp.dbmodule).
