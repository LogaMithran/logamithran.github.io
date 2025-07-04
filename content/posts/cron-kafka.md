+++
date = '2025-06-15T13:10:56+05:30'
title = 'Real-Time vs Scheduled: Kafka Connect and Cron in Data Workflows'
+++

Polling vs Real-Time data sync

Syncing and processing data is crucial in real time. Running concurrent crons in place may solve the issue, but is it really enough when you have millions of records to handle?

### Use case
Some of the use cases where kafka connect can be useful
1. Syncing inventory in real time 
2. Event-driven architecture
3. Syncing data from datasource A to datasource B 

![cron-diagram](/cdc-block-diagram-Page.svg)

### When to choose CDC

<strong> CDC - Change data capture</strong>, a process where all the transactional events like create, update, delete will be captured and emitted to a message broker.

Choose CDC when you need
- Low Latency
- Scalablity
- Efficient resource usage

### But Why not cron?
Cron can be used 
- Low priority tasks
- Periodic batch jobs or backups
- When data size is small

### Flow
1. Whenever data is inserted or updated, a transactional log is created by MySQL in the form of a binlog.
2. CDC tools like Debezium read these events and publish them to Kafka topics.
3. Applications or consumers can then read these events and process them as needed.

Up next!!
Let's set up CDC tool(Debezium) for a simple data sync from a Mysql Table A -> B