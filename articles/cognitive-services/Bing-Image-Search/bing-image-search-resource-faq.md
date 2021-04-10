---
title: Vanliga frågor och svar (FAQ) – API för bildsökning i Bing
titleSuffix: Azure Cognitive Services
description: Få svar på vanliga frågor om begrepp, kod och scenarier som är relaterade till API för bildsökning i Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: e8502b1d01e0f3fbf5d42e924511cc978690bce4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96342200"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>Vanliga frågor och svar om API för bildsökning i Bing

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Få svar på vanliga frågor om begrepp, kod och scenarier som är relaterade till API för bildsökning i Bing för Microsoft Cognitive Services på Azure.

## <a name="response-headers-in-javascript"></a>Svars rubriker i Java Script

Följande rubriker kan förekomma i svar från API för bildsökning i Bing.

| Attribut           | Beskrivning   |
| ------------------- | ------------- |
| `X-MSEdge-ClientID` |Det unika ID som Bing har tilldelat till användaren |
| `BingAPIs-Market`   |Marknaden som användes för att uppfylla begäran |
| `BingAPIs-TraceId`  |Logg posten på Bing API-servern för den här begäran (för support) |

Det är särskilt viktigt att spara klient-ID och returnera det till efterföljande begär Anden. När du gör detta använder sökningen tidigare kontext i ranknings Sök Resultat och ger också en konsekvent användar upplevelse.

Men när du anropar API för bildsökning i Bing från java script kan webbläsarens inbyggda säkerhetsfunktioner (CORS) förhindra åtkomst till värdena i dessa huvuden.

Om du vill få åtkomst till sidhuvudena kan du göra API för bildsökning i Bing begäran via en CORS-proxy. Svaret från en sådan proxy har ett `Access-Control-Expose-Headers` huvud som filtrerar svarshuvuden och gör dem tillgängliga för Java Script.

Det är enkelt att installera en CORS-proxy så att vår [självstudie](tutorial-bing-image-search-single-page-app.md) kan komma åt de valfria klient rubrikerna. [Installera Node.js](https://nodejs.org/en/download/) om du inte redan har det. Ange sedan följande kommando i en kommandotolk.

```console
npm install -g cors-proxy-server
```

Ändra sedan API för bildsökning i Bing-slutpunkten i HTML-filen till: \
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Slutligen startar du CORS-proxyn med följande kommando:

```console
cors-proxy-server
```

Lämna kommandofönstret öppet medan du använder självstudieappen. Om du stänger fönstret stoppas proxyn. I det expanderbara avsnittet om HTTP-huvuden nedan kan du nu se `X-MSEdge-ClientID`-huvudet (bland annat) under sökresultatet och du kan kontrollera att det är samma för varje begäran.

## <a name="response-headers-in-production"></a>Svars rubriker i produktion

Den CORS-proxy metoden som beskrivs i föregående svar är lämplig för utveckling, testning och inlärning.

I en produktions miljö bör du dock vara värd för ett skript på Server sidan på samma domän som webb sidan som använder API för webbsökning i Bing. Det här skriptet bör faktiskt utföra API-anropen på begäran från webb sidans Java Script och skicka alla resultat, inklusive huvuden, tillbaka till klienten. Eftersom de två resurserna (sida och skript) delar ett ursprung, kommer CORS inte att komma in i spelet och de särskilda rubrikerna är tillgängliga för Java Script på webb sidan.

Den här metoden skyddar också API-nyckeln från att exponeras för allmänheten, eftersom endast skript på Server sidan behöver det. Skriptet kan använda en annan metod (t. ex. HTTP-referenten) för att se till att begäran är auktoriserad.

## <a name="next-steps"></a>Nästa steg

Är din fråga om en funktion eller funktionalitet som saknas? Överväg att begära eller rösta på vår [användares röst webbplats](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Se även

 [Stack Overflow: Cognitive Services](https://stackoverflow.com/questions/tagged/bing-api)