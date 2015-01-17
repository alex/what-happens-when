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

In the case of the USB example: the USB circuitry of the keyboard is powered
by the 5V supply provided over pin 1 from the computer's USB host controller.
17.78 mA of this current is returned on either the D+ or D- pin (the middle 2)
of the keyboard's USB connector. Which pin carries the current is rapidly
toggled between the two creating a high speed bitstream (the rate depending on
USB 1, 2, or 3) serially encoding the digital value of the enter key.  This
serial signal is then decoded at the computer's host USB controller, and
interpreted by the computer's Human Interface Device (HID) universal keyboard
device driver.  The value of the key is then passed into the operating system's
hardware abstraction layer.

Interrupt fires...
------------------

The keyboard sends signals on its interrupt request line (IRQ), which is mapped
to an ``interrupt vector`` (integer) by the interrupt controller. The CPU uses
the ``Interrupt Descriptor Table`` (IDT) to map the interrupt vectors to
functions (``interrupt handlers``) which are supplied by the kernel. When an
interrupt arrvies, the CPU indexes the IDT with the interrupt vector and runs
the appropriate handler. Thus, the kernel is entered.

(On Windows) A ``WM_KEYDOWN`` message is sent to the app
--------------------------------------------------------

The HID transport passes the key down event to the ``KBDHID.sys`` driver which
converts the HID usage into a scancode. In this case the scan code is
``VK_RETURN`` (``0x0D``). The ``KBDHID.sys`` driver interfaces with the
``KBDCLASS.sys`` (keyboard class driver). This driver is responsible for
handling all keyboard and keypad input in a secure manner. It then calls into
``Win32K.sys`` (after potentially passing the message through an 3rd party
keyboard filters that are installed). This all happens in kernel mode.

``Win32K.sys`` figures out what window is the active window through the
``GetForegroundWindow()`` API. This API provides the window handle of the
browser's address box. The main Windows "message pump" then calls
``SendMessage(hwnd, WM_KEYDOWN, VK_RETURN, lParam)``. ``lParam`` is a bitmask
that indicates further information about the keypress: repeat count (0 in this
case), the actual scan code (can be OEM dependent, but generally wouldn't be for
``VK_RETURN``), whether extended keys (e.g. alt, shift, ctrl) were also pressed
(they weren't), and some other state.

The Windows ``SendMessage`` API is a relatively straightforward function that
simply calls the main message processing function (called a ``WindowProc``)
assigned to the window handle (``hWnd``).

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
* Since the hostname is ``google.com`` there won't be any, but if there were the
  browser would apply `Punycode`_ encoding to the hostname portion of the URL.

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
* Port 53 is opened to send a UDP request to DNS server (if the response size is
  too large, TCP will be used instead).
* If the local/ISP DNS server does not have it, then a recursive search is
  requested and that flows up the list of DNS servers until the SOA is reached,
  and if found an answer is returned.

Opening of a socket
-------------------
Once the browser receives the IP address of the destination server it takes
that and the given port number from the URL (the http protocol defaults to port
80, and https to port 443) and makes a call to the system library function named
``socket`` and requests a TCP socket stream - ``AF_INET`` and ``SOCK_STREAM``.

This request is passed to the Transport Layer where the extra love that TCP/IP
requires for ensuring packet delivery and ordering is added and then an IP
packet is fashioned. The IP packet is then handed off to the physical network
layer which inspects the target IP address, looks up the subnet in its route
tables and wrapped in an ethernet frame with the proper gateway address as the
recipient. At this point the packet is ready to be transmitted, most likely
through either:

* `Ethernet`_
* `WiFi`_
* `Cellular data network`_

In all cases the last point at which the packet leaves your computer is a
digital-to-analog (DAC) converter which fires off electrical 1's and 0's on a
wire. On the other end of the physical bit transfer is a `analog-to-digital
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

where ``[other headers]`` refers to a colon-separated series of key-value pairs
formatted as per the HTTP specification and separated by single new lines. (This
assumes the web browser being used doesn't have any bugs violating the HTTP
spec. This also assumes that the web browser is using ``HTTP/1.1``, otherwise it
may not include the ``Host`` header in the request and the version specified in
the ``GET`` request will either be ``HTTP/1.0`` or ``HTTP/0.9``.)

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
the web server to determine if the version of the file cached by the web browser
has been unmodified since the last retrieval (ie. if the web browser included an
``ETag`` header), it may have instead responded with a request of the form::

    304 Not Modified
    [response headers]

and no payload, and the web browser instead retrieves the HTML from its cache.

After parsing the HTML, the web browser (and server) will repeat this process
for every resource (image, CSS, favicon.ico, etc) referenced by the HTML page,
except instead of ``GET / HTTP/1.1`` the request will be
``GET /$(URL relative to www.google.com) HTTP/1.1``.

If the HTML referenced a resource on a different domain than ``www.google.com``,
the web browser will go back to the steps involved in resolving the other
domain, and follow all steps up to this point for that domain. The ``Host``
header in the request will be set to the appropriate server name instead of
``google.com``.

HTML parsing...
---------------

* Fetch contents of requested document from network layer in 8kb chunks
* Parse HTML document
* Convert elements to DOM nodes in the content tree
* Fetch/prefetch external resources linked to the page (CSS, Images, JavaScript
  files, etc.)
* Execute synchronous JavaScript code

Content Delivery Networks
-------------------------

* If there were any cdn references in the ``<link>`` or ``<script>`` tags the process will begin again at DNS Lookup for these addresses

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
* Final layer positions are computed and the composite commands are issued
  via Direct3D/OpenGL. The GPU command buffer(s) are flushed to the GPU for
  asyncrounous rendering and the frame is sent to the window server.

GPU Rendering
-------------

Window Server
-------------

Post-rendering and user-induced execution
-----------------------------------------

After rendering has completed, the browser executes JavaScript code as a result
of some timing mechanism (such as a Google Doodle animation) or user interaction
(typing a query into the search box and receiving suggestions). Plugins such as
Flash or Java may execute as well, although not at this time on the Google
homepage. Scripts can cause additional network requests to be performed, as well
as modify the page or its layout, effecting another round of page rendering and
painting.

.. _`Creative Commons Zero`: https://creativecommons.org/publicdomain/zero/1.0/
.. _`"CSS lexical and syntax grammar"`: http://www.w3.org/TR/CSS2/grammar.html
.. _`Punycode`: https://en.wikipedia.org/wiki/Punycode
.. _`Ethernet`: http://en.wikipedia.org/wiki/IEEE_802.3
.. _`WiFi`: https://en.wikipedia.org/wiki/IEEE_802.11
.. _`Cellular data network`: https://en.wikipedia.org/wiki/Cellular_data_communication_protocol
.. _`analog-to-digital converter`: https://en.wikipedia.org/wiki/Analog-to-digital_converter
.. _`network node`: https://en.wikipedia.org/wiki/Computer_network#Network_nodes
