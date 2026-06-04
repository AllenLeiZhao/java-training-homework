## 1.client - server model
- Client-Server model is a communication pattern between client and server. A client, typically a browser or a mobile app, sends an HTTP request to the server. The server will handle it with business logic or database operations, and then respond an HTTP response to the client. A server could handle multiple clients through multi-threading.

## 2.application service
- Application server is the software that hosts and runs the business logic of a web application. It receives HTTP requests, executes the application code, and returns responses. Common Java application servers include Tomcat, JBoss, and Jetty. In Spring Boot, Tomcat is embedded by default, so no separate installation is needed.

## 3.http request/response
- HTTP request is sent from client to server. It contains a request method (GET, POST, PUT, DELETE, PATCH), a URL, request headers (e.g. Content-Type, Authorization), and an optional request body (POST/PUT have body, GET/DELETE typically don't). HTTP response is sent from server back to client. It contains a status code (e.g. 200 OK, 404 Not Found), response headers, and a response body usually in JSON format.

## 4.horizontal scaling vs vertical scaling
- Horizontal scaling means adding more servers to handle increased traffic. It is theoretically unlimited and is the recommended approach in modern architecture. Vertical scaling means upgrading existing hardware, such as adding more CPU, memory or RAM to a single server. It has a physical limit and is more expensive. Horizontal scaling is preferred because it also enables load balancing across multiple servers.

## 5.load balancer
- A load balancer sits between client and servers. It evenly distributes incoming requests to multiple servers to prevent any single server from being overloaded. It also improves high availability because if one server goes down, the load balancer redirects traffic to other active servers, keeping the service still available.

## 6.microservice, microfronted
- Microservice is an architectural style in contrast to monolithic architecture. Instead of building one large application, we split it into small independent services, each responsible for a specific business function. Each service can be developed, deployed, and scaled independently using different tech stacks. If one service goes down or is being updated, it does not affect the others, which improves availability and maintainability. Microfrontend applies the same concept to the frontend, splitting the UI into small independent pieces that different teams can develop and deploy independently.

## 7.database: relational database (sql database), nonrelational database ( no-sql database)
- Relational databases like MySQL and PostgreSQL have a fixed schema with predefined tables and columns. They support SQL for querying and guarantee ACID transactions — Atomicity, Consistency, Isolation and Durability — ensuring data integrity. Non-relational databases like Redis, MongoDB and Elasticsearch are more flexible with dynamic schemas, each designed for different purposes — Redis for caching, MongoDB for documents, Elasticsearch for searching. They are generally faster because they relax ACID constraints and support better horizontal scaling, but they sacrifice strong consistency.

## 8.api gateway
- API Gateway acts like a security guard sitting at the front door of the system. All client requests must pass through it first before reaching any backend service. It is responsible for authentication — verifying the client's identity, rate limiting — preventing too many requests from overwhelming the system, and routing — directing requests to the correct microservice. API Gateway sits before the Load Balancer in the request flow.

## 9.message queue
- Message Queue is an asynchronous communication tool between backend services. It decouples the sender and receiver, letting them work independently without relying on each other. Once the sender puts a message into the queue, it can immediately continue without waiting for the receiver to process it. During peak hours, the queue safely stores all incoming messages and the receiver processes them one by one at its own pace, preventing the system from being overwhelmed. Common tools include Kafka and RabbitMQ.

## 10.log, monitor
- -Log records all system activities and events in a timeline, such as errors, requests and operations. It is used for troubleshooting and auditing after incidents occur. Common tools include ELK Stack. Monitor tracks the real-time health and performance of services, such as CPU usage, memory, response time and error rate. It triggers alerts when anomalies are detected. Common tools include Prometheus, Grafana and AWS CloudWatch.

## 11.deployment with AWS/Azure/GCP
- Deployment with AWS, Azure or GCP means deploying your application to a cloud platform instead of maintaining your own physical servers. AWS is from Amazon, Azure from Microsoft, and GCP from Google. They provide various services — for example AWS offers EC2 for virtual servers, S3 for file storage, RDS for managed databases, and ECS for container deployment. You rent these resources, deploy your code, configure authentication and access control, and choose your region. After deployment, users can access your application from anywhere.

## 12.security (authentication and authorization)
- Security in web applications involves two key concepts: Authentication and Authorization. Authentication verifies who you are — for example, logging in with username and password, or using JWT token. Authorization verifies what you are allowed to do — for example, a regular user can view data but cannot delete it, while an admin can do everything. In short, Authentication asks 'who are you?' and Authorization asks 'what can you do?

## 13.why testing
- Testing helps us find potential bugs and problems in our code before deploying to production. It reduces risks, ensures code quality, and improves user experience. Testing also provides a safety net when refactoring — if we change existing code, tests ensure we don't break other parts of the application. Without testing, bugs may reach production, damage our reputation and even cause financial loss. Common testing tools in Java include JUnit and Mockito.

## Record Link
[Click here to watch HW6 Record](https://allen-2026-learning.s3.us-east-2.amazonaws.com/HW6-Recording-2026-06-03.mp4)
