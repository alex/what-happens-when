## Handling of HTTPS Protocol

When a URL with HTTPS is requested, a series of steps known as the "SSL/TLS handshake" is initiated:

1. **Client Hello**: The client sends a Client Hello message with its SSL/TLS version, preferred cipher suites, and a random byte string known as the "client random".
2. **Server Hello**: The server responds with a Server Hello message, including its SSL/TLS version, chosen cipher suite, and a "server random".
3. **Certificate & Server Key Exchange**: The server sends its SSL certificate for the client to verify. It also sends a Server Key Exchange message and a Server Hello Done message.
4. **Client Key Exchange**: The client sends a Client Key Exchange message, which either contains a pre-master secret key encrypted with the server’s public key, or provides parameters so both the client and server can agree upon the pre-master secret key.
5. **Change Cipher Spec & Finished**: Both the client and server send a Change Cipher Spec message to switch to the chosen cipher suite. They also send a Finished message to verify the handshake's success.

After the SSL/TLS handshake is completed, data transferred between the client and the server is encrypted, ensuring a secure connection.

