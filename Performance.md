
## Design principles for high-performance architecture

some vital design principles for your workload performance optimization.

### Reducing latency
Latency can significantly affect your product adoption because users are looking for the fastest applications. It doesn’t matter where your users are located; you need to provide an efficient and reliable service
for your product to grow. Latency is a measure of the time taken for a packet of data to get from one designated point to another. In simpler terms, it’s the delay or lag you experience between initiating an action
and seeing the response on your device or system.

### Improving throughput
Network throughput refers to the amount of data that is successfully transferred over a network in a given period of time.

### Handling concurrency
`Concurrency` plays a vital role in designing scalable and efficient applications. It allows an application to perform multiple tasks simultaneously, making better use of system resources and improving overall application 
performance. By implementing concurrency, developers can ensure that their applications can handle multiple operations at the same time without waiting for one task to complete before starting another. 
This is particularly important in web applications that serve thousands of users and in data processing tasks that need to handle large volumes of data efficiently. Implementing concurrency can lead to significant 
improvements in response times and throughput, enhancing the user experience and the application’s ability to scale.

`Parallelism` is another crucial concept in software design, complementing concurrency by executing multiple operations simultaneously across different processors or cores. While concurrency involves dealing with many 
tasks at once (for example, multitasking on a single-core CPU, where tasks are switched rapidly to give the appearance of simultaneous execution), parallelism takes this a step further by truly performing multiple 
operations at the same time via multi-core processors or distributed systems. This approach significantly speeds up processing times for compute-intensive tasks by dividing the work into small chunks that can be 
processed in parallel. Applications that process large datasets, complex calculations, or tasks that can be divided into independent units benefit greatly from parallelism, achieving higher throughput and efficiency.

Applying caching
Caching helps to improve application performance significantly. It’s essential to understand that almost every application component and infrastructure has a cache mechanism. Utilizing the caching mechanism at each 
layer can help reduce latency and improve the application’s performance.

## Technology selection for performance optimization
We learned about various design patterns, including microservice, event-driven, cached-based, and stateless patterns. An organization may choose a combination of these design patterns depending on their solution’s 
design needs. You can have multiple approaches to architecture design, depending on your workload. Once you have finalized your strategy and start to implement your solution, the next step is to optimize your application. 
To optimize your application, you must collect data by performing load testing and defining benchmarking as per your application’s performance requirements.
Performance optimization is a continuous improvement process in which you need to be cognizant of optimal resource utilization from the beginning of solution design until after the application’s launch. You need to choose 
the right resources as per the workload or tune the application and infrastructure configuration. For example, you might select a NoSQL database to store your application’s session state and store transactions in a 
relational database.

For analytics and reporting purposes, you can offload your production database by loading data from the application database into data warehousing solutions and creating reports from there.

In the case of servers, you can choose a virtual machine or containers. You can take an entirely serverless approach to build and deploy your application code. Regardless of your approach and application workload, you need 
to choose the primary resource type: compute, storage, database, or network.

### Making a computational choice
 public cloud provider such as AWS has serverless offerings, where you don’t need a server to run your application. One of the most popular FaaS offerings is AWS Lambda. Like AWS Lambda, other popular public 
 cloud providers provide solutions in the FaaS space—for example, Microsoft Azure has Azure Functions, and GCP has Google Cloud Functions.

### Working with containers

### Going serverless


## Making a storage choice


## Making a database choice

### Online transactional processing
Most of the traditional relational databases are considered to use online transactional processing (OLTP). The transactional database is the oldest and most popular method of storing and handling application data. 
Some examples of relational OLTP databases are Oracle, Microsoft SQL Server, MySQL, PostgreSQL, and Amazon RDS. The data access pattern for OLTP involves fetching a small dataset by looking up its ID. A database 
transaction means that either all related database updates were completed or none of them were. OLTP databases are suitable for large and complex transactional applications.

### Nonrelational databases
A lot of unstructured and semi-structured data is produced by applications such as social media programs, the Internet of Things (IoT), clickstream data, and logs, where you have very dynamic schemas.
NoSQL databases can store a large amount of data and provide low-access latency. They are easy to scale by adding more nodes when required and can support horizontal scaling out of the box. They are an excellent choice 
for storing user session data and making your application stateless in order to achieve horizontal scaling without compromising user experience. You can develop a distributed application on top of the NoSQL database, 
which provides good latency and scaling, but query joining must be handled at the application layer because NoSQL databases don’t support complex queries such as joining tables and entities.
WS provides a managed NoSQL database called Amazon DynamoDB, which provides high-throughput sub-millisecond latency with unlimited scaling.

AWS provides a managed NoSQL database called Amazon DynamoDB, which provides high-throughput sub-millisecond latency with unlimited scaling.

### Online analytical processing

OLTP and NoSQL databases are helpful for application deployment but have limited capabilities for large-scale analysis. OLAP is primarily used in data warehouse technology. A query for a large volume of structured data 
for analytics purposes is better served by a data warehouse platform designed for faster access to structured data. Modern data warehouses leverage columnar storage formats and Massively Parallel Processing (MPP) 
architecture to significantly enhance data retrieval and analysis speeds. Unlike traditional row-oriented databases, where data is stored in rows, columnar storage organizes data in columns.

The columnar format means you don’t need to scan the entire table when you need to aggregate only one column for data—for example, if you want to determine your inventory sales in a given month. There may be hundreds of 
columns in the order table, but you only need to aggregate data from the purchase column. With a columnar format, you will only scan the purchase column, which reduces the amount of data scanned compared to the row format 
and increases the query performance.

### Building a data search functionality
Often, you need to search a large volume of data to solve issues quickly or get business insights. Searching your applications’ data will help you access and analyze detailed information from different views. 
To search for data with low latency and high throughput, you need search engines.

Elasticsearch is one of the most popular search engine platforms; it is built on top of the Apache Lucene library. Apache Lucene is a free and open-source software library that is the foundation of many popular 
search engines. The ELK (short for Elasticsearch, Logstash, and Kibana) Stack is easy to use to discover large-scale data and index it for searching automatically. Because of its properties, multiple tools have been 
developed around Elasticsearch for visualization and analysis. For example, Logstash works with Elasticsearch to collect, transform, and analyze a large amount of an application’s log data. Kibana has an in-built 
connector with Elasticsearch that provides a simple solution for creating dashboards and analyzing indexed data. Elasticsearch can be deployed in virtual machines and scaled horizontally to increase capacity by adding 
new nodes to the cluster. The AWS public cloud provides the managed Amazon OpenSearch Service, making it cost-effective and simple to scale and manage the Elasticsearch cluster in the cloud.


## Improving network performance

### Using edge computing
### Applying a load balancer
### Applying auto-scaling
### Using
### Using
### Using

## Performance testing

### Types of performance testing
### Load testing: 
This type of testing is about understanding how a system behaves under expected, real-world loads. It’s the equivalent of testing a bridge by gradually adding weight until it carries the anticipated maximum number of 
vehicles. For example, if an e-commerce website expects 10,000 visitors during a holiday sale, load testing will simulate these 10,000 users to ensure the site works smoothly under such conditions.
### Stress testing: 
Imagine cramming too many people into an elevator, beyond its capacity, to see if it still operates or breaks down; that’s the essence of stress testing. It’s designed to push systems to their breaking point, ensuring 
that even in worst-case scenarios, failures don’t lead to catastrophic results. For instance, a banking app might be stress tested to see how it behaves if a million users tried to log in simultaneously, far exceeding its regular traffic.
### Endurance testing: 
The question endurance testing answers is “Can the system run efficiently when subjected to an expected load for an extended period of time?” For example, a streaming service might be endurance tested to ensure it can 
stream movies and shows to users continuously over a weekend without any degradation in quality or speed.
### Spike testing: 
In the real world, user traffic can be unpredictable. Spike testing is like observing how a power grid reacts when everyone suddenly turns on their air conditioners simultaneously during a heatwave. An example might be 
testing a news website during a major event, like the Olympics, where sudden influxes of users might want to check results or updates.
### Volume testing:
Here, the emphasis is on data. It’s analogous to checking how well a library can organize and lend millions of books. For a database-driven application, volume testing might involve seeing how the system behaves when the 
database has billions of entries. A practical example is a global email service testing its system’s responsiveness when searching through vast numbers of stored emails.

## Managing performance monitoring
Performance monitoring is essential when you are trying to keep an eye on performance issues and proactively reduce end-user impact.
You should define your performance baseline and raise the alarm to the team in the case of a threshold breach—for example, an application’s mobile app load time should not be more than three seconds. 
Your alarm should be able to trigger an automated action to handle poorly performing components—for example, adding more nodes to a web application cluster to reduce the request load.

There are multiple monitoring tools that measure application performance and overall infrastructure. You can use a third-party tool like Splunk or the AWS-provided Amazon CloudWatch to monitor any application.

Monitoring solutions can be categorized into active monitoring and passive monitoring solutions:

With active monitoring, you must simulate user activity and identify performance gaps up front. Application data and workload situations constantly change, requiring continuous proactive monitoring. 
Active monitoring works alongside passive monitoring as you run the known possible scenarios to replicate the user experience. You should run active monitoring across all dev, test, and prod environments to catch any 
issue before reaching the user.
Passive monitoring tries to identify unknown patterns in real time. For a web-based application, passive monitoring needs to collect essential metrics from the browser that can cause performance issues. 
You can gather metrics from users regarding their geolocation, browser types, and device types to understand your application’s user experience and geographic performance. Monitoring is all about data, including the 
ingestion, processing, and visualization of lots of data.
Performance always comes at a cost, and, as a solution architect, you need to think about the trade-offs to take the right approach. For example, an organization’s internal applications, such as the timesheet and 
HR programs, may not need as high performance as external products, such as e-commerce applications. 
