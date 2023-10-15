What happens when you type google.com and press Enter?
Let's assume you want to search for something in google.com, the first thing you have to do is to go to your browser and then start typing 'google.com', easy right? But not always for your computer.

Every website has it's own IP address that identifies it from other ones (Eg: 8.8.8.8) and we as humans can't remember all this numbers, and that's why we have the DNS. DNS stands for Domain Name System, it translates human-adapted, text-based domain name to machine adapted, numerical-based IP (Eg: google.com -> 8.8.8.8). The browser and OS (Operating System) search in their cache first to see if they knew the IP for google.com, but since they didn't the OS call the resolver. This last is usually your ISP (Internet Service Provider) it checks its cache files, if it doesn't exist it will ask the root server, this one knows where to locate the Top Level Domain (in our example it's .com from google.com), this last find the authoritative name servers for the domain 'google.com' and finally the resolver get the answer from these authoritative name servers. The resolver saves it and sends it to the OS and this one saves it and sends it to the browser.

The html page of google.com website is stored in a server, and just like people, it's important for computers to have a common way to communicate with each other. Today most computers do this through TCP/IP model (stands for Transmission Control Protocol/Internet Protocol). It's a set of standardized rules that allow computers to communicate on a network such as the internet. TCP and IP are two separate computer network protocols:
IP is the part that obtains the address to which data is sent. TCP is responsible for data delivery once that IP address has been found. TCP/IP breaks each message into packets, and those packets are then reassembled on the other end. In fact, each packet could take a different route to the other computer, if the first route is unavailable. TCP/IP divides the different communications tasks into 4 layers:
Application layer:
	This what the user interacts with such as email and messaging.
Transport layer:
	It ensures a reliable data connection between the two devices. It devides data in packets, acknowledges the packets that it has received from the other device, and make sure that the other device acknowledges the packets it receives.
Internet layer (Network layer):
	Controls the movement of packets around the network.
Datalink layer:
	What handles physical parts of sending and receiving data using the Ethernet cable, wireless network, network interface card, device driver in the computer, and so on.

Each of our computers and webservers should have a firewall. A firewall is a division between a private network and an outer network, often the internet, that manages traffic passing between the two networks. It's implemented through either hardware or software. Firewalls allow, limit and block network traffic based on preconfigured rules in the hardware or software, analysing data packets that request entry to the network.

So after getting the IP address of google.com our browser sends an HTTPS request to the google server, and this last sends an HTTPS response to our browser that contains the html page of google.com website. The HTTPS protocol stands for Hyper Text Transfer Protocol Secure, it's a language that webbrowsers use to communicate with webservers. The 'S' in 'HTTPS' stands for secure and means that messages are send encrypted using SSL/TLS protocols so that eavesdroppers can't read messages.

Ever wonder how Google and other web giants are handling such huge amounts of traffic? They don't have just one server, but tens of thousands of them. In order to achieve this, web traffic needs to be distributed to these servers, and that is the role of a load-balancer.

Then it comes the role of web servers, it's a software (Nginx, Apache...) that delivers web pages it's like a link between our browser and servers. And the application server that exposes business logic to client applications through various protocols.

And finally the Database, an organized collection of data stored in servers and usually controlled by DBMS (stands for DataBase Management System).
