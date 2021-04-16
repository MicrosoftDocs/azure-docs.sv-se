---
title: Lista grupper Programproxy Azure AD-anslutningsprogram för appar
description: PowerShell-exempel som visar alla Azure Active Directory (Azure AD) Programproxy Connector-grupper med de tilldelade programmen.
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
ms.openlocfilehash: 1adb576e751d93dc4a9d240546325d7e671d0892
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377059"
---
# <a name="get-all-application-proxy-apps-and-list-by-connector-group"></a>Hämta alla Programproxy och lista efter anslutningsgrupp

Det här PowerShell-skriptexempel visar information om alla Azure Active Directory (Azure AD) Programproxy Connector-grupper med de tilldelade programmen.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Det här exemplet kräver [AzureAD V2 PowerShell för Graph-modulen](/powershell/azure/active-directory/install-adv2) (AzureAD) eller Förhandsversionen av [AzureAD V2 PowerShell för Graph-modulen](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-by-connectorgroup.ps1 "Get all Application Proxy Connector groups with the assigned applications")]

## <a name="script-explanation"></a>Förklaring av skript

| Kommando | Kommentarer |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Hämtar ett huvudnamn för tjänsten. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Hämtar ett Azure AD-program. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Hämtar ett program som konfigurerats för Programproxy i Azure AD. |
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | Hämtar en lista över alla anslutningsgrupper, eller, om det har angetts, information om den angivna anslutningsgruppen. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD PowerShell-modulen finns i [Översikt över Azure AD PowerShell-modulen.](/powershell/azure/active-directory/overview)

Andra PowerShell-exempel för Programproxy finns i [Azure AD PowerShell-exempel för Azure AD Programproxy](../application-proxy-powershell-samples.md).
