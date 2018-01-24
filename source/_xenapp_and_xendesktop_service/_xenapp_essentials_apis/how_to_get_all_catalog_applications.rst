===================================
How to get all catalog applications
===================================

This *REST API* gets the details of all the applications published for a catalog in a customer's account.

Steps
=====
* Read the `prerequisites <../prerequisites.html>`_ and ensure that you have the ``customerId``, ``siteId`` and ``bearer token``.
* If you do not have the ``catalogId``, see `how to get details of all the catalogs <how_to_get_details_of_all_the_catalogs.html>`_ to get the details of all your catalogs.
* Use the API in this document to get details of all applications for a catalog.

REST Example
============

This example illustrates how to get the details of all applications published for a catalog in a customer’s account using REST API.

Request
~~~~~~~
::

  GET https://catalogs.apps.cloud.com/{customerId}/{siteId}/catalogs/{catalogId}/apps HTTP/1.1
  Accept: application/json
  Content-Type: application/json
  Authorization: CwsAuth bearer=<token-from-prerequisites>

Response
~~~~~~~~
::

  HTTP/1.1 200 OK
  Content-Length: 6676
  Content-Type: application/json; charset=utf-8
  Date: "Tue, 26 September 2017 19:20:13 GMT"

  {
    "Applications": [
      {
        "State": "Published",
        "Id": "522e06cf-974e-4a7a-bd26-06faa010288f",
        "Name": "Calculator",
        "ApplicationPath": "C:\\Windows\\system32\\calc.exe",
        "WorkingDirectory": "%windir%\\system32",
        "Compressedb64Icon": "iVBORw0KGgoAAA..."
      },
      {
        "State": "Published",
        "Id": "73352308-ea31-41dc-ab95-9fa524e67fed",
        "Name": "Command Prompt",
        "ApplicationPath": "C:\\Windows\\system32\\cmd.exe",
        "Compressedb64Icon": "iVBORw0KGgoAAA..."
        "CommandLineParams": "start notepad"
      }
    ],
  }

Interpreting the response
~~~~~~~~~~~~~~~~~~~~~~~~~

The response is a JSON with a list of all your published applications for the catalog.

==================== ======================================================================
Property Name        | Description
==================== ======================================================================
State                | Publish state of the application. This is always set to ``Published``.
Id                   | The ID of the application. This is the ID to pass to any API that
                     | accepts an appId.
Name                 | The name of the application. This name matches the start menu
                     | discovered applications on a master image.
ApplicationPath      | The path of the application executable on the master image.
WorkingDirectory     | By default, this path is the same as the path in the ApplicationPath
                     | field. To run the application from a different directory, add an
                     | absolute path to this field.
Compressedb64Icon    | The compressed application icon in Base64 format. This is for
                     | displaying the icon in the UI.
CommandLineParams    | The command line parameters for the app at startup.
==================== ======================================================================

PowerShell Example
==================

This example illustrates how to get the details of all applications published for a catalog in a customer’s account using PowerShell.

.. code-block:: powershell

  function GetCatalogApplications {
    param (
      [Parameter(Mandatory=$true)]
      [string] $customerId,
      [Parameter(Mandatory=$true)]
      [string] $siteId,
      [Parameter(Mandatory=$true)]
      [string] $catalogId,
      [Parameter(Mandatory=$true)]
      [string] $bearerToken
    )
    $requestUri = [string]::Format("https://catalogs.apps.cloud.com/{0}/{1}/catalogs/{2}/apps", $customerId, $siteId, $catalogId)
    $headers = @{"Accept"="application/json";
                 "Authorization"="CwsAuth bearer=$bearerToken"}

    $response = Invoke-RestMethod -Uri $requestUri -Method GET -Headers $headers
    return $response
  }

  $customerId = "customer1" #Replace with your customerId
  $siteId = "61603f15-cdf9-4c7f-99ff-91636601a795" #Replace with your site ID
  $catalogId = "8d352ba7-1917-41c3-95e5-50f436be8968" #Replace with your catalog ID
  $bearerToken = "ey1.." #See Prerequisites for all API calls section for a sample of how to get your bearer token
  $response = GetCatalogApplications $customerId $siteId $catalogId $bearerToken

C# Example
==========

This example illustrates how to get the details of all applications published for a catalog in a customer’s account using C#.

.. code-block:: csharp

  public static async Task<string> GetCatalogApplications(
      string customerId,
      string siteId,
      string catalogId,
      string bearerToken)
  {
      var requestUri = string.Format("https://catalogs.apps.cloud.com/{0}/{1}/catalogs/{2}/apps", customerId, siteId, catalogId);
      using (var client = new HttpClient())
      {
          client.DefaultRequestHeaders.Accept.ParseAdd("application/json");
          client.DefaultRequestHeaders.Authorization =
                     new AuthenticationHeaderValue("CwsAuth", "Bearer=" + bearerToken);

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
