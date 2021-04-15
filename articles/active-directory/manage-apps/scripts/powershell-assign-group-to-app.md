---
title: PowerShell-exempel – Tilldela grupp till en Programproxy app
description: PowerShell-exempel som tilldelar en grupp till ett Azure Active Directory (Azure AD) Programproxy program.
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 57c0319efe9353355dc59d9380860569ccbb18cb
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375635"
---
# <a name="assign-a-group-to-a-specific-azure-ad-application-proxy-application"></a>Tilldela en grupp till ett specifikt Azure AD Programproxy program

Med det här PowerShell-skriptexempel kan du tilldela en specifik grupp till en Azure Active Directory (Azure AD) Programproxy program.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Det här exemplet kräver [AzureAD V2 PowerShell för Graph-modulen](/powershell/azure/active-directory/install-adv2) (AzureAD) eller förhandsversionen av [AzureAD V2 PowerShell för Graph-modulen](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-group-to-app.ps1 "Assign a group to a specific Azure AD Application Proxy application")]

## <a name="script-explanation"></a>Förklaring av skript

| Kommando | Kommentarer |
|---|---|
| [New-AzureADGroupAppRoleAssignment](/powershell/module/AzureAD/New-azureadgroupapproleassignment) | Tilldelar en grupp till en programroll. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD PowerShell-modulen finns i Översikt över [Azure AD PowerShell-modulen](/powershell/azure/active-directory/overview).

Andra PowerShell-exempel för Programproxy finns i [Azure AD PowerShell-exempel för Azure AD Programproxy](../application-proxy-powershell-samples.md).
