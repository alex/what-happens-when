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
This is now almost universally over a Universal Serial Bus (USB) connection,
but historically has been over PS/2 or ADB connections.

The USB circuitry of the keyboard is powered by the 5V supply provided over pin
1 from the computer's USB host controller. 17.78 mA of this current is returned
on either the D+ or D- pin (the middle 2) of the keyboard's USB connector.
Which pin carries the current is rapidly toggled between the two creating
a high speed bitstream (the rate depending on USB 1, 2, or 3) serially encoding
the digital value of the enter key.  This serial signal is then decoded at the
computer's host USB controller, and interpreted by the computer's Human
Interface Device (HID) universal keyboard device driver.  The value of the key
is then passed into the operating system's hardware abstraction layer.

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
  known DNS server that was given to the network stack. This is typical the
  local router or the ISP's caching DNS server
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
requires for ensuring packet delivery and ordering is added and then a UDP
datagram is fashioned. The UDP datagram is handed off to the physical network
layer which inspects the target IP address, looks up the subnet in it's route
tables and wraps the datagram in an envelope with the proper gateway address as
the recipient.

This address lookup and wrapping of datagrams continues until one of two things
happen, the time-to-live value for a datagram reaches zero at which point the
packet is dropped or it reaches the destination.

This send and receive happens multiple times following the TCP connection flow:

* SYN request is sent to the target
* target receives SYN and if it's in an agreeable mood, replies with SYN/ACK
* source sends ACK to acknowledge socket is established
* data is transferred until FIN/ACK is sent and acknowledged

UDP packets
~~~~~~~~~~~

TLS handshake...
----------------

TCP packets
~~~~~~~~~~~

HTTP protocol...
----------------

Page rendering...
-----------------

Javascript execution...
-----------------------

CSS interpretation...
---------------------


.. _`Creative Commons Zero`: https://creativecommons.org/publicdomain/zero/1.0/
