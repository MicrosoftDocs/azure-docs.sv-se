---
title: Exempel på Bing Insights – Visuell sökning i Bing
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller exempel på hur Visuell sökning i Bing kan använda och Visa bild insikter på Bing.com.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: e0a3bdc7e68301c067b7e1ca80a3e708522e0df1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96493363"
---
# <a name="examples-of-bing-insights-usage"></a>Exempel på Bing Insights-användning

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Den här artikeln innehåller exempel på hur Bing kan använda och Visa bild insikter på Bing.com.

## <a name="pagesincluding-insight-example"></a>Exempel på PagesIncluding Insight

Följande visar en länk till den första webb sidan och låter användaren expandera och komprimera listan över andra webb sidor som innehåller avbildningen:

![Utökade sidor inklusive](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>Exempel på ShoppingSources Insight

Följande visar hur Bing kan visa shopping källor för produkter som visas i bilden:

![Shoppingkällor](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>Exempel på VisualSearch Insight

Följande visar hur Bing kan visa visuellt likartade bilder (se **relaterade bilder** i exemplet):

![Visuellt likartade bilder](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Exempel på recept insikter

Följande visar hur Bing kan visa recept för det livsmedel som visas i bilden. Exemplet låter användaren veta att det finns recept:

![Recept och sidor inklusive](./media/recipes-pages-including.PNG)

 Och innehåller en länk till recepten när användaren expanderar listan:

![Utökade recept sidor, inklusive](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>Exempel på RelatedSearches Insight

Följande visar hur Bing kan visa relaterade sökningar av bilder som andra har gjort. Om användaren klickar på bilden, hämtas användaren till sidan för Bing.com/images Sök Resultat för den relaterade frågan.

![Relaterade sökningar efter bilder](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Exempel på insikter för entitet

Följande visar hur Bing kan visa information om entiteten (person, plats eller sak) som visas i bilden. Om användaren klickar på enhets länken, tas användaren till sidan för Bing.com Sök Resultat för entiteten:

![Entitet som visas i bild](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>Visa andra insikter som användaren kan utforska

Följande visar hur Bing kan visa annan information om den bild som användaren kan utforska.

![Utforska andra insikter om avbildningen](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>Avgränsnings rutor och aktiva punkter

Taggar som inte är standard inkluderar den avgränsnings ruta som identifierar det intresse område i bilden som taggen gäller för. Om markerings rutan inte identifierar hela bilden använder du avgränsnings rutan för att skapa en aktiv punkt på bilden. Användaren kan klicka på den aktiva punkten för att få information som rör det innehåll som finns under den aktiva punkten (eller rektangeln). Om bilden till exempel är en högmode-bild kan resultatet innehålla Taggar (och markerings rutor) för tillbehör som visas i bilden, till exempel en väska, smyckespecifikationer, scarfs och så vidare. I följande exempel visas en varm punkts ram för sol glasögon som visas i bilden:

![Avgränsnings ruta och aktiv punkt](./media/click-to-search.PNG)

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med din första begäran finns i snabb starter:

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)