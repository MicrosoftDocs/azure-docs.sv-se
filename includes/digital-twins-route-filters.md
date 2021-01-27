---
author: baanders
description: inkludera fil för Azure Digitals dubbla väg filter alternativ
ms.service: digital-twins
ms.topic: include
ms.date: 12/04/2020
ms.author: baanders
ms.openlocfilehash: d93f484e318c10489eb1db3e9c65c6e0c7479c90
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98859572"
---
| Filternamn | Description | Filtrera text schema | Värden som stöds | 
| --- | --- | --- | --- |
| True/false | Tillåter att du skapar en väg utan filtrering eller inaktiverar en väg så att inga händelser skickas | `<true/false>` | `true` = Route är aktiverat utan filtrering <br> `false` = vägen är inaktive rad |
| Typ | Den [typ av händelse](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) som flödar genom den digitala dubbla instansen | `type = '<eventType>'` | Här följer möjliga värden för händelse typ: <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Källa | Namn på Azure Digitals dubbla instanser | `source = '<hostname>'`| Här följer möjliga värden för värdnamn: <br> **För meddelanden**: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net` <br> **För telemetri**: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net/<twinId>`|
| Ämne | En beskrivning av händelsen i kontexten för händelse källan ovan | `subject = '<subject>'` | Här följer möjliga ämnes värden: <br>**För meddelanden**: ämnet är `<twinid>` <br> eller ett URI-format för ämnen som identifieras unikt av flera delar eller ID:<br>`<twinid>/relationships/<relationshipid>`<br> **För telemetri**: ämnet är komponent Sök vägen (om telemetri skickas från en dubbel komponent), till exempel `comp1.comp2` . Om telemetri inte genereras från en komponent är dess ämnes fält tomt. |
| Data schema | DTDL modell-ID | `dataschema = '<model-dtmi-ID>'` | **För telemetri**: dataschemat är modell-ID: t för den dubbla eller komponenten som utvärderar Telemetrin. Till exempel `dtmi:example:com:floor4;2` <br>**För meddelanden (skapa/ta bort)**: data schema kan nås i meddelande texten på `$body.$metadata.$model` . <br>**För meddelanden (uppdatering)**: data schema kan nås i meddelande texten på `$body.modelId`|
| Innehållstyp | Innehålls typ för data värde | `datacontenttype = '<contentType>'` | Innehålls typen är `application/json` |
| Specifikations version | Den version av händelse schemat som du använder | `specversion = '<version>'` | Versionen måste vara `1.0` . Detta anger CloudEvents-schema version 1,0 |
| Meddelande text | Referera till en egenskap i `data` fältet för ett meddelande | `$body.<property>` | Se [*anvisningar: förstå händelse data*](../articles/digital-twins/how-to-interpret-event-data.md) för exempel på meddelanden. `data`Du kan referera till en egenskap i fältet med hjälp av`$body`

Följande data typer stöds som värden som returneras av referenser till data ovan:

| Datatyp | Exempel |
|-|-|-|
|**Sträng**| `STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')` <br> `CONTAINS(subject, '<twinID>')`|
|**Integer**|`$body.errorCode > 200`|
|**Dubbelklicka**|`$body.temperature <= 5.5`|
|**Booleska**|`$body.poweredOn = true`|
|**Null**|`$body.prop != null`|

Följande operatorer stöds när du definierar väg filter:

|Familj|Operatorer|Exempel|
|-|-|-|
|Logiskt|AND, OR, ()|`(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')`|
|Jämförelse|<, <=, >, >=, =,! =|`$body.temperature <= 5.5`

Följande funktioner stöds när du definierar flödes filter:

|Funktion|Beskrivning|Exempel|
|--|--|--|
|STARTS_WITH (x, y)|Returnerar true om värdet `x` börjar med strängen `y` .|`STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')`|
|ENDS_WITH (x, y) | Returnerar true om värdet `x` slutar med strängen `y` .|`ENDS_WITH($body.$metadata.$model, 'floor;1')`|
|INNEHÅLLER (x, y)| Returnerar true om värdet `x` innehåller strängen `y` .|`CONTAINS(subject, '<twinID>')`|

När du implementerar eller uppdaterar ett filter kan det ta några minuter innan ändringen visas i data pipelinen.