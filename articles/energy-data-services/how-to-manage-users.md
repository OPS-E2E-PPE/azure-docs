---
title: How to manage users in Microsoft Azure Data Manager for Energy
description: This article describes how to manage users in Azure Data Manager for Energy
author: shikhagarg1
ms.author: shikhagarg
ms.service: energy-data-services
ms.topic: how-to
ms.date: 08/19/2022
ms.custom: template-how-to
---

# How to manage users
In this article, you learn how to manage users and their memberships in OSDU groups in Azure Data Manager for Energy. [Entitlements APIs](https://community.opengroup.org/osdu/platform/security-and-compliance/entitlements/-/tree/master/) are used to add or remove users to OSDU groups and to check the entitlements when the user tries to access the OSDU services or data. For more information about OSDU groups, see [entitlement services](concepts-entitlements.md).


## Prerequisites
1. Create an Azure Data Manager for Energy instance using the tutorial at [How to create Azure Data Manager for Energy instance](quickstart-create-microsoft-energy-data-services-instance.md).
2. Generate the access token needed to call the Entitlements APIs.
3. Get various parameters of your instance such as client-id, client-secret, etc.  
4. Keep all these parameter values handy as they are needed for executing different user management requests via the Entitlements API. 

## Fetch parameters
#### Find `tenant-id`
1. Navigate to the Microsoft Entra account for your organization. You can search for "Microsoft Entra ID" in the Azure portal's search bar.
2. Locate `tenant-id` under the basic information section in the *Overview* tab.
3. Copy the `tenant-id` and paste it into an editor to be used later.  

:::image type="content" source="media/how-to-manage-users/azure-active-directory.png" alt-text="Screenshot of search for Microsoft Entra ID.":::

:::image type="content" source="media/how-to-manage-users/tenant-id.png" alt-text="Screenshot of finding the tenant-id.":::

#### Find `client-id`
It's the same value that you use to register your application during the provisioning of your [Azure Data Manager for Energy instance](quickstart-create-microsoft-energy-data-services-instance.md). It is often referred to as `app-id`.

1. Find the `client-id` in the *Essentials* pane of Azure Data Manager for Energy *Overview* page.
2. Copy the `client-id` and paste it into an editor to be used later.
3. Currently, one Azure Data Manager for Energy instance allows one app-id to be as associated with one instance.

> [!IMPORTANT]
> The 'client-id' that is passed as values in the entitlement API calls needs to be the same that was used for provisioning your Azure Data Manager for the Energy instance.

:::image type="content" source="media/how-to-manage-users/client-id-or-app-id.png" alt-text="Screenshot of finding the client-id for your registered App.":::

#### Find `client-secret`
A `client-secret` is a string value your app can use in place of a certificate to identify itself. It is sometimes referred to as an application password. 

1. Navigate to *App Registrations*.
2. Open 'Certificates & secrets' under the *Manage* section.
3. Create a `client-secret` for the `client-id` that you used to create your Azure Data Manager for Energy instance.
4. Add one now by clicking on *New Client Secret*.
5. Record the `secret's value` for later use in your client application code.
6. The access token of the `app-id` and client secret has the Infra Admin access to the instance.

> [!CAUTION]
> Don't forget to record the secret's value. This secret value is never displayed again after you leave this page of 'client secret' creation.

:::image type="content" source="media/how-to-manage-users/client-secret.png" alt-text="Screenshot of finding the client secret.":::

#### Find the `URL` for your Azure Data Manager for Energy instance
1. Navigate to your Azure Data Manager for Energy *Overview* page on the Azure portal.
2. Copy the URI from the essentials pane. 

:::image type="content" source="media/how-to-manage-users/endpoint-url.png" alt-text="Screenshot of finding the URL from Azure Data Manager for Energy instance.":::

#### Find the `data-partition-id` 
1. You have two ways to get the list of data partitions in your Azure Data Manager for Energy instance.
2. One option is to navigate the *Data Partitions* menu item under the Advanced section of your Azure Data Manager for Energy UI.

:::image type="content" source="media/how-to-manage-users/data-partition-id.png" alt-text="Screenshot of finding the data-partition-id from the Azure Data Manager for Energy instance.":::

3. Another option is to click on the *view* below the *data partitions* field in the essentials pane of your Azure Data Manager for Energy *Overview* page. 

:::image type="content" source="media/how-to-manage-users/data-partition-id-second-option.png" alt-text="Screenshot of finding the data-partition-id from the Azure Data Manager for Energy instance overview page.":::

:::image type="content" source="media/how-to-manage-users/data-partition-id-second-option-step-2.png" alt-text="Screenshot of finding the data-partition-id from the Azure Data Manager for Energy instance overview page with the data partitions.":::
## Generate service principal access token

1. Run the below curl command in Azure Cloud Bash after replacing the placeholder values with the corresponding values found earlier in the above steps.
 
**Request format**

```bash
curl --location --request POST 'https://login.microsoftonline.com/<tenant-id>/oauth2/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'grant_type=client_credentials' \
--data-urlencode 'scope=<client-id>.default' \
--data-urlencode 'client_id=<client-id>' \
--data-urlencode 'client_secret=<client-secret>' \
--data-urlencode 'resource=<client-id>'
```

**Sample response**

```JSON
    {
        "token_type": "Bearer",
        "expires_in": 86399,
        "ext_expires_in": 86399,
        "access_token": "abcdefgh123456............."
    }
```
2. Copy the `access_token` value from the response. You need it to pass as one of the headers in all calls to the Entitlements APIs. 

## Fetch OID
`object-id` (OID) is the Microsoft Entra user Object ID.

1. Find the 'object-id' (OID) of the user(s) first. If you are managing an application's access, you must find and use the application ID (or client ID) instead of the OID.
2. Input the `object-id` (OID) of the users (or the application or client ID if managing access for an application) as parameters in the calls to the Entitlements API of your Azure Data Manager for Energy instance. 

:::image type="content" source="media/how-to-manage-users/azure-active-directory-object-id.png" alt-text="Screenshot of finding the object-id from Microsoft Entra ID.":::

:::image type="content" source="media/how-to-manage-users/profile-object-id.png" alt-text="Screenshot of finding the object-id from the profile.":::

## First time addition of users in a new data partition
1. In order to add entitlements to a new data partition of Azure Data Manager for Energy instance, use the access token of the app that was used to provision the instance.
2. Get the service principal access token using [Generate service principal access token](how-to-manage-users.md#generate-service-principal-access-token).
3. If you try to directly use user tokens for adding entitlements, it results in 401 error. The service principal access token must be used to add initial users in the system and those users (with admin access) can then manage more users.
4. Use the service principal access token to do these three steps using the commands outlined in the following sections.
   1. Add the users to the `users@<data-partition-id>.<domain>` OSDU group.
   1. Get the OSDU group such as `service.legal.editor@<data-partition-id>.<domain>` you want to add the user to.
   1. Add the users to that group.

## Get the list of all available groups in a data partition

Run the below curl command in Azure Cloud Bash to get all the groups that are available for your Azure Data Manager for the Energy instance and its data partitions.

```bash
    curl --location --request GET "https://<URI>/api/entitlements/v2/groups/" \
    --header 'data-partition-id: <data-partition>' \
    --header 'Authorization: Bearer <access_token>'
```

## Add users to an OSDU group in a data partition

1. Run the below curl command in Azure Cloud Bash to add the user(s) to the "Users" group using the Entitlement service.
2. The value to be sent for the param `email` is the `Object_ID` (OID) of the user and not the user's email.

```bash
    curl --location --request POST 'https://<URI>/api/entitlements/v2/groups/<group-name>@<data-partition-id>.dataservices.energy/members' \
    --header 'data-partition-id: <data-partition-id>' \
    --header 'Authorization: Bearer <access_token>' \
    --header 'Content-Type: application/json' \
    --data-raw '{
                    "email": "<Object_ID>",
                    "role": "MEMBER"
                }'
```

**Sample request for `users` OSDU group**

Consider an Azure Data Manager for Energy instance named "medstest" with a data partition named "dp1"

```bash
    curl --location --request POST 'https://medstest.energy.azure.com/api/entitlements/v2/groups/users@medstest-dp1.dataservices.energy/members' \
    --header 'data-partition-id: medstest-dp1' \
    --header 'Authorization: Bearer abcdefgh123456.............' \
    --header 'Content-Type: application/json' \
    --data-raw '{
                    "email": "90e0d063-2f8e-4244-860a-XXXXXXXXXX",
                    "role": "MEMBER"
                }'
```

**Sample Response**

```JSON
    {
        "email": "90e0d063-2f8e-4244-860a-XXXXXXXXXX",
        "role": "MEMBER"
    }
```
**Sample request for `legal service editor` OSDU group**
```bash
    curl --location --request POST 'https://medstest.energy.azure.com/api/entitlements/v2/groups/service.legal.editor@medstest-dp1.dataservices.energy/members' \
    --header 'data-partition-id: medstest-dp1' \
    --header 'Authorization: Bearer abcdefgh123456.............' \
    --header 'Content-Type: application/json' \
    --data-raw '{
                    "email": "90e0d063-2f8e-4244-860a-XXXXXXXXXX",
                    "role": "MEMBER"
                }'
```

> [!IMPORTANT]
> The app-id is the default OWNER of all the groups.
:::image type="content" source="media/how-to-manage-users/appid.png" alt-text="Screenshot of app-d in Microsoft Entra ID.":::

## Get OSDU groups for a given user in a data partition

1. Run the below curl command in Azure Cloud Bash to get all the groups associated with the user.

```bash
    curl --location --request GET 'https://<URI>/api/entitlements/v2/members/<OBJECT_ID>/groups?type=none' \
    --header 'data-partition-id: <data-partition-id>' \
    --header 'Authorization: Bearer <access_token>'
```

**Sample request**

Consider an Azure Data Manager for Energy instance named "medstest" with a data partition named "dp1"

```bash
    curl --location --request GET 'https://medstest.energy.azure.com/api/entitlements/v2/members/90e0d063-2f8e-4244-860a-XXXXXXXXXX/groups?type=none' \
    --header 'data-partition-id: medstest-dp1' \
    --header 'Authorization: Bearer abcdefgh123456.............'
```
**Sample response**

```JSON
    {
    "desId": "90e0d063-2f8e-4244-860a-XXXXXXXXXX",
    "memberEmail": "90e0d063-2f8e-4244-860a-XXXXXXXXXX",
    "groups": [
        {
        "name": "users",
        "description": "Datalake users",
        "email": "users@medstest-dp1.dataservices.energy"
        },
        {
        "name": "service.search.user",
        "description": "Datalake Search users",
        "email": "service.search.user@medstest-dp1.dataservices.energy"
        }
    ]
    }
```

## Delete OSDU groups of a given user in a data partition

1. Run the below curl command in Azure Cloud Bash to delete a given user from a given data partition.
2. **DO NOT** delete the OWNER of a group unless you have another OWNER who can manage users in that group.

```bash
    curl --location --request DELETE 'https://<URI>/api/entitlements/v2/members/<OBJECT_ID>' \
    --header 'data-partition-id: <data-partition-id>' \
    --header 'Authorization: Bearer <access_token>'
```

**Sample request**

Consider an Azure Data Manager for Energy instance named "medstest" with a data partition named "dp1"

```bash
    curl --location --request DELETE 'https://medstest.energy.azure.com/api/entitlements/v2/members/90e0d063-2f8e-4244-860a-XXXXXXXXXX' \
    --header 'data-partition-id: medstest-dp1' \
    --header 'Authorization: Bearer abcdefgh123456.............'
```

**Sample response**
No output for a successful response



## Next steps
After you have added users to the groups, you can do the following:
- [How to manage legal tags](how-to-manage-legal-tags.md)
- [How to manage ACLs](how-to-manage-acls.md)

You can also ingest data into your Azure Data Manager for Energy instance with
- [Tutorial on CSV parser ingestion](tutorial-csv-ingestion.md)
- [Tutorial on manifest ingestion](tutorial-manifest-ingestion.md)
