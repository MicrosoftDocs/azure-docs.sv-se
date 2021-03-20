---
title: Anpassad webb-API-kunskap i färdighetsuppsättningar
titleSuffix: Azure Cognitive Search
description: 'Utöka funktionerna i Azure Kognitiv sökning färdighetsuppsättningar genom att anropa ut till webb-API: er. Använd anpassade webb-API-kunskaper för att integrera din anpassade kod.'
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: cb5ee7d3549e433fb184b8c55c28b9a28ed89272
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011939"
---
# <a name="custom-web-api-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Anpassad webb-API-kunskap i en Azure Kognitiv sökning anriknings pipeline

Med den **anpassade webb-API-** kunskapen kan du utöka AI-anrikning genom att anropa till en webb-API-slutpunkt som tillhandahåller anpassade åtgärder. På liknande sätt som inbyggda kunskaper har en **anpassad webb-API-** kunskap indata och utdata. Beroende på indata får ditt webb-API en JSON-nyttolast när indexeraren körs, och utvärderar en JSON-nyttolast som svar, tillsammans med status koden lyckades. Svaret förväntas ha de utdata som anges av din anpassade färdighet. Andra svar betraktas som ett fel och inga berikningar utförs.

Strukturen för JSON-nyttolasterna beskrivs ytterligare ned i det här dokumentet.

> [!NOTE]
> Indexeraren försöker igen två gånger för vissa standard-HTTP-statuskod som returneras från webb-API: et. HTTP-status koderna är: 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft. färdigheter. Custom. WebApiSkill

## <a name="skill-parameters"></a>Kunskaps parametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| `uri` | URI för webb-API: t som _JSON_ -nyttolasten ska skickas till. Endast **https** URI-schema tillåts |
| `httpMethod` | Den metod som ska användas vid sändning av nytto lasten. Tillåtna metoder är `PUT` eller `POST` |
| `httpHeaders` | En samling nyckel/värde-par där nycklarna representerar rubrik namn och värden representerar huvud värden som skickas till ditt webb-API tillsammans med nytto lasten. Följande rubriker är förbjudna att tas med i samlingen:,,,,,,, `Accept` `Accept-Charset` `Accept-Encoding` `Content-Length` `Content-Type` `Cookie` `Host` `TE` `Upgrade``Via` |
| `timeout` | Valfritt Anger tids gränsen för http-klienten som gör API-anropet. Det måste formateras som ett XSD "dayTimeDuration"-värde (en begränsad delmängd av ett [varaktighets värde på ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Till exempel `PT60S` i 60 sekunder. Om inget värde anges väljs ett standardvärde på 30 sekunder. Tids gränsen kan anges till högst 230 sekunder och minst 1 sekund. |
| `batchSize` | Valfritt Anger hur många "data poster" (se _JSON_ nytto Last strukturen nedan) som ska skickas per API-anrop. Om den inte anges väljs standardvärdet 1000. Vi rekommenderar att du använder den här parametern för att uppnå en lämplig kompromiss mellan indexering av data flödet och belastningen på ditt API |
| `degreeOfParallelism` | Valfritt Anger hur många anrop som indexeraren ska göra parallellt med den slut punkt som du har angett. Du kan minska det här värdet om slut punkten har misslyckats under för hög belastning på begäran, eller öka den om slut punkten kan ta emot fler begär Anden och du vill ha en ökning av prestandan för indexeraren.  Om inget värde anges används standardvärdet 5. `degreeOfParallelism`Kan anges till högst 10 och minst 1. |

## <a name="skill-inputs"></a>Kompetens inmatningar

Det finns inga fördefinierade indata för den här kunskapen. Du kan välja ett eller flera fält som redan är tillgängliga när du kör den här färdighets körningen som indata och _JSON_ -nyttolasten som skickas till webb-API: t har olika fält.

## <a name="skill-outputs"></a>Kunskaps utmatningar

Det finns inga fördefinierade utdata för den här kunskapen. Beroende på vilket svar som webb-API: t kommer att returnera, lägger du till utdatakolumner så att de kan hämtas från _JSON_ -svaret.


## <a name="sample-definition"></a>Exempel definition

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can identify positions of different phrases in the source text",
        "uri": "https://contoso.count-things.com",
        "batchSize": 4,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/languageCode"
          },
          {
            "name": "phraseList",
            "source": "/document/keyphrases"
          }
        ],
        "outputs": [
          {
            "name": "hitPositions"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>Exempel-JSON-struktur

Den här _JSON_ -strukturen representerar den nytto last som ska skickas till ditt webb-API.
Den kommer alltid att följa dessa begränsningar:

* Entiteten på den översta nivån anropas `values` och är en matris med objekt. Antalet sådana objekt är högst `batchSize`
* Varje objekt i `values` matrisen kommer att ha
    * En `recordId` egenskap som är en **unik** sträng som används för att identifiera posten.
    * En `data` egenskap som är ett _JSON_ -objekt. Fälten i `data` egenskapen motsvarar de "namn" som anges i `inputs` avsnittet i kunskaps definitionen. Värdet för dessa fält kommer från `source` dessa fält (som kan komma från ett fält i dokumentet eller som kan komma från en annan kunskap)

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "phraseList": ["Este", "Inglés"]
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "phraseList": ["Hi"]
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world, Hi world",
             "language": "en",
             "phraseList": ["world"]
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "phraseList": []
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Exempel-JSON-struktur för utdata

"Utdata" motsvarar det svar som returneras från ditt webb-API. Webb-API: et ska bara returnera en _JSON_ -nyttolast (verifierad genom att titta på `Content-Type` svars huvudet) och bör uppfylla följande begränsningar:

* Det bör finnas en entitet på den översta nivån `values` som ska vara en matris med objekt.
* Antalet objekt i matrisen måste vara samma som antalet objekt som skickas till webb-API: et.
* Varje objekt bör ha:
   * En `recordId` egenskap
   * En `data` egenskap, som är ett objekt där fälten är anrikninger som matchar "namn" i `output` och vars värde betraktas som berikning.
   * En `errors` egenskap, en matris som visar eventuella fel som kommer att läggas till i körnings historiken för indexeraren. Den här egenskapen är obligatorisk, men kan ha ett `null` värde.
   * En `warnings` egenskap, en matris som visar alla varningar som kommer att läggas till i körnings historiken för indexeraren. Den här egenskapen är obligatorisk, men kan ha ett `null` värde.
* Objekt i `values` matrisen behöver inte vara i samma ordning som de objekt i `values` matrisen som skickas som en begäran till webb-API: et. Men `recordId` används för korrelation så att alla poster i svaret som innehåller en `recordId` som inte var en del av den ursprungliga begäran till webb-API: et ignoreras.

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "'phraseList' should not be null or empty"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "hitPositions": [6, 16]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "hitPositions": [0, 23]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "hitPositions": []
            },
            "errors": null,
            "warnings": {
                "message": "No occurrences of 'Hi' were found in the input text"
            }
        },
    ]
}

```

## <a name="error-cases"></a>Fel fall
Förutom att ditt webb-API är otillgängligt, eller om du skickar ut status koder som inte lyckades, anses följande vara felaktiga fall:

* Om webb-API: t returnerar en status kod för lyckad status men svaret visar att svaret inte är det `application/json` , anses inte svaret vara ogiltigt och inga berikningar utförs.
* Om det finns **ogiltiga** (med `recordId` inte i den ursprungliga begäran eller med dubblettvärden) poster i svars `values` mat ris, utförs ingen berikning för **dessa** poster.

När webb-API: t inte är tillgängligt eller returnerar ett HTTP-fel, läggs ett fel meddelande med tillgänglig information om HTTP-felet till i körnings historiken för Indexer.

## <a name="see-also"></a>Se även

+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Lägg till anpassad kompetens i en AI-pipeline för anrikning](cognitive-search-custom-skill-interface.md)
+ [Exempel: skapa en anpassad färdighet för AI-anrikning](cognitive-search-create-custom-skill-example.md)
