what happens when you type www.google.com or any website and hit enter? In this blog post, we'll take a deep dive into the journey of a web request, from the moment you type in the URL to the moment you see the webpage rendered on your screen. We'll explore each step of the process, including DNS resolution, TCP/IP connections, firewall traversal, HTTPS encryption, load balancing, web server handling, application server processing, and database retrieval.

when you type any URL on the browser. the browser will as the operating system if the URL exists in the local machine if not

DNS (domain name system) will resolve the human-readable URL to the actual address of the website which is called the IP address. but wait how DNS resolves domain name IP address

DNS server involved in loading a web page

DNS recursor -> used as a recursive caller to others until the IP address is resolved. after the IP address is resolved the DNS recusor returns the webpage to the client’s computer.

Root nameserver -> resolves the hostname without resolving the top-level domain name such as (.com, .net, .org)

TLD (the top-level domain server) -> This server resolves the top-level domain name and returns the IP address of the website)

Authoritative nameserver -> checks if the client or user is authorized to access the resource they requested

first of all the DNS recursor will take a request from the browser and ask the Root nameserver which returns the address of the TLD server to the DNS recursor then the request will be sent to TLD to resolve the IP address. TLD will return the numeric machine-readable IP address after the Authoritative nameserver finally the DNS recursor can able to find the requested web page using the IP address.

DNS Request: The journey begins with a Domain Name System (DNS) request. When you type "https://www.google.com" into your browser, your computer first needs to translate the human-readable domain name "www.google.com" into an IP address, which is the numerical address that uniquely identifies the server on the internet. Your computer sends a DNS query to a DNS server, which then responds with the corresponding IP address for the domain.

TCP/IP: Once the IP address is obtained, your computer establishes a Transmission Control Protocol (TCP) connection with theserver using Internet Protocol (IP). TCP/IP is the fundamental communication protocol of the internet, responsible for establishing and maintaining connections between devices.

Firewall: Before the TCP connection is established, the request may need to pass through a firewall, a network security system that monitors and controls incoming and outgoing traffic based on predetermined security rules. The firewall ensures that only authorized traffic is allowed to pass through to the server.

HTTPS/SSL: In the case of secure websites (those using HTTPS), an additional layer of encryption is applied to the communication between the browser and the server. This is achieved through Secure Sockets Layer (SSL) or its successor, Transport Layer Security (TLS). HTTPS encryption ensures that the data exchanged between the browser and the server is secure and cannot be intercepted by malicious actors.

Load-Balancer: Large websites like Google typically have multiple servers distributed across different locations to handle the incoming traffic efficiently. A load balancer sits in front of these servers and distributes incoming requests across them, ensuring optimal performance and reliability.

Web Server: Once the request reaches the appropriate web server, the server processes the request and retrieves the requestedwebpage or resource. The web server software (e.g., Apache, Nginx) handles tasks such as serving static files, executing server-side scripts, and generating dynamic content.

Application Server: In some cases, the web server may need to communicate with an application server to process dynamic content or execute complex business logic. The application server runs the backend code responsible for generating dynamic web pages, interacting with databases, and performing other application-specific tasks.

Database: If the requested content involves retrieving data from a database (e.g., search results, user information), the application server queries the database server to fetch the relevant data. The database server processes the query and returns the requested data to the application server, which then incorporates it into the response sent back to the client.

Conclusion: The journey of a web request involves multiple steps, from DNS resolution to database retrieval. Understanding this process provides valuable insights into how the internet works and how websites deliver content to users. Next time you type a URL into your browser, remember the intricate journey that takes place behind the scenes to bring you the desired webpage.
