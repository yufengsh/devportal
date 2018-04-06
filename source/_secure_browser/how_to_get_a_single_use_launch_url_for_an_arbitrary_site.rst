============================================================
How To Get a Launch URL for an Arbitrary Site - Tech Preview
============================================================

Secure Browser provides an API for launching arbitrary addresses within the hosted environment, without permanently publishing the URL. The returned value is a launch.cloud.com URL which may only be used once and will time out if not used within ten minutes.

Prerequisites
=============

.. include:: prerequisites.txt

REST Example
============

This example illustrates how to create a single-use launch.cloud.com URL using the REST API.

Request
-------
.. sourcecode:: http

  POST https://session.browser.cloud.com/{customer}/singleuse/launchurl HTTP/1.1
  Accept: application/json
  Content-Type: application/json
  Authorization: CwsAuth bearer=<token-from-call-to-trust>

  {    
    "Address": "https://www.citrix.com",
    "Name": "Citrix",
    "UserIdentity": {
      "Username": "user@example.com",
    }
  }

Response
--------
.. sourcecode:: http

  HTTP/1.1 200 OK
  Content-Length: 89
  Content-Type: application/json; charset=utf-8

  {
    "LaunchUrl": "https://launch.cloud.com/{customer}?ticket=75DC45..."
  }

Interpreting the request
------------------------

.. include:: single_use_launch_model.txt

Interpreting the response
-------------------------

The response is a JSON object with a single property, ``LaunchUrl``. This URL may be loaded into any web browser in order to launch the hosted session with an embedded Citrix Receiver for Web.

Comments
========

.. disqus::