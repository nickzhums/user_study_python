Quickstart Tutorial - Resource Management (Preview Libraries)
===============================================================

We are excited to announce that a new set of management libraries are now in Public Preview.
Those packages share a number of new features such as Azure Identity support,
HTTP pipeline, error-handling.,etc, and they also follow the new Azure SDK guidelines which
create easy-to-use APIs that are idiomatic, compatible, and dependable.

You can find the details of those new libraries `here <https://azure.github.io/azure-sdk/releases/latest/#python>`__

In this basic quickstart guide, we will walk you through how to
authenticate to Azure using the preview libraries and start interacting with
Azure resources. There are several possible approaches to
authentication. This document illustrates the most common scenario

Prerequisites
-------------

| You will need the following values to authenticate to Azure
 
- **Subscription ID**
- **Client ID** 
- **Client Secret** 
- **Tenant ID** 

These values can be obtained from the portal, here's the instructions:

Get Subscription ID
^^^^^^^^^^^^^^^^^^^

1. Login into your Azure account (https://portal.azure.com/)
2. Select Subscriptions in the left sidebar
3. Select whichever subscription is needed
4. Click on Overview
5. Copy the Subscription ID

Get Client ID / Tenant ID
^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. Login into your Azure account (https://portal.azure.com/)
2. Select "Azure Active Directory" in the left sidebar
3. Click "App Registrations" in the left sidebar
4. Create a new application or select the application which you have created
5. Copy "Application (Client) ID" and "Directory (Tenant) ID"

Get Client Secret
^^^^^^^^^^^^^^^^^
1. Login into your Azure account (https://portal.azure.com/)
2. Select "Azure Active Directory" in the left sidebar
3. Click "App Registrations" in the left sidebar
4. Create a new application or select the application which you have created
5. Click "Certificates & Secrets"
6. Click "+ New client secret"
7. Type description and click "Add"
8. Copy and store the key value. You won’t be able to retrieve it after
   you leave this page.
   
If you are interested in detailed information on Azure Active Directory, please refer to `this document <https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal>`__

Setting Environment Variables
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

After you obtained the values, you need to set the following values as
your environment variables

-  ``AZURE_CLIENT_ID``
-  ``AZURE_CLIENT_SECRET``
-  ``AZURE_TENANT_ID``
-  ``AZURE_SUBSCRIPTION_ID``

To set the following environment variables on your development system:

Windows (Note: Administrator access is required)

1. Open the Control Panel
2. Click System Security, then System
3. Click Advanced system settings on the left
4. Inside the System Properties window, click the Environment Variables… button.
5. Click on the property you would like to change, then click the Edit… button. If the property name is not listed, then click the New… button.

Linux-based OS
::

    export AZURE_CLIENT_ID="__CLIENT_ID__"
    export AZURE_CLIENT_SECRET="__CLIENT_SECRET__"
    export AZURE_TENANT_ID="__TENANT_ID__"
    export AZURE_SUBSCRIPTION_ID="__SUBSCRIPTION_ID__"
    
Add Role Assignment
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
In order to access resources in a subscription, you must assign a role to the application. Please `follow the instructions here 
<https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application>`__ to add the role assignment

Authentication and Creating Resource Management Client
------------------------------------------------------

Now that the environment is setup, all you need to do is to create an
authenticated client. Our default option is to use
**DefaultAzureCredential** and in this guide we have picked
**Resources** and **Netowrk** as our target services, but you can set it up similarly for any other service that you are using. To view all the services that are supported by the SDK, please refer to the `API references <https://docs.microsoft.com/python/api/?view=azure-python>`__

To authenticate to Azure and create
a management client, simply do the following:

::

    # Import the target services 
    import azure.mgmt.resource
    import azure.mgmt.network
    from azure.identity import DefaultAzureCredential
    ...
    subscription_id = os.environ.get("AZURE_SUBSCRIPTION_ID")
    credential = DefaultAzureCredential()
    
    # Create a management client for this service
    resource_client = azure.mgmt.resource.ResourceManagementClient(credential=credential, subscription_id=subscription_id)
    network_client = azure.mgmt.network.NetworkManagementClient(credential=credential, subscription_id=subscription_id)

More information and different authentication approaches using Azure Identity can be found in
`this document <https://docs.microsoft.com/python/api/overview/azure/identity-readme?view=azure-python>`__

Interacting with Azure Resources
--------------------------------

Now that we are authenticated, we can use our management client to make API
calls. Let's demonstrate management client's usage by showing concrete examples 

Managing Resource Groups
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We can use the Resource client (``azure.mgmt.resource.ResourceManagementClient``) we have created to perform operations on Resource Group. In this example, we will show to manage Resource Groups.

**Create a resource group**

::

    LOCATION = "eastus"
    GROUP_NAME = "my_resource_group_name"
    group = resource_client.resource_groups.create_or_update(
        GROUP_NAME,
        {'location': LOCATION}
    )

**Update a resource group**

::

    GROUP_NAME = "my_resource_group_name"
    group.tags = {
        "environment":"test",
        "department":"tech"
    }
    updated_group = resource_client.resource_groups.create_or_update(GROUP_NAME, group)

**List all resource groups**

::

    group_list = self.resource_client.resource_groups.list()
    for g in group_list:
        print(g.name)

**Delete a resource group**

::

    delete_async_op = resource_client.resource_groups.begin_delete(GROUP_NAME)
    delete_async_op.wait()
    
Managing Network Resources
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We can use the Network client (``azure.mgmt.resource.NetworkManagementClient``) we have created to perform operations on Network related resources. In this example, we will show how to manage Public IP Addresses.

**Create a Network Public IP Address**
::
    GROUP_NAME = "testgroup"
    PUBLIC_IP_ADDRESS = "public_ip_address_name"
 
    # Create Resource Group
    resource_client.resource_groups.create_or_update(
        GROUP_NAME,
        {"location": "eastus"}
    )

    # Create Public IP Address
    public_ip_address = network_client.public_ip_addresses.begin_create_or_update(
        GROUP_NAME,
        PUBLIC_IP_ADDRESS,
        {
          "location": "eastus"
        }
    ).result()
    print("Create Public IP Address:\n{}".format(public_ip_address))

**Get Public IP Address**
::
    public_ip_address = network_client.public_ip_addresses.get(
        GROUP_NAME,
        PUBLIC_IP_ADDRESS
    )
    print("Get Public IP Address:\n{}".format(public_ip_address))

**Update Public IP Address**
::
    # Update Public IP Address
    public_ip_address = network_client.public_ip_addresses.update_tags(
        GROUP_NAME,
        PUBLIC_IP_ADDRESS,
        {
          "tags": {
            "tag1": "value1",
            "tag2": "value2"
          }
        }
    )
    print("Updated Public IP Address \n{}".format(public_ip_address))

**Delete Public IP Address**
::
    # Delete Public IP Address
    public_ip_address = network_client.public_ip_addresses.begin_delete(
        GROUP_NAME,
        PUBLIC_IP_ADDRESS
    ).result()
    print("Delete Public IP Address.\n")

Need help?
----------
- File an issue via `Github Issues <https://github.com/Azure/azure-sdk-for-python/issues>`__ and make sure you add the "Preview" label to the issue
- Check `previous questions <https://stackoverflow.com/questions/tagged/azure+python>`__ or ask new ones on StackOverflow using azure and python tags.

Contributing
------------
For details on contributing to this repository, see the contributing guide.

This project welcomes contributions and suggestions. Most contributions require you to agree to a Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us the rights to use your contribution. For details, visit https://cla.microsoft.com.

When you submit a pull request, a CLA-bot will automatically determine whether you need to provide a CLA and decorate the PR appropriately (e.g., label, comment). Simply follow the instructions provided by the bot. You will only need to do this once across all repositories using our CLA.

This project has adopted the Microsoft Open Source Code of Conduct. For more information see the Code of Conduct FAQ or contact opencode@microsoft.com with any additional questions or comments.
