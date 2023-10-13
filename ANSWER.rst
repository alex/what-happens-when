So what really happens when you type Google.com in your computer and press enter ?

**DOMAIN NAME SYSTEM (DNS)**
First, the request is sent to the local DNS server. This raises the question, "What is DNS?" Humans are generally not very good at memorizing numbers compared to words. Imagine a world where you couldn't just type "Google.com" and had to know its IP address.
"Hey, mate, what was Google's IP address again?" I know this would have been quite a hassle.
A clever individual came up with an idea to make the process more abstract, and that's when DNS was born. So, what DNS actually does is take in your request, in our case, "Google.com," and it checks which IP address is associated with that site name. For example, it takes "Google.com" and consults a cache in its servers. After checking, it might find the IP address, let's say, "172.217.170.206." Now, the computer understands what this means, but it doesn't understand the site name you initially entered.
The local DNS server then returns to your computer the IP address related to the site you typed. However, the local DNS server only contains information about sites you've previously accessed. This means that if it's not the first time you're visiting Google.com, the IP address will be found in the local DNS server. But what if it's your first time searching for that site? In that case, it connects to other superior servers known as root servers. The root servers provide the next step for the request. They are used to reroute the request, directing it toward the Top-Level Domain Name System (TLD) servers, in this case, '.com.' The TLD servers evaluate the request and provide the associated IP address. The results are then cached within the local DNS servers to avoid repeating the lengthy process if you return to the site. NOTE: //This entire process typically takes only a few milliseconds in real-time.//


**TRANSFER CONTROL PROTOCOL/ INTERNET PROTOCOL (TCP/IP)**
TCP/IP plays a critical role in packaging data into packets, delivering them, and ensuring their reliable delivery.
After your computer acquires the IP address for Google.com, the following steps occur:
Step 1: Your computer initiates the connection by sending a request, known as a "SYN" (Synchronize) segment, to the Google web server, indicating its desire to establish a connection.


Step 2: The Google web server receives the SYN segment and acknowledges your request by sending back a "SYN-ACK" (Synchronize-Acknowledge) segment to your computer. This step essentially says, "I'm ready to connect, and I acknowledge your request."

Step 3: Your computer, upon receiving the SYN-ACK segment, sends an "ACK" (Acknowledge) segment to the server, confirming that it received the acknowledgment. At this point, a connection is established, and this process is often referred to as the "TCP Handshake."

With the TCP handshake successfully completed, a reliable connection is established. This ensures the reliable and ordered delivery of data packets, thanks to the acknowledgments exchanged between your computer and the server, which confirm the receipt of data packets. This process is crucial for the secure and accurate exchange of data between your computer and the remote server.



**FIREWALL**
After a successful connection with the server, the server and your computer have firewalls to allow no harmful information or program to enter your PC or the server. So when as information is sent and received it goes through a firewall to pass the security check. Think of the firewall as security guards that are used to validate information passed through and from one device to the other.
HTTPS/SSL
The challenge with data transfer between devices is that it could be intercepted and diverted by hackers, potentially allowing them to access information meant for your personal computer.
To counter this security threat, encryption of data was introduced. Data in transit is encrypted, making it appear as gibberish to anyone attempting to intercept it without the decryption key. Unauthorized personnel may capture the data packets, but they'll only see nonsensical characters, as they lack the decryption key.
On your personal computer, the data is received and automatically decrypted, rendering it in a human-readable format. In this way, the data remains confidential and secure during transmission, safeguarding it from unauthorized access or tampering.
This is the main importance of using HTTPS(hypertext transferprotocol secure) and HTTP (hypertext transferprotocol) ->[HTTP is not secure]

**LOAD BALANCER**
The IP address you receive when accessing Google.com often points to a load balancer. A load balancer is a crucial component that manages and controls the distribution of traffic to a group of servers. Google, like many large-scale online services, operates multiple servers distributed across the globe. The role of a load balancer is to evenly distribute incoming requests across these servers to prevent overloading any single server.
Load balancers use various algorithms to determine how traffic is distributed, ensuring fair resource utilization and optimal server performance. Here are some common load balancing algorithms:
a) Round Robin Algorithm: This algorithm distributes requests among servers in a cyclical, round-robin fashion. Each server takes its turn to handle requests.
b) Weighted Round Robin Algorithm: With this approach, requests are distributed among servers in a weighted manner. Servers with better performance receive a higher number of requests compared to those with lower performance. For example, Server 1 may receive two requests for every one request sent to Server 2, based on predefined ratios.
c) Least Connection Algorithm: This algorithm directs traffic to the server with the least number of active connections at any given moment, helping to evenly distribute the load.
d) Weighted Least Connection Algorithm: Similar to the "Least Connection" algorithm, but it also considers predefined weight ratios to direct traffic based on server performance. The server with the least connections relative to its performance capacity is favored.
e) IP Hash: In this approach, the load balancer maintains a cache of IP addresses that connect to specific servers. It maps these IP addresses to the server they were initially connected to, ensuring future requests from the same IP address are directed to the same server. This is useful for maintaining session consistency.
These load balancing algorithms play a crucial role in ensuring that traffic is efficiently managed across multiple servers, improving performance, scalability, and overall system reliability.

**WEB SERVERS**
Web servers are like waiters in a hotel they serve already cooked meals. So for web servers they serve web pages content to the user. They process the instructions you have given then from there they know exactly what to serve.
If a certain dish... sorry page is not present it gives a 404 error unless the error response is changed to something else.

**APPLICATION SERVERS**
Application servers are used to serve dynamic contents, for example if you are searching for something in google the results processed is dynamic since whatever you are searching may change instantly, so having web pages for each and everyone of those search is painfully hard and consumes a lot of storage.
