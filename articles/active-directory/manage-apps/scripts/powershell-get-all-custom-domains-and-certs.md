---
title: PowerShell-exempel – Programproxy appar med anpassade domäner
description: PowerShell-exempel som visar alla Azure Active Directory (Azure AD) Programproxy program som använder anpassade domäner och certifikatinformation.
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
ms.openlocfilehash: ec424d59fe7fee59c226ad51c74e6c106a4a5fc7
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377640"
---
# <a name="get-all-application-proxy-apps-using-custom-domains-and-certificate-information"></a>Hämta alla Programproxy appar med hjälp av anpassade domäner och certifikatinformation

Det här PowerShell-skriptexempel visar alla Azure Active Directory (Azure AD) Programproxy som använder anpassade domäner och visar certifikatinformationen som är associerad med de anpassade domänerna.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Det här exemplet kräver [AzureAD V2 PowerShell för Graph-modulen](/powershell/azure/active-directory/install-adv2) (AzureAD) eller förhandsversionen av [AzureAD V2 PowerShell för Graph-modulen](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-custom-domains-and-certs.ps1 "Get all Application Proxy apps using custom domains and certificate information")]

## <a name="script-explanation"></a>Förklaring av skript

| Kommando | Kommentarer |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Hämtar ett huvudnamn för tjänsten. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Hämtar ett Azure AD-program. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Hämtar ett program som konfigurerats för Programproxy i Azure AD. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD PowerShell-modulen finns i Översikt över [Azure AD PowerShell-modulen](/powershell/azure/active-directory/overview).

Andra PowerShell-exempel för Programproxy finns i [Azure AD PowerShell-exempel för Azure AD Programproxy](../application-proxy-powershell-samples.md).
