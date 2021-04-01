---
title: Sök efter kategorier för API för lokal sökning i Bing
titleSuffix: Azure Cognitive Services
description: I den här artikeln får du lära dig hur du anger Sök kategorier för API-slutpunkt för lokal sökning i Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 19b769d1fff431f95c20e607c17747f2ff483d2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96487192"
---
# <a name="search-categories-for-the-bing-local-business-search-api"></a>Sök efter kategorier för API för lokal sökning i Bing

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Med API: t för lokal sökning i Bing kan du söka efter lokala affär senheter i en mängd olika kategorier, med prioritet till resultat som stänger en användares plats. Du kan inkludera dessa sökningar i sökningar tillsammans med `localCircularView` `localMapView` [parametrarna](specify-geographic-search.md)och.


## <a name="toplevel-categories"></a>TopLevel-kategorier 

Följande typer definierar viktiga Sök kategorier.  Mer än en kategori kan anges med en kommaavgränsad lista som har tilldelats till `localCategories` parametern.  
- EatDrink 
- SeeDing 
- Kafé 
- HotelsAndMotels 
- BanksAndCreditUnions 
- Parkering 
- Sjukhus 

## <a name="sub-categories"></a>Under Kategorier
Under Kategorier skickas på samma sätt som `localCategories` . Under kategorier är mer exakta kategorier. De är underordnade i den meningen att om du anger en kategori C och en av dess under Kategorier i samma kommaavgränsad lista får du samma resultat som om du har angett enbart C.

### <a name="eat-drink"></a>Äta dryck

> BreweriesAndBrewPubs, CocktailLounges, AfricanRestaurants, AmericanRestaurants, bagels, BarbecueRestaurants, Taverns, SportsBars, barer, BarsGrillsAndPubs, BuffetRestaurants | BelgianRestaurants, BritishRestaurants, CafeRestaurants, CaribbeanRestaurants, ChineseRestaurants, CoffeeAndTea, Delicatessens, DeliveryService, Diners, DiscountStores, Donuts, FastFood, FrenchRestaurants, FrozenYogurt, GermanRestaurants, supermarknadar, GreekRestaurants, livsmedels varor, HawaiianRestaurants, HungarianRestaurants, IceCreamAndFrozenDesserts, IndianRestaurants, ItalianRestaurants, JapaneseRestaurants, juice, KoreanRestaurants, LiquorStores, MexicanRestaurants, MiddleEasternRestaurants, pizza, PolishRestaurants, PortugueseRestaurants, pretzels, RussianAndUkrainianRestaurants, SeafoodRestaurants, SpanishRestaurants, SteakHouseRestaurants, SushiRestaurants, sak, ThaiRestaurants, TurkishRestaurants, VegetarianAndVeganRestaurants, VietnameseRestaurants,,,,

### <a name="see-do"></a>Se gör

> AmusementParks, Attractions, Carnivals, Casinos, LandmarksAndHistoricalSites, MiniatureGolfCourses, MovieTheaters, Museums, Parkes, SightseeingTours, TouristInformation, zoos

### <a name="shop"></a>Kafé

> AntiqueStores, böcker, CDAndRecordStores, ChildrensClothingStores, CigarAndTobaccoShops, ComicBookStores, DepartmentStores, DiscountStores, FleaMarketsAndBazaars, FurnitureStores, HomeImprovementStores, JewelryAndWatchesStores, KitchenwareStores, LiquorStores, MallsAndShoppingCenters, MensClothingStores, MusicStores, OutletStores, PetShops, PetSupplyStores, SchoolAndOfficeSupplyStores, ShoeStores, SportingGoodsStores, ToyAndGameStores, VitaminAndSupplementStores, WomensClothingStores,,,,


## <a name="examples-of-local-categories-search"></a>Exempel på lokala Sök kategorier

Följande exempel får resultat enligt `localCategories` parametern:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=HotelsAndMotels`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=EatDrink`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Shop`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Hospitals`

Följande fråga begränsar antalet "sjukhus"-resultat till de första tre returnerade från Bing-API: et för lokal affärs sökning:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localCategories=Hospitals&count=3&offset=0`

I följande exempel på JSON-svar ingår tre sjukhus i den större Seattle-ytan:

```json
BingAPIs-TraceId: 68AFB51807C6485CAB8AAF20E232EFFF
BingAPIs-SessionId: F89E7B8539B34BF58AAF811485E83B20
X-MSEdge-ClientID: 1C44E64DBFAA6BCA1270EADDBE7D6A22
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 68AFB51807C6485CAB8AAF20E232EFFF Ref B: CO1EDGE0108 Ref C: 2018-10-22T18:52:28Z

{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": ""
   },
   "places": {
      "readLink": "https:\/\/www.bingapis.com\/api\/v7\/places\/search?q=",
      "totalEstimatedMatches": 0,
      "value": [
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.0",
            "name": "Overlake Hospital Medical Center",
            "url": "http:\/\/www.overlakehospital.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6204986572266,
               "longitude": -122.185829162598
            },
            "routablePoint": {
               "latitude": 47.6204986572266,
               "longitude": -122.185668945312
            },
            "address": {
               "streetAddress": "1035 116th Ave NE",
               "addressLocality": "Bellevue",
               "addressRegion": "WA",
               "postalCode": "98004",
               "addressCountry": "US",
               "neighborhood": "",
               "text": "1035 116th Ave NE, Bellevue, WA, 98004"
            },
            "telephone": "(425) 688-5000"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.1",
            "name": "Virginia Mason University Village Medical Center",
            "url": "https:\/\/virginiamason.org\/Seattle",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6095390319824,
               "longitude": -122.327941894531
            },
            "routablePoint": {
               "latitude": 47.6093978881836,
               "longitude": -122.328277587891
            },
            "address": {
               "streetAddress": "1100 9th Ave",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98101",
               "addressCountry": "US",
               "neighborhood": "University District",
               "text": "1100 9th Ave, Seattle, WA, 98101"
            },
            "telephone": "(206) 223-6600"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.2",
            "name": "Seattle Children’s Hospital",
            "url": "http:\/\/www.seattlechildrens.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6625061035156,
               "longitude": -122.283760070801
            },
            "routablePoint": {
               "latitude": 47.6631507873535,
               "longitude": -122.284614562988
            },
            "address": {
               "streetAddress": "4800 Sand Point Way NE",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98105",
               "addressCountry": "US",
               "neighborhood": "Laurelhurst",
               "text": "4800 Sand Point Way NE, Seattle, WA, 98105"
            },
            "telephone": "(206) 987-2000"
         }
      ],
      "searchAction": {

      }
   }
}
```

## <a name="next-steps"></a>Nästa steg
- [Geografiska Sök gränser](specify-geographic-search.md)
- [Fråga och svar](local-search-query-response.md)
- [Snabb start i C #](quickstarts/local-quickstart.md)