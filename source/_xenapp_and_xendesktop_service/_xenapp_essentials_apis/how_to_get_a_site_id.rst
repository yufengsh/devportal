=====================
How to get a site ID
=====================

This *REST API* lists all the sites assigned to a customer. Currently the customers have only one site assigned.

Steps
=====
* Read the `prerequisites <prerequisites.html>`_ and ensure that you have the ``customerId`` and ``bearer token``.
* Invoke the API in this document to get the sites.

REST Example
============

This example illustrates how to get a customer's site identifiers using REST API.

Request
~~~~~~~
::

  GET https://catalogs.apps.cloud.com/{customerId}/sites HTTP/1.1
  Accept: application/json
  Content-Type: application/json
  Authorization: CwsAuth bearer=<token-from-prerequisites>
  
Response
~~~~~~~~
::

  HTTP/1.1 200 OK
  Content-Length: 132
  Content-Type: application/json; charset=utf-8
  Date: Fri, 18 Aug 2017 14:52:44 GMT
  
  {
    "Sites": [
      {
        "Id": "61603f15-cdf9-4c7f-99ff-91636601a795",
        "DisplayName": "XenApp Essentials"
      }
    ]
  }

Interpreting the response
~~~~~~~~~~~~~~~~~~~~~~~~~
The response is a JSON with a list of all your sites. Currently all customers have only one site.

==================   ================================================================================
Property Name        | Description
==================   ================================================================================
Id                   | The site ID. This is the ID that needs to be passed in all the requests urls.
DisplayName          | The display name of the site.
==================   ================================================================================

Powershell Example
==================

This example illustrates how to get a customer's site identifiers using Powershell.

.. code-block:: powershell

  function GetSites {
    param (
      [Parameter(Mandatory=$true)]
      [string] $customerId,
      [Parameter(Mandatory=$true)]
      [string] $bearerToken
    )
    $requestUri = [string]::Format("https://catalogs.apps.cloud.com/{0}/sites", $customerId)
    $headers = @{"Accept"="application/json";
                 "Authorization"="CWSAuth bearer=$bearerToken"}

    $response = Invoke-RestMethod -Uri $requestUri -Method GET -Headers $headers
    return $response
  }
  
  $customerId = "exampleCust" #Replace with your customerId
  $bearerToken = "ey.." #See Prerequisites for all API calls section for a sample of how to get your bearer token
  $response = GetSites $customerId $bearerToken

C# Example
==========

This example illustrates how to get a customer's site identifiers using C#.
  
.. code-block:: csharp

  public static async Task<string> GetSites(
      string customerId,
      string bearerToken)
  {   
      var requestUri = string.Format("https://catalogs.apps.cloud.com/{0}/sites", customerId);
      using (var client = new HttpClient())
      {
          client.DefaultRequestHeaders.Accept.ParseAdd("application/json");
          client.DefaultRequestHeaders.Authorization =
                     new AuthenticationHeaderValue("CWSAuth", "Bearer=" + bearerToken);

          var response = await client.GetAsync(requestUri);

          if (response != null)
          {
              var content = await response.Content.ReadAsStringAsync();

              // Parsing the JSON content is left as an exercise to the reader.
              // Consult Json.NET documentation on newtonsoft.com for more information.

              return content;
          }

          return null;
      }
  }

Comments
========

.. disqus::