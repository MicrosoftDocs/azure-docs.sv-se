---
title: PowerShell-exempel – Visa utökad information för Programproxy appar
description: PowerShell-exempel som visar en lista över alla Azure Active Directory-program (Azure AD) Programproxy tillsammans med program-ID(AppId), namn (DisplayName), extern URL (ExternalUrl), intern URL (InternalUrl) och autentiseringstyp (ExternalAuthenticationType).
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
ms.openlocfilehash: 1b3234a13a0e3fac760a899ce66e7faa7e7b642c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377369"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>Hämta alla Programproxy appar och visa utökad information

Det här PowerShell-skriptexempel visar information om alla Azure Active Directory-program (Azure AD) Programproxy, inklusive program-ID (AppId), namn (DisplayName), extern URL (ExternalUrl), intern URL (InternalUrl), autentiseringstyp (ExternalAuthenticationType), SSO-läge och ytterligare inställningar.

Genom att ändra värdet för $ssoMode variabeln aktiveras filtrerade utdata efter SSO-läge. Mer information finns dokumenterad i skriptet.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Det här exemplet kräver [AzureAD V2 PowerShell för Graph-modulen](/powershell/azure/active-directory/install-adv2) (AzureAD).

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Förklaring av skript

| Kommando | Kommentarer |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Hämtar ett huvudnamn för tjänsten. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Hämtar ett Azure AD-program. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Hämtar ett program som konfigurerats för Programproxy i Azure AD. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD PowerShell-modulen finns i Översikt [över Azure AD PowerShell-modulen.](/powershell/azure/active-directory/overview)

Andra PowerShell-exempel för Programproxy finns i [Azure AD PowerShell-exempel för Azure AD Programproxy](../application-proxy-powershell-samples.md).
