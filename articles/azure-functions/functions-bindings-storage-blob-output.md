---
title: Azure Blob Storage utgående bindning för Azure Functions
description: Lär dig hur du tillhandahåller utdata från data bindnings data i Azure Blob Storage till en Azure-funktion.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: eaa8a4c600864f636d49813d415621d46130fff7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381671"
---
# <a name="azure-blob-storage-output-binding-for-azure-functions"></a>Azure Blob Storage utgående bindning för Azure Functions

Med utgående bindning kan du ändra och ta bort Blob Storage-data i en Azure-funktion.

Information om konfiguration och konfigurations information finns i [översikten](./functions-bindings-storage-blob.md).

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

Följande exempel är en [C#-funktion](functions-dotnet-class-library.md) som använder en BLOB-utlösare och två utgående BLOB-bindningar. Funktionen utlöses genom att en avbildnings-BLOB skapas i behållaren för *exempel bilder* . Det skapar små och medel stora kopior av avbildnings-bloben.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

public class ResizeImages
{
    [FunctionName("ResizeImage")]
    public static void Run([BlobTrigger("sample-images/{name}")] Stream image,
        [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
        [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
    {
        IImageFormat format;

        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageSmall, ImageSize.Small, format);
        }

        image.Position = 0;
        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageMedium, ImageSize.Medium, format);
        }
    }

    public static void ResizeImage(Image<Rgba32> input, Stream output, ImageSize size, IImageFormat format)
    {
        var dimensions = imageDimensionsTable[size];

        input.Mutate(x => x.Resize(dimensions.Item1, dimensions.Item2));
        input.Save(output, format);
    }

    public enum ImageSize { ExtraSmall, Small, Medium }

    private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
        { ImageSize.ExtraSmall, (320, 200) },
        { ImageSize.Small,      (640, 400) },
        { ImageSize.Medium,     (800, 600) }
    };

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

* [HTTP-utlösare med hjälp av OutputBinding](#http-trigger-using-outputbinding-java)
* [Köa utlösare med funktions retur värde](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP-utlösare med hjälp av OutputBinding (Java)

 I följande exempel visas en Java-funktion som använder `HttpTrigger` anteckningen för att ta emot en parameter som innehåller namnet på en fil i en Blob Storage-behållare. `BlobInput`Anteckningen läser sedan filen och skickar innehållet till funktionen som en `byte[]` . `BlobOutput`Anteckningen binder till `OutputBinding outputItem` , som sedan används av funktionen för att skriva innehållet i bloben för indata till den konfigurerade lagrings behållaren.

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>Köa utlösare med funktions retur värde (Java)

 I följande exempel visas en Java-funktion som använder `QueueTrigger` anteckningen för att ta emot ett meddelande som innehåller namnet på en fil i en Blob Storage-behållare. `BlobInput`Anteckningen läser sedan filen och skickar innehållet till funktionen som en `byte[]` . `BlobOutput`Anteckningen binder till funktionens retur värde, som sedan används av körnings miljön för att skriva innehållet i bloben för indata till den konfigurerade lagrings behållaren.

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `@BlobOutput` anteckningen för funktions parametrar vars värde skrivs till ett objekt i Blob Storage.  Parameter typen bör vara `OutputBinding<T>` , där T är en inbyggd Java-typ eller en POJO.

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

Följande exempel visar hur du skapar en kopia av en inkommande blob som utdata från en PowerShell- [funktion](functions-reference-powershell.md).

I funktionens konfigurations fil (*function.jspå*) `trigger` används egenskapen metadata för att ange utgående BLOB-namn i `path` egenskaperna.

> [!NOTE]
> Kontrol lera att dina indata-och utmatnings Sök vägar är olika för att undvika oändlig loop.

```json
{
  "bindings": [
    {
      "name": "myInputBlob",
      "path": "data/{trigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in",
      "type": "blobTrigger"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "data/copy/{trigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Här är PowerShell-koden:

```powershell
# Input bindings are passed in via param block.
param([byte[]] $myInputBlob, $TriggerMetadata)
Write-Host "PowerShell Blob trigger function Processed blob Name: $($TriggerMetadata.Name)"
Push-OutputBinding -Name myOutputBlob -Value $myInputBlob
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
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "outputblob",
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

Här är python-koden:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

Använd [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)i [C#-klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktor tar vägen till blobben och en `FileAccess` parameter som visar Läs-eller Skriv behörighet, som visas i följande exempel:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Du kan ställa in `Connection` egenskapen för att ange det lagrings konto som ska användas, som du ser i följande exempel:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Attribut stöds inte av C#-skript.

# <a name="java"></a>[Java](#tab/java)

`@BlobOutput`Attributet ger dig åtkomst till den blob som utlöste funktionen. Om du använder en byte mat ris med attributet, anges `dataType` till `binary` . Mer information finns i [utdata-exemplet](#example) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Attribut stöds inte av PowerShell.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av python.

---

Ett fullständigt exempel finns i [output-exemplet](#example).

Du kan använda `StorageAccount` attributet för att ange lagrings kontot på klass-, metod-eller parameter nivå. Mer information finns i avsnittet om [Utlösar-attribut](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `Blob` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**bastyp** | saknas | Måste anges till `blob` . |
|**position** | saknas | Måste anges till `out` för en utgående bindning. Undantag anges i [användnings](#usage) avsnittet. |
|**Namn** | saknas | Namnet på variabeln som representerar blobben i funktions koden.  Ange till `$return` att referera till funktionens retur värde.|
|**sökväg** |**BlobPath** | Sökvägen till BLOB-behållaren. |
|**anslutningen** |**Anslutning**| Namnet på en app-inställning som innehåller den lagrings anslutnings sträng som ska användas för den här bindningen. Om appens inställnings namn börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till exempel anger `connection` "unstorage" söker funktions körningen efter en app-inställning med namnet "AzureWebJobsMyStorage". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen för lagring i den angivna app-inställningen `AzureWebJobsStorage` .<br><br>Anslutnings strängen måste vara avsedd för ett allmänt lagrings konto, inte ett [enbart BLOB-lagrings konto](../storage/common/storage-account-overview.md#types-of-storage-accounts).<br><br>Om du använder [version 5. x eller högre av tillägget](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher), i stället för en anslutnings sträng, kan du ange en referens till ett konfigurations avsnitt som definierar anslutningen. Se [anslutningar](./functions-reference.md#connections).|
|saknas | **Åtkomst** | Anger om du kommer att läsa eller skriva. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="java"></a>[Java](#tab/java)

`@BlobOutput`Attributet ger dig åtkomst till den blob som utlöste funktionen. Om du använder en byte mat ris med attributet, anges `dataType` till `binary` . Mer information finns i [utdata-exemplet](#example) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Få åtkomst till BLOB-data med `context.bindings.<BINDING_NAME>` , där bindnings namnet har definierats i _function.jsi_ filen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Få åtkomst till BLOB-data via en parameter som matchar namnet som anges av bindningens namn parameter i _function.jsi_ filen.

# <a name="python"></a>[Python](#tab/python)

Du kan deklarera funktions parametrar som följande typer för att skriva ut till Blob Storage:

* Strängar som `func.Out(str)`
* Strömmar som `func.Out(func.InputStream)`

Mer information finns i [utdata-exemplet](#example) .

---

## <a name="exceptions-and-return-codes"></a>Undantag och retur koder

| Bindning |  Referens |
|---|---|
| Blob | [BLOB-felkoder](/rest/api/storageservices/fileservices/blob-service-error-codes) |
| BLOB, tabell, kö |  [Lagrings fel koder](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabell, kö |  [Felsökning](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Nästa steg

- [Köra en funktion när Blob Storage-data ändras](./functions-bindings-storage-blob-trigger.md)
- [Läsa Blob Storage-data när en funktion körs](./functions-bindings-storage-blob-input.md)
