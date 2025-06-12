+++
date = '2025-06-03T22:06:43+05:30'
title = 'Transactions Aren’t Just for Databases — Kafka Does Them Too'
+++

We have a use case where a consumer reads a message, processes it, and sends the result to another topic. If something
goes wrong during this process, we need to roll back the operation.

Yes, with Kafka transactions, everything is handled in a single atomic operation.

![kafka.drawio.svg](/kafka.drawio.svg)

### Real world scenario

When a ride ends, the system must:

1. Generate a payment charge
2. Update trip status to “completed”
3. Push notification to the user

Using **Kafka transactions**, all related events (on different topics) can be committed atomically. If Kafka crashes or
the app fails mid-way, either all these records are visible to consumers or none are — ensuring exactly-once and
consistent updates.

#### 1. Create a producer

```javascript
const producer = client.producer({
    transactionalId: `ride-transaction`,
    maxInFlightRequests: 1, // Default value is 5
    idempotent: true
})
```

Why and what is **idempotent?**<br>

- If <code>enable.idempotence</code> set to <code>true</code> then the producer ensures that the message is delivered only once(Exactly once semantics), eventhough there are network issues | broker failure

- The producer config <code>transactional.id</code> is a unique identifier for the producer instance and it allows kafka to track the transactions of the prodcuer

- As the name suggests, it is the number of requests that can be made over a single connection. when <code>max.in.flight.requests.per.connection</code> is set to less than 5, say 2(Default is 5), it means producer can send 2 requests in a single connection without acknowledgement. 

#### Create a transaction

```javascript
const transaction = await producer.transaction()
```

#### Emit respective events

```javascript
await transaction.send({
  topic: "trip-status-topic",
  messages: [{ value: "<STATUS>" }]
})
await transaction.send({
  topic: "payment-processing-topic",
  messages: [{ value: "<AMOUNT>" }]
})
await transaction.send({
  topic: "alert-notification-topic",
  messages: [{ value: "<ALERT-MESSAGE>" }]
})
```

#### Commit the created transaction

```javascript
await transaction.commit()
```

All events are atomically published. Consumers using read_committed will receive only fully committed transactions.

If Kafka crashes or something goes wrong, abort the transaction

```javascript
 await transaction.abort()
```