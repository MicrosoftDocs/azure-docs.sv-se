---
title: Azure Blob Storage-indata-bindning för Azure Functions
description: Lär dig hur du tillhandahåller data bindnings data i Azure Blob Storage till en Azure-funktion.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: cd69e89954fab2256ffc7c23e22d3b8d44ab2a11
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455881"
---
# <a name="azure-blob-storage-input-binding-for-azure-functions"></a>Azure Blob Storage-indata-bindning för Azure Functions

Med hjälp av den angivna bindningen kan du läsa Blob Storage-data som indata till en Azure-funktion.

Information om konfiguration och konfigurations information finns i [översikten](./functions-bindings-storage-blob.md).

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

Följande exempel är en [C#-funktion](functions-dotnet-class-library.md) som använder en Queue-utlösare och en BLOB-bindning för inflöde. Queue-meddelandet innehåller namnet på blobben och funktionen loggar storleken på blobben.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

<!--Same example for input and output. -->

I följande exempel visas BLOB-indata och utdata-bindningar i en *function.jspå* fil [-och C#-skript (. CSX)](functions-reference-csharp.md) som använder bindningarna. Funktionen gör en kopia av en text-blob. Funktionen utlöses av ett köat meddelande som innehåller namnet på den blob som ska kopieras. Den nya blobben heter *{originalblobname}-Copy*.

I *function.jsi* filen `queueTrigger` används egenskapen metadata för att ange BLOB-namnet i `path` egenskaperna:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

Här är C#-skript koden:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="java"></a>[Java](#tab/java)

Det här avsnittet innehåller följande exempel:

* [HTTP-utlösare, slå upp BLOB-namn från frågesträng](#http-trigger-look-up-blob-name-from-query-string)
* [Köa utlösare, ta emot BLOB-namn från köa meddelande](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>HTTP-utlösare, slå upp BLOB-namn från frågesträng

 I följande exempel visas en Java-funktion som använder `HttpTrigger` anteckningen för att ta emot en parameter som innehåller namnet på en fil i en Blob Storage-behållare. `BlobInput`Anteckningen läser sedan filen och skickar innehållet till funktionen som en `byte[]` .

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Köa utlösare, ta emot BLOB-namn från köa meddelande

 I följande exempel visas en Java-funktion som använder `QueueTrigger` anteckningen för att ta emot ett meddelande som innehåller namnet på en fil i en Blob Storage-behållare. `BlobInput`Anteckningen läser sedan filen och skickar innehållet till funktionen som en `byte[]` .

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `@BlobInput` anteckningen för parametrar vars värde kommer från en blob.  Den här anteckningen kan användas med inbyggda Java-typer, Pojo eller null-värden med hjälp av `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

I följande exempel visas BLOB-indata och utgående bindningar i en *function.jspå* fil-och [JavaScript-kod](functions-reference-node.md) som använder bindningarna. Funktionen gör en kopia av en blob. Funktionen utlöses av ett köat meddelande som innehåller namnet på den blob som ska kopieras. Den nya blobben heter *{originalblobname}-Copy*.

I *function.jsi* filen `queueTrigger` används egenskapen metadata för att ange BLOB-namnet i `path` egenskaperna:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

Här är JavaScript-koden:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

I följande exempel visas en BLOB-databindning som definieras i _function.jspå_ filen, vilket gör inkommande BLOB-data tillgängliga för [PowerShell](functions-reference-powershell.md) -funktionen.

Här är JSON-konfigurationen:

```json
{
  "bindings": [
    {
      "name": "InputBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "source/{name}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Här är funktions koden:

```powershell
# Input bindings are passed in via param block.
param([byte[]] $InputBlob, $TriggerMetadata)

Write-Host "PowerShell Blob trigger: Name: $($TriggerMetadata.Name) Size: $($InputBlob.Length) bytes"
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

I följande exempel visas BLOB-indata och utgående bindningar i en *function.jspå* fil-och [python-kod](functions-reference-python.md) som använder bindningarna. Funktionen gör en kopia av en blob. Funktionen utlöses av ett köat meddelande som innehåller namnet på den blob som ska kopieras. Den nya blobben heter *{originalblobname}-Copy*.

I *function.jsi* filen `queueTrigger` används egenskapen metadata för att ange BLOB-namnet i `path` egenskaperna:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "dataType": "binary",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

`dataType`Egenskapen bestämmer vilken bindning som används. Följande värden är tillgängliga för att stödja olika bindnings strategier:

| Bindnings värde | Standardvärde | Beskrivning | Exempel |
| --- | --- | --- | --- |
| `undefined` | Y | Använder omfattande bindning | `def main(input: func.InputStream)` |
| `string` | N | Använder generisk bindning och anger indatatypen som en `string` | `def main(input: str)` |
| `binary` | N | Använder generisk bindning och skickar bloben för indataport som `bytes` python-objekt | `def main(input: bytes)` |

Här är python-koden:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

Använd [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)i [C#-klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktor tar vägen till blobben och en `FileAccess` parameter som visar Läs-eller Skriv behörighet, som visas i följande exempel:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

Du kan ställa in `Connection` egenskapen för att ange det lagrings konto som ska användas, som du ser i följande exempel:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Du kan använda `StorageAccount` attributet för att ange lagrings kontot på klass-, metod-eller parameter nivå. Mer information finns i avsnittet om [utlösare-attribut och anteckningar](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Attribut stöds inte av C#-skript.

# <a name="java"></a>[Java](#tab/java)

`@BlobInput`Attributet ger dig åtkomst till den blob som utlöste funktionen. Om du använder en byte mat ris med attributet, anges `dataType` till `binary` . Mer information finns i [indata-exemplet](#example) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Attribut stöds inte av PowerShell.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av python.

---

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `Blob` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**bastyp** | saknas | Måste anges till `blob` . |
|**position** | saknas | Måste anges till `in` . Undantag anges i [användnings](#usage) avsnittet. |
|**Namn** | saknas | Namnet på variabeln som representerar blobben i funktions koden.|
|**sökväg** |**BlobPath** | Sökvägen till blobben. |
|**anslutningen** |**Anslutning**| Namnet på en app-inställning som innehåller den [lagrings anslutnings sträng](../storage/common/storage-configure-connection-string.md) som ska användas för den här bindningen. Om appens inställnings namn börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till exempel anger `connection` "unstorage" söker funktions körningen efter en app-inställning med namnet "AzureWebJobsMyStorage". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen för lagring i den angivna app-inställningen `AzureWebJobsStorage` .<br><br>Anslutnings strängen måste vara avsedd för ett allmänt lagrings konto, inte ett [enbart BLOB-lagrings konto](../storage/common/storage-account-overview.md#types-of-storage-accounts).<br><br>Om du använder [version 5. x eller högre av tillägget](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher), i stället för en anslutnings sträng, kan du ange en referens till ett konfigurations avsnitt som definierar anslutningen. Se [anslutningar](./functions-reference.md#connections).|
|**dataType**| saknas | För dynamiskt skrivna språk anger den underliggande data typen. Möjliga värden är `string` , `binary` , eller `stream` . Mer information finns i [utlösare och bindningar begrepp](functions-triggers-bindings.md?tabs=python#trigger-and-binding-definitions). |
|saknas | **Åtkomst** | Anger om du kommer att läsa eller skriva. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="java"></a>[Java](#tab/java)

`@BlobInput`Attributet ger dig åtkomst till den blob som utlöste funktionen. Om du använder en byte mat ris med attributet, anges `dataType` till `binary` . Mer information finns i [indata-exemplet](#example) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Få åtkomst till BLOB-data med `context.bindings.<NAME>` var `<NAME>` matchar värdet som definierades i *function.js*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Få åtkomst till BLOB-data via en parameter som matchar namnet som anges av bindningens namn parameter i _function.jsi_ filen.

# <a name="python"></a>[Python](#tab/python)

Få åtkomst till BLOB-data via parametern som anges som [InputStream](/python/api/azure-functions/azure.functions.inputstream). Mer information finns i [indata-exemplet](#example) .

---

## <a name="next-steps"></a>Nästa steg

- [Köra en funktion när Blob Storage-data ändras](./functions-bindings-storage-blob-trigger.md)
- [Skriv Blob Storage-data från en funktion](./functions-bindings-storage-blob-output.md)
