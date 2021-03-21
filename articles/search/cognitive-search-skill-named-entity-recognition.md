---
title: Kognitiv kompetens för entitets igenkänning
titleSuffix: Azure Cognitive Search
description: Extrahera namngivna entiteter för person, plats och organisation från text i en AI-pipeline i Azure Kognitiv sökning.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e598f16c6b441cf986c7ac82d67c037f75be8982
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547449"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Kognitiv kompetens för entitets igenkänning

Den **namngivna Recognition** -kompetensen extraherar namngivna entiteter från text. Tillgängliga entiteter innehåller typerna `person` `location` och `organization` .

> [!IMPORTANT]
> Den namngivna kunskapen om enhets igenkänning har nu ersatts av [Microsoft. skicklighets. text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md). Supporten stoppades den 15 februari 2019 och API: et togs bort från produkten den 2 maj 2019. Följ rekommendationerna i [inaktuella kognitiva Sök kunskaper](cognitive-search-skill-deprecated.md) för att migrera till en färdighet som stöds.

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [koppla en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifterna påförs när API: er anropas i Cognitive Services, och för avbildnings extrahering som en del av stadiet för dokument sprickor i Azure Kognitiv sökning. Det finns inga kostnader för text extrahering från dokument.
>
> Körningen av inbyggda kunskaper debiteras enligt den befintliga [Cognitive Services betala per](https://azure.microsoft.com/pricing/details/cognitive-services/)användning-pris. Priser för avbildnings extrahering beskrivs på [sidan med priser för Azure kognitiv sökning](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft. färdigheter. text. NamedEntityRecognitionSkill

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken för en post ska vara 50 000 tecken som mäts av [`String.Length`](/dotnet/api/system.string.length) . Om du behöver dela upp dina data innan du skickar dem till nyckel frasen Extractor, bör du överväga att använda [text delnings kunskapen](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Kunskaps parametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| kategorier    | Matris med kategorier som ska extraheras.  Möjliga kategori typer: `"Person"` , `"Location"` , `"Organization"` . Om ingen kategori anges returneras alla typer.|
|defaultLanguageCode |  Språk koden för inmatad text. Följande språk stöds: `de, en, es, fr, it`|
| minimumPrecision  | Ett tal mellan 0 och 1. Om precisionen är lägre än det här värdet returneras inte entiteten. Standardvärdet är 0.|

## <a name="skill-inputs"></a>Kompetens inmatningar

| Inmatat namn      | Beskrivning                   |
|---------------|-------------------------------|
| languageCode  | Valfritt. Standardvärdet är `"en"`.  |
| text          | Den text som ska analyseras.          |

## <a name="skill-outputs"></a>Kunskaps utmatningar

| Namn på utdata     | Beskrivning                   |
|---------------|-------------------------------|
| ansvariga      | En sträng mat ris där varje sträng representerar namnet på en person. |
| platser  | En sträng mat ris där varje sträng representerar en plats. |
| organisationer  | En sträng mat ris där varje sträng representerar en organisation. |
| poster | En matris med komplexa typer. Varje komplex typ innehåller följande fält: <ul><li>kategori ( `"person"` , `"organization"` eller `"location"` )</li> <li>värde (namnet på den faktiska entiteten)</li><li>offset (platsen där den hittades i texten)</li><li>förtroende (ett värde mellan 0 och 1 som representerar det förtroende att värdet är en faktisk entitet)</li></ul> |

##  <a name="sample-definition"></a>Exempel definition

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person", "Location", "Organization"],
    "defaultLanguageCode": "en",
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
      }
    ]
  }
```
##  <a name="sample-input"></a>Exempelindata

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "This is the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia… Ana Smith is provided as a reference.",
             "languageCode": "en"
           }
      }
    ]
}
```

##  <a name="sample-output"></a>Exempelutdata

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "Joe Romero", "Ana Smith"],
        "locations": ["Chile", "Australia"],
        "organizations":["Microsoft"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 33,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 124,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Chile",
            "offset": 88,
            "confidence": 0.99
          },
          {
            "category":"location",
            "value": "Australia",
            "offset": 112,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft",
            "offset": 54,
            "confidence": 0.99
          }
        ]
      }
    }
  ]
}
```


## <a name="warning-cases"></a>Varnings fall
Om språk koden för dokumentet inte stöds returneras en varning och inga entiteter extraheras.

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Kompetens för enhets igenkänning](cognitive-search-skill-entity-recognition.md)