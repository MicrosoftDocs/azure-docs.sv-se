---
title: Azure Event Grid utlösare för Azure Functions
description: Lär dig att köra kod när Event Grid händelser i Azure Functions skickas.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: 17968f2c137eef51eecdb6c7098c7056944dc970
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782197"
---
# <a name="azure-event-grid-trigger-for-azure-functions"></a>Azure Event Grid utlösare för Azure Functions

Använd funktionsutlösaren för att svara på en händelse som skickas till ett Event Grid ämne.

Information om installation och konfiguration finns i [översikten.](./functions-bindings-event-grid.md)

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

Ett EXEMPEL på HTTP-utlösare finns i [Ta emot händelser till en HTTP-slutpunkt.](../event-grid/receive-events.md)

### <a name="c-2x-and-higher"></a>C# (2.x och senare)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som binder till `EventGridEvent` :

```cs
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
        public static void EventGridTest([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Mer information finns i Paket, [attribut,](#attributes-and-annotations) [konfiguration](#configuration)och [användning.](#usage)

### <a name="version-1x"></a>Version 1.x

I följande exempel visas en Functions 1.x [C#-funktion](functions-dotnet-class-library.md) som binder till `JObject` :

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

I följande exempel visas en utlösarbindning i *enfunction.jspå* filen och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen.

Här är bindningsdata i filen *function.jsfilen:*

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

### <a name="version-2x-and-higher"></a>Version 2.x och senare

Här är ett exempel som binder till `EventGridEvent` :

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Mer information finns i Paket, [attribut,](#attributes-and-annotations) [konfiguration](#configuration)och [användning.](#usage)

### <a name="version-1x"></a>Version 1.x

Här är Functions 1.x C#-skriptkod som binder till `JObject` :

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="java"></a>[Java](#tab/java)

Det här avsnittet innehåller följande exempel:

* [Event Grid utlösare, strängparameter](#event-grid-trigger-string-parameter)
* [Event Grid utlösare, POJO-parameter](#event-grid-trigger-pojo-parameter)

I följande exempel visas utlösarbindning i [Java](functions-reference-java.md) som använder bindningen och skriver ut en händelse, först tar emot händelsen som `String` och den andra som en POJO.

### <a name="event-grid-trigger-string-parameter"></a>Event Grid utlösare, strängparameter

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: " + content);      
  }
```

### <a name="event-grid-trigger-pojo-parameter"></a>Event Grid utlösare, POJO-parameter

I det här exemplet används följande POJO, som representerar egenskaperna på den översta nivån för en Event Grid händelse:

```java
import java.util.Date;
import java.util.Map;

public class EventSchema {

  public String topic;
  public String subject;
  public String eventType;
  public Date eventTime;
  public String id;
  public String dataVersion;
  public String metadataVersion;
  public Map<String, Object> data;

}
```

Vid ankomst av serialiseras händelsens JSON-nyttolast till ```EventSchema``` POJO för användning av funktionen . Den här processen gör att funktionen kan komma åt händelsens egenskaper på ett objektorienterat sätt.

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

I [Java Functions-körningsbiblioteket](/java/api/overview/azure/functions/runtime)använder du `EventGridTrigger` kommentaren för parametrar vars värde skulle komma från EventGrid. Parametrar med dessa anteckningar gör att funktionen körs när en händelse tas emot.  Den här anteckningen kan användas med interna Java-typer, POJOs eller nullbara värden med hjälp av `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en utlösarbindning i *enfunction.jsfil* och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen.

Här är bindningsdata i filen *function.jsfilen:*

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Här är JavaScript-koden:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

I följande exempel visas hur du konfigurerar en Event Grid utlösarbindning i *function.jspå* filen.

```powershell
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ]
}
```

Den Event Grid händelsen görs tillgänglig för funktionen via en parameter med namnet `eventGridEvent` , som du ser i följande PowerShell-exempel.

```powershell
param($eventGridEvent, $TriggerMetadata)

# Make sure to pass hashtables to Out-String so they're logged correctly
$eventGridEvent | Out-String | Write-Host
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas en utlösarbindning i *enfunction.jspå* filen och en [Python-funktion](functions-reference-python.md) som använder bindningen.

Här är bindningsdata i filen *function.jsfilen:*

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "event",
      "direction": "in"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Här är Python-koden:

```python
import json
import logging

import azure.functions as func

def main(event: func.EventGridEvent):

    result = json.dumps({
        'id': event.id,
        'data': event.get_json(),
        'topic': event.topic,
        'subject': event.subject,
        'event_type': event.event_type,
    })

    logging.info('Python EventGrid trigger processed an event: %s', result)
```

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

I [C#-klassbibliotek](functions-dotnet-class-library.md)använder du [attributet EventGridTrigger.](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs)

Här är ett attribut `EventGridTrigger` i en metodsignatur:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Ett komplett exempel finns i C#-exempel.

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Attribut stöds inte av C#-skript.

# <a name="java"></a>[Java](#tab/java)

Med [EventGridTrigger-anteckningen](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) kan du deklarativt konfigurera en Event Grid-bindning genom att ange konfigurationsvärden. Mer information [finns](#example) i [exempel-](#configuration) och konfigurationsavsnitten.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Attribut stöds inte av PowerShell.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av Python.

---

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som du anger *ifunction.jspå* filen. Det finns inga konstruktorparametrar eller egenskaper att ange i `EventGridTrigger` attributet.

|function.jspå egenskapen |Description|
|---------|---------|
| **Typ** | Obligatoriskt – måste anges till `eventGridTrigger` . |
| **riktning** | Obligatoriskt – måste anges till `in` . |
| **Namn** | Obligatoriskt – variabelnamnet som används i funktionskoden för den parameter som tar emot händelsedata. |

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

I Azure Functions 1.x kan du använda följande parametertyper för den Event Grid utlösaren:

* `JObject`
* `string`

I Azure Functions 2.x och senare har du också möjlighet att använda följande parametertyp för Event Grid utlösaren:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`– Definierar egenskaper för de fält som är gemensamma för alla händelsetyper.

> [!NOTE]
> Om du försöker binda till i Functions v1 visar kompilatorn ett "inaktuellt" meddelande och rekommenderar `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` att du använder i `Microsoft.Azure.EventGrid.Models.EventGridEvent` stället. Om du vill använda den nyare typen refererar du till [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet-paketet och kvalificerar typnamnet fullständigt genom `EventGridEvent` att lägga till prefixet `Microsoft.Azure.EventGrid.Models` .

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

I Azure Functions 1.x kan du använda följande parametertyper för den Event Grid utlösaren:

* `JObject`
* `string`

I Azure Functions 2.x och senare har du också möjlighet att använda följande parametertyp för den Event Grid utlösaren:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`– Definierar egenskaper för de fält som är gemensamma för alla händelsetyper.

> [!NOTE]
> Om du försöker binda till i Functions v1 visar kompilatorn ett "inaktuellt" meddelande och rekommenderar `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` att du använder i `Microsoft.Azure.EventGrid.Models.EventGridEvent` stället. Om du vill använda den nyare typen refererar du till [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet-paketet och kvalificerar typnamnet fullständigt genom `EventGridEvent` att lägga till prefixet `Microsoft.Azure.EventGrid.Models` . Information om hur du refererar till NuGet-paket i en C#-skriptfunktion finns i [Använda NuGet-paket](functions-reference-csharp.md#using-nuget-packages)

# <a name="java"></a>[Java](#tab/java)

Den Event Grid händelseinstansen är tillgänglig via parametern som är associerad `EventGridTrigger` med attributet och har skrivits som en `EventSchema` . Mer [information finns](#example) i exemplet.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Den Event Grid instansen är tillgänglig via parametern som *konfigurerasfunction.jspå* filens `name` -egenskap.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Den Event Grid instansen är tillgänglig via parametern som *konfigurerasfunction.jspå* filens `name` -egenskap.

# <a name="python"></a>[Python](#tab/python)

Den Event Grid-instansen är tillgänglig via parametern *som konfigurerats ifunction.jspå* filens `name` -egenskap, som har skrivits som `func.EventGridEvent` .

---

## <a name="event-schema"></a>Händelseschema

Data för en Event Grid-händelse tas emot som ett JSON-objekt i brödtexten i en HTTP-begäran. JSON ser ut ungefär som i följande exempel:

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Exemplet som visas är en matris med ett element. Event Grid alltid en matris och kan skicka fler än en händelse i matrisen. Körningen anropar din funktion en gång för varje matriselement.

Egenskaperna på den översta nivån i händelse-JSON-data är desamma för alla händelsetyper, medan innehållet i egenskapen `data` är specifikt för varje händelsetyp. Exemplet som visas är för en bloblagringshändelse.

Förklaringar av vanliga och händelsespecifika egenskaper finns i [Händelseegenskaper i](../event-grid/event-schema.md#event-properties) Event Grid dokumentationen.

Typen `EventGridEvent` definierar endast egenskaperna på den översta nivån. `Data` Egenskapen är en `JObject` .

## <a name="create-a-subscription"></a>Skapa en prenumeration

Om du vill Event Grid http-begäranden skapar du en Event Grid-prenumeration som anger den slutpunkts-URL som anropar funktionen.

### <a name="azure-portal"></a>Azure Portal

För funktioner som du utvecklar i Azure Portal med Event Grid-utlösaren väljer du **Integration** och sedan **Event Grid Utlösare** och **väljer Skapa Event Grid prenumeration**.

:::image type="content" source="media/functions-bindings-event-grid/portal-sub-create.png" alt-text="Anslut en ny händelseprenumeration som ska utlösas i portalen.":::

När du väljer den här länken öppnar portalen sidan Skapa **händelseprenumeration** med den aktuella utlösarslutpunkten redan definierad.

:::image type="content" source="media/functions-bindings-event-grid/endpoint-url.png" alt-text="Skapa händelseprenumeration med funktionsslutpunkten redan definierad" :::

Mer information om hur du skapar prenumerationer med hjälp av Azure Portal finns i Skapa anpassad [händelse – Azure Portal](../event-grid/custom-event-quickstart-portal.md) i Event Grid dokumentationen.

### <a name="azure-cli"></a>Azure CLI

Om du vill skapa en prenumeration [med hjälp av Azure CLI](/cli/azure/get-started-with-azure-cli)använder du kommandot az [eventgrid event-subscription create.](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create)

Kommandot kräver den slutpunkts-URL som anropar funktionen. I följande exempel visas det versionsspecifika URL-mönstret:

#### <a name="version-2x-and-higher-runtime"></a>Körningsversion 2.x (och senare)

```http
https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}
```

#### <a name="version-1x-runtime"></a>Version 1.x runtime

```http
https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}
```

Systemnyckeln är en auktoriseringsnyckel som måste ingå i slutpunkts-URL:en för en Event Grid utlösare. I följande avsnitt förklaras hur du hämtar systemnyckeln.

Här är ett exempel som prenumererar på ett bloblagringskonto (med en platshållare för systemnyckeln):

#### <a name="version-2x-and-higher-runtime"></a>Körning av version 2.x (och senare)

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
    --provider-namespace Microsoft.Storage --resource-type storageAccounts \
    --resource-name myblobstorage12345 --name myFuncSub \
    --included-event-types Microsoft.Storage.BlobCreated \
    --subject-begins-with /blobServices/default/containers/images/blobs/ \
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup ^
    --provider-namespace Microsoft.Storage --resource-type storageAccounts ^
    --resource-name myblobstorage12345 --name myFuncSub ^
    --included-event-types Microsoft.Storage.BlobCreated ^
    --subject-begins-with /blobServices/default/containers/images/blobs/ ^
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

---

#### <a name="version-1x-runtime"></a>Version 1.x runtime

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
    --provider-namespace Microsoft.Storage --resource-type storageAccounts \
    --resource-name myblobstorage12345 --name myFuncSub \
    --included-event-types Microsoft.Storage.BlobCreated \
    --subject-begins-with /blobServices/default/containers/images/blobs/ \
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup ^
    --provider-namespace Microsoft.Storage --resource-type storageAccounts ^
    --resource-name myblobstorage12345 --name myFuncSub ^
    --included-event-types Microsoft.Storage.BlobCreated ^
    --subject-begins-with /blobServices/default/containers/images/blobs/ ^
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

---

Mer information om hur du skapar en prenumeration finns i [snabbstarten för bloblagring](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) eller andra Event Grid snabbstarter.

### <a name="get-the-system-key"></a>Hämta systemnyckeln

Du kan hämta systemnyckeln med hjälp av följande API (HTTP GET):

#### <a name="version-2x-and-higher-runtime"></a>Körningsversion 2.x (och senare)

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Version 1.x runtime

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Det här är ett administratörs-API, så det kräver din huvudnyckel [för funktionsappen.](functions-bindings-http-webhook-trigger.md#authorization-keys) Blanda inte ihop systemnyckeln (för att aktivera en Event Grid utlösarfunktion) med huvudnyckeln (för att utföra administrativa uppgifter i funktionsappen). När du prenumererar på Event Grid bör du använda systemnyckeln.

Här är ett exempel på svaret som tillhandahåller systemnyckeln:

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

Du kan hämta huvudnyckeln för funktionsappen på fliken **Funktionsappinställningar** i portalen.

> [!IMPORTANT]
> Huvudnyckeln ger administratörsåtkomst till din funktionsapp. Dela inte den här nyckeln med tredje part eller distribuera den i interna klientprogram.

Mer information finns i [Auktoriseringsnycklar](functions-bindings-http-webhook-trigger.md#authorization-keys) i referensartikeln om HTTP-utlösare.

Du kan också skicka en HTTP PUT för att ange nyckelvärdet själv.

## <a name="local-testing-with-viewer-web-app"></a>Lokal testning med visningswebbapp

Om du vill Event Grid en utlösare lokalt måste du få Event Grid HTTP-begäranden som levereras från deras ursprung i molnet till din lokala dator. Ett sätt att göra det är att samla in begäranden online och manuellt skicka dem på den lokala datorn:

1. [Skapa en visningswebbapp](#create-a-viewer-web-app) som samlar in händelsemeddelanden.
1. [Skapa en Event Grid prenumeration](#create-an-event-grid-subscription) som skickar händelser till visningsappen.
1. [Generera en begäran](#generate-a-request) och kopiera begärandetexten från visningsappen.
1. [Publicera begäran manuellt till](#manually-post-the-request) localhost-URL:en för Event Grid utlösarfunktion.

När du är klar med testningen kan du använda samma prenumeration för produktion genom att uppdatera slutpunkten. Använd [Azure CLI-kommandot az eventgrid event-subscription update.](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_update)

### <a name="create-a-viewer-web-app"></a>Skapa en visningswebbapp

För att förenkla insamlingen av händelsemeddelanden kan du distribuera [en förbyggd webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar händelsemeddelandena. Den distribuerade lösningen innehåller en App Service-plan,en webbapp för App Service och källkod från GitHub.

Välj **Deploy to Azure** (Distribuera till Azure) för att distribuera lösningen till din prenumeration. Ange parametervärdena i Azure Portal.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Azure." /></a>

Det kan ta några minuter att slutföra distributionen. Efter distributionen har slutförts kan du visa webbappen för att kontrollera att den körs. I en webbläsare navigerar du till: `https://<your-site-name>.azurewebsites.net`

Du ser webbplatsen men det har inte publicerats händelser till den än.

![Visa ny webbplats](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Skapa en Event Grid-prenumeration

Skapa en Event Grid prenumeration av den typ som du vill testa och ge den URL:en från webbappen som slutpunkt för händelseavisering. Slutpunkten för ditt webbprogram måste innehålla suffixet `/api/updates/`. Därför är den fullständiga URL:en `https://<your-site-name>.azurewebsites.net/api/updates`

Information om hur du skapar prenumerationer med hjälp av Azure Portal finns i Skapa anpassad [händelse – Azure Portal](../event-grid/custom-event-quickstart-portal.md) i Event Grid dokumentationen.

### <a name="generate-a-request"></a>Generera en begäran

Utlöst en händelse som genererar HTTP-trafik till slutpunkten för webbappen.  Om du till exempel har skapat en bloblagringsprenumeration laddar du upp eller tar bort en blob. När en begäran visas i webbappen kopierar du begärandetexten.

Begäran om prenumerationsverifiering tas emot först. ignorera alla valideringsbegäranden och kopiera händelsebegäran.

![Kopiera begärandetexten från webbappen](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Publicera begäran manuellt

Kör din Event Grid lokalt. `Content-Type` `aeg-event-type` Sidhuvudena och måste anges manuellt, medan alla andra värden kan lämnas kvar som standard.

Använd ett verktyg som [Postman eller](https://www.getpostman.com/) curl för [att](https://curl.haxx.se/docs/httpscripting.html) skapa en HTTP POST-begäran:

* Ange en `Content-Type: application/json` rubrik.
* Ange en `aeg-event-type: Notification` rubrik.
* Klistra in RequestBin-data i begärandetexten.
* Publicera till URL:en för din Event Grid utlösarfunktion.
  * För 2.x och senare använder du följande mönster:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * För 1.x använder du:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

Parametern `functionName` måste vara det namn som anges i `FunctionName` attributet.

Följande skärmbilder visar huvudena och begärandetexten i Postman:

![Rubriker i Postman](media/functions-bindings-event-grid/postman2.png)

![Begärandetext i Postman](media/functions-bindings-event-grid/postman.png)

Funktionen Event Grid körs och visar loggar som liknar följande exempel:

![Exempelloggar Event Grid utlösarfunktion](media/functions-bindings-event-grid/eg-output.png)

## <a name="next-steps"></a>Nästa steg

* [Skicka en Event Grid händelse](./functions-bindings-event-grid-output.md)
