---
title: PowerShell-exempel – exportera appar med förfallna hemligheter och certifikat i Azure Active Directory klient organisationen.
description: PowerShell-exempel som exporterar alla appar med förfallna hemligheter och certifikat för de angivna apparna i din Azure Active Directory klient.
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
ms.openlocfilehash: def9b55a1d873cccda5d1c48921e3f098beeced1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149723"
---
# <a name="export-apps-with-expiring-secrets-and-certificates"></a>Exportera appar med utgångna hemligheter och certifikat

I det här PowerShell-skriptet exporteras alla registrerings program med utgångna hemligheter, certifikat och deras ägare för de angivna apparna från katalogen i en CSV-fil.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Det här exemplet kräver [AzureAD v2 PowerShell för Graph module](/powershell/azure/active-directory/install-adv2) (AzureAD) eller [AzureAD v2 PowerShell för för hands versionen](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) av (AzureADPreview).

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-expiring-secrets.ps1 "Exports all apps with expiring secrets and certificates for the specified apps in your directory.")]

## <a name="script-explanation"></a>Förklaring av skript

Skriptet kan användas direkt utan några ändringar. Administratören kommer att tillfrågas om förfallo datumet och om de vill visa redan utgångna hemligheter eller certifikat eller inte.

Kommandot "Lägg till medlem" ansvarar för att skapa kolumner i CSV-filen.
Kommandot "nytt-objekt" skapar ett objekt som ska användas för kolumnerna i exporten av CSV-filen.
Du kan ändra variabeln "$Path" direkt i PowerShell med en CSV-fil Sök väg, om du hellre vill att exporten ska vara icke-interaktiv.

| Kommando | Kommentarer |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | Hämtar ett program från din katalog. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner?view=azureadps-2.0&preserve-view=true) | Hämtar ägare av ett program från din katalog. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD PowerShell-modulen finns i [Översikt över Azure AD PowerShell-modulen](/powershell/azure/active-directory/overview).

Andra PowerShell-exempel för program hantering finns i [Azure AD PowerShell-exempel för program hantering](../app-management-powershell-samples.md).
