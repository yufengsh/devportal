=========================
How to get a master image
=========================

This *REST API* gets the details of a master image that was previously added to a customer's account.

Steps
=====
* Read the `prerequisites <prerequisites.html>`_ and ensure that you have the ``customerId``, ``siteId`` and ``bearer token``.
* If you do not have the ``imageId``, see `how to get all master images <how_to_get_all_master_images.html>`_ to get the image id of all your posted images.
* Use the API in this document to get the master image.

REST Example
============

This example illustrates how to get a master image that was added to a customer's account using REST API.

Request
~~~~~~~
::

  GET https://catalogs.apps.cloud.com/{customerId}/{siteId}/images/{imageId} HTTP/1.1
  Accept: application/json
  Content-Type: application/json
  Authorization: CwsAuth bearer=<token-from-prerequisites>
  
Response
~~~~~~~~
::

  HTTP/1.1 200 OK
  Content-Length: 1616
  Content-Type: application/json; charset=utf-8
  Date: Wed, 02 Aug 2017 20:35:07 GMT
  
  {
    "Catalogs": [
      {
        "CatalogId": "198a1538-ec7a-48db-8b38-fca55542f4f3",
        "CatalogName": "Finance Catalog",
        "State": "Active",
        "AzureSubscription": "Finance Azure Subscription"
      }
    ],
    "Applications": [
      {
        "Id": "fc9d89b5-9914-4367-b32a-2a48b1db40af",
        "Name": "Acrobat Reader DC",
        "ApplicationPath": "C:\\Program Files (x86)\\Adobe\\Acrobat Reader DC\\Reader\\AcroRd32.exe",
        "WorkingDirectory": "%ProgramFiles(x86)%\\Adobe",
        "Description": "Read pdf documents",
        "Compressedb64Icon": "..."
        "CommandLineParams": "doc.pdf"
      }
	],
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
  }

Interpreting the request URL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

imageId
-------
The ``imageId`` should be replaced with the actual image ID of the master image. You must have obtained the ``imageId`` as response when invoking APIs listed in `how to add a master image <how_to_add_a_master_image.html>`_ or `how to get all master images <how_to_get_all_master_images.html>`_ sections.

Interpreting the response
~~~~~~~~~~~~~~~~~~~~~~~~~

==================   ================================================================================
Property Name        | Description
==================   ================================================================================
Catalogs             | The list of catalogs using this master image.
Applications         | The list of start menu applications on the master image.
                     | *Id* - The ID of the application. Use this ID when publishing an application.
                     | *Name* - The name of the application on the master image. Use this name when
                     |          publishing an application.
                     | *ApplicationPath* - The application path on the master image. Use this path
                     |                     when publishing an application.
                     | *WorkingDirectory* - By default, this path is the same as the path in the 
                     |                      ApplicationPath field. To run the application from a 
                     |                      different directory, add an absolute path to this field.
                     | *Description* - This is the description that shows in your user's workspace.
                     | *Compressedb64Icon* - The compressed application icon in Base64 format. This
                     |                       icon is for display purpose only. Do not use this when
                     |                       publishing an application.
                     | *CommandLineParams* - The command line parameters for the app at startup.
Id                   | The ID of the master image.
Name                 | The friendly name if the master image.
State                | The current state of the master image. A state of ``Ready`` indicates that the 
                     | image is good and ready to be used by a catalog.
SubState             | The current sub state of the master image. This gives more visibility into the
                     | current state of the image.
Status               | If the image is not in ``Ready`` state, this will give a friendly message 
                     | describing the current state of the image.
TransactionId        | Provide this transactionId to Citrix Support if you need help with this image.
CitrixPrepared       | Indicates if this is a Citrix provided image. This will be false for all 
                     | images added by the customer.
IsDeprecated         | Indicates if this is a Citrix prepared deprecated image. This will be false
                     | for all images added by the customer.
LinkedCatalogs       | The number of catalogs using this image. This will be 0 for an image that 
                     | has just been added and has never been used by a catalog.
==================   ================================================================================

Powershell Example
==================

This example illustrates how to get a master image that was added to a customer's account using Powershell.

.. code-block:: powershell

  function GetMasterImage {
    param (
      [Parameter(Mandatory=$true)]
      [string] $customerId,
      [Parameter(Mandatory=$true)]
      [string] $siteId,
      [Parameter(Mandatory=$true)]
      [string] $bearerToken,
      [Parameter(Mandatory=$true)]
      [string] $imageId
    )
    $requestUri = [string]::Format("https://catalogs.apps.cloud.com/{0}/{1}/images/{2}", $customerId, $siteId, $imageId)
    $headers = @{"Accept"="application/json";
                 "Authorization"="CWSAuth bearer=$bearerToken"}

    $response = Invoke-RestMethod -Uri $requestUri -Method GET -Headers $headers
    return $response
  }
  
  $customerId = "exampleCust" #Replace with your customerId
  $siteId = "61603f15-cdf9-4c7f-99ff-91636601a795" #Replace with your site ID
  $bearerToken = "ey.." #See Prerequisites for all API calls section for a sample of how to get your bearer token
  $imageId = "56f1cbf3-1cc6-40cd-9c82-c95633ba88bb" #Replace with your master image ID
  $response = GetMasterImage $customerId $siteId $bearerToken $imageId

C# Example
==========

This example illustrates how to get a master image that was added to a customer's account using C#.
  
.. code-block:: csharp

  public static async Task<string> GetMasterImage(
      string customerId,
      string siteId,
      string bearerToken,
      string imageId)
  {   
      var requestUri = string.Format("https://catalogs.apps.cloud.com/{0}/{1}/images/{2}", customerId, siteId, imageId);
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