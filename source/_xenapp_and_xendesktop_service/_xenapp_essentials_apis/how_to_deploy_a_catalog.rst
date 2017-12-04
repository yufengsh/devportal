=======================
How to deploy a catalog
=======================

This *REST API* deploys a new catalog in a customer's account.

Steps
=====
* Read the `prerequisites <prerequisites.html>`_ and ensure that you have the ``customerId``, ``siteId`` and ``bearer token``.
* Ensure an Azure subscription is already linked to the customer's account via the XenApp Essentials UI.
* Use the API listed below to deploy the catalog.
* Monitor the deployment status of your catalog using steps listed in `how to get catalog information <how_to_get_catalog_information.html>`_.
* A state of ``Processing`` indicates that the catalog is still being deployed. Keep monitoring the state.
* A state of ``InputRequired`` or ``Active`` indicates that the catalog deployment has completed. You can now publish apps and assign subscribers to the catalog.

REST Example
============

This example illustrates how to deploy a catalog to a customer's account using REST API.

Request
~~~~~~~
::

  POST https://catalogs.apps.cloud.com/{customerid}/{siteId}/catalogs/deploy HTTP/1.1
  Accept: application/json
  Content-Type: application/json
  Authorization: CwsAuth bearer=<token-from-prerequisites>
  
  {
    "AddCatalog": {
      "Name": "Finance Catalog"
    },
    "AddAzureConfiguration": {
      "AzureSubscriptionId": "c38c7a5c-...",
      "AzureResourceGroup": "FinanceRG",
      "AzureVNet": "FinanceVnet",
      "AzureSubnet": "FinanceSubnet"
    },
    "AddCatalogDomain": {
      "DomainName": "customer.local",
      "DomainOu": "",
      "ServiceAccountName": "admin"
    },
    "AddCatalogResourceLocation": {
      "Name": "Finance Resource Location",
      "AzureResourceGroup": "FinanceRG",
      "OrganizationalUnit": ""
    },
    "AddCatalogImage": {
      "TemplateId": "ba75cfe9-c08a-4cd6-9ac5-5cbb39a139a7",
      "CitrixPrepared": true
    },
    "AddCatalogCapacity": {
      "ComputeWorker": {
        "UsePremiumStorage": false,
        "UseAzureHUB": false,
        "MaxUsersPerVM": 16,
        "InstanceName": "Standard_D2_v2"
      },
      "ScaleSettings": {
        "MinInstances": 1,
        "MaxInstances": 2,
        "Weekdays": {"monday": true, "tuesday": true, "wednesday": true, "thursday": true, "friday": true, "saturday": false, "sunday": false},
        "PeakStartTime": 9,
        "PeakEndTime": 17,
        "PeakTimeZone": "Eastern Standard Time",
        "PeakMinInstances": 2
      },
      "SessionTimeout": 15,
      "IsActive": true
    },
    "DeploySecrets": {
      "ClientId": "b3175618-...",
      "ClientSecret": "iEnl...",
      "ServiceAccountPassword": "..."
    }
  }

Response
~~~~~~~~
::

  HTTP/1.1 200 OK
  Content-Length: 38
  Content-Type: application/json; charset=utf-8
  Date: Wed, 22 Nov 2017 15:05:23 GMT
  
  "55cab14f-efb7-4bd6-bd9c-ebe991c9d165"

Interpreting the request
~~~~~~~~~~~~~~~~~~~~~~~~

The request JSON body contains all the fields needed to deploy a new catalog. On the XenApp Essentials UI, these fields are split into seven different steps which are described below.

Step 1: Pick a Name (AddCatalog)

==================================  =============================================================
Property Name                       | Description
==================================  =============================================================
Name                                | [Required] The name you want to give to your new catalog. 
IsUserProvidedAzureResourceGroup    | [Optional] Internal use only. Do not set it. 
IsDomainJoined                      | [Optional] Internal use only. Do not set it.
==================================  =============================================================

Step 2: Link your Azure Subscription (AddAzureConfiguration)

=====================  ==========================================================================
Property Name          | Description
=====================  ==========================================================================
AzureSubscriptionId    | [Required] The Azure subscription ID to be used by the catalog.
                       | The connectors and VDAs will be hosted in this subscription. 
AzureResourceGroup     | [Required] The Azure Resource group where the VNET resides.
AzureVNet              | [Required] The Azure VNET to be used for this catalog. Ensure that
                       | this VNET has connectivity to your domain and to the internet.
AzureSubnet            | [Required] The Azure subnet to be used for this catalog.
=====================  ==========================================================================

Step 3: Join local domain (AddCatalogDomain)

=====================  ==========================================================================
Property Name          | Description
=====================  ==========================================================================
DomainName             | [Required] The fuly qualified domain name to be used by the catalog.
                       | The connector and VDA machines will be joined to this domain.
DomainOu               | [Optional] The Active Directory OU. The VDA machine accounts will be
                       | created in this OU.
ServiceAccountName     | [Required] The domain service account name in UPN format. Ensure that
                       | this account has premissions to join machines to the domain.
=====================  ==========================================================================

Step 4: Connect to a resource location (AddCatalogResourceLocation)

====================================  ===========================================================
Property Name                         | Description
====================================  ===========================================================
IsAssignedExistingResourceLocation    | [Optional] Internal use only. Do not set it.
Name                                  | [Required] The name you want to give to your resource
                                      | location. Note: If the catalog is connected to an 
                                      | existing Resource Location, this value will be ignored.
AzureResourceGroup                    | [Required] The Azure Resource group to create the 
                                      | connector machines.
OrganizationalUnit                    | [Optional] The Active Directory OU. The connector 
                                      | machines accounts will be created in this OU.
====================================  ===========================================================

Step 5: Choose master image (AddCatalogImage)

=====================  ==========================================================================
Property Name          | Description
=====================  ==========================================================================
TemplateId             | [Required] The ID of the master image you want to use for the catalog. 
                       | See `how to get all master images <how_to_get_all_master_images.html>`_ to get the ``imageid`` of all your posted images.
CitrixPrepared         | [Optional] This should be set to false for a customer provided image.  
                       | This should be set to true if you are creating the catalog with a  
                       | Citrix Prepared image. Default value if false.
=====================  ==========================================================================

Step 6: Select capacity and manage costs (AddCatalogCapacity)

Property: ComputeWorker

====================  ===========================================================================
Property Name         | Description
====================  ===========================================================================
UsePremiumStorage     | [Optional] Defaults to false. True if you want to use Azure Premium 
                      | disks (SSD). False if you want to use Azure Standard disks (HDD).
UseAzureHUB           | [Optional] Defaults to false. True if you want to use existing 
                      | on-premises Windows Server licenses to provision the VMs in this 
                      | catalog at the base compute rate.
MaxUsersPerVM         | [Required] The max number of user sessions on each VDA machine.
InstanceTypeId        | [Optional] Internal use only. Do not set it.
InstanceName          | [Required] Azure virtual machine size to provision for the VDA.
                      | See `virtual machine size <https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes>`_ for all avaliable azure VM sizes.
====================  ===========================================================================

Property: ScaleSettings

==================  =============================================================================
Property Name       | Description
==================  =============================================================================
MaxUsers            | [Optional] Internal use only. Do not set it.
MinInstances        | [Required] The minimum number of VDA instances running at all times.
MaxInstances        | [Required] The max number of VDA instances to provision for the catalog.
Weekdays            | [Optional] Required only if setting a peak schedule. Set true for days you
                    | want the peak schedule to be enabled. eg. {monday: true, tuesday: true, 
                    | wednesday: true, thursday: true, friday: true, saturday: false, sunday: false}
PeakStartTime       | [Optional] Required only if setting a peak schedule. The houe of the day
                    | when peak schedule begins. Any integer number between 0 and 23.
PeakEndTime         | [Optional] Required only if setting a peak schedule. The houe of the day
                    | when peak schedule ends. Any integer number between 0 and 23.
PeakTimeZone        | [Optional] Required only if setting a peak schedule. The timezone name for 
                    | the peak schedule. See `timezone names <https://msdn.microsoft.com/en-us/library/ms912391(v=winembedded.11).aspx>`_ for a list of valid time zones.
PeakTimeZoneId      | [Optional] Internal use only. Do not set it.
PeakMinInstances    | [Optional] Required only if setting a peak schedule. The minimum number 
                    | of VDA instances running at peak times.
==================  =============================================================================

Property: Others

=================  ==============================================================================
Property Name      | Description
=================  ==============================================================================
SessionTimeout     | [Required] Timeout in minutes after which the subscriber sessions end 
                   | automatically if the session remains idle or is disconnected.                   
IsActive           | [Optional] Internal use only. Do not set it.
=================  ==============================================================================

Step 7: Start Deployment (DeploySecrets)

=========================  ======================================================================
Property Name              | Description
=========================  ======================================================================
ClientId                   | [Required] The client Id to authenticate to Citrix Cloud. 
                           | See `getting started <../../create_api_client.html>`_ section for
                           | more details about creating an API ``ClientId``.
ClientSecret               | [Required] The client secret to authenticate to Citrix Cloud. 
                           | See `getting started <../../create_api_client.html>`_ section for
                           | more details about creating an API ``ClientSecret``.
ServiceAccountPassword     | [Required] The domain service account password.
=========================  ======================================================================

Interpreting the response
~~~~~~~~~~~~~~~~~~~~~~~~~

The response is a GUID string that represents the ``catalogId``. You will need this ``catalogId`` to query the deployment status of the catalog using `how to get catalog information <how_to_get_catalog_information.html>`_.

Powershell Example
==================

This example illustrates how to deploy a catalog to a customer's account using Powershell.

.. code-block:: powershell

  function DeployCatalog {
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
    $requestUri = [string]::Format("https://catalogs.apps.cloud.com/{0}/{1}/catalogs/deploy", $customerId, $siteId)
    $headers = @{"Accept"="application/json";
                 "Content-Type"="application/json"
                 "Authorization"="CWSAuth bearer=$bearerToken"}

    $response = Invoke-RestMethod -Uri $requestUri -Method POST -Headers $headers -Body $jsonBody
    return $response
  }

  $body = @{
    "AddCatalog" = @{
      "Name" = "Finance Catalog";
    }
    "AddAzureConfiguration" = @{
      "AzureSubscriptionId" = "c38c7a5c-...";
      "AzureResourceGroup" = "FinanceRG";
      "AzureVNet" = "FinanceVnet";
      "AzureSubnet" = "FinanceSubnet"
    }
    "AddCatalogDomain" = @{    
      "DomainName" = "customer.local";
      "DomainOu" = "";
      "ServiceAccountName" = "admin"
    }
    "AddCatalogResourceLocation" = @{
      "Name" = "Finance Resource Location";
      "AzureResourceGroup" = "FinanceRG";
      "OrganizationalUnit" = ""
    }
    "AddCatalogImage" = @{
      "TemplateId" = "ba75cfe9-c08a-4cd6-9ac5-5cbb39a139a7";
      "CitrixPrepared" = $true
    }
    "AddCatalogCapacity" = @{
      "ComputeWorker" = @{
        "UsePremiumStorage" = $false;
        "UseAzureHUB" = $false;
        "MaxUsersPerVM" = "16";
        "InstanceName" = "Standard_D2_v2"
      }
      "ScaleSettings" = @{
        "MinInstances" = "1";
        "MaxInstances" = "2";      
        "Weekdays" = @{"monday" = $true; "tuesday" = $true; "wednesday" = $true; "thursday" = $true; "friday" = $true; "saturday" = $false; "sunday" = $false};
        "PeakStartTime" = "9";
        "PeakEndTime" = "17";
        "PeakTimeZone" = "Eastern Standard Time";
        "PeakMinInstances" = "2"
      }
      "SessionTimeout" = "15";
    }
    "DeploySecrets" = @{
      "ClientId" = "b3175618-...";
      "ClientSecret" = "iEnl...";
      "ServiceAccountPassword" = "..."
    }
  }
  
  $customerId = "exampleCust" #Replace with your customerId
  $siteId = "61603f15-cdf9-4c7f-99ff-91636601a795" #Replace with your site ID
  $bearerToken = "ey.." #See Prerequisites for all API calls section for a sample of how to get your bearer token
  $response = DeployCatalog $customerId $siteId $bearerToken (ConvertTo-Json -Depth 3 $body)

C# Example
==========

This example illustrates how to deploy a catalog to a customer's account using C#.

.. code-block:: csharp

  public sealed class AddCatalogModel
  {
      /// <summary>
      /// Name of the catalog
      /// </summary>                    
      [StringLength(38, MinimumLength = 2)]
      [Required]
      public string Name { get; set; }
  }

  public class CatalogAzureConfigurationModel
  {
      /// <summary>
      /// ID of the azure subscription the catalog is associated with
      /// </summary>
      [Required]
      public Guid AzureSubscriptionId { get; set; }

      /// <summary>
      /// Name of the resource group where all objects are going to be located in
      /// </summary>
      [Required]            
      [StringLength(64, MinimumLength = 1)]
      public string AzureResourceGroup { get; set; }

      /// <summary>
      /// Name of the VNet all machines will be connected to
      /// </summary>
      [Required]            
      [StringLength(64, MinimumLength = 2)]
      public string AzureVNet { get; set; }

      /// <summary>
      /// Address range of the machines in the catalog
      /// </summary>
      [Required]            
      [StringLength(80, MinimumLength = 2)]
      public string AzureSubnet { get; set; }
  }

  public class CatalogDomainModel
  {
      /// <summary>
      /// Domain the VMs will join
      /// </summary>
      [Required]            
      [StringLength(63, MinimumLength = 1)]
      public string DomainName { get; set; }

      /// <summary>
      /// OU of the domain
      /// </summary>
      public string DomainOu { get; set; }

      /// <summary>
      /// Name of the service account that will be used to join the domain
      /// </summary>
      [Required]            
      [StringLength(104, MinimumLength = 1)]
      public string ServiceAccountName { get; set; }
  }

  public class CatalogResourceLocationConfiguration
  {
      /// <summary>
      /// The desired name of the resource location that will be created for the catalog
      /// </summary>            
      [StringLength(64, MinimumLength = 1)]
      [Required]
      public string Name { get; set; }

      /// <summary>
      /// Name of the resource location where to provision the connector vdas
      /// </summary>            
      [StringLength(64, MinimumLength = 1)]
      [Required]
      public string AzureResourceGroup { get; set; }

      /// <summary>
      /// Organization Unit associated with computer accounts added for the Resource Location
      /// </summary>
      public string OrganizationalUnit { get; set; }
  }

  public class CatalogTemplateImageModel
  {
      /// <summary>
      /// ID of the Template image to configure for the catalog
      /// </summary>
      [Required]
      public string TemplateId { get; set; }

      /// <summary>
      /// Whether the image was prepared by Citrix, or provided by the customer
      /// </summary>
      public bool CitrixPrepared { get; set; } = false;
  }

  public class CatalogCapacitySettingsModel
  {
      /// <summary>
      /// Compute settings for the catalog
      /// </summary>
      public CatalogComputeWorkerModel ComputeWorker { get; set; }

      /// <summary>
      /// Scale settings for the catalog
      /// </summary>
      public CatalogScaleSettingsModel ScaleSettings { get; set; }

      /// <summary>
      /// Idle timeout for session in the catalog (in mins)
      /// </summary>
      public int SessionTimeout { get; set; }

      /// <summary>
      /// Indicates if the capacity job is currently active
      /// This should always be set to true
      /// </summary>
      public bool IsActive { get; }
  }

  public class CatalogComputeWorkerModel
  {
      /// <summary>
      /// Indicates if Premium Storage will be used
      /// </summary>
      public bool UsePremiumStorage { get; set; }

      /// <summary>
      /// Indicates if the catalog VMs should be deployed with Azure HUB license
      /// </summary>
      public bool UseAzureHUB { get; set; }

      /// <summary>
      /// Number of concurrent users per VM
      /// </summary>
      public int MaxUsersPerVM { get; set; }

      /// <summary>
      /// Type of VM to create for VDA machines
      /// </summary>            
      public string InstanceName { get; set; }
  }

  public class CatalogScaleSettingsModel
  {            
      /// <summary>
      /// Min number of active vms for the catalog
      /// </summary>
      public int MinInstances { get; set; }

      /// <summary>
      /// Number of VMs that will be provisioned for  this catalog
      /// </summary>
      public int MaxInstances { get; set; }

      /// <summary>
      /// Days of the week that are included in peek days
      /// </summary>
      public Dictionary<string, bool> Weekdays { get; set; }

      /// <summary>
      /// Hour of day when peak usage begins
      /// </summary>
      public int PeakStartTime { get; set; }

      /// <summary>
      /// Hour of day when peak usage ends
      /// </summary>
      public int PeakEndTime { get; set; }

      /// <summary>
      /// Display of the peak usage timezone
      /// </summary>
      public string PeakTimeZone { get; set; }
      
      /// <summary>
      /// Min number of insances that should be running durring peak hours
      /// </summary>
      public int PeakMinInstances { get; set; }
  }

  public class DeploySecretsModel
  {
      /// <summary>
      /// The client id that has admin permissions to Citrix Cloud
      /// </summary>
      [Required]
      public string ClientId { get; set; }

      /// <summary>
      /// The client secret that has admin permissions to Citrix cloud
      /// </summary>
      [Required]
      public string ClientSecret { get; set; }

      /// <summary>
      /// Service account password for required in domain joining. This will be stored in a azure vault. 
      /// </summary>        
      public string ServiceAccountPassword { get; set; }
  }

  public class CatalogConfigDeployModel
  {
      /// <summary>
      /// Step 1 Properties
      /// </summary>
      public AddCatalogModel AddCatalog { get; set; }

      /// <summary>
      /// Step 2 Properties
      /// </summary>
      public CatalogAzureConfigurationModel AddAzureConfiguration { get; set; }

      /// <summary>
      /// Step 3 Properties
      /// </summary>
      public CatalogDomainModel AddCatalogDomain { get; set; }

      /// <summary>
      /// Step 4 Properties
      /// </summary>
      public CatalogResourceLocationConfiguration AddCatalogResourceLocation { get; set; }

      /// <summary>
      /// Step 5 Properties
      /// </summary>
      public CatalogTemplateImageModel AddCatalogImage { get; set; }

      /// <summary>
      /// Step 6 Properties
      /// </summary>
      public CatalogCapacitySettingsModel AddCatalogCapacity { get; set; }

      /// <summary>
      /// Step 7 Properties
      /// </summary>
      public DeploySecretsModel DeploySecrets { get; set; }
  }
  
.. code-block:: csharp

  public static async Task<string> DeployCatalog(
      string customerId,
      string siteId,
      string bearerToken,
      CatalogConfigDeployModel model)
  {   
      var requestUri = string.Format("https://catalogs.apps.cloud.com/{0}/{1}/catalogs/deploy", customerId, siteId);
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

See `how to get catalog information <how_to_get_catalog_information.html>`_ to monitor the deployment status of your catalog.

Comments
========

.. disqus::