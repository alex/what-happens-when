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
