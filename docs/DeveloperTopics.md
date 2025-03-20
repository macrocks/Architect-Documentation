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


