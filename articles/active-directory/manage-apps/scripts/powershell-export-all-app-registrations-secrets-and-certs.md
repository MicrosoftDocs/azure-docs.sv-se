---
title: PowerShell-exempel – Exportera hemligheter och certifikat för appregistreringar i Azure Active Directory klientorganisation.
description: PowerShell-exempel som exporterar alla hemligheter och certifikat för de angivna appregistreringarna i din Azure Active Directory klientorganisation.
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
ms.openlocfilehash: b5cbb6b3843e81d9265405dcea24a092e57bf65e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377054"
---
# <a name="export-secrets-and-certificates-for-app-registrations"></a>Exportera hemligheter och certifikat för appregistreringar

Det här PowerShell-skriptexempel exporterar alla hemligheter och certifikat för de angivna appregistreringarna från din katalog till en CSV-fil.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Det här exemplet kräver [AzureAD V2 PowerShell för Graph-modulen](/powershell/azure/active-directory/install-adv2) (AzureAD) eller Förhandsversionen av [AzureAD V2 PowerShell för Graph-modulen](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-app-registrations-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified app registrations in your directory.")]

## <a name="script-explanation"></a>Förklaring av skript

Kommandot "Add-Member" ansvarar för att skapa kolumnerna i CSV-filen.
Du kan ändra variabeln "$Path" direkt i PowerShell, med en CSV-filsökväg, om du föredrar att exporten inte är interaktiv.

| Kommando | Kommentarer |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Hämtar ett program från din katalog. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner) | Hämtar ägare av ett program från din katalog. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD PowerShell-modulen finns i [Översikt över Azure AD PowerShell-modulen.](/powershell/azure/active-directory/overview)

Andra PowerShell-exempel för programhantering finns i [Azure AD PowerShell-exempel för programhantering.](../app-management-powershell-samples.md)
