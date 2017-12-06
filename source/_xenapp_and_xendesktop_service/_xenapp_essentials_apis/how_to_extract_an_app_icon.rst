===========================
How to extract an app icon
===========================

This *REST API* extracts raw icon from a file containing icon data. The file can be an exe, ico, png, bmp or any other format containing icon data. 
The base64 string returned by this API needs to be passed to the API when publishing a catalog application.

Steps
=====
* Read the `prerequisites <prerequisites.html>`_ and ensure that you have the ``customerId``, ``siteId`` and ``bearer token``.
* Use the API in this document to get the raw icon base64 string from a file containing icon data.

REST Example
============

This example illustrates how to get icon data in base64 string fromat from a file using REST API.

Request
~~~~~~~
::

  GET https://catalogs.apps.cloud.com/{customerId}/{siteId}/icons HTTP/1.1
  Accept: application/json
  Content-Type: application/json
  Authorization: CwsAuth bearer=<token-from-prerequisites>
  
  {
    "FileName": "string",
    "IconFileBytes": "string"
  }

Interpreting the request
~~~~~~~~~~~~~~~~~~~~~~~~

==================   ================================================================================
Property Name        | Description
==================   ================================================================================
FileName             | This is used for logging purpose only. This can be set to any string.
IconFileBytes        | The data of the file containing icon data represented as a JSON string.
==================   ================================================================================

Response
~~~~~~~~
::

  HTTP/1.1 200 OK
  Content-Length: 63254
  Content-Type: application/json; charset=utf-8
  Date: "Tue, 26 Sep 2017 21:24:11 GMT"
  
  "AAABAAcAAAAAAAEAIABnYQAAdgAAADAwAAABAAgAqA4AAN1hAAAwMAAAAQA..."
  
Interpreting the response
~~~~~~~~~~~~~~~~~~~~~~~~~

The response is a string in base64 format containing raw icon data. This is the icon string that needs to be passed to the API when publishing a custom (Publish by path) application or updating a published application.

PowerShell Example
==================

This example illustrates how to get icon data in base64 string fromat from a file using PowerShell.

.. code-block:: powershell

  function ExtractIcon {
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
    $requestUri = [string]::Format("https://catalogs.apps.cloud.com/{0}/{1}/icons", $customerId, $siteId)
    $headers = @{"Accept"="application/json";
                 "Content-Type"="application/json"
                 "Authorization"="CWSAuth bearer=$bearerToken"}

    $response = Invoke-RestMethod -Uri $requestUri -Method POST -Headers $headers -Body $jsonBody
    return $response
  }
  
  $customerId = "exampleCust" #Replace with your customerId
  $siteId = "61603f15-cdf9-4c7f-99ff-91636601a795" #Replace with your site ID
  $bearerToken = "ey.." #See Prerequisites for all API calls section for a sample of how to get your bearer token
  
  $bytes = [System.IO.File]::ReadAllBytes("C:\temp\chrome.exe") #Replace with your file path

  $body = @{
    "FileName" = "Google Chrome";  #This is used for logging purpose only. This can be any string
    "IconFileBytes" = $bytes;
  }

  $response = ExtractIcon $CustomerId $siteId $bearerToken (ConvertTo-Json $body)
  
C# Example
==========

This example illustrates how to get icon data in base64 string fromat from a file using C#.
  
.. code-block:: csharp

  public class ExtractIconModel
  {
      /// <summary>
      /// The name of the file that comtains the image. This is only used for logging purpose. This can be set to any string.
      /// </summary>
      public string FileName { get; set; }
      
      /// <summary>
      /// The bytes of the file that contains the icon to be extracted
      /// </summary>
      public byte[] IconFileBytes { get; set; }
  }

  public static async Task<string> ExtractIcon(
      string customerId,
      string siteId,
      string bearerToken,
      ExtractIconModel model)
  {   
      var requestUri = string.Format("https://catalogs.apps.cloud.com/{0}/{1}/icons", customerId, siteId);
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

Comments
========

.. disqus::