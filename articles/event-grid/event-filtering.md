---
title: Händelse filtrering för Azure Event Grid
description: Beskriver hur du filtrerar händelser när du skapar en Azure Event Grid-prenumeration.
ms.topic: conceptual
ms.date: 02/26/2021
ms.openlocfilehash: 7253c4a38660b0041f27918309efae21675fdc8f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721964"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Förstå händelse filtrering för Event Grid prenumerationer

I den här artikeln beskrivs olika sätt att filtrera vilka händelser som skickas till din slut punkt. När du skapar en händelse prenumeration har du tre alternativ för filtrering:

* Händelsetyper
* Ämne börjar med eller slutar med
* Avancerade fält och operatorer

## <a name="event-type-filtering"></a>Händelse typs filtrering

Som standard skickas alla [händelse typer](event-schema.md) för händelse källan till slut punkten. Du kan välja att bara skicka vissa händelse typer till din slut punkt. Du kan till exempel få ett meddelande om uppdateringar av dina resurser, men inte meddelas om andra åtgärder, t. ex. borttagningar. I så fall filtrerar du efter `Microsoft.Resources.ResourceWriteSuccess` händelse typ. Ange en matris med händelse typerna eller ange `All` för att hämta alla händelse typer för händelse källan.

JSON-syntaxen för att filtrera efter händelse typ är:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Ämnes filtrering

För enkel filtrering efter ämne anger du ett start-eller slut värde för ämnet. Du kan till exempel ange att ämnet slutar med `.txt` om du bara vill hämta händelser som rör överföring av en textfil till lagrings kontot. Eller så kan du filtrera ämnet börjar med `/blobServices/default/containers/testcontainer` för att hämta alla händelser för behållaren, men inte andra behållare i lagrings kontot.

När du publicerar händelser till anpassade ämnen skapar du ämnen för dina händelser som gör det enkelt för prenumeranter att veta om de är intresserade av evenemanget. Prenumeranter använder egenskapen subject för att filtrera och dirigera händelser. Överväg att lägga till sökvägen till platsen där händelsen inträffade, så att prenumeranter kan filtrera efter segment i den sökvägen. Med hjälp av sökvägen kan prenumeranter begränsa eller filtrera händelser på ett stort sätt. Om du anger en tre segment Sök väg som `/A/B/C` i ämnet kan prenumeranter filtrera efter det första segmentet `/A` för att få en bred uppsättning händelser. Dessa prenumeranter får händelser med ämnen som `/A/B/C` eller `/A/D/E` . Andra prenumeranter kan filtrera efter `/A/B` för att få en smalare uppsättning händelser.

JSON-syntaxen för att filtrera efter ämne är:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Avancerad filtrering

Använd alternativet avancerad filtrering för att filtrera efter värden i data fälten och ange jämförelse operatorn. I avancerad filtrering anger du:

* Operator typ – typen av jämförelse.
* Key – fältet i de händelse data som du använder för filtrering. Det kan vara ett tal, en boolesk sträng eller en matris.
* värden – värdet eller värdena som ska jämföras med nyckeln.

## <a name="key"></a>Nyckel
Key är fältet i de händelse data som du använder för filtrering. Det kan vara ett tal, en boolesk sträng eller en matris. För händelser i **Event Grid-schemat** använder du följande värden för nyckeln:,,,, `ID` `Topic` `Subject` `EventType` `DataVersion` eller händelse data (t `data.key1` . ex.).

För händelser i **Cloud Events-schemat** använder du följande värden för nyckeln: `eventid` , `source` , `eventtype` , `eventtypeversion` eller händelse data (t `data.key1` . ex.).

Använd händelse data fält (som) för **anpassade indata-schema** `data.key1` .

Om du vill komma åt fält i data avsnittet använder du `.` (punkt) notation. Till exempel, `data.sitename` för `data.appEventTypeDetail.action` att komma åt `sitename` eller `action` för följande exempel händelse.

```json
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
```


## <a name="values"></a>Värden
Värdena kan vara: Number, String, Boolean eller array


## <a name="operators"></a>Operatorer

De tillgängliga operatorerna för **tal** är:

## <a name="numberin"></a>Numberin
Operatorn numberin utvärderar värdet sant om **nyckelvärdet** är ett av de angivna **filtervärdet** . I följande exempel kontrollerar den om värdet för `counter` attributet i `data` avsnittet är 5 eller 1. 

```json
"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]
```


Om nyckeln är en matris kontrol leras alla värden i matrisen mot matrisen med filter värden. Här är pseudo-koden med nyckeln: `[v1, v2, v3]` och filtret: `[a, b, c]` . Alla nyckel värden med data typer som inte matchar filter data typen ignoreras.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="numbernotin"></a>NumberNotIn
NumberNotIn utvärderar till sant om **nyckelvärdet** **inte** är något av de angivna **filtervärdet** . I följande exempel kontrollerar den om värdet för `counter` attributet i `data` avsnittet inte är 41 och 0. 

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0
    ]
}]
```

Om nyckeln är en matris kontrol leras alla värden i matrisen mot matrisen med filter värden. Här är pseudo-koden med nyckeln: `[v1, v2, v3]` och filtret: `[a, b, c]` . Alla nyckel värden med data typer som inte matchar filter data typen ignoreras.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```

## <a name="numberlessthan"></a>NumberLessThan
NumberLessThan-operatorn utvärderar värdet sant om **nyckelvärdet** är **mindre än** det angivna **filtervärdet** . I följande exempel kontrollerar den om värdet för `counter` attributet i `data` avsnittet är mindre än 100. 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

Om nyckeln är en matris kontrol leras alla värden i matrisen mot filtervärdet. Här är pseudo-koden med nyckeln: `[v1, v2, v3]` . Alla nyckel värden med data typer som inte matchar filter data typen ignoreras.

```
FOR_EACH key IN (v1, v2, v3)
    IF key < filter
        MATCH
```

## <a name="numbergreaterthan"></a>NumberGreaterThan
NumberGreaterThan-operatorn utvärderar värdet sant om **nyckelvärdet** är **större än** det angivna **filtervärdet** . I följande exempel kontrollerar den om värdet för `counter` attributet i `data` avsnittet är större än 20. 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

Om nyckeln är en matris kontrol leras alla värden i matrisen mot filtervärdet. Här är pseudo-koden med nyckeln: `[v1, v2, v3]` . Alla nyckel värden med data typer som inte matchar filter data typen ignoreras.

```
FOR_EACH key IN (v1, v2, v3)
    IF key > filter
        MATCH
```

## <a name="numberlessthanorequals"></a>NumberLessThanOrEquals
NumberLessThanOrEquals-operatorn utvärderar värdet sant om **nyckelvärdet** är **mindre än eller lika** med det angivna **filtervärdet** . I följande exempel kontrollerar den om värdet för `counter` attributet i `data` avsnittet är mindre än eller lika med 100. 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

Om nyckeln är en matris kontrol leras alla värden i matrisen mot filtervärdet. Här är pseudo-koden med nyckeln: `[v1, v2, v3]` . Alla nyckel värden med data typer som inte matchar filter data typen ignoreras.

```
FOR_EACH key IN (v1, v2, v3)
    IF key <= filter
        MATCH
```

## <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals
NumberGreaterThanOrEquals-operatorn utvärderar värdet sant om **nyckelvärdet** är **större än eller lika** med det angivna **filtervärdet** . I följande exempel kontrollerar den om värdet för `counter` attributet i `data` avsnittet är större än eller lika med 30. 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

Om nyckeln är en matris kontrol leras alla värden i matrisen mot filtervärdet. Här är pseudo-koden med nyckeln: `[v1, v2, v3]` . Alla nyckel värden med data typer som inte matchar filter data typen ignoreras.

```
FOR_EACH key IN (v1, v2, v3)
    IF key >= filter
        MATCH
```

## <a name="numberinrange"></a>NumberInRange
NumberInRange-operatorn utvärderar värdet sant om **nyckelvärdet** är i ett av de angivna **filter intervallen**. I följande exempel kontrollerar den om värdet för `key1` attributet i `data` avsnittet är i något av de två områdena: 3,14159-999,95, 3000-4000. 

```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```

`values`Egenskapen är en matris med intervall. I föregående exempel är det en matris med två intervall. Här är ett exempel på en matris med ett intervall att kontrol lera. 

**Matris med ett intervall:** 
```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

Om nyckeln är en matris kontrol leras alla värden i matrisen mot matrisen med filter värden. Här är pseudo-koden med nyckeln: `[v1, v2, v3]` och filtret: en matris med intervall. I den här pseudo-koden `a` och `b` är låga och höga värden för varje intervall i matrisen. Alla nyckel värden med data typer som inte matchar filter data typen ignoreras.

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
       IF key >= a AND key <= b
           MATCH
```


## <a name="numbernotinrange"></a>NumberNotInRange
NumberNotInRange-operatorn utvärderar värdet sant om **nyckelvärdet** **inte** finns i något av de angivna **filter intervallen**. I följande exempel kontrollerar den om värdet för `key1` attributet i `data` avsnittet är i något av de två områdena: 3,14159-999,95, 3000-4000. Om så är fallet returnerar operatorn false. 

```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```
`values`Egenskapen är en matris med intervall. I föregående exempel är det en matris med två intervall. Här är ett exempel på en matris med ett intervall att kontrol lera.

**Matris med ett intervall:** 
```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

Om nyckeln är en matris kontrol leras alla värden i matrisen mot matrisen med filter värden. Här är pseudo-koden med nyckeln: `[v1, v2, v3]` och filtret: en matris med intervall. I den här pseudo-koden `a` och `b` är låga och höga värden för varje intervall i matrisen. Alla nyckel värden med data typer som inte matchar filter data typen ignoreras.

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
        IF key >= a AND key <= b
            FAIL_MATCH
```


Den tillgängliga operatorn för **booleska värden** är: 

## <a name="boolequals"></a>BoolEquals
Operatorn BoolEquals utvärderar till sant om **nyckelvärdet** är det angivna booleska värde **filtret**. I följande exempel kontrollerar den om värdet för `isEnabled` attributet i `data` avsnittet är `true` . 

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```

Om nyckeln är en matris kontrol leras alla värden i matrisen mot det booleska värdet för filtret. Här är pseudo-koden med nyckeln: `[v1, v2, v3]` . Alla nyckel värden med data typer som inte matchar filter data typen ignoreras.

```
FOR_EACH key IN (v1, v2, v3)
    IF filter == key
        MATCH
```

Tillgängliga operatorer för **strängar** är:

## <a name="stringcontains"></a>StringContains
**StringContains** utvärderas till sant om **nyckelvärdet** **innehåller** något av de angivna **filter** värdena (som under strängar). I följande exempel kontrollerar den om värdet för `key1` attributet i `data` avsnittet innehåller någon av de angivna under strängarna: `microsoft` eller `azure` . Innehåller till exempel `azure data factory` `azure` . 

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

Om nyckeln är en matris kontrol leras alla värden i matrisen mot matrisen med filter värden. Här är pseudo-koden med nyckeln: `[v1, v2, v3]` och filtret: `[a,b,c]` . Alla nyckel värden med data typer som inte matchar filter data typen ignoreras.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            MATCH
```

## <a name="stringnotcontains"></a>StringNotContains
Operatorn **StringNotContains** utvärderar till sant om **nyckeln** **inte innehåller** de angivna **filter** värdena som del strängar. Om nyckeln innehåller ett av de angivna värdena som en under sträng, utvärderas operatorn till false. I följande exempel returnerar operatorn True endast om värdet för `key1` attributet i `data` avsnittet inte har `contoso` och `fabrikam` som del strängar. 

```json
"advancedFilters": [{
    "operatorType": "StringNotContains",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam"
    ]
}]
```

Om nyckeln är en matris kontrol leras alla värden i matrisen mot matrisen med filter värden. Här är pseudo-koden med nyckeln: `[v1, v2, v3]` och filtret: `[a,b,c]` . Alla nyckel värden med data typer som inte matchar filter data typen ignoreras.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            FAIL_MATCH
```

## <a name="stringbeginswith"></a>StringBeginsWith
**StringBeginsWith** -operatorn utvärderar värdet sant  om nyckelvärdet **börjar med** något av de angivna **filtervärdet** . I följande exempel kontrollerar den om värdet för `key1` attributet i `data` avsnittet börjar med `event` eller `grid` . Börjar till exempel `event hubs` med `event` .  

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

Om nyckeln är en matris kontrol leras alla värden i matrisen mot matrisen med filter värden. Här är pseudo-koden med nyckeln: `[v1, v2, v3]` och filtret: `[a,b,c]` . Alla nyckel värden med data typer som inte matchar filter data typen ignoreras.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            MATCH
```

## <a name="stringnotbeginswith"></a>StringNotBeginsWith
**StringNotBeginsWith** -operatorn utvärderar värdet sant om **nyckelvärdet** **inte börjar med** något av de angivna **filtervärdet** . I följande exempel kontrollerar den om värdet för `key1` attributet i `data` avsnittet inte börjar med `event` eller `message` .

```json
"advancedFilters": [{
    "operatorType": "StringNotBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

Om nyckeln är en matris kontrol leras alla värden i matrisen mot matrisen med filter värden. Här är pseudo-koden med nyckeln: `[v1, v2, v3]` och filtret: `[a,b,c]` . Alla nyckel värden med data typer som inte matchar filter data typen ignoreras.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            FAIL_MATCH
```

## <a name="stringendswith"></a>StringEndsWith
**StringEndsWith** -operatorn utvärderar värdet sant  om nyckelvärdet **slutar med** ett av de angivna **filtervärdet** . I följande exempel kontrollerar den om värdet för `key1` attributet i `data` avsnittet slutar med `jpg` eller `jpeg` eller `png` . Slutar till exempel `eventgrid.png` med `png` .


```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

Om nyckeln är en matris kontrol leras alla värden i matrisen mot matrisen med filter värden. Här är pseudo-koden med nyckeln: `[v1, v2, v3]` och filtret: `[a,b,c]` . Alla nyckel värden med data typer som inte matchar filter data typen ignoreras.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            MATCH
```

## <a name="stringnotendswith"></a>StringNotEndsWith
**StringNotEndsWith** -operatorn utvärderar värdet sant om **nyckelvärdet** **inte slutar med** något av de angivna **filtervärdet** . I följande exempel kontrollerar den om värdet för `key1` attributet i `data` avsnittet inte slutar med `jpg` eller `jpeg` eller `png` . 


```json
"advancedFilters": [{
    "operatorType": "StringNotEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

Om nyckeln är en matris kontrol leras alla värden i matrisen mot matrisen med filter värden. Här är pseudo-koden med nyckeln: `[v1, v2, v3]` och filtret: `[a,b,c]` . Alla nyckel värden med data typer som inte matchar filter data typen ignoreras.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            FAIL_MATCH
```

## <a name="stringin"></a>Strängin
Operatorn **stringin** kontrollerar om **nyckel** värdet **exakt matchar** ett av de angivna **filter** värdena. I följande exempel kontrollerar den om värdet för `key1` attributet i `data` avsnittet är `exact` eller `string` eller `matches` . 

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam", 
        "factory"
    ]
}]
```

Om nyckeln är en matris kontrol leras alla värden i matrisen mot matrisen med filter värden. Här är pseudo-koden med nyckeln: `[v1, v2, v3]` och filtret: `[a,b,c]` . Alla nyckel värden med data typer som inte matchar filter data typen ignoreras.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="stringnotin"></a>StringNotIn
**StringNotIn** -operatorn kontrollerar om **nyckelvärdet** **inte matchar** något av de angivna **filter** värdena. I följande exempel kontrollerar den om värdet för `key1` attributet i `data` avsnittet inte är `aws` och `bridge` . 

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

Om nyckeln är en matris kontrol leras alla värden i matrisen mot matrisen med filter värden. Här är pseudo-koden med nyckeln: `[v1, v2, v3]` och filtret: `[a,b,c]` . Alla nyckel värden med data typer som inte matchar filter data typen ignoreras.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```


Alla sträng jämförelser är inte Skift läges känsliga.

> [!NOTE]
> Om händelse-JSON inte innehåller den avancerade filter nyckeln är filtret evaulated **inte matchat** för följande operatorer: NumberGreaterThan, NumberGreaterThanOrEquals, NumberLessThan, NumberLessThanOrEquals, numberi, BoolEquals, StringContains, StringNotContains, StringBeginsWith, StringNotBeginsWith, StringEndsWith, StringNotEndsWith, stringin.
> 
>Filtret är evaulated **matchat** för följande operatorer: NumberNotIn, StringNotIn.


## <a name="isnullorundefined"></a>IsNullOrUndefined
IsNullOrUndefined-operatorn utvärderar till sant om nyckelns värde är NULL eller odefinierat. 

```json
{
    "operatorType": "IsNullOrUndefined",
    "key": "data.key1"
}
```

I följande exempel saknas KEY1, så operatorn utvärderar till true. 

```json
{ 
    "data": 
    { 
        "key2": 5 
    } 
}
```

I följande exempel är KEY1 inställt på null, så operatorn skulle då utvärderas till true.

```json
{
    "data": 
    { 
        "key1": null
    }
}
```

om KEY1 har ett annat värde i de här exemplen kommer operatorn att utvärderas till false. 

## <a name="isnotnull"></a>IsNotNull
IsNotNull-operatorn utvärderar till sant om nyckelns värde inte är NULL eller odefinierat. 

```json
{
    "operatorType": "IsNotNull",
    "key": "data.key1"
}
```

## <a name="or-and-and"></a>ELLER och
Om du anger ett enskilt filter med flera värden utförs en- **eller** -åtgärd, så värdet för nyckel fältet måste vara ett av dessa värden. Här är ett exempel:

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

Om du anger flera olika filter utförs en- **och** -åtgärd, så varje filter villkor måste vara uppfyllt. Här är ett exempel: 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

## <a name="cloudevents"></a>CloudEvents 
För händelser i **CloudEvents-schemat** använder du följande värden för nyckeln: `eventid` ,,, `source` `eventtype` `eventtypeversion` eller händelse data (t `data.key1` . ex.). 

Du kan också använda [attribut för tilläggets context i CloudEvents 1,0](https://github.com/cloudevents/spec/blob/v1.0.1/spec.md#extension-context-attributes). I följande exempel `comexampleextension1` och `comexampleothervalue` är utöknings kontextens attribut. 

```json
{
    "specversion" : "1.0",
    "type" : "com.example.someevent",
    "source" : "/mycontext",
    "id" : "C234-1234-1234",
    "time" : "2018-04-05T17:31:00Z",
    "subject": null,
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
        "appinfoA" : "abc",
        "appinfoB" : 123,
        "appinfoC" : true
    }
}
```

Här är ett exempel på hur du använder ett tilläggs kontext-attribut i ett filter.

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "comexampleothervalue",
    "values": [
        "5", 
        "1"
    ]
}]
```


## <a name="limitations"></a>Begränsningar

Avancerad filtrering har följande begränsningar:

* 5 avancerade filter och 25 filter värden i alla filter per Event Grid-prenumeration
* 512 tecken per sträng värde
* Fem värden för **in** -och **not** -operatorer
* Nycklar med **`.` (punkt)** -tecknen i dem. Till exempel: `http://schemas.microsoft.com/claims/authnclassreference` eller `john.doe@contoso.com` . För närvarande finns det inget stöd för escape-tecken i nycklar. 

Samma nyckel kan användas i mer än ett filter.





## <a name="next-steps"></a>Nästa steg

* Information om hur du filtrerar händelser med PowerShell och Azure CLI finns i [Filtrera händelser för Event Grid](how-to-filter-events.md).
* Information om hur du snabbt kommer igång med Event Grid finns i [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
