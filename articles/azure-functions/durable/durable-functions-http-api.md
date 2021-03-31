---
title: 'HTTP-API: er i Durable Functions-Azure Functions'
description: 'Lär dig hur du implementerar HTTP-API: er i Durable Functions-tillägget för Azure Functions.'
author: cgillum
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 4e4081ecca4714c713d105d363a83a4f96a0d3fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84697851"
---
# <a name="http-api-reference"></a>HTTP API-referens

Durable Functions-tillägget visar en uppsättning inbyggda HTTP-API: er som kan användas för att utföra hanterings åtgärder på [Dirigerings](durable-functions-types-features-overview.md#orchestrator-functions)-, [entitet](durable-functions-types-features-overview.md#entity-functions)-och [aktivitets nav](durable-functions-task-hubs.md). Dessa HTTP API: er är utöknings bara Webhooks som har auktoriserats av Azure Functions värden, men som hanteras direkt av Durable Functions-tillägget.

Alla HTTP-API: er som implementeras av tillägget kräver följande parametrar. Data typen för alla parametrar är `string` .

| Parameter        | Parameter typ  | Beskrivning |
|------------------|-----------------|-------------|
| **`taskHub`**    | Frågesträng    | Namnet på [aktivitets navet](durable-functions-task-hubs.md). Om detta inte anges antas den aktuella Function-appens aktivitets namn. |
| **`connection`** | Frågesträng    | **Namnet** på anslutnings strängen för lagrings kontot. Om inget värde anges antas standard anslutnings strängen för Function-appen. |
| **`systemKey`**  | Frågesträng    | Den auktoriseringskod som krävs för att anropa API: et. |

`systemKey` är en auktoriseringspost som genereras automatiskt av Azure Functions-värden. Den ger särskilt åtkomst till de ständiga API: erna för aktivitets tillägg och kan hanteras på samma sätt som [andra auktoriseringsarkiv](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Du kan generera URL: er som innehåller rätt `taskHub` , `connection` och `systemKey` frågesträngs värden med hjälp av [Dirigerings](durable-functions-bindings.md#orchestration-client) -API: er för bindning, till exempel `CreateCheckStatusResponse` API: `CreateHttpManagementPayload` er i .net eller `createCheckStatusResponse` API: `createHttpManagementPayload` er i Java Script.

I följande avsnitt beskrivs de HTTP-API: er som stöds av tillägget och innehåller exempel på hur de kan användas.

## <a name="start-orchestration"></a>Starta dirigering

Startar körning av en ny instans av den angivna Orchestrator-funktionen.

### <a name="request"></a>Förfrågan

För version 1. x av Functions-körningen formateras begäran enligt följande (flera rader visas för tydlighetens skull):

```http
POST /admin/extensions/DurableTaskExtension/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

I version 2. x av Functions-körningen har URL-formatet samma parametrar, men med något annorlunda prefix:

```http
POST /runtime/webhooks/durabletask/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

Parametrarna för begäran för detta API inkluderar den standard uppsättning som nämnts tidigare samt följande unika parametrar:

| Fält              | Parametertyp  | Beskrivning |
|--------------------|-----------------|-------------|
| **`functionName`** | URL             | Namnet på Orchestrator-funktionen som ska startas. |
| **`instanceId`**   | URL             | Valfri parameter. ID: t för Orchestration-instansen. Om inget värde anges startar Orchestrator-funktionen med ett slumpmässigt instans-ID. |
| **`{content}`**    | Begär innehåll | Valfritt. Den JSON-formaterade Orchestrator-funktionen inmatade. |

### <a name="response"></a>Svarsåtgärder

Flera möjliga status kod värden kan returneras.

* **HTTP 202 (accepterad)**: den angivna Orchestrator-funktionen har schemalagts för att börja köras. `Location`Svars huvudet innehåller en URL för avsökning av Orchestration-status.
* **HTTP 400 (felaktig begäran)**: den angivna Orchestrator-funktionen finns inte, det angivna instans-ID: t var inte giltigt eller så var begärd innehåll inte en giltig JSON.

Följande är en exempel förfrågan som startar en `RestartVMs` Orchestrator-funktion och som inkluderar nytto last för JSON-objekt:

```http
POST /runtime/webhooks/durabletask/orchestrators/RestartVMs?code=XXX
Content-Type: application/json
Content-Length: 83

{
    "resourceGroup": "myRG",
    "subscriptionId": "111deb5d-09df-4604-992e-a968345530a9"
}
```

Svarets nytto last för **HTTP 202-** fall är ett JSON-objekt med följande fält:

| Fält                       | Beskrivning                          |
|-----------------------------|--------------------------------------|
| **`id`**                    |ID: t för Orchestration-instansen. |
| **`statusQueryGetUri`**     |Status-URL: en för Orchestration-instansen. |
| **`sendEventPostUri`**      |URL: en "öka händelse" för Orchestration-instansen. |
| **`terminatePostUri`**      |"Avsluta"-URL: en för Orchestration-instansen. |
| **`purgeHistoryDeleteUri`** |URL: en för "Rensa historik" för Orchestration-instansen. |
| **`rewindPostUri`**         |förhandsgranskningsvyn URL: en "spola tillbaka" för Orchestration-instansen. |

Data typen för alla fält är `string` .

Här är ett exempel på en nytto last för en Dirigerings instans med `abc123` som sitt ID (formaterat för läsbarhet):

```http
{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

HTTP-svaret är avsett att vara kompatibelt med *avsöknings konsument mönstret*. Den innehåller också följande viktiga svarshuvuden:

* **Plats**: URL: en för status slut punkten. Denna URL innehåller samma värde som `statusQueryGetUri` fältet.
* **Nytt försök-efter**: antalet sekunder att vänta mellan avsöknings åtgärderna. Standardvärdet är `10`.

Mer information om det asynkrona HTTP-avsöknings mönstret finns i dokumentationen för [http async operation tracking](durable-functions-http-features.md#async-operation-tracking) .

## <a name="get-instance-status"></a>Hämta instans status

Hämtar status för en angiven Orchestration-instans.

### <a name="request"></a>Förfrågan

För version 1. x av Functions-körningen formateras begäran enligt följande (flera rader visas för tydlighetens skull):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

I version 2. x av Functions-körningen har URL-formatet samma parametrar, men med något annorlunda prefix:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Parametrarna för begäran för detta API inkluderar den standard uppsättning som nämnts tidigare samt följande unika parametrar:

| Fält                   | Parametertyp  | Beskrivning |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | ID: t för Orchestration-instansen. |
| **`showInput`**         | Frågesträng    | Valfri parameter. Om detta är inställt på `false` , inkluderas inte funktions inmatning i svarets nytto Last.|
| **`showHistory`**       | Frågesträng    | Valfri parameter. Om detta är inställt på `true` , tas Dirigerings körnings historiken med i svars nytto lasten.|
| **`showHistoryOutput`** | Frågesträng    | Valfri parameter. Om detta är inställt på `true` , kommer funktionen utdata att inkluderas i körnings historiken för dirigering.|
| **`createdTimeFrom`**   | Frågesträng    | Valfri parameter. Filtrerar listan med returnerade instanser som skapades vid eller efter angiven ISO8601-tidsstämpel.|
| **`createdTimeTo`**     | Frågesträng    | Valfri parameter. Filtrerar listan med returnerade instanser som skapades vid eller före den angivna ISO8601-tidsstämpeln.|
| **`runtimeStatus`**     | Frågesträng    | Valfri parameter. Filtrerar listan över returnerade instanser baserat på deras körnings status. Om du vill se en lista över möjliga körnings status värden, se artikeln [frågor om instanser](durable-functions-instance-management.md) . |

### <a name="response"></a>Svarsåtgärder

Flera möjliga status kod värden kan returneras.

* **HTTP 200 (OK)**: den angivna instansen är i ett slutfört tillstånd.
* **HTTP 202 (accepterad)**: den angivna instansen pågår.
* **HTTP 400 (felaktig begäran)**: den angivna instansen misslyckades eller avslutades.
* **HTTP 404 (hittades inte)**: den angivna instansen finns inte eller så har den inte startats.
* **HTTP 500 (internt Server fel)**: den angivna instansen misslyckades med ett ohanterat undantag.

Svars nytto lasten för **http 200-** och **http 202** -fall är ett JSON-objekt med följande fält:

| Fält                 | Datatyp | Beskrivning |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | sträng    | Körnings status för instansen. Värdena omfattar *körning*, *väntar*, *misslyckade*, *avbrutna*, *avslutade*, *slutförda*. |
| **`input`**           | JSON      | JSON-data som används för att initiera instansen. Det här fältet är `null` om frågesträngparametern `showInput` har angetts till `false` .|
| **`customStatus`**    | JSON      | De JSON-data som används för anpassad Dirigerings status. Det här fältet är `null` om inget anges. |
| **`output`**          | JSON      | Instansens JSON-utdata. Det här fältet är `null` om instansen inte är i ett slutfört tillstånd. |
| **`createdTime`**     | sträng    | Tiden då instansen skapades. Använder utökad ISO 8601-notation. |
| **`lastUpdatedTime`** | sträng    | Tiden då instansen senast sparades. Använder utökad ISO 8601-notation. |
| **`historyEvents`**   | JSON      | En JSON-matris som innehåller Dirigerings körnings historiken. Det här fältet är `null` om inte frågesträngparametern `showHistory` har angetts till `true` . |

Här är ett exempel på en nytto last för svar på begäran, inklusive körnings historik och aktivitets utdata (formaterade för läsbarhet):

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

**HTTP 202-** svaret innehåller också ett rubrik för **plats** svar som refererar till samma URL som det `statusQueryGetUri` angivna fältet ovan.

## <a name="get-all-instances-status"></a>Hämta alla instans status

Du kan också fråga efter status för alla instanser genom att ta bort `instanceId` från begäran om att hämta instans status. I det här fallet är de grundläggande parametrarna samma som "Hämta instans status". Parametrar för frågesträng för filtrering stöds också.

En sak att komma ihåg är att `connection` och `code` är valfria. Om du har anonym autentisering för funktionen är det `code` inte nödvändigt.
Om du inte vill använda en annan lagrings anslutnings sträng än den som har definierats i AzureWebJobsStorage-appen, kan du utan risk ignorera parametern för anslutnings frågesträngen.

### <a name="request"></a>Förfrågan

För version 1. x av Functions-körningen formateras begäran enligt följande (flera rader visas för tydlighetens skull):

```http
GET /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

I version 2. x av Functions-körningen har URL-formatet samma parametrar, men med något annorlunda prefix:

```http
GET /runtime/webhooks/durableTask/instances?
    taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

Parametrarna för begäran för detta API inkluderar den standard uppsättning som nämnts tidigare samt följande unika parametrar:

| Fält                   | Parametertyp  | Beskrivning |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | ID: t för Orchestration-instansen. |
| **`showInput`**         | Frågesträng    | Valfri parameter. Om detta är inställt på `false` , inkluderas inte funktions inmatning i svarets nytto Last.|
| **`showHistory`**       | Frågesträng    | Valfri parameter. Om detta är inställt på `true` , tas Dirigerings körnings historiken med i svars nytto lasten.|
| **`showHistoryOutput`** | Frågesträng    | Valfri parameter. Om detta är inställt på `true` , kommer funktionen utdata att inkluderas i körnings historiken för dirigering.|
| **`createdTimeFrom`**   | Frågesträng    | Valfri parameter. Filtrerar listan med returnerade instanser som skapades vid eller efter angiven ISO8601-tidsstämpel.|
| **`createdTimeTo`**     | Frågesträng    | Valfri parameter. Filtrerar listan med returnerade instanser som skapades vid eller före den angivna ISO8601-tidsstämpeln.|
| **`runtimeStatus`**     | Frågesträng    | Valfri parameter. Filtrerar listan över returnerade instanser baserat på deras körnings status. Om du vill se en lista över möjliga körnings status värden, se artikeln [frågor om instanser](durable-functions-instance-management.md) . |
| **`top`**               | Frågesträng    | Valfri parameter. Begränsar antalet instanser som returneras av frågan. |

### <a name="response"></a>Svarsåtgärder

Här är ett exempel på svars nytto laster, inklusive Orchestration-status (formaterad för läsbarhet):

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> Den här åtgärden kan vara dyrare vad gäller Azure Storage I/O om det finns många rader i instans tabellen. Mer information om instans tabellen finns i dokumentationen om [prestanda och skala i Durable functions (Azure Functions)](durable-functions-perf-and-scale.md#instances-table) .
>

Om det finns fler resultat returneras en fortsättnings-token i svars huvudet.  Namnet på rubriken är `x-ms-continuation-token` .

Om du ställer in värdet för värde för fortsättnings-token i nästa begär ande huvud kan du hämta nästa sida med resultat. Namnet på begär ande rubriken är också `x-ms-continuation-token` .

## <a name="purge-single-instance-history"></a>Rensa enstaka instans historik

Tar bort historiken och relaterade artefakter för en angiven Dirigerings instans.

### <a name="request"></a>Förfrågan

För version 1. x av Functions-körningen formateras begäran enligt följande (flera rader visas för tydlighetens skull):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

I version 2. x av Functions-körningen har URL-formatet samma parametrar, men med något annorlunda prefix:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Parametrarna för begäran för detta API inkluderar den standard uppsättning som nämnts tidigare samt följande unika parametrar:

| Fält             | Parametertyp  | Beskrivning |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID: t för Orchestration-instansen. |

### <a name="response"></a>Svarsåtgärder

Följande HTTP status kod värden kan returneras.

* **HTTP 200 (OK)**: instans historiken har rensats.
* **HTTP 404 (hittades inte)**: den angivna instansen finns inte.

Svarets nytto last för **HTTP 200-** fallet är ett JSON-objekt med följande fält:

| Fält                  | Datatyp | Beskrivning |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | heltal   | Antal borttagna instanser. För det enskilda instans fallet bör det här värdet alltid vara `1` . |

Här är ett exempel på en nytto Last (formaterad för läsbarhet):

```json
{
    "instancesDeleted": 1
}
```

## <a name="purge-multiple-instance-histories"></a>Rensa flera instans historik

Du kan också ta bort historiken och relaterade artefakter för flera instanser i en aktivitets hubb genom att ta bort `{instanceId}` från begäran om att rensa en enskild instans historik. Om du vill rensa instans historik selektivt använder du samma filter som beskrivs i begäran "Hämta alla instans status".

### <a name="request"></a>Förfrågan

För version 1. x av Functions-körningen formateras begäran enligt följande (flera rader visas för tydlighetens skull):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

I version 2. x av Functions-körningen har URL-formatet samma parametrar, men med något annorlunda prefix:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Parametrarna för begäran för detta API inkluderar den standard uppsättning som nämnts tidigare samt följande unika parametrar:

| Fält                 | Parametertyp  | Beskrivning |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Frågesträng    | Filtrerar listan över rensade instanser som skapades vid eller efter den tilldelade ISO8601-tidsstämpeln.|
| **`createdTimeTo`**   | Frågesträng    | Valfri parameter. Filtrerar listan över rensade instanser som skapades vid eller före angiven ISO8601-tidsstämpel.|
| **`runtimeStatus`**   | Frågesträng    | Valfri parameter. När det här alternativet anges filtreras listan över rensade instanser utifrån deras körnings status. Om du vill se en lista över möjliga körnings status värden, se artikeln [frågor om instanser](durable-functions-instance-management.md) . |

> [!NOTE]
> Den här åtgärden kan vara mycket kostsam vad gäller Azure Storage I/O om det finns många rader i instans-och historik tabellerna. Mer information om dessa tabeller finns i dokumentationen om [prestanda och skala i Durable functions (Azure Functions)](durable-functions-perf-and-scale.md#instances-table) .

### <a name="response"></a>Svarsåtgärder

Följande HTTP status kod värden kan returneras.

* **HTTP 200 (OK)**: instans historiken har rensats.
* **HTTP 404 (hittades inte)**: inga instanser hittades som matchar filter uttrycket.

Svarets nytto last för **HTTP 200-** fallet är ett JSON-objekt med följande fält:

| Fält                   | Datatyp | Beskrivning |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | heltal   | Antal borttagna instanser. |

Här är ett exempel på en nytto Last (formaterad för läsbarhet):

```json
{
    "instancesDeleted": 250
}
```

## <a name="raise-event"></a>Utlös händelse

Skickar ett händelse meddelande till en pågående Orchestration-instans.

### <a name="request"></a>Förfrågan

För version 1. x av Functions-körningen formateras begäran enligt följande (flera rader visas för tydlighetens skull):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

I version 2. x av Functions-körningen har URL-formatet samma parametrar, men med något annorlunda prefix:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Parametrarna för begäran för detta API inkluderar den standard uppsättning som nämnts tidigare samt följande unika parametrar:

| Fält             | Parametertyp  | Beskrivning |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID: t för Orchestration-instansen. |
| **`eventName`**   | URL             | Namnet på händelsen som mål Dirigerings instansen väntar på. |
| **`{content}`**   | Begär innehåll | Den JSON-formaterade händelse nytto lasten. |

### <a name="response"></a>Svarsåtgärder

Flera möjliga status kod värden kan returneras.

* **HTTP 202 (accepterad)**: den upphöjda händelsen godtogs för bearbetning.
* **HTTP 400 (felaktig begäran)**: innehållet i begäran var inte av typen `application/json` eller är inte ett giltigt JSON-format.
* **HTTP 404 (hittades inte)**: det gick inte att hitta den angivna instansen.
* **HTTP 410 (borta)**: den angivna instansen har slutförts eller misslyckats och kan inte bearbeta några aktiverade händelser.

Här är en exempel förfrågan som skickar JSON-strängen `"incr"` till en instans som väntar på en händelse med namnet:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Svaren för detta API innehåller inget innehåll.

## <a name="terminate-instance"></a>Avsluta instans

Avslutar en pågående Dirigerings instans.

### <a name="request"></a>Förfrågan

För version 1. x av Functions-körningen formateras begäran enligt följande (flera rader visas för tydlighetens skull):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

I version 2. x av Functions-körningen har URL-formatet samma parametrar, men med något annorlunda prefix:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Parametrarna för begäran för detta API inkluderar den standard uppsättning som nämnts tidigare samt följande unika parameter.

| Fält             | Parameter typ  | Beskrivning |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID: t för Orchestration-instansen. |
| **`reason`**      | Frågesträng    | Valfritt. Orsak till att avsluta Orchestration-instansen. |

### <a name="response"></a>Svarsåtgärder

Flera möjliga status kod värden kan returneras.

* **HTTP 202 (accepterad)**: avbrotts förfrågan godkändes för bearbetning.
* **HTTP 404 (hittades inte)**: det gick inte att hitta den angivna instansen.
* **HTTP 410 (borta)**: den angivna instansen har slutförts eller misslyckats.

Här är en exempel förfrågan som avslutar en inaktive instansen och anger en orsak till **fel sökning**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Svaren för detta API innehåller inget innehåll.

## <a name="rewind-instance-preview"></a>Återspol instans (förhands granskning)

Återställer en misslyckad Orchestration-instans till ett kör tillstånd genom att spela upp de senaste misslyckade åtgärderna.

### <a name="request"></a>Förfrågan

För version 1. x av Functions-körningen formateras begäran enligt följande (flera rader visas för tydlighetens skull):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

I version 2. x av Functions-körningen har URL-formatet samma parametrar, men med något annorlunda prefix:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Parametrarna för begäran för detta API inkluderar den standard uppsättning som nämnts tidigare samt följande unika parameter.

| Fält             | Parameter typ  | Beskrivning |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID: t för Orchestration-instansen. |
| **`reason`**      | Frågesträng    | Valfritt. Anledningen till att du spolar tillbaka Orchestration-instansen. |

### <a name="response"></a>Svarsåtgärder

Flera möjliga status kod värden kan returneras.

* **HTTP 202 (accepterad)**: återspolen godkändes för bearbetning.
* **HTTP 404 (hittades inte)**: det gick inte att hitta den angivna instansen.
* **HTTP 410 (borta)**: den angivna instansen har slutförts eller avslut ATS.

Här är en exempel förfrågan som spolar tillbaka en misslyckad instans och anger en orsak till **åtgärdat**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Svaren för detta API innehåller inget innehåll.

## <a name="signal-entity"></a>Signal enhet

Skickar ett envägs åtgärds meddelande till en [varaktig enhet](durable-functions-types-features-overview.md#entity-functions). Om entiteten inte finns skapas den automatiskt.

> [!NOTE]
> Varaktiga entiteter är tillgängliga från och med Durable Functions 2,0.

### <a name="request"></a>Förfrågan

HTTP-begäran formateras enligt följande (flera rader visas för tydlighetens skull):

```http
POST /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

Parametrarna för begäran för detta API inkluderar den standard uppsättning som nämnts tidigare samt följande unika parametrar:

| Fält             | Parametertyp  | Beskrivning |
|-------------------|-----------------|-------------|
| **`entityName`**  | URL             | Entitetens namn (typ). |
| **`entityKey`**   | URL             | Entitetens nyckel (unikt ID). |
| **`op`**          | Frågesträng    | Valfritt. Namnet på den användardefinierade åtgärd som ska anropas. |
| **`{content}`**   | Begär innehåll | Den JSON-formaterade händelse nytto lasten. |

Här är en exempel förfrågan som skickar ett användardefinierat "Lägg till"-meddelande till en `Counter` entitet med namnet `steps` . Innehållet i meddelandet är värdet `5` . Om entiteten inte redan finns kommer den att skapas av den här begäran:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

> [!NOTE]
> Som standard med [klassbaserade entiteter i .net](durable-functions-dotnet-entities.md#defining-entity-classes), som anger `op` värdet för, `delete` tar bort statusen för en entitet. Om entiteten definierar en åtgärd med namnet `delete` , anropas dock den användardefinierade åtgärden i stället.

### <a name="response"></a>Svarsåtgärder

Den här åtgärden har flera möjliga svar:

* **HTTP 202 (accepterad)**: signal åtgärden godkändes för asynkron bearbetning.
* **HTTP 400 (felaktig begäran)**: innehållet i begäran var inte av typen `application/json` , var inte giltigt JSON eller hade ett ogiltigt `entityKey` värde.
* **HTTP 404 (hittades inte)**: det `entityName` gick inte att hitta den angivna.

En lyckad HTTP-begäran innehåller inget innehåll i svaret. En misslyckad HTTP-begäran kan innehålla JSON-formaterad fel information i svars innehållet.

## <a name="get-entity"></a>Hämta entitet

Hämtar status för den angivna entiteten.

### <a name="request"></a>Förfrågan

HTTP-begäran formateras enligt följande (flera rader visas för tydlighetens skull):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

### <a name="response"></a>Svarsåtgärder

Den här åtgärden har två möjliga svar:

* **HTTP 200 (OK)**: den angivna entiteten finns.
* **HTTP 404 (hittades inte)**: det gick inte att hitta den angivna entiteten.

Ett lyckat svar innehåller den JSON-serialiserade statusen för entiteten som dess innehåll.

### <a name="example"></a>Exempel
I följande exempel HTTP-begäran hämtas statusen för en befintlig `Counter` entitet med namnet `steps` :

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

Om `Counter` entiteten bara innehåller ett antal steg som sparats i ett `currentValue` fält kan svars innehållet se ut så här (formaterat för läsbarhet):

```json
{
    "currentValue": 5
}
```

## <a name="list-entities"></a>Lista entiteter

Du kan fråga efter flera entiteter efter entitetsnamnet eller efter det senaste åtgärds datumet.

### <a name="request"></a>Förfrågan

HTTP-begäran formateras enligt följande (flera rader visas för tydlighetens skull):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &lastOperationTimeFrom={timestamp}
    &lastOperationTimeTo={timestamp}
    &fetchState=[true|false]
    &top={integer}
```

Parametrarna för begäran för detta API inkluderar den standard uppsättning som nämnts tidigare samt följande unika parametrar:

| Fält                       | Parametertyp  | Beskrivning |
|-----------------------------|-----------------|-------------|
| **`entityName`**            | URL             | Valfritt. Filtrerar listan över returnerade entiteter efter enhets namn (Skift läges okänsligt). |
| **`fetchState`**            | Frågesträng    | Valfri parameter. Om värdet `true` är, kommer enhetens tillstånd att inkluderas i svarets nytto Last. |
| **`lastOperationTimeFrom`** | Frågesträng    | Valfri parameter. Filtrerar listan med returnerade entiteter som bearbetade åtgärder efter den angivna ISO8601-tidsstämpeln. |
| **`lastOperationTimeTo`**   | Frågesträng    | Valfri parameter. Filtrerar listan med returnerade entiteter som bearbetade åtgärder före angiven ISO8601-tidsstämpel. |
| **`top`**                   | Frågesträng    | Valfri parameter. Begränsar antalet entiteter som returneras av frågan. |


### <a name="response"></a>Svarsåtgärder

Ett lyckat HTTP 200-svar innehåller en JSON-serialiserad matris med entiteter och eventuellt statusen för varje entitet.

Som standard returnerar åtgärden de första 100 entiteter som matchar kriterierna för frågan. Anroparen kan ange ett parameter värde för frågesträng för `top` att returnera ett annat maximalt antal resultat. Om det finns fler resultat utöver vad som returneras, returneras även en fortsättnings-token i svars huvudet. Namnet på rubriken är `x-ms-continuation-token` .

Om du ställer in värdet för värde för fortsättnings-token i nästa begär ande huvud kan du hämta nästa sida med resultat. Namnet på begär ande rubriken är också `x-ms-continuation-token` .

### <a name="example---list-all-entities"></a>Exempel – Visa en lista över alla entiteter

I följande exempel HTTP-begäran visas alla entiteter i aktivitets navet:

```http
GET /runtime/webhooks/durabletask/entities
```

Svars-JSON kan se ut så här (formaterad för läsbarhet):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z"
    },
    {
        "entityId": { "key": "mice", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:15.4626159Z"
    },
    {
        "entityId": { "key": "radio", "name": "device" },
        "lastOperationTime": "2019-12-18T21:46:18.2616154Z"
    },
]
```

### <a name="example---filtering-the-list-of-entities"></a>Exempel – filtrera listan över entiteter

I följande exempel visar HTTP-förfrågan bara de två första typerna av typ `counter` och hämtar även deras status:

```http
GET /runtime/webhooks/durabletask/entities/counter?top=2&fetchState=true
```

Svars-JSON kan se ut så här (formaterad för läsbarhet):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
        "state": { "value": 9 }
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z",
        "state": { "value": 10 }
    }
]
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du använder Application Insights för att övervaka dina varaktiga funktioner](durable-functions-diagnostics.md)