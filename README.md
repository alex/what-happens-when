# From Typing “https://www.google.com" to Displaying the Web Page: How does it work? The overview...

<p>A lot happen when you type “https://www.google.com" in your browser and hit the “Enter” button, there are series of processes behind the scenes that retrieves and display the desired web page (google).

Here i will take you on this journey of a browser request and explore the key components involved in this process, including DNS, TCP/IP, Firewall, HTTPS/SSL, Load-balancer, Web server, Application server, and Database.

Let’s break it down in process blocks, follow me.</p>

## PROCESS 1 (DNS REQUEST):
<p>When you hit enter, the browser initiates a request first from DNS (Domain Name System), this is commonly called the “phonebook” of the internet, it attempts to resolve the domain name “www.google.com" to its corresponding IP address via DNS resolver or the DNS server.</p>

<p>Now let me explain, The browser first checks it’s local DNS cache to see if it has the IP address for the requested domain. If found, it skips the DNS resolution process and directly uses the IP address, however if the IP address is not found in the local DNS cache, the browser sends a DNS query to a recursive DNS resolver by ISPs or some kind of DNS third-parties, The recursive DNS resolver receives the query and, if necessary, contacts the root DNS servers (They are the top-level in the DNS hierarchy and maintain information about the authoritative DNS servers responsible for specific top-level domains (TLDs) like “.com,” “.org,” or country-specific TLDs), at this point the root DNS servers respond to the DNS resolver with the IP address of the TLD DNS servers that is in-charge of the requested domain.</p>

<p>Let’s say, you requested “google.com,” the Top Level Domain DNS servers for the “.com” TLD would be queried.</p>
<p>When this is done, the recursive DNS resolver then contacts the authoritative DNS servers responsible for the requested domain. These servers have the specific information about the domain, including the IP address associated with it.
If IP was found, the authoritative DNS servers respond to the DNS resolver with the IP address of the requested domain, while the resolver caches this information for a specified time (TTL) to improve future query response times, and finally provides the IP address to the browser, allowing it to establish a “TCP connection”, Voila! We move to the next big process.</p>

## PROCESS 2 (TCP/IP Handshake):
<p>When the browser obtains the IP address of the web server hosting google.com from PROCESS 1, it attempts to establish a TCP (Transmission Control Protocol) connection with the server using a specific port (usually port 80 for HTTP or port 443 for HTTPS). TCP provides reliable, connection-oriented communication between the client (browser) and the server. Note, before this connection is established this request must pass through a “Firewall”, it will inspect the request, apply predefined rules, and ensures that it is not malicious or violating any security policy, as soon as the request passes active firewall checks there will be a handshake :), let’s move to PROCESS 3. </p>

## PROCESS 3 (HTTPS/SSL):
<p>Remember we prefixed “https://” in the URL, here the browser will use “https” and not “http” protocol, this protocol uses SSL (Secure Sockets Layer) or later TLS (Transport Layer Security) for secure communication. So the browser and server perform an SSL handshake, where they exchange cryptographic keys, verify authenticity, and establish an encrypted connection.</p>

## PROCESS 4 (LOAD BALANCERS):
<p>Have you imagined how large-scale companies like google, meta, etc. with multiple servers handle the high traffic the record daily. A load-balancer acts as a mediator between the client and the web servers. It distributes incoming requests across the available servers to optimize performance, maintain high availability, and prevent overloading of any particular server. So, when connection established, load balancers route requests to web servers to fetch resources seamlessly. So we move to the next process.</p>

## PROCESS 5 (WEB SERVERS):
<p>After the load-balancer routes the request, it reaches one of the web servers hosting the Google website. The web server (such as Apache or Nginx) receives the request, processes it, and retrieves the appropriate files (HTML, CSS, JavaScript) required to generate the web page response.</p>

## PROCESS 6 (APPLICATION SERVERS):
<p>The web server may delegate certain tasks, such as dynamic content generation or database access, to the application server. The application server executes the necessary logic, interacts with databases or external services, and prepares the data to be sent back to the web server.</p>

## PROCESS 7 (DATABASE):
<p>For data-driven websites like Google, information is often stored in databases. The application server queries the database to retrieve relevant data based on the request. This data can include search results, user preferences, or any other dynamic content.</p>

## PROCESS 8 (RESPONSE GENERATION, TRANSMISSION, RENDERING):
<p>Once the application server has the required data, it generates an HTML response dynamically, incorporating the retrieved information. The response may also include other resources like images, stylesheets, or scripts referenced in the HTML. Then, the web server sends the generated response back to the client (browser) over the established TCP connection.</p>

<p>Upon receiving the response, the browser starts rendering the web page. It parses the HTML, fetches additional resources (CSS, JavaScript, images) referenced in the page, and displays the content in the appropriate layout on the user’s screen.</p>

## SUMMARY:
<p>The journey of a browser request, from typing a URL to displaying a web page, involves a complex interplay of various components. Understanding the roles of DNS, TCP/IP, Firewalls, HTTPS/SSL, Load-balancers, Web servers, Application servers, and Databases provides insight into the inner workings of the web stack.</p>

## DNS REQUEST > TCP/IP CONNECTION > FIREWALL CHECKS > HTTPS PARSING > LOAD BALANCING > WEB SERVERS >APPLICATION SERVERS > DATABASE > RESPONSE GENERATED, TRANSMITTED VIA TCP AND RENDERED BACK IN BROWSER

