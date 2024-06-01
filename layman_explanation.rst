What Happens When You Type “google.com” and Press Enter

When you type “google.com” in your browser and press Enter, a complex sequence of events occurs involving multiple layers of the web stack as follows:

DNS request
TCP/IP
Firewall
HTTPS/SSL
Load-balancer
Web server
Application server
Database

This detailed explanation and diagram cover the entire process, from typing “google.com” in your browser to seeing the webpage rendered
Here’s a detailed breakdown:

(1) DNS Resolution:

First the user types “google.com” into the address bar of a web browser and presses Enter.

a) Local DNS Resolver: If the IP address is not in the browser cache, the request is sent to the local DNS resolver (usually managed by the user’s Internet Service Provider (ISP)).

b) Recursive Query: If the local DNS resolver does not have the IP address for “google.com” cached, it performs a recursive query.

Root DNS Servers: The resolver first contacts one of the root DNS servers.
TLD DNS Servers: The root server responds with the address of the TLD server responsible for “.com” domains.
Authoritative DNS Server: The resolver then queries the TLD server, which responds with the authoritative DNS server for “google.com”.
IP Address Retrieval: Finally, the authoritative server responds with the IP address of “google.com”.
(2) TCP/IP Connection:

The browser initiates a TCP (Transmission Control Protocol) connection to the resolved IP address on port 80 (HTTP) or 443 (HTTPS).
This involves a three-way handshake process:
The client sends a SYN (synchronize) packet to the server.
The server responds with a SYN-ACK (synchronize-acknowledge) packet.
The client replies with an ACK (acknowledge) packet, establishing the connection.
(3) HTTPS/SSL:

If the connection is over HTTPS, an SSL/TLS handshake occurs to establish a secure connection.
The browser and server exchange encryption keys to encrypt the data sent over the connection as follows:
i) Certificate Exchange: The server sends its SSL certificate to the browser.

ii) Certificate Validation: The browser validates the server’s certificate.

iii) Key Exchange: The browser and server exchange encryption keys to establish a secure session.

(4) Firewall and Load Balancer:

Firewall: The request may pass through firewalls which filter out unauthorized or malicious traffic.
Load Balancer: If the website uses load balancing, the request is distributed to one of several web servers to balance the load.
(5) Web Server:

The request reaches the web server (e.g., Nginx, Apache) which handles the HTTP request.
The web server processes the request or forwards it to an application server if further processing is required.
(6) Application Server:

Dynamic Content Processing: If dynamic content is needed, the web server forwards the request to an application server (e.g., using PHP, Python, Node.js). The application server processes the request, executes business logic, and prepares a response.

(7) Database Query:

Database Interaction: The application server may need to query a database to retrieve or store data. It sends a query to the database server (e.g., MySQL, PostgreSQL).
Data Retrieval: The database processes the query and sends the required data back to the application server.
(8) Response:

Response Preparation: The application server generates the response (e.g., an HTML page) and sends it back to the web server.
Response Delivery: The web server sends the response back to the browser over the established TCP connection.
(9) Rendering:

HTML Parsing: The browser receives the HTML content and begins parsing it.
Resource Requests: As the HTML is parsed, the browser requests additional resources like CSS, JavaScript, and images.
Page Rendering: The browser renders the page using the HTML and associated resources, displaying it to the user.

+------------------+        +------------------+        +------------------+
|     Browser      |        |       DNS        |        |       CDN        |
+--------+---------+        +--------+---------+        +--------+---------+
         |                          |                          |
         |  1. User types URL       |                          |
         |------------------------->|                          |
         |                          |                          |
         |  2. DNS resolution       |                          |
         |------------------------->|                          |
         |                          |                          |
         |  3. Get IP address       |                          |
         |<-------------------------|                          |
         |                          |                          |
         |  4. TCP/IP connection    |                          |
         |------------------------->|                          |
         |                          |                          |
         |  5. SSL Handshake        |                          |
         |------------------------->|                          |
         |                          |                          |
         |  6. Firewall and         |                          |
         |     Load Balancer        |                          |
         |------------------------->|                          |
         |                          |                          |
         |  7. Web server processes |                          |
         |     the request          |                          |
         |------------------------->|                          |
         |                          |                          |
         |  8. Application server   |                          |
         |     and Database query   |                          |
         |------------------------->|                          |
         |                          |                          |
         |  9. Generate response    |                          |
         |     and send back        |                          |
         |<-------------------------|                          |
         |                          |                          |
         | 10. Render the page      |                          |
         |                          |                          |
         +------------------+        +------------------+        +------------------+

