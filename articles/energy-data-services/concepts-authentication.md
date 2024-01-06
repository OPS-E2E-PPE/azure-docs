---
title: Authentication concepts in Microsoft Azure Data Manager for Energy 
description:  This article describes the various concepts regarding the authentication in Azure Data Manager for Energy.
author: shikhagarg1
ms.author: shikhagarg
ms.service: energy-data-services
ms.topic: conceptual
ms.date: 02/10/2023
ms.custom: template-concept
---

# Authentication concepts in Azure Data Manager for Energy 
Authentication confirms the identity of the users. The access flows can be user triggered, system triggered, or system API communication. In this article, you learn about service principals and authorization token.

## Service principals
In the Azure Data Manager for Energy instance, 
1. No Service Principals are created. 
2. The app-id is used for API access. The same app-id is used to provision ADME instance.
3. The app-id doesn't have access to infrastructure resources. 
4. The app-id also gets added as OWNER to all OSDU groups by default. 
5. For service-to-service (S2S) communication, ADME uses MSI (Microsoft Service Identity).

In the OSDU instance, 
1. Terraform scripts create two Service Principals: 
   1. The first Service Principal is used for API access. It can also manage infrastructure resources. 
   2. The second Service Principal is used for service-to-service (S2S) communications. 

## Generate authorization token
You can generate the authorization token using the steps outlined in [Generate auth token](how-to-generate-auth-token.md). 
