================================================
Create a web hook to receive Citrix Cloud events
================================================

Citrix Cloud raises events for various activities, including:

* Administrator logon
* Create, update, and delete of some objects
* Notifications raised
* Connector health check requests and responses

Customers may hook into these events by configuring Citrix Cloud to call a URL of their choosing when the events occur.

Prerequisites.
==============

1) `Create an API client </create_api_client.html>`_.
2) Read the `authenticate using the API client </authenticate_api_client.html>`_,
   explanation of how to get Citrix Cloud bearer token.
3) Use the obtained bearer token, `as explained here </call_api_bearer_token.html>`_,
   in the section below.
4) You need the ``customer`` to use as first part of the URL path. Getting the
   value `is explained here </customer_id.html>`_.

Creating a web hook
===================

The API call needed to create a web hook is shown below:

Request
~~~~~~~
::

  POST https://core.citrixworkspacesapi.net/acme/serviceStates HTTP/1.1
  Accept: application/json
  Authorization: CwsAuth Bearer=<token-from-call-to-trust>
  Content-Type: application/json

  {
    "customerId": "acme",
    "description": "my web hook",
    "url": "https://mycallback.myserver.com/myapi",
    "authHeaders": "Basic ...",
    "changeType": "*",
    "eventType": "*"
  }

.. note::
    Replace ``acme`` above with the ID of the customer you wish to query, both in the URL and in the POST body.

    Replace ``url`` above with the URL of your service's REST endpoint.  This endpoint will be called with the HTTP ``POST`` method.

Interpreting the request
~~~~~~~~~~~~~~~~~~~~~~~~
``customerId`` must be the same customer ID used in the request URL, and must be a customer for which the bearer token is authorized.

``description`` can be an arbitrary string, useful to locate specific web hooks later.

``url`` is the URL of your service's REST endpoint.  This endpoint will be called with the HTTP ``POST`` method and must be internet-accessible.

``authHeaders`` is optional, but defines the ``Authorization`` header value that will be set when your service's REST endpoint is called.  If not defined, no Authorization header will be set.

``changeType`` allows you to listen only to specific kinds of changes:

    **Create**: invoked when objects are created.  This includes when Notifications are created.

    **Update**: invoked when objects are updated.

    **Delete**: invoked when objects are deleted.

    **HealthDataRequest**: indicates that the system is requesting health data from connectors.

    **HealthDataResponse**: the health data returned in response to a health data request.

    **\***: Asterisk indicates that all types of changes should be listened to.

``eventType`` allows you to listen only to events on specific types of objects:

    **Administrator**: Citrix Cloud administrator events.

    **ClientAdministrator**: Secure client events.

    **ResourceLocation**: Resource location changes.

    **EdgeServers**: Connectors.

    **Notifications**: Notifications.

    **Forests**: Active Directory forests.

    **Domains**: Active Directory domains.

    **Entitlement**: Entitlements, which may change a service's `entitlement status <get_service_entitlement_status.html>`_.

    **Subscriptions**: User assignments to resources in the Library.

    **Webhooks**: Web hooks.

    **UiEvent:CCConsole:AdministratorLogon**: Administrator logon.

    **\***: Asterisk indicates that all types of objects should be listened to.

.. note:: Both ``changeType`` and ``eventType`` are extensible.  Values may be used that are not yet documented.


Response
~~~~~~~~
::

  HTTP/1.1 200 OK
  Cache-Control: no-cache
  Content-Length: 2031
  Content-Type: application/json; charset=utf-8
  Date: Fri, 23 Dec 2016 22:24:00 GMT
  X-Cws-TransactionId: bfc9b56c-bcd0-4cf1-9ea1-3da4d48a81c0

  {
      "id": "dcc7cb2f-2eb6-47a9-b738-7c86290b6f1f",
      "customerId": "acme",
      "subscriptionType": "Webhook",
      "url": "https://mycallback.myserver.com/myapi",
      "description": "synergy demo",
      "authHeaders": "Basic ...",
      "eventType": "*",
      "changeType": "*",
      "createdBy": "my.email@mycompany.com",
      "eTag": "W/\"datetime'2018-04-23T22%3A05%3A25.0107692Z'\""
  }

.. note:: Some response properties are omitted in this documentation because they are not relevant to external callers.  Undocumented properties are subject to be removed or changed at any time.



Callbacks
=========

Common callback request body
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Each time the web hook is called, the request body will look something like:

::

  {
    "CustomerId": "acme",
    "Type": "Notifications",
    "ChangeType": "Create",
    "TransactionId": "9cc8c8d0-3d24-46f4-b63c-8c5d092b7205",
    "Identity": "tom.kludy@citrix.com",
    "BeforeChange": <beforeChange>,
    "AfterChange": <afterChange>,
    "TimeStamp": "2018-04-24T15:15:49.493Z"
  }

.. note:: Some response properties are omitted in this documentation because they are not relevant to external callers.  Undocumented properties are subject to be removed or changed at any time.

``CustomerId`` is the Citrix Cloud customer ID.

``Type`` is the type of the object on which the event occurred.

``ChangeType`` is the type of event. For example, **Create**, **Update**, etc.

``TransactionId`` identifies the high-level operation that caused the event.  This can be used to correlate multiple events if they happen as part of a single transaction, or can also be used when opening a Citrix Cloud support ticket to help Citrix locate further information about calls that fail.

``Identity`` is the identity of the person or service that triggered the event.  When a person triggers an event through a UI action or API call, this will be the identity of that person.  If an event happens as part of a background operation in Citrix Cloud, this will be the identity of the service that triggered the background operation.

``BeforeChange`` and ``AfterChange`` indicate the state of the object before and after the event occurred.  Not all events use these fields, and the format of the fields varies for each event that can be triggered.  When the fields are used, their content is string-encoded JSON, for example:

::

    "AfterChange": "{\"PublishNotificationId\":\"2518777178829331203_YB2Y1\",\"Destination\":\"acme;*\",\"Component\":\"Citrix Cloud\",\"CreatedDate\":\"2018-04-24T15:15:17.0668796Z\",\"Categories\":[],\"Severity\":0,\"EventId\":\"7df85e7a-79b8-4cc2-abe0-c2f9ba2ec715\",\"Priority\":0,\"Content\":[{\"LanguageTag\":\"en-US\",\"Title\":\"This is a title\",\"Description\":\"This is a description\",\"DetailUri\":null}],\"Data\":null,\"ExternalId\":null}",


``TimeStamp`` indicates the date and time when the event occurred, in RFC 3339 format.



Type: "Notifications" / ChangeType: "Create"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Called when a notification is raised.  The ``BeforeChange`` property will be null, and the ``AfterChange`` property will be string-encoded JSON in this format:

::

  {
    "PublishNotificationId": "2518777178829331203_YB2Y1",
    "Destination": "acme;*",
    "Component": "Citrix Cloud",
    "CreatedDate": "2018-04-24T15:15:17.0668796Z",
    "Categories": [],
    "Severity": 0,
    "EventId": "7df85e7a-79b8-4cc2-abe0-c2f9ba2ec715",
    "Priority": 0,
    "Content": [
        {
            "LanguageTag": "en-US",
            "Title": "This is a title",
            "Description": "This is a description",
            "DetailUri": null
        }
    ],
    "Data": null,
    "ExternalId": null
  }

The properties are:

``Destination``: The customer and administrator targeted by the notification.  Customer and administrator are separated with a semicolon (;), and asterisk (\*) means "all".

``Component``: Component which raised the notification.

``CreatedDate``: Date when the notification was created.  Note that this is not validated; callers can specify any date and time they like.  The ``TimeStamp`` in the outer payload may be a more accurate indicator.

``Categories``: Optional list of categories which the notification falls under.

``Severity``: Severity of the event.

    **0**: Informational.

    **1**: Success.

    **2**: Warning.

    **3**: Error.

``Priority``: Priority of the event.

    **0**: Normal.

    **1**: Low.

    **2**: High.

    **3**: Urgent.

``EventId``: ID of the event.  Note that this is not validated; callers can create multiple events with the same ID.

``Content``: The notification content.  This is an array and can contain the message in multiple languages.

Type: "UiEvent:CCConsole:AdministratorLogon" / ChangeType: "Create"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Called when an admin logs into the customer.  The ``BeforeChange`` property will be null, and the ``AfterChange`` property will be string-encoded JSON in this format:

::

  {
    "CustomerId": "acme",
    "LogonTime": "2018-04-24T21:21:42.521Z",
    "OrgId": "1487a6",
    "Principal": "joe@mycompany.com"
  }

The properties are:

``CustomerId``: The Citrix Cloud customer that the admin logged into.

``LogonTime``: The date and time the administrator logon occurred, in RFC 3339 format.

``OrgId``: The Citrix OrgID for the customer that the admin logged into.

``Principal``: The identity of the admin that logged in.  Note: this is not guaranteed to be in email format.


Comments
========

.. disqus::
