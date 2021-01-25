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
ms.openlocfilehash: c7abad31c9936729b8d9c19ed2efcb841ac103ca
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756238"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Webbapp som loggar in användare: flytta till produktion

Nu när du vet hur du kan hämta en token för att anropa webb-API: er, lär dig hur du flyttar den till produktionen.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="troubleshooting"></a>Felsökning

> [!NOTE]
> När användarna loggar in på webb programmet för första gången måste de godkänna. Men i vissa organisationer kan användare se ett meddelande som liknar följande:
>
> *AppName behöver behörighet för att få åtkomst till resurser i din organisation som bara en administratör kan bevilja. Be en administratör att bevilja behörighet till den här appen innan du kan använda den.*
>
> Detta beror på att klient administratören har **inaktiverat** möjligheten för användare att godkänna. I så fall måste du kontakta klient organisationens administratörer så att de gör ett administrativt medgivande för de omfattningar som krävs av programmet.

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