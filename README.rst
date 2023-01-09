What Happens When You Type www.google.com In Your Browser And Press Enter




INTRODUCTION TO HOW THE WEB STACK WORKS

    In this modern day, we use the internet for virtually all our activities in life. We can study, connect with our friends and relatives through social media, play online games with people, share your presentations with your co-workers, etc.

    in this article, we will take a journey from www. google .com all the way to Google website. Let’s get started!

    Before getting started, what is a webpage. A webpage is basically a text file formatted a certain way so that your browser (ie. Chrome, Firefox, Safari, etc) can understand it; this format is called HyperText Markup Language (HTML). These files are located in computers that provide the service of storing said files and wait for someone to need them to deliver them. They are called servers because they serve the content that they hold to whoever needs it. 
    There are many servers out here, the most common and the one that we focus on is a web server, the one that serves web pages. We can also find application servers, which are the ones that hold an application base code that will then be used to interact with a web browser or other applications. Database servers are also out there, which are the ones that hold a database that can be updated and consulted when needed.
    These servers in order to deliver their content, need to have an address where the server can deliver the content to. These addresses are called IP (Internet Protocol) Address, a set of 4 numbers that range from 0 to 255 (one byte) separated by periods (ie. 127.0.0.1 (for localhost)), in our case www.google.com ( 216.58.210.142)
    Another concept that is important to know is that the service traffic for the delivery can be one of two: Transmission Control Protocol (TCP) and User Datagram Protocol (UDP). Each one determines the way the content of a server is served, or delivered. 
    TCP is usually used to deliver static websites such as Google or Wikipedia and also email services and to download files to your computer because TCP makes sure that all the content that is needed gets delivered. It accomplishes this by sending the file in small packets of data and along with each packet a confirmation to know that the packet was delivered; that's why if you are ever downloading something and your internet connection suddenly drops when it comes back up you don't have to start over because the server would know exactly how many packets you have and how many you still need to receive. The downside to TCP is that because it has to confirm whether you got the packet or not before sending the next, it tends to be slower.
    UDP, on the other hand, is usually used to serve live videos or online games. This is because UDP is a lot faster than TCP since UDP does not check if the information was received or not; it is not important. The only thing UDP cares about is sending the information. That is the reason why if you've ever watched a live video and if either your internet connection or the host's drops, you would just stop seeing the content; and when the connection comes back up you will only see the current stream of the broadcast and what was missed is forever lost. This is also true for online videogames (if you've played them you know exactly what this means)



	

DNS - Server

Imagine we have to search websites with IP address. That is when domain name comes to rescue. Like you save your friend’s phone number in your contact with a name you want to, domain names are alphabetic representation for IP address. The reason we have domain names in the first place is because humans remember words better than numbers. DNS is here for us to remember the IP of each domain. If the browser doesn’t know that domain name (it’s not stored in its cache), it is going to ask the Domain Name System for the IP address corresponding to this particular domain name.

The DNS request first goes through the resolver. The resolver is usually our Internet Service Provider, and if it doesn’t find the IP in its cache, it’s going to request the root server. The root server knows where the TLD (Top-Level Domain) server is. In our case, the top-level domain is .com (for google.com). Other types of TLD are .net, .fr, etc. If the TLD server doesn’t know the IP, it points the resolver to the Authoritative Name Servers for the domain name. Usually, there is more than one name server attached to one domain name. But any of those name servers can give the IP for the domain name they are attached to. Now the resolver has the IP address(for example, 216.58.210.142), and can send it back to the browser which will perform its request to the corresponding server.

    Protocols: TCP/IP

We mentioned how domain names actually represent IP addresses, but IP is not the only type of protocol use by the Internet. The Internet Protocol Suite is often referred to as TCP/IP (TCP stand for Transmission Control Protocol), and it also contains other types of protocols. It’s a set of rules that define how servers and clients interact over the network, and how data should be transferred, broken into packets, received, etc. as explained earlier.

    Firewall

In order to secure our contents from hackers and attacks, servers are often equipped with a firewall. A firewall is a software that sets rules about what can enter or leave a part of a network. In the case of our example, when the browser asks for the website at the address 216.58.210.142 (google.com), that request has be processed by a firewall which will decide if it’s safe, or if it’s a threat to the server’s security. The browser itself can also be equipped with a firewall to detect if the IP given by the DNS request is a potential malicious agent.

    HTTPS/SLL (Security & Encryption)

Now that the browser has the IP address, it is going to take care of the other part of the URL, the https:// part. HTTPS stands for HyperText Transfer Protocol Secure, and is a secure version of the regular HTTP. This transfer protocol defines different types of requests and responses served to clients and servers over a network. In other terms, it’s the main way to transfer data between a browser and a website. HTTP and HTTPS requests include GET, POST, PUT, and others. The HTTPS requests and responses are encrypted, which ensure the users that their data can’t be stolen or used by third-parties. For example, if we put our credit card information in a website that uses HTTPS, we are guaranteed that this info is not going to be stored in plain text somewhere accessible to anybody.

Another key component in securing websites is the SSL certificate. SSL stands for Secure Sockets Layer (also known as TSL, Transport Layer Security). The certificate needs be issued from a trusted Certificate Authority, like the famous Let’s Encrypt for example, which gives free SSL certificates. When a website has this certificate, we’re able to see a little lock icon next to the website name in the search bar. On some browsers and with certain types of SSL certificates, the bar turns green.

    Load Balancer

As we mentioned earlier, websites live on servers. For most website where the traffic is consequent, it would be impossible to be hosted on a single server. Plus, it would create a Single Point of Failure (SPOF), because it would only need one attack on said server to take the whole site down.

As needs for higher availability and security rises, websites started augmenting the number of servers they have, organizing them in clusters, and using load-balancers. A load-balancer is a software program that distribute network requests between several servers, following a load-balancing algorithm. HAproxy is a very famous load-balancer, and example of algorithms that we can use are the round-robin, which distributes the requests alternating between all the servers evenly and consequentially, or the least-connection, which distributes requests depending on the current server loads.

    Web Server

Once the requests have been evenly distributed to the servers, they will be processed by one or more web servers. A web server is a software program that serves static content, like simple HTML pages, images or plain text files. Examples of web servers are Nginx or Apache. The web server is responsible for finding where the static content corresponding to the address asked for is living, and for serving it as an HTTP, or HTTPS response.

    Application Server

Having a web server is the basis of any web page. But most sites don’t just want a static page where no interaction is happening, and most websites are dynamic. That means that it’s possible to interact with the site, save information into it, log in with a user name and a password, etc.

This is made possible by the use of one or more application servers. These are software programs responsible for operating applications, communicate with databases and manage user information, among other things. they work behind web servers and will be able to serve a dynamic application using the static content from the web server.

    Database

The last step in our web infrastructure is the Data Base Management System (DBMS). A database is a collection of data, and the DBMS is the program that is going to interact with the database and retrieve, add, modify data in it.

There are several types of database models. The two main ones are relational databases, and non-relational databases. A relational database can be seen as a collection of tables representing objects, where each column is an attribute and each row is an instance of that object. We can perform SQL (Structured Query Language) queries on those databases. MySQL and PostgreSQL are two popular relational databases. A non-relational database can have many forms, as the data inserted in it doesn’t have to follow a particular schema. They are also called NoSQL databases.

A web stack has many layers, and we touched just the surface of it. When we type a www.google.com or any URL in a browser, it takes only microseconds for all the agents we talked about to form a response and serve it to the client. Even knowing what is happening behind the curtain, it is still pretty magical to see it happening before our eyes.

Conclusion

In our modern society when everything is online, it good to know the complexity that takes place in order for us to be able to get to a website. Yet, it is done so fast that very few would even begin to fathom the amazing process that takes place.

I hope this gives you more insight of everything that happens "under the hood" when you type www.google.com in your browser and hit Enter
