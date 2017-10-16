============================
How to get all master images
============================

This *REST API* lists the details of all master images that were added to a customer's account.

Steps
=====
* Read the `prerequisites <prerequisites.html>`_ and ensure that you have the ``customerId``, ``siteId`` and ``bearer token``.
* Use the API in this document to get all master images.

REST Example
============

This example illustrates how to list all master images that were added to a customer’s account using REST API.

Request
~~~~~~~
::

  GET https://catalogs.apps.cloud.com/{customerId}/{siteId}/images?includeCitrixPrepared=false&includeCustomerPrepared=true HTTP/1.1
  Accept: application/json
  Content-Type: application/json
  Authorization: CwsAuth bearer=<token-from-prerequisites>
  
Response
~~~~~~~~
::

  HTTP/1.1 200 OK
  Content-Length: 2304
  Content-Type: application/json; charset=utf-8
  Date: Wed, 02 Aug 2017 20:35:07 GMT
  
  {
    "Overviews": [
      {
        "Id": "55cab14f-efb7-4bd6-bd9c-ebe991c9d165",
        "Name": "Finance Image",
        "State": "Ready",
        "SubState": "",
        "Status": "",
        "ErrorDetails": "",
        "TransactionId": "7ece7d3f-f1ba-480a-be25-f2d5352d0e38",
        "SubscriptionName": "Finance Azure Subscription",
        "ResourceGroup": "FinanceRG",
        "StorageAccount": "xaensfa5edge1gu4o1",
        "Region": "westus",
        "CitrixPrepared": false,
        "IsDeprecated": false,
        "LinkedCatalogs": 1
      },
      {
        "Id": "f14b839f-2297-498d-b969-a62e65a9203b",
        "Name": "Sales Image",
        "State": "Failed",
        "SubState": "ErrorMasterImageNoSupportedVdaFound",
        "Status": "The Virtual Delivery Agent (VDA) for Server OS was not found on the master image.  Please ensure that the Citrix VDA 7.11 or later is installed. Contact Citrix support and provide the Transaction ID if further assistance is required.",
        "ErrorDetails": "ImageVerification failed:CTX ERROR CODE:[ErrorNoSupportedVda]",
        "TransactionId": "7ece7d3f-f1ba-480a-be25-f2d5352d0e38",
        "SubscriptionName": "Sales Azure Subscription",
        "ResourceGroup": "SalesRG",
        "StorageAccount": "xaensfa5edge1gu4o1",
        "Region": "westus",
        "CitrixPrepared": false,
        "IsDeprecated": false,
        "LinkedCatalogs": 0
      }
    ]
  }

Interpreting the request URL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

includeCitrixPrepared
---------------------
Set this to true to have the Citrix prepared images included in the response.

includeCustomerPrepared
-----------------------
This should always be set to true to list all master images.

Interpreting the response
~~~~~~~~~~~~~~~~~~~~~~~~~

The response is a JSON with a list of all your requested images.

==================   ================================================================================
Property Name        | Description
==================   ================================================================================
Id                   | The ID of the master image.
Name                 | The friendly name of the master image.
State                | The current state of the master image. A state of ``Ready`` indicates that the 
                     | image is ready to be used by a catalog.
SubState             | The current sub state of the master image. This gives more visibility into the
                     | current state of the image.
Status               | The friendly message describing the current state of the image when the image
                     | has not yet transitioned to ``Ready`` state.
TransactionId        | Provide this transactionId to Citrix Support if you need help with this image.
CitrixPrepared       | Indicates if this is a Citrix prepared image or not. This will be false for 
                     | all images added by the customer.
IsDeprecated         | Indicates if this is a Citrix prepared deprecated image or not. This will be 
                     | false for all images added by the customer.
LinkedCatalogs       | The number of catalogs using this image. This will be 0 for an image that 
                     | has just been added and has never been used by a catalog.
==================   ================================================================================

Powershell Example
==================

This example illustrates how to list all master images that were added to a customer’s account using PowerShell.

.. code-block:: powershell

  function GetAllMasterImages {
    param (
      [Parameter(Mandatory=$true)]
      [string] $customerId,
      [Parameter(Mandatory=$true)]
      [string] $siteId,
      [Parameter(Mandatory=$true)]
      [string] $bearerToken
    )
    $requestUri = [string]::Format("https://catalogs.apps.cloud.com/{0}/{1}/images?includeCitrixPrepared=false&includeCustomerPrepared=true", $customerId, $siteId)
    $headers = @{"Accept"="application/json";
                 "Authorization"="CWSAuth bearer=$bearerToken"}

    $response = Invoke-RestMethod -Uri $requestUri -Method GET -Headers $headers
    return $response
  }
  
  $customerId = "exampleCust" #Replace with your customerId
  $siteId = "61603f15-cdf9-4c7f-99ff-91636601a795" #Replace with your site ID
  $bearerToken = "ey.." #See Prerequisites for all API calls section for a sample of how to get your bearer token
  $response = GetAllMasterImages $customerId $siteId $bearerToken
  
C# Example
==========

This example illustrates how to list all master images that were added to a customer’s account using C#.
  
.. code-block:: csharp

  public static async Task<string> GetAllMasterImages(
      string customerId,
      string siteId,
      string bearerToken)
  {   
      var requestUri = string.Format("https://catalogs.apps.cloud.com/{0}/{1}/images?includeCitrixPrepared=false&includeCustomerPrepared=true", customerId, siteId);
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

See `how to get a master image <how_to_get_a_master_image.html>`_ to get the details of a specific master image.

Comments
========

.. disqus::