---
title: 'Förutsägelse slut punkt ändringar i v3-API: et'
description: 'Slut punkten v3-API: er för fråga förutsägelse har ändrats. Använd den här guiden för att lära dig hur du migrerar till API: er för version 3-slutpunkt.'
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 59dfa439f6428f2db972a8f848887e1a74bc2622
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624311"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Förutsägelse slut punkt ändringar för v3

Slut punkten v3-API: er för fråga förutsägelse har ändrats. Använd den här guiden för att lära dig hur du migrerar till API: er för version 3-slutpunkt.

**Allmänt tillgänglig status** – detta v3-API inkluderar AVSEVÄRDa JSON-förfrågningar och svars ändringar från v2 API.

V3-API: et tillhandahåller följande nya funktioner:

* [Externa entiteter](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)
* [Dynamiska listor](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Inbyggda JSON-ändringar för entitet](#prebuilt-entity-changes)

Förutsägelse slut punkts [förfrågan](#request-changes) och- [svaret](#response-changes) har betydande ändringar för att stödja de nya funktionerna ovan, inklusive följande:

* [Svars objekt ändringar](#top-level-json-changes)
* [Namn referenser för enhets roll i stället för entitetsnamnet](#entity-role-name-instead-of-entity-name)
* [Egenskaper för att markera entiteter i yttranden](#marking-placement-of-entities-in-utterances)

[Referens dokumentationen](https://aka.ms/luis-api-v3) är tillgänglig för v3.

## <a name="v3-changes-from-preview-to-ga"></a>V3 ändras från för hands version till GA

V3 gjorde följande ändringar som en del av över gången till GA:

* Följande fördefinierade entiteter har olika JSON-svar:
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * Namn på mätbar enhets nyckel från `units` till `unit`

* Begär ande text JSON-ändring:
    * från `preferExternalEntities` till `preferExternalEntities`
    * valfri `score` parameter för externa entiteter

* JSON-ändringar för svars text:
    * `normalizedQuery` bort

## <a name="suggested-adoption-strategy"></a>Föreslagen införande strategi

Om du använder bot Framework, Stavningskontroll i Bing v7, eller om du vill migrera din LUIS app-redigering, fortsätter du att använda v2-slutpunkten.

Om du vet att ingen av dina klient program eller-integrationer (bot Framework och Stavningskontroll i Bing v7) påverkas och du är van att migrera LUIS-appens redigering och din förutsägelse slut punkt på samma gång börjar du med att använda v3 förutsägelse slut punkten. Slut punkten för version v2-förutsägelse är fortfarande tillgänglig och är en lämplig strategi för att återställa.


## <a name="not-supported"></a>Stöds inte

### <a name="bing-spell-check"></a>Stavningskontroll i Bing

Detta API stöds inte i v3 förutsägelse slut punkt – Fortsätt att använda v2 API förutsägelse slut punkt för stavnings korrigeringar. Om du behöver stavnings korrigering när du använder v3 API, måste klient programmet anropa [stavningskontroll i Bing](../bing-spell-check/overview.md) API och ändra texten till rätt stavning, innan texten skickas till Luis-API: et.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Bot Framework och Azure Bot Service klient program

Fortsätt att använda v2 API förutsägelse-slutpunkten tills V 4.7 i bot Framework har släppts.


## <a name="endpoint-url-changes"></a>Slut punkts-URL-ändringar

### <a name="changes-by-slot-name-and-version-name"></a>Ändringar efter plats namn och versions namn

[Formatet på v3-slut punktens HTTP-](developer-reference-resource.md#rest-endpoints) anrop har ändrats.

Om du vill fråga efter version måste du först [publicera via API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) med `"directVersionPublish":true` . Fråga slut punkten som refererar till versions-ID: t i stället för plats namnet.

|Giltiga värden för `SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Begära ändringar

### <a name="query-string-changes"></a>Ändra frågesträngar

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

### <a name="v3-post-body"></a>V3-INLÄGGs text

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "preferExternalEntities": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Egenskap|Typ|Version|Standardvärde|Syfte|
|--|--|--|--|--|
|`dynamicLists`|matris|Endast v3|Krävs inte.|Med [dynamiska listor](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time) kan du utöka en befintlig utbildad och publicerad List-entitet, redan i Luis-appen.|
|`externalEntities`|matris|Endast v3|Krävs inte.|[Externa entiteter](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time) ger din Luis-app möjlighet att identifiera och märka enheter under körning, som kan användas som funktioner till befintliga entiteter. |
|`options.datetimeReference`|sträng|Endast v3|Ingen standard|Används för att fastställa [datetimeV2 offset](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Formatet för datetimeReference är [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|boolean|Endast v3|falskt|Anger om användarens [externa entitet (med samma namn som befintlig entitet)](schema-change-prediction-runtime.md#override-existing-model-predictions) används eller om den befintliga entiteten i modellen används för förutsägelse. |
|`query`|sträng|Endast v3|Krävs.|**I v2** är uttryck som ska förutsägas i `q` parametern. <br><br>**I v3** skickas funktionerna i- `query` parametern.|

## <a name="response-changes"></a>Svars ändringar

JSON-svaret har ändrats för att ge bättre programmerings åtkomst till de data som används oftast.

### <a name="top-level-json-changes"></a>JSON-ändringar på högsta nivån



De översta JSON-egenskaperna för v2 är, när `verbose` har angetts till true, vilket returnerar alla intentor och deras resultat i `intents` egenskapen:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

De största JSON-egenskaperna för v3 är:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "topIntent": "intent-name-1",
        "intents": {},
        "entities":{}
    }
}
```

`intents`Objektet är en osorterad lista. Anta inte att det första barnet i `intents` motsvarar `topIntent` . Använd i stället `topIntent` värdet för att hitta poängen:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Ändringarna i svars-JSON-schemat tillåter:

* Ta bort distinktionen mellan ursprunglig uttryck, `query` och returnerade förutsägelser `prediction` .
* Enklare programmerings åtkomst till förväntade data. I stället för att räkna upp genom en matris i v2 kan du komma åt värden efter **namn** för både avsikter och entiteter. För förväntade enhets roller returneras roll namnet eftersom det är unikt i hela appen.
* Data typer, om de fastställs, är uppfyllda. Numeriska värden returneras inte längre som strängar.
* Skillnaden mellan den första prioriterade förutsägelse informationen och ytterligare metadata som returneras i `$instance` objektet.

### <a name="entity-response-changes"></a>Enhets svars ändringar

#### <a name="marking-placement-of-entities-in-utterances"></a>Markera placering av entiteter i yttranden

**I v2** har en entitet marker ATS i en uttryck med `startIndex` och `endIndex` .

**I v3** markeras entiteten med `startIndex` och `entityLength` .

#### <a name="access-instance-for-entity-metadata"></a>Åtkomst `$instance` för entitets-metadata

Om du behöver entitetens metadata måste frågesträngen använda `verbose=true` flaggan och svaret innehåller metadata i `$instance` objektet. Exempel visas i JSON-svaren i följande avsnitt.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Varje förväntad entitet representeras som en matris

`prediction.entities.<entity-name>`Objektet innehåller en matris eftersom varje entitet kan förutsägas mer än en gång i uttryck.

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Förbyggda enhets ändringar

Objektet v3-svar innehåller ändringar av fördefinierade entiteter. Granska [vissa förbyggda entiteter](luis-reference-prebuilt-entities.md) för mer information.

#### <a name="list-entity-prediction-changes"></a>Visa lista över enhets förutsägelse ändringar

JSON för en List enhets förutsägelse har ändrats till att vara en matris med matriser:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Varje inre matris motsvarar text inuti uttryck. Det inre objektet är en matris eftersom samma text kan visas i fler än en under lista i en List-entitet.

Vid mappning mellan `entities` objektet och `$instance` objektet bevaras objekt ordningen för List entiteten förutsägelser.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Enhets roll namn i stället för entitetsnamnet

I v2 `entities` returnerade matrisen alla förväntade entiteter med entitetens namn som unik identifierare. I v3, om entiteten använder roller och förutsägelsen för en Entity-roll, är den primära identifieraren roll namnet. Detta är möjligt eftersom entiteternas roll namn måste vara unika i hela appen, inklusive andra modell namn (avsikt, entitet).

I följande exempel: Överväg en uttryck som innehåller texten, `Yellow Bird Lane` . Den här texten är förväntad som en anpassad `Location` entitets roll för `Destination` .

|Uttryck-text|Entitetsnamn|Rollnamn|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

I v2 identifieras entiteten av _entitetsnamnet_ med rollen som en egenskap för objektet:

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

I v3 refereras entiteten av entitets _rollen_, om förutsägelsen är för rollen:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

I v3, samma resultat med `verbose` flaggan för att returnera metadata för entiteten:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

<a name="external-entities-passed-in-at-prediction-time"></a>
<a name="override-existing-model-predictions"></a>

## <a name="extend-the-app-at-prediction-time"></a>Utöka appen vid förutsägelse tiden

Lär dig [begrepp](schema-change-prediction-runtime.md) om hur du utökar appen vid förutsägelse körning.


## <a name="next-steps"></a>Nästa steg

Använd v3 API-dokumentationen för att uppdatera befintliga REST-anrop till LUIS [slut punkts](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/operations/5cb0a9459a1fe8fa44c28dd8) -API: er.
