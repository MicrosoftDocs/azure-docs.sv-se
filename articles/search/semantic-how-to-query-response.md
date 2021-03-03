---
title: Strukturera ett semantiskt svar
titleSuffix: Azure Cognitive Search
description: Beskriver algoritmen för semantisk rankning i Kognitiv sökning och hur du strukturerar semantiska svar och semantiska bild texter från en resultat uppsättning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: febbfd0f3def8e681107ef78d9478463b1c2a872
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680401"
---
# <a name="semantic-ranking-and-responses-in-azure-cognitive-search"></a>Semantisk rangordning och svar i Azure Kognitiv sökning

> [!IMPORTANT]
> Algoritmen för semantisk rangordning och semantiska svar/under texter finns i offentlig för hands version, endast tillgängligt via för hands versionen REST API. För hands versions funktionerna erbjuds i befintligt skick under [kompletterande användnings villkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Semantisk rangordning förbättrar precisionen för Sök resultaten genom att rangordna de främsta matchningarna med hjälp av en semantisk ranknings modell som är utbildad för frågor som uttrycks på naturligt språk, till skillnad från nyckelord.

Den här artikeln beskriver algoritmen för semantisk rangordning och hur ett semantiskt svar är formad. Ett svar innehåller under texter, både i oformaterad text och med högdagrar och svar (valfritt).

+ Semantiska under texter är text som är relevanta för den fråga som extraheras från Sök resultaten. De kan hjälpa till att sammanfatta ett resultat när enskilda innehålls fält är för stora för resultat sidan. Under texter har semantiska höjd punkter, vilket gör det möjligt för användare att snabbt Skim frågeresultaten för att hitta de mest relevanta dokumenten och därigenom förbättra den övergripande användar upplevelsen

+ Semantiska svar använder Machine Learning-modeller från Bing för att formulera svar på frågor som ser ut som frågor. Svaren väljs från en lista över de som är mest relevanta för frågan, som extraheras från de översta dokumenten i frågeresultatet. Svar returneras som ett oberoende objekt på den översta nivån i svars nytto lasten för frågan som du kan välja att återge på Sök sidorna, samtidigt som Sök resultaten på sidan.

## <a name="prerequisites"></a>Förutsättningar

+ Frågor som formuleras med typen semantisk fråga. Mer information finns i [skapa en semantisk fråga](semantic-how-to-query-request.md).

## <a name="understanding-a-semantic-response"></a>Förstå ett semantiskt svar

Ett semantiskt svar innehåller nya egenskaper för poäng, under texter och svar. Ett semantiskt svar bygger på standardsvaret, med hjälp av de översta 50 resultaten som returnerades av [full texts öknings motorn](search-lucene-query-architecture.md), som sedan omrankas med semantisk rangation. Om fler än 50 anges returneras ytterligare resultat, men de blir inte semantiskt omrankade.

Som med alla frågor består ett svar av alla fält som har marker ATS som hämtnings bara eller bara de fält som anges i SELECT-instruktionen. Det innehåller också ett "svar"-fält och "bild texter".

+ För varje semantiskt resultat finns det ett "svar" som standard och returneras som ett distinkt fält som du kan välja att återge på en Sök sida. Du kan ange upp till fem. Formulering av svar är automatiserat: läsa igenom alla dokument i de första resultaten, köra extraherings sammanfattning, följt av maskin läsnings förståelse och slutligen befordra ett direkt svar till användarens fråga i fältet svar.

+ En "rubrik" är en extraherings-baserad Sammanfattning av dokument innehåll, som returneras som oformaterad text eller med högdagrar. Under texter tas med automatiskt och kan inte undertryckas. Högdagrar tillämpas med hjälp av maskin läsnings förståelse för att identifiera vilka strängar som ska framhävas. Högdagrar drar uppmärksamheten till de mest relevanta resultaten, så att du snabbt kan söka igenom en sida med resultat för att hitta rätt dokument.

En semantiskt omrankad resultat uppsättnings ordning med @search.rerankerScore värdet. Om du lägger till en OrderBy-sats returneras ett HTTP 400-fel eftersom satsen inte stöds i en semantisk fråga.

## <a name="example"></a>Exempel

@search.rerankerScoreFinns tillsammans med standard @search.score och används för att rangordna resultaten igen.

Fått följande fråga:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "answers": "none",
    "searchFields": "HotelName,Category,Description",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

Du kan vänta på att se följande resultat, som är representativa för ett semantiskt svar. Dessa resultat visar bara de tre översta svaren som rangordnas av " @search.rerankerScore ". Observera att Oceanside-utväg nu rangordnas först. Under standard rangordningen för " @search.score " returneras det här resultatet på andra platsen efter slutet av slutet.

```http
{
    "@odata.count": 31,
    "@search.answers": [],
    "value": [
        {
            "@search.score": 1.8920634,
            "@search.rerankerScore": 1.1091284966096282,
            "@search.captions": [
                {
                    "text": "Oceanside Resort. Budget. New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
                    "highlights": "<em>Oceanside Resort.</em> Budget. New Luxury Hotel.  Be the first to stay.<em> Bay views</em> from every room, location near the piper, rooftop pool, waterfront dining & more."
                }
            ],
            "HotelId": "18",
            "HotelName": "Oceanside Resort",
            "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
            "Category": "Budget"
        },
        {
            "@search.score": 2.5204072,
            "@search.rerankerScore": 1.0731962407007813,
            "@search.captions": [
                {
                    "text": "Trails End Motel. Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
                    "highlights": "<em>Trails End Motel.</em> Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
                }
            ],
            "HotelId": "40",
            "HotelName": "Trails End Motel",
            "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
            "Category": "Luxury"
        },
        {
            "@search.score": 1.4104348,
            "@search.rerankerScore": 1.06992666143924,
            "@search.captions": [
                {
                    "text": "Winter Panorama Resort. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
                    "highlights": "<em>Winter Panorama Resort</em>. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs."
                }
            ],
            "HotelId": "12",
            "HotelName": "Winter Panorama Resort",
            "Description": "Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
            "Category": "Resort and Spa"
        }
```

## <a name="next-steps"></a>Nästa steg

+ [Översikt över semantisk sökning](semantic-search-overview.md)
+ [Likhetsalgoritm](index-ranking-similarity.md)
+ [Skapa en semantisk fråga](semantic-how-to-query-request.md)
+ [Skapa en grundläggande fråga](search-query-create.md)