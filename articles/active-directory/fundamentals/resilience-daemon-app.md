---
title: Öka återhämtningen av autentisering och auktorisering i daemon-program som du utvecklar
titleSuffix: Microsoft identity platform
description: Vägledning för ökad återhämtning av autentisering och auktorisering i daemon-program med hjälp av Microsoft Identity Platform
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: a7b8f893026bb96c8d768d2e6d07d0240ecb81fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724849"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-daemon-applications-you-develop"></a>Öka återhämtningen av autentisering och auktorisering i daemon-program som du utvecklar

Den här artikeln innehåller rikt linjer för hur utvecklare kan använda Microsoft Identity Platform och Azure Active Directory för att öka återhämtningen av daemon-program. Detta inkluderar bakgrunds processer, tjänster, server-till-Server-appar och program utan användare.

![Ett daemon-program som gör ett anrop till Microsoft-identitet](media/resilience-daemon-app/calling-microsoft-identity.png)

## <a name="use-managed-identities-for-azure-resources"></a>Använda hanterade identiteter för Azure-resurser

Utvecklare som skapar daemon-appar på Microsoft Azure kan använda [hanterade identiteter för Azure-resurser](../managed-identities-azure-resources/overview.md). Hanterade identiteter eliminerar behovet av utvecklare att hantera hemligheter och autentiseringsuppgifter. Funktionen förbättrar återhämtningen genom att undvika misstag runt certifikatets förfallo datum, rotations fel eller förtroende. Den har också flera inbyggda funktioner som specifikt är avsedda att öka återhämtningen.

Hanterade identiteter använder långa åtkomsttoken och information från Microsoft Identity för att proaktivt hämta nya token inom ett stort tidsintervall innan den befintliga token upphör att gälla. Din app kan fortsätta att köras vid försök att hämta en ny token.

Hanterade identiteter använder också regionala slut punkter för att förbättra prestanda och återhämtning mot problem som är utanför region. Genom att använda en regional slut punkt kan du hålla all trafik inom ett geografiskt område. Om din Azure-resurs till exempel är i WestUS2, ska all trafik, inklusive Microsoft Identity-genererad trafik, vara kvar i WestUS2. Detta eliminerar möjliga problem punkter genom att konsolidera beroenden för din tjänst.

## <a name="use-the-microsoft-authentication-library"></a>Använd Microsoft Authentication Library

Utvecklare av daemon-appar som inte använder hanterade identiteter kan använda [Microsoft Authentication Library (MSAL)](../develop/msal-overview.md), som gör det enkelt att implementera autentisering och auktorisering, och använder automatiskt bästa metoder för återhämtning. MSAL gör det enklare att tillhandahålla nödvändiga klient uppgifter. Ditt program behöver till exempel inte implementera skapande och signering JSON Web Token intyg när du använder certifikatbaserade autentiseringsuppgifter.

### <a name="use-microsoftidentityweb-for-net-developers"></a>Använd Microsoft. Identity. Web för .NET-utvecklare

Utvecklare som skapar daemon-appar på ASP.NET Core kan använda [Microsoft. Identity. Web-](../develop/microsoft-identity-web.md) biblioteket. Det här biblioteket bygger på MSAL för att göra det ännu enklare att implementera auktorisering för ASP.NET Core appar. Den innehåller flera metoder för [distribuerad token-cache](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization#distributed-token-cache) för distribuerade appar som kan köras i flera regioner.

## <a name="cache-and-store-tokens"></a>Cache-och lagrings-token

Om du inte använder MSAL för att implementera autentisering och auktorisering kan du implementera några metod tips för cachelagring och lagring av tokens. MSAL implementerar och följer dessa bästa metoder automatiskt.

Ett program hämtar token från en identitets leverantör för att ge programmet tillåtelse att anropa skyddade API: er. När din app tar emot token innehåller svaret som innehåller tokens även en "Expires \_ in"-egenskap som talar om för programmet hur lång tid det tar att cachelagra och återanvända token. Det är viktigt att programmen använder egenskapen "Expires \_ in" för att fastställa livs längd för token. Programmet får aldrig försöka avkoda en API-åtkomsttoken. Om du använder den cachelagrade token förhindras onödig trafik mellan appen och Microsoft-identiteten. Din användare kan vara inloggad på ditt program för längden på den tokens livs längd.

## <a name="properly-handle-service-responses"></a>Hantera tjänst svar korrekt

Slutligen, medan program ska hantera alla fel svar finns det vissa svar som kan påverka återhämtningen. Om ditt program får en HTTP 429-svarskod, för många begär Anden, begränsar Microsoft-identiteten dina begär Anden. Om din app fortsätter att göra för många begär Anden fortsätter den att begränsas förhindra att din app tar emot tokens. Ditt program bör inte försöka hämta en token igen förrän efter den tiden, i sekunder, i svars fältet "retry-efter" har passerat. Att ta emot ett 429-svar är ofta en indikation på att programmet inte cachelagrar och återanvänder tokens på rätt sätt. Utvecklare bör se hur token cachelagras och återanvändas i programmet.

När ett program får en HTTP 5xx-svarskod får appen inte ange en snabb återförsöks slinga. När programmet är närvarande bör programmet respektera samma "försök-efter"-hantering eftersom det gör ett 429-svar. Om det inte finns någon "retry-by"-rubrik från svaret rekommenderar vi att du implementerar ett exponentiellt återförsök med det första försöket minst 5 sekunder efter svaret.

När en tids gräns för begäran inte bör försöka igen omedelbart. Implementera ett exponentiellt återförsök med det första försöket minst 5 sekunder efter svaret.

## <a name="next-steps"></a>Nästa steg

- [Bygg återhämtning till program som loggar in användare](resilience-client-app.md)
- [Bygg återhämtning i din infrastruktur för identitets-och åtkomst hantering](resilience-in-infrastructure.md)
- [Bygg återhämtning i dina CIAM-system](resilience-b2c.md)