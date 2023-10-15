# What happens when You type www.google.com and hit enter
====================

The internet and the world wide web has really grown since it was lauched, with millions of daily users on multiple devices. Only a little number of people actually understand or have a glimpse of what happens when you enter an address called “url” a universal resource locator.

In this post, I will attempt to demystify the process of getting a [age rendered in your browser.

When you type a url for example “https://www.google.com" into your browser’s address bar and hit Enter, it initiates a complex sequence of events that ultimately brings the Google homepage to your screen. This events may happen very fast or slow depending on your internet connection speed. Let’s unravel this intricate process step by step to get a better understanding.

## 1. DNS Request:
The first step is translating the human-readable domain name, which is usually the first part of your url, “www.google.com" into a numerical IP address. The thing is that servers do not communicate with words, but rather in electronic language of binary digits, so what computers understand is numbers, the human readable words must be used to get the numbers that represent the address of the server you want to get a resource from. Getting this is achieved through a Domain Name System (DNS) request. Your browser contacts a DNS server, which acts as a yellow page phone number locator, to obtain the IP address associated with the domain. Once obtained, this IP address is used to locate the server hosting Google’s website.

## 2. TCP/IP:
With the IP address in hand, your browser establishes a Transmission Control Protocol (TCP) connection with the server. This protocol contains different layers of data transfer between your computer and the server or servers hosting the website. TCP ensures reliable, ordered, and error-checked delivery of data between your browser and the server.

## 3. Firewall:
Every computer may have a defense system set up that prevents unwanted connections in and out of the computer, this is called a firewall.

At this point, the request passes through your local firewall, If the firewall allows the connection, the request proceeds to the next stage.

## 4. HTTPS/SSL:
To ensure a secure communication channel, the browser and the server engage in a handshake to establish a secure connection using the Hypertext Transfer Protocol Secure (HTTPS) and the Secure Sockets Layer (SSL) or Transport Layer Security (TLS) protocols. These are connections protocols that ensure transfer of data in packets (chunks) while the ssl encrypts the packets to ensure that hackers have no ability to see the information sent over the connection. This encryption ensures that the data exchanged between your browser and the server remains private and integral.

## 5. Load-Balancer:
Large websites like Google often have multiple servers or cluster of servers in different locations over the world, they often employ use of load balancers. These distribute incoming network traffic across multiple servers, ensuring optimal resource utilization and preventing any single server from becoming a bottleneck. Load balancing enhances the website’s performance, scalability, and availability. A load balancer may be a software hosted on a physical computer, or a hardware component made specifically for this purpose

## 6. Web Server:
Upon reaching the appropriate server, the request lands on a web server. The web server is a program running on the physical server that will process the request, which typically involves fetching static content like HTML, CSS, and images. For Google’s homepage, the server may generate an HTML page dynamically based on various factors.

## 7. Application Server:
For more complex websites, an application server may come into play. This server executes application logic, handles dynamic content generation, and interacts with databases. While Google’s homepage might not require an extensive application server, more complex web applications certainly benefit from this architecture. Here the webserver will communicate with the application server to generate the dynamic page.
It is worthy of note that now there are services availab;e on servers now with both the functionalities of a webserver and application server integrated

## 8. Database:
For websites that rely on dynamic content or personalized information, a database is often involved. The application server communicates with the database to fetch or update data. In Google’s case, its vast infrastructure likely involves complex database systems to manage search queries, user preferences, and more.

## Conclusion:
In summary, the seemingly simple act of typing “https://www.google.com" in your browser triggers a cascade of events involving DNS resolution, TCP/IP connections, firewall checks, secure communication setups, load balancing, web server processing, application server logic, and potential interactions with databases. This intricate dance ensures a seamless and secure user experience, highlighting the sophisticated architecture behind the websites we interact with daily.

Now that we’ve demystified the journey, take a moment to appreciate the technological marvel that brings the vast expanse of the internet to your fingertips with just a simple keystroke.