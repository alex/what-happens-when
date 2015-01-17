Convert non-ASCII Unicode characters in hostname
------------------------------------------------

* The browser checks the hostname for characters that are not in ``a-z``,
  ``A-Z``, ``0-9``, ``-``, or ``.``.
* Since the hostname is ``google.com`` there won't be any, but if there were the
  browser would apply `Punycode`_ encoding to the hostname portion of the URL.
