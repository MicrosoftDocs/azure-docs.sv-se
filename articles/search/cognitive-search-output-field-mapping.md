---
title: Mappa indata till utdatakolumner
titleSuffix: Azure Cognitive Search
description: Extrahera och utöka käll data fält och mappa till utmatnings fält i ett Azure Kognitiv sökning-index.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 58bb87d5af785d3cffd96f3bd02477f97ed967a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001311"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>Så här mappar du AI-berikade fält till ett sökbart index

![Indexerings steg](./media/cognitive-search-output-field-mapping/indexer-stages-output-field-mapping.png "indexerings steg")

I den här artikeln får du lära dig hur du mappar informativa inmatnings fält till utdatakolumner i ett sökbart index. När du har [definierat en färdigheter](cognitive-search-defining-skillset.md)måste du mappa utmatnings fälten för alla färdigheter som direkt bidrar med värden till ett angivet fält i ditt sökindex.

Mappningar av utdatakolumner krävs för att flytta innehåll från berikade dokument till indexet.  Det förrikade dokumentet är egentligen ett träd med information, även om det finns stöd för komplexa typer i indexet, ibland kanske du vill omvandla informationen från det berikade trädet till en mer enkel typ (till exempel en sträng mat ris). Med mappningar av utdata fält kan du utföra transformeringar av data former genom att förenkla informationen. Mappningar av utdatakolumner sker alltid efter färdigheter-körning, även om det är möjligt för det här steget att köras även om ingen färdigheter har definierats.

Exempel på mappningar av utdata-fält:

* Som en del av din färdigheter extraherade du namnen på de organisationer som anges på varje sida i ditt dokument. Nu vill du mappa vart och ett av dessa organisations namn till ett fält i indexet av typen EDM. Collection (EDM. String).

* Som en del av din färdigheter skapade du en ny nod med namnet "Document/translated_text". Du vill mappa informationen på den här noden till ett särskilt fält i ditt index.

* Du har inte någon färdigheter men indexerar en komplex typ från en Cosmos DB databas. Du vill komma till en nod på den komplexa typen och mappa den till ett fält i ditt index.

> [!NOTE]
> Vi har nyligen aktiverat funktionerna i mappnings funktioner för mappningar av utdatakolumner. Mer information om mappnings funktioner finns i [fält mappnings funktioner](./search-indexer-field-mappings.md#field-mapping-functions)

## <a name="use-outputfieldmappings"></a>Använd outputFieldMappings

Om du vill mappa fält lägger `outputFieldMappings` du till den i index definitions definitionen enligt nedan:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
```

Bröd texten i begäran är strukturerad enligt följande:

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions",
            "mappingFunction": {
                "name": "base64Decode"
            }
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```

För varje fält mappning för utdata anger du platsen för data i det fördefinierade dokument trädet (sourceFieldName) och namnet på fältet som refereras till i indexet (targetFieldName).

## <a name="flattening-information-from-complex-types"></a>Förenkling av information från komplexa typer 

Sökvägen i en sourceFieldName kan representera ett eller flera element. I exemplet ovan ```/document/content/sentiment``` representerar ett enkelt numeriskt värde, medan ```/document/content/organizations/*/description``` representerar flera organisations beskrivningar. 

I de fall där det finns flera element blir de "förenklade" i en matris som innehåller varje element. 

Mer konkret, i ```/document/content/organizations/*/description``` exemplet skulle data i fältet *beskrivningar* se ut som en plan mat ris med beskrivningar innan de indexeras:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

Detta är en viktig princip, så vi kommer att ange ett annat exempel. Anta att du har en matris med komplexa typer som en del av ditt anriknings träd. Anta att det finns en medlem med namnet customEntities som har en matris av komplexa typer som den som beskrivs nedan.

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

Vi antar att ditt index har ett fält med namnet "sjukdomar" av typen Collection (EDM. String) där du vill lagra vart och ett av namnen på entiteterna. 

Detta kan göras enkelt med hjälp av symbolen " \* ", enligt följande:

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

Den här åtgärden kommer bara att "förenkla" var och en av namnen på customEntities-elementen till en enda sträng mat ris som detta:

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>Nästa steg
När du har mappat dina berikade fält till sökbara fält kan du ange fältattribut för varje sökbart fält [som en del av index definitionen](search-what-is-an-index.md).

Mer information om fält mappning finns i [fält mappningar i Azure kognitiv sökning indexerare](search-indexer-field-mappings.md).