**1. What is amqp?**

AMQP, or the Advanced Message Queuing Protocol, is a vendor-neutral standard for exchanging messages across distributed applications. It facilitates dependable, non-blocking communication between services, which is essential for building systems that can grow and adapt. AMQP offers capabilities such as message queuing, guaranteed delivery, and flexible routing, and is often paired with brokers like RabbitMQ. By leveraging this protocol, you can decouple components so each service can run independently without having to wait for others.


**2. What does it mean? guest:guest@localhost:5672 , what is the first guest, and what is the second guest, and what is localhost:5672 is for?**

This string represents the connection URI for an AMQP broker—commonly RabbitMQ—running on your local machine. Here's the breakdown:

- guest – the username

- guest – the password

- localhost – specifies that the broker is hosted locally

- 5672 – the standard port used by RabbitMQ for AMQP connections

In summary, this URI tells the client to connect to a RabbitMQ instance on localhost:5672 using the default credentials guest/guest.


**3. Simulation slow subscriber**
![image.png](img\image.png)

When I executed the publisher program, it sent sixteen messages to RabbitMQ, which appeared on the “Queued messages” chart under the “Total” line. That initial spike indicated that the messages were temporarily waiting in the queue—either because the consumer hadn’t started yet or because it was slower at processing than the publisher was at sending.

Eventually, the consumer caught up: it acknowledged each message and removed them from the queue, causing the total message count to drop back to zero. This pattern confirms that the messaging pipeline is functioning properly—messages are enqueued when published and dequeued once processed. The height of that initial spike may differ depending on how quickly the consumer starts or how many messages are sent, which can vary between different machines.


**4. Reflection and Running at least three subscribers**
![image1.png](img/image1.png)

When I run only a single subscriber, it processes messages one at a time, which causes the queue to grow until that subscriber can catch up. As a result, the “Queued messages” metric spikes higher and stays elevated for a longer period, since just one consumer is handling all the messages sequentially.

However, once I start additional subscriber instances, RabbitMQ distributes incoming messages in a round-robin manner across all active subscribers. This parallel processing significantly reduces the time it takes to empty the queue, making the spike on the chart both smaller and shorter in duration. This behavior—multiple consumers working from a shared queue—is a core benefit of event-driven architectures. It allows for horizontal scaling and improves system responsiveness under load by simply adding more subscribers.