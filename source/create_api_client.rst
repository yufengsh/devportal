======================
Creating an API client
======================

Create an API client in Citrix Cloud in order to call APIs from a script or other
external program.

* `Sign in <https://citrix.cloud.com>`_ to the Citrix Cloud administrator console.

* Click the "hamburger menu" |hamburger| in the upper left corner of the console.

* Select "Identity and Access Management" from the drop-down menu.
    *Note: if this option does not appear, you may not have adequate permissions to
    create an API client.  Ask your administrator to increase your permissions if
    necessary.*

* Click on the "API Access" tab.

* Type a name for your client, and click "Create Client".

* You will be given an ID and Secret for your client.  Download or copy these to a
  safe place.


API client scope and permissions
================================

API clients in Citrix Cloud are always tied to **one administrator** and
**one customer**.  The API clients that you create are not visible to other admins
in your organization; and, if you have access to more than one customer, you will
need to create API client(s) within each customer that you want to call APIs for.

API clients owned by a Citrix Cloud administrator are also automatically restricted
to the rights of that administrator, within the customer.  For example, if you are
restricted to access only Notifications within a customer, then any API clients
that you create within that customer are also automatically restricted to access
only Notifications.

If an administrator's access is reduced at any point, all API clients owned by that
administrator also automatically have their access reduced as well.

If an administrator's access is removed from a customer (i.e. they are removed from
the list of admins within that customer), then all of the administrator's API clients
with access to that customer are automatically deleted.

.. important:: If an administrator account is federated from Azure Active Directory
    or other federated identity provider, and the administrator account is *Disabled*,
    the administrator's API clients **remain active**.  For this reason it is important to
    delete administrator accounts from Citrix Cloud when you wish to completely revoke
    access for those administrators.


.. |hamburger| image:: _static/hamburger.png
    :scale: 25%


Comments
========

.. disqus::
