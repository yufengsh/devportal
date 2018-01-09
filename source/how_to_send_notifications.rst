===========================================================
How To Send Notifications to Administrators in Citrix Cloud
===========================================================

Citrix Cloud (CC from now on) has a notification feature that sends messages to
the administrators using the main console:

|figure_1|

The notifications can be sent using a *REST API call*. This could be useful for
third parties trying to extend CC functionality.

Prerequisites.
==============

1) `Create an API client <create_api_client.html>`_.
2) Read the `authenticate using the API client <authenticate_api_client.html>`_,
   explanation of how to get Citrix Cloud bearer token.
3) Use the obtained bearer token, `as explained here <call_api_bearer_token.html>`_,
   in the section below.
4) You need the ``customer`` to use as first part of the URL path. Getting the
   value `is explained here <customer_id.html>`_.

Sending the notification.
=========================

The API call needed to send the notification is shown below:

Request
~~~~~~~
::

  POST https://notifications.citrixworkspacesapi.net/{customer}/notifications/items HTTP/1.1
  Accept: application/json
  Content-Type: application/json
  Authorization: CwsAuth bearer=<token-from-call-to-trust>

  {
    "destinationAdmin": "*",
    "component": "Citrix Cloud",
    "createdDate": "2016-04-22T21:11:25.006Z",
    "severity": "Information",
    "priority": "Normal",
    "content": [{
        "languageTag": "en-US",
        "title": "This is a title",
        "description": "This is a description"
     }]
  }

Response
~~~~~~~~
::

  HTTP/1.1 200 OK
  Cache-Control: no-cache
  Content-Length: 2623
  Content-Type: application/json; charset=utf-8
  Date: Fri, 23 Dec 2016 21:53:37 GMT
  X-Cws-TransactionId: 6c3db4d6-125f-4ea3-b938-882bc5dc3caf

  {
    "destinationAdmin": "*",
    "component": "Citrix Cloud",
    "createdDate": "2017-04-22T21:11:25.006Z",
    "categories": null,
    "severity": "Information",
    "eventId": null,
    "priority": "Normal",
    "content": [
      {
        "languageTag": "en-US",
        "title": "This is a title",
        "description": "This is a description",
        "detailUri": null
      }
    ],
    "data": null,
    "externalId": null
  }

The start (*) in the destination administrators field indicates the notification will be
sent to all administrators on the account.

The notification can be seen now on the Citrix Cloud console:

|figure_2|

.. |figure_1| image:: _static/how_to_send_notifications/figure_1.png
    :scale: 25%
.. |figure_2| image:: _static/how_to_send_notifications/figure_2.png
    :scale: 25%
