## ActiveMQ:

Apache ActiveMQ is an open-source **message broker** written in Java that enables **asynchronous communication** between distributed applications or components using **message queues**. It implements the **Java Message Service (JMS)** API and supports multiple messaging protocols, making it a powerful and flexible tool for enterprise-level message-driven architectures.



### Core Concept:
ActiveMQ acts as a **middleman** between producers and consumers:

- **Producer** – Sends (publishes) messages to a queue or topic.
- **Consumer** – Receives (subscribes to) messages from a queue or topic.
- **Broker** – The ActiveMQ server that routes and stores messages.


### Key Features:

| Feature                     | Description                                                                   |
| --------------------------- | ----------------------------------------------------------------------------- |
| **JMS Support**             | Fully compliant with JMS 1.1 and 2.0 standards.                               |
| **Multi-protocol**          | Supports OpenWire, AMQP, MQTT, STOMP, and WebSocket.                          |
| **Persistence**             | Messages can be stored on disk or in a database for reliability.              |
| **High Availability**       | Supports clustering, master-slave, and shared storage configurations.         |
| **Security**                | Provides SSL/TLS, JAAS authentication, and role-based authorization.          |
| **Management & Monitoring** | Comes with a web console and JMX support for broker health and queue metrics. |
| **Integration**             | Works with Java, Spring, Camel, .NET, Node.js, and more.                      |



### Architecture Overview:

```
Producer  -->  [ActiveMQ Broker]  -->  Consumer
```


ActiveMQ supports two main messaging models:

1. **Point-to-Point (Queue-based)**:
    - One producer → One consumer (or one message delivered to only one consumer).
    - Ideal for task distribution.
    - Example: A print job queue.

2. **Publish/Subscribe (Topic-based)**:
    - One producer → Multiple consumers (broadcast).
    - All active subscribers receive a copy.
    - Example: Sending stock price updates to multiple dashboards.






### Persistence and Storage

ActiveMQ can use:
- KahaDB (default) – File-based, fast local persistence.
- JDBC – Store messages in a database.
- LevelDB / AMQ Store – Other optional storage backends.





### ActiveMQ Variants:
There are two main versions: (For new deployments, **ActiveMQ Artemis** is recommended)

| Version                    | Description                                                                                                                   |
| -------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| **ActiveMQ Classic (5.x)** | Stable, widely used version supporting JMS 1.1.                                                                               |
| **ActiveMQ Artemis**       | Next-generation broker (based on HornetQ), JMS 2.0 compliant, much faster, and designed for high performance and scalability. |




---
---





## Install Apache ActiveMQ:


### System Requirements:

- Java 11+ (ActiveMQ requires Java)
- Network ports:
    - 61616 → Broker (JMS)
    - 8161 → Web Console


### Install Java: 

_To install ActiveMQ, your system must have Java installed on your server:_
```
yum install java-17-openjdk-devel
```



### Download ActiveMQ:

Visit the Apache ActiveMQ downloads page (`https://activemq.apache.org/download.html` or `https://downloads.apache.org/activemq/`) to get the latest of Apache ActiveMQ. 



```
mkdir -p /opt/activemq

cd /opt/activemq
```


```
wget https://downloads.apache.org/activemq/5.19.1/apache-activemq-5.19.1-bin.tar.gz

Or,

wget https://downloads.apache.org/activemq/6.1.8/apache-activemq-6.1.8-bin.tar.gz
```



```
tar -xvf apache-activemq-5.19.1-bin.tar.gz

mv apache-activemq-5.19.1 activemq-5.19.1
cd activemq-5.19.1
```



### Configure ActiveMQ: 


_Find this `<property name="host" value="127.0.0.1"/>` and change it to: `<property name="host" value="0.0.0.0"/>`:_

```bash 

vim /opt/activemq/activemq-5.19.1/conf/jetty.xml


<bean id="jettyPort" class="org.apache.activemq.web.WebConsolePort" init-method="start">
             <!-- the default port number for the web console -->
        <property name="host" value="0.0.0.0"/>
        <property name="port" value="8161"/>
    </bean>
```





### Running ActiveMQ as a Service Under Systemd: 

```
useradd activemq

chown -R activemq:activemq /opt/activemq/activemq-5.19.1
```



_Create a systemd unit file:_
```bash

vim /etc/systemd/system/activemq.service


[Unit]
Description=Apache ActiveMQ Message Broker
After=network.target

[Service]
Type=forking

User=activemq
Group=activemq

#WorkingDirectory=/opt/activemq/activemq-5.19.1/bin

ExecStart=/opt/activemq/activemq-5.19.1/bin/activemq start
ExecStop=/opt/activemq/activemq-5.19.1/bin/activemq stop

Restart=on-abort


[Install]
WantedBy=multi-user.target
```


```
systemctl daemon-reload

systemctl enable activemq
systemctl start activemq

systemctl status activemq
```


```
netstat -tlpn | grep 8161

tcp6       0      0 :::8161        :::*       LISTEN      37744/java
```




### ActiveMQ Web console:

Open your browser: `http://<server-ip>:8161/admin`


**Default credentials:**
    - Username: admin
    - Password: admin






### Ref: 
- [Download ActiveMQ Classic](https://activemq.apache.org/components/classic/download/)

