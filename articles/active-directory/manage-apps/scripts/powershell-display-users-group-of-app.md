---
title: PowerShell-exempel – lista användare & grupper för Application Proxy-appen
description: PowerShell-exempel som visar alla användare och grupper som har tilldelats till ett specifik Azure Active Directory (Azure AD) Application Proxy-program.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 540df7cbe2d35cd705e9d2b88f66a82604951957
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548554"
---
# <a name="display-users-and-groups-assigned-to-an-application-proxy-application"></a>Visa användare och grupper som har tilldelats till ett program för programproxy

Det här PowerShell-skript exemplet visar en lista över användare och grupper som har tilldelats till ett specifik Azure Active Directory (Azure AD) Application Proxy-program.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Det här exemplet kräver [AzureAD v2 PowerShell för Graph module](/powershell/azure/active-directory/install-adv2) (AzureAD) eller [AzureAD v2 PowerShell för för hands versionen](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) av (AzureADPreview).

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/display-users-group-of-an-app.ps1 "Display users and groups assigned to an Application Proxy application")]

## <a name="script-explanation"></a>Förklaring av skript

| Kommando | Kommentarer |
|---|---|
| [Get-AzureADUser](/powershell/module/AzureAD/get-azureaduser)| Hämtar en användare. |
| [Get-AzureADGroup](/powershell/module/AzureAD/get-azureadgroup)| Hämtar en grupp. |
| [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Hämtar ett huvud namn för tjänsten. |
| [Get-AzureADUserAppRoleAssignment](/powershell/module/AzureAD/get-azureaduserapproleassignment) | Hämta en roll tilldelning för användar program. |
| [Get-AzureADGroupAppRoleAssignment](/powershell/module/AzureAD/get-azureadgroupapproleassignment) | Hämta en grupp program roll tilldelning. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD PowerShell-modulen finns i [Översikt över Azure AD PowerShell-modulen](/powershell/azure/active-directory/overview).

Andra PowerShell-exempel för Application Proxy finns i [Azure AD PowerShell-exempel för azure AD-programproxy](../application-proxy-powershell-samples.md).
