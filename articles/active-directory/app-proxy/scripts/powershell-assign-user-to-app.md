---
title: PowerShell sample - Assign user to a Microsoft Entra application proxy app
description: PowerShell example that assigns a user to a Microsoft Entra application proxy application.
services: active-directory
author: kenwith
manager: amycolannino
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.custom: has-azure-ad-ps-ref
ms.topic: sample
ms.date: 08/29/2022
ms.author: kenwith
ms.reviewer: ashishj
---

# Assign a user to a specific Microsoft Entra application proxy application

This PowerShell script example allows you to assign a user to a specific Microsoft Entra application proxy application.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

This sample requires the [Microsoft Entra V2 PowerShell for Graph module](/powershell/azure/active-directory/install-adv2) (Microsoft Entra ID) or the [Microsoft Entra V2 PowerShell for Graph module preview version](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## Sample script

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-user-to-app.ps1 "Assign a user to an application")]

## Script explanation

| Command | Notes |
|---|---|
| [New-AzureADUserAppRoleAssignment](/powershell/module/AzureAD/new-azureaduserapproleassignment) | Assigns a user to an application role. |

## Next steps

For more information on the Azure AD PowerShell module, see [Azure AD PowerShell module overview](/powershell/azure/active-directory/overview).

For other PowerShell examples for Application Proxy, see [Azure AD PowerShell examples for Microsoft Entra application proxy](../application-proxy-powershell-samples.md).
