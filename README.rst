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

Interrupt fires...
------------------

Check HSTS list...
------------------

DNS lookup...
-------------

* Browser checks if the domain is in it's cache.
* If not found, calls ``gethostbyname`` library function (varies by OS) to do
  the lookup.
* If ``gethostbyname`` does not have it cached then a request is made to the
  known DNS server that was given to the network stack. This is typical the
  local router or the ISP's caching DNS server
* If the local/ISP DNS server does not have it, then a recursive search is
  requested and that flows up the list of DNS servers until the SOA is reached,
  and if found an answer is returned.

BGP lookup
~~~~~~~~~~

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
