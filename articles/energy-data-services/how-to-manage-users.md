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
2. Get various parameters of your instance such as client-id, client-secret, etc. using the tutorial at [How to generate auth token](how-to-generate-auth-token.md).
3. Generate the service principal access token needed to call the Entitlements APIs using the tutorial at [How to generate auth token](how-to-generate-auth-token.md).
4. Keep all these parameter values handy as they are needed for executing different user management requests via the Entitlements API. 


## Fetch OID
`object-id` (OID) is the Microsoft Entra user Object ID.

1. Find the 'object-id' (OID) of the user(s) first. If you are managing an application's access, you must find and use the application ID (or client ID) instead of the OID.
2. Input the `object-id` (OID) of the users (or the application or client ID if managing access for an application) as parameters in the calls to the Entitlements API of your Azure Data Manager for Energy instance. 

:::image type="content" source="media/how-to-manage-users/azure-active-directory-object-id.png" alt-text="Screenshot of finding the object-id from Microsoft Entra ID.":::

:::image type="content" source="media/how-to-manage-users/profile-object-id.png" alt-text="Screenshot of finding the object-id from the profile.":::

## First time addition of users in a new data partition
1. In order to add first admin to a new data partition of Azure Data Manager for Energy instance, use the access token of the `client-id` that was used to provision the instance.
2. Get the `client-id` access token using [Generate client-id access token](how-to-generate-auth-token.md#generate-client-id-auth-token).
3. If you try to directly use your own access token for adding entitlements, it results in 401 error. The client-id access token must be used to add first set of users in the system and those users (with admin access) can then manage more users with their own access token.
4. Use the client-id access token to do these three steps using the commands outlined in the following sections:
   1. Add the user to the `users@<data-partition-id>.<domain>` OSDU group.
   2. Add the user to the `users.datalake.ops@<data-partition-id>.<domain>` OSDU group.
5. The user becomes the admin of the data partition. The admin can then add or remove more users to the required entitlement groups:
   1. Get admin's auth token using [Generate user access token](how-to-generate-auth-token.md#generate-user-auth-token) using the same client-id and client-secret.
   2. Get the OSDU group such as `service.legal.editor@<data-partition-id>.<domain>` you want to add more users to using the admin's access token.
   3. Add more users to that OSDU group using the admin's access token.

## Get the list of all available groups in a data partition

Run the below curl command in Azure Cloud Bash to get all the groups that are available for you or you have access to in the given data partition of Azure Data Manager for the Energy instance.

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
