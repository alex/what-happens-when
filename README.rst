What happens when...
====================

This repository is an attempt to answer the age old interview question "What
happens when you type google.com into your browser and press enter?"

Except instead of the usual story, we're going to try to answer this question
in as much detail as possible. No skipping out on anything.

This is a collaborative process, so dig in and try to help out! There's tons of
details missing, just waiting for you to add them! So send us a pull request,
please!

This is all licensed under the terms of the `Creative Commons Zero`_ license.

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

In the case of the USB keyboard: the USB circuitry of the keyboard is powered
by the 5V supply provided over pin 1 from the computer's USB host controller.
The keycode generated is stored by internal keyboard circuitry memory in a register
called "endpoint".
The host USB contoller polls that "endpoint" every ~10ms (minimum value declared by
the keyboard), so it gets the keycode value stored on it.
This value goes to the USB SIE (Serial Interface Engine) to be converted in one or 
more USB packets that follows the low level USB protocol.
Those packets are sent by a diferential electrical signal over D+ and D- pins
(the middle 2) at a maximum speed of 1.5 Mb/s, as an HID  (Human Interface Devide)
device is always declared to be a "low speed device" (USB 2.0 compliance).  
This serial signal is then decoded at the computer's host USB controller, and
interpreted by the computer's Human Interface Device (HID) universal keyboard
device driver.  The value of the key is then passed into the operating system's
hardware abstraction layer.

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
``SendMessage(hwnd, WM_KEYDOWN, VK_RETURN, lParam)``. ``lParam`` is a bitmask
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


(On GNU/Linux) the Xorg server listen for keycodes
--------------------------------------------------

When a graphical ``X server`` is used, a re-mapping of keycodes to scancodes
is made with ``X server`` specific keymaps and rules.
When the scancode mapping of the key pressed is complete, the ``X server``
sends the character to the ``window manager`` (DWM, metacity, i3, etc), so the
``window manager`` in turn sends the character to the focused window.
The graphical API of the window  that receives the character prints the appropiate
font symbol in the appropiate focused field.


Is it a URL or a search term?
-----------------------------

Parse URL...
------------

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
* The local DNS server (or local gateway's) MAC address is looked up in the ARP
  cache. If the MAC address is missing, an ARP request packet is sent.
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

This request is passed to the Transport Layer where the extra love that TCP/IP
requires for ensuring packet delivery and ordering is added and then an IP
packet is fashioned. The IP packet is then handed off to the physical network
layer which inspects the target IP address, looks up the subnet in its route
tables and wrapped in an ethernet frame with the proper gateway address as the
recipient. At this point the packet is ready to be transmitted through either:

* `Ethernet`_
* `WiFi`_
* `Cellular data network`_

In all cases the last point at which the packet leaves your computer is a
digital-to-analog (DAC) converter which fires off electrical 1's and 0's on a
wire. On the other end of the physical bit transfer is an `analog-to-digital
converter`_  which converts the electrical bits into logic signals to be
processed by the next `network node`_ where its from and to addresses would be
analyzed further.

This address lookup and wrapping of datagrams continues until one of two things
happen, the time-to-live value for a datagram reaches zero at which point the
packet is dropped or it reaches the destination.

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
   * Increases the receiver acknowledgement number
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

TLS handshake...
----------------
* The client computer sends a ``Client hello`` message to the server with it TLS version,
list of cipher algorithms and compression methods available.

* The server replies with a ``Server hello`` message to the client with the TLS version, cipher
and compression methods selected + the Server public certificate signed by a CA (Certificate Authority)
that also contains a public key.

* The client verifies the server digital certificate and cipher a symetric cryptography key using an asymetric
  cryptography algorithm, attaching the server public key and an encrypted message for verification purposes.

* The server decrypts the key using its private key and decrypts the verification message with it, then replies
  with the verification message decrypted and signed with its private key

* The client confirm the server identity, cipher the agreed key and sends a ``finished`` message to the server,
  attaching the encrypted agreed key.

* The server sends a ``finished`` message to the client, encrypted with the agreed key.

* From now on the TLS session communicates information encrypted with the agreed key



TCP packets
~~~~~~~~~~~

HTTP protocol...
----------------

If the web browser used was written by Google, instead of sending an HTTP
request to retrieve the page, it will send an request to try and negotiate with
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

HTML parsing...
---------------

* Fetch contents of requested document from network layer in 8kb chunks
* Parse HTML document
* Convert elements to DOM nodes in the content tree
* Fetch/prefetch external resources linked to the page (CSS, Images, JavaScript
  files, etc.)
* Execute synchronous JavaScript code

CSS interpretation...
---------------------

* Parse CSS files and ``<style>`` tag contents using `"CSS lexical and syntax
  grammar"`_

Page Rendering
--------------

* Create a 'Frame Tree' or 'Render Tree' by running Layout (reflow) on the
  content nodes. This gives each node exact coordinates.
* Create layers to describe which parts of the page can be animated as a group
  without being re-rasterized. Each frame/render object is assigned to a layer.
* Textures are allocated for each layer of the page.
* The frame/render objects for each layers are traversed and drawing commands
  are executed for their respective layer. This may be rasterized by the CPU
  or drawn on the GPU directly using D2D/SkiaGL.
* The page layers are sent to the compositing process where they are combined
  with layers for other visible content like the browser chrome, iframes
  and addon panels.
.. _`Creative Commons Zero`: https://creativecommons.org/publicdomain/zero/1.0/
.. _`"CSS lexical and syntax grammar"`: http://www.w3.org/TR/CSS2/grammar.html
.. _`Punycode`: https://en.wikipedia.org/wiki/Punycode
.. _`Ethernet`: http://en.wikipedia.org/wiki/IEEE_802.3
.. _`WiFi`: https://en.wikipedia.org/wiki/IEEE_802.11
.. _`Cellular data network`: https://en.wikipedia.org/wiki/Cellular_data_communication_protocol
.. _`analog-to-digital converter`: https://en.wikipedia.org/wiki/Analog-to-digital_converter
.. _`network node`: https://en.wikipedia.org/wiki/Computer_network#Network_nodes
