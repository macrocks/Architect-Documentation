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
2. publisher (producers) - produce events on a topic
3. subscriber (consumers)  -consume events from topics

### Event Structure 
- > Event Key  ,Event Value, Timestamp, optional:metadata
Event is a records the fact that something happened in business. it can called as record or message. You write or read data from kafak
as a event payload. Its a fire n forgot mechanism.
![image](https://github.com/user-attachments/assets/a6a05a52-a635-481f-9cc7-bd6f19a89e05)

### Topic - events are orgnied and durable stored in topics. Topic groups similar type of events
you define topics to have collection of events related to orders or products or customers.
Use Case - real time monitoring (e.g. Uber app -track vehicle movement) 
website activity tracking, personalized recommendation, fraud detection.

## Real Time Processing (Stream )
-> think of stream as continous real time key value pair flow of records
->you don't need to request new event, you just keep on reciveing them.
-> kafka provides high level functionas and transformations on these stream to produce analytics report or data using avg, count, sum or joins on events

having topic per producer and consumer will make kafka slow or limit on scalability. to avoid and improvde performance Kafka introduces PARTITIONS
## partitions
you decide partitions of your schema design for a given topic. e.g. for Orders topic you can provide partitions based on continent or country 
Even after partition we have solved performance issue as kafka topic which can scale now. what about consumer as per below dia. if single consumer is getting events for different partition at the same time.
![image](https://github.com/user-attachments/assets/1ae10609-a0d1-4efb-9e64-a5245ef92632)

## Consumer Groups
Kafka introduces CONsumer Groups - scalable message consumers
consumer wll have groupId and we add replicas of consumer. Kafka distribute load balance by sending events to replicas of consumer group.

## kafka brokers







reference - https://kafka.apache.org/24/documentation.html
