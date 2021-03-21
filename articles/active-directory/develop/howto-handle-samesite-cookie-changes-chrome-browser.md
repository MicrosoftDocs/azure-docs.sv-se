---
title: Så här hanterar du ändringar av SameSite-cookie i Chrome Browser | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du hanterar SameSite cookie-ändringar i Chrome-webbläsaren.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 30c4f054259aa7c3f2a9fdfaeeadd64f26dd9bea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94444919"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>Hantera SameSite-cookieändringar i webbläsaren Chrome

## <a name="what-is-samesite"></a>Vad är SameSite?

`SameSite` är en egenskap som kan anges i HTTP-cookies för att förhindra förfalskning av CSRF-attacker (Cross Site Request) i webb program:

- När `SameSite` är inställt på **lax** skickas cookien i begär Anden inom samma plats och i Hämta förfrågningar från andra platser. Den skickas inte i GET-begäranden som är mellan domäner.
- Värdet **strict** garanterar att cookien skickas i begär Anden endast inom samma plats.

Som standard `SameSite` är värdet inte angivet i webbläsare och det är därför att det inte finns några begränsningar för cookies som skickas i begär Anden. Ett program måste delta i CSRF-skyddet genom att ställa in **lax** eller **strict** enligt deras krav.

## <a name="samesite-changes-and-impact-on-authentication"></a>SameSite ändringar och inverkan på autentisering

[De senaste uppdateringarna av standarderna på SameSite](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) föreslår skydd av appar genom att göra standard beteendet för `SameSite` när inget värde är inställt på lax. Den här minskningen innebär att cookies begränsas för HTTP-förfrågningar, förutom att de görs från andra platser. Dessutom introduceras värdet **inget** för att ta bort begränsningar för cookies som skickas. De här uppdateringarna kommer snart att lanseras i en kommande version av Chrome-webbläsaren.

När webbappar autentiserar med Microsoft Identity Platform med svars läget "form_post", svarar inloggnings servern på programmet med ett HTTP-inlägg för att skicka token eller auth-koden. Eftersom den här begäran är en kors domän förfrågan (från `login.microsoftonline.com` till din domän-till-instans `https://contoso.com/auth` ) hamnar cookies som har angetts av din app nu under de nya reglerna i Chrome. De cookies som måste användas i scenarier med olika platser är cookies som innehåller värdena för *status* och *nonce* , som också skickas i inloggningsbegäran. Det finns andra cookies som har släppts av Azure AD för att hålla sessionen.

Om du inte uppdaterar dina webbappar kommer det här nya beteendet leda till autentiseringsfel.

## <a name="mitigation-and-samples"></a>Minskning och exempel

För att undvika autentiseringsfel kan webbappar som autentiseras med Microsoft Identity Platform ställa in `SameSite` egenskapen på `None` för cookies som används i scenarier mellan domäner när de körs i webbläsaren Chrome.
Andra webbläsare (se [här](https://www.chromium.org/updates/same-site/incompatible-clients) för en fullständig lista) Följ föregående beteende för `SameSite` och kommer inte att inkludera cookies om `SameSite=None` har angetts.
Därför måste du, för att få stöd för autentisering på flera webbläsare-webbappar, ange `SameSite` värdet till `None` endast på Chrome och lämna värdet tomt i andra webbläsare.

Den här metoden visas i våra kod exempel nedan.

# <a name="net"></a>[.NET](#tab/dotnet)

I tabellen nedan presenteras de pull-begäranden som arbetade kring SameSite ändringar i vår ASP.NET och ASP.NET Core exempel.

| Exempel | Pull-begäran |
| ------ | ------------ |
|  [Stegvis självstudie för ASP.NET Core-webbappar](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [Korrigering av samma plats cookie #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [Exempel på ASP.NET MVC-webbapp](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [Korrigering av samma plats cookie #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [Active Directory-dotNet-admin-restricted-scope – v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [Korrigering av samma plats cookie #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

Mer information om hur du hanterar SameSite cookies i ASP.NET och ASP.NET Core finns också i:

- [Arbeta med SameSite cookies i ASP.net Core](/aspnet/core/security/samesite) .
- [ASP.NET blogg om SameSite-problem](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| Exempel |
| ------ |
|  [MS-Identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| Exempel | Pull-begäran |
| ------ | ------------ |
|  [MS-Identity-Java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [Korrigering av samma plats cookie #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [MS-Identity-Java-WebAPI](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [Korrigering av samma plats cookie #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>Nästa steg

Läs mer om SameSite och scenariot för webb program:

- [Vanliga frågor och svar om Google Chrome på SameSite](https://www.chromium.org/updates/same-site/faq)

- [Sidan krom SameSite](https://www.chromium.org/updates/same-site)

- [Scenario: webb program som loggar in användare](scenario-web-app-sign-user-overview.md)