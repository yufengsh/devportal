=========================
How to add a master image
=========================

This *REST API* adds a new master image to a customer's account.

Steps
=====
* Read the `prerequisites <prerequisites.html>`_ and ensure that you have the ``customerId``, ``siteId`` and ``bearer token``.
* Use the API listed below to add a master image.
* Check the verification status of the master image using the steps detailed in `how to get a master image <how_to_get_a_master_image.html>`_.
* A state of ``Enumerating`` indicates that the image processing has begun. Keep monitoring until the status changes to either ``Ready`` or ``Failed``.
* A state of ``Ready`` indicates that the image has been added successfully. No further action is required.
* A state of ``Failed`` indicates that adding the image has failed. Correct the error shown in the ``Status`` property.
* Delete the image using `how to delete a master image <how_to_delete_a_master_image.html>`_
* Add the image again.

REST Example
============

This example illustrates how to add a new master image to a customer's account using REST API.

Request
~~~~~~~
::

  POST https://catalogs.apps.cloud.com/{customerid}/{siteId}/images HTTP/1.1
  Accept: application/json
  Content-Type: application/json
  Authorization: CwsAuth bearer=<token-from-prerequisites>
  
  {
    "Name": "Finance Image",
    "SubscriptionId": "c38c7a5c-..",
    "ResourceGroup": "FinanceRG",
    "StorageAccount": "xaensfa5edge1gu4o1",
    "VhdUrl": "https://xaensfa5edge1gu4o1.blob.core.windows.net/vhds/XAEnsfa5-Findisk0.vhd",
    "Validate": true
  }

Response
~~~~~~~~
::

  HTTP/1.1 200 OK
  Content-Length: 38
  Content-Type: application/json; charset=utf-8
  Date: Wed, 02 Aug 2017 15:05:23 GMT
  
  "55cab14f-efb7-4bd6-bd9c-ebe991c9d165"

Interpreting the request
~~~~~~~~~~~~~~~~~~~~~~~~
==================   ================================================================================
Property Name        | Description
==================   ================================================================================
Name                 | The friendly name you want to assign to the image.
SubscriptionId       | The Azure subscription ID where the image resides.
                     | This subscription should already be linked to the customer's account via the UI.
ResourceGroup        | The Azure resource group name where the image resides.
StorageAccount       | The Azure storage account name where the image resides.
VhdUrl               | The VHD URL of the image.
Validate             | This should always be set to true.
==================   ================================================================================

Interpreting the response
~~~~~~~~~~~~~~~~~~~~~~~~~

The response is a GUID string that represents the ``imageId``. You will need this ``imageId`` to query the status of the image.

Powershell Example
==================

This example illustrates how to add a new master image to a customer's account using Powershell.

.. code-block:: powershell

  function AddMasterImage {
    param (
      [Parameter(Mandatory=$true)]
      [string] $customerId,
      [Parameter(Mandatory=$true)]
      [string] $siteId,
      [Parameter(Mandatory=$true)]
      [string] $bearerToken,
      [Parameter(Mandatory=$true)]
      [string] $jsonBody
    )
    $requestUri = [string]::Format("https://catalogs.apps.cloud.com/{0}/{1}/images", $customerId, $siteId)
    $headers = @{"Accept"="application/json";
                 "Content-Type"="application/json"
                 "Authorization"="CWSAuth bearer=$bearerToken"}

    $response = Invoke-RestMethod -Uri $requestUri -Method POST -Headers $headers -Body $jsonBody
    return $response
  }

  $body = @{
    "Name" = "Finance Image";
    "ResourceGroup" = "FinanceRG";
    "StorageAccount" = "xaensfa5edge1gu4o1";
    "SubscriptionId" = "c38c7a5c-..";
    "Validate" = $true;
    "VhdUrl" = "https://xaensfa5edge1gu4o1.blob.core.windows.net/vhds/XAEnsfa5-Findisk0.vhd"
  }
  
  $customerId = "exampleCust" #Replace with your customerId
  $siteId = "61603f15-cdf9-4c7f-99ff-91636601a795" #Replace with your site ID
  $bearerToken = "ey.." #See Prerequisites for all API calls section for a sample of how to get your bearer token
  $response = AddMasterImage $customerId $siteId $bearerToken (ConvertTo-Json $body)

C# Example
==========

This example illustrates how to add a new master image to a customer's account using C#.

.. code-block:: csharp

  public class AddMasterImageModel
  {
      /// <summary>
      /// Friendly name of the template
      /// </summary>
      [Required]
      public string Name { get; set; }

      /// <summary>
      /// ID of the Azure Subscription
      /// </summary>
      [Required]
      public string SubscriptionId { get; set; }

      /// <summary>
      /// Name of the Resource Group
      /// </summary>
      [Required]
      public string ResourceGroup { get; set; }

      /// <summary>
      /// Name of the storage account
      /// </summary>
      [Required]
      public string StorageAccount { get; set; }

      /// <summary>
      /// Url of the VHD image within the storage account
      /// </summary>
      public string VhdUrl { get; set; }

      /// <summary>
      /// Indicates if the template image should be validated upon creation
      /// </summary>
      public bool Validate { get; set; }
  }
  
.. code-block:: csharp

  public static async Task<string> AddMasterImage(
      string customerId,
      string siteId,
      string bearerToken,
      AddMasterImageModel model)
  {   
      var requestUri = string.Format("https://catalogs.apps.cloud.com/{0}/{1}/images", customerId, siteId);
      using (var client = new HttpClient())
      {
          client.DefaultRequestHeaders.Accept.ParseAdd("application/json");
          client.DefaultRequestHeaders.Authorization =
                     new AuthenticationHeaderValue("CWSAuth", "Bearer=" + bearerToken);

          var jsonBody = JsonConvert.SerializeObject(model, new JsonSerializerSettings
          {
              Converters = new JsonConverter[] { new StringEnumConverter() }
          });

          var response = await client.PostAsync(requestUri, new StringContent(jsonBody, Encoding.UTF8, "application/json"));

          if (response != null)
          {
              var content = await response.Content.ReadAsStringAsync();
              return content;
          }

          return null;
      }
  }

See `how to get a master image <how_to_get_a_master_image.html>`_ to monitor the image status once it has been added.

See `how to get all master images <how_to_get_all_master_images.html>`_ to get the image ID of all your posted images.

Comments
========

.. disqus::