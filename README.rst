What happens behind the scenes when we type google.com in a browser and press enter?


Table of Contents
====================

.. contents::
   :backlinks: none
   :local:

When the "g" key is pressed
----------------------
When you press the key "g" the browser receives the
event and the auto-complete functions kick in.
Depending on your browser's algorithm and if you are in
private/incognito mode or not various suggestions will be presented
to you in the dropdown below the URL bar. Most of these algorithms sort
and prioritize results based on search history, and
popular searches from the internet as a whole. When typing
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

*In the case of Virtual Keyboard:*

- When the user puts their finger on a modern capacitive touch screen, a
  tiny amount of current gets transferred to the finger. This completes the
  circuit through the electrostatic field of the conductive layer and generates a voltage drop at that point of the screen.
- The screen contoller then raises an interrupt, thus rreporting the cordinate of the click or key pressed.
- The device OS the notifies the present focused application of a click event it's GUI elements.
- The touch screen can now raise a software interrupt for sending a key pressed message back to the Operating System (OS).
- This interrupt notifies the current focused app of a key pressed action.

Parse the URL
-------------

Then, as you hit 'Enter,' the browser embarks on the journey by parsing the entered URL. This involves dissecting the URL into its fundamental components — protocol, domain, and resource path — laying the foundation for the subsequent steps.

Check HSTS list (deprecated)
---------------------------

In the historical context of web security, browsers once consulted the HSTS (HTTP Strict Transport Security) list. However, with advancements in security protocols, this step has been deprecated, paving the way for more robust measures. Bear in mind that, the site can still make use of the HTST policy without being the HTST list.

DNS lookup
----------

In this stage, the browser tries to figure out the IP address for the entered domain name, 'google.com'. It undergoes translation through the Domain Name System (DNS). This process converts the human-readable name into a machine-readable IP address, enabling the browser to locate the server on the vast expanse of the internet. The look process is as follow; Browser cache, Operating System cache, Router cache, ISP DNS cache, and Recursive search.

Opening of a socket + TLS handshake
-----------------------------------

At this point, security takes center stage as the browser initiates a sophisticated dance: the opening of a socket and a Transport Layer Security (TLS) handshake. This ensures a secure and encrypted communication channel between your device and the server.

HTTP protocol
-------------

If the web browser used was written by Google, instead of sending an HTTP
request to retrieve the page, it will send a request to try and negotiate with
the server an "upgrade" from HTTP to the SPDY protocol.

If the client is using the HTTP protocol and does not support SPDY, it sends a
request to the server of the form::

    GET / HTTP/1.1
    Host: google.com
    Connection: close
    [other headers]

where ``[other headers]`` refers to a series of colon-separated key-value pairs
formatted as per the HTTP specification and separated by single newlines.
(This assumes the web browser being used doesn't have any bugs violating the
HTTP spec. This also assumes that the web browser is using ``HTTP/1.1``,
otherwise it may not include the ``Host`` header in the request and the version
specified in the ``GET`` request will either be ``HTTP/1.0`` or ``HTTP/0.9``.)

HTTP/1.1 defines the "close" connection option for the sender to signal that
the connection will be closed after completion of the response. For example,

    Connection: close

HTTP/1.1 applications that do not support persistent connections MUST include
the "close" connection option in every message.

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
the webserver to determine if the version of the file cached by the web
browser has been unmodified since the last retrieval (ie. if the web browser
included an ``ETag`` header), it may instead respond with a request of
the form::

    304 Not Modified
    [response headers]

and no payload, and the web browser instead retrieve the HTML from its cache.

After parsing the HTML, the web browser (and server) repeats this process
for every resource (image, CSS, favicon.ico, etc) referenced by the HTML page,
except instead of ``GET / HTTP/1.1`` the request will be
``GET /$(URL relative to www.google.com) HTTP/1.1``.

If the HTML referenced a resource on a different domain than
``www.google.com``, the web browser goes back to the steps involved in
resolving the other domain, and follows all steps up to this point for that
domain. The ``Host`` header in the request will be set to the appropriate
server name instead of ``google.com``.

HTTP Server Request Handle
--------------------------
The HTTPD (HTTP Daemon) server is the one handling the requests/responses on
the server-side. The most common HTTPD servers are Apache or nginx for Linux
and IIS for Windows.

* The HTTPD (HTTP Daemon) receives the request.
* The server breaks down the request to the following parameters:
   * HTTP Request Method (either ``GET``, ``HEAD``, ``POST``, ``PUT``,
     ``PATCH``, ``DELETE``, ``CONNECT``, ``OPTIONS``, or ``TRACE``). In the
     case of a URL entered directly into the address bar, this will be ``GET``.
   * Domain, in this case - google.com.
   * Requested path/page, in this case - / (as no specific path/page was
     requested, / is the default path).
* The server verifies that there is a Virtual Host configured on the server
  that corresponds with google.com.
* The server verifies that google.com can accept GET requests.
* The server verifies that the client is allowed to use this method
  (by IP, authentication, etc.).
* If the server has a rewrite module installed (like mod_rewrite for Apache or
  URL Rewrite for IIS), it tries to match the request against one of the
  configured rules. If a matching rule is found, the server uses that rule to
  rewrite the request.
* The server goes to pull the content that corresponds with the request,
  in our case it will fall back to the index file, as "/" is the main file
  (some cases can override this, but this is the most common method).
* The server parses the file according to the handler. If Google
  is running on PHP, the server uses PHP to interpret the index file, and
  streams the output to the client.

Armed with the appropriate address and a secure connection, the browser sends out an HTTP or HTTPS request to the server, articulating the specific web content it needs.

Server Response
---------------

The server responds to the browser's request by sending back a payload of data. This could include HTML, CSS, JavaScript, images, or other resources essential for constructing the web page. Funny thing is that the whole reponse won't be more 36 kb.


Behind the scenes of the Browser
----------------------------------

Once the server supplies the resources (HTML, CSS, JS, images, etc.)
to the browser it undergoes the below process:

* Parsing - HTML, CSS, JS
* Rendering - Construct DOM Tree → Render Tree → Layout of Render Tree →
  Painting the render tree

Browser's High Level Structure
------------------------------

The browser, a complex software entity, reveals its high-level structure. This includes components such as the user interface, browser engine, rendering engine, networking components, and more.

* **User interface:** This includes the address bar, back/forward button, bookmarking menu, etc. Every part of the browser display except the window where you see the requested page.
* **Browser engine:** It marshals actions between the UI and the rendering engine.
* **Rendering engine:** The rendering engine is responsible for displaying
  requested content. For example if the requested content is HTML, the
  rendering engine parses HTML and CSS, and displays the parsed content on
  the screen.
* **Networking:** The networking handles network calls such as HTTP requests,
  using different implementations for different platforms behind a
  platform-independent interface.
* **UI backend:** The UI backend is used for drawing basic widgets like combo
  boxes and windows. This backend exposes a generic interface that is not
  platform-specific.
  Underneath it uses operating system user interface methods.
* **JavaScript engine:** The JavaScript engine is used to parse and
  execute JavaScript code.
* **Data storage:** The data storage is a persistence layer. The browser may
  need to save all sorts of data locally, such as cookies. Browsers also
  support storage mechanisms such as localStorage, IndexedDB, WebSQL and
  FileSystem.

Rendering Engine
----------------

The heartbeat of the browser, the rendering engine, interprets HTML and CSS, constructing the Document Object Model (DOM) and orchestrating the visual presentation on the screen.

The Main Flow
-------------

The journey from receiving raw HTML to the final rendering of a web page is a meticulously orchestrated process. Understanding this main flow sheds light on the intricacies of the browsing experience.

Parsing Basics
--------------

Parsing, the fundamental process of converting raw HTML and CSS into a structured format, is essential for the browser to comprehend and render web content accurately.

DOM Tree
--------

The DOM tree, a dynamic representation of the web page structure, facilitates interaction through JavaScript. Its creation and significance are pivotal to understanding the dynamic nature of web pages.

Why is the DOM slow?
-------------------

Unpacking the factors that might impact the speed of the DOM provides insights into optimization strategies, ensuring a seamless and responsive user experience.

Render Tree
-----------

The transition from the DOM tree to the render tree is a critical step in determining the visual representation of the web page and its elements.

Render Tree's Relation to the DOM Tree
--------------------------------------

Understanding how changes in the DOM tree reverberate through the render tree, shaping the visual display on the screen.

CSS Parsing
-----------

The intricate process of interpreting CSS rules, styling the elements in the DOM, and contributing to the overall aesthetics of the web page.

Layout
------

The determination of the position and size of each element on the web page, influenced by the applied styles.

Painting
--------

This is the final act in the rendering process, where the browser paints pixels on the screen, translating code into a visual masterpiece.

Trivia
------

Unearthing fascinating tidbits about browser mechanics and web intricacies adds a layer of curiosity to the exploration.

The Birth of the Web
--------------------
Embarking on a historical journey, we reflect on the inception of the web. From its humble beginnings to the intricate, interconnected network that defines our digital landscape today, this section provides a historical perspective on the evolution of the web.
