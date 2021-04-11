---
title: Returnera ett semantiskt svar
titleSuffix: Azure Cognitive Search
description: Beskriver sammansättningen av ett semantiskt svar och hur du får svar från en resultat uppsättning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 9bb62544887e0bc0269b98cd98fbf97fc477352f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104722437"
---
# <a name="return-a-semantic-answer-in-azure-cognitive-search"></a>Returnera ett semantiskt svar i Azure Kognitiv sökning

> [!IMPORTANT]
> Semantisk sökning är i offentlig för hands version, endast tillgängligt via för hands versionen REST API. För hands versions funktionerna erbjuds i befintligt skick, under [kompletterande användnings villkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)och är inte garanterade att ha samma implementering vid allmän tillgänglighet. Dessa funktioner är fakturerbara. Mer information finns i [tillgänglighet och priser](semantic-search-overview.md#availability-and-pricing).

När du utvinner en [semantisk fråga](semantic-how-to-query-request.md)kan du välja att extrahera innehåll från de översta matchande dokumenten som "svarar" på frågan direkt. Ett eller flera svar kan inkluderas i svaret, som du sedan kan återge på en Sök sida för att förbättra appens användar upplevelse.

I den här artikeln får du lära dig hur du begär ett semantiskt svar, packar upp svaret och lär dig mer om vilka innehålls egenskaper som är mest effektiv för att producera svar med hög kvalitet.

## <a name="prerequisites"></a>Förutsättningar

Alla krav som gäller för [semantiska frågor](semantic-how-to-query-request.md) gäller även för svar, inklusive tjänst nivå och region.

+ Frågans logik måste innehålla parametrarna för semantisk fråga, plus parametern "svar". Obligatoriska parametrar beskrivs i den här artikeln.

+ Frågesträngar som anges av användaren måste formuleras i språk som har egenskaperna för en fråga (vad, var, när, hur).

+ Sök efter dokument måste innehålla text med egenskaperna för ett svar och texten måste finnas i något av fälten som anges i "searchFields". Till exempel kan en fråga "Vad är en hash-tabell", om ingen av searchFields innehåller passager som innehåller "en hash-tabell är...", så är det osannolikt att ett svar returneras.

## <a name="what-is-a-semantic-answer"></a>Vad är ett semantiskt svar?

Ett semantiskt svar är en under struktur till ett [semantiskt fråge svar](semantic-how-to-query-request.md). Det består av en eller flera orda Grant-passager från ett sökdokument, formulerat som ett svar på en fråga som ser ut som en fråga. För att ett svar ska returneras måste det finnas fraser eller meningar i ett sökdokument som har språk egenskaperna för ett svar, och själva frågan måste vara en fråga.

Kognitiv sökning använder en maskin läsnings förståelse modell för att välja det bästa svaret. Modellen skapar en uppsättning möjliga svar från det tillgängliga innehållet och när den når en hög tillräckligt konfidensnivå, föreslår den ett svar.

Svar returneras som ett oberoende objekt på den översta nivån i svars nytto lasten för frågan som du kan välja att återge på Sök sidor, samtidigt som Sök Resultat från sida. Strukturellt är det ett mat ris element i svaret som består av text, en dokument nyckel och en säkerhets poäng.

<a name="query-params"></a>

## <a name="how-to-request-semantic-answers-in-a-query"></a>Så här begär du semantiska svar i en fråga

Om du vill returnera ett semantiskt svar måste frågan ha semantisk "queryType", "queryLanguage", "searchFields" och parametern "Answers". Att ange parametern "svar" garanterar inte att du får ett svar, men begäran måste innehålla den här parametern om svars bearbetningen ska anropas alls.

Parametern "searchFields" är avgörande för att returnera ett svar med hög kvalitet, både i termer av innehåll och ordning (se nedan). 

```json
{
    "search": "how do clouds form",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "title,locations,content",
    "answers": "extractive|count-3",
    "count": "true"
}
```

+ En frågesträng får inte vara null och bör formuleras som fråga. I den här för hands versionen måste "queryType" och "queryLanguage" anges exakt som visas i exemplet.

+ Parametern "searchFields" avgör vilka sträng fält som tillhandahåller token till extraherings modellen. Samma fält som skapar under texter ger också svar. Mer information om hur du ställer in det här fältet så att det fungerar för både bild texter och svar finns i [set searchFields](semantic-how-to-query-request.md#searchfields). 

+ För "svar" är parameter konstruktion `"answers": "extractive"` , där standard antalet returnerade svar är ett. Du kan öka antalet svar genom att lägga till ett antal som visas i exemplet ovan, högst fem.  Om du behöver mer än ett svar beror på användarens upplevelse av appen och hur du vill återge resultaten.

## <a name="deconstruct-an-answer-from-the-response"></a>Dekonstruera ett svar från svaret

Svar finns i @search.answers matrisen, som visas först i svaret. Om ett svar är obestämt visas svaret som `"@search.answers": []` . När du skapar en Sök resultat sida som innehåller svar ska du se till att hantera fall där inga svar hittas.

I @search.answers är "nyckeln" dokument nyckeln eller ID: t för matchningen. Med en dokument nyckel kan du använda sökverktygets API för att hämta alla eller alla delar av [Sök dokumentet som](/rest/api/searchservice/lookup-document) ska tas med på Sök sidan eller på en informations sida.

Både "text" och "högdagrar" ger identiskt innehåll i både oformaterad text och med högdagrar. Som standard formateras högdagrar som `<em>` , som du kan åsidosätta med hjälp av de befintliga parametrarna highlightPreTag och highlightPostTag. Som anges någon annan stans är ämnet i ett svar orda Grant innehåll från ett sökdokument. Extraherings modellen söker efter egenskaper för ett svar för att hitta lämpligt innehåll, men skapar inte ett nytt språk i svaret.

"Poängen" är ett säkerhets resultat som återspeglar svarets styrka. Om det finns flera svar i svaret används den här poängen för att fastställa ordningen. Främsta svar och de översta rubrikerna kan härledas från olika Sök dokument, där det främsta svaret kommer från ett dokument och den översta rubriken från ett annat, men i allmänhet ser du samma dokument i de översta positionerna i varje matris.

Svaren följs av "value"-matrisen, som alltid innehåller resultat, under texter och fält som kan hämtas som standard. Om du har angett parametern Select är "värde"-matrisen begränsad till de fält som du har angett. Mer information om objekt i svaret finns i [skapa en semantisk fråga](semantic-how-to-query-request.md).

Med tanke på frågan "Hur gör jag moln", returneras följande svar i svaret:

```json
{
    "@search.answers": [
        {
            "key": "4123",
            "text": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form where air is ascending (over land in this case),   but not where it is descending (over the river).",
            "highlights": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form<em> where air is ascending</em> (over land in this case),   but not where it is<em> descending</em> (over the river).",
            "score": 0.94639826
        }
    ],
    "value": [
        {
            "@search.score": 0.5479723,
            "@search.rerankerScore": 1.0321671911515296,
            "@search.captions": [
                {
                    "text": "Like all clouds, it forms when the air reaches its dew point—the temperature at which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley fog, which is common in the Pacific Northwest of North America.",
                    "highlights": "Like all<em> clouds</em>, it<em> forms</em> when the air reaches its dew point—the temperature at    which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley<em> fog</em>, which is common in the Pacific Northwest of North America."
                }
            ],
            "title": "Earth Atmosphere",
            "content": "Fog is essentially a cloud lying on the ground. Like all clouds, it forms when the air reaches its dew point—the temperature at  \n\nwhich an air mass is cool enough for its water vapor to condense into liquid droplets.\n\nThis false-color image shows valley fog, which is common in the Pacific Northwest of North America. On clear winter nights, the \n\nground and overlying air cool off rapidly, especially at high elevations. Cold air is denser than warm air, and it sinks down into the \n\nvalleys. The moist air in the valleys gets chilled to its dew point, and fog forms. If undisturbed by winds, such fog may persist for \n\ndays. The Terra satellite captured this image of foggy valleys northeast of Vancouver in February 2010.\n\n\n",
            "locations": [
                "Pacific Northwest",
                "North America",
                "Vancouver"
            ]
        }
```

## <a name="tips-for-producing-high-quality-answers"></a>Tips för att producera svar med hög kvalitet

För bästa resultat kan du returnera semantiska svar för ett dokument sökkorpus med följande egenskaper:

+ "searchFields" måste innehålla fält som erbjuder tillräckligt med text där ett svar förmodligen kan hittas. Endast orda Grant text från ett dokument kan visas som ett svar.

+ frågesträngar får inte vara null (search = `*` ) och strängen ska ha egenskaperna för en fråga, till skillnad från en nyckelords sökning (en sekventiell lista med valfria uttryck eller fraser). Om frågesträngen inte verkar vara svar hoppas svars bearbetningen över, även om begäran anger "svar" som en frågeparameter.

+ Semantisk extrahering och sammanfattning har gränser över hur många tokens per dokument som kan analyseras inom rimlig tid. Om du har stora dokument som körs på hundratals sidor i praktiska termer bör du försöka dela upp innehållet i mindre dokument först.

## <a name="next-steps"></a>Nästa steg

+ [Översikt över semantisk sökning](semantic-search-overview.md)
+ [Algoritm för semantisk rangordning](semantic-ranking.md)
+ [Algoritm för rangordning av likhets sätt](index-ranking-similarity.md)
+ [Skapa en semantisk fråga](semantic-how-to-query-request.md)