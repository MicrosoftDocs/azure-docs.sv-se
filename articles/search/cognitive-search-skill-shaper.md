---
title: Kognitiv formarfärdighet
titleSuffix: Azure Cognitive Search
description: Extrahera metadata och strukturerad information från ostrukturerade data och forma den som en komplex typ i en pipeline för AI-anrikning i Azure Kognitiv sökning.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 81eb0e60befc544a6c3bee8f04e901b6a5e472bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85560827"
---
# <a name="shaper-cognitive-skill"></a>Kognitiv formarfärdighet

**Formaren** -kompetensen konsoliderar flera indata till en [komplex typ](search-howto-complex-data-types.md) som kan refereras till senare i pipelinen för anrikning. Med **formaren** -kompetensen kan du i princip skapa en struktur, definiera namnet på medlemmarna i strukturen och tilldela värden till varje medlem. Exempel på konsoliderade fält som är användbara i Sök scenarier är att kombinera ett förnamn och efter namn i en enda struktur, stad och stat till en enda struktur, eller namn och födelse datum i en enda struktur för att upprätta unik identitet.

Dessutom lägger **formaren** -kunskapen som illustreras i [Scenario 3](#nested-complex-types) till en valfri *sourceContext* -egenskap i indatamängden. Egenskaperna *Source* och *sourceContext* kan inte anges samtidigt. Om indatamängden är i kontexten för kunskapen använder du bara *källa*. Om indatatypen är i ett *annat* sammanhang än kunskaps kontexten använder du *sourceContext*. *SourceContext* kräver att du definierar en kapslad indatamängd med det angivna elementet som ska hanteras som källa. 

Utdatafilens namn är alltid "output". I ett internt sätt kan pipelinen mappa ett annat namn, till exempel "analyzedText" som visas i exemplen nedan, men **formaren** -skickligheten returnerar "output" i svaret. Detta kan vara viktigt om du felsöker omfattande dokument och märker namngivnings avvikelsen, eller om du skapar en anpassad färdighet och strukturerar svaret själv.

> [!NOTE]
> **Formaren** -kompetensen är inte kopplad till ett COGNITIVE Services-API och du debiteras inte för att använda den. Du bör fortfarande [bifoga en Cognitive Services resurs](cognitive-search-attach-cognitive-services.md), men för att åsidosätta det **kostnads fria** resurs alternativet som begränsar dig till ett litet antal dagliga berikare per dag.

## <a name="odatatype"></a>@odata.type  
Microsoft. färdigheter. util. ShaperSkill

## <a name="scenario-1-complex-types"></a>Scenario 1: komplexa typer

Tänk dig ett scenario där du vill skapa en struktur med namnet *analyzedText* som har två medlemmar: *text* respektive *sentiment*. I ett index kallas ett sökbart fält med flera delar som en *komplex typ* och det skapas ofta när källdata har en motsvarande komplex struktur som mappar till den.

En annan metod för att skapa komplexa typer är dock genom **formaren** -kompetensen. Genom att inkludera den här kunskapen i en färdigheter, kan åtgärder i minnet under färdigheter-bearbetningen mata ut data former med kapslade strukturer, som sedan kan mappas till en komplex typ i indexet. 

Följande exempel på kunskaps definition ger medlems namnen som inmatade. 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>Exempel index

En färdigheter anropas av en indexerare och en indexerare kräver ett index. En komplex fälts representation i indexet kan se ut som i följande exempel. 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>Kompetens ingång

Ett inkommande JSON-dokument som tillhandahåller användbara ininformation för den här **formaren** -kompetensen kan vara:

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="skill-output"></a>Kompetens utmatning

**Formaren** -kompetensen genererar ett nytt element med namnet *analyzedText* med de kombinerade elementen *text* och *sentiment*. Dessa utdata följer index schemat. Den importeras och indexeras i ett Azure Kognitiv sökning-index.

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="scenario-2-input-consolidation"></a>Scenario 2: inmatad konsolidering

I ett annat exempel kan du anta att du har extraherat titeln på en bok och kapitel rubriker på olika sidor i boken. Nu kan du skapa en enda struktur som består av dessa olika indata.

**Formaren** kompetens definition för det här scenariot kan se ut som i följande exempel:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Kompetens utmatning
I det här fallet fören klar **formaren** alla kapitel rubriker för att skapa en enda matris. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Scenario 3: indatamängds konsolidering från kapslade kontexter

Tänk dig att du har rubriken, kapitlen och innehållet i en bok och att du har kört enhets igenkänning och nyckel fraser på innehållet. nu måste du samla in resultat från de olika färdigheterna i en enda form med kapitel namn, entiteter och nyckel fraser.

**Formaren** kompetens definition för det här scenariot kan se ut som i följande exempel:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Kompetens utmatning
I det här fallet skapar **formaren** en komplex typ. Den här strukturen finns i minnet. Om du vill spara den i ett [kunskaps lager](knowledge-store-concept-intro.md)bör du skapa en projektion i färdigheter som definierar lagrings egenskaper.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Använda komplexa typer](search-howto-complex-data-types.md)
+ [Knowledge Store](knowledge-store-concept-intro.md)
+ [Skapa ett kunskaps lager i REST](knowledge-store-create-rest.md)