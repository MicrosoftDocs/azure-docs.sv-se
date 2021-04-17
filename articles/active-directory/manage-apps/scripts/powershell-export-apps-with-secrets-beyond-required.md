---
title: PowerShell-exempel – Exportera appar med hemligheter och certifikat som går ut efter det datum som Azure Active Directory klientorganisationen.
description: PowerShell-exempel som exporterar alla appar med hemligheter och certifikat som går ut efter det obligatoriska datumet för de angivna apparna i din Azure Active Directory klientorganisation.
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
ms.openlocfilehash: 692ab2cfd480fd15760c13c63922fe76d23058ea
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375397"
---
# <a name="export-apps-with-secrets-and-certificates-expiring-beyond-the-required-date"></a>Exportera appar med hemligheter och certifikat som upphör att gälla efter det obligatoriska datumet

Det här PowerShell-skriptexempel exporterar alla appregistreringar hemligheter och certifikat som går ut efter en nödvändig period för de angivna apparna från din katalog i en CSV-fil icke-interaktivt.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-secrets-beyond-required.ps1 "Exports all apps with secrets and certificates expiring beyond the required date for the specified apps in your directory.")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet fungerar icke-interaktivt. Administratören som använder den måste ändra värdena i avsnittet "#PARAMETERS TO CHANGE" med sitt eget app-ID, programhemlighet, klientorganisationsnamn, perioden för att appautentiseringsuppgifterna upphör att gälla och sökvägen där CSV:n kommer att exporteras.
Det här skriptet [använder Client_Credential Oauth Flow](../../develop/v2-oauth2-client-creds-grant-flow.md) Funktionen "RefreshToken" skapar åtkomsttoken baserat på värdena för de parametrar som har ändrats av administratören.

Kommandot "Add-Member" ansvarar för att skapa kolumnerna i CSV-filen.

| Kommando | Kommentarer |
|---|---|
| [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1&preserve-view=true) | Skickar HTTP- och HTTPS-begäranden till en webbsida eller webbtjänst. Den parsar svaret och returnerar samlingar av länkar, bilder och andra viktiga HTML-element. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD PowerShell-modulen finns i Översikt [över Azure AD PowerShell-modulen.](/powershell/azure/active-directory/overview)

Andra PowerShell-exempel för programhantering finns i [Azure AD PowerShell-exempel för programhantering.](../app-management-powershell-samples.md)
