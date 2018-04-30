===================================================
Determine service entitlement status for a customer
===================================================

In order to find out which services a customer is entitled to, the days remaining
on each of those entitlements, and the "quantity" of each entitlement, you can use
the ``serviceStates`` API within Citrix Cloud.

Prerequisites.
==============

1) `Create an API client </create_api_client.html>`_.
2) Read the `authenticate using the API client </authenticate_api_client.html>`_,
   explanation of how to get Citrix Cloud bearer token.
3) Use the obtained bearer token, `as explained here </call_api_bearer_token.html>`_,
   in the section below.
4) You need the ``customer`` to use as first part of the URL path. Getting the
   value `is explained here </customer_id.html>`_.

Getting Service States.
=======================

The API call needed to get service states is shown below:

Request
~~~~~~~
::

  GET https://core.citrixworkspacesapi.net/acme/serviceStates HTTP/1.1
  Accept: application/json
  Authorization: CwsAuth Bearer=<token-from-call-to-trust>

.. note:: Replace ``acme`` above with the ID of the customer you wish to query.

Response
~~~~~~~~
::

  HTTP/1.1 200 OK
  Cache-Control: no-cache
  Content-Length: 2031
  Content-Type: application/json; charset=utf-8
  Date: Fri, 23 Dec 2016 22:24:00 GMT
  X-Cws-TransactionId: bfc9b56c-bcd0-4cf1-9ea1-3da4d48a81c0

  {
      "items": [
          {
              "serviceName": "applayering",
              "state": "Production",
              "type": "Production",
              "quantity": 1,
              "daysToExpiration": 622,
              "futureEntitlementStartDate": null
          },
          {
              "serviceName": "xendesktop",
              "state": "Production",
              "type": "Production",
              "quantity": 100,
              "daysToExpiration": 622,
              "futureEntitlementStartDate": null
          },
          ...
      ]
  }

Interpreting the response
~~~~~~~~~~~~~~~~~~~~~~~~~

``serviceName`` is the name of each product service within Citrix Cloud.

  * **applayering**: App Layering.

  * **xendesktop**: XenApp & XenDesktop service.

  * **lifecyclemanagement**: Smart tools.

  * **sharefile**: ShareFile.

  * **xenmobile**: XenMobile service.

  * **browserservice**: Secure browser service.

  * **licensing**: License usage insights service.

  * **office365**: Office365 service (labs).

  * **mas**: NetScaler Management and Analytics service.

  * **netscalergateway**: NetScaler Gateway service.

  * **aggregation**: On-premises Site Aggregation service.

  * **cas**: Citrix Analytics service.

  * **waf**: Web App Security service.

``state`` can be one of:

  * **NotOnboarded**: The customer has not yet been onboarded to the service.  Customers get onboarded to services when they receive an entitlement to the service.

  * **Expired**: The entitlement to the service has expired.

  * **Default**: The service has not received any entitlements.

  * **ProductionTrialDenied**: A trial request for the service has been denied.

  * **ProductionTrialPending**: A trial for the service has been requested and is awaiting approval.

  * **ProductionTrialApproved**: A trial for the service has been granted, and is currently being provisioned.

  * **ProductionTrial**: The service is active, within a trial period.

  * **ProductionTrialDeleted**: The service trial period was completed, and the service data was deleted.

  * **ProductionPending**: The service is currently being provisioned.

  * **Production**: The service is active in a production mode.

  * **PartnerProductionPending**: The service is being provisioned in a partner (e.g. resellable) mode.

  * **PartnerProduction**: The service is active in a partner (e.g. resellable) mode.

  * **NotOnboardedTrialPending**: The customer has not yet been onboarded to the service but a trial has already been requested.

.. note::
  The states can be generally grouped into three categories:

    #. The customer is not currently entitled to the service: **NotOnboarded**, **Expired**, **Default**, **ProductionTrialDenied**, **ProductionTrialPending**, **ProductionTrialDeleted**.
  
    #. The customer is entitled to the service but the service has not yet been provisioned: **ProductionTrialApproved**, **ProductionPending**, **PartnerProductionPending**, **NotOnboardedTrialPending**.

    #. The customer is entitled to the service and it is fully provisioned: **ProductionTrial**, **Production**, **PartnerProduction**.

``type`` indicates the service mode:

  * **Production**: The service operates with full capabilities as defined by the terms of its license.

  * **ProductionTrial**: The customer has temporary access in order to evaluate the service.  Note that the service may also have lower availability and redundancy than **Production** mode.

  * **PartnerProduction**: Similar to **Production** but allowing the partner to resell the service.

  * **Default**: The customer is not entitled to the service.

``quantity`` indicates the number of licenses currently active for the service.  Consult the service documentation to determine what the ``quantity`` metric refers to (users, devices, capacity units, etc).

``daysToExpiration`` indicates the number of days remaining in the license term.

.. note:: if a customer has purchased multiple licenses for the same service, with different end dates, ``daysToExpiration`` will reflect the longest remaining license term.

``futureEntitlementStartDate``, if present, indicates a future date at which the service entitlement will take effect.


C# Example
==========

This example illustrates how to enumerate the customer's service states.

.. code-block:: csharp

  public static async Task<ItemCollection<ServiceStateResponse>> GetServiceStates(string bearerToken, string customer)
  {
      var client = new HttpClient();
      client.DefaultRequestHeaders.Accept.ParseAdd("application/json");
      client.DefaultRequestHeaders.Authorization =
                new AuthenticationHeaderValue("CwsAuth", "Bearer=" + bearerToken);

      var response = await client.GetAsync(
          "https://core.citrixworkspacesapi.net/" + customer + "/serviceStates"
      );

      response.EnsureSuccessStatusCode();

      var content = await response.Content.ReadAsStringAsync();
      return JsonConvert.DeserializeObject<ItemCollection<ServiceStateResponse>>(content);
  }

  public class ItemCollection<T>
  {
      public IList<T> Items { get; set; }
  }

  public class ServiceStateResponse
  {
      public string ServiceName { get; set; }
      public string State { get; set; }
      public string Type { get; set; }
      public int? Quantity { get; set; }
      public int? DaysToExpiration { get; set; }
      public string FutureEntitlementStartDate { get; set; }
  }

Comments
========

.. disqus::
