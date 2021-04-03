---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 2ccff72be66a88b9bf0a5e9eb9c29ade8397804b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96356201"
---
Fel som har Aktiver ATS i en Azure Functions kan komma från något av följande ursprung:

- Användning av inbyggda Azure Functions [utlösare och bindningar](..\articles\azure-functions\functions-triggers-bindings.md)
- Anrop till API: er för underliggande Azure-tjänster
- Anrop till REST-slutpunkter
- Anrop till klient bibliotek, paket eller API: er från tredje part

Följande metoder för bra fel hantering är viktigt för att undvika förlust av data eller missade meddelanden. Rekommenderade fel hanterings metoder omfattar följande åtgärder:

- [Aktivera Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Använd strukturerad fel hantering](#use-structured-error-handling)
- [Design för idempotens](../articles/azure-functions/functions-idempotent.md)
- [Implementera principer för återförsök](#retry-policies-preview) (där det är lämpligt)

### <a name="use-structured-error-handling"></a>Använd strukturerad fel hantering

Det är viktigt att samla in och logga fel för att övervaka hälso tillståndet för programmet. Den översta nivån i en funktions kod ska innehålla ett try/catch-block. I catch-blocket kan du samla in och logga fel.

## <a name="retry-policies-preview"></a>Principer för återförsök (förhands granskning)

En princip för återförsök kan definieras för alla funktioner för utlösnings typ i din Function-app.  Principen för återförsök kör om en funktion tills körningen lyckades eller tills det maximala antalet återförsök inträffar.  Principer för återförsök kan definieras för alla funktioner i en app eller för enskilda funktioner.  Som standard försöker en Function-app inte göra om meddelanden (förutom de [angivna utlösare som har en princip för återförsök på utlösnings källan](#using-retry-support-on-top-of-trigger-resilience)).  En princip för återförsök utvärderas varje gång en körning resulterar i ett undantag som inte kan identifieras.  Som bästa praxis bör du fånga alla undantag i koden och utlösa eventuella fel som skulle leda till ett nytt försök.  Det går inte att skriva Event Hubs och Azure Cosmos DB kontroll punkter förrän återförsöks principen för körningen har slutförts, vilket innebär att förloppet på den partitionen pausas tills den aktuella batchen har slutförts.

### <a name="retry-policy-options"></a>Försök igen princip alternativ

Följande alternativ är tillgängliga för att definiera en princip för återförsök.

Maximalt antal **återförsök** är det maximala antalet gånger som en körning har gjorts om före eventuella fel. Ett värde på `-1` innebär att du vill försöka på obestämd tid. Det aktuella antalet återförsök lagras i instansens minne. Det är möjligt att en instans har ett fel mellan nya försök.  När en instans Miss lyckas under en princip för återförsök försvinner antalet återförsök.  Om det finns instans fel, utlösare som Event Hubs, Azure Cosmos DB och Queue Storage kan återuppta bearbetningen och försöka utföra batchen igen på en ny instans, med antalet nya försök återställt till noll.  Andra utlösare, t. ex. HTTP och timer, återupptas inte på en ny instans.  Det innebär att max antalet återförsök är bästa möjliga, och i vissa sällsynta fall kan en körning göras mer än det högsta, eller för utlösare som HTTP och timer-försök som är mindre än max värdet.

**Återförsöks strategi** styr hur återförsök fungerar.  Följande är två alternativ för omförsök som stöds:

| Alternativ | Beskrivning|
|---|---|
|**`fixedDelay`**| En angiven tids period får förflyta mellan varje nytt försök,|
| **`exponentialBackoff`**| Det första återförsöket väntar för minsta fördröjning. Vid efterföljande återförsök läggs tiden till exponentiellt till den inledande varaktigheten för varje nytt försök tills den maximala fördröjningen uppnås.  Exponentiella säkerhets kopieringar lägger till en liten slump mellan att fördröja nya försök i stora data flödes scenarier.|

#### <a name="app-level-configuration"></a>Konfiguration av app-nivå

En princip för återförsök kan definieras för alla funktioner i en app [med hjälp av `host.json` filen](../articles/azure-functions/functions-host-json.md#retry). 

#### <a name="function-level-configuration"></a>Konfiguration av funktions nivå

En princip för återförsök kan definieras för en viss funktion.  En funktions bestämd konfiguration prioriteras över konfiguration på program nivå.

#### <a name="fixed-delay-retry"></a>Försök till fast fördröjning

# <a name="c"></a>[C#](#tab/csharp)

Nya försök kräver NuGet-paketet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23

```csharp
[FunctionName("EventHubTrigger")]
[FixedDelayRetry(5, "00:00:10")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Här är principen för återförsök i *function.jspå* filen:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
# <a name="javascript"></a>[JavaScript](#tab/javascript)

Här är principen för återförsök i *function.jspå* filen:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Här är principen för återförsök i *function.jspå* filen:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Här är principen för återförsök i *function.jspå* filen:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Här är principen för återförsök i *function.jspå* filen:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
---

#### <a name="exponential-backoff-retry"></a>Nytt försök för exponentiell backoff

# <a name="c"></a>[C#](#tab/csharp)

Nya försök kräver NuGet-paketet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23

```csharp
[FunctionName("EventHubTrigger")]
[ExponentialBackoffRetry(5, "00:00:04", "00:15:00")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Här är principen för återförsök i *function.jspå* filen:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Här är principen för återförsök i *function.jspå* filen:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Här är principen för återförsök i *function.jspå* filen:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Här är principen för återförsök i *function.jspå* filen:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Här är principen för återförsök i *function.jspå* filen:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```
---

|function.jspå egenskap  |Attributets egenskap | Beskrivning |
|---------|---------|---------| 
|strategi|saknas|Krävs. Återförsöksstrategin som ska användas. Giltiga värden är `fixedDelay` eller `exponentialBackoff` .|
|maxRetryCount|saknas|Krävs. Maximalt antal tillåtna försök per funktions körning. `-1` innebär ett nytt försök på obestämd tid.|
|delayInterval|saknas|Fördröjningen som ska användas mellan återförsök när du använder en `fixedDelay` strategi.|
|minimumInterval|saknas|Den minsta fördröjningen för återförsök när du använder `exponentialBackoff` strategin.|
|maximumInterval|saknas|Den maximala fördröjningen vid nya försök när du använder `exponentialBackoff` strategin.| 

### <a name="retry-limitations-during-preview"></a>Begränsningar för återförsök under för hands versionen

- För .NET-projekt kan du behöva hämta en version av [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23 manuellt.
- I förbruknings planen kan appen skalas ned till noll samtidigt som du försöker utföra de sista meddelandena i en kö.
- I förbruknings planen kan appen skalas ned när du utför nya försök.  För bästa resultat väljer du ett återförsöksintervall <= 00:01:00 och <= 5 försök.

## <a name="using-retry-support-on-top-of-trigger-resilience"></a>Använda stöd för återförsök ovanpå utlösnings återhämtning

Funktionen för att försöka köra en princip oberoende av eventuella återförsök eller återhämtning som utlösaren tillhandahåller.  Principen för att försöka utföra endast lager på ett elastiskt återställnings försök.  Om du till exempel använder Azure Service Bus har köer som standard ett meddelande leverans på 10.  Standardvärdet för leverans innebär att efter 10 försök att leverera ett Queue-meddelande kommer Service Bus att obeställbara meddelandet.  Du kan definiera en princip för återförsök för en funktion som har en Service Bus-utlösare, men försöken kommer att skiktas över Service Bus leverans försök.  

Om du till exempel använde standard antalet Service Bus leverans antal 10, och definierat en princip för återförsök för funktion på 5.  Meddelandet skulle först ta bort kön och öka Service Bus-leveransens konto till 1.  Om varje körning Miss lyckas, efter fem försök att utlösa samma meddelande, skulle meddelandet markeras som överlappande.  Service Bus kommer omedelbart att köa meddelandet, det skulle utlösa funktionen och öka leverans antalet till 2.  Slutligen, efter 50 eventuella försök (10 Service Bus-leveranser * fem funktions försök per leverans), skulle meddelandet överges och utlöser en obeställbara meddelanden på Service Bus.

> [!WARNING]
> Vi rekommenderar inte att du anger antalet leveranser för en utlösare som Service Bus köer till 1, vilket innebär att meddelandet inte kommer att besvaras omedelbart efter en enda funktion för nya försök.  Detta beror på att utlösare ger återhämtning med återförsök, medan principen för att försöka igen är bästa möjliga och kan leda till mindre än det önskade antalet återförsök.

### <a name="triggers-with-additional-resiliency-or-retries"></a>Utlösare med ytterligare återhämtning eller återförsök

Följande utlösare stöder återförsök vid utlösarens Källa:

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure Queue Storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (kö/ämne)](../articles/azure-functions/functions-bindings-service-bus.md)

Som standard begär de flesta utlösare nya försök upp till fem gånger. Efter det femte försöket kommer både Azure Queue Storage att skriva ett meddelande till en [Poison-kö](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages).  Standard principen Service Bus kö och ämnes princip skriver ett meddelande till en [kö för obeställbara meddelanden](../articles/service-bus-messaging/service-bus-dead-letter-queues.md) efter 10 försök.
