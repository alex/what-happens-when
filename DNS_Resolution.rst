DNS lookup

Browser checks if the domain is in its cache. (to see the DNS Cache in Chrome, go to chrome://net-internals/#dns).

If not found, the browser calls gethostbyname library function (varies by OS) to do the lookup.

gethostbyname checks if the hostname can be resolved by reference in the local hosts file (whose location varies by OS) before trying to resolve the hostname through DNS.

If gethostbyname does not have it cached nor can find it in the hosts file then it makes a request to the DNS server configured in the network stack. This is typically the local router or the ISP's caching DNS server.

If it is not cache in the DNS Recursor (ISP DNS/DNS Resolver) then the DNS Resolver queries the root nameserver server.

The root nameserver responds to the DNS Resolver with the address of a .com Top Level Domain (TLD) DNS server.

The DNS Resolver then makes a request to the .com TLD, and the TLD server in return responds with the IP address of the appropriate Authoritative nameserver.

Lastly, the DNS Recursor then queries the authoritative nameserver, and the authoritative nameserver responds to the DNS Resolver's final request with the IP address of the domain. 

The DNS resolver then responds to the web browser with the IP address of the domain requested initially.
