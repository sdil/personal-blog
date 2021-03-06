---
title: "An Introduction to Message Queues With RabbitMQ and Python"
date: 2020-02-21
slug: "intro-rabbitmq"
description: "An Introduction to Message Queues With RabbitMQ and Python"
keywords: ["Python", "Programming", "Message Queue"]
draft: false
tags: ["Python", "Message Queue"]
math: false
toc: true
---

## What Is a Message Queue in the First Place?
Message queues (MQ) are a fundamental concept in programming and software development. In a distributed system, a message queue is the backbone of the system. A message queue allows inter-process communication between services/applications in your system (eg. Service A can talk to Service B).

In MQ terminology, the service that emits the message is called the producer worker, while the service that listens and reacts to messages is called the consumer worker. This is how communication happens between the services.

You can scale up or down the number of producer and consumer workers running depending on their loads. For example, you may have two producers running in two VMs and ten consumers running CPU-intensive tasks across 10 VMs. You can also increase the number of workers during the day and shut down the workers at night (provided that your application traffic is a diurnal pattern).

---

## Imagine a World Without Message Queues

Without a message queue, your system runs synchronously. Even though synchronous programming comes with ease in mind, it’s not really convenient for the end-user experience. Let’s take an example of signing up for an online service. Once you’re done filling up your details and press the “Sign up” button, the system will send you an email and create a database row for you. Let’s say you put a wrong, unreachable email address. The system will retry to send you the activation email for maybe ten seconds. As the new user, you will have to wait for at least ten seconds for the system to finish attempting to send an email before you’re redirected to the next page. Isn’t that a bad user experience? Waiting is a very big deal for a web app!

Here is the ideal scenario is: You press the “Sign up” button, you are redirected into the homepage, and you wait for the activation email to reach your inbox. In that case, you do not have to wait for the system to successfully send you the email. This is because there is a mailer worker that is consuming the MQ message, waiting to send you the email. As a new user, you do not have to wait for a long time (10 seconds is a long time) before being redirected to the next page. Everyone is happy.

---

## Understanding Message Queues With an Analogy

When I first dealt with message queues and had no idea what they were, my ex-team manager, Sian Lerk, used this brilliant analogy to explain the concept to me. Imagine that you’re sending a letter to your mother via the local postal service. Here’s what you’ll do:

1. Write the letter.
2. Go to the nearest mailbox and send the letter.
3. The post officers will sort the letter according to the postal code.
4. The postman will deliver the letter to your mom.
5. The post office confirms that the letter was successfully delivered.

It seems so natural, right? Now let’s see how a message queue (RabbitMQ specifically) works:

1. A publisher worker constructs a message (usually in JSON format).
2. The publisher worker publishes the message to an MQ Exchange.
3. The MQ broker will route the message to a designated queue based on the predefined routing rule.
4. The message is consumed by a consumer worker and processed.
5. The consumer acknowledges through the message queue that the message was successfully delivered.

Typically, the producer workers will publish a JSON message to message broker that looks something like this:

```
{"action": "delete_user", "userId": "john_doe"}
```

The consumer will consume the message and perform actions like:

- Delete the user data in the database
- Remove their assets from S3 object storage
- Dispatch a goodbye email to the user
- Much more…

---

## Introducing RabbitMQ

RabbitMQ is an open-source message broker developed by Pivotal Software that offers what we saw in the previous section. It uses AMQP for communication between the services. You can read more about RabbitMQ on Wikipedia.

There many alternatives to RabbitMQ available on the market. Open-source solutions include ActiveMQ, ZeroMQ, Redis, NATS, and KubeMQ. There are also managed message queue solutions offered by major cloud providers: A managed AWS MQ service as well as proprietary queue solutions like AWS SQS and GCP Pub/Sub.

### When is this useful

The benefits of message queues are as follows:

- The message can be delivered in the correct sequence. This is great for an entity state change. For example, a user mistakenly lists his birthday as July 17. Then the same user updates his birthday July 18. If the message does not arrive in the correct sequence, your application might think the final value is July 17 instead of July 18. This is super important for consistency.
- The data can be persisted if the consumer is unavailable at the moment. This is important so that you do not lose any data and state change during downtime. Let’s say your consumer worker is having downtime and the message is not consumed. The RabbitMQ server will keep the message until it is consumed or it reaches its maximum queue length.
- The system is distributed. Your system has no single point of failure (SPOF) and is able to scale up and down depending on its load. However, it’s super important to keep the RabbitMQ server healthy. Otherwise, you might lose the data and state change.

### Wait, what about Apache Kafka

I personally have little experience with Apache Kafka compared to RabbitMQ. From my knowledge, the most significant differences between Kafka and RabbitMQ are:

- The message in RabbitMQ can be routed based on a routing rule, while the producers and consumers in Kafka publish and subscribe to a topic. There is no message routing concept in Kafka.
- The message in RabbitMQ is guaranteed to be delivered in an ordered sequence, while the message is not guaranteed to be in an ordered sequence in Kafka — particularly because of its data partitioning.
- RabbitMQ is a push-based broker where the RabbitMQ server will push the message to its consumers. Kafka, on the other hand, is a pull-based worker where the consumers will always pull for new messages from the server.

For more information, refer to [this excellent piece](https://medium.com/better-programming/rabbitmq-vs-kafka-1779b5b70c41) written by Eran Stiller.

Nevertheless, both Kafka and RabbitMQ are built to solve different problems. You should be able to distinguish which one suits your use case the best. Pick wisely.

---

## Setting Up RabbitMQ in Docker

Docker is the fastest way to get your hands dirty with new software. Here’s the command to get RabbitMQ 3 with Management UI running and ports 5672 and 15672 exposed:

```shell
$ docker run -d --hostname my-rabbit -p 15672:15672 -p 5672:5672 --name rabbit-server -e RABBITMQ_DEFAULT_USER=user -e RABBITMQ_DEFAULT_PASS=password rabbitmq:3-management
```

Now open in your browser `localhost:15672`. The username is user and the password is password.

### Create Exchange and Queue

Let’s create an exchange:

- Name: my_exchange
- Type: default

Next, let’s create a queue and queue binding:

- Name: my_app
- Type: Classic
- Binding: From: my_exchange, Routing Key: test

Let’s check if our exchange and queue are actually working. We’ll be publishing a message to exchange and check in the queue if the message is there.

---

## Building a Python Producer Worker

### Step 1: Create a Python virtual environment

It’s always recommended to create a new Python environment every time you start working on a new project. I am using Python 3.7.3 throughout this tutorial.

```shell
# Create our working directory
$ mkdir rabbitmq-python
$ cd rabbitmq-python
# Create a virtual environment
$ python3 -m venv .
# Activate virtual environment
$ source bin/activate
# Install python-pika package
$ pip install pika
```

### Step 2: The actual code

Save the following code as producer.py in your directory.

```python
# producer.py
# This script will publish MQ message to my_exchange MQ exchange

import pika

connection = pika.BlockingConnection(pika.ConnectionParameters('localhost', 5672, '/', pika.PlainCredentials('user', 'password')))
channel = connection.channel()

channel.basic_publish(exchange='my_exchange', routing_key='test', body='Test!')

connection.close()
```

The sixth line of the code shows how you establish the connection to the RabbitMQ server. I am using a BlockingConnection adapter here that establishes a synchronous connection to the RabbitMQ server. You may use a SelectConnectionadapter instead as an asynchronous connection, but you need to beware of handling arising asynchronous issues in Python (I’m talking about GIL). Read more about the adapters on GitHub.

The ninth line of the code shows how you can publish a message to the my_exchange RabbitMQ exchange with the test routing key and message body of 'Test!'.

### Step 3: Test your Python script

Run the script and observe the queue.

### Mind-opening words

This little piece of code minimally demonstrates how you can publish a message into a RabbitMQ exchange. In reality, you might use Flask as the API server for your app and publish a message whenever it receives an HTTP request. That’s what happens in reality.

## Building a Python Consumer Worker

Now we’ve made our producer worker. The message will remain in the queue until a consumer worker consumes the queue. This is what our Python code looks like:

```python
# consumer.py
# Consume RabbitMQ queue

import pika
connection = pika.BlockingConnection(pika.ConnectionParameters('localhost', 5672, '/', pika.PlainCredentials("user", "password")))
channel = connection.channel()

def callback(ch, method, properties, body):
    print(f'{body} is received')

channel.basic_consume(queue="my_queue", on_message_callback=callback, auto_ack=True)
channel.start_consuming()
```

On line 8, I defined the callback function that will be invoked whenever the consumer receives a new MQ message from the RabbitMQ queue. This is where you write what operation the consumer should do. In this tutorial, the consumer worker just prints out the message it receives. But in reality, the worker should be doing all the massive operations here (e.g. resizing images, sending signup emails, performing AI/ML operations, encoding videos, starting EC2 instances, etc.). It’s all up to your imagination.

I set the auto_ack=True, which means it will immediately acknowledge the message once it is consumed. Alternatively, you can manually acknowledge the message after the program finishes executing the process related to the message consumed. The RabbitMQ server will dequeue and remove the message once it is acknowledged.

### Let’s test the script

Again, run the script and observe the queue being consumed.

## Well Done!

Congratulations, you’ve built a distributed system using RabbitMQ. I hope that this tutorial has been eye-opening and inspiring. I believe that you can do more things by combining this MQ concept with your existing skills.

### What’s next

If you wish to learn more about message queues and RabbitMQ specifically, I recommend trying different types of exchanges and queues, authentication, running MQ producer and consumer workers in an asynchronous approach, etc.

### Warning: Message Queue Caveats

To be honest, message queues are not an easy system to operate and maintain. Especially when it comes to clustering and failover, they require a lot of attention from your sysadmins. If you are working on a solo project or on a team of devs, I personally recommend using a managed service offered by major cloud providers to ease the pain until you are big enough (in team scale and capacity scale) to handle them in house
