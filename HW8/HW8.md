## 1. TCP 3 Way Handshaking
TCP three way handshake is the process of establishing a connection between
client and server before data transmission begins. First, the client sends
a SYN packet to the server saying it wants to connect. Second, the server
responds with SYN+ACK to acknowledge the request and confirm it is ready.
Third, the client sends ACK back to confirm the connection is established.
After these three steps, data can be transmitted safely.

## 2. TCP vs UDP
TCP is connection-based and reliable. It guarantees delivery of data through
acknowledgment and retransmission, but is slower due to the overhead. It is
used for HTTP, file transfer, and database connections where data integrity
is critical. UDP is connectionless and unreliable. It sends data without
waiting for acknowledgment, making it faster but with possible data loss.
It is used for video streaming, gaming, and live calls where speed matters
more than perfect delivery.

## 3. What is Tomcat
Tomcat is an open source Java application server developed by Apache. It
hosts and runs Java web applications by providing a runtime environment for
Servlets and JSP. Spring Boot embeds Tomcat by default, so no separate
installation is needed.

## 4. What are the Basic Components of Tomcat
Tomcat consists of a Connector that handles incoming HTTP requests and
responses, an Engine that processes requests and routes them to the correct
application, a Host that represents a virtual host mapped to a domain name,
and a Context that represents an individual web application deployed on Tomcat.

## 5. What is a Web Server
A web server is software that receives HTTP requests from clients, processes
them, and returns HTTP responses. It hosts and runs web applications and
handles the communication between client and backend code. Common Java web
servers include Tomcat, JBoss, Jetty, and Netty.

## 6. What is 3 Tier Architecture
Three tier architecture divides an application into three layers. The
Controller layer handles incoming HTTP requests and returns responses to
the client. The Service layer contains the business logic and processes
data. The Repository layer connects to the database and performs CRUD
operations. This separation makes the code easier to maintain, test, and
scale independently.

## 7. What is OSI Model
The OSI model is a conceptual framework that standardizes network communication
into 7 layers. The Physical layer handles raw bit transmission over hardware.
The Data Link layer handles node to node data transfer and error detection.
The Network layer handles routing and IP addressing. The Transport layer
handles end to end communication using TCP or UDP. The Session layer manages
sessions between applications. The Presentation layer handles data formatting
and encryption. The Application layer is the closest to the user and handles
protocols like HTTP, FTP, and SMTP. In practice, the TCP/IP model simplifies
this into 4 layers: Network Access, Internet, Transport, and Application.
