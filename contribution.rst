What happens when you type in "https://www.google.com" in your #browser and click enter ?

The behind the scene process of loading a #web page(www.google.com) involves multiple systems and #protocols working together to achieve this aim including:
#DNS requests
#TCP/IP
#Firewalls
#HTTPS/SSL
#Load balancers
Web #servers
#Application servers
#Databases

DNS Request:
It starts with a Domain Name System (DNS) request. On entering "https://www.google.com" in your browser, the browser finds the IP address associated with that domain name and sends a DNS request to the DNS server, which resolves domain names to IP addresses. The DNS server responds with the corresponding IP address for "www.google.com".

TCP/IP:
The browser now creates a Transmission Control Protocol (TCP) connection with the server using Internet Protocol (IP).
The aim is to ensure a trusted communication by dividing the data into separate packets which are reassembled accordingly at the destination site.
It is the Internet Protocol that routes these packets of data over the internet to their destination.

Firewalls:
Firewalls are security bridges that control and monitor incoming and outgoing network traffic and they are encountered usually before the TCP connection is established. Therefore a secure TCP connection is established if the firewall allows the packets to pass across after examining them.

HTTPS/SSL:
The browser initiates a Hypertext Transfer Protocol Secure (HTTPS) connection with a Secure Socket Layer (SSL) or a Transport Layer Security (TLS) after a secure TCP connection is established. SSL or TLS encrypts the data being transferred between the browser and the server to ensure a trusted communication establishment. This prevents interference to the moving data.

Load Balancer:
Consider websites like google and other high traffic websites. Load balancers act as intermediary between the client and the server by ensuring that each data is sent to an available server that can handle it. It disseminates these data requests to different web servers to optimize performance and prevent system overload.

Web Servers:
As the request reaches the web server, the server processes it and returns the appropriate resource like images or html file and generates a HTTP response. Google web server processes the search query and returns a search result page.

Application Server:
In complex web apps, the application server is usually different from the web server in that it processes the request from the web server and prepares the response to be sent back.
It stores the organization's logic and handles dynamic content generation.

Databases:
This stores the data allowing efficient manipulation and retrieval of data by processing query requests from the application server and sending back appropriate data requested for.

Though it may happen in a very short time but the processes undergone in this little time may be as complex as we've seen.
