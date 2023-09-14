# What Happens When You Type "google.com" into Your Browser's Address Box and Press Enter

This repository aims to answer the age-old interview question "What happens when you type google.com into your browser's address box and press enter?" in as much detail as possible. It covers the entire process, leaving no step unexplained. This is a collaborative project, so contributions are welcome through pull requests.

## Table of Contents

1. [The "g" key is pressed](#the-g-key-is-pressed)
2. [The "enter" key bottoms out](#the-enter-key-bottoms-out)
3. [Interrupt fires [NOT for USB keyboards]](#interrupt-fires-not-for-usb-keyboards)
4. [(On Windows) A WM_KEYDOWN message is sent to the app](#on-windows-a-wm_keydown-message-is-sent-to-the-app)
5. [(On OS X) A KeyDown NSEvent is sent to the app](#on-os-x-a-keydown-nsevent-is-sent-to-the-app)
6. [(On GNU/Linux) the Xorg server listens for keycodes](#on-gnulinux-the-xorg-server-listens-for-keycodes)
7. [Parse URL](#parse-url)
8. [Is it a URL or a search term?](#is-it-a-url-or-a-search-term)
9. [Convert non-ASCII Unicode characters in the hostname](#convert-non-ascii-unicode-characters-in-the-hostname)
10. [Check HSTS list](#check-hsts-list)
11. [DNS lookup](#dns-lookup)
12. [ARP process](#arp-process)
13. [Opening of a socket](#opening-of-a-socket)
14. [TLS handshake](#tls-handshake)
15. [If a packet is dropped](#if-a-packet-is-dropped)
16. [HTTP protocol](#http-protocol)
17. [HTTP Server Request Handle](#http-server-request-handle)
18. [Behind the scenes of the Browser](#behind-the-scenes-of-the-browser)
19. [Browser](#browser)
20. [HTML parsing](#html-parsing)
21. [CSS interpretation](#css-interpretation)
22. [Page Rendering](#page-rendering)
23. [GPU Rendering](#gpu-rendering)
24. [Window Server](#window-server)
25. [Post-rendering and user-induced execution](#post-rendering-and-user-induced-execution)

## The "g" key is pressed

When the user presses the "g" key, the browser receives the event, and auto-complete functions may kick in, suggesting various options based on the user's browsing history, bookmarks, cookies, and popular searches from the internet. These suggestions are refined with each keypress, and they may even suggest "google.com" before the user finishes typing it.

## The "enter" key bottoms out

When the user presses the "enter" key, a series of actions occur:

- An electrical circuit specific to the enter key is closed, allowing a small amount of current to flow into the keyboard's logic circuitry.
- The keyboard controller scans the state of each key switch, debounces the electrical noise, and converts it to a keycode integer (e.g., 13 for the enter key).
- The keycode is stored in the keyboard's internal memory.
- The host USB controller polls the keyboard's memory for the keycode.
- The keycode is converted into one or more USB packets and sent over a USB connection to the computer's USB host controller.
- The computer's Human Interface Device (HID) universal keyboard device driver interprets the key value and passes it into the operating system's hardware abstraction layer.

For virtual keyboards on touch screen devices, a similar process occurs, with touch events triggering software interrupts.

## Interrupt fires [NOT for USB keyboards]

For non-USB keyboards, when a key is pressed, an interrupt is sent via the keyboard's interrupt request line (IRQ). The CPU uses the Interrupt Descriptor Table (IDT) to map the interrupt to an interrupt handler supplied by the kernel. The kernel is then entered.

## (On Windows) A WM_KEYDOWN message is sent to the app

On Windows, when the keyboard sends a keydown event, it is processed by the KBDHID.sys driver, which converts it into a scancode. This scancode is further processed by the KBDCLASS.sys (keyboard class driver), and eventually, the Win32K.sys kernel mode driver determines the active window (e.g., the browser's address bar) and sends a WM_KEYDOWN message to it.

## (On OS X) A KeyDown NSEvent is sent to the app

On macOS (OS X), when a keydown event occurs, the I/O Kit kext keyboard driver generates an interrupt event, which is then processed by the WindowServer. An NSEvent of type KeyDown is dispatched to the appropriate application, such as the browser, to handle the keypress.

## (On GNU/Linux) the Xorg server listens for keycodes

On GNU/Linux systems running the Xorg server, the server listens for keycodes when a key is pressed. These keycodes are mapped to scancodes and then to characters by the X server. The focused window receives the character input.

## Parse URL

The browser parses the URL entered by the user. This process involves breaking down the URL into its constituent parts, including the protocol (e.g., "http"), the hostname (e.g., "google.com"), and the resource path (e.g.,

 "/search").

## Is it a URL or a search term?

The browser determines whether the entered text is a valid URL or a search term. If it's a valid URL, it proceeds to load that URL. If it's a search term, the browser may initiate a search using the default search engine (e.g., Google).

## Convert non-ASCII Unicode characters in the hostname

If the hostname contains non-ASCII Unicode characters, such as accented letters, they are converted to Punycode, which is an ASCII representation of the internationalized domain name (IDN). This ensures that the domain name is compatible with the DNS system.

## Check HSTS list

The browser checks its HTTP Strict Transport Security (HSTS) list to see if the entered domain is listed. HSTS is a web security policy mechanism that helps to protect websites against protocol downgrade attacks and cookie hijacking.

## DNS lookup

If the domain is not found in the browser's cache, it needs to be resolved to an IP address. The browser sends a DNS (Domain Name System) lookup request to a DNS resolver, such as one provided by the user's Internet Service Provider (ISP) or a public DNS resolver like Google's 8.8.8.8.

The DNS resolver checks its own cache for the IP address of the domain. If it has the IP address, it responds with the address, and the browser can proceed. If not, the resolver acts as a client, querying the root DNS servers, then the authoritative DNS servers for the top-level domain (TLD), and finally the authoritative DNS servers for the specific domain until it obtains the IP address. This process is recursive.

## ARP process

Before the browser can send an HTTP request, it needs to know the MAC (Media Access Control) address of the destination IP address (the web server). To obtain this, it performs an ARP (Address Resolution Protocol) request on the local network. If the destination IP address is on a different network, it forwards the request to the default gateway, which handles the ARP process for external networks.

## Opening of a socket

With the IP address of the web server known, the browser opens a socket connection to the server on port 80 (for HTTP) or port 443 (for HTTPS). A socket is an endpoint for sending or receiving data across a computer network. The browser uses the IP address and port number to identify the server it wants to communicate with.

## TLS handshake

If the website uses HTTPS, there is an additional step before sending the HTTP request: the TLS (Transport Layer Security) handshake. This is a cryptographic protocol that establishes a secure connection between the browser and the web server. It involves the following steps:

1. The browser sends a ClientHello message to the server, along with supported cryptographic algorithms and a random value.
2. The server responds with a ServerHello message, selecting a cryptographic algorithm and sending its own random value.
3. The server sends its digital certificate, which contains its public key and is signed by a trusted Certificate Authority (CA).
4. The browser verifies the certificate's authenticity by checking the CA's signature and checking if the certificate is still valid.
5. If the certificate is valid, the browser generates a pre-master secret and encrypts it with the server's public key, sending it to the server.
6. Both the browser and the server use the pre-master secret to derive a session key for encrypting and decrypting data.
7. The browser sends a Finished message to confirm that the handshake is complete.
8. From this point, all data exchanged between the browser and the server is encrypted using the session key.

## If a packet is dropped

If any packet in the communication between the browser and the server is dropped, the TCP (Transmission Control Protocol) stack on both sides detects the loss and automatically retransmits the packet. This ensures reliable data transfer.

## HTTP protocol

Once the socket connection is established (and the TLS handshake is complete if using HTTPS), the browser sends an HTTP request to the server. The request includes the HTTP method (e.g., GET), the requested resource (e.g., "/index.html"), and various headers that provide additional information, such as the user agent, accepted content types, and more.

## HTTP Server Request Handle

The web server receives the HTTP request and processes it. It locates the requested resource on its file system or generates dynamic content if necessary (e.g., running a server-side script). The server then sends an HTTP response back to the browser.

## Behind the scenes of the Browser

Inside the browser, multiple components work together to handle the HTTP response. This includes the networking layer, which receives and manages data from the network; the rendering engine, which parses HTML, CSS, and JavaScript; and the JavaScript engine, which executes scripts and interacts with the Document Object Model (DOM).

## Browser

The browser receives the HTTP response from the server and starts processing it. It first checks the HTTP status code to determine if the request was successful (e.g., 200 OK) or if there was an error (e.g., 404 Not Found).

## HTML parsing

If the response contains HTML content, the browser's rendering engine parses the HTML document. It constructs the Document Object Model (DOM), which is a representation of the page's structure and content.

## CSS interpretation

The browser also processes any linked or inline CSS (Cascading Style Sheets) that is part of the HTML document. It applies styles to the DOM elements, determining how the content should be displayed on the screen.

## Page Rendering

With the DOM and CSS styles in place, the browser's rendering engine performs layout calculations to determine the position and size of each element on the page. It then paints the page on the screen, rendering text, images, and other content.

## GPU Rendering

Modern browsers often offload rendering tasks to the GPU (Graphics Processing Unit) for improved performance. This involves transferring rendering instructions to the GPU, which can handle complex graphics operations more efficiently than the CPU.

## Window Server

On macOS, the rendered page is sent to the WindowServer, which composites it with other graphical elements and manages the final display on the screen. On other operating systems, a similar windowing system handles the rendering of browser windows.

## Post-rendering and user-induced execution

After rendering, the browser may continue to fetch additional resources such as images, scripts, and stylesheets specified in the HTML. JavaScript code may also execute, interacting with the DOM and making additional network requests as needed.

Once the page is fully loaded and rendered, the user can interact with it, clicking links, submitting forms, and triggering further requests and page updates.

This entire process, from pressing the "enter" key to seeing the fully rendered web page, occurs in a matter of seconds and involves numerous complex steps performed by both the browser and the web server.

Contributions are welcome to expand and refine this document to provide an even more detailed and comprehensive understanding of what happens when you type "google.com" into your browser's address bar and press enter.
