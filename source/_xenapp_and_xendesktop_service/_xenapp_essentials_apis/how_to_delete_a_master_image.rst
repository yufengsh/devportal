============================
How to delete a master image
============================

This *REST API* deletes a master image from a customer's account.

Steps
=====
* Read the `prerequisites <prerequisites.html>`_ and ensure that you have the ``customerId``, ``siteId`` and ``bearer token``.
* If you do not have the ``imageId``, see `how to get all master images <how_to_get_all_master_images.html>`_ to get the image ID of all your master images available in your account.
* Use the API in this document to delete the master image.

REST Example
============

This example illustrates how to delete a master image from a customer's account using REST API.

Request
~~~~~~~
::

  DELETE https://catalogs.apps.cloud.com/{customerid}/{siteId}/images/{imageId} HTTP/1.1
  Accept: application/json
  Content-Type: application/json
  Authorization: CwsAuth bearer=<token-from-prerequisites>
  
Response
~~~~~~~~
::

  HTTP/1.1 200 OK
  Content-Length: 6
  Content-Type: application/json; charset=utf-8
  Date: Fri, 04 Aug 2017 13:43:31 GMT
  
  "True"

Interpreting the request URL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

imageId
-------
The ``imageId`` should be replaced with the actual image ID of the master image. You must have obtained the ``imageId`` as response when invoking APIs listed in `how to add a master image <how_to_add_a_master_image.html>`_ API or `how to get all master images <how_to_get_all_master_images.html>`_ sections.

Interpreting the response
~~~~~~~~~~~~~~~~~~~~~~~~~

The response is either "True" or "False" indicating either success or failure respectively.

Powershell Example
==================

This example illustrates how to delete a master image from a customer's account using Powershell.

.. code-block:: powershell

  function DeleteMasterImage {
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

    $response = Invoke-RestMethod -Uri $requestUri -Method DELETE -Headers $headers
    return $response
  }
  
  $customerId = "exampleCust" #Replace with your customerId
  $siteId = "61603f15-cdf9-4c7f-99ff-91636601a795" #Replace with your site ID
  $bearerToken = "ey.." #See Prerequisites for all API calls section for a sample of how to get your bearer token
  $imageId = "56f1cbf3-1cc6-40cd-9c82-c95633ba88bb" #Replace with your master image ID
  $response = DeleteMasterImage $customerId $siteId $bearerToken $imageId
  
C# Example
==========

This example illustrates how to delete a master image from a customer's account using C#.

.. code-block:: csharp

  public static async Task<string> DeleteMasterImage(
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

          var response = await client.DeleteAsync(requestUri);

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