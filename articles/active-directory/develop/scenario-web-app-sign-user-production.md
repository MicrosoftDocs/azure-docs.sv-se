---
title: Flytta webb program som loggar in användare till produktion | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar en webbapp som loggar in användare (flytta till produktion)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e4a47112d2f66edc8af9b7f100d48bc205f2e85e
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584305"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Webbapp som loggar in användare: flytta till produktion

Nu när du vet hur du kan hämta en token för att anropa webb-API: er, här är några saker att tänka på när du flyttar ditt program till produktion.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="troubleshooting"></a>Felsökning
När användarna loggar in på webb programmet för första gången måste de godkänna. Men i vissa organisationer kan användare se ett meddelande som liknar följande: *APPNAME behöver behörighet för att komma åt resurser i din organisation som bara en administratör kan bevilja. Be en administratör att bevilja behörighet till den här appen innan du kan använda den.*
Detta beror på att klient administratören har **inaktiverat** möjligheten för användare att godkänna. I så fall kan du kontakta klient organisationens administratörer så att de gör ett administrativt medgivande för de omfattningar som krävs av programmet.

## <a name="same-site"></a>Samma plats

Se till att du förstår möjliga problem med nya versioner av Chrome-webbläsaren: [så här hanterar du ändringar i SameSite cookie i Chrome-webbläsaren](howto-handle-samesite-cookie-changes-chrome-browser.md).

Paketet Microsoft. Identity. Web NuGet hanterar de vanligaste SameSite-problemen.

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Djupgående övning: ASP.NET Core Web Apps-självstudie

Lär dig mer om andra sätt att logga in användare med den här ASP.NET Core själv studie kursen: 

[Aktivera dina webbappar för att logga in användare och anropa API: er med Microsoft Identity Platform för utvecklare](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)

Den här Progressive-kursen har produktions klart kod för en webbapp, inklusive hur du lägger till inloggning med konton i:

- Din organisation
- Flera organisationer
- Arbets-eller skol konton eller personliga Microsoft-konton
- [Azure AD B2C](../../active-directory-b2c/overview.md)
- Nationella moln

## <a name="sample-code-java-web-app"></a>Exempel kod: Java-webbapp

Läs mer om Java-webbappen från det här exemplet på GitHub: 

[Ett Java-webbprogram som loggar in användare med Microsoft Identity Platform och anropar Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)

## <a name="next-steps"></a>Nästa steg

När din webbapp loggar in användare kan den anropa webb-API: er åt de inloggade användarna. Anrop till webb-API: er från webbappen är objektet i följande scenario: [webbappen som anropar webb-API: er](scenario-web-app-call-api-overview.md).