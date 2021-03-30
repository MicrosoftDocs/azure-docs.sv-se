---
title: Gränssnitts definition för anpassade kunskaper
titleSuffix: Azure Cognitive Search
description: Anpassat data extraherings gränssnitt för anpassad kunskap om webb-API i en AI-pipeline i Azure Kognitiv sökning.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: e78f0d1e8d6d637dfebe1ff475ab8416ba49a263
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88935423"
---
# <a name="how-to-add-a-custom-skill-to-an-azure-cognitive-search-enrichment-pipeline"></a>Så här lägger du till en anpassad färdighet i en Azure Kognitiv sökning-rikare pipeline

> [!VIDEO https://www.youtube.com/embed/fHLCE-NZeb4?version=3&start=172&end=221]

En [pipeline](cognitive-search-concept-intro.md) i Azure kognitiv sökning kan samlas in från [inbyggda kognitiva kunskaper](cognitive-search-predefined-skills.md) samt [anpassade kunskaper](cognitive-search-custom-skill-web-api.md) som du själv skapar och lägger till i pipelinen. I den här artikeln lär du dig hur du skapar en anpassad färdighet som visar ett gränssnitt som gör det möjligt att inkludera i en AI-pipeline. 

Genom att skapa en anpassad färdighet får du ett sätt att infoga omvandlingar som är unika för ditt innehåll. En anpassad färdighet körs oberoende av varandra och använder det steg du behöver. Du kan till exempel definiera specifika anpassade entiteter, skapa anpassade klassificerings modeller för att särskilja affärs-och finansiella kontrakt och dokument, eller lägga till en kunskap om tal igenkänning för att komma djupare till ljudfilerna för relevant innehåll. Ett steg-för-steg-exempel finns i [exempel: skapa en anpassad färdighet för AI-berikning](cognitive-search-create-custom-skill-example.md).

 Vilken anpassad funktion du behöver finns det ett enkelt och tydligt gränssnitt för att ansluta en anpassad färdighet till resten av pipelinen för anrikning. Det enda kravet för att inkludera i en [färdigheter](cognitive-search-defining-skillset.md) är möjligheten att acceptera indata och generera utdata på ett sätt som kan användas i färdigheter som helhet. Fokus för den här artikeln är i indata-och utdataformat som krävs för anriknings pipelinen.

## <a name="web-api-custom-skill-interface"></a>Anpassat kunskaps gränssnitt för webb-API

Anpassad slut punkt för WebAPI-kunskaper som standard tids gräns om de inte returnerar ett svar inom 30 sekunders fönster. Indexerings pipelinen är synkron och indexeringen skapar ett tids gräns fel om inget svar tas emot i det fönstret.  Det går att konfigurera tids gränsen till upp till 230 sekunder genom att ange parametern timeout:

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 230 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT230S",
```

Kontrol lera att URI: n är säker (HTTPS).

För närvarande är den enda mekanismen för att interagera med en anpassad färdighet via ett webb-API-gränssnitt. Webb-API-behoven måste uppfylla kraven som beskrivs i det här avsnittet.

### <a name="1--web-api-input-format"></a>1. utdataformat för webb-API


> [!VIDEO https://www.youtube.com/embed/fHLCE-NZeb4?version=3&start=294&end=340]


Webb-API: et måste acceptera en matris med poster som ska bearbetas. Varje post måste innehålla en "egenskaps uppsättning" som är den indata som ges till ditt webb-API. 

Anta att du vill skapa en enkel berikare som identifierar det första datumet som anges i texten i ett kontrakt. I det här exemplet accepterar kunskapen en enskild *contractText* som kontrakts text. Kompetensen har också en enda utmatning, vilket är datumet för kontraktet. Om du vill bli mer intressant kan du returnera den här *contractDate* i form av en komplex typ av flera delar.

Ditt webb-API bör vara redo att ta emot en batch med inmatade poster. Varje medlem i matrisen *Values* representerar indata för en viss post. Varje post måste ha följande element:

+ En *recordId* -medlem som är den unika identifieraren för en viss post. När din berikare returnerar resultaten måste den ge den här *recordId* -funktionen för att tillåta anroparen att matcha postens resultat med indata.

+ En *data* medlem, som i princip är en uppsättning inmatnings fält för varje post.

För att vara mer konkret, enligt exemplet ovan, bör ditt webb-API förvänta sig förfrågningar som ser ut så här:

```json
{
    "values": [
      {
        "recordId": "a1",
        "data":
           {
             "contractText": 
                "This is a contract that was issues on November 3, 2017 and that involves... "
           }
      },
      {
        "recordId": "b5",
        "data":
           {
             "contractText": 
                "In the City of Seattle, WA on February 5, 2018 there was a decision made..."
           }
      },
      {
        "recordId": "c3",
        "data":
           {
             "contractText": null
           }
      }
    ]
}
```
I verkligheten kan din tjänst anropas med hundratals eller tusentals poster i stället för bara de tre som visas här.

### <a name="2-web-api-output-format"></a>2. utdataformat för webb-API

Formatet på utdata är en uppsättning poster som innehåller ett *recordId* och en egenskaps uppsättning 

```json
{
  "values": 
  [
      {
        "recordId": "b5",
        "data" : 
        {
            "contractDate":  { "day" : 5, "month": 2, "year" : 2018 }
        }
      },
      {
        "recordId": "a1",
        "data" : {
            "contractDate": { "day" : 3, "month": 11, "year" : 2017 }                    
        }
      },
      {
        "recordId": "c3",
        "data" : 
        {
        },
        "errors": [ { "message": "contractText field required "}   ],  
        "warnings": [ {"message": "Date not found" }  ]
      }
    ]
}
```

Det här specifika exemplet har bara ett utdata, men du kan skriva ut mer än en egenskap. 

### <a name="errors-and-warning"></a>Fel och varning

Som du ser i föregående exempel kan du returnera fel-och varnings meddelanden för varje post.

## <a name="consuming-custom-skills-from-skillset"></a>Konsumera anpassade kunskaper från färdigheter

När du skapar en webb-API-berikare kan du beskriva HTTP-huvuden och-parametrar som en del av begäran. I kodfragmentet nedan visas hur parametrarna för begäran och *valfria* HTTP-huvuden kan beskrivas som en del av färdigheter-definitionen. HTTP-huvuden är inte ett krav, men de gör att du kan lägga till ytterligare konfigurations funktioner i din skicklighet och ställa in dem från färdigheter-definitionen.

```json
{
    "skills": [
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill calls an Azure function, which in turn calls TA sentiment",
        "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/DateExtractor?language=en",
        "context": "/document",
        "httpHeaders": {
            "DateExtractor-Api-Key": "foo"
        },
        "inputs": [
          {
            "name": "contractText",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "contractDate",
            "targetName": "date"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs de gränssnitts krav som krävs för att integrera en anpassad färdighet i en färdigheter. Klicka på följande länkar om du vill veta mer om anpassade kunskaper och färdigheter-sammansättning.

+ [Titta på vår video om anpassade kunskaper](https://youtu.be/fHLCE-NZeb4)
+ [Energi kunskaper: ett lager med anpassade kunskaper](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Exempel: skapa en anpassad färdighet för AI-anrikning](cognitive-search-create-custom-skill-example.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Skapa färdigheter (REST)](/rest/api/searchservice/create-skillset)
+ [Så här mappar du omfattande fält](cognitive-search-output-field-mapping.md)