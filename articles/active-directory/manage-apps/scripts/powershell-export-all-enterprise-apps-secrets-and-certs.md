---
title: PowerShell-exempel – Exportera hemligheter och certifikat för företagsappar i Azure Active Directory klientorganisation.
description: PowerShell-exempel som exporterar alla hemligheter och certifikat för de angivna företagsapparna i Azure Active Directory klientorganisationen.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: 536197ebc5df94447f3937773e0447e47961bd92
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378609"
---
# <a name="export-secrets-and-certificates-for-enterprise-apps"></a>Exportera hemligheter och certifikat för företagsappar
Det här PowerShell-skriptexempel exporterar alla hemligheter, certifikat och ägare för de angivna företagsapparna från din katalog till en CSV-fil.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Det här exemplet kräver [AzureAD V2 PowerShell för Graph-modulen](/powershell/azure/active-directory/install-adv2) (AzureAD) eller förhandsversionen av [AzureAD V2 PowerShell för Graph-modulen](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-enterprise-apps-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified enterprise apps in your directory.")]

## <a name="script-explanation"></a>Förklaring av skript

Kommandot "Lägg till medlem" ansvarar för att skapa kolumnerna i CSV-filen.
Du kan ändra variabeln "$Path" direkt i PowerShell med en CSV-filsökväg, om du föredrar att exporten inte är interaktiv.

| Kommando | Kommentarer |
|---|---|
| [Get-AzureADServicePrincipal](/powershell/module/azuread/Get-azureADServicePrincipal?view=azureadps-2.0&preserve-view=true) | Hämtar ett företagsprogram från din katalog. |
| [Get-AzureADServicePrincipalOwner](/powershell/module/azuread/Get-AzureADServicePrincipalOwner?view=azureadps-2.0&preserve-view=true) | Hämtar ägarna av ett företagsprogram från din katalog. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD PowerShell-modulen finns i Översikt över [Azure AD PowerShell-modulen](/powershell/azure/active-directory/overview).

Andra PowerShell-exempel för programhantering finns i [Azure AD PowerShell-exempel för programhantering.](../app-management-powershell-samples.md)
