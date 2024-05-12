### What Happens When You Type “https://www.google.com” in Your Browser and Press Enter?

**Introduction** When a user enters a web address (URL) like *[google.com] a fascinating journey begins behind the scenes. The process involves translating the domain name into an IP address, establishing secure connections, load balancing, and interacting with web servers and databases. It’s like a precisely planned and coordinated dance of protocols and servers, all working together to deliver the magic of Google right to the screen.

The internet connection: Allows user to send and receive data on the web.

## What is a server?

A server is a computer or computer program which manages access to a centralized resource or service in a network.
Servers are computers that store webpages, sites, or apps.
When a client device wants to access a webpage, a copy of the webpage
is downloaded from the server onto the client machine to be displayed
in the user’s web browser.

What is DNS

**DNS stands for Domain Name System** It’s a hierarchical decentralized naming system for computers, services, or any resource connected to the internet or a private network. DNS translates domain names, like “google.com,” into IP addresses, which are numerical identifiers necessary for routing data packets across networks. In simpler terms, DNS helps users access websites and services using human-readable domain names instead of complex IP addresses.

## What is IP address

**An Internet Protocol (IP) address** is a unique string of characters that identifies each computer using the Internet protocol to communicate over a network or a numerical label assigned to a device connected to a computer network that uses the Internet Protocol for communication.

These are the steps taken place when we type https://www.google.com and help google access to the user.

## 1.DNS Request
When you type **[https://www.google.com](./https://www.google.com)** in a browser and press Enter, the first step is a DNS request. The browser sends a request to a Domain Name System (DNS) server to translate the human-readable domain name, www.google.com, into an IP address. The DNS server responds with the IP address associated with that domain, allowing the browser to locate the server hosting Google.

## 2.TCP/IP
Next, user establishes a connection with the web server using the TCP/IP protocol.
This protocol governs how data is transmitted across the internet. The Transmission Control Protocol (TCP) breaks down data into packets and ensures they reach their intended destination. The Internet Protocol (IP) routes these packets to the correct location.

## 3.Firewall
Before the connection is fully established, the user computer’s firewall steps in. The firewall examines the incoming packets to ensure they are not malicious. If any packets raise suspicion, they are blocked, preventing the connection.

## 4.HTTPS (SSL/TLS) Encryption:

**HTTPS (Hypertext Transfer Protocol Secure)** ensures secure communication between the client (browser) and the server.
When you visit a website using HTTPS (e.g., google.com), the following steps occur:
The server presents an SSL/TLS certificate containing its public key.
The client (browser) verifies the certificate’s authenticity.
A symmetric session key is generated for this connection.
All subsequent data exchanged between client and server is encrypted using this session key.
The server decrypts the data using its private key.
HTTPS prevents Man-in-the-Middle attacks by ensuring that data remains confidential during transmission.

## 5.Load Balancer
With the HTTPS connection established, the request is sent to a load balancer.
A load balancer is a device or software that evenly distributes incoming network
traffic across multiple web servers. This balancing act prevents any single server
from being overwhelmed by traffic, ensuring efficient handling of requests.

## 6.Web Server
One of the web servers receives the HTTPS request from the load balancer. The web server processes the request by locating the necessary resource or web page. Once found, the server generates a response and sends it back to user computer.

## 7.Application Server
In some cases, the web server may not have all the required information to fulfill the request. When this happens, the web server forwards the request to an application server. The application server processes dynamic content, interacts with databases, and performs other backend tasks. It then sends the relevant data back to the web server, which includes it in the response to user browser.

## 8.Database
If the requested content involves database queries (e.g., search results, user profiles), the application server communicates with the database. The database retrieves the necessary data, and the application server incorporates it into the final response.

## Conclusion

The journey from typing google.com to receiving search results involves a complex dance of DNS, protocols, security, load balancing, web servers, application servers, and databases. It’s like a well-composed combination where each instrument plays its part to deliver the magic of Google right to user screen.
