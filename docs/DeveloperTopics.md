# Streaming
previous to streaming we had file input stream which used to file content into FIS but we can read only so we can't use FIS after first time use.
To overcome this streams come into picture.

Streaming in MuleSoft
Streaming in MuleSoft is a technique used to process large amounts of data efficiently without loading the entire payload into memory. It helps in improving performance and avoiding memory overflow errors when dealing with large files, databases, or API responses.

How Streaming Works in MuleSoft
MuleSoft provides different streaming strategies depending on the source of the data:

1. Repeatable In-Memory Stream
Stores the stream in memory.
Allows re-reading multiple times.
Suitable for small to medium-sized payloads.
2. Repeatable File Store Stream
Stores the stream in a temporary file.
Useful for large payloads where in-memory storage is not feasible.
Provides a balance between performance and memory usage.
3. Non-Repeatable Stream
Processes data as it arrives.
Cannot be read multiple times.
Efficient for one-time read scenarios like logging or forwarding requests.
Where is Streaming Used in MuleSoft?
File Handling – Processing large files without loading them into memory.
Database Querying – Fetching large result sets without overloading memory.
API Calls – Streaming responses from REST or SOAP APIs.
Batch Processing – Handling records efficiently in a batch job.
Example: Enabling Streaming in MuleSoft
When consuming data from APIs or reading files, you can enable streaming using the streamingStrategy attribute.

Example: Enabling Streaming in an HTTP Request
```
<http:request method="GET" url="http://example.com/largefile" streamingStrategy="MULE_STREAMING"/>
```
Example: Reading a Large File with Streaming
```
<file:read path="/data/largefile.csv" streaming="true"/>
```
Best Practices for Using Streaming in MuleSoft
Use Repeatable File Store Stream when dealing with large payloads that need to be read multiple times.
Use Non-Repeatable Stream for one-time processing to optimize performance.
Be cautious with In-Memory Streaming as it can lead to OutOfMemory errors with very large payloads.
Configure streamingStrategy explicitly to ensure efficient processing.


In Mule 3- file operation happens over FileInputStream which is anon -repeatable stream so we cannot perform any operation on read data more than once. while 4 introduced non-repetable stream in which. file read is part of FIS which is wrapped by Stream Object along with buffer object for which we define properties as initial buffer size,incremental size, max size

2 type of non-repeatable streams- 1. file based  2. in memory 


#Fail Fast vs. Its Opposite (Fail Safe)
1. Fail Fast
Concept: Detect failures as early as possible and stop further execution immediately.
Goal: Prevent unnecessary processing and resource wastage by halting as soon as an error is detected.
Example in MuleSoft:
Input Validation: Checking request payload before processing (e.g., rejecting invalid data upfront).
API Gateway Security: Denying unauthorized requests before they reach the backend.
Database Constraints: If a required field is missing, the request fails before attempting an insert.
2. Fail Safe (Opposite of Fail Fast)
Concept: The system continues to operate even in the presence of failures, often by implementing fallback mechanisms.
Goal: Ensure system resilience and graceful degradation instead of stopping execution.
Example in MuleSoft:
Retry Mechanism: Using the Retry Scope to attempt an operation multiple times before failing.
Circuit Breaker Pattern: Temporarily blocking a failing service and using a default response.
On Error Continue: Handling errors gracefully without stopping the whole flow.

Comparison Table
Feature	Fail Fast	Fail Safe

Behavior	Stops execution immediately on failure	Tries to recover and continue execution

Goal	Avoid wasting resources	Ensure system resilience

Example in MuleSoft	Validation, Authentication Failure, On Error Propagate	Retry Scope, Circuit Breaker, On Error Continue

# Salesforce Integration patterns
1. Request -reply pattern  (REST/SOAP Endpoint calls) 
2. Fire n Forget (Asynchronous/Event Based) Platform event based(platformevents)
3. Batch Data synchronization (change data capture batch processing)
4. Remote Call in (mulesoft will be calling insert/upsert/delete on objects) soap rest bulk api options

# **Integration Patterns in MuleSoft**  
MuleSoft follows well-defined **Enterprise Integration Patterns (EIPs)** to enable smooth communication between systems, applications, and services. These patterns help solve common integration challenges such as data transformation, routing, and messaging.  

---

## **Key Integration Patterns in MuleSoft**
Here are some of the most commonly used integration patterns:

### **1️⃣ API-Led Connectivity** *(MuleSoft-Specific)*
**Concept:**  
- Organizes APIs into three layers:  
  - **Experience Layer** – Provides APIs for web, mobile, or third-party applications.  
  - **Process Layer** – Orchestrates multiple APIs to create business processes.  
  - **System Layer** – Connects directly to backend systems like databases, SAP, or legacy applications.  

✅ **Example:** A banking app where the Experience API calls a Process API, which then retrieves customer data from a System API connected to the database.  

---

### **2️⃣ Request-Reply Pattern**
**Concept:**  
- A synchronous pattern where a request is sent, and a response is expected.  
- Used in real-time scenarios where the client needs an immediate response.  

✅ **Example:** A customer places an order in an e-commerce system, and MuleSoft retrieves available stock in real time before confirming the order.  

🔹 **MuleSoft Implementation:**  
```xml
<http:listener config-ref="HTTP_Listener_config" path="/checkStock" method="GET"/>
<flow name="CheckStock">
    <http:request method="GET" url="https://inventory.api.com/stock" />
</flow>
```

---

### **3️⃣ Event-Driven (Pub-Sub) Pattern**
**Concept:**  
- **Publisher (Producer)** sends events to a messaging system.  
- **Subscribers (Consumers)** listen and process events asynchronously.  
- Ensures loose coupling between systems.  

✅ **Example:** A **payment service** publishes a "Payment Processed" event, and multiple consumers (billing, notifications, and analytics services) listen for it.  

🔹 **MuleSoft Implementation Using Anypoint MQ:**  
```xml
<flow name="PublishEvent">
    <mq:publish destination="payments.queue" message="#[payload]" />
</flow>

<flow name="ConsumeEvent">
    <mq:listener destination="payments.queue"/>
</flow>
```

---

### **4️⃣ Scatter-Gather Pattern**
**Concept:**  
- Sends a request to multiple targets in **parallel** and aggregates the responses.  
- Used for calling multiple APIs or services simultaneously.  

✅ **Example:** A travel booking system calls multiple hotel, flight, and car rental APIs and returns the combined response.  

🔹 **MuleSoft Implementation:**  
```xml
<scatter-gather>
    <http:request url="https://flights.api.com/search"/>
    <http:request url="https://hotels.api.com/search"/>
    <http:request url="https://rentals.api.com/search"/>
</scatter-gather>
```

---

### **5️⃣ Message Routing (Choice & Content-Based Routing)**
**Concept:**  
- Routes messages to different destinations based on conditions.  
- Ensures data is sent to the correct system based on its content.  

✅ **Example:**  
- If a payment is over **$10,000**, route to the **Fraud Detection System**.  
- If under **$10,000**, route to the **Standard Processing System**.  

🔹 **MuleSoft Implementation:**  
```xml
<choice>
    <when expression="#[payload.amount > 10000]">
        <flow-ref name="FraudCheckFlow"/>
    </when>
    <otherwise>
        <flow-ref name="StandardPaymentFlow"/>
    </otherwise>
</choice>
```

---

### **6️⃣ Batch Processing Pattern**
**Concept:**  
- Processes **large volumes of data** in chunks to improve efficiency.  
- Used for handling **bulk data processing** (e.g., database migrations, ETL jobs).  

✅ **Example:** A company processes **millions of customer transactions** from a CSV file and inserts them into a database.  

🔹 **MuleSoft Implementation:**  
```xml
<batch:job>
    <batch:input>
        <file:read path="/transactions.csv"/>
    </batch:input>
    <batch:process-record>
        <db:insert config-ref="DB_Config"/>
    </batch:process-record>
</batch:job>
```

---

### **7️⃣ Webhooks (Event-Driven API Pattern)**
**Concept:**  
- MuleSoft listens for **external system events** and triggers a flow.  
- Used for **real-time updates** without polling.  

✅ **Example:** A **CRM system** sends a webhook when a new lead is created, triggering a MuleSoft flow to update the marketing database.  

🔹 **MuleSoft Implementation:**  
```xml
<http:listener config-ref="WebhookListener" path="/new-lead" method="POST"/>
<flow name="LeadProcessing">
    <db:insert config-ref="CRM_DB"/>
</flow>
```

---

## **Choosing the Right Pattern**
| **Pattern**            | **Best For** |
|------------------------|-------------|
| API-Led Connectivity   | Modular, reusable APIs |
| Request-Reply         | Real-time API calls |
| Pub-Sub (Event-Driven) | Asynchronous messaging |
| Scatter-Gather        | Parallel processing |
| Content-Based Routing | Conditional message delivery |
| Batch Processing      | Large data processing |
| Webhooks             | Real-time event triggers |

---

### **Final Thoughts**
MuleSoft's integration patterns provide **scalability, reliability, and efficiency** for connecting diverse systems. Choosing the right pattern depends on **business needs, data volume, and performance requirements**.

