What happens when...
====================
This repository is an attempt to answer the age-old interview question "What
happens when you type google.com into your browser's address box and press
enter?"
Except instead of the usual story, we're going to try to answer this question
in as much detail as possible. No skipping out on anything.
This is a collaborative process, so dig in and try to help out! There are tons
of details missing, just waiting for you to add them! So send us a pull
request, please!
This is all licensed under the terms of the `Creative Commons Zero`_ license.
Read this in `简体中文`_ (simplified Chinese), `日本語`_ (Japanese), `한국어`_
(Korean) and `Spanish`_. NOTE: these have not been reviewed by the alex/what-happens-when
maintainers.
Table of Contents
====================
.. contents::
   :backlinks: none
   :local:
The "g" key is pressed
----------------------
The following sections explain the physical keyboard actions
and the OS interrupts. When you press the key "g" the browser receives the
event and the auto-complete functions kick in.
Depending on your browser's algorithm and if you are in
private/incognito mode or not various suggestions will be presented
to you in the dropdown below the URL bar. Most of these algorithms sort
and prioritize results based on search history, bookmarks, cookies, and
popular searches from the internet as a whole. As you are typing
"google.com" many blocks of code run and the suggestions will be refined
with each keypress. It may even suggest "google.com" before you finish typing
it.
The "enter" key bottoms out
---------------------------
To pick a zero point, let's choose the Enter key on the keyboard hitting the
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
  one or more USB packets that follow the low-level USB protocol.
- Those packets are sent by a differential electrical signal over D+ and D-
  pins (the middle 2) at a maximum speed of 1.5 Mb/s, as an HID
  (Human Interface Device) device is always declared to be a "low-speed device"
  (USB 2.0 compliance).
- This serial signal is then decoded at the computer's host USB controller, and
  interpreted by the computer's Human Interface Device (HID) universal keyboard
  device driver.  The value of the key is then passed into the operating
  system's hardware abstraction layer.
*In the case of Virtual Keyboard (as in touch screen devices):*
- When the user puts their finger on a modern capacitive touch screen, a
  tiny amount of current gets transferred to the finger. This completes the
  circuit through the electrostatic field of the conductive layer and
  creates a voltage drop at that point on the screen. The
  ``screen controller`` then raises an interrupt reporting the coordinate of
  the keypress.
- Then the mobile OS notifies the currently focused application of a press event
  in one of its GUI elements (which now is the virtual keyboard application
  buttons).
- The virtual keyboard can now raise a software interrupt for sending a
  'key pressed' message back to the OS.
- This interrupt notifies the currently focused application of a 'key pressed'
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
converts the HID usage into a scancode. In this case, the scan code is
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
In many cases the URL has a special piece of text appended to it to tell the
search engine that it came from a particular browser's URL bar.
Convert non-ASCII Unicode characters in the hostname
------------------------------------------------
* The browser checks the hostname for characters that are not in ``a-z``,
  ``A-Z``, ``0-9``, ``-``, or ``.``.
* Since the hostname is ``google.com`` there won't be any, but if there were
  the browser would apply `Punycode`_ encoding to the hostname portion of the
  URL.
Check HSTS list
---------------
* The browser checks its "preloaded HSTS (HTTP Strict Transport Security)"
  list. This is a list of websites that have requested to be contacted via
  HTTPS only.
* If the website is in the list, the browser sends its request via HTTPS
  instead of HTTP. Otherwise, the initial request is sent via HTTP.
  (Note that a website can still use the HSTS policy *without* being in the
  HSTS list.  The first HTTP request to the website by a user will receive a
  response requesting that the user only send HTTPS requests.  However, this
  single HTTP request could potentially leave the user vulnerable to a
  `downgrade attack`_, which is why the HSTS list is included in modern web
  browsers.)
DNS lookup
----------
* Browser checks if the domain is in its cache. (to see the DNS Cache in
  Chrome, go to `chrome://net-internals/#dns <chrome://net-internals/#dns>`_).
* If not found, the browser calls ``gethostbyname`` library function (varies by
  OS) to do the lookup.
* ``gethostbyname`` checks if the hostname can be resolved by reference in the
  local ``hosts`` file (whose location `varies by OS`_) before trying to
  resolve the hostname through DNS.
* If ``gethostbyname`` does not have it cached nor can find it in the ``hosts``
  file then it makes a request to the DNS server configured in the network
  stack. This is typically the local router or the ISP's caching DNS server.
* If the DNS server is on the same subnet the network library follows the
  ``ARP process`` below for the DNS server.
* If the DNS server is on a different subnet, the network library follows
  the ``ARP process`` below for the default gateway IP.
ARP process
-----------
In order to send an ARP (Address Resolution Protocol) broadcast the network
stack library needs the target IP address to lookup. It also needs to know the
MAC address of the interface it will use to send out the ARP broadcast.
The ARP cache is first checked for an ARP entry for our target IP. If it is in
the cache, the library function returns the result: Target IP = MAC.
If the entry is not in the ARP cache:
* The route table is looked up, to see if the Target IP address is on any of
  the subnets on the local route table. If it is, the library uses the
  interface associated with that subnet. If it is not, the library uses the
  interface that has the subnet of our default gateway.
* The MAC address of the selected network interface is looked up.
* The network library sends a Layer 2 (data link layer of the `OSI model`_)
  ARP request:
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
* TCP transfers will slow down when packets are lost, so actively dropping a few 
  packets before the router is completely full is a way to ask for the incoming 
  traffic to slow down.
* After reaching the slow-start threshold, the window increases additively for
  each packet acknowledged. If a packet is dropped, the window reduces
  exponentially until another packet is acknowledged.
