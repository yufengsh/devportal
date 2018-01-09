=====================================
How to unpublish catalog applications
=====================================

This *REST API* un-publishes applications from a catalog in a customer's account.

Steps
=====
* Read the `prerequisites <prerequisites.html>`_ and ensure that you have the ``customerId``, ``siteId`` and ``bearer token``.
* If you do not have the ``catalogId``, see `how to get details of all the catalogs <how_to_get_details_of_all_the_catalogs.html>`_ to get the details of all your catalogs.
* If you do not have the AppId of the applications to un-publish, see `how to get all catalog applications <how_to_get_all_catalog_applications.html>`_ to get the details of all your catalog applications.
* Use the API listed below to un-publish applications from a catalog.

REST Example
============

This example illustrates how to un-publish applications from a catalog in a customer's account using REST API.

Request
~~~~~~~
::

  DELETE https://catalogs.apps.cloud.com/{customerId}/{siteId}/catalogs/{catalogId}/apps?appIds={appId}&appIds={appId} HTTP/1.1
  Accept: application/json
  Content-Type: application/json
  Authorization: CwsAuth bearer=<token-from-prerequisites>
  
Response
~~~~~~~~
::

  HTTP/1.1 200 OK
  Content-Length: 6
  Content-Type: application/json; charset=utf-8
  Date: "Tue, 03 Oct 2017 22:15:10 GMT"
  
  "True"

Interpreting the request URL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

appId
-----
``appId`` is a query parameter in the request url. Specify multiple appId to delete multiple applications. See `how to get all catalog applications <how_to_get_all_catalog_applications.html>`_ API to obtain the ``appId`` for all your published apps.

Interpreting the response
~~~~~~~~~~~~~~~~~~~~~~~~~

The response is either "True" or "False" indicating either success or failure respectively.

PowerShell Example
==================

This example illustrates how to un-publish applications from a catalog in a customer's account using PowerShell.

.. code-block:: powershell

  function UnPublishCatalogApplications {
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
      [string[]] $appIds
    )
  
    $query = [System.Web.HttpUtility]::ParseQueryString([String]::Empty)
    foreach($appId in $appIds)
    {
      $query.Add("appIds", $appId)
    }
  
    $requestUri = [string]::Format("https://catalogs.apps.cloud.com/{0}/{1}/catalogs/{2}/apps?{3}", $customerId, $siteId, $catalogId, $query.ToString())
    $headers = @{"Accept"="application/json";
                 "Authorization"="CwsAuth bearer=$bearerToken"}

    $response = Invoke-RestMethod -Uri $requestUri -Method DELETE -Headers $headers
    return $response
  }
  
  $customerId = "customer1" #Replace with your customerId
  $siteId = "61603f15-cdf9-4c7f-99ff-91636601a795" #Replace with your site ID
  $bearerToken = "ey1.." #See Prerequisites for all API calls section for a sample of how to get your bearer token
  $catalogId = "56f1cbf3-1cc6-40cd-9c82-c95633ba88bb" #Replace with your catalog ID
  $appIds = @("50af6370-fff1-4fc9-b082-a33761fd2f20", "37ee73e3-8dc1-4f8d-8924-cef5724e3305", "804af829-5701-48ef-b4e4-f91d2012f816") #Replace with your app ids
  $response = UnPublishCatalogApplications $customerId $siteId $bearerToken $catalogId $appIds
  
C# Example
==========

This example illustrates how to un-publish applications from a catalog in a customer's account using C#.

.. code-block:: csharp

  public static async Task<string> UnPublishCatalogApplications(
      string customerId,
      string siteId,
      string bearerToken,
      string catalogId,
      string[] appIds)
  {
      var query = HttpUtility.ParseQueryString(string.Empty);
      foreach (var appId in appIds)
      {
        query.Add("appIds", appId);
      }
      
      var requestUri = string.Format("https://catalogs.apps.cloud.com/{0}/{1}/catalogs/{2}/apps?{3}", customerId, siteId, catalogId, query);
      using (var client = new HttpClient())
      {
          client.DefaultRequestHeaders.Accept.ParseAdd("application/json");
          client.DefaultRequestHeaders.Authorization =
                     new AuthenticationHeaderValue("CwsAuth", "Bearer=" + bearerToken);

          var response = await client.DeleteAsync(requestUri);

          if (response != null)
          {
              var content = await response.Content.ReadAsStringAsync();
              return content;
          }

          return null;
      }
  }

Comments
========

.. disqus::