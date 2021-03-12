---
title: PowerShell-exempel – exportera hemligheter och certifikat för registrerings program i Azure Active Directory klient organisationen.
description: PowerShell-exempel som exporterar alla hemligheter och certifikat för de angivna app-registreringarna i Azure Active Directory-klienten.
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
ms.openlocfilehash: d0de96d0d8a5edc6fbacc25dcbcb868073e57183
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556561"
---
# <a name="export-secrets-and-certificates-for-app-registrations"></a>Exportera hemligheter och certifikat för app-registreringar

Detta exempel på PowerShell-skript exporterar alla hemligheter och certifikat för de angivna registrerings program från din katalog till en CSV-fil.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Det här exemplet kräver [AzureAD v2 PowerShell för Graph module](/powershell/azure/active-directory/install-adv2) (AzureAD) eller [AzureAD v2 PowerShell för för hands versionen](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) av (AzureADPreview).

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-app-registrations-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified app registrations in your directory.")]

## <a name="script-explanation"></a>Förklaring av skript

Kommandot "Lägg till medlem" ansvarar för att skapa kolumner i CSV-filen.
Du kan ändra variabeln "$Path" direkt i PowerShell med en CSV-fil Sök väg, om du hellre vill att exporten ska vara icke-interaktiv.

| Kommando | Kommentarer |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Hämtar ett program från din katalog. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner) | Hämtar ägare av ett program från din katalog. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD PowerShell-modulen finns i [Översikt över Azure AD PowerShell-modulen](/powershell/azure/active-directory/overview).

Andra PowerShell-exempel för program hantering finns i [Azure AD PowerShell-exempel för program hantering](../app-management-powershell-samples.md).
