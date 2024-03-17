What happens when you type an URL in the browser and press Enter?


Ever wondered what happens when you type https://www.gooogle.com, in this article we are going to dive into the internal operations of a browser when a URL is entered into the search bar and Enter button is clicked.

1. Domain Name Resolution: The first thing that happens when you type "google.com" in your browser, is that your computer first checks the Domain Name System (DNS) to translate the human-readable domain name "www.google.com" into an IP address. This process allows your browser to locate the server that hosts the Google website, the DNS server is responsible for maintaining a database of domain names and their corresponding IP addresses.

2. Establishing a Connection: Once the IP address is obtained, your browser initiates a TCP (Transmission Control Protocol) connection to the server associated with that IP address. This connection is established through a series of messages exchanged between your computer and the server.

3. TLS Handshake: Since "https://" is specified in the URL, your browser and the server engage in a TLS (Transport Layer Security) handshake. This process involves negotiating encryption parameters and exchanging cryptographic keys to establish a secure connection. This encryption ensures that the data exchanged between your browser and the server remains private and secure.

4. Sending an HTTP Request: Once the secure connection is established, your browser sends an HTTP (Hypertext Transfer Protocol) request to the server for the Google homepage. This request includes various headers such as the type of browser being used, accepted content types, and any cookies associated with the domain.

5. Processing the Request: The server receives the HTTP request and processes it and any associated resources such as images, scripts, and stylesheets.

6. Sending an HTTP the Response: After processing the request, the server sends an HTTP response back to your browser that includes the HTML, CSS, and JavaScript files that make up the Google homepage. The HTML file contains the content of the webpage, while the CSS file defines the styling and layout of the page. The JavaScript file contains code that can be executed by your browser to modify the contents of the page or make additional requests to the server.

7. Rendering the Page: Your browser receives the HTTP response and begins to render the Google homepage. It interprets the HTML content received from the server, fetches additional resources referenced in the HTML (such as images and stylesheets), and constructs the visual representation of the webpage.

8. Displaying the Page: Finally, your browser displays the fully rendered Google homepage, allowing you to interact with it, This process may involve executing JavaScript code, handling user interactions, and dynamically updating the page content.

In conclusion, this sequence of steps allows to you access and view the Google homepage in your browser.
