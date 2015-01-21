What happens when...
====================

This repository is an attempt to answer the age old interview question "What
happens when you type google.com into your browser's address box and press
enter?"

Except instead of the usual story, we're going to try to answer this question
in as much detail as possible. No skipping out on anything.

This is a collaborative process, so dig in and try to help out! There's tons of
details missing, just waiting for you to add them! So send us a pull request,
please!

This is all licensed under the terms of the `Creative Commons Zero`_ license.

Table of Contents
====================

.. contents::
   :backlinks: none
   :local:

The "enter" key bottoms out
---------------------------

To pick a zero point, let's choose the enter key on the keyboard hitting the
bottom of its range. At this point, an electrical circuit specific to the enter
key is closed (either directly or capacitively). This allows a small amount of
current to flow into the logic circuitry of the keyboard, which scans the state
of each key switch, debounces the electrical noise of the rapid intermittent
closure of the switch, and converts it to a keycode integer, in this case 13.
The keyboard controller then encodes the keycode for transport to the computer.
This is now almost universally over a Universal Serial Bus (USB) or Bluetooth
connection, but historically has been over PS/2 or ADB connections.

*In the case of the USB keyboard:*

- The USB circuitry of the keyboard is powered by the 5V supply provided over
   pin 1 from the computer's USB host controller.

- The keycode generated is stored by internal keyboard circuitry memory in a
  register called "endpoint".

- The host USB controller polls that "endpoint" every ~10ms (minimum value
  declared by the keyboard), so it gets the keycode value stored on it.

- This value goes to the USB SIE (Serial Interface Engine) to be converted in
  one or more USB packets that follows the low level USB protocol.

- Those packets are sent by a differential electrical signal over D+ and D-
  pins (the middle 2) at a maximum speed of 1.5 Mb/s, as an HID
  (Human Interface Device) device is always declared to be a "low speed device"
  (USB 2.0 compliance).

- This serial signal is then decoded at the computer's host USB controller, and
  interpreted by the computer's Human Interface Device (HID) universal keyboard
  device driver.  The value of the key is then passed into the operating
  system's hardware abstraction layer.

*In the case of Virtual Keyboard (as in touch screen devices):*

- In modern capacitive touch screens when the user puts his finger on the
  screen a tiny amount of current from the electrostatic field of the
  conductive layer gets transferred to the finger completing the circuit
  and creating a voltage dropping at that point on the screen so that the
  ``screen controller`` raises an interrupt reporting the coordinate of
  the 'click'.

- Then the mobile OS notifies the current focused application of a click event
  in one of its GUI elements (which now is the virtual keyboard application
  buttons).

- The virtual keyboard can now raise a software interrupt for sending a
  'key pressed' message back to the OS.

- Which in turn notifies the current focused application of a 'key pressed'
  event.


Interrupt fires [NOT for USB keyboards]
---------------------------------------

The keyboard sends signals on its interrupt request line (IRQ), which is mapped
to an ``interrupt vector`` (integer) by the interrupt controller. The CPU uses
the ``Interrupt Descriptor Table`` (IDT) to map the interrupt vectors to
functions (``interrupt handlers``) which are supplied by the kernel. When an
interrupt arrives, the CPU indexes the IDT with the interrupt vector and runs
the appropriate handler. Thus, the kernel is entered.

(On Windows) A ``WM_KEYDOWN`` message is sent to the app
--------------------------------------------------------

The HID transport passes the key down event to the ``KBDHID.sys`` driver which
converts the HID usage into a scancode. In this case the scan code is
``VK_RETURN`` (``0x0D``). The ``KBDHID.sys`` driver interfaces with the
``KBDCLASS.sys`` (keyboard class driver). This driver is responsible for
handling all keyboard and keypad input in a secure manner. It then calls into
``Win32K.sys`` (after potentially passing the message through 3rd party
keyboard filters that are installed). This all happens in kernel mode.

``Win32K.sys`` figures out what window is the active window through the
``GetForegroundWindow()`` API. This API provides the window handle of the
browser's address box. The main Windows "message pump" then calls
``SendMessage(hWnd, WM_KEYDOWN, VK_RETURN, lParam)``. ``lParam`` is a bitmask
that indicates further information about the keypress: repeat count (0 in this
case), the actual scan code (can be OEM dependent, but generally wouldn't be
for ``VK_RETURN``), whether extended keys (e.g. alt, shift, ctrl) were also
pressed (they weren't), and some other state.

The Windows ``SendMessage`` API is a straightforward function that
adds the message to a queue for the particular window handle (``hWnd``).
Later, the main message processing function (called a ``WindowProc``) assigned
to the ``hWnd`` is called in order to process each message in the queue.

The window (``hWnd``) that is active is actually an edit control and the
``WindowProc`` in this case has a message handler for ``WM_KEYDOWN`` messages.
This code looks within the 3rd parameter that was passed to ``SendMessage``
(``wParam``) and, because it is ``VK_RETURN`` knows the user has hit the ENTER
key.

(On OS X) A ``KeyDown`` NSEvent is sent to the app
--------------------------------------------------

The interrupt signal triggers an interrupt event in the I/O Kit kext keyboard
driver. The driver translates the signal into a key code which is passed to the
OS X ``WindowServer`` process. Resultantly, the ``WindowServer`` dispatches an
event to any appropriate (e.g. active or listening) applications through their
Mach port where it is placed into an event queue. Events can then be read from
this queue by threads with sufficient privileges calling the
``mach_ipc_dispatch`` function. This most commonly occurs through, and is
handled by, an ``NSApplication`` main event loop, via an ``NSEvent`` of
``NSEventType`` ``KeyDown``.

(On GNU/Linux) the Xorg server listens for keycodes
---------------------------------------------------

When a graphical ``X server`` is used, ``X`` will use the generic event
driver ``evdev`` to acquire the keypress. A re-mapping of keycodes to scancodes
is made with ``X server`` specific keymaps and rules.
When the scancode mapping of the key pressed is complete, the ``X server``
sends the character to the ``window manager`` (DWM, metacity, i3, etc), so the
``window manager`` in turn sends the character to the focused window.
The graphical API of the window  that receives the character prints the
appropriate font symbol in the appropriate focused field.


Parse URL
---------

* The browser now has the following information contained in the URL (Uniform
  Resource Locator):

    - ``Protocol``  "http"
        Use 'Hyper Text Transfer Protocol'

    - ``Resource``  "/"
        Retrieve main (index) page


Is it a URL or a search term?
-----------------------------

When no protocol or valid domain name is given the browser proceeds to feed
the text given in the address box to the browser's default web search engine.


Check HSTS list...
------------------

Convert non-ASCII Unicode characters in hostname
------------------------------------------------

* The browser checks the hostname for characters that are not in ``a-z``,
  ``A-Z``, ``0-9``, ``-``, or ``.``.
* Since the hostname is ``google.com`` there won't be any, but if there were
  the browser would apply `Punycode`_ encoding to the hostname portion of the
  URL.

DNS lookup...
-------------

* Browser checks if the domain is in its cache.
* If not found, calls ``gethostbyname`` library function (varies by OS) to do
  the lookup.
* ``gethostbyname`` checks if the hostname can be resolved by looking in the
  ``/etc/hosts`` file, before trying to resolve the hostname through DNS.
* If ``gethostbyname`` does not have it cached nor in the ``hosts`` file then a
  request is made to the known DNS server that was given to the network stack.
  This is typically the local router or the ISP's caching DNS server.

* The local DNS server is looked up.

* If the DNS server is on the same subnet the ARP cache is checked for an ARP
  entry for the DNS server. If there is no entry in the ARP cache we do the
  ``ARP process`` (see below) for the DNS server. If there is an entry in the
  ARP cache, we get the information: DNS.server.ip.address = dns:mac:address

* If the DNS server is on a different subnet, we check the ARP cache for the
  default gateway IP. If we do not have an entry in the ARP cache we do the
  ``ARP process`` (see below) for the default gateway IP. If we have an entry
  in the ARP cache, we get the information:
  default.gateway.ip.address = gateway:mac:address


ARP process
-----------
In order to send an ARP broadcast we need to have a Target IP address we want
to look up. We also need to know the MAC address of the interface we are going
to use to send out the ARP broadcast.

* The ARP cache is checked for an ARP entry for our target IP. If it's in the
  cache, we return the result: Target IP = MAC.

If the entry is not in the ARP cache:

* The route table is looked up, to see if the Target IP address is on any of
  the subnets on the local route table. If it is, we use the interface
  associated with that subnet. If it is not, we use the interface that has the
  subnet of our default gateway.

* The MAC address of the selected network interface is looked up.

* We send a Layer 2 ARP request:

``ARP Request``::

    Sender MAC: interface:mac:address:here
    Sender IP: interface.ip.goes.here
    Target MAC: 255.255.255.255 (Broadcast)
    Target IP: target.ip.goes.here

Depending on what type of hardware we have between us and the router:

Directly connected:

* If we are directly connected to the router the router will respond with an
  ``ARP Reply`` (see below)

Hub:

* If we are connected to a HUB the HUB will broadcast the ARP request out all
  other ports of the HUB. If the router is connected on the same "wire" it will
  respond with an ``ARP Reply`` (see below).

Switch:

* If we are connected to a switch it will check it's local CAM/MAC table to see
  which port has the MAC address we are looking for. If the switch has no entry
  for the MAC address it will rebroadcast the ARP request to all other ports.

* If the switch has an entry in the MAC/CAM table it will send the ARP request
  to the port that has the MAC address we are looking for.

* If the router is on the same "wire" it will respond with an ``ARP Reply``
  (see below)

``ARP Reply``::

    Sender MAC: target:mac:address:here
    Sender IP: target.ip.goes.here
    Target MAC: interface:mac:address:here
    Target IP: interface.ip.goes.here

Now that we have the IP address of either our DNS server or the default gateway
we can resume our DNS process:

* Port 53 is opened to send a UDP request to DNS server (if the response size
  is too large, TCP will be used instead).
* If the local/ISP DNS server does not have it, then a recursive search is
  requested and that flows up the list of DNS servers until the SOA is reached,
  and if found an answer is returned.

Opening of a socket
-------------------
Once the browser receives the IP address of the destination server it takes
that and the given port number from the URL (the http protocol defaults to port
80, and https to port 443) and makes a call to the system library function
named ``socket`` and requests a TCP socket stream - ``AF_INET`` and
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
node`_ where its from and to addresses would be analyzed further.

Most larger businesses and some newer residential connections will have fibre
or direct Ethernet connections, in which case the data remains digital, and
is passed directly to the next `network node`_ for processing.

Eventually, the packet will reach the router managing the local subnet. From
there, it will continue to travel to the AS's border routers, other ASes, and
finally to the destination server. Each router along the way extracts the
destination address from the IP header and routes it to the appropriate next
hop. The TTL field in the IP header is decremented by one for each router that
passes. The packet will be dropped if the TTL field reaches zero or if the
current router has no space in its queue (perhaps due to network congestion).

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

UDP packets
~~~~~~~~~~~

TLS handshake
-------------
* The client computer sends a ``Client hello`` message to the server with it
  TLS version, list of cipher algorithms and compression methods available.

* The server replies with a ``Server hello`` message to the client with the
  TLS version, cipher and compression methods selected + the Server public
  certificate signed by a CA (Certificate Authority) that also contains a
  public key.

* The client verifies the server digital certificate and cipher a symmetric
  cryptography key using an asymmetric cryptography algorithm, attaching the
  server public key and an encrypted message for verification purposes.

* The server decrypts the key using its private key and decrypts the
  verification message with it, then replies with the verification message
  decrypted and signed with its private key

* The client confirm the server identity, cipher the agreed key and sends a
  ``finished`` message to the server, attaching the encrypted agreed key.

* The server sends a ``finished`` message to the client, encrypted with the
  agreed key.

* From now on the TLS session communicates information encrypted with the
  agreed key


TCP packets
~~~~~~~~~~~

HTTP protocol...
----------------

If the web browser used was written by Google, instead of sending an HTTP
request to retrieve the page, it will send a request to try and negotiate with
the server an "upgrade" from HTTP to the SPDY protocol.

If the client is using the HTTP protocol and does not support SPDY, it sends a
request to the server of the form::

    GET / HTTP/1.1
    Host: google.com
    [other headers]

where ``[other headers]`` refers to a series of colon-separated key-value pairs
formatted as per the HTTP specification and separated by single new lines.
(This assumes the web browser being used doesn't have any bugs violating the
HTTP spec. This also assumes that the web browser is using ``HTTP/1.1``,
otherwise it may not include the ``Host`` header in the request and the version
specified in the ``GET`` request will either be ``HTTP/1.0`` or ``HTTP/0.9``.)

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
the web server to determine if the version of the file cached by the web
browser has been unmodified since the last retrieval (ie. if the web browser
included an ``ETag`` header), it may have instead responded with a request of
the form::

    304 Not Modified
    [response headers]

and no payload, and the web browser instead retrieves the HTML from its cache.

After parsing the HTML, the web browser (and server) will repeat this process
for every resource (image, CSS, favicon.ico, etc) referenced by the HTML page,
except instead of ``GET / HTTP/1.1`` the request will be
``GET /$(URL relative to www.google.com) HTTP/1.1``.

If the HTML referenced a resource on a different domain than
``www.google.com``, the web browser will go back to the steps involved in
resolving the other domain, and follow all steps up to this point for that
domain. The ``Host`` header in the request will be set to the appropriate
server name instead of ``google.com``.

HTTP Server Request Handle
--------------------------
The HTTPD (HTTP Daemon) server is the one handling the requests/responses on
the server side.
The most common HTTPD servers are Apache for Linux, and IIS for windows.

* The HTTPD (HTTP Daemon) receives the request.
* The server breaks down the request to the following parameters:
   * HTTP Request Method (GET, POST, HEAD, PUT and DELETE), in our case - GET.
   * Domain, in our case - google.com.
   * Requested path/page, in our case - / (as no specific path/page was
     requested, / is the default path).
* The server verifies that there is a Virtual Host configured on the server
  that corresponds with google.com.
* The server verifies that google.com can accept GET requests.
* The server verifies that the client is allowed to use this method
  (by IP, authentication, etc.).
* If the server has a rewrite module installed (like mod_rewrite for Apache or
  URL Rewrite for IIS), it tries to match the request against one of the
  configured rules. If a matching rule is found, the server uses that rule to
  rewrite the request.
* The server goes to pull the content that corresponds with the request,
  in our case it will fall back to the index file, as "/" is the main file
  (some cases can override this, but this is the most common method).
* The server will parse the file according to the handler, for example -
  let's say that Google is running on PHP.
* The server will use PHP to interpret the index file, and catch the output.
* The server will return the output, on the same request to the client.

HTML parsing
------------

* Fetch contents of requested document from network layer in 8kb chunks.
* Parse HTML document (See
  https://html.spec.whatwg.org/multipage/syntax.html#parsing for more
  information).
* Convert elements to DOM nodes in the content tree.
* Fetch/prefetch external resources linked to the page (CSS, Images, JavaScript
  files, etc.)
* Execute synchronous JavaScript code.

CSS interpretation
------------------

* Parse CSS files and ``<style>`` tag contents using `"CSS lexical and syntax
  grammar"`_
* Each CSS file is parsed into a ``StyleSheet object``, where each object
  contains CSS rules with selectors and objects corresponding CSS grammar.
* A CSS parser can be top-down or bottom-up when a specific parser generator
  is used.

Page Rendering
--------------

* Create a 'Frame Tree' or 'Render Tree' by traversing the DOM nodes, and
  calculating the CSS style values for each node.
* Calculate the preferred width of each node in the 'Frame Tree' bottom up
  by summing the preferred width of the child nodes and the node's
  horizontal margins, borders, and padding.
* Calculate the actual width of each node top-down by allocating each node's
  available width to its children.
* Calculate the height of each node bottom-up by applying text wrapping and
  summing the child node heights and the node's margins, borders, and padding.
* Calculate the coordinates of each node using the information calculated
  above.
* More complicated steps are taken when elements are ``floated``,
  positioned ``absolutely`` or ``relatively``, or other complex features
  are used. See
  http://dev.w3.org/csswg/css2/ and http://www.w3.org/Style/CSS/current-work
  for more details.
* Create layers to describe which parts of the page can be animated as a group
  without being re-rasterized. Each frame/render object is assigned to a layer.
* Textures are allocated for each layer of the page.
* The frame/render objects for each layer are traversed and drawing commands
  are executed for their respective layer. This may be rasterized by the CPU
  or drawn on the GPU directly using D2D/SkiaGL.
* All of the above steps may reuse calculated values from the last time the
  webpage was rendered, so that incremental changes require less work.
* The page layers are sent to the compositing process where they are combined
  with layers for other visible content like the browser chrome, iframes
  and addon panels.
* Final layer positions are computed and the composite commands are issued
  via Direct3D/OpenGL. The GPU command buffer(s) are flushed to the GPU for
  asynchronous rendering and the frame is sent to the window server.

GPU Rendering
-------------

* During the rendering process the graphical computing layers can use general
  purpose ``CPU`` or the graphical processor ``GPU`` as well.

* When using ``GPU`` for graphical rendering computations the graphical
  software layers split the task into multiple pieces, so it can take advantage
  of ``GPU`` massive parallelism for float point calculations required for
  the rendering process.


Window Server
-------------

Post-rendering and user-induced execution
-----------------------------------------

After rendering has completed, the browser executes JavaScript code as a result
of some timing mechanism (such as a Google Doodle animation) or user
interaction (typing a query into the search box and receiving suggestions).
Plugins such as Flash or Java may execute as well, although not at this time on
the Google homepage. Scripts can cause additional network requests to be
performed, as well as modify the page or its layout, effecting another round of
page rendering and painting.

.. _`Creative Commons Zero`: https://creativecommons.org/publicdomain/zero/1.0/
.. _`"CSS lexical and syntax grammar"`: http://www.w3.org/TR/CSS2/grammar.html
.. _`Punycode`: https://en.wikipedia.org/wiki/Punycode
.. _`Ethernet`: http://en.wikipedia.org/wiki/IEEE_802.3
.. _`WiFi`: https://en.wikipedia.org/wiki/IEEE_802.11
.. _`Cellular data network`: https://en.wikipedia.org/wiki/Cellular_data_communication_protocol
.. _`analog-to-digital converter`: https://en.wikipedia.org/wiki/Analog-to-digital_converter
.. _`network node`: https://en.wikipedia.org/wiki/Computer_network#Network_nodes
