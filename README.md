# System Design Case Study                   
                      Topic: PayPal System Design
## Introduction: 
PayPal is an online payment platform that facilitates secure and convenient electronic transactions between individuals and businesses. It allows users to make payments, transfer funds, and conduct financial transactions online. The platform supports various payment methods, including credit/debit cards, bank transfers, and PayPal balances.

# Requirements:
## Functional Requirements
1.	User authentication and Registration:  user can create accounts with unique credentials. Secure authentication and mechanisms including multifactor authentication.
2.	Fund transfer: Users can transfer funds PayPal accounts. Supports for various funding sources such as bank accounts and credit/debit cards.

3.	Payment processing: Enable users to make online purchase using PayPal. Integration with e-commerce platforms for seamless transactions

4.	Withdrawals: User can withdraw their funds to link bank accounts.

5.	Transaction History: Maintain a detailed transaction history for users to track their financial activites.

6.	Payment Requests: User can send and receive payment requests
Non-Functional Requirements:

7.	Performance: Low latency payment processing and fund transfer.

8.	Scalability:  The system should scale horizontally to handle a growing base and transaction volume.

9.	Security:  Implement a robast security measures to protect user accounts and financial data.

10.	Availability:  Ensure high availabity to allow user to access the platform. Implement redundancy and failover mechanisms.

## Extended requirements:
1.	Developer API:  Provide a robust developer API to integrate PayPal service to their applications.

2.	Internationalizations: Support multiple currencies and languages to cater a global user base.

4.	Analytics and metrics.

# Estimation and Constraints
## Traffic
PayPal processes millions of transactions daily, with a mix of different transaction types ( payments, transfer et…)

As per as our requirement we have 300 million daily active users. So we have **10 million daily active users (DAU).**

Let’s assume that each user do 5 transaction or payment on average.  So this gives us **50 millions** transaction/Payments per day.

What would be request per second (RPS) for our system?

**5o million / (24hrs * 3600s ) ~= 5k/s**

## Storage:
Let’s assume that each transaction or payments is about 200 bytes. We will require 100GB  of database storage daily.

**50 million * 200 bytes  ~= 100 GB/ per day.**

After 10 years, we will require 3.65 PB of database storage.

**100GB * 365 days * 10 Yrs =~ 3.65 PB**

## Bandwidth: 
As our system handling 5k request per second and we assume that each request size is 1kb, the minimum Bandwidth is 4.88MB per second.
5k/s * 1kb  ~= 4.88 MB/s
High Level Estimation:
Type	Estimate
Daily Active Users (DAU)	10 Million
Request Per Second (RPS)	~= 5k/s
Storage Per Day	~= 100GB / per day
Storage After 10 years	~= 3.65 PB 
Bandwidth Per Second	~= 4.88 MB/s

## High Level Estimation
| Type	 | Estimate |
|--|--|
| Daily Active Users (DAU) | 10 million |
|Request Per Second (RPS) |5k/s | 
|Storage (Per Day)	 | 100GB|
| Storage (10 years)| 3.65PB|
|Bandwidth	 |4.88MB /s |

## Data Model
![enter image description here](https://i.ibb.co/Lhn91NW/paypal-entity.png)
 

# Database
While our data model seems quite relational, we don't necessarily need to store everything in a single database, as this can limit our scalability and quickly become a bottleneck. 

We will split the data between different services each having ownership over a particular table. Then we can use a relational database such as PostgreSQL or a distributed NoSQL database such as Apache Cassandra for our use case.

# API Design 
APIs are interfaces that allow one-of-a-kind software program components to communicate with every different components. In a Online Banking System it also permits various APIs facilitate seamless integration between microservices, external services, and the user interface. Some additives of API are as comply with:

Payment Gateway API: This API allows communicate a number of the payment processing microservice and external payment gateways. This API is make sure payment is processed to right user.

User Authentication API: It allows user authentication microservice to interact with different additives. It verifies user credentials at the time when person login in his device and also validate that only authorize person have access to system.

Notification API: It Facilitates communication among the notification microservice and its exceptional components. This API ensures that clients receive notifications when they perform any transactions.

External Services Integration APIs: It allows integration with external services which consist of banks, credit card networks, and identity verification 
services. These APIs permit the Online Banking System to have interaction with external entities securely.

Internal Communication APIs: It Facilitates conversation between distinct microservices within the price tool. These APIs make certain that unique element can interact and percentage their records internally.

## API Code implementation
| Name	 | Method	 |path	| param	| Return |
|--|--|--|--|--|
| Register	 | POST	 |/api/user/register	|- | Message|
| Login	  | POST	 | /api/user/login	  | - |User     information and token   |
| Payment process	   | POST  | /api/payment/process	   | - |  -	message  |
| Transaction history  | GET	 | /api/transaction/history	 | userID	 | Transaction  |
| Transaction update  | PUT	  | /api/transaction/update  | transactionId	 | message |

# High Level Design
## Choose Architecture

Which architecture should we choose?
1.	Monolithic
2.	Microservice

To scale easily we can choose microservice

## Services
1.	User service
2.	Transaction service
3.	Analytics  service
4.	Notification service
5.	Payment service

## Inter service Communication
Since our architecture is microservices-based, services will be communicating with each other as well. Generally, REST or HTTP performs well but we can further improve the performance using gRPC which is more lightweight and efficient. 

Service discovery is another thing we will have to take into account. We can also use a service mesh that enables managed, observable, and secure communication between individual services.

For asynchronous communication between services, we can choose integrated message queue.

 # Detailed Design
## Data Partitioning:
To scale out our databases we will need to partition our data. Horizontal partitioning (aka Sharding) can be a good first step. We can use partitions schemes such as: 

1.	 Hash-Based Partitioning 
2.	 List-Based Partitioning 
3.	 Range Based Partitioning 
4.	 Composite Partitioning 

The above approaches can still cause uneven data and load distribution, we can solve this using Consistent hashing.

## Metrics and Analytics
Recording analytics and metrics is one of our extended requirements. As we will be using Apache Kafka to publish all sorts of events, we can process these events and run analytics on the data using Apache Spark which is an open-source unified analytics engine for large-scale data processing.
Caching

Which cache eviction policy to use? 

We can use solutions like Redis or Memcached and cache 20% of the daily traffic but what kind of cache eviction policy would best fit our needs? 
Least Recently Used (LRU) can be a good policy for our system. In this policy, we discard the least recently used key first. 

How to handle cache miss?

 Whenever there is a cache miss, our servers can hit the database directly and update the cache with the new entries.
 
## Horizontal scaling
Horizontal scaling includes including more servers or times to distribute the workload efficiently. this indicates increasing the number of servers to address a higher range of concurrent clients and transactions. By horizontally scaling, the device can preserve responsiveness throughout height intervals with out experiencing standard overall performance bottlenecks.
## Load Balancing
Implementing load balancing mechanisms is essential for various incoming requests gently during more than one servers. This guarantees that no single server is overload with traffic, optimizing useful resource usage and preventing a single point of failure. Load balancing is a crucial strategy for boosting tool reliability and responsiveness.
## Security
Protocols-Information sent over the network is secured through HTTPS.

Signature – An HMAC based signature is used to verify both request and response message’s authenticity.

PCI DSC – the payment gateway are highest level certified for the data security standards defined by the payment industry.

Multi –factor Authentication – 2 factor authentication is done using a 3D secure password or an OTP.

Tokenization – Used to save cards as token.

Database – secured using the advanced Encryption standard.
External Integrations:

Financial Institutions: Integrates with banking and financial partners for fund transfers and transactions.

E-commerce Platforms: Collaborates with e-commerce platforms to facilitate seamless transactions.

Developer APIs:  we can provides a robust API for developers to integrate PayPal services into third-party applications.

## Detailed Design implementation
![enter image description here](https://i.ibb.co/tYv5Ygj/pay-Pal-detailed-design.png)
# Identify Bottlenecks
 ● "What if one of our services crashes?"
 
 ● "How will we distribute our traffic between our components?" 
 
● "How can we reduce the load on our database?" 

● "How to improve the availability of our cache?" 

● "How can we make our notification system more robust?" 

● "How can we reduce media storage costs"?
# Resolve Bottlenecks
Resolve Bottlenecks To make our system more resilient we can do the following:

 ● Running multiple instances of our Servers and Key Generation Service. 
 
● Introducing load balancers between clients, servers, databases, and cache servers. 

● Using multiple read replicas for our database as it's a read-heavy system. 

● Exactly once delivery and message ordering is challenging in a distributed system, we can use a dedicated message broker such as Apache Kafka or NATS to make our notification system more robust.

**Thank You**
