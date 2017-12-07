=============================================
Call an API using a Citrix Cloud bearer token
=============================================

Use the ``token`` property returned from the authentication API in the request
Authorization header, prefixed by ``CwsAuth Bearer=`` like this:

``Authorization: CwsAuth Bearer=ey1..``


Response status codes
=====================
Each API defines success and failure status codes independently, but a few
response codes are common across Citrix Cloud, and are used for determining whether
authentication was successful.

401 Unauthorized
~~~~~~~~~~~~~~~~
Indicates that the caller identity is unknown.  This could be because a bearer
token was not present in the request, or the bearer token was invalid or expired.
The caller should try to authenticate and retry the request using a valid and
authorized bearer token.

403 Forbidden
~~~~~~~~~~~~~
Indicates that the caller identity is known, but access control restricts the
caller from accessing the requested resource.  This indicates that the bearer
token was present and valid, but did not have adequate permissions to call the
API.  The caller is unlikely to be successful retrying the call to the API,
unless the caller's permissions are changed.

429 Too Many Requests
~~~~~~~~~~~~~~~~~~~~~
Indicates that the caller is making too many requests in a short time period,
and is being rate-limited.  Slow down the rate of requests from the caller.

Other 400-series error codes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Indicates that the caller is authorized to call the API, but is passing bad or
unexpected data.  The caller should not retry the call without changing the
request to address the problem.

503 Service Unavailable
~~~~~~~~~~~~~~~~~~~~~~~
The server is overloaded.  The caller may retry the call after waiting some time
for the server to become responsive again.

REST example
============

Request
~~~~~~~
::

  GET https://registry.citrixworkspacesapi.net/acme/resourcelocations HTTP/1.1
  Accept: application/json
  Authorization: CwsAuth Bearer=ey1..

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
        "id": "03..",
        "name": "AWS - USA - West"
      },
      {
        "id": "23..",
        "name": "Azure  - Europe  - North"
      },
      {
        "id": "42..",
        "name": "Azure - US - West"
      },
      {
        "id": "75..",
        "name": "Citrite - Secure Browser"
      }
    ]
  }


C# Example
==========

This example illustrates how to use a `bearer token <authenticate_api_client.html>`_
to enumerate the customer's resource locations.  Note that the Authorization
header scheme is "CwsAuth" and the value is "Bearer=\ *[bearerToken]*".

.. code-block:: csharp

  public static async Task<string> GetResourceLocations(string bearerToken)
  {
      var client = new HttpClient();
      client.DefaultRequestHeaders.Accept.ParseAdd("application/json");
      client.DefaultRequestHeaders.Authorization =
                new AuthenticationHeaderValue("CwsAuth", "Bearer=" + bearerToken);

      var response = await client.GetAsync(
          "https://registry.citrixworkspacesapi.net/acme/resourcelocations"
      );

      response.EnsureSuccessStatusCode();

      var content = await response.Content.ReadAsStringAsync();

      // Parsing the JSON content is left as an exercise to the reader.
      // Consult Json.NET documentation on newtonsoft.com for more information.

      return content;
  }


Comments
========

.. disqus::
