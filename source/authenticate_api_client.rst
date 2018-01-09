================================================
Authenticate to Citrix Cloud using an API client
================================================

Once you have created an API client, you can use that to authenticate to Citrix
Cloud.  When authenticated, you receive a bearer token that can be used for up to
one hour to call any of the REST APIs exposed by Citrix Cloud.

To authenticate, use the Citrix Cloud API:
`POST https://trust.citrixworkspacesapi.net/{customer}/tokens/clients
<https://trust.citrixworkspacesapi.net/Help/Api/POST-customer-tokens-clients>`_.

Notes
=====

* The ``customer`` parameter in the URL must be the special value ``root`` for this API.
* The request must include ``Accept: application/json`` header.
* The request must include ``Content-Type: application/json`` header.

REST Example
============

Request
~~~~~~~
::

  POST https://trust.citrixworkspacesapi.net/root/tokens/clients HTTP/1.1
  Accept: application/json
  Content-Type: application/json

  {
    "clientId": "3e..",
    "clientSecret": "f7.."
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
    "principal": "john.down@citrix.com",
    "subject": "16..",
    "token": "ey1..",
    "openIdToken": "ey1..",
    "expiresIn": 3600
  }

Interpreting the response
~~~~~~~~~~~~~~~~~~~~~~~~~

X-Cws-TransactionId header
--------------------------
This is used by Citrix Cloud to track related operations.  If you receive unexpected
results or errors, you can provide this ID to the Citrix Cloud support team to aid
in debugging.

principal
---------
This identifies the owner of the API client.  Note that this is not guaranteed to
be in the format of an email address.  Note also that the Principal is also not
guaranteed to be 'immutable'; it may change.

subject
-------
The subject is the immutable identifier of the client.  This is guaranteed never
to change for a given client.

token
-----
The Citrix Cloud bearer token.  This is the critical token required in order to make
further API calls to Citrix Cloud.

openIdToken
-----------
The OpenID-Connect identity token issued to the client.  OpenID-Connect is an open
standard and the token can be used to get additional information about the caller
identity and how they authenticated themselves.

expiresIn
---------
Seconds until the bearer token expires.  Bearer tokens obtained using an API client
cannot be refreshed; if the bearer token expires, request a new one using this API
a second time.

PowerShell Example
==================

Step 1:
~~~~~~~
Define a method to get a bearer token given a client id and secret.

.. code-block:: powershell

  function GetBearerToken {
    param (
      [Parameter(Mandatory=$true)]
      [string] $clientId,
      [Parameter(Mandatory=$true)]
      [string] $clientSecret
    )

    $postHeaders = @{"Content-Type"="application/json"}
    $body = @{
      "ClientId"=$clientId;
      "ClientSecret"=$clientSecret
    }
    $trustUrl = "https://trust.citrixworkspacesapi.net/root/tokens/clients"

    $response = Invoke-RestMethod -Uri $trustUrl -Method POST -Body (ConvertTo-Json $body) -Headers $postHeaders
    $bearerToken = $response.token

    return $bearerToken;
  }

Step 2:
~~~~~~~
In your script, call the newly defined method. 

.. code-block:: powershell

  $clientId = "b959ac67-..." #Replace with your clientId
  $clientSecret = "D8..."    #Replace with your clientSecret

  $bearerToken = GetBearerToken $clientId $clientSecret

C# Example
==========

Step 1:
~~~~~~~
Add a package reference to 'Json.NET' and 'System.Net.Http' via NuGet.  In
Visual Studio you can right-click on "References" and select "Manage NuGet
Packages" to add the references.

Step 2:
~~~~~~~
Add ``using`` references to your code.

.. code-block:: csharp

  using System.Net.Http;
  using System.Net.Http.Headers;
  using System.Text;
  using System.Threading.Tasks;
  using Newtonsoft.Json;

Step 3:
~~~~~~~
Define a method to get a bearer token given a client id and secret.

.. code-block:: csharp

  public static async Task<string> GetBearerToken(
      string clientId,
      string clientSecret)
  {
      var client = new HttpClient();
      client.DefaultRequestHeaders.Accept.ParseAdd("application/json");
      var payload = JsonConvert.SerializeObject(
           new
           {
               clientId = clientId,
               clientSecret = clientSecret
           }
      );
      var response = await client.PostAsync(
          "https://trust.citrixworkspacesapi.net/root/tokens/clients",
          new StringContent(payload, Encoding.UTF8, "application/json")
      );

      response.EnsureSuccessStatusCode();

      var content = await response.Content.ReadAsStringAsync();
      return JsonConvert.DeserializeObject<dynamic>(content).token;
  }

.. note:: If you experience a deadlock when executing this code, consult
   Microsoft documentation about how the captured context functions with ``async`` /
   ``await`` code.  You can usually resolve this using the ``ConfigureAwait(false)``
   method on the result of each of the ``await`` calls.  `More information can
   be found on MSDN <https://msdn.microsoft.com/en-us/magazine/jj991977.aspx>`_.

The result of this method is a bearer token that can be used for one hour to
call Citrix Cloud APIs.

Step 4:
~~~~~~~
In your code, call the newly defined method.  If your code is already using
``async``, you can ``await`` the result.

.. code-block:: csharp

  var bearerToken = await GetBearerToken(clientId, clientSecret);

If your code is synchronous, use the ``Task.Result`` property to await the
result.

.. code-block:: csharp

  var bearerToken = GetBearerToken(clientId, clientSecret).Result;

Once you have the bearer token you can begin to `call APIs using the bearer
token for authentication <call_api_bearer_token.html>`_.

Comments
========

.. disqus::
