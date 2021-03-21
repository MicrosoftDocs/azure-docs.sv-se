---
title: Kognitiv kunskap om entitets igenkänning
titleSuffix: Azure Cognitive Search
description: Extrahera olika typer av entiteter från text i en pipeline för anrikning i Azure Kognitiv sökning.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 3300d68c6356cb7cdd56a9ad79eb0ac011942eb2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547908"
---
#    <a name="entity-recognition-cognitive-skill"></a>Kognitiv kunskap om entitets igenkänning

Kunskapen om **enhets igenkänning** extraherar entiteter av olika typer från text. Den här kunskapen använder Machine Learning-modeller som tillhandahålls av [textanalys](../cognitive-services/text-analytics/overview.md) i Cognitive Services.

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [koppla en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifterna påförs när API: er anropas i Cognitive Services, och för avbildnings extrahering som en del av stadiet för dokument sprickor i Azure Kognitiv sökning. Det finns inga kostnader för text extrahering från dokument.
>
> Körningen av inbyggda kunskaper debiteras enligt den befintliga [Cognitive Services betala per](https://azure.microsoft.com/pricing/details/cognitive-services/)användning-pris. Priser för avbildnings extrahering beskrivs på [sidan med priser för Azure kognitiv sökning](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft. färdigheter. text. EntityRecognitionSkill

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken för en post ska vara 50 000 tecken som mäts av [`String.Length`](/dotnet/api/system.string.length) . Om du behöver dela upp dina data innan du skickar dem till nyckel frasen Extractor, bör du överväga att använda [text delnings kunskapen](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Kunskaps parametrar

Parametrar är Skift läges känsliga och alla är valfria.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| `categories`    | Matris med kategorier som ska extraheras.  Möjliga kategori typer:,,,,, `"Person"` `"Location"` `"Organization"` `"Quantity"` `"Datetime"` `"URL"` , `"Email"` . Om ingen kategori anges returneras alla typer.|
| `defaultLanguageCode` |    Språk koden för inmatad text. Följande språk stöds: `ar, cs, da, de, en, es, fi, fr, hu, it, ja, ko, nl, no, pl, pt-BR, pt-PT, ru, sv, tr, zh-hans` . Alla enhets kategorier stöds inte för alla språk. se kommentaren nedan.|
| `minimumPrecision` | Ett värde mellan 0 och 1. Om förtroende poängen (i `namedEntities` utdata) är lägre än det här värdet returneras inte entiteten. Standardvärdet är 0. |
| `includeTypelessEntities` | Ange till `true` om du vill känna igen välkända entiteter som inte passar de aktuella kategorierna. Identifierade entiteter returneras i `entities` fältet komplext utdata. Till exempel är "Windows 10" en välkänd entitet (en produkt), men eftersom "produkter" inte är en kategori som stöds, skulle den här enheten inkluderas i fältet entiteter-utdata. Standard är `false` |


## <a name="skill-inputs"></a>Kompetens inmatningar

| Inmatat namn      | Beskrivning                   |
|---------------|-------------------------------|
| `languageCode`    | Valfritt. Standardvärdet är `"en"`.  |
| `text`          | Den text som ska analyseras.          |

## <a name="skill-outputs"></a>Kunskaps utmatningar

> [!NOTE]
> Alla enhets kategorier stöds inte för alla språk. `"Person"` `"Location"` Kategori typerna, och `"Organization"` för entiteter stöds för den fullständiga listan över språk ovan. Endast _de_, _en_, _es_, _fr_ och _zh-hans_ stöder extrahering av `"Quantity"` , `"Datetime"` , `"URL"` , och `"Email"` typer. Mer information finns i [språk-och region stöd för API för textanalys](../cognitive-services/text-analytics/language-support.md).  

| Namn på utdata      | Beskrivning                   |
|---------------|-------------------------------|
| `persons`       | En sträng mat ris där varje sträng representerar namnet på en person. |
| `locations`  | En sträng mat ris där varje sträng representerar en plats. |
| `organizations`  | En sträng mat ris där varje sträng representerar en organisation. |
| `quantities`  | En sträng mat ris där varje sträng representerar en kvantitet. |
| `dateTimes`  | En sträng mat ris där varje sträng representerar ett DateTime-värde (som det visas i texten). |
| `urls` | En sträng mat ris där varje sträng representerar en URL |
| `emails` | En sträng mat ris där varje sträng representerar ett e-postmeddelande |
| `namedEntities` | En matris med komplexa typer som innehåller följande fält: <ul><li>category</li> <li>värde (namnet på den faktiska entiteten)</li><li>offset (platsen där den hittades i texten)</li><li>förtroende (högre värde innebär att det är mer att vara en riktig entitet)</li></ul> |
| `entities` | En matris med komplexa typer som innehåller omfattande information om de entiteter som extraheras från text, med följande fält <ul><li> namn (namnet på den faktiska entiteten. Detta representerar en "normaliserad" form)</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (en länk till Wikipedia-sidan för entiteten)</li><li>bingId</li><li>typ (den enhets kategori som identifieras)</li><li>Undertyp (endast tillgängligt för vissa kategorier, detta ger en mer detaljerad vy av entitetstypen)</li><li> matchningar (en komplex samling som innehåller)<ul><li>text (den råa texten för entiteten)</li><li>förskjutning (platsen där den hittades)</li><li>längd (längden på texten i den obearbetade entiteten)</li></ul></li></ul> |

##    <a name="sample-definition"></a>Exempel definition

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "minimumPrecision": 0.5,
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
      }
    ]
  }
```
##    <a name="sample-input"></a>Exempelindata

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Exempelutdata

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": 0.98
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```

Observera att förskjutningarna som returneras för entiteter i resultatet av den här kunskapen returneras direkt från [API för textanalys](../cognitive-services/text-analytics/overview.md), vilket innebär att om du använder dem för att indexera till den ursprungliga strängen, bör du använda [StringInfo](/dotnet/api/system.globalization.stringinfo) -klassen i .net för att extrahera rätt innehåll.  [Mer information hittar du här.](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="warning-cases"></a>Varnings fall
Om språk koden för dokumentet inte stöds returneras en varning och inga entiteter extraheras.

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)