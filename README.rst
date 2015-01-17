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

Check HSTS list...
------------------

DNS lookup...
-------------

* Browser checks if the domain is in its cache.
* If not found, calls ``gethostbyname`` library function (varies by OS) to do
  the lookup.
* If ``gethostbyname`` does not have it cached then a request is made to the
  known DNS server that was given to the network stack. This is typically the
  local router or the ISP's caching DNS server.
* Port 53 is opened to send a UDP request to DNS server (if the response size is
  too large, TCP will be used instead).
* If the local/ISP DNS server does not have it, then a recursive search is
  requested and that flows up the list of DNS servers until the SOA is reached,
  and if found an answer is returned.

Opening of a socket
-------------------
Once the browser receives the IP address of the destination server it takes
that and the given port number from the URL and makes a call to the system
library function names ``socket`` and requests a TCP socket stream -
``AF_INET`` and ``SOCK_STREAM``.

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
     received seequence from the other
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

CSS interpretation...
---------------------

* Parse CSS files and ``<style>`` tag contents using `"CSS lexical and syntax
  grammar"`_

Page Rendering
---------------------

* Create render tree from CSS and visual instructions in HTML
* Layout (reflow) render tree nodes by giving them exact coordiantes of where
  they should appear
* Traverse and "paint" each DOM node

Javascript execution...
-----------------------


.. _`Creative Commons Zero`: https://creativecommons.org/publicdomain/zero/1.0/
.. _`"CSS lexical and syntax grammar"`: http://www.w3.org/TR/CSS2/grammar.html
