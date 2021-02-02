---
title: PowerShell-exempel – tilldela grupp till en Application Proxy-app
description: PowerShell-exempel som tilldelar en grupp till ett Azure Active Directory (Azure AD) Application Proxy-program.
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
ms.openlocfilehash: b071bd29c3e66b2d2e504936b3653d15a22ca6f2
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258481"
---
# <a name="assign-a-group-to-a-specific-azure-ad-application-proxy-application"></a>Tilldela en grupp till ett enskilt Azure AD-programproxy-program

Med det här PowerShell-skript exemplet kan du tilldela en specifik grupp till ett Azure Active Directory (Azure AD) Application Proxy-program.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Det här exemplet kräver [AzureAD v2 PowerShell för Graph module](/powershell/azure/active-directory/install-adv2) (AzureAD) eller [AzureAD v2 PowerShell för för hands versionen](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) av (AzureADPreview).

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-group-to-app.ps1 "Assign a group to a specific Azure AD Application Proxy application")]

## <a name="script-explanation"></a>Förklaring av skript

| Kommando | Kommentarer |
|---|---|
| [New-AzureADGroupAppRoleAssignment](/powershell/module/AzureAD/New-azureadgroupapproleassignment) | Tilldelar en program roll en grupp. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD PowerShell-modulen finns i [Översikt över Azure AD PowerShell-modulen](/powershell/azure/active-directory/overview).

Andra PowerShell-exempel för Application Proxy finns i [Azure AD PowerShell-exempel för azure AD-programproxy](../application-proxy-powershell-samples.md).
