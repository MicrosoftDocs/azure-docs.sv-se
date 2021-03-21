---
title: PowerShell-exempel – exportera appar med hemligheter och certifikat som upphör att gälla efter det datum som krävs i Azure Active Directory klient organisationen.
description: PowerShell-exempel som exporterar alla appar med hemligheter och certifikat som upphör att gälla efter det datum som anges för de angivna apparna i Azure Active Directory klient organisationen.
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
ms.openlocfilehash: 9c0e5508830343561833785fbce31f547a8a7428
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149689"
---
# <a name="export-apps-with-secrets-and-certificates-expiring-beyond-the-required-date"></a>Exportera appar med hemligheter och certifikat som upphör att gälla efter det datum som krävs

Det här exemplet på PowerShell-skript exporterar alla hemligheter och certifikat som upphör att gälla efter en obligatorisk period för de angivna apparna från din katalog i en CSV-fil som inte är interaktivt.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-secrets-beyond-required.ps1 "Exports all apps with secrets and certificates expiring beyond the required date for the specified apps in your directory.")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet fungerar icke-interaktivt. Administratören som använder den måste ändra värdena i avsnittet "#PARAMETERS att ändra" med sitt eget app-ID, program hemlighet, klient namn, period för förfallo datum för appar och sökvägen dit CSV-filen ska exporteras.
Det här skriptet använder [Client_Credential OAuth-flöde](../../develop/v2-oauth2-client-creds-grant-flow.md) som funktionen "RefreshToken" kommer att bygga åtkomsttoken baserat på värdena för parametrarna som administratören ändrat.

Kommandot "Lägg till medlem" ansvarar för att skapa kolumner i CSV-filen.

| Kommando | Kommentarer |
|---|---|
| [Anropa-webbegäran](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1) | Skickar HTTP-och HTTPS-begäranden till en webb sida eller webb tjänst. Den tolkar svaret och returnerar samlingar med länkar, bilder och andra viktiga HTML-element. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD PowerShell-modulen finns i [Översikt över Azure AD PowerShell-modulen](/powershell/azure/active-directory/overview).

Andra PowerShell-exempel för program hantering finns i [Azure AD PowerShell-exempel för program hantering](../app-management-powershell-samples.md).
