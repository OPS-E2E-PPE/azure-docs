---
title: Microsoft Sentinel Authentication normalization schema reference | Microsoft Docs
description: This article describes the Microsoft Sentinel Authentication normalization schema.
author: batamig
ms.topic: reference
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
---

# Microsoft Sentinel Authentication normalization schema reference (Public preview)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

The Microsoft Sentinel Authentication schema is used to describe events related to user authentication, sign-in, and sign-out. Authentication events are sent by many reporting devices, usually as part of the event stream alongside other events. For example, Windows sends several authentication events alongside other OS activity events.

Authentication events include both events from systems that focus on authentication such as VPN gateways or domain controllers, and direct authentication to an end system, such as a computer or firewall.

For more information about normalization in Microsoft Sentinel, see [Normalization and the Advanced SIEM Information Model (ASIM)](normalization.md).

> [!IMPORTANT]
> The Authentication normalization schema is currently in PREVIEW. This feature is provided without a service level agreement, and is not recommended for production workloads.
>
> The [Azure Preview Supplemental Terms](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) include additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.
>

## Parsers

Deploy ASIM parsers from the [Microsoft Sentinel GitHub repository](https://aka.ms/DeployASIM). For more information about ASIM parsers, see the articles [ASIM parsers overview](normalization-parsers-overview.md) and [Use ASIM parsers](normalization-about-parsers.md).

### Unifying parsers

To use parsers that unify all ASIM out-of-the-box parsers, and ensure that your analysis runs across all the configured sources, use the `ImAuthentication` filtering parser or the `ASimAuthentication` parameter-less parser.

## Source-specific parsers

Microsoft Sentinel provides the following built-in, product-specific authentication event parsers: 

- **Windows sign-ins**
    - Collected using the Log Analytics Agent or Azure Monitor Agent.
    - Collected using either the Security Events connectors to the SecurityEvent table or using the WEF connector to the WindowsEvent table.
    - Reported as Security Events (4624, 4625, 4634, and 4647).
- **Windows sign-ins** reported by Microsoft 365 Defender for Endpoint, collected using the Microsoft 365 Defender connector.
- **Linux Sign-ins** reported by Microsoft Defender to IoT Endpoint.
- **Azure Active Directory sign-ins**, collected using the Azure Active Directory connector. Separate parsers are provided for regular, Non-Interactive, Managed Identities and Service Principles Sign-ins.
- **AWS sign-ins**, collected using the AWS CloudTrail connector.
- **Okta authentication**, collected using the Okta connector.

### Add your own normalized parsers

When implementing custom parsers for the Authentication information model, name your KQL functions using the following syntax:

- `vimAuthentication<vendor><Product>` for filtering parsers
- `ASiAuthentication<vendor><Product>` for parameter-less parsers

For information on adding the your custom parsers to the unifying parser, refer to [Managing ASIM parsers](normalization-manage-parsers.md).

### Filtering parser parameters

The `im` and `vim*` parsers support [filtering parameters](normalization-about-parsers.md#optimized-parsers). While these parsers are optional, they can improve your query performance.

The following filtering parameters are available:

| Name     | Type      | Description |
|----------|-----------|-------------|
| **starttime** | datetime | Filter only DNS queries that ran at or after this time. |
| **endtime** | datetime | Filter only DNS queries that finished running at or before this time. |
| **targetusername_has** | string | Filter only authentication events that has any of the listed user names. |
| | | |

For example, to filter only DNS queries from the last day to a specific user, use:

```kql
imAuthentication (targetusername_has = 'johndoe', starttime = ago(1d), endtime=now())
```


## Normalized content

Normalized authentication analytic rules are unique as they detect attacks across sources. So, for example, if a user logged in to different, unrelated systems, from different countries, Microsoft Sentinel will now detect this threat.

For a full list of analytics rules that use normalized Authentication events, see [Authentication schema security content](normalization-content.md#authentication-security-content).

## Schema overview

The Authentication information model is aligned with the [OSSEM logon entity schema](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/logon.md).

The fields listed in the table below are specific to Authentication events, but are similar to fields in other schemas and follow similar naming conventions.

Authentication events reference the following entities:

- **TargetUser** - The user information used to authenticate to the system. The **TargetSystem** is the primary subject of the authentication event, and the alias User aliases a **TargetUser** identified.
- **TargetApp** - The application authenticated to.
- **Target** - The system on which **TaregtApp*** is running.
- **Actor** - The user initiating the authentication, if different than **TargetUser**.
- **ActingApp** - The application used by the **Actor** to perform the authentication.
- **Src** - The system used by the **Actor** to initiate the authentication.

The relationship between these entities is best demonstrated as follows:

An **Actor**, running an acting Application, **ActingApp**, on a source system, **Src**, attempts to authenticate as a **TargetUser** to a target application, **TargetApp**, on a target system, **TargetDvc**.

## Schema details

In the following tables, *Type* refers to a logical type. For more information, see [Logical types](normalization-about-schemas.md#logical-types).

### Common fields

> [!IMPORTANT]
> Fields common to all schemas are described in the [ASIM schema overview](normalization-about-schemas.md#common). The following list mentions only fields that have specific guidelines for user management events.
>

| Field               | Class       | Type       |  Description        |
|---------------------|-------------|------------|--------------------|
| **EventType**           | Mandatory   | Enumerated |    Describes the operation reported by the record. <br><br>For Authentication records, supported values include: <br>- `Logon` <br>- `Logoff`|
| <a name ="eventresultdetails"></a>**EventResultDetails**         | Optional   | String |  One of the following values: <br><br>-	`No such user or password`. This value should be used also when the original event reports that there is no such user, without reference to a password. <br>-	`Incorrect password`<br>-	`Account expired`<br>-	`Password expired`<br>-	`User locked`<br>-	`User disabled`<br>-	`Logon violates policy`. This value should be used when the original event reports, for example: MFA required, logon outside of working hours, conditional access restrictions, or too frequent attempts.<br>-	`Session expired`<br>-	`Other`<br><br>**Note**: The value may be provided in the source record using different terms, which should be normalized to these values. The original value should be stored in the field [EventOriginalResultDetails](normalization-about-schemas.md#eventoriginalresultdetails)|
| **EventSubType**    | Optional    | String     |   The sign-in type. Allowed values include: `System`, `Interactive`, `Service`, `RemoteInteractive`, `RemoteService`, `AssumeRole`. <br><br>Example: `Interactive`. Store the original value in [EventOriginalSubType](normalization-about-schemas.md#eventoriginalsubtype). |
| **EventSchemaVersion**  | Mandatory   | String     |    The version of the schema. The version of the schema documented here is `0.1.1`         |
| **EventSchema** | Mandatory | String | The name of the schema documented here is **Authentication**. |
| **Dvc** fields| -      | -    | For authentication events, device fields refer to the system reporting the event. |
| | | | |


### Authentication-specific fields

| Field          | Class        | Type       | Description   |
|---------------|--------------|------------|-----------------|
|**LogonMethod** |Optional |String |The method used to perform authentication. <br><br>Example: `Username & Password` |
|**LogonProtocol** |Optional |String |The protocol used to perform authentication. <br><br>Example: `NTLM` |
| | | | |

### Actor fields

| Field          | Class        | Type       | Description   |
|---------------|--------------|------------|-----------------|
| <a name="actoruserid"></a>**ActorUserId**    | Optional  | String     |   A machine-readable, alphanumeric, unique representation of the Actor. For more information, and for alternative fields for additional IDs, see [The User entity](normalization-about-schemas.md#the-user-entity).  <br><br>Example: `S-1-12-1-4141952679-1282074057-627758481-2916039507`    |
| **ActorUserIdType**| Optional  | UserIdType |  The type of the ID stored in the [ActorUserId](#actoruserid) field. For more information and list of allowed values, see [UserIdType](normalization-about-schemas.md#useridtype) in the [Schema Overview article](normalization-about-schemas.md).|
| <a name="actorusername"></a>**ActorUsername**  | Optional    | Username     | The Actor’s username, including domain information when available. For more information, see [The User entity](normalization-about-schemas.md#the-user-entity).<br><br>Example: `AlbertE`     |
| **ActorUsernameType**              | Optional    | UsernameType |   Specifies the type of the user name stored in the [ActorUsername](#actorusername) field. For more information, and list of allowed values, see [UsernameType](normalization-about-schemas.md#usernametype) in the [Schema Overview article](normalization-about-schemas.md). <br><br>Example: `Windows`       |
| **ActorUserType** | Optional | UserType | The type of the Actor. For more information, and  list of allowed values, see [UserType](normalization-about-schemas.md#usertype) in the [Schema Overview article](normalization-about-schemas.md).<br><br>For example: `Guest` |
| **ActorSessionId** | Optional     | String     |   The unique ID of the sign-in session of the Actor.  <br><br>Example: `102pTUgC3p8RIqHvzxLCHnFlg`  |
| | | | |

### Acting Application fields

| Field          | Class        | Type       | Description   |
|---------------|--------------|------------|-----------------|
| **ActingAppId** | Optional | String | The ID of the application authorizing on behalf of the actor, including a process, browser, or service. <br><br>For example: `0x12ae8` |
| **ActiveAppName** | Optional | String | The name of the application authorizing on behalf of the actor, including a process, browser, or service. <br><br>For example: `C:\Windows\System32\svchost.exe` |
| **ActingAppType** | Optional | AppType | The type of acting application. For more information, and allowed list of values, see [AppType](normalization-about-schemas.md#apptype) in the [Schema Overview article](normalization-about-schemas.md). |
| **HttpUserAgent** |	Optional	| String |	When authentication is performed over HTTP or HTTPS, this field's value is the user_agent HTTP header provided by the acting application when performing the authentication.<br><br>For example: `Mozilla/5.0 (iPhone; CPU iPhone OS 12_1 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/12.0 Mobile/15E148 Safari/604.1` |
| | | | |

### Target user fields

| Field          | Class        | Type       | Description   |
|---------------|--------------|------------|-----------------|
|<a name="targetuserid"></a> **TargetUserId**   | Optional | UserId     | A machine-readable, alphanumeric, unique representation of the target user. For more information, and for alternative fields for additional IDs, see [The User entity](normalization-about-schemas.md#the-user-entity).  <br><br> Example: `00urjk4znu3BcncfY0h7`    |
| **TargetUserIdType** | Optional | UserIdType | The type of the user ID stored in the [TargetUserId](#targetuserid) field. For more information and list of allowed values, see [UserIdType](normalization-about-schemas.md#useridtype) in the [Schema Overview article](normalization-about-schemas.md).            <br><br> Example:  `SID`  |
| <a name="targetusername"></a>**TargetUsername** | Optional | Username     | The target user username, including domain information when available. For more information, see [The User entity](normalization-about-schemas.md#the-user-entity).  <br><br>Example:   `MarieC`      |
| **TargetUsernameType**             |Optional  | UsernameType | Specifies the type of the username stored in the [TargetUsername](#targetusername) field. For more information and list of allowed values, see [UsernameType](normalization-about-schemas.md#usernametype) in the [Schema Overview article](normalization-about-schemas.md).          |
| **TargetUserType** | Optional | UserType | The type of the Target user. For more information, and  list of allowed values, see [UserType](normalization-about-schemas.md#usertype) in the [Schema Overview article](normalization-about-schemas.md). <br><br>For example: `Member` |
| **TargetSessionId** | Optional | String | The sign-in session identifier of the TargetUser on the source device. |
| **User**  | Alias  | Username | Alias to the [TargetUsername](#targetusername) or to the [TargetUserId](#targetuserid) if [TargetUsername](#targetusername) is not defined. <br><br>Example: `CONTOSO\dadmin`     |
| | | | |

### Source system fields

| Field          | Class        | Type       | Description   |
|---------------|--------------|------------|-----------------|
| <a name="src"></a>**Src** | Recommended       | String     |    A unique identifier of the source device. <br><br>This field may alias the [SrcDvcId](#srcdvcid), [SrcHostname](#srchostname), or [SrcIpAddr](#srcipaddr) fields. <br><br>Example: `192.168.12.1` |
| <a name="srcdvcid"></a>**SrcDvcId** | Optional | String | The ID of the source device as reported in the record. <br><br>For example: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **SrcDvcIdType** | Optional | DvcIdType | The type of [SrcDvcId](#srcdvcid). For more information, and  list of allowed values, see [DvcIdType](normalization-about-schemas.md#dvcidtype) in the [Schema Overview article](normalization-about-schemas.md).<br><br>**Note**: This field is required if [SrcDvcId](#srcdvcid) is used. |
| **SrcDeviceType** | Optional | DeviceType | The type of the source device. For more information, and  list of allowed values, see [The Device entity](normalization-about-schemas.md#the-device-entity). |
| <a name="srchostname"></a> **SrcHostname** | Recommended | Hostname | The source device hostname, excluding domain information. If no device name is available, store the relevant IP address in this field. <br><br>Example: `DESKTOP-1282V4D` |
|<a name="srcdomain"></a> **SrcDomain** | Recommended | String | The domain of the source device.<br><br>Example: `Contoso` |
| <a name="srcdomaintype"></a>**SrcDomainType** | Recommended | DomainType | The type of [SrcDomain](#srcdomain). For a list of allowed values and further information refer to [DomainType](normalization-about-schemas.md#domaintype) in the [Schema Overview article](normalization-about-schemas.md).<br><br>Required if [SrcDomain](#srcdomain) is used. |
| **SrcFQDN** | Optional | String | The source device hostname, including domain information when available. <br><br>**Note**: This field supports both traditional FQDN format and Windows domain\hostname format. The [SrcDomainType](#srcdomaintype) field reflects the format used. <br><br>Example: `Contoso\DESKTOP-1282V4D` |
| <a name="srcdvcid"></a>**SrcDvcId** | Optional | String |  The ID of the source device. If multiple IDs are available, use the most important one, and store the others in the fields `SrcDvc<DvcIdType>`.<br><br>Example: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **SrcDvcIdType** | Optional | DvcIdType | The type of [SrcDvcId](#srcdvcid). For a list of allowed values and further information refer to [DvcIdType](normalization-about-schemas.md#dvcidtype) in the [Schema Overview article](normalization-about-schemas.md). <br><br>**Note**: This field is required if [SrcDvcId](#srcdvcid) is used. |
| **SrcDeviceType** | Optional | DeviceType | The type of the source device. For a list of allowed values and further information refer to [DeviceType](normalization-about-schemas.md#devicetype) in the [Schema Overview article](normalization-about-schemas.md). |
|<a name="srcipaddr"></a>**SrcIpAddr** |Optional | IP Address|The IP address of the source device. <br><br>Example: `2.2.2.2` |
| **SrcDvcOs**|Optional |String |The OS of the source device. <br><br>Example: `Windows 10` |
| **IpAddr** | Alias | | Alias to [SrcIpAddr](#srcipaddr) |
|**SrcIsp** | Optional|String |The Internet Service Provider (ISP) used by the source device to connect to the internet. <br><br>Example: `corpconnect` |
| **SrcGeoCountry**|Optional |Country |Example: `Canada` <br><br>For more information, see [Logical types](normalization-about-schemas.md#logical-types). |
| **SrcGeoCity**|Optional |City |Example: `Montreal` <br><br>For more information, see [Logical types](normalization-about-schemas.md#logical-types). |
|**SrcGeoRegion** | Optional|Region | Example: `Quebec` <br><br>For more information, see [Logical types](normalization-about-schemas.md#logical-types).|
| **SrcGeoLongtitude**|Optional |Longitude  | Example: `-73.614830` <br><br>For more information, see [Logical types](normalization-about-schemas.md#logical-types).|
| **SrcGeoLatitude**|Optional |Latitude |Example: `45.505918` <br><br>For more information, see [Logical types](normalization-about-schemas.md#logical-types). |
| | | | |


### Target system fields

| Field          | Class        | Type       | Description   |
|---------------|--------------|------------|-----------------|
| <a name="dst"></a>**Dst** | Recommended       | String     |    A unique identifier of the authetication target. <br><br>This field may alias the [SrcDvcId](#targetdvcid), [SrcHostname](#targethostname), [TargetIpAddr](#targetipaddr), [TargetAppId](#targetappid), or [TargetAppName](#targetappname) fields. <br><br>Example: `192.168.12.1` |
| <a name="targetappid"></a>**TargetAppId** |Optional | String| The ID of the application to which the authorization is required, often assigned by the reporting device. <br><br>Example: `89162` |
|<a name="targetappname"></a>**TargetAppName** |Optional |String |The name of the application to which the authorization is required, including a service, a URL, or a SaaS application. <br><br>Example: `Saleforce` |
| **TargetAppType**|Optional |AppType |The type of the application authorizing on behalf of the Actor. For more information, and allowed list of values, see [AppType](normalization-about-schemas.md#apptype) in the [Schema Overview article](normalization-about-schemas.md).|
| <a name="targeturl"></a>**TargetUrl** |Optional |URL |The URL associated with the target application. <br><br>Example: `https://console.aws.amazon.com/console/home?fromtb=true&hashArgs=%23&isauthcode=true&nc2=h_ct&src=header-signin&state=hashArgsFromTB_us-east-1_7596bc16c83d260b` |
|**LogonTarget**| Alias| |Alias to either [TargetAppName](#targetappname), [TargetUrl](#targeturl), or [TargetHostname](#targethostname), whichever field best describes the authentication target. |
| <a name="targethostname"></a>**TargetHostname** | Recommended | Hostname | The target device hostname, excluding domain information.<br><br>Example: `DESKTOP-1282V4D` |
| <a name="targetdomain"></a>**TargetDomain** | Recommended | String | The domain of the target device.<br><br>Example: `Contoso` |
| <a name="targetdomaintype"></a>**TargetDomainType** | Recommended | Enumerated | The type of [TargetDomain](#targetdomain). For a list of allowed values and further information refer to [DomainType](normalization-about-schemas.md#domaintype) in the [Schema Overview article](normalization-about-schemas.md).<br><br>Required if [TargetDomain](#targetdomain) is used. |
| **TargetFQDN** | Optional | String | The target device hostname, including domain information when available. <br><br>Example: `Contoso\DESKTOP-1282V4D` <br><br>**Note**: This field supports both traditional FQDN format and Windows domain\hostname format. The [TargetDomainType](#targetdomaintype) reflects the format used.   |
| <a name="targetdvcid"></a>**TargetDvcId** | Optional | String | The ID of the target device. If multiple IDs are available, use the most important one, and store the others in the fields `TargetDvc<DvcIdType>`. <br><br>Example: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **TargetDvcIdType** | Optional | Enumerated | The type of [TargetDvcId](#targetdvcid). For a list of allowed values and further information refer to [DvcIdType](normalization-about-schemas.md#dvcidtype) in the [Schema Overview article](normalization-about-schemas.md). <br><br>Required if **TargetDeviceId** is used.|
| **TargetDeviceType** | Optional | Enumerated | The type of the target device.  For a list of allowed values and further information refer to [DeviceType](normalization-about-schemas.md#devicetype) in the [Schema Overview article](normalization-about-schemas.md). |
|<a name="targetipaddr"></a>**TargetIpAddr** |Optional | IP Address|The IP address of the target device. <br><br>Example: `2.2.2.2` |
| **TargetDvcOs**| Optional| String| The OS of the target device. <br><br>Example: `Windows 10`|
| **TargetPortNumber** |Optional |Integer |The port of the target device.|
| | | | |

### Schema updates

These are the changes in version 0.1.1 of the schema:
- Updated user and device entity fields to align with other schemas.
- Renamed `TargetDvc` and `SrcDvc` to `Target` and `Src` respectively to align with current ASIM guidelines. The renamed fields will be implemented as aliases until July 1st 2022. Those fields include: `SrcDvcHostname`, `SrcDvcHostnameType`, `SrcDvcType`, `SrcDvcIpAddr`, `TargetDvcHostname`, `TargetDvcHostnameType`, `TargetDvcType`, `TargetDvcIpAddr`, and `TargetDvc`.
- Added the aliases `Src` and `Dst`.
- Added the fields `SrcDvcIdType`, `SrcDeviceType`, `TargetDvcIdType`, and `TargetDeviceType`.
- Added the field `EventSchema` - currently optional, but will become mandatory on July 1st, 2022.

## Next steps

For more information, see:

- Watch the [ASIM Webinar](https://www.youtube.com/watch?v=WoGD-JeC7ng) or review the [slides](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG)
- [Advanced SIEM Information Model (ASIM) overview](normalization.md)
- [Advanced SIEM Information Model (ASIM) schemas](normalization-about-schemas.md)
- [Advanced SIEM Information Model (ASIM) parsers](normalization-parsers-overview.md)
- [Advanced SIEM Information Model (ASIM) content](normalization-content.md)
