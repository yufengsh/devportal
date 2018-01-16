====================================
How to publish catalog applications
====================================

This *REST API* publishes applications to a catalog in a customer's account.

Steps
=====
* Read the `prerequisites <prerequisites.html>`_ and ensure that you have the ``customerId``, ``siteId`` and ``bearer token``.
* The catalog must be in ``InputRequired`` or ``Active`` state.
* If you do not have the ``catalogId``, see `how to get details of all the catalogs <how_to_get_details_of_all_the_catalogs.html>`_ to get the details of all your catalogs.
* Use the API listed below to publish applications to a catalog.

REST Example
============

This example illustrates how to publish applications to a catalog in a customer's account using REST API.

Request
~~~~~~~
::

  POST https://catalogs.apps.cloud.com/{customerId}/{siteId}/catalogs/{catalogId}/apps HTTP/1.1
  Accept: application/json
  Content-Type: application/json
  Authorization: CwsAuth bearer=<token-from-prerequisites>
  
  {
    "Applications": [
      {
        "Id": "d288f8ee-538e-41ae-b79e-bd562baff78b",
        "IsStartMenuDiscovered": true,
        "Name": "Acrobat Reader DC",
        "Path": "C:\\Program Files (x86)\\Adobe\\Acrobat Reader DC\\Reader\\AcroRd32.exe",
        "WorkingDirectory": "%ProgramFiles(x86)%\\Adobe",
        "Description": "Read pdf documents"
      },
      {
        "Id": "e8b97101-5484-40da-a863-7540f2efb4ad";
        "IsStartMenuDiscovered": true,
        "Name": "Notepad",
        "Path": "C:\\Windows\\System32\\notepad.exe",
        "Description": "Notepad application",
        "CommandLineParameters": "C:\\Sample.txt"
      },
      { 
        "IsStartMenuDiscovered": false,
        "Name": "Google Chrome",
        "Path": "C:\\Program Files (x86)\\Google\\Chrome\\Application\\chrome.exe",
        "CommandLineParameters": "https://www.citrix.com",
        "Base64Icon": "AAABAAcAAAAAAAEAIABnYQAAdgAAADAwAAABAAgAqA4AAN1hAAAwMAAAAQA..."
      }
    ]
  }

Response
~~~~~~~~
::

  HTTP/1.1 200 OK
  Content-Length: 13455
  Content-Type: application/json; charset=utf-8
  Date: "Fri, 29 September 2017 20:21:09 GMT"
  
  {
    "Applications": [
      {
        "State": "Published",
        "Id": "bfca73a0-c30a-4db5-af55-b1cc02f9b0f2",
        "Name": "Acrobat Reader DC",
        "ApplicationPath": "C:\\Program Files (x86)\\Adobe\\Acrobat Reader DC\\Reader\\AcroRd32.exe",
        "WorkingDirectory": "%ProgramFiles(x86)%\\Adobe",
        "Description": "Read pdf documents",
        "Compressedb64Icon": "iVBORw0KGgoAAAANSUhEUgAAACwAAAAsCAYAAAAehFoBAAAAAXNSR..."
      },
      {
        "State": "Published",
        "Id": "8bc13ade-b275-4f4b-97e9-98ccd3575b03",
        "Name": "Notepad",
        "ApplicationPath": "C:\\Windows\\System32\\notepad.exe",
        "Description": "Notepad application",
        "Compressedb64Icon": "iVBORw0KGgoAAAANSUhEUgAAACwAAAAsCAYAAAAehFoBAAAAAXNSR...",
        "CommandLineParams": "C:\\Sample.txt"
      },
      {
        "State": "Published",
        "Id": "49a7181a-fee2-4a6b-ac75-ff0f2d235574",
        "Name": "Google Chrome",
        "ApplicationPath": "C:\\Program Files (x86)\\Google\\Chrome\\Application\\chrome.exe",
        "Compressedb64Icon": "iVBORw0KGgoAAAANSUhEUgAAACwAAAAsCAYAAAAehFoBAAAAAXNSR...",
        "CommandLineParams": "https://www.citrix.com"
      }
    ]
  }
  
Interpreting the request
~~~~~~~~~~~~~~~~~~~~~~~~
The request is a JSON with a list of all the start menu (Publish from start menu) and custom (Publish by path) applications that you want to publish to the catalog.

======================= ===================================================================================
Property Name           | Description
======================= ===================================================================================
Id                      | The ID of the application. See `how to get a master image <how_to_get_a_master_image.html>`_
                        | to get the application ID for publishing start menu apps. Leave this 
                        | property empty for publishing custom (Publish by path) applications.
IsStartMenuDiscovered   | True if publishing a start menu app. False if publishing an application by path.
Name                    | The name of the application. See `how to get a master image <how_to_get_a_master_image.html>`_
                        | to get the application name. Set this to the name of you application for 
                        | publishing custom (Publish by path) application.
Path                    | The full path of the application executable on the master image.
WorkingDirectory        | By default, this path is the same as the path in the Path field. To run the 
                        | application from a different directory, add an absolute path to this field.
Description             | This is the description that shows in your user's workspace.
CommandLineParameters   | [Optional] The command line parameters for the app at startup.
Base64Icon              | [Optional] Leave this property empty when publishing a start menu application 
                        | since we already have the icon. Set this to the raw application icon in base64
                        | format when publishing an application by path. See `how to extract an app icon <how_to_extract_an_app_icon.html>`_
                        | to get this icon string in the correct format. If an icon is not specified, a
                        | default icon will be used.
======================= ===================================================================================

Interpreting the response
~~~~~~~~~~~~~~~~~~~~~~~~~

The request is a JSON with a list of all the applications that were successfully published as part of this request.

PowerShell Example
==================

This example illustrates how to publish applications to a catalog in a customer's account using PowerShell.

.. code-block:: powershell

  function PublishCatalogApplications {
    param (
      [Parameter(Mandatory=$true)]
      [string] $customerId,
      [Parameter(Mandatory=$true)]
      [string] $siteId,
      [Parameter(Mandatory=$true)]
      [string] $catalogId,
      [Parameter(Mandatory=$true)]
      [string] $bearerToken,
      [Parameter(Mandatory=$true)]
      [string] $jsonBody
    )
    $requestUri = [string]::Format("https://catalogs.apps.cloud.com/{0}/{1}/catalogs/{2}/apps", $customerId, $siteId, $catalogId)
    $headers = @{"Accept"="application/json";
                 "Content-Type"="application/json"
                 "Authorization"="CwsAuth bearer=$bearerToken"}

    $response = Invoke-RestMethod -Uri $requestUri -Method POST -Headers $headers -Body $jsonBody
    return $response
  }

  $body =   @{
    "Applications" = @(
      @{        
        "Id"= "d288f8ee-538e-41ae-b79e-bd562baff78b";
        "IsStartMenuDiscovered" = true;
        "Name"= "Acrobat Reader DC";
        "Path"= "C:\Program Files (x86)\Adobe\Acrobat Reader DC\Reader\AcroRd32.exe";
        "Description"= "Read pdf documents";
        "WorkingDirectory"= "%ProgramFiles(x86)%\Adobe"
      },
      @{        
        "Id"= "e8b97101-5484-40da-a863-7540f2efb4ad";
        "IsStartMenuDiscovered" = true;
        "Name"= "Notepad";
        "Path"= "C:\Windows\system32\notepad.exe";
        "Description"= "Notepad application";
        "CommandLineParameters" = "C:\Sample.txt"
      },
      @{
        "IsStartMenuDiscovered" = false;
        "Name"= "Google Chrome";
        "Path"= "C:\Program Files (x86)\Google\Chrome\Application\chrome.exe";        
        "CommandLineParameters"= "https://www.citrix.com";
        "Base64Icon"= "iVBORw0KGgoAAAANSUhEUgAAACwAAAAsCAYAAAAehFoBAAAAAXNSR0IArs4c..."
      }
    )
  }
  
  $customerId = "customer1" #Replace with your customerId
  $siteId = "61603f15-cdf9-4c7f-99ff-91636601a795" #Replace with your site ID
  $catalogId = "8d352ba7-1917-41c3-95e5-50f436be8968" #Replace with your catalog ID
  $bearerToken = "ey1.." #See Prerequisites for all API calls section for a sample of how to get your bearer token
  $response = PublishCatalogApplications $customerId $siteId $catalogId $bearerToken (ConvertTo-Json $body)

C# Example
==========

This example illustrates how to publish applications to a catalog in a customer's account using C#.

.. code-block:: csharp

  public class AddCatalogApplicationsModel
  {
    /// <summary>
    /// List of applications to be published
    /// </summary>
    [Required]
    public IEnumerable<AddCatalogApplicationModel> Applications { get; set; }
  }
  
  public class AddCatalogApplicationModel
  {
    /// <summary>
    /// The application id on the master image for the catalog. If the id is not known (for a start menu application) or for adding a custom (publish by path) application, the Id should be left null
    /// </summary>        
    public string Id { get; set; }

    /// <summary>
    /// Indicates if the application was discovered via scan of start menu apps or if this is a custom (publish by path) application
    /// </summary>
    public bool IsStartMenuDiscovered { get; set; } = true;

    /// <summary>
    /// Name of the application on the master image
    /// </summary>
    [Required]
    public string Name { get; set; }

    /// <summary>
    /// Path of the application on the master image
    /// </summary>
    [Required]
    public string Path { get; set; }

    /// <summary>
    /// Command line parameters to pass to the app when launching
    /// </summary>
    public string CommandLineParameters { get; set; }

    /// <summary>
    /// The raw app icon represented as a base64 string. For start menu app this field should be left blank.
    /// </summary>
    public string Base64Icon { get; set; }
  }
  
.. code-block:: csharp

  public static async Task<string> PublishCatalogApplications(
      string customerId,
      string siteId,
      string catalogId,
      string bearerToken,
      AddCatalogApplicationsModel model)
  {   
      var requestUri = string.Format("https://catalogs.apps.cloud.com/{0}/{1}/catalogs/{2}/apps", customerId, siteId, catalogId);
      using (var client = new HttpClient())
      {
          client.DefaultRequestHeaders.Accept.ParseAdd("application/json");
          client.DefaultRequestHeaders.Authorization =
                     new AuthenticationHeaderValue("CwsAuth", "Bearer=" + bearerToken);

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

See `how to get a master image <how_to_get_a_master_image.html>`_ to get all start menu applications on your catalog master image.

Comments
========

.. disqus::