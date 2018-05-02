==========================================================
Get details about the resource locations within a customer
==========================================================

Applications and scripts that interface with Citrix Cloud often need to know the
resource locations that the customer has created.

Additionally, in order to silently install Citrix Cloud connectors when you have
more than one resource location, the ID of the target resource location must be
passed on the command-line of the installer.  Although this ID is available in
the CC user interface, it is sometimes desirable to get this information
programmatically.

Prerequisites.
==============

1) `Create an API client </create_api_client.html>`_.
2) Read the `authenticate using the API client </authenticate_api_client.html>`_,
   explanation of how to get Citrix Cloud bearer token.
3) Use the obtained bearer token, `as explained here </call_api_bearer_token.html>`_,
   in the section below.
4) You need the ``customer`` to use as first part of the URL path. Getting the
   value `is explained here </customer_id.html>`_.

Getting Resource Locations.
===========================

The API call needed to get resource locations is shown below:

Request
~~~~~~~
::

  GET https://registry.citrixworkspacesapi.net/acme/resourcelocations HTTP/1.1
  Accept: application/json
  Authorization: CwsAuth Bearer=<token-from-call-to-trust>

.. note:: Replace ``acme`` above with the ID of the customer you wish to query.

Response
~~~~~~~~
::

  HTTP/1.1 200 OK
  Cache-Control: no-cache
  Content-Length: 316
  Content-Type: application/json; charset=utf-8
  Date: Fri, 23 Dec 2016 22:24:00 GMT
  X-Cws-TransactionId: bfc9b56c-bcd0-4cf1-9ea1-3da4d48a81c0

  {
    "items": [
      {
        "id": "03...",
        "name": "AWS - USA - West"
      },
      {
        "id": "23...",
        "name": "Azure  - Europe  - North"
      },
      ...
    ]
  }


C# Example
==========

This example illustrates how to enumerate the customer's resource locations.

.. code-block:: csharp

  public static async Task<ItemCollection<ResourceLocationResponse>> GetResourceLocations(string bearerToken, string customer)
  {
      var client = new HttpClient();
      client.DefaultRequestHeaders.Accept.ParseAdd("application/json");
      client.DefaultRequestHeaders.Authorization =
          new AuthenticationHeaderValue("CwsAuth", "Bearer=" + bearerToken);

      var response = await client.GetAsync(
          "https://registry.citrixworkspacesapi.net/" + customer + "/resourcelocations"
      );

      response.EnsureSuccessStatusCode();

      var content = await response.Content.ReadAsStringAsync();
      return JsonConvert.DeserializeObject<ItemCollection<ResourceLocationResponse>>(content);
  }

  public class ItemCollection<T>
  {
      public IList<T> Items { get; set; }
  }

  public class ResourceLocationResponse
  {
      public string Id { get; set; }
      public string Name { get; set; }
  }

Comments
========

.. disqus::
