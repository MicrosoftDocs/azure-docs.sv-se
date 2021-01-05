---
title: Anpassad sökning av kognitiva entiteter för entitets ökning
titleSuffix: Azure Cognitive Search
description: Extrahera olika anpassade entiteter från text i en Azure Kognitiv sökning kognitiv Sök pipeline. Den här kunskapen är för närvarande en offentlig för hands version.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 704763e8e6e7c5336d0ed3e1c28791fb96c77aba
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/01/2021
ms.locfileid: "97844940"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>Anpassad enhets sökning av kognitiva kunskaper (för hands version)

> [!IMPORTANT] 
> Den här kunskapen är för närvarande en offentlig för hands version. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Det finns för närvarande inget stöd för Portal eller .NET SDK.

Den **anpassade Sök** kompetensen för entiteten söker efter text från en anpassad, användardefinierad lista med ord och fraser. Med den här listan etiketteras alla dokument med matchande entiteter. Kompetensen har även stöd för en viss fuzzy-matchning som kan användas för att söka efter matchningar som liknar varandra, men som inte exakt stämmer.  

Den här kunskapen är inte kopplad till ett Cognitive Services-API och kan användas kostnads fritt under för hands perioden. Du bör fortfarande [bifoga en Cognitive Services resurs](./cognitive-search-attach-cognitive-services.md), men för att åsidosätta den dagliga gränsen för anrikning. Den dagliga gränsen gäller för kostnads fri åtkomst till Cognitive Services vid åtkomst via Azure Kognitiv sökning.

## <a name="odatatype"></a>@odata.type  
Microsoft. färdigheter. text. CustomEntityLookupSkill 

## <a name="data-limits"></a>Databegränsningar
+ Den maximala storlek som stöds för indata-poster är 256 MB. Om du behöver dela upp dina data innan du skickar dem till den anpassade Sök kompetensen för entiteter bör du överväga att använda [text delnings kunskaper](cognitive-search-skill-textsplit.md).
+ Den största definitions tabellen för entiteter som stöds är 10 MB om den anges med parametern *entitiesDefinitionUri* . 
+ Om entiteterna har definierats infogas med hjälp av parametern *inlineEntitiesDefinition* , är den maximala storleken som stöds är 10 kB.

## <a name="skill-parameters"></a>Kunskaps parametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| `entitiesDefinitionUri`    | Sökväg till en JSON-eller CSV-fil som innehåller all mål text som ska matchas mot. Den här definitionen av entiteten läses i början av en indexerare-körning. alla uppdateringar av filen Mid-Run kommer inte att realiseras förrän efterföljande körningar. Den här konfigurationen måste vara tillgänglig via HTTPS. Se formatet för [anpassad definition av entitet](#custom-entity-definition-format) "nedan för förväntat CSV-eller JSON-schema.|
|`inlineEntitiesDefinition` | Definitioner av infogade JSON-enheter. Den här parametern ersätter parametern entitiesDefinitionUri om den finns. Högst 10 KB konfiguration kan anges infogas. Se [definitionen av anpassade entiteter](#custom-entity-definition-format) nedan för förväntat JSON-schema. |
|`defaultLanguageCode` |    Valfritt Språk koden för den inmatade text som används för att Tokenize och avgränsa inmatade text. Följande språk stöds: `da, de, en, es, fi, fr, it, ko, pt` . Standardvärdet är engelska ( `en` ). Om du skickar ett languageCode-CountryCode-format används endast languageCode-delen av formatet.  |
|`globalDefaultCaseSensitive` | Valfritt Standard Skift läges känsligt värde för färdigheten. Om `defaultCaseSensitive` värdet för en entitet inte anges blir det här värdet `defaultCaseSensitive` värdet för entiteten. |
|`globalDefaultAccentSensitive` | Valfritt Standardvärdet för dekor färgs läge för färdigheten. Om `defaultAccentSensitive` värdet för en entitet inte anges blir det här värdet `defaultAccentSensitive` värdet för entiteten. |
|`globalDefaultFuzzyEditDistance` | Valfritt Standard för fuzzy Edit Distance-värde för färdigheten. Om `defaultFuzzyEditDistance` värdet för en entitet inte anges blir det här värdet `defaultFuzzyEditDistance` värdet för entiteten. |

## <a name="skill-inputs"></a>Kompetens inmatningar

| Inmatat namn      | Beskrivning                   |
|---------------|-------------------------------|
| `text`          | Den text som ska analyseras.          |
| `languageCode`    | Valfritt. Standardvärdet är `"en"`.  |


## <a name="skill-outputs"></a>Kunskaps utmatningar


| Namn på utdata      | Beskrivning                   |
|---------------|-------------------------------|
| `entities` | En matris med objekt som innehåller information om de matchningar som hittades, och relaterade metadata. Varje entitet som identifieras kan innehålla följande fält:  <ul> <li> *namn*: entiteten på den översta nivån har identifierats. Entiteten representerar "normaliserad" form. </li> <li> *ID*: en unik identifierare för entiteten som definieras av användaren i definitions formatet för den anpassade entiteten.</li> <li> *Beskrivning*: Beskrivning av entitet som definieras av användaren i definitions formatet för den anpassade entiteten. </li> <li> *Typ:* Entitetstyp som definieras av användaren i definitions formatet för den anpassade entiteten.</li> <li> *undertyp:* Undertyp för entitet som definieras av användaren i definitions formatet för den anpassade entiteten.</li>  <li> *matchar*: samling som beskriver var och en av matchningarna för den entiteten i käll texten. Varje matchning kommer att ha följande medlemmar: </li> <ul> <li> *text*: den obehandlade text matchningen från käll dokumentet. </li> <li> *offset*: den plats där matchningen påträffades i texten. </li> <li> *längd*: den matchade textens längd. </li> <li> *matchDistance*: antalet tecken som skiljer matchningen från det ursprungliga enhets namnet eller aliaset.  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>Definitions format för anpassad entitet

Det finns tre olika sätt att tillhandahålla listan över anpassade entiteter till den anpassade Sök kompetensen för entiteten. Du kan ange listan i en. CSV-fil, a. JSON-fil eller som en infogad definition som en del av kunskaps definitionen.  

Om definitions filen är en. CSV eller. JSON-fil måste sökvägen till filen anges som en del av parametern *entitiesDefinitionUri* . I det här fallet hämtas filen en gång i början av varje indexerare-körning. Filen måste vara tillgänglig så länge indexeraren är avsedd att köras. Dessutom måste filen kodas UTF-8.

Om definitionen anges som infogad, ska den anges som infogad som innehåll i *inlineEntitiesDefinition* -färdighets parametern. 

### <a name="csv-format"></a>CSV-format

Du kan ange definitionen av de anpassade entiteterna som ska sökas efter i en Comma-Separated värde-fil (CSV) genom att ange sökvägen till filen och ange den i *entitiesDefinitionUri*  -kunskaps parametern. Sökvägen ska vara på en https-plats. Definitions filen kan vara upp till 10 MB stor.

CSV-formatet är enkelt. Varje rad representerar en unik entitet, som du ser nedan:

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

I det här fallet finns det tre entiteter som kan returneras som entiteter (Bill Gates, Satya Nadella, Microsoft), men de kommer att identifieras om någon av villkoren på raden (alias) matchar texten. Om t. ex. strängen "William H. Gates" finns i ett dokument returneras en matchning för entiteten "Bill Gates".

### <a name="json-format"></a>JSON-format

Du kan också ange definitionen för de anpassade entiteter som du vill söka efter i en JSON-fil. JSON-formatet ger dig större flexibilitet eftersom du kan definiera matchande regler per period. Du kan till exempel ange det fuzzy matchande avståndet (Damerau-levenshtein avstånd) för varje period eller om matchningen ska vara Skift läges känslig eller inte. 

 Precis som med CSV-filer måste du ange sökvägen till JSON-filen och ange den i *entitiesDefinitionUri* -kunskaps parametern. Sökvägen ska vara på en https-plats. Definitions filen kan vara upp till 10 MB stor.

Den mest grundläggande JSON-definitionen för anpassade entiteter kan vara en lista över entiteter som ska matchas:

```json
[ 
    { 
        "name" : "Bill Gates"
    }, 
    { 
        "name" : "Microsoft"
    }, 
    { 
        "name" : "Satya Nadella"
    }
]
```

Ett mer avancerat exempel på en JSON-definition kan alternativt ange ID, beskrivning, typ och undertyp för varje entitet – samt andra *alias*. Om ett alias matchas returneras även entiteten:

```json
[ 
    { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
    }, 
    { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
    }, 
    { 
        "name" : "LinkedIn" , 
        "description" : "The LinkedIn company", 
        "id" : "differentIdentifyingScheme123", 
        "fuzzyEditDistance" : 0 
    }, 
    { 
        "name" : "Microsoft" , 
        "description" : "Microsoft Corporation", 
        "id" : "differentIdentifyingScheme987", 
        "defaultCaseSensitive" : false, 
        "defaultFuzzyEditDistance" : 1, 
        "aliases" : [ 
            { "text" : "MSFT", "caseSensitive" : true }
        ]
    } 
] 
```

Tabellerna nedan beskriver de olika konfigurations parametrar som du kan ange när du definierar de entiteter som ska matchas i mer information:

|  Fältnamn  |        Beskrivning  |
|--------------|----------------------|
| `name` | Enhets beskrivningen på den översta nivån. Matchningar i färdighets utmatningen grupperas efter det här namnet och ska motsvara "normaliserad" form för den text som hittas.  |
| `description`  | Valfritt Det här fältet kan användas som en genom strömning för anpassade metadata om matchade text (er). Värdet för det här fältet visas med varje matchning av dess entitet i kunskaps resultatet. |
| `type` | Valfritt Det här fältet kan användas som en genom strömning för anpassade metadata om matchade text (er). Värdet för det här fältet visas med varje matchning av dess entitet i kunskaps resultatet. |
| `subtype` | Valfritt Det här fältet kan användas som en genom strömning för anpassade metadata om matchade text (er). Värdet för det här fältet visas med varje matchning av dess entitet i kunskaps resultatet. |
| `id` | Valfritt Det här fältet kan användas som en genom strömning för anpassade metadata om matchade text (er). Värdet för det här fältet visas med varje matchning av dess entitet i kunskaps resultatet. |
| `caseSensitive` | Valfritt Standardvärdet är false. Booleskt värde som anger om jämförelser med entitetsnamnet ska vara känslig för Skift läge. Exempel på SKIFT läges okänsliga matchningar av "Microsoft" kan vara: Microsoft, microSoft, MICROSOFT |
| `accentSensitive` | Valfritt Standardvärdet är false. Booleskt värde som anger om accenttecken och accenttecken, till exempel "é" och "e" ska vara identiska. |
| `fuzzyEditDistance` | Valfritt Standardvärdet är 0. Högsta värdet 5. Anger det godkända antalet Divergent-tecken som fortfarande utgör en matchning med entitetsnamnet. Det minsta möjliga oskärpa för en bestämd matchning returneras.  Om till exempel redigerings avståndet är inställt på 3, kommer "Windows 10" fortfarande att matcha "Windows", "windows10" och "Windows 7". <br/> Om SKIFT läges känslighet är inställt på falskt räknas inte fall skillnaderna över mot oskärpa tolerans, men annars görs. |
| `defaultCaseSensitive` | Valfritt Ändrar standard Skift läges känslighets värde för den här entiteten. Det kan användas för att ändra standardvärdet för alla alias caseSensitive-värden. |
| `defaultAccentSensitive` | Valfritt Ändrar standard värdet för dekor känslighet för den här entiteten. Det kan användas för att ändra standardvärdet för alla alias accentSensitive-värden.|
| `defaultFuzzyEditDistance` | Valfritt Ändrar standardvärdet för fuzzy Edit för den här entiteten. Det kan användas för att ändra standardvärdet för alla alias fuzzyEditDistance-värden. |
| `aliases` | Valfritt En matris med komplexa objekt som kan användas för att ange alternativa stavningar eller synonymer till rot enhetens namn. |

| Egenskaper för alias | Beskrivning |
|------------------|-------------|
| `text`  | Den alternativa stavningen eller representationen av ett visst mål enhets namn.  |
| `caseSensitive` | Valfritt Fungerar på samma sätt som rot entiteten "caseSensitive" ovan, men gäller endast detta alias. |
| `accentSensitive` | Valfritt Fungerar på samma sätt som rot entiteten "accentSensitive" ovan, men gäller endast detta alias. |
| `fuzzyEditDistance` | Valfritt Fungerar på samma sätt som rot entiteten "fuzzyEditDistance" ovan, men gäller endast detta alias. |


### <a name="inline-format"></a>Infogat format

I vissa fall kan det vara bekvämare att ange en lista med anpassade entiteter som matchar direkt i kunskaps definitionen. I så fall kan du använda ett liknande JSON-format för det som beskrivs ovan, men det finns i kunskaps definitionen.
Endast konfigurationer som är mindre än 10 KB i storlek (serialiserad storlek) kan definieras infogade. 

##    <a name="sample-definition"></a>Exempel definition

En exempel kunskaps definition som använder ett infogat format visas nedan:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "inlineEntitiesDefinition": 
    [
      { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
      }, 
      { 
        "name" : "Xbox One", 
        "type": "Hardware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
      }
    ],    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }
```
Alternativt, om du bestämmer dig för att tillhandahålla en pekare till definitions filen för entiteter, visas en exempel kunskaps definition med `entitiesDefinitionUri` formatet nedan:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "entitiesDefinitionUri": "https://myblobhost.net/keyWordsConfig.csv",    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
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
             "text": "The company, Microsoft, was founded by Bill Gates. Microsoft's gaming console is called Xbox",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Exempelutdata

```json
  { 
    "values" : 
    [ 
      { 
        "recordId": "1", 
        "data" : { 
          "entities": [
            { 
              "name" : "Microsoft", 
              "description" : "This document refers to Microsoft the company", 
              "id" : "differentIdentifyingScheme987", 
              "matches" : [ 
                { 
                  "text" : "microsoft", 
                  "offset" : 13, 
                  "length" : 9, 
                  "matchDistance" : 0 
                }, 
                { 
                  "text" : "Microsoft",
                  "offset" : 49, 
                  "length" : 9, 
                  "matchDistance" : 0
                }
              ] 
            },
            { 
              "name" : "Bill Gates",
              "description" : "William Henry Gates III, founder of Microsoft.", 
              "matches" : [
                { 
                  "text" : "Bill Gates",
                  "offset" : 37, 
                  "length" : 10,
                  "matchDistance" : 0 
                }
              ]
            }
          ] 
        } 
      } 
    ] 
  } 
```

## <a name="errors-and-warnings"></a>Fel och varningar

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>Varning: maximal kapacitet har nåtts för matchningar och alla ytterligare dubbla matchningar hoppas över.

Den här varningen genereras om antalet matchningar som har identifierats är större än det högsta tillåtna antalet. I det här fallet slutar vi att inkludera dubbletter av matchningar. Om detta inte är acceptabelt kan du ange ett [support ärende](https://ms.portal.azure.com/#create/Microsoft.Support) så att vi kan hjälpa dig med ditt enskilda användnings fall.

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Kunskap om enhets igenkänning (för att söka efter välkända entiteter)](cognitive-search-skill-entity-recognition.md)
