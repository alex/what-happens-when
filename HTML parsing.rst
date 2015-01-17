HTML parsing...
-----------------

* Fetch contents of requested document from network layer in 8kb chunks
* Parse HTML document
* Convert elements to DOM nodes in the content tree
* Prefetch external resources linked to the page (CSS, Images, JavaScript files,
  etc.)
