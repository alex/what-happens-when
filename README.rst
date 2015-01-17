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

In the case of the the USB example: the USB circuitry of the keyboard is powered
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
* If ``gethostbyname`` does not have it cached then a request is made to the
  known DNS server that was given to the network stack. This is typically the
  local router or the ISP's caching DNS server.
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
requires for ensuring packet delivery and ordering is added and then a IP
packet is fashioned. The IP packet is then handed off to the physical network
layer which inspects the target IP address, looks up the subnet in it's route
tables and wrapped in an ethernet frame with the proper gateway address as the
recipient.

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

HTML parsing...
-----------------

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
* Final layer positions are computed and the composite commands are issued
  via Direct3D/OpenGL. The GPU command buffer(s) are flushed to the GPU for
  asyncrounous rendering and the frame is sent to the window server.
  
GPU Rendering
-------------

Window Server
-------------

Post-rendering and user-induced execution
-----------------------

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
