What happens when...
====================
This repository is an attempt to answer the age-old interview question "What
happens when you type google.com into your browser's address box and press enter?"

When you type a uniform resource locator (URL) "https://www.google.com" into your browser and hit Enter, there are several key paths that take effect. These pathways include:

DNS Pathways: This is the first step that takes effect as soon as you input a URL in your browser and hit Enter. The browser contacts the DNS resolver for the IP address linked to the website. If found, the resolver returns the IP address to the browser. In some instances, a URL can be an alias (a pointer) to another URL. In this instance, the resolver finds the aliased URL and also utilizes this URL to search for the IP address associated with it. If found, its IP address is returned to the browser. If the URL cannot be resolved by the resolver, an error message is returned to the browser. Examples of such error messages include "404 Not Found" or "Server Not Found". This error message simply implies that the domain name associated with this URL could not be resolved or found.

TCP/IP: This is the second step, which takes effect after the DNS has returned the IP address of the domain name to the browser. Here, the browser uses the IP address, which is a unique identifier of a device on a network received from the DNS, and makes a call or request to the server, requesting for the web pages or application stored in the IP address. This request is made using the Transmission Control Protocol/Internet Protocol (TCP/IP). If the domain is a secured domain, TCP provides a reliable, ordered and error-free delivery of data. It also breaks data into small packets over a network and then reassembles them in the correct order once they arrive at the destination.

Firewall: This is the gatekeeper of the server. It filters incoming and outgoing requests by ensuring that requests made from IP addresses that are flagged by the Admins do not get across to our server. It acts as a barrier between your network and the internet, allowing only authorized traffic (IPs) to pass through while blocking unauthorized traffic.

Load Balancers: Load balancers simply help to coordinate the amount of load each server receives, reducing the chances of server overload. Overloading a server could lead to server failure, slow response time, or poor user experience. This Load balancer simply distributes incoming traffic that has been granted access by the firewall to multiple servers on the network, handling the load more efficiently and providing a more reliable and faster response. Load balancers use various algorithms to determine how traffic is distributed, such as Round-robin, IP hash, and others.

Web Servers: This server receives the request from the load balancer and searches for the files contained in the IP received. If the IP address has an SSL/TLS certificate, the files that will be sent back through the TCP connection, in terms of packets, will be encrypted, thus preventing any unauthorized user on the network from accessing the contents. This is very essential in securing all user data and transaction information. After encryption, the data is sent back to the client using the same route, and before the browser displays the content, the encryption will be decrypted using the client's (computer) SSL/TLS private key. This process results in an SSL/TLS handshake before the packets are assembled and displayed to the client.

Application Servers: An application server is a software platform that provides an environment for running and managing web applications. When a request is made to an application server, the server receives and processes it. It generates a response and sends it back to the load balancer. The response typically includes the HTTP status code response.

Databases: A database is a software system that stores and manages data. It provides a structured and organized way to store and retrieve data, and it can be accessed by multiple applications and servers. Common examples of databases include MySQL, PostgreSQL, and MongoDB.

When a web or app server needs to retrieve information from a database, it initiates a connection to the database using the server name, port number, database name, username, and password. To store or retrieve information, the server interacts with the database by executing SQL queries.

When retrieving information, the data is packaged and sent to the server, which then encrypts it and sends it to the load balancer. The load balancer delivers it safely to the client.

When updating information on the server, the database sends a successful status report to the client after a successful update.
