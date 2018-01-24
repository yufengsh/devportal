===================================
Delivery Groups APIs - Tech Preview
===================================
The Delivery Groups APIs provide a set of REST APIs that can be used to automate the management of Delivery Groups.
The complete set of available APIs can be viewed and tried out at Delivery Groups APIs.

Using this documentation
========================
* Read the `prerequisites <../prerequisites.html>`_ and ensure that you have the ``bearer token`` and ``customerId``.
* Use the following Delivery Group APIs.

API list
========
* GET /v1/deliverygroups
* GET /v1/deliverygroups/{id}/applications
* GET /v1/deliverygroups/{id}/machinecatalogs
* GET /v1/deliverygroups/{id}/machines
* GET /v1/deliverygroups/{id}/desktops
* GET /v1/deliverygroups/{id}/tags
* GET /v1/deliverygroups/{id}/usage
* GET /v1/deliverygroups/{id}/appdisks
* GET /v1/deliverygroups/{id}/appgroups
* GET /v1/deliverygroups/{id}/scopes

Get the API detail information from:
`<https://{customerid}.xendesktop.net/citrix/orchestration/api/swagger/ui/index#/>`

REST example
============

This example illustrates how to get delivery groups using REST API.

Request
~~~~~~~
::

  GET http://{customerid}.xendesktop.net/citrix/orchestration/api/v1/deliverygroups HTTP/1.1
  Accept: application/json
  Content-Type: application/json
  Authorization: bearer <token-from-prerequisites>

Response
~~~~~~~~
::

  HTTP/1.1 200 OK
  Content-Length: 1014
  Content-Type: application/json; charset=utf-8
  Date Mon, 15 Jan 2018 03:02:52 GMT

  {
    "Items": [
        {
            "Id": "8fcdd17b-3f3e-404b-bdf7-220c6dd6353c",
            "Uid": 1,
            "Uuid": "8fcdd17b-3f3e-404b-bdf7-220c6dd6353c",
            "Name": "DeliveryGroup0000",
            "InMaintenanceMode": false,
            "DeliveryKind": "DesktopsAndApps",
            "DesktopKind": "Shared",
            "DesktopsFaulted": 0,
            "MinimumFunctionalLevel": "L7_9",
            "TotalApplications": 21,
            "TotalDesktops": 2,
            "DesktopsAvailable": 0,
            "DesktopsUnregistered": 2,
            "SessionCount": 0,
            "DesktopsDisconnected": 0,
            "SessionSupport": "MultiSession",
            "IsRemotePC": false,
            "HasBeenPromoted": false,
            "HasBeenPromotedFrom": "Unknown",
            "CloudLibraryManaged": false,
            "Delivering": [
                "Apps"
            ],
            "AppDNAState": "Unsupported",
            "AppDisks": [],
            "CustomerId": null,
            "SiteId": null,
            "Description": null,
            "Enabled": true,
            "RequireUserHomeZone": false,
            "IsBroken": false
        }
      ]
  }


Interpreting the response
~~~~~~~~~~~~~~~~~~~~~~~~~

======================   ==========================================================================
Property Name            | Description
======================   ==========================================================================
Id                       | Id of the Delivery Group.
Uid                      | Uid of the Delivery Group.
                         | This should only be used in conjunction with the PowerShell SDK.
Uuid                     | UUID of the Delivery Group.
                         | This should only be used in conjunction with the PowerShell SDK.
Name                     | Name of the Delivery Group.
InMaintenanceMode        | Specifies whether the machines in the Delivery Group are in
                         | maintenance mode or not.
DeliveryKind             | Data contract for DeliveryKind = ['Unknown', 'DesktopsOnly',
                         | 'DesktopsAndApps', 'AppsOnly'].
DesktopKind              | Data contract for DesktopKind = ['Unknown', 'Private', 'Shared'].
DesktopsFaulted          | The number of machines in the Delivery Group whose FaultState is
                         | not None.
MinimumFunctionalLevel   | Data contract for FunctionalLevelModel = ['Unknown', 'L5', 'LMIN',
                         | 'L7', 'L7_6', 'L7_7', 'L7_8', 'L7_9', 'LMAX'].
TotalApplications        | Total number of applications associated with the Delivery Group.
TotalDesktops            | Total number of machines in the Delivery Group.
DesktopsAvailable        | The number of machines in the Delivery Group in state Available;
                         | this is the number of machines with no sessions present.
DesktopsUnregistered     | The number of machines in the Delivery Group that are currently
                         | unregistered.
SessionCount             | The total number of user sessions currently running on all of the
                         | machines in the Delivery Group.
DesktopsDisconnected     | The number of disconnected sessions present on machines in the
                         | Delivery Group.
SessionSupport           | Data contract for SessionSupport = ['SingleSession', 'MultiSession',
                         | 'Unknown'].
IsRemotePC               | Specifies whether the Delivery Group is a Remote PC Delivery Group.
HasBeenPromoted          | Indicates whether the Delivery Group has been promoted from a
                         | previous functional level.
HasBeenPromotedFrom      | Data contract for FunctionalLevelModel = ['Unknown', 'L5', 'LMIN',
                         | 'L7', 'L7_6', 'L7_7', 'L7_8', 'L7_9', 'LMAX'].
CloudLibraryManaged      | Indicates whether the Delivery Group is managed by Citrix Cloud.
Delivering               | Indicates the type of resources being delivered from the Delivery
                         | Group.
AppDNAState              | Data contract for AppDNAState = ['Unknown', 'Unsupported',
                         | 'Importing', 'Capturing', 'Analyzing', 'Error', 'Compatible',
                         | 'ProblemsDetected'].
AppDisks                 | The Application Disks used by machines in the Delivery Group.
CustomerId               | Identity of tenant associated with Delivery Group. Not applicable
                         | (always blank) in non-multitenant sites.
SiteId                   | Identity of site associated with Delivery Group.
Description              | Description of the Delivery Group.
Enabled                  | Specifies whether the Delivery Group is enabled or not; disabled
                         | Delivery Groups do not appear to users.
RequireUserHomeZone      | Indicates whether the user's home zone is required to launch resources
                         | from this Delivery Group.
IsBroken                 | Indicates whether the Delivery Group is in an invalid state, such as
                         | invalid access policy rules.
======================   ==========================================================================

Comments
========

.. disqus::
