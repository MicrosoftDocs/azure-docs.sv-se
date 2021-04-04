---
title: Utöka app vid körning – LUIS
description: Lär dig hur du utökar en redan publicerad förutsägelse slut punkt för att skicka ny information.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 1d94e9f59062e4d730b8f3b71022442e81e6eeda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98953029"
---
# <a name="extend-app-at-prediction-runtime"></a>Utöka appen vid förutsägelse körning

Appens schema (modeller och funktioner) har tränats och publicerats till förutsägelse slut punkten. Den här publicerade modellen används i förutsägelse körning. Du kan skicka ny information, tillsammans med användarens uttryck, till förutsägelse körningen för att förstärka förutsägelsen.

I två förutsägelse körnings schema ändringar ingår:
* [Externa entiteter](#external-entities)
* [Dynamiska listor](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>Externa entiteter

Externa entiteter ger din LUIS-app möjlighet att identifiera och märka enheter under körning, som kan användas som funktioner till befintliga entiteter. På så sätt kan du använda egna separata och anpassade enhets Extraherare innan du skickar frågor till din förutsägelse slut punkt. Eftersom detta görs på slut punkten för frågans förutsägelse behöver du inte träna och publicera din modell.

Klient programmet tillhandahåller sin egen enhets-Extractor genom att hantera enhets matchning och fastställa platsen i uttryck för den matchade entiteten och sedan skicka den informationen med begäran.

Externa entiteter är mekanismen för att utöka alla entitetstyper samtidigt som de fortfarande används som signaler till andra modeller.

Detta är användbart för en entitet som bara är tillgänglig vid körning av frågans förutsägelse. Exempel på den här typen av data ändrar ständigt data eller specifika per användare. Du kan utöka en LUIS kontakt-entitet med extern information från en användares kontakt lista.

Externa entiteter är en del av v3-redigerings-API: et. Läs mer om att [migrera](luis-migration-api-v3.md) till den här versionen.

### <a name="entity-already-exists-in-app"></a>Enheten finns redan i appen

Värdet för `entityName` den externa entiteten som skickades i slut punkts förfrågan efter inläggs meddelande måste redan finnas i den utbildade och publicerade appen när begäran görs. Typen av entitet spelar ingen roll, men alla typer stöds.

### <a name="first-turn-in-conversation"></a>Första inaktive samtal

Överväg en första uttryck i en chatt robot-konversation där en användare anger följande ofullständiga information:

`Send Hazem a new message`

Begäran från chatt-roboten till LUIS kan skicka in information i INLÄGGs texten om `Hazem` så att den matchas direkt som en av användarens kontakter.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

I förutsägelse svaret ingår den externa entiteten, med alla andra förväntade entiteter, eftersom den definieras i begäran.

### <a name="second-turn-in-conversation"></a>Andra aktivering i konversation

Nästa användarens uttryck i Chat-roboten använder en mer vagt-period:

`Send him a calendar reminder for the party.`

I den här konversationen använder uttryck `him` som en referens till `Hazem` . Chatten i POST texten kan mappas `him` till enhet svärdet som extraheras från den första uttryck `Hazem` .

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

I förutsägelse svaret ingår den externa entiteten, med alla andra förväntade entiteter, eftersom den definieras i begäran.

### <a name="override-existing-model-predictions"></a>Åsidosätt befintliga modell förutsägelser

`preferExternalEntities`Egenskapen Options anger att om användaren skickar en extern entitet som överlappar med en förväntad entitet med samma namn, väljer Luis den enhet som har skickats eller entiteten som finns i modellen.

Anta till exempel frågan `today I'm free` . LUIS identifierar `today` som en datetimeV2 med följande svar:

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Om användaren skickar den externa entiteten:

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

Om `preferExternalEntities` är inställt på `false` , returnerar Luis ett svar som om den externa entiteten inte skickades.

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Om `preferExternalEntities` är inställt på `true` , returnerar Luis ett svar, inklusive:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Lösning

Den _valfria_ `resolution` egenskapen returneras i förutsägelse svaret, så att du kan skicka de metadata som är associerade med den externa entiteten och sedan ta emot den igen i svaret.

Det primära syftet är att utöka fördefinierade entiteter, men den är inte begränsad till den typen av enhet.

`resolution`Egenskapen kan vara ett tal, en sträng, ett objekt eller en matris:

* Kontoret
* {"text": "värde"}
* 12345
* ["a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>Dynamiska listor

Med dynamiska listor kan du utöka en befintlig utbildad och publicerad List-entitet, redan i LUIS-appen.

Använd den här funktionen när du behöver ändra listan med enhets värden. Med den här funktionen kan du utöka en entitet som redan har tränats och publicerats:

* Vid tiden för slut punkten för förfrågan förutsägelse.
* För en enskild begäran.

List entiteten kan vara tom i LUIS-appen, men den måste finnas. List-entiteten i LUIS-appen har inte ändrats, men förutsägelse funktionen i slut punkten utökas till att innehålla upp till 2 listor med cirka 1 000 objekt.

### <a name="dynamic-list-json-request-body"></a>Brödtext för dynamisk lista JSON-begäran

Skicka i följande JSON-brödtext för att lägga till en ny under lista med synonymer till listan och Förutsäg List entiteten för texten, `LUIS` med `POST` frågan förutsägelse-begäran:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

Förutsägelse-svaret innehåller List entitet, med alla andra förväntade entiteter, eftersom den definieras i begäran.

## <a name="next-steps"></a>Nästa steg

* [Förutsägelseresultat](luis-concept-prediction-score.md)
* [Redigera API v3-ändringar](luis-migration-api-v3.md)
