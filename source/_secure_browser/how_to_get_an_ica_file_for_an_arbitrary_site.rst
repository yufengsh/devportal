===========================================================
How To Get an ICA File for an Arbitrary Site - Tech Preview
===========================================================

Secure Browser provides an API for launching arbitrary addresses within the hosted environment, without permanently publishing the URL. The returned value is an ICA file that may be passed to any Citrix Receiver in order to launch the session, typically by saving the ICA data to a local file on the user's endpoint and invoking the installed Receiver.

Prerequisites
=============

.. include:: prerequisites.txt

REST Example
============

This example illustrates how to create an ICA file using the REST API.

Request
-------
.. sourcecode:: http

  POST https://session.browser.cloud.com/{customer}/singleuse/icafile HTTP/1.1
  Accept: application/json
  Content-Type: application/json
  Authorization: CwsAuth bearer=<token-from-call-to-trust>

  {    
    "Address": "https://www.citrix.com",
    "Name": "Citrix",
    "Geo": "wus",
    "Whitelist": "*citrix.com:443",
    "Policies": {
      "CopyPaste": true,
      "NonKiosk": true
    }
  }

Response
--------
.. sourcecode:: http

  HTTP/1.1 200 OK
  Content-Length: 1496
  Content-Type: application/json; charset=utf-8

  {
    "IcaFile": "[Encoding]..."
  }

Interpreting the request
------------------------

.. include:: single_use_launch_model.txt

Interpreting the response
-------------------------

The response is a JSON object with a single property, ``IcaFile``. Any version of Citrix Receiver can use this data to launch the requested session, typically by saving it to a local file and then invoking an installed copy of Receiver.

Comments
========

.. disqus::