==============================
How to get catalog information
==============================

This *REST API* gets the details of a catalog that was created in customer's account.

Steps
=====
* Read the `prerequisites <prerequisites.html>`_ and ensure that you have the ``customerId``, ``siteId`` and ``bearer token``.
* If you do not have the ``catalogId``, see `how to get details of all the catalogs <how_to_get_details_of_all_the_catalogs.html>`_ to get the details of all your catalogs.
* Use the API in this document to get catalog details.

REST Example
============

This example illustrates how to get a details for a catalog that was created in a customer’s account using REST API.

Request
~~~~~~~
::

  GET https://catalogs.apps.cloud.com/{customerId}/{siteId}/catalogs/{catalogId} HTTP/1.1
  Accept: application/json
  Content-Type: application/json
  Authorization: CwsAuth bearer=<token-from-prerequisites>
  
Response
~~~~~~~~
::

  HTTP/1.1 200 OK
  Content-Length: 871
  Content-Type: application/json; charset=utf-8
  Date: "Thu, 03 Aug 2017 18:04:15 GMT"
  
  {
    "StoreFrontUrl": "customer.cloud.com",
    "AzureSubscriptionId": "c38c7a5c...",
    "Id": "cd2efc54-6918-4bf8-b469-18b62cd73bc2",
    "Name": "Finance Catalog",
    "State": "Active",
	"SubState": "",
    "Warnings": [
      {
        "Type": "CapacityUpdateFailed",
        "IsDismissible": false,
        "WarningMessage": "The capacity settings failed to apply. Click Save to try to save the settings again."
      }
    ],
	"StatusMessage": "",
    "SubscriptionName": "Finance Azure Subscription",
    "ResourceGroup": "FinanceRG",
    "Region": "westus",
    "VNetName": "FinanceVnet",
    "Subnet": "FinanceSubnet",
    "DomainName": "customer.local",
    "DomainServiceAccount": "user",
    "ImageId": "f5b89234-3898-49de-9a5b-6838fc58c617",
    "TemplateImageName": "Windows Server 2012 R2 with VDA 7.14.1",
    "TaskCompletionPercentage": 100,
    "LastModifiedTime": "2017-08-03T18:04:15.6821456+00:00"
  }

Interpreting the request URL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

catalogId
---------
The ``catalogId`` should be replaced with the actual catalog ID of the catalog. You can get the ``catalogId`` by using steps listed in `how to get details of all the catalogs <how_to_get_details_of_all_the_catalogs.html>`_ section.


Interpreting the response
~~~~~~~~~~~~~~~~~~~~~~~~~

======================== ========================================================================
Property Name            | Description
======================== ========================================================================
StoreFrontUrl            | The Storefront or Workspace URL for the customer to access their 
                         | apps. This is only available for Active catalogs.
AzureSubscriptionId      | The Azure subscription ID linked to the catalog.
Id                       | The ID of the catalog. This is the ID to pass to any API that 
                         | accepts a catalogId.
Name                     | The name of the catalog.
State                    | The current state of the catalog. A state of ``Input Required`` or 
                         | ``Active`` indicates that the catalog deployment is successful.
SubState                 | The substate of the catalog. This gives more information about the 
                         | current state of the catalog if it is in ``Processing`` state.
Warnings                 | Any warnings associated with the catalog.
StatusMessage            | If the catalog is not in ``InputRequired`` or ``Active`` state, this 
                         | property will provide a friendly message describing the current 
                         | state of the catalog.
SubscriptionName         | Name of the Azure subscription linked to the catalog.
ResourceGroup            | Azure resource group of the catalog.
Region                   | The Azure region that VNET tied to the catalog resides in.
VNetName                 | The Azure VNET used for the catalog.
Subnet                   | The Azure subnet used for the catalog.
DomainName               | The name of the domain to which Cloud Connectors and VDAs are
                         | joined.
DomainServiceAccount     | The service account name used to join the Cloud Connectors and  
                         | VDAs to the domain.
ImageId                  | The ID of the master image tied to the catalog. 
                         | Use `how to get a master image <how_to_get_a_master_image.html>`_ to get the details of this image.
TemplateImageName        | The friendly name of the master image.
TaskCompletionPercentage | The current percentage completion of the catalog creation task.
LastModifiedTime         | The list time the catalog was modified.
======================== ========================================================================

PowerShell Example
==================

This example illustrates how to get a details for a catalog that was created in a customer’s account using PowerShell.

.. code-block:: powershell

  function GetCatalog {
    param (
      [Parameter(Mandatory=$true)]
      [string] $customerId,
      [Parameter(Mandatory=$true)]
      [string] $siteId,
      [Parameter(Mandatory=$true)]
      [string] $bearerToken,
      [Parameter(Mandatory=$true)]
      [string] $catalogId
    )
    $requestUri = [string]::Format("https://catalogs.apps.cloud.com/{0}/{1}/catalogs/{2}", $customerId, $siteId, $catalogId)
    $headers = @{"Accept"="application/json";
                 "Authorization"="CWSAuth bearer=$bearerToken"}

    $response = Invoke-RestMethod -Uri $requestUri -Method GET -Headers $headers
    return $response
  }
  
  $customerId = "exampleCust" #Replace with your customerId
  $siteId = "61603f15-cdf9-4c7f-99ff-91636601a795" #Replace with your site ID
  $bearerToken = "ey.." #See Prerequisites for all API calls section for a sample of how to get your bearer token
  $catalogId = "56f1cbf3-1cc6-40cd-9c82-c95633ba88bb" #Replace with your catalog ID
  $response = GetCatalog $customerId $siteId $bearerToken $catalogId
  
C# Example
==========

This example illustrates how to get a details for a catalog that was created in a customer’s account using C#.
  
.. code-block:: csharp

  public static async Task<string> GetCatalog(
      string customerId,
      string siteId,
      string bearerToken,
      string catalogId)
  {   
      var requestUri = string.Format("https://catalogs.apps.cloud.com/{0}/{1}/catalogs/{2}", customerId, siteId, catalogId);
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