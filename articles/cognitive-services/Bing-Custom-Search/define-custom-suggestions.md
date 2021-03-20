---
title: Definiera anpassade förslag för automatiska förslag – Anpassad Bing-sökning
titleSuffix: Azure Cognitive Services
description: Anpassad automatiska förslag returnerar en lista med föreslagna Sök frågesträngar som är relevanta för din Sök funktion.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 986a7c8f1282b76c2ae94eebcc2c31dc3b3e7cad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96352022"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Konfigurera din anpassade automatiska förslags upplevelse

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Anpassad automatiska förslag returnerar en lista med föreslagna Sök frågesträngar som är relevanta för din Sök funktion. De föreslagna frågesträngarna baseras på en partiell frågesträng som användaren anger i sökrutan. Listan får innehålla högst 10 förslag. 

Du anger om du bara vill returnera anpassade förslag eller om du även vill inkludera Bing-förslag. Om du inkluderar Bing-förslag visas anpassade förslag före Bing-förslagen. Om du tillhandahåller tillräckligt med förslag, är det möjligt att den returnerade listan över förslag inte innehåller Bing-förslag. Bing-förslag är alltid i kontexten för din anpassade Sök instans. 

Klicka på fliken **föreslå** automatiskt om du vill konfigurera Sök fråge förslag för din instans.  

> [!NOTE]
> Om du vill använda den här funktionen måste du prenumerera på Anpassad sökning på rätt nivå (se [prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)).

Det kan ta upp till 24 timmar innan förslag visas i den betjäna slut punkten (API eller värdbaserat gränssnitt).

## <a name="enable-bing-suggestions"></a>Aktivera Bing-förslag

Aktivera Bing-förslag genom att växla skjutreglaget för **Automatisk Bing-förslag** till på plats. Skjutreglaget blir blått.

## <a name="add-your-own-suggestions"></a>Lägg till egna förslag

Lägg till dina egna fråge Strängs förslag genom att lägga till dem i listan under **användardefinierade förslag**. När du har lagt till ett förslag i listan trycker du på RETUR-tangenten eller på **+** ikonen. Du kan ange förslaget på valfritt språk. Du kan lägga till högst 5 000 fråge Strängs förslag.

## <a name="upload-suggestions"></a>Ladda upp förslag

Som ett alternativ kan du ladda upp en lista med förslag från en fil. Filen måste innehålla en Sök frågesträng per rad. Överför filen genom att klicka på ikonen Ladda upp och markera den fil som ska laddas upp. Tjänsten extraherar förslagen från filen och lägger till dem i listan.

## <a name="remove-suggestions"></a>Ta bort förslag

Om du vill ta bort ett fråge Strängs förslag klickar du på ikonen Ta bort bredvid förslaget som du vill ta bort.

## <a name="block-suggestions"></a>Blockera förslag

Om du inkluderar Bing-förslag kan du lägga till en lista med Sök fråga-strängar som du inte vill att Bing ska returnera. Om du vill lägga till blockerade frågesträngar klickar du på **Visa blockerade förslag**. Lägg till frågesträngen i listan och tryck på RETUR eller klicka på **+** ikonen. Du kan lägga till högst 50 blockerade frågesträngar.



[!INCLUDE [publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Det kan ta upp till 24 timmar innan anpassade automatiska förslag på konfigurations ändringar börjar gälla.


## <a name="enabling-autosuggest-in-hosted-ui"></a>Aktivera automatiska förslag i värdbaserade gränssnitt

Om du vill aktivera fråge Strängs förslag för ditt värdbaserade användar gränssnitt klickar du på **VÄRDBASERAD gränssnitt**. Rulla ned till avsnittet **ytterligare konfiguration** . Under **Webbs ökning** väljer du **på** för **Aktivera automatiska förslag**. Om du vill aktivera automatiska förslag måste du välja en layout som innehåller en sökruta.


## <a name="calling-the-autosuggest-api"></a>Anropar API: et för automatiska förslag

Skicka en `GET` begäran till följande slut punkt för att få förslag på frågesträngar med hjälp av API för anpassad Bing-sökning.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

Svaret innehåller en lista med `SearchAction` objekt som innehåller de föreslagna frågesträngarna.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Varje förslag innehåller `displayText` fältet och `query` . `displayText`Fältet innehåller den föreslagna frågesträngen som du använder för att fylla i Sök rutans listruta.

Om användaren väljer en föreslagen frågesträng från List rutan använder du frågesträngen i `query` fältet när du anropar [API för anpassad Bing-sökning](overview.md).


## <a name="next-steps"></a>Nästa steg

- [Få anpassade förslag]()
- [Sök i den anpassade instansen](./search-your-custom-view.md)
- [Konfigurera och använda anpassat värdbaserade gränssnitt](./hosted-ui.md)