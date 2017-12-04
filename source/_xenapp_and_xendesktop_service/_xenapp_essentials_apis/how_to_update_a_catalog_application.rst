===================================
How to update a catalog application
===================================
This *REST API* updates the details of an existing application for a catalog.

Steps
=====
* Read the `prerequisites <prerequisites.html>`_ and ensure that you have the ``customerId``, ``siteId`` and ``bearer token``.
* If you do not have the ``catalogId``, see `how to get details of all the catalogs <how_to_get_details_of_all_the_catalogs.html>`_ to get the details of all your catalogs.
* If you do not have the AppId of the application to update, see `how to get all catalog applications <how_to_get_all_catalog_applications.html>`_ to get the details of all your catalog applications.
* Use the API listed below to update application details from a catalog.

REST Example
============

This example illustrates how to update the details of an existing application for a catalog using REST API.

Request
~~~~~~~
::

  PUT https://catalogs.apps.cloud.com/{customerId}/{siteId}/catalogs/{catalogId}/apps/{appId} HTTP/1.1
  Accept: application/json
  Content-Type: application/json
  Authorization: CwsAuth bearer=<token-from-prerequisites>
  
  { 
    "Name": "Google Chrome",
    "ApplicationPath": "C:\\Program Files (x86)\\Google\\Chrome\\Application\\chrome.exe",
    "CommandLineParameters": "https://developer.cloud.com",
    "WorkingDirectory": "%ProgramFiles(x86)%\\Google",
    "Base64Icon": "AAABAAcAAAAAAAEAIABnYQAAdgAAADAwAAABAAgAqA4AAN1hAAAwMAAAAQA..."
  }
  
Response
~~~~~~~~
::

  HTTP/1.1 200 OK
  Content-Length: 6
  Content-Type: application/json; charset=utf-8
  Date: "Fri, 06 Oct 2017 14:26:08 GMT"
  
  "True"

Interpreting the request
~~~~~~~~~~~~~~~~~~~~~~~~

======================= ===================================================================================
Property Name           | Description
======================= ===================================================================================
Name                    | [Required] The new name of the application. Set this to the original name if
                        | you do not wish to modify it.
ApplicationPath         | [Required] The new full path of the application executable on the master image.
                        | Set this to the original name if you do not wish to modify it.
CommandLineParameters   | [Optional] The new command line parameters for the app at startup. If this 
                        | property is not specified, the original value will remain intact.
WorkingDirectory        | [Optional] By default, this path is the same as the path in the ApplicationPath
                        | field. To run the application from a different directory, add an 
                        | absolute path to this field.
Base64Icon              | [Optional] The new base64 icon for the application. If this property is not
                        | specified, the original value will remain intact.
======================= ===================================================================================

Interpreting the response
~~~~~~~~~~~~~~~~~~~~~~~~~

The response is either "True" or "False" indicating either success or failure respectively.

Powershell Example
==================

This example illustrates how to update the details of an existing application for a catalog using Powershell.

.. code-block:: powershell

  function UpdateCatalogApplication {
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
      [string] $appId,
      [Parameter(Mandatory=$true)]
      [string] $jsonBody
    )
    $requestUri = [string]::Format("https://catalogs.apps.cloud.com/{0}/{1}/catalogs/{2}/apps/{3}", $customerId, $siteId, $catalogId, $appId)
    $headers = @{"Accept"="application/json";
                 "Content-Type"="application/json"
                 "Authorization"="CWSAuth bearer=$bearerToken"}

    $response = Invoke-RestMethod -Uri $requestUri -Method PUT -Headers $headers -Body $jsonBody    
    return $response
  }

  $body = @{
    "Name" = "Google Chrome";
    "ApplicationPath" = "C:\Program Files (x86)\Google\Chrome\Application\chrome.exe";
    "CommandLineParameters" = "https://developer.cloud.com";
    "WorkingDirectory" = "%ProgramFiles(x86)%\Google";
    "Base64Icon"= "AAABAAcAAAAAAAEAIABnYQAAdgAAADAwAAABAAgAqA4AAN1hAAAwMAAA..."
  }
  
  $customerId = "exampleCust" #Replace with your customerId
  $siteId = "61603f15-cdf9-4c7f-99ff-91636601a795" #Replace with your site ID
  $bearerToken = "ey.." #See Prerequisites for all API calls section for a sample of how to get your bearer token
  $catalogId = "56f1cbf3-1cc6-40cd-9c82-c95633ba88bb" #Replace with your catalog ID
  $appId = "f17bbe18-83a9-461c-a890-9c424596f0f3" #Replace with your app ID
  $response = UpdateCatalogApplication $customerId $siteId $bearerToken $catalogId $appId (ConvertTo-Json $body)
  
C# Example
==========

This example illustrates how to update the details of an existing application for a catalog using C#.
  
.. code-block:: csharp

  public class UpdateApplicationConfigurationModel
  {
    /// <summary>
    /// Display name of app
    /// </summary>
    [Required]
    public string Name { get; set; }

    /// <summary>
    /// Path of the application on the msater image
    /// </summary>
    [Required]
    public string ApplicationPath { get; set; }

    /// <summary>
    /// Working directory of the app at launch
    /// </summary>
    public string WorkingDirectory { get; set; }

    /// <summary>
    /// Command line paramters to pass to the app when launching
    /// </summary>
    public string CommandLineParams { get; set; }

    /// <summary>
    /// The raw app icon represented as a base64 string
    /// </summary>
    public string Base64Icon { get; set; }
  }

  public static async Task<string> UpdateCatalogApplication(
      string customerId,
      string siteId,
      string bearerToken,
      string catalogId,
      string appId,
      UpdateApplicationConfigurationModel model)
  {
      var requestUri = string.Format("https://catalogs.apps.cloud.com/{0}/{1}/catalogs/{2}/apps/{3}", customerId, siteId, catalogId, appId);
      using (var client = new HttpClient())
      {
          client.DefaultRequestHeaders.Accept.ParseAdd("application/json");
          client.DefaultRequestHeaders.Authorization =
                     new AuthenticationHeaderValue("CWSAuth", "Bearer=" + bearerToken);

          var jsonBody = JsonConvert.SerializeObject(model, new JsonSerializerSettings
          {
              Converters = new JsonConverter[] { new StringEnumConverter() }
          });

          var response = await client.PutAsync(requestUri, new StringContent(jsonBody, Encoding.UTF8, "application/json"));

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