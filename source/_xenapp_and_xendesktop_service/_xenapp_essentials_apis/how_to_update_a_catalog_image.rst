=============================
How to update a catalog image
=============================
This *REST API* updates an existing catalog with a new master image.

Steps
=====
* Read the `prerequisites <prerequisites.html>`_ and ensure that you have the ``customerId`` and ``bearer token``.
* The master image must already be added to the customers account. See `how to add a master image <how_to_add_a_master_image.html>`_ for steps to add a new master image.
* The catalog must be in ``InputRequired`` or ``Active`` state.
* If you do not have the ``catalogId``, see `how to get details of all the catalogs <how_to_get_details_of_all_the_catalogs.html>`_ to get the details of all your catalogs.
* If you do not have the ``imageId``, see `how to get all master images <how_to_get_all_master_images.html>`_ to get the image ID of all your posted images.
* Use the API in this document to update the catalog with a new master image.
* Check steps listed in `how to get catalog information <how_to_get_catalog_information.html>`_ to monitor the update status of your catalogs.
* A state of ``Processing`` indicates that the catalog is still being updated. Keep monitoring the state.
* A state of ``InputRequired`` or ``Active`` indicates that the image update has completed.

REST Example
============

This example illustrates how to update a catalog with a new master image using REST API.

Request
~~~~~~~
::

  POST https://catalogs.apps.cloud.com/{customerId}/{siteId}/catalogs/{catalogId}/updateImage HTTP/1.1
  Accept: application/json
  Content-Type: application/json
  Authorization: CwsAuth bearer=<token-from-prerequisites>
  
  {
    "TemplateId": "7388dbc7-6b69-40a0-a4c9-18efd3b7afe3",
    "CitrixPrepared": false,
    "VdaUpdateDelay": 60
  }
  
Response
~~~~~~~~
::

  HTTP/1.1 200 OK
  Content-Length: 38
  Content-Type: application/json; charset=utf-8
  Date: Thu, 03 Aug 2017 20:56:36 GMT
  
  "cd2efc54-6918-4bf8-b469-18b62cd73bc2"

Interpreting the request URL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

catalogId
---------
The ``catalogId`` should be replaced with the actual ID of the catalog. You can get the ``catalogId`` by calling the `how to get details of all the catalogs <how_to_get_details_of_all_the_catalogs.html>`_ API.

Interpreting the request
~~~~~~~~~~~~~~~~~~~~~~~~

==================   ================================================================================
Property Name        | Description
==================   ================================================================================
TemplateId           | The ID of the master image you want to update the catalog with. 
                     | See `how to get all master images <how_to_get_all_master_images.html>`_ to
                     | get the image ID of all your posted images.
CitrixPrepared       | This should be set to false for a customer provided image. This should be 
                     | set to true if you are updating the catalog with a Citrix Prepared image.
VdaUpdateDelay       | This is the time in minutes after which active sessions are automatically 
                     | logged off.
==================   ================================================================================

Interpreting the response
~~~~~~~~~~~~~~~~~~~~~~~~~

The response is a GUID string that represents the ``catalogId`` of the catalog that was just updated. You will need this ``catalogId`` to query the details of the catalog.

Powershell Example
==================

This example illustrates how to update a catalog with a new master image using Powershell.

.. code-block:: powershell

  function UpdateCatalogImage {
    param (
      [Parameter(Mandatory=$true)]
      [string] $customerId,
      [Parameter(Mandatory=$true)]
      [string] $siteId,
      [Parameter(Mandatory=$true)]
      [string] $bearerToken,
      [Parameter(Mandatory=$true)]
      [string] $catalogId,
      [Parameter(Mandatory=$true)]
      [string] $jsonBody
    )
    $requestUri = [string]::Format("https://catalogs.apps.cloud.com/{0}/{1}/catalogs/{2}/updateimage", $customerId, $siteId, $catalogId)
    $headers = @{"Accept"="application/json";
                 "Content-Type"="application/json"
                 "Authorization"="CWSAuth bearer=$bearerToken"}

    $response = Invoke-RestMethod -Uri $requestUri -Method POST -Headers $headers -Body $jsonBody    
    return $response
  }

  $body = @{
    "TemplateId" = "7388dbc7-6b69-40a0-a4c9-18efd3b7afe3";
    "CitrixPrepared" = $false;
    "VdaUpdateDelay" = "60"
  }
  
  $customerId = "exampleCust" #Replace with your customerId
  $bearerToken = "ey.." #See Prerequisites for all API calls section for a sample of how to get your bearer token
  $catalogId = "56f1cbf3-1cc6-40cd-9c82-c95633ba88bb" #Replace with your catalog ID
  $response = UpdateCatalogImage $customerId $bearerToken $catalogId (ConvertTo-Json $body)
  
C# Example
==========

This example illustrates how to update a catalog with a new master image using C#.
  
.. code-block:: csharp

  public class UpdateCatalogTemplateImageModel
  {
      /// <summary>
      /// ID of the master image to update the catalog with
      /// </summary>
      [Required]
      public string TemplateId { get; set; }

      /// <summary>
      /// Whether the image was prepared by Citrix, or provided by the customer
      /// </summary>
      public bool CitrixPrepared { get; set; } = false; // default to false if not specified

      /// <summary>
      /// Number of minutes to delay updating the VDAs
      /// </summary>
      public int VdaUpdateDelay { get; set; } = 0;
  }

  public static async Task<string> UpdateCatalogImage(
      string customerId,
      string siteId,
      string bearerToken,
      string catalogId,
      UpdateCatalogTemplateImageModel model)
  {
      var requestUri = string.Format("https://catalogs.apps.cloud.com/{0}/{1}/catalogs/{2}/updateimage", customerId, siteId, catalogId);
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

See `how to get catalog information <how_to_get_catalog_information.html>`_ to monitor the update status of your catalogs.

Comments
========

.. disqus::