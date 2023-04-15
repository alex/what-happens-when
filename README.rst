=====================================================================
What happens when you type https://www.google.com in your browser and press Enter.
=====================================================================


| Initial typing
========
Immediately we start typing our query string, “letter W”, our browser uses its algorithms to search if we have made such a query before. It goes through our history, bookmarks, cookies, and popular searches from the internet as a whole. As we continue typing the query string, the algorithms continue refining the suggestions presented to us based on the results from the searches digs from our history and from the internet. The most interesting thing is that it may even suggest google.com to us even before we finish typing it.


| Url or search term 
===========
In our case, https://www.google.com is a valid url.
⇒ In the case where the search term is not a valid URL, the browser feeds the search query into the default search engine(specialized software systems that search for data on the Web and present the results in a list or a line of results. They use special programs to find and index web pages and files. Google is the world's best and most popular search engine, followed by Bing). In many cases the URL has a special piece of text appended to it to tell the search engine that it came from a particular browser's URL bar.


| Convert non-ASCII Unicode characters in the hostname
================================
In our case, the hostname is google.com. This host name contains only ASCII Unicode characters (a-z, A-Z, 0-9, -, or .), it therefore doesn’t require any conventions.
⇒ In some cases, the hostname might contain some non-ASCII Unicode characters, and the browser might be forced to apply Punycode encoding to the hostname portion of the URL.


| Check HSTS list
=========
Since our query string is https://www.google.com, the protocol to be used in sending the requests is automatically HTTPS.
⇒ In some cases, the request protocol is not stated. In this case, the browser uses its preloaded list of websites that have requested to be contacted via HTTPS only, called HSTS (HTTP Strict Transport Security). If the website is in the list, the browser sends its request via HTTPS instead of HTTP. Otherwise, the initial request is sent via HTTP.
⇒ Note: a website can still use the HSTS policy without being in the HSTS list. The first HTTP request to the website by a user will receive a response requesting that the user only send HTTPS requests. However, this single HTTP request could potentially leave the user vulnerable to a downgrade attack, which is why the HSTS list is included in modern web browsers.


| DNS lookup
=======
Browser checks if the domain is in its cache. To do a manual DNS lookup on chrome, we can use; chrome://net-internals/#dns.
If not found, the browser calls gethostbyname library function - which retrieves host information corresponding to a host name from a host database, to do the lookup.
gethostbyname checks if the hostname can be resolved by reference in the local hosts file (whose location varies by OS) before trying to resolve the hostname through DNS.
In the case where the browser does not have it cached, not does the gethostbyname find it in the hosts file, it makes a request to the DNS server configured in the network stack(typically the local router or the ISP's caching DNS server).


| ARP Process
=======
Most of the computer programs/applications use logical address (IP address) to send/receive messages, however, the actual communication happens over the physical address (MAC address) i.e from layer 2 of the OSI model.
In order to send an ARP (Address Resolution Protocol) broadcast the network stack library needs the target IP address to lookup. It also needs to know the MAC address of the interface it will use to send out the ARP broadcast.
The ARP cache is first checked for an ARP entry for our target IP. If it is in the cache, the library function returns the result: Target IP = MAC.
If the entry is not in the ARP cache:
* The route table is looked up, to see if the Target IP address is on any of the subnets on the local route table. If it is, the library uses the interface associated with that subnet. If it is not, the library uses the interface that has the subnet of our default gateway.
* The MAC address of the selected network interface is looked up.
* The network library sends a Layer 2 (data link layer of the OSI model) ARP request:
ARP Request:
Sender MAC: interface:mac:address:here
Sender IP: interface.ip.goes.here
Target MAC: FF:FF:FF:FF:FF:FF (Broadcast)
Target IP: target.ip.goes.here
Depending on what type of hardware is between the computer and the router:
Directly connected:
If the computer is directly connected to the router the router immediately responds with an ARP Reply.


Hub:
If the computer is connected to a hub, the hub will broadcast the ARP request out of all other ports. If the router is connected on the same "wire", it will respond with an ARP Reply.


Switch:
If the computer is connected to a switch, the switch will check its local CAM/MAC table to see which port has the MAC address we are looking for. If   the switch has no entry for the MAC address it will rebroadcast the ARP   request to all other ports.


* If the switch has an entry in the MAC/CAM table it will send the ARP request to the port that has the MAC address we are looking for.
* If the router is on the same "wire", it will respond with an ARP Reply
ARP reply 
Sender MAC: target:mac:address:here
Sender IP: target.ip.goes.here
Target MAC: interface:mac:address:here
Target IP: interface.ip.goes.here
Now that the network library has the IP address of either our DNS server or the default gateway it can resume its DNS process:
* The DNS client establishes a socket to UDP port 53 on the DNS server, using a source port above 1023.
* If the response size is too large, TCP will be used instead.
* If the local/ISP DNS server does not have it, then a recursive search is requested and that flows up the list of DNS servers until the SOA is reached, and if found an answer is returned.


| TLS Handshake
=======
* The client computer sends a ClientHello message to the server with its Transport Layer Security (TLS) version, list of cipher algorithms and compression methods available.
* The server replies with a ServerHello message to the client with the TLS version, selected cipher, selected compression methods and the server's public certificate signed by a CA (Certificate Authority). The certificate contains a public key that will be used by the client to encrypt the rest of the handshake until a symmetric key can be agreed upon.
* The client verifies the server digital certificate against its list of trusted CAs. If trust can be established based on the CA, the client generates a string of pseudo-random bytes and encrypts this with the server's public key. These random bytes can be used to determine the symmetric key.
* The server decrypts the random bytes using its private key and uses these bytes to generate its own copy of the symmetric master key.  (s
* The client sends a Finished message to the server, encrypting a hash of the transmission up to this point with the symmetric key.
* The server generates its own hash, and then decrypts the client-sent hash to verify that it matches. If it does, it sends its own Finished message to the client, also encrypted with the symmetric key.
* From now on the TLS session transmits the application (HTTP) data encrypted with the agreed symmetric key.


| If a packet is dropped
===========
Sometimes, due to network congestion or flaky hardware connections, TLS packets will be dropped before they get to their final destination. The sender then has to decide how to react. The algorithm for this is called TCP congestion control. This varies depending on the sender; the most common algorithms are cubic on newer operating systems and New Reno on almost all others.
* Client chooses a congestion window based on the maximum segment size (MSS) of the connection.
* For each packet acknowledged, the window doubles in size until it reaches the 'slow-start threshold'. In some implementations, this threshold is adaptive.
* After reaching the slow-start threshold, the window increases additively for each packet acknowledged. If a packet is dropped, the window reduces exponentially until another packet is acknowledged.


| HTTP Protocol
===========
If the web browser used was written by Google, instead of sending an HTTP request to retrieve the page, it will send a request to try and negotiate with the server an "upgrade" from HTTP to the SPDY protocol.
If the client is using the HTTP protocol and does not support SPDY, it sends a request to the server of the form:
GET / HTTP/1.1
Host: google.com
Connection: close
[other headers]
where [other headers] refers to a series of colon-separated key-value pairs formatted as per the HTTP specification and separated by single newlines. (This assumes the web browser being used doesn't have any bugs violating the HTTP spec. This also assumes that the web browser is using HTTP/1.1, otherwise it may not include the Host header in the request and the version specified in the GET request will either be HTTP/1.0 or HTTP/0.9.)
HTTP/1.1 defines the "close" connection option for the sender to signal that the connection will be closed after completion of the response. For example,
Connection: close
HTTP/1.1 applications that do not support persistent connections MUST include the "close" connection option in every message.
After sending the request and headers, the web browser sends a single blank newline to the server indicating that the content of the request is done.


The server responds with a response code denoting the status of the request and responds with a response of the form::
    200 OK
    [response headers]
Followed by a single newline, and then sends a payload of the HTML content of ``www.google.com``. The server may then either close the connection, or if headers sent by the client requested it, keep the connection open to be reused for further requests.
If the HTTP headers sent by the web browser included sufficient information for the webserver to determine if the version of the file cached by the web browser has been unmodified since the last retrieval (ie. if the web browser included an ``ETag`` header), it may instead respond with a request of the form::
    304 Not Modified
    [response headers]
and no payload, and the web browser instead retrieves the HTML from its cache.
After parsing the HTML, the web browser (and server) repeats this process for every resource (image, CSS, favicon.ico, etc) referenced by the HTML page, except instead of ``GET / HTTP/1.1`` the request will be ``GET /$(URL relative to www.google.com) HTTP/1.1``.
If the HTML referenced a resource on a different domain than ``www.google.com``, the web browser goes back to the steps involved in resolving the other domain, and follows all steps up to this point for that domain. The ``Host`` header in the request will be set to the appropriate
server name instead of ``google.com``.


| HTTP Server Request Handle
=================
The HTTPD (HTTP Daemon) server is the one handling the requests/responses on the server-side. The most common HTTPD servers are Apache or nginx for Linux and IIS for Windows.
* The HTTPD (HTTP Daemon) receives the request.
* The server breaks down the request to the following parameters:
* HTTP Request Method (either GET, HEAD, POST, PUT, PATCH, DELETE, CONNECT, OPTIONS, or TRACE). In the case of a URL entered directly into the address bar, this will be GET.
* The domain name - in our case google.com
* Requested path/page, in our case - / (as no specific path/page was requested, / is the default path).
* The server verifies that there is a Virtual Host configured on the server that corresponds with our domain google.com, then it basically checks to verify that the same domain can accept Get requests.
* The server also verifies that the client is allowed to use this method (by IP,  authentication, etc.)
* If the server has a rewrite module installed (like mod_rewrite for Apache or URL Rewrite for IIS), it tries to match the request against one of the configured rules. If a matching rule is found, the server uses that rule to rewrite the request.
* The server goes to pull the content that corresponds with the request, in our case it will fall back to the index file, as "/" is the main file (some cases can override this, but this is the most common method).
* The server parses the file according to the handler. If Google is running on PHP, the server uses PHP to interpret the index file, and streams the output to the client.


| Behind the scenes of the Browser
=================
After the server has supplied all the resources(HTML, CSS, JS, images, etc.) sorted for by the browser, the browser undertakes two basic processes.
* Parsing - HTML, CSS, JS
⇒ In this part of the process, the rendering engine starts getting the contents of the requested document from the networking layer. This will usually be done in 8kB chunks.
⇒ The Html parser is usually tasked with the primary job of parsing the HTML markup into a parse tree.
⇒ HTML cannot be parsed using the regular top-down or bottom-up parsers due to the following reasons.
* The forgiving nature of the language.
* The fact that browsers have traditional error tolerance to support well known cases of invalid HTML.
* The parsing process is reentrant. For other languages, the source doesn't change during parsing, but in HTML, dynamic code (such as script elements containing `document.write()` calls) can add extra tokens, so the parsing process actually modifies the input.
⇒ Due to its inability to use the regular parsing techniques, the browser utilizes a custom
parser for parsing HTML. The parsing algorithm is described in detail by the HTML5 specification.
⇒ The algorithm consists of two stages: tokenization and tree construction.
⇒ The following are the main actions that are undertaken immediately parsing is completed;
* The browser begins fetching external resources linked to the page (CSS, images,
* JavaScript files, etc.).
* At this stage the browser marks the document as interactive and starts parsing scripts that are in "deferred" mode: those that should be executed after the document is parsed. The document state is set to "complete" and a "load" event is fired.
NOTE: there is never an "Invalid Syntax" error on an HTML page. Browsers fix
any invalid content and go on.
* The CSS interpretation takes place, where each CSS file is parsed into a StyleSheet object, where each object contains CSS rules with selectors and objects corresponding CSS grammar.
* Rendering  - Construct DOM Tree → Render Tree → Layout of Render Tree → Painting the render tree
The following are the main undertakings of the rendering process;
*  Create a 'Frame Tree' or 'Render Tree' by traversing the DOM nodes, and calculating the CSS style values for each node.
* Calculate the preferred width of each node in the 'Frame Tree' bottom-up by summing the preferred width of the child nodes and the node's horizontal margins, borders, and padding.
* Calculate the actual width of each node top-down by allocating each node's available width to its children.
* Calculate the height of each node bottom-up by applying text wrapping and summing the child node heights and the node's margins, borders, and padding.
* Calculate the coordinates of each node using the information calculated above.
* More complicated steps are taken when elements are ``floated``, positioned ``absolutely`` or ``relatively``, or other complex features are used. 
* Create layers to describe which parts of the page can be animated as a group without being re-rasterized. Each frame/render object is assigned to a layer.
* Textures are allocated for each layer of the page.
* The frame/render objects for each layer are traversed and drawing commands are executed for their respective layer. This may be rasterized by the CPU or drawn on the GPU directly using D2D/SkiaGL.
* All of the above steps may reuse calculated values from the last time the webpage was rendered, so that incremental changes require less work.
* The page layers are sent to the composting process where they are combined with layers for other visible content like the browser chrome, iframes and addon panels.
* Final layer positions are computed and the composite commands are issued via Direct3D/OpenGL. The GPU command buffer(s) are flushed to the GPU for asynchronous rendering and the frame is sent to the window server.
| GPU Rendering
=================
* During the rendering process the graphical computing layers can use general purpose “CPU” or the graphical processor “GPU” as well.
* When using “GPU” for graphical rendering computations the graphical software layers split the task into multiple pieces, so it can take advantage of “GPU” massive parallelism for float point calculations required for the rendering process.
| Post-rendering and user-induced execution
=================
After rendering has been completed, the browser executes JavaScript code as a result of some timing mechanism (such as a Google Doodle animation) or user interaction (typing a query into the search box and receiving suggestions). Plugins such as Flash or Java may execute as well, although not at this time on the Google homepage. Scripts can cause additional network requests to be performed, as well as modify the page or its layout, causing another round of page rendering and painting.