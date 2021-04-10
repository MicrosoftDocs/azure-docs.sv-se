---
title: Aktivera och efterfråga diagnostikloggar
titleSuffix: Azure Digital Twins
description: Se hur du aktiverar loggning med diagnostikinställningar och frågar loggarna för omedelbar visning.
author: baanders
ms.author: baanders
ms.date: 2/24/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 08db4d92da5213b1ce1b79867650da9df8c38ee4
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385087"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Felsöka Azure Digitals sammanflätade: diagnostikloggning

Azure Digitals dubblare kan samla in loggar för din tjänst instans för att övervaka prestanda, åtkomst och andra data. Du kan använda dessa loggar för att få en uppfattning om vad som händer i din Azure Digital-instansen och utföra rotor Saks analys av problem utan att behöva kontakta Azure-supporten.

Den här artikeln visar hur du [**konfigurerar diagnostikinställningar**](#turn-on-diagnostic-settings) i [Azure Portal](https://portal.azure.com) för att börja samla in loggar från din Azure Digital-instansen. Du kan också ange var loggarna ska lagras (till exempel Log Analytics eller ett valfritt lagrings konto).

Den här artikeln innehåller också listor över alla [logg kategorier](#log-categories) och [logg scheman](#log-schemas) som samlas in av Azure Digitals.

När du har konfigurerat loggar kan du också [**skicka frågor till loggarna**](#view-and-query-logs) för att snabbt samla in anpassade insikter.

## <a name="turn-on-diagnostic-settings"></a>Aktivera diagnostikinställningar 

Aktivera diagnostikinställningar för att börja samla in loggar på din Azure Digital-instansen. Du kan också välja den plats där de exporterade loggarna ska lagras. Så här aktiverar du diagnostikinställningar för din Azure Digital-instansen.

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till din Azure Digital-instansen. Du kan hitta det genom att skriva dess namn i portalens sökfält. 

2. Välj **diagnostikinställningar** på menyn och **Lägg sedan till en diagnostisk inställning**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Skärm bild som visar sidan diagnostikinställningar och knappen som ska läggas till" lightbox="media/troubleshoot-diagnostics/diagnostic-settings.png":::

3. Fyll i följande värden på sidan som följer:
     * **Namn på diagnos inställning**: ge diagnostikinställningar ett namn.
     * **Kategori information**: Välj vilka åtgärder som du vill övervaka och markera kryss rutorna för att aktivera diagnostik för dessa åtgärder. De åtgärder som diagnostikinställningar kan rapportera om är:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        Mer information om dessa kategorier och den information som de innehåller finns i avsnittet [*logg kategorier*](#log-categories) nedan.
     * **Mål information**: Välj var du vill skicka loggarna. Du kan välja en valfri kombination av de tre alternativen:
        - Skicka till Log Analytics
        - Arkivera till ett lagringskonto
        - Strömma till en händelsehubb

        Du kan bli ombedd att fylla i ytterligare information om de behövs för val av mål.  
    
4. Spara de nya inställningarna. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Skärm bild som visar sidan för diagnostikinställningar där användaren har fyllt i ett namn för diagnostikinställningar och gjort några kryss rutor för kategori information och mål information. Knappen Spara är markerad." lightbox="media/troubleshoot-diagnostics/diagnostic-settings-details.png":::

Nya inställningar börjar gälla om 10 minuter. Därefter visas loggar i det konfigurerade målet tillbaka på sidan **diagnostikinställningar** för din instans. 

Mer detaljerad information om diagnostikinställningar och deras installations alternativ finns i [*skapa diagnostikinställningar för att skicka plattforms loggar och mått till olika mål*](../azure-monitor/essentials/diagnostic-settings.md).

## <a name="log-categories"></a>Logg kategorier

Här är mer information om de kategorier av loggar som Azure Digitals samlar in.

| Loggkategori | Beskrivning |
| --- | --- |
| ADTModelsOperation | Logga alla API-anrop som rör modeller |
| ADTQueryOperation | Logga alla API-anrop som rör frågor |
| ADTEventRoutesOperation | Logga alla API-anrop som hör till händelse vägar samt utgående av händelser från Azure Digitals dubbla till en slut punkts tjänst som Event Grid, Event Hubs och Service Bus |
| ADTDigitalTwinsOperation | Logga alla API-anrop som rör enskilda dubbla |

Varje logg kategori består av åtgärder av Skriv-, Läs-, borttagnings-och åtgärds typ.  Dessa mappar till REST API samtal på följande sätt:

| Händelsetyp | REST API åtgärder |
| --- | --- |
| Skriva | PLACERING och korrigering |
| Läs | GET |
| Ta bort | DELETE |
| Action | POST |

Här är en omfattande lista över åtgärderna och motsvarande [Azure Digital-meddelanden REST API samtal](/rest/api/azure-digitaltwins/) som loggas i varje kategori. 

>[!NOTE]
> Varje logg kategori innehåller flera åtgärder/REST API-anrop. I tabellen nedan mappas varje logg kategori till alla åtgärder/REST API samtal under den tills nästa logg kategori visas. 

| Loggkategori | Åtgärd | REST API-anrop och andra händelser |
| --- | --- | --- |
| ADTModelsOperation | Microsoft. DigitalTwins/modeller/Skriv | Uppdaterings-API för digitala dubbla modeller |
|  | Microsoft. DigitalTwins/modeller/läsa | Digitala dubbla modeller får av ID-och list-API: er |
|  | Microsoft. DigitalTwins/Models/Delete | Digitala dubbla modeller ta bort API |
|  | Microsoft. DigitalTwins/modeller/åtgärd | Digitala dubbla modeller Lägg till API |
| ADTQueryOperation | Microsoft. DigitalTwins/fråga/åtgärd | Frågans dubbla API |
| ADTEventRoutesOperation | Microsoft. DigitalTwins/eventroutes/Write | Händelse vägar Lägg till API |
|  | Microsoft. DigitalTwins/eventroutes/Read | Händelse vägar hämtas efter ID och lista API: er |
|  | Microsoft. DigitalTwins/eventroutes/Delete | Borttagnings-API för händelse vägar |
|  | Microsoft. DigitalTwins/eventroutes/Action | Ett problem uppstod vid försök att publicera händelser till en slut punkts tjänst (inte ett API-anrop) |
| ADTDigitalTwinsOperation | Microsoft. DigitalTwins/DigitalTwins/Write | Digitals, Lägg till, Lägg till relation, uppdatera, uppdatera komponent |
|  | Microsoft. DigitalTwins/DigitalTwins/Read | Digitals, Hämta efter ID, Hämta komponent, Hämta relation efter ID, lista inkommande relationer, list relationer |
|  | Microsoft. DigitalTwins/DigitalTwins/Delete | Digitals ta bort, ta bort relation |
|  | Microsoft. DigitalTwins/DigitalTwins/Action | Digitala dubblare skicka telemetri för komponent, skicka telemetri |

## <a name="log-schemas"></a>Logg scheman 

Varje logg kategori har ett schema som definierar hur händelser i den kategorin rapporteras. Varje enskild loggpost lagras som text och formateras som en JSON-blob. Fälten i loggen och exempel på JSON-kroppar anges för varje loggtyp nedan. 

`ADTDigitalTwinsOperation`, `ADTModelsOperation` , och `ADTQueryOperation` Använd ett konsekvent API-logg schema. `ADTEventRoutesOperation` utökar schemat så att det innehåller ett `endpointName` fält i egenskaper.

### <a name="api-log-schemas"></a>API-logg scheman

Det här logg schemat är konsekvent för `ADTDigitalTwinsOperation` , `ADTModelsOperation` , `ADTQueryOperation` . Samma schema används också för `ADTEventRoutesOperation` , med **undantag** för `Microsoft.DigitalTwins/eventroutes/action` Åtgärds namnet (mer information om schemat finns i nästa avsnitt, [*utgående logg scheman*](#egress-log-schemas)).

Schemat innehåller information som är relevant för API-anrop till en digital Azure-instans.

Här är fält-och egenskaps beskrivningarna för API-loggar.

| Fältnamn | Datatyp | Beskrivning |
|-----|------|-------------|
| `Time` | DateTime | Datum och tid då händelsen inträffade, i UTC |
| `ResourceId` | Sträng | Azure Resource Manager resurs-ID för resursen där händelsen ägde rum |
| `OperationName` | Sträng  | Typ av åtgärd som utförs under händelsen |
| `OperationVersion` | Sträng | Den API-version som används under händelsen |
| `Category` | Sträng | Typ av resurs som genereras |
| `ResultType` | Sträng | Händelsens resultat |
| `ResultSignature` | Sträng | HTTP-statuskod för händelsen |
| `ResultDescription` | Sträng | Ytterligare information om händelsen |
| `DurationMs` | Sträng | Hur lång tid det tog att utföra händelsen i millisekunder |
| `CallerIpAddress` | Sträng | En maskerad käll-IP-adress för händelsen |
| `CorrelationId` | GUID | Kunden tillhandahöll en unik identifierare för händelsen |
| `ApplicationId` | GUID | Program-ID som används i Bearer-auktorisering |
| `Level` | Int | Händelsens allvarlighets grad |
| `Location` | Sträng | Den region där händelsen ägde rum |
| `RequestUri` | URI | Slut punkten som används under händelsen |
| `TraceId` | Sträng | `TraceId`, som en del av [W3C's trace context](https://www.w3.org/TR/trace-context/). ID: t för hela spårningen som används för att unikt identifiera en distribuerad spårning mellan system. |
| `SpanId` | Sträng | `SpanId` som en del av [W3C's trace context](https://www.w3.org/TR/trace-context/). ID för den här begäran i spårningen. |
| `ParentId` | Sträng | `ParentId` som en del av [W3C's trace context](https://www.w3.org/TR/trace-context/). En begäran utan ett överordnat ID är roten för spårningen. |
| `TraceFlags` | Sträng | `TraceFlags` som en del av [W3C's trace context](https://www.w3.org/TR/trace-context/). Styr spårnings flaggor som sampling, spårnings nivå osv. |
| `TraceState` | Sträng | `TraceState` som en del av [W3C's trace context](https://www.w3.org/TR/trace-context/). Ytterligare leverantörsspecifik spårnings identifierings information som ska sträckas över olika distribuerade spårnings system. |

Nedan visas exempel på JSON-instanser för dessa typer av loggar.

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-10-31",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": 8,
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-10-31",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "80",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-10-31",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

Här är ett exempel på en JSON-brödtext för en `ADTEventRoutesOperation` som **inte** är av `Microsoft.DigitalTwins/eventroutes/action` typen (mer information om schemat finns i nästa avsnitt, [*utgående logg scheman*](#egress-log-schemas)).

```json
  {
    "time": "2020-10-30T22:18:38.0708705Z",
    "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
    "operationName": "Microsoft.DigitalTwins/eventroutes/write",
    "operationVersion": "2020-10-31",
    "category": "EventRoutesOperation",
    "resultType": "Success",
    "resultSignature": "204",
    "resultDescription": "",
    "durationMs": 42,
    "callerIpAddress": "212.100.32.*",
    "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
    "identity": {
      "claims": {
        "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
      }
    },
    "level": "4",
    "location": "southcentralus",
    "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/EventRoutes/egressRouteForEventHub?api-version=2020-10-31",
    "properties": {},
    "traceContext": {
      "traceId": "95ff77cfb300b04f80d83e64d13831e7",
      "spanId": "b630da57026dd046",
      "parentId": "9f0de6dadae85945",
      "traceFlags": "01",
      "tracestate": "k1=v1,k2=v2"
    }
  },
```

### <a name="egress-log-schemas"></a>Utgående logg scheman

Detta är schemat för loggar som är `ADTEventRoutesOperation` speciella för `Microsoft.DigitalTwins/eventroutes/action` Åtgärds namnet. Dessa innehåller information som rör undantag och API-åtgärder kring utgående slut punkter som är anslutna till en Azure Digitals-instans.

|Fältnamn | Datatyp | Beskrivning |
|-----|------|-------------|
| `Time` | DateTime | Datum och tid då händelsen inträffade, i UTC |
| `ResourceId` | Sträng | Azure Resource Manager resurs-ID för resursen där händelsen ägde rum |
| `OperationName` | Sträng  | Typ av åtgärd som utförs under händelsen |
| `Category` | Sträng | Typ av resurs som genereras |
| `ResultDescription` | Sträng | Ytterligare information om händelsen |
| `CorrelationId` | GUID | Kunden tillhandahöll en unik identifierare för händelsen |
| `ApplicationId` | GUID | Program-ID som används i Bearer-auktorisering |
| `Level` | Int | Händelsens allvarlighets grad |
| `Location` | Sträng | Den region där händelsen ägde rum |
| `TraceId` | Sträng | `TraceId`, som en del av [W3C's trace context](https://www.w3.org/TR/trace-context/). ID: t för hela spårningen som används för att unikt identifiera en distribuerad spårning mellan system. |
| `SpanId` | Sträng | `SpanId` som en del av [W3C's trace context](https://www.w3.org/TR/trace-context/). ID för den här begäran i spårningen. |
| `ParentId` | Sträng | `ParentId` som en del av [W3C's trace context](https://www.w3.org/TR/trace-context/). En begäran utan ett överordnat ID är roten för spårningen. |
| `TraceFlags` | Sträng | `TraceFlags` som en del av [W3C's trace context](https://www.w3.org/TR/trace-context/). Styr spårnings flaggor som sampling, spårnings nivå osv. |
| `TraceState` | Sträng | `TraceState` som en del av [W3C's trace context](https://www.w3.org/TR/trace-context/). Ytterligare leverantörsspecifik spårnings identifierings information som ska sträckas över olika distribuerade spårnings system. |
| `EndpointName` | Sträng | Namnet på den utgående slut punkten som skapades i Azure Digitals dubbla |

Nedan visas exempel på JSON-instanser för dessa typer av loggar.

#### <a name="adteventroutesoperation-for-microsoftdigitaltwinseventroutesaction"></a>ADTEventRoutesOperation för Microsoft. DigitalTwins/eventroutes/Action

Här är ett exempel på en JSON-text `ADTEventRoutesOperation` som är av `Microsoft.DigitalTwins/eventroutes/action` typen.

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "operationVersion": "",
  "category": "EventRoutesOperation",
  "resultType": "",
  "resultSignature": "",
  "resultDescription": "Unable to send EventHub message to [myPath] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "durationMs": -1,
  "callerIpAddress": "",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "",
  "properties": {
    "endpointName": "myEventHub"
  },
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
},
```

## <a name="view-and-query-logs"></a>Visa och fråga efter loggar

Tidigare i den här artikeln konfigurerade du typerna av loggar för att lagra och ange deras lagrings plats.

Om du vill felsöka problem med att generera insikter från dessa loggar kan du generera **anpassade frågor**. För att komma igång kan du också dra nytta av några exempel frågor som tillhandahålls av tjänsten, som åtgärdar vanliga frågor som kunder kan ha på sin instans.

Så här frågar du loggarna efter din instans.

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till din Azure Digital-instansen. Du kan hitta det genom att skriva dess namn i portalens sökfält. 

2. Öppna sidan logg fråga genom att välja **loggar** på menyn. Sidan öppnas till ett fönster som kallas *frågor*.

    :::image type="content" source="media/troubleshoot-diagnostics/logs.png" alt-text="Skärm bild som visar sidan loggar för en digital Azure-instans. Den är överdefinierad med ett fråge fönster som visar fördefinierade frågor som heter efter olika logg alternativ, till exempel DigitalTwin API-svars tid och API-svars tid för modell" lightbox="media/troubleshoot-diagnostics/logs.png":::

    Dessa är färdiga exempel frågor skrivna för olika loggar. Du kan välja en av frågorna för att läsa in den i Frågeredigeraren och köra den för att se dessa loggar för din instans.

    Du kan också stänga fönstret *frågor* utan att köra något för att gå direkt till sidan Frågeredigeraren, där du kan skriva eller redigera anpassad kod.

3. När du har avslutat fönstret *frågor* visas huvud sidan för Frågeredigeraren. Här kan du Visa och redigera texten i exempel frågorna eller skriva egna frågor från grunden.
    :::image type="content" source="media/troubleshoot-diagnostics/logs-query.png" alt-text="Skärm bild som visar sidan loggar för en digital Azure-instans. Fönstret frågor är borta och det finns en lista över olika loggar i ett redigerings fönster som visar redigerbar kod och ett fönster som visar frågor historik." lightbox="media/troubleshoot-diagnostics/logs-query.png":::

    I det vänstra fönstret, 
    - Fliken *tabeller* visar de olika Azures digitala dubbla databas [Kategorier](#log-categories) som är tillgängliga för användning i dina frågor. 
    - Fliken *frågor* innehåller exempel frågor som du kan läsa in i redigeraren.
    - På fliken *filter* kan du anpassa en filtrerad vy av de data som frågan returnerar.

Mer detaljerad information om logg frågor och hur du skriver dem finns [*i Översikt över logg frågor i Azure Monitor*](../azure-monitor/logs/log-query-overview.md).

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du konfigurerar diagnostik finns i [*samla in och använda loggdata från dina Azure-resurser*](../azure-monitor/essentials/platform-logs-overview.md).
* Information om Azure Digitals dubbla mått finns i [*fel sökning: Visa mått med Azure Monitor*](troubleshoot-metrics.md).
* Information om hur du aktiverar aviseringar för dina mått finns i [*fel sökning: Konfigurera aviseringar*](troubleshoot-alerts.md).