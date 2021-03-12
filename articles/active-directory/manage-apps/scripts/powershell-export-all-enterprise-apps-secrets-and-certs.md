---
title: PowerShell-exempel – exportera hemligheter och certifikat för företags program i Azure Active Directory klient organisationen.
description: PowerShell-exempel som exporterar alla hemligheter och certifikat för de angivna företags apparna i din Azure Active Directory klient.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: 20caefe74a7c047fb8690bb1d9e6f4eb9da7e9b7
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "102635203"
---
# <a name="export-secrets-and-certificates-for-enterprise-apps"></a>Exportera hemligheter och certifikat för företags program
Detta exempel på PowerShell-skript exporterar alla hemligheter, certifikat och ägare för de angivna företags apparna från din katalog till en CSV-fil.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Det här exemplet kräver [AzureAD v2 PowerShell för Graph module](/powershell/azure/active-directory/install-adv2) (AzureAD) eller [AzureAD v2 PowerShell för för hands versionen](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) av (AzureADPreview).

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-enterprise-apps-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified enterprise apps in your directory.")]

## <a name="script-explanation"></a>Förklaring av skript

Kommandot "Lägg till medlem" ansvarar för att skapa kolumner i CSV-filen.
Du kan ändra variabeln "$Path" direkt i PowerShell med en CSV-fil Sök väg, om du hellre vill att exporten ska vara icke-interaktiv.

| Kommando | Kommentarer |
|---|---|
| [Get-AzureADServicePrincipal](/powershell/module/azuread/Get-azureADServicePrincipal?view=azureadps-2.0&preserve-view=true) | Hämtar ett företags program från din katalog. |
| [Get-AzureADServicePrincipalOwner](/powershell/module/azuread/Get-AzureADServicePrincipalOwner?view=azureadps-2.0&preserve-view=true) | Hämtar ägare av ett företags program från din katalog. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD PowerShell-modulen finns i [Översikt över Azure AD PowerShell-modulen](/powershell/azure/active-directory/overview).

Andra PowerShell-exempel för program hantering finns i [Azure AD PowerShell-exempel för program hantering](../app-management-powershell-samples.md).
