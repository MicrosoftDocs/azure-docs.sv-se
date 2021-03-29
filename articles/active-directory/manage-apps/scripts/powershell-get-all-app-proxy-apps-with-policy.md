---
title: PowerShell-exempel – Visa en lista över alla Application Proxy-appar med en princip
description: PowerShell-exempel som visar alla Azure Active Directory (Azure AD) Application Proxy-program i din katalog som har en princip för livs längds-token.
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
ms.openlocfilehash: c2da1885d975ea6b4daf26714e430143dd89da42
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709777"
---
# <a name="get-all-application-proxy-apps-with-a-token-lifetime-policy"></a>Hämta alla Application Proxy-appar med en livs längd princip för token

Detta exempel på PowerShell-skript visar alla Azure Active Directory (Azure AD) Application Proxy-program i din katalog som har en princip för token-livstid och visar information om principen.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Det här exemplet kräver [AzureAD v2 PowerShell för AzureADPreview (för hands version av Graph module](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) ).

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-with-policy.ps1 "Get all Application Proxy apps with a token lifetime policy")]

## <a name="script-explanation"></a>Förklaring av skript

| Kommando | Kommentarer |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Hämtar ett huvud namn för tjänsten. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Hämtar ett Azure AD-program. |
|[Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hämtar en princip i Azure AD. |
|[Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hämtar principen för ett huvud namn för tjänsten i Azure AD. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD PowerShell-modulen finns i [Översikt över Azure AD PowerShell-modulen](/powershell/azure/active-directory/overview).

Andra PowerShell-exempel för Application Proxy finns i [Azure AD PowerShell-exempel för azure AD-programproxy](../application-proxy-powershell-samples.md).
