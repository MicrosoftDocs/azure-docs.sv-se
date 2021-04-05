---
title: Vad är API:er för Bing-sökresultat?
titleSuffix: Azure Cognitive Services
description: Använd den här artikeln om du vill veta mer om API:er för Bing-sökresultat och hur du kan aktivera kognitiva Internets ökningar i dina appar och tjänster.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 72b4755c6f01a10851e79cf274842f1599bc2c55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96349545"
---
# <a name="what-are-the-bing-search-apis"></a>Vad är API:er för Bing-sökresultat?

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Med API:er för Bing-sökning kan du skapa webbanslutna appar och tjänster som söker efter webbsidor, bilder, nyheter, platser med mera, utan annonser. Genom att skicka Sök begär Anden med Bing-sökning REST-API: er eller SDK: er kan du hämta relevant information och innehåll för Webbs ökningar. Använd den här artikeln om du vill veta mer om de olika API: erna för Bing search och hur du kan integrera kognitiva sökningar i dina program och tjänster. Priser och hastighets begränsningar kan variera mellan API: er.

## <a name="the-bing-web-search-api"></a>API för webbsökning i Bing

[API för webbsökning i Bing](../Bing-Web-Search/overview.md) returnerar webb sidor, bilder, video, nyheter med mera. Du kan filtrera Sök frågorna som skickas till detta API för att inkludera eller exkludera vissa innehålls typer.

Överväg att använda API för webbsökning i Bing i program som kan behöva söka efter alla typer av relevant webb innehåll. Om programmet söker efter en specifik typ av onlineinnehåll, bör du överväga något av Sök-API: erna nedan:

## <a name="content-specific-bing-search-apis"></a>API: er för innehålls bestämd Bing-sökning

Följande API: er för Bing-sökning returnerar särskilt innehåll från webben som bilder, nyheter, lokala företag och videor.

| Bing-API | Beskrivning |
| -- | -- |
| [Entitetssökning](../Bing-Entities-Search/overview.md) | API för entitetsökning i Bing returnerar Sök resultat som innehåller entiteter, som kan vara personer, platser eller saker. Beroende på frågan returnerar API: n en eller flera entiteter som uppfyller Sök frågan. Sök frågan kan innehålla viktiga individer, lokala företag, landmärken, destinationer och mycket annat. |
| [Bildsökning](../Bing-Image-Search/overview.md) | Med API för bildsökning i Bing kan du söka efter och hitta statiska och animerade bilder med hög kvalitet som liknar [Bing.com/images](https://www.Bing.com/images). Du kan förfina sökningar för att inkludera eller exkludera bilder efter attribut, inklusive storlek, färg, licens och aktualitet. Du kan också söka efter trender för bilder, ladda upp bilder för att få insikter om dem och Visa miniatyr bilder. |
| [Nyhetssökning](../Bing-News-Search/search-the-web.md) | I API för nyhetssökning i Bing kan du hitta nyhets berättelser som liknar [Bing.com/news](https://www.Bing.com/news). API: n returnerar nyhets artiklar från antingen flera källor eller vissa domäner. Du kan söka bland kategorier för att få trender för artiklar, huvud berättelser och rubriker. |
| [Videosökning](../Bing-Video-Search/overview.md) | Med API för videosökning i Bing kan du hitta videor på webben. Få trender i videor, relaterat innehåll och miniatyr bilder. |
| [Visuell sökning](../Bing-visual-search/overview.md) | Ladda upp en bild eller Använd en URL för att få detaljerad information om den, t. ex. visuella liknande produkter, bilder och relaterade sökningar. |
 [Lokal företags sökning](../bing-local-business-search/overview.md) | Med API: t för lokal sökning i Bing kan dina program hitta kontakt-och plats information om lokala företag baserat på Sök frågor. |

## <a name="the-bing-custom-search-api"></a>API för anpassad Bing-sökning

Genom att skapa en anpassad Sök instans med [anpassad Bing-sökning](../Bing-Custom-Search/overview.md) API kan du skapa en Sök upplevelse som bara fokuserar på innehåll och ämnen som du bryr dig om. När du till exempel har angett domäner, webbplatser och vissa webb sidor som Bing söker, kommer Anpassad Bing-sökning att skräddarsy resultaten till det aktuella innehållet. Du kan lägga till anpassade automatiska förslag, bilder och Videosökning-API: er i Bing för att ytterligare anpassa din Sök upplevelse.

## <a name="additional-bing-search-apis"></a>Ytterligare API:er för Bing-sökresultat

I följande API:er för Bing-sökresultat kan du förbättra din Sök upplevelse genom att kombinera dem med andra Bing Search-API: er.

| API | Beskrivning |
| -- | -- |
| [Automatiska förslag i Bing](../Bing-Autosuggest/get-suggested-search-terms.md) | Förbättra programmets Sök upplevelse med API för automatiska förslag i Bing genom att returnera föreslagna sökningar i real tid.  |
| [Bing-statistik](bing-web-stats.md) | Bing-statistik ger analyser för de API:er för Bing-sökresultat som används i programmet. Några av de tillgängliga analyserna är anrops volym, topp frågesträngar och geografisk distribution. |

## <a name="next-steps"></a>Nästa steg

* [Information om](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) Bing-sökning-API-pris
* I [användnings- och visningskraven för Bing](./use-display-requirements.md) specificeras godtagbar användning för det innehåll och den information du får via API:erna för Bing-sökning.