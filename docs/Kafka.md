# What is Kafka
Distributed event streamming platform
A streaming platform has three key capabilities:

Publish and subscribe to streams of records, similar to a message queue or enterprise messaging system.
Store streams of records in a fault-tolerant durable way.
Process streams of records as they occur.

started at Linkedin and open sources in 2011 under Apache Foundation

# How it works 
works on pub/sub model
1. message broker
2. publisher
3. subscriber 

Event Structure - > Event Key  ,Event Value, Timestamp, optional:metadata
Event is a records the fact that something happened in business. it can called as record or message. You write or read data from kafak
as a event payload. Its a fire n forgot mechanism.
Topic - events are orgnied and durable stored in topics
you define topics to have collection of events related to orders or products or customers.




reference - https://kafka.apache.org/24/documentation.html
