What happens when...
====================

This vault is an endeavor to answer the well-established inquiry question "What
happens when you type google.com into your program's location box and press
enter?"


The "g" key is hit
----------------------
The accompanying areas make sense of the actual console activities
also, the operating system interferes. At the point when you press the key "g" the program gets the
occasion and the auto-complete capabilities kick in.
Contingent upon your program's calculation and on the off chance that you are in
private/undercover mode or not different ideas will be introduced
to you in the dropdown beneath the URL bar. The majority of these calculations sort
also, focus on results in light of search history, bookmarks, treats, and
well-known looks from the web all in all. As you are composing
"google.com" many blocks of code run and the ideas will be refined
with each key press. It might try and propose "google.com" before you complete the process of composing
it.

The "enter" key bottoms out
---------------------------

To pick a zero point, we should pick the Enter key on the console stirring things up around town
lower part of its reach. Right now, an electrical circuit well defined for the enter
key is shut (either straightforwardly or capacitively). This permits a limited quantity of
current to stream into the rationale hardware of the console, which filters the state
of each key switch, debounces the electrical clamor of the fast irregular
conclusion of the switch, and converts it to a keycode number, for this situation 13.
The console regulator then, at that point, and encodes the keycode for transport to the PC.
This is currently generally over an All inclusive Sequential Transport (USB) or Bluetooth
association, yet generally has been over PS/2 or ADB associations.

*On account of the USB keyboard:*

- The USB hardware of the console is fueled by the 5V inventory gave over
  pin 1 from the PC's USB have regulator.

- The keycode created is put away by inward console hardware memory in a
  register called "endpoint".

- The host USB regulator surveys that "endpoint" each ~10ms (least worth
  pronounced by the console), so it gets the keycode esteem put away on it.

- This worth goes to the USB SIE (Sequential Connection point Motor) to be changed over in
  at least one USB bundles that follow the low-level USB convention.

- Those bundles are conveyed by a differential electrical message over D+ and D-
  pins (the center 2) at a most extreme speed of 1.5 Mb/s, as a Stowed away
  (Human Connection point Gadget) gadget is constantly pronounced to be a "low-speed gadget"
  (USB 2.0 consistence).

- This sequential sign is then decoded at the PC's host USB regulator, and
  deciphered by the PC's Human Point of interaction Gadget (Stowed away) all inclusive console
  gadget driver. The worth of the key is then passed into the working
  framework's equipment reflection layer.

*On account of Virtual Console (as in touch screen devices):*

- At the point when the client places a cutting edge capacitive touch screen, a
  minuscule measure of current gets moved to the finger. This finishes the
  circuit through the electrostatic field of the conductive layer and
  makes a voltage drop by then on the screen. The
  ''screen regulator'' then, at that point, raises a hinder revealing the direction of
  the keypress.

- Then the versatile operating system informs the at present centered use of a press occasion
  in one of its GUI components (which presently is the virtual console application
  buttons).

- The virtual console can now raise a product hinder for sending a
  'key squeezed' message back to the operating system.

- This hinder tells the right now centered utilization of a 'key squeezed'
  occasion.

Hinder fires [NOT for USB keyboards]
---------------------------------------

The console conveys messages on its hinder demand line (IRQ), which is planned
to an ''hinder vector'' (number) by the hinder regulator. The central processor utilizes
the ''Interfere with Descriptor Table'' (IDT) to plan the intrude on vectors to
capabilities (''hinder overseers'') which are provided by the piece. When an
hinder shows up, the computer processor lists the IDT with the intrude on vector and runs
the fitting overseer. Hence, the part is placed.

(On Windows) A ''WM_KEYDOWN'' message is shipped off the application
--------------------------------------------------------

The Concealed vehicle passes the critical down occasion to the ''KBDHID.sys'' driver which
changes over the Concealed utilization into a scancode. For this situation, the output code is
''VK_RETURN'' (''0x0D''). The ''KBDHID.sys'' driver communicates with the
''KBDCLASS.sys'' (console class driver). This driver is liable for
dealing with all console and keypad input in a solid way. It then, at that point, calls into
''Win32K.sys'' (after possibly going the message through outsider
console channels that are introduced). This all occurs in part mode.

''Win32K.sys'' sorts out what window is the dynamic window through the
''GetForegroundWindow()'' Programming interface. This Programming interface gives the window handle of the
program's location box. The fundamental Windows "message siphon" then calls
''SendMessage(hWnd, WM_KEYDOWN, VK_RETURN, lParam)''. ''lParam'' is a bitmask
that shows additional data about the keypress: rehash count (0 in this
case), the genuine sweep code (can be OEM subordinate, however by and large wouldn't be
for ''VK_RETURN''), whether broadened keys (for example alt, shift, ctrl) were too
squeezed (they weren't), and another state.

The Windows ''SendMessage'' Programming interface is a direct capability that
adds the message to a line for the specific window handle (''hWnd'').
Afterward, the principal message handling capability (called a ''WindowProc'') doled out
to the ''hWnd'' is brought to deal with each message in the line.

The window (''hWnd'') that is dynamic is really an alter control and the
''WindowProc'' for this situation has a message controller for ''WM_KEYDOWN'' messages.
This code searches inside the third boundary that was passed to ''SendMessage''
(''wParam'') and, in light of the fact that it is ''VK_RETURN'' realizes the client has raised a ruckus around town
key.

(On operating system X) A ''KeyDown'' NSEvent is shipped off the application
--------------------------------------------------

The hinder signal triggers a hinder occasion in the I/O Pack kext console
driver. The driver makes an interpretation of the sign into a key code which is passed to the
Operating system X ''WindowServer'' process. Resultantly, the ''WindowServer'' dispatches an
occasion to any proper (for example dynamic or tuning in) applications through their
Mach port where it is set into an occasion line. Occasions can then be perused from
this line by strings with adequate honors calling the
''mach_ipc_dispatch'' capability. This most normally happens through, and is
dealt with by, an ''NSApplication'' headliner circle, through an ''NSEvent'' of
''NSEventType'' ''KeyDown''.

(On GNU/Linux) the Xorg server tunes in for keycodes
---------------------------------------------------

When a graphical ''X server'' is utilized, ''X'' will utilize the nonexclusive occasion
driver ''evdev'' to get the keypress. A re-planning of keycodes to scancodes
is made with ''X server'' explicit keymaps and rules.
When the scancode planning of the key squeezed is finished, the ''X server''
sends the person to the ''window director'' (DWM, metacity, i3, and so on), so the
''window director'' thus sends the person to the engaged window.
The graphical Programming interface of the window that gets the person prints the
suitable textual style image in the fitting centered field.

Parse URL
---------

* The program presently has the accompanying data contained in the URL (Uniform
  Asset Finder):

    - ''Convention'' "http"
        Use 'Hyper Text Move Convention'

    - ''Asset'' "/"
        Recover primary (record) page

Is it a URL or an inquiry term?
-----------------------------

At the point when no convention or legitimate area name is given the program continues to take care of
the text given in the location box to the program's default web search tool.
As a rule the URL has a unique piece of text affixed to it to tell the
web crawler that it came from a specific program's URL bar.

Convert non-ASCII Unicode characters in the hostname
------------------------------------------------

* The program checks the hostname for characters that are not in ''a-z'',
  ''A-Z'', ''0-9'', ''- '', or ''.''.
* Since the hostname is ''google.com'' there will not be any, however in the event that there were
  the program would apply 'Punycode'_ encoding to the hostname part of the
  URL.

Check HSTS list
---------------
* The program really looks at its "preloaded HSTS (HTTP Severe Vehicle Security)"
  list. This is a rundown of sites that have mentioned to be reached through
  HTTPS as it were.
* In the event that the site is in the rundown, the program sends its solicitation through HTTPS
  rather than HTTP. In any case, the underlying solicitation is sent through HTTP.
  (Note that a site can in any case utilize the HSTS strategy *without* being in the
  HSTS list. The main HTTP solicitation to the site by a client will get a
  reaction mentioning that the client just send HTTPS demands. Be that as it may, this
  single HTTP solicitation might actually leave the client helpless against a
  'minimize attack'_, which is the reason the HSTS list is remembered for present day web
  programs.)

DNS lookup
----------

* Browser  checks assuming the space is in its store. (to see the DNS Reserve in
  Chrome, go to 'chrome://net-internals/#dns <chrome://net-internals/#dns>'_).
* In the event that not found, the program calls ''gethostbyname'' library capability (changes by
  Operating system) to do the query.
* ''gethostbyname'' checks if the hostname can be settled by reference in the
  neighborhood ''has'' document (whose area 'shifts by OS'_) prior to attempting to
  resolve the hostname through DNS.
* On the off chance that ''gethostbyname'' doesn't have it reserved nor can track down it in the ''has''
  document then it makes a solicitation to the DNS server designed in the organization
  stack. This is commonly the neighborhood switch or the ISP's storing DNS server.
* Assuming that the DNS server is on the equivalent subnet the organization library follows the
  ''ARP process'' beneath for the DNS server.
* In the event that the DNS server is on an alternate subnet, the organization library follows
  the ''ARP process'' beneath for the default passage IP.


ARP process
-----------
To send an ARP (Address Resolution Protocol) broadcast the network
stack library needs the objective IP address to query. It likewise has to know the
MAC address of the point of interaction it will use to convey the ARP broadcast.

The ARP cache is first checked for an ARP section for our target IP. On the off chance that it is in
the ca che, the library capability returns the outcome: Target IP = MAC.

In the event that the passage isn't in the ARP cache:

* The route table is turned upward, to check whether the target IP address is on any of
  the subnets on the nearby route table. Assuming it is, the library utilizes the
  interface related with that subnet. In the event that it isn't, the library utilizes the
  interface that has the subnet of our default gateway.

* The MAC address of the chosen network connection interface is looked up.

* The network library sends a Layer 2 (information interface layer of the 'OSI model.
  ARP demand:

``ARP Request``::

    Sender MAC: interface:mac:address:here
    Sender IP: interface.ip.goes.here
    Target MAC: FF:FF:FF:FF:FF:FF (Broadcast)
    Target IP: target.ip.goes.here

Depending on what type of hardware is between the computer and the router:

Directly connected:

* If the computer is directly connected to the router the router response
  with an ``ARP Reply`` (see below)

Hub:

* If the computer is connected to a hub, the hub will broadcast the ARP
  request out of all other ports. If the router is connected on the same "wire",
  it will respond with an ``ARP Reply`` (see below).

Switch:

* If the computer is connected to a switch, the switch will check its local
  CAM/MAC table to see which port has the MAC address we are looking for. If
  the switch has no entry for the MAC address it will rebroadcast the ARP
  request to all other ports.

* If the switch has an entry in the MAC/CAM table it will send the ARP request
  to the port that has the MAC address we are looking for.

* If the router is on the same "wire", it will respond with an ``ARP Reply``
  (see below)

``ARP Reply``::

    Sender MAC: target:mac:address:here
    Sender IP: target.ip.goes.here
    Target MAC: interface:mac:address:here
    Target IP: interface.ip.goes.here

Now that the network library has the IP address of either our DNS server or
the default gateway it can resume its DNS process:

* The DNS client establishes a socket to UDP port 53 on the DNS server,
  using a source port above 1023.
* If the response size is too large, TCP will be used instead.
* If the local/ISP DNS server does not have it, then a recursive search is
  requested and that flows up the list of DNS servers until the SOA is reached,
  and if found an answer is returned.

Opening of a socket
-------------------
Once the browser receives the IP address of the destination server, it takes
that and the given port number from the URL (the HTTP protocol defaults to port
80, and HTTPS to port 443), and makes a call to the system library function
named ``socket`` and requests a TCP socket stream - ``AF_INET/AF_INET6`` and
``SOCK_STREAM``.

* This request is first passed to the Transport Layer where a TCP segment is
  crafted. The destination port is added to the header, and a source port is
  chosen from within the kernel's dynamic port range (ip_local_port_range in
  Linux).
* This segment is sent to the Network Layer, which wraps an additional IP
  header. The IP address of the destination server as well as that of the
  current machine is inserted to form a packet.
* The packet next arrives at the Link Layer. A frame header is added that
  includes the MAC address of the machine's NIC as well as the MAC address of
  the gateway (local router). As before, if the kernel does not know the MAC
  address of the gateway, it must broadcast an ARP query to find it.

At this point the packet is ready to be transmitted through either:

* `Ethernet`_
* `WiFi`_
* `Cellular data network`_

For most home or small business Internet connections the packet will pass from
your computer, possibly through a local network, and then through a modem
(MOdulator/DEModulator) which converts digital 1's and 0's into an analog
signal suitable for transmission over telephone, cable, or wireless telephony
connections. On the other end of the connection is another modem which converts
the analog signal back into digital data to be processed by the next `network
node`_ where the from and to addresses would be analyzed further.

Most larger businesses and some newer residential connections will have fiber
or direct Ethernet connections in which case the data remains digital and
is passed directly to the next `network node`_ for processing.

Eventually, the packet will reach the router managing the local subnet. From
there, it will continue to travel to the autonomous system's (AS) border
routers, other ASes, and finally to the destination server. Each router along
the way extracts the destination address from the IP header and routes it to
the appropriate next hop. The time to live (TTL) field in the IP header is
decremented by one for each router that passes. The packet will be dropped if
the TTL field reaches zero or if the current router has no space in its queue
(perhaps due to network congestion).

This send and receive happens multiple times following the TCP connection flow:

* Client chooses an initial sequence number (ISN) and sends the packet to the
  server with the SYN bit set to indicate it is setting the ISN
* Server receives SYN and if it's in an agreeable mood:
   * Server chooses its own initial sequence number
   * Server sets SYN to indicate it is choosing its ISN
   * Server copies the (client ISN +1) to its ACK field and adds the ACK flag
     to indicate it is acknowledging receipt of the first packet
* Client acknowledges the connection by sending a packet:
   * Increases its own sequence number
   * Increases the receiver acknowledgment number
   * Sets ACK field
* Data is transferred as follows:
   * As one side sends N data bytes, it increases its SEQ by that number
   * When the other side acknowledges receipt of that packet (or a string of
     packets), it sends an ACK packet with the ACK value equal to the last
     received sequence from the other
* To close the connection:
   * The closer sends a FIN packet
   * The other sides ACKs the FIN packet and sends its own FIN
   * The closer acknowledges the other side's FIN with an ACK

TLS handshake
-------------
* The client computer sends a ``ClientHello`` message to the server with its
  Transport Layer Security (TLS) version, list of cipher algorithms and
  compression methods available.

* The server replies with a ``ServerHello`` message to the client with the
  TLS version, selected cipher, selected compression methods and the server's
  public certificate signed by a CA (Certificate Authority). The certificate
  contains a public key that will be used by the client to encrypt the rest of
  the handshake until a symmetric key can be agreed upon.

* The client verifies the server digital certificate against its list of
  trusted CAs. If trust can be established based on the CA, the client
  generates a string of pseudo-random bytes and encrypts this with the server's
  public key. These random bytes can be used to determine the symmetric key.

* The server decrypts the random bytes using its private key and uses these
  bytes to generate its own copy of the symmetric master key.

* The client sends a ``Finished`` message to the server, encrypting a hash of
  the transmission up to this point with the symmetric key.

* The server generates its own hash, and then decrypts the client-sent hash
  to verify that it matches. If it does, it sends its own ``Finished`` message
  to the client, also encrypted with the symmetric key.

* From now on the TLS session transmits the application (HTTP) data encrypted
  with the agreed symmetric key.

If a packet is dropped
----------------------

Sometimes, due to network congestion or flaky hardware connections, TLS packets
will be dropped before they get to their final destination. The sender then has
to decide how to react. The algorithm for this is called `TCP congestion
control`_. This varies depending on the sender; the most common algorithms are
`cubic`_ on newer operating systems and `New Reno`_ on almost all others.

* Client chooses a `congestion window`_ based on the `maximum segment size`_
  (MSS) of the connection.
* For each packet acknowledged, the window doubles in size until it reaches the
  'slow-start threshold'. In some implementations, this threshold is adaptive.
* After reaching the slow-start threshold, the window increases additively for
  each packet acknowledged. If a packet is dropped, the window reduces
  exponentially until another packet is acknowledged.

HTTP protocol
-------------

If the web browser used was written by Google, instead of sending an HTTP
request to retrieve the page, it will send a request to try and negotiate with
the server an "upgrade" from HTTP to the SPDY protocol.

If the client is using the HTTP protocol and does not support SPDY, it sends a
request to the server of the form::

    GET / HTTP/1.1
    Host: google.com
    Connection: close
    [other headers]

where ``[other headers]`` refers to a series of colon-separated key-value pairs
formatted as per the HTTP specification and separated by single newlines.
(This assumes the web browser being used doesn't have any bugs violating the
HTTP spec. This also assumes that the web browser is using ``HTTP/1.1``,
otherwise it may not include the ``Host`` header in the request and the version
specified in the ``GET`` request will either be ``HTTP/1.0`` or ``HTTP/0.9``.)

HTTP/1.1 defines the "close" connection option for the sender to signal that
the connection will be closed after completion of the response. For example,

    Connection: close

HTTP/1.1 applications that do not support persistent connections MUST include
the "close" connection option in every message.

After sending the request and headers, the web browser sends a single blank
newline to the server indicating that the content of the request is done.

The server responds with a response code denoting the status of the request and
responds with a response of the form::

    200 OK
    [response headers]

Followed by a single newline, and then sends a payload of the HTML content of
``www.google.com``. The server may then either close the connection, or if
headers sent by the client requested it, keep the connection open to be reused
for further requests.

If the HTTP headers sent by the web browser included sufficient information for
the webserver to determine if the version of the file cached by the web
browser has been unmodified since the last retrieval (ie. if the web browser
included an ``ETag`` header), it may instead respond with a request of
the form::

    304 Not Modified
    [response headers]

and no payload, and the web browser instead retrieve the HTML from its cache.

After parsing the HTML, the web browser (and server) repeats this process
for every resource (image, CSS, favicon.ico, etc) referenced by the HTML page,
except instead of ``GET / HTTP/1.1`` the request will be
``GET /$(URL relative to www.google.com) HTTP/1.1``.

If the HTML referenced a resource on a different domain than
``www.google.com``, the web browser goes back to the steps involved in
resolving the other domain, and follows all steps up to this point for that
domain. The ``Host`` header in the request will be set to the appropriate
server name instead of ``google.com``.

HTTP Server Request Handle
--------------------------
The HTTPD (HTTP Daemon) server is the one handling the requests/responses on
the server-side. The most common HTTPD servers are Apache or nginx for Linux
and IIS for Windows.

* The HTTPD (HTTP Daemon) receives the request.
* The server breaks down the request to the following parameters:
   * HTTP Request Method (either ``GET``, ``HEAD``, ``POST``, ``PUT``,
     ``PATCH``, ``DELETE``, ``CONNECT``, ``OPTIONS``, or ``TRACE``). In the
     case of a URL entered directly into the address bar, this will be ``GET``.
   * Domain, in this case - google.com.
   * Requested path/page, in this case - / (as no specific path/page was
     requested, / is the default path).
* The server confirms that there is a Virtual Host designed on the server
  that compares with google.com.
* The server confirms that google.com can acknowledge GET demands.
* The server checks that the client is permitted to utilize this technique
  (by IP, authentication, and so on.).
* In the event that the server has a rewrite module installed (like mod_rewrite for Apache or
  URL Rewrite for IIS), it attempts to match the solicitation against one of the
  arranged rules. Assuming a matching standard is found, the server utilizes that standard to
  change the solicitation.
* The server goes to pull the substance that relates with the solicitation,
  for our situation it will fall back to the list record, as "/" is the principal document
  (a few cases can supersede this, yet this is the most widely recognized technique).
* The server parses the file as per the handler. If Google
  is running on PHP, the server utilizes PHP to decipher the index file, and
  streams the result to the client.

Behind the scenes of the Browser
----------------------------------

Once the server supplies the resources (HTML, CSS, JS, images, etc.)
to the browser it undergoes the below process:

* Parsing - HTML, CSS, JS
* Rendering - Construct DOM Tree → Render Tree → Layout of Render Tree →
  Painting the render tree

Browser
-------

The browser's usefulness is to introduce the web asset you pick, by
mentioning it from the server and showing it in the browser window.
The asset is normally a HTML document, however, may likewise be a PDF,
picture, or another sort of content. The area of the asset is
determined by the client utilizing a URI (Uniform Resource Identifier).

The manner in which the browser deciphers and shows HTML files  is determined
in the HTML and CSS details. These particulars are kept up with
by the W3C (World Wide Web Consortium) association, which is the
principles association for the web.

Browser UIs share a great deal for all intents and purpose with one another. Among the
normal UI components are:

* A address bar for inserting  a URI
* Back and forward buttons
* Bookmarking choices
* Revive and stop buttons for invigorating or halting the stacking of
  current documents
* Home button that takes you to your landing page

**Browser High-Level Structure**

The components of the browsers are:

* **Client interface:** The UI incorporates the address bar,
  back/forward button, bookmarking menu, and so forth. All aspects of the browser
  show aside from the window where you see the mentioned page.
* **Browser engine:** The program motor marshals activities between the UI
  what's more, the rendering engine.
* **Rendering engine:** The rendering motor is answerable for showing
  mentioned content. For instance in the event that the mentioned content is HTML, the
  rendering engine parses HTML and CSS, and shows the parsed content on
  the screen.
* **Networking:** The networking handles network calls, for example, HTTP requests,
  involving various executions for various stages behind a
  stage free point of interaction.
* **UI backend:** The UI backend is utilized for drawing essential gadgets like combo
  boxes and windows. This backend uncovered a nonexclusive connection point that isn't
  stage explicit.
  Under it, it utilizes working framework UI techniques.
* **JavaScript engine:** The JavaScript engine is utilized to parse and
  execute JavaScript code.
* **Data storage:** The information stockpiling is a persistence layer. The browser may
  need to save a wide range of information locally, like cookies. Browsers too
  support storage mechanisms like localStorage, IndexedDB, WebSQL and
  FileSystem.

HTML parsing
------------
The rendering engine turns over getting the items in the mentioned
document from the networking administration layer. This will ordinarily be finished in 8kB lumps.

The essential occupation of the HTML parser is to parse the HTML markup into a parse tree.

The resulting tree (the "parse tree") is a tree of DOM components and attribute
nodes. DOM is short for  Document Object Model. It is the object show
of the HTML document and the point of interaction of HTML components to the rest of the world
like JavaScript. The foundation of the tree is the "Document" object. Preceding
any manipulation by means of scripting, the DOM has a very nearly coordinated connection to
the markup.
**The parsing algorithm**

HTML can't be parsed utilizing the customary hierarchical or base up parsers.

The reasons are:

* The easygoing nature of the language.
* The way that browsers have conventional mistake resilience to help well
  known instances of invalid HTML.
* The parsing process is reentrant. For different dialects, the source doesn't
  change during parsing, however in HTML, dynamic code (like script components
  containing 'document.write()' calls) can add additional tokens, so the parsing
  process really changes the input.

Incapable to utilize the normal parsing procedures, the browser uses a custom
parser for parsing HTML. The parsing calculation is depicted in
detail by the HTML5 determination.

The calculation comprises of two phases: tokenization and tree development.
**Actions when the parsing is finished**

The browser starts getting outer assets connected to the page (CSS, pictures,
JavaScript records, and so forth.).

At this stage, the browser denotes the document as intuitive and begins
parsing scripts that are in "deferred" mode: those that ought to be
executed after the document is parsed. The document state is
set to "complete" and a "heap" occasion is fired.

Note there will never be an "Invalid Syntax" mistake on an HTML page. Browsers fix
any invalid substance and go on.

CSS interpretation
------------------

* Parse CSS documents, ''<style>'' label items, and ''style'' characteristic
  values utilizing '"CSS lexical and syntax grammar"'_
* Each CSS document is parsed into a ''StyleSheet object'', where each item
  contains CSS rules with selectors and items comparing CSS syntax.
* A CSS parser can be hierarchical or base up when a particular parser generator
  is utilized.
Page Rendering
--------------

* Make a 'Frame Tree' or 'Render Tree' by crossing the DOM hubs, and
  working out the CSS style values for every node.
* Work out the favored width of every nodein the 'Frame Tree' bottom-up
  by adding the favored width of the child nodes and the node's
  horizontal margins, borders, and padding.
* Work out the genuine width of every node hierarchical by assigning every node's
  accessible width to its children.
* Work out the the height of each node bottom-up by applying text wrapping and
  summing the child node heights and the node's margins, borders, and padding.
* Work out the coordinates of every node utilizing the information  determined
  above.
* More complicated steps are taken when elements are ``floated``,
  positioned ``absolutely`` or ``relatively``, or other complex features
  are used.
* Make layers to portray what parts of the page can be animated collectively
  without being re-rasterized. Each frame/render object is doled out to a layer.
* Textures are designated for each layer of the page.
* The frame/render objects for each layer are navigated and drawing orders
  are executed for their particular layer. This might be rasterized by the central processing unit
  or on the other hand drawn on the GPU straightforwardly utilizing D2D/SkiaGL.
* Every one of the above advances might reuse determined values from the last time the
  website page was rendered, so gradual changes require less work.
* The page layers are shipped off the process system where they are consolidated
  with layers for other apparent substance like the browser chrome, iframes
  furthermore, addon panels.
* Last layer positions are computed and the composite orders are given
  through Direct3D/OpenGL. The GPU order buffer(s) are flushed to the GPU for
  nonconcurrent rendering and the frame is shipped off the window server.

GPU Rendering
-------------

* During the rendering process the graphical computing layers can utilize general
  purpose ''CPU'' or the graphical processor ''GPU'' also.

* While utilizing ''GPU'' for graphical rendering calculations the graphical
  software layers split the assignment into numerous pieces, so it can make use
  of ''GPU'' enormous parallelism for float point computations expected for
  the rendering system.


Window Server
-------------

Post-rendering and user-induced execution
-----------------------------------------

Subsequent to rendering has been finished, the program executes JavaScript code thus
of some timing instrument, (for example, a Google Doodle animation) or client
interaction (composing a question into the inquiry box and getting ideas).
Plugins, for example, Blaze or Java might execute too, albeit not right now on
the Google landing page. Scripts can cause extra network solicitations to be
performed, as well as adjust the page or its design, causing one more round of
page rendering and painting.

.. _`Creative Commons Zero`: https://creativecommons.org/publicdomain/zero/1.0/
.. _`"CSS lexical and syntax grammar"`: http://www.w3.org/TR/CSS2/grammar.html
.. _`Punycode`: https://en.wikipedia.org/wiki/Punycode
.. _`Ethernet`: http://en.wikipedia.org/wiki/IEEE_802.3
.. _`WiFi`: https://en.wikipedia.org/wiki/IEEE_802.11
.. _`Cellular data network`: https://en.wikipedia.org/wiki/Cellular_data_communication_protocol
.. _`analog-to-digital converter`: https://en.wikipedia.org/wiki/Analog-to-digital_converter
.. _`network node`: https://en.wikipedia.org/wiki/Computer_network#Network_nodes
.. _`TCP congestion control`: https://en.wikipedia.org/wiki/TCP_congestion_control
.. _`cubic`: https://en.wikipedia.org/wiki/CUBIC_TCP
.. _`New Reno`: https://en.wikipedia.org/wiki/TCP_congestion_control#TCP_New_Reno
.. _`congestion window`: https://en.wikipedia.org/wiki/TCP_congestion_control#Congestion_window
.. _`maximum segment size`: https://en.wikipedia.org/wiki/Maximum_segment_size
.. _`varies by OS` : https://en.wikipedia.org/wiki/Hosts_%28file%29#Location_in_the_file_system
.. _`简体中文`: https://github.com/skyline75489/what-happens-when-zh_CN
.. _`한국어`: https://github.com/SantonyChoi/what-happens-when-KR
.. _`日本語`: https://github.com/tettttsuo/what-happens-when-JA
.. _`downgrade attack`: http://en.wikipedia.org/wiki/SSL_stripping
.. _`OSI Model`: https://en.wikipedia.org/wiki/OSI_model
.. _`Spanish`: https://github.com/gonzaleztroyano/what-happens-when-ES
