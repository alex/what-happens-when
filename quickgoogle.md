Almost everyday we try to remember something, or try to get some information about something and for most of us we immediately hop on our phones/computers and ask our tried an true friend....at least the one we all had before chatGPT. 

We ask google. 

Now it's basically a click of a button for us but there are a lot of processes that occur in the few seconds (considering you're not using glo network....lol)  before we can get on the google.com page and I'd like to explain the process to you. I will try to make this as easy to understand as possible.


## Step 1 : DNS (Domain Name System) Request ##
When you type in "google.com" and press enter, your computer which does not understand english, needs to immediately translate this text into something it can understand and work with and that is an IP address. Just think of DNS as Duolingo converting one language to another, in this case its human-text to IP address (which is the language the computer understands).  In order for your computer to get the IP address using the DNS it needs to know which authoritative name server to ask. This brings us to the 3 levels of DNS heirarchy.

Root Server : at this stage your computer looks at the website URL "google.com" and forwards the query to the correct top-level domain server and since this is a dot-com website (i.e. it ends with ".com"), it is forwarded to the dot-com top-level domain server.

Top-Level Domain Server : there are a various types of top-level domain servers such as dot-com, dot-net, 
dot-org etc. In this case, we will be in the dot-com top-level domain server. This then sends it to the exact name server i.e the google.com authoritative name server.

Authoritative Name Server : at this stage, the google.com text will finally be converted to an IP address (which is what the computer can understand)


## Step 2: TCP (Transmission Control Protocol) /IP (Internet Protocol) ##
Now that we have the IP address, your browser uses the TCP/IP to connect to the server. The TCP and the IP play different yet complementary roles in the process. 

TCP : ensures that the data transferred between your browser (i.e. Opera, Google Chrome, Mozilla Firefox etc) and the server is reliable and delivered in the right order.

IP : helps data find its way through the vast network of interconnected computers and devices on the internet.


## Step 3: Firewall ##
A firewall is like a gate, it checks credentials and ensures only authorized persons (in this case, websites) have access to your computer via the internet. It monitors both incoming and outgoing network traffic.


## Step 4 : HTTPS (Hypertext Transfer Protocol Secure) /SSL (Secure Sockets Layer) ##
When you type in "google.com" on your browser, you will notice that when it opens the website, it comes out showing "https://www.google.com" or in more recent browser updates you will see a small padlock icon beside the now "www.google.com" website URL. The "https" or padlock beside the URL signifies that there is an extra layer of security because the data exchanges between the browser and the server have been encrypted. This encrption takes place with the help of the SSL. Basically your browser and server share a key that only them can use to unlock the date sent this way, your data is protected.


## Step 5: Load Balancer ##
As the name implies it helps in the balancing of load across multiple servers. Assuming google has 2 servers, the load balancer ensures that the traffic is distributed as efficiently as possible across all 2 servers so that no one is over-worked or under-utilized.


Step 6 : Web Server
With the help of the load balancer, the selected web server receives your HTTP request (i.e. the message your browser has sent to the web server asking for a particular webpage) in this case the display of the google.com webpage


## Step 7 : Application Server ##
If the webpage (in this case google.com) has dynamic content then the web server will reach out to the application server. 

What is dynamic content you might ask. 

It is content that changes based on the user. In the case of google.com, at the point when the "google.com" webpage is opened on your browser, it is static as the webpage is the same for everyone but the minute you start typing into the search bar, it becomes dynamic as the information generated is specifically based on what you searched for, it is not the same information across everyones browser. Fo instance, person A can be searching for "how to cook" and person B can be searching for "real housewives of beverly hills". Same website, different searches.


## Step 8 : Database Server ## 
If your search further involves you fetching data then the application server sends a links up with the database server to retreive the necessary information or if it involves storing information then the database does that also.


When all the necessary data has been collected, the information is compiled by the web server and sent to the browser as a HTTP response to the browsers HTTP request. Once it gets to the DNS it converts the IP address back to the website and displays information that is readable and understood by the client.

And there you have it - a simplified explanation of the journey from pressing "Enter" to accessing the google homepage.
