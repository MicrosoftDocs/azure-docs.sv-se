---
title: Mobile Apps bindningar för Azure Functions
description: Lär dig hur du använder Azure Mobile Apps-bindningar i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 5ea58cc3d9f3615a74249b36f3f9ffb79caddda1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88212242"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Mobile Apps bindningar för Azure Functions 

> [!NOTE]
> Azure Mobile Apps-bindningar är bara tillgängliga för Azure Functions 1. x. De stöds inte i Azure Functions 2. x och högre.

Den här artikeln förklarar hur du arbetar med [Azure Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) -bindningar i Azure Functions. Azure Functions stöder bindningar för indata och utdata för Mobile Apps.

Med Mobile Apps bindningar kan du läsa och uppdatera data tabeller i mobilappar.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket-funktioner 1. x

Mobile Apps-bindningar finns i [Microsoft. Azure. WebJobs. Extensions. MobileApps](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) NuGet-paketet, version 1. x. Käll koden för paketet finns i [Azure-WebJobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/) lagringsplatsen.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Indata

Mobile Apps-indata-bindningen läser in en post från en Mobile Table-slutpunkt och skickar den till din funktion. I C#-och F #-funktioner skickas alla ändringar som görs i posten automatiskt tillbaka till tabellen när funktionen avslutas.

## <a name="input---example"></a>Indatamängds exempel

Se språkspecifika exempel:

* [C#-skript (.csx)](#input---c-script-example)
* JavaScript

### <a name="input---c-script-example"></a>Exempel på inmatade C#-skript

I följande exempel visas en Mobile Apps indata-bindning i en *function.jspå* fil och en [C#-skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen utlöses av ett köat meddelande som har en post-ID. Funktionen läser den angivna posten och ändrar dess `Text` egenskap.

Här är bindnings data i *function.jspå* filen:

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
        "type": "queueTrigger",
        "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id": "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
I [konfigurations](#input---configuration) avsnittet förklaras dessa egenskaper.

Här är C#-skript koden:

```cs
#r "Newtonsoft.Json"    
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, JObject record)
{
    if (record != null)
    {
        record["Text"] = "This has changed.";
    }    
}
```

### <a name="input---javascript"></a>Inmatade Java Script

I följande exempel visas en Mobile Apps indata-bindning i en *function.jsi* filen och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen utlöses av ett köat meddelande som har en post-ID. Funktionen läser den angivna posten och ändrar dess `Text` egenskap.

Här är bindnings data i *function.jspå* filen:

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
        "type": "queueTrigger",
        "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id": "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
I [konfigurations](#input---configuration) avsnittet förklaras dessa egenskaper.

Här är JavaScript-koden:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>In-attribut

Använd attributet [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) i [C#-klass bibliotek](functions-dotnet-class-library.md).

Information om vilka egenskaper för attribut som du kan konfigurera finns i [följande konfigurations avsnitt](#input---configuration).

## <a name="input---configuration"></a>Indatamängds konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `MobileTable` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
| **bastyp**| saknas | Måste anges till "mobileTable"|
| **position**| saknas |Måste anges till "i"|
| **Namn**| saknas | Namnet på Indataparametern i Function-signaturen.|
|**tableName** |**TableName**|Namn på den mobila appens data tabell|
| **id**| **Identitet** | Identifieraren för den post som ska hämtas. Kan vara statisk eller baserad på den utlösare som anropar funktionen. Om du till exempel använder en Queue-utlösare för din funktion `"id": "{queueTrigger}"` använder det strängvärdet i Queue-meddelandet som post-ID för hämtning.|
|**anslutningen**|**Anslutning**|Namnet på en app-inställning som har appens URL för mobilapp. Funktionen använder denna URL för att skapa nödvändiga REST-åtgärder mot mobilappen. Skapa en app-inställning i din Function-app som innehåller webbappens URL och ange sedan namnet på appens inställning i `connection` egenskapen i den angivna bindningen. URL: en ser ut som `http://<appname>.azurewebsites.net` .
|**apiKey**|**ApiKey**|Namnet på en app-inställning som har din Mobilapps API-nyckel. Ange API-nyckeln om du [implementerar en API-nyckel i din Node.js mobilapp](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)eller [implementerar en API-nyckel i din .net-mobilapp](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). För att tillhandahålla nyckeln skapar du en app-inställning i din Function-app som innehåller API-nyckeln och lägger sedan till `apiKey` egenskapen i din databindning med namnet på appens inställning. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Dela inte API-nyckeln med dina mobilappar. Den bör endast distribueras säkert till klienter på klient sidan, t. ex. Azure Functions. Azure Functions lagrar anslutnings information och API-nycklar som appinställningar så att de inte checkas in i lagrings platsen för käll kontroll. Detta skyddar känslig information.

## <a name="input---usage"></a>Inmatad användning

När posten med det angivna ID: t hittas i C#-funktioner skickas den till den namngivna [JObject](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) -parametern. När posten inte hittas är parametervärdet `null` . 

I JavaScript-funktioner skickas posten till `context.bindings.<name>` objektet. När posten inte hittas är parametervärdet `null` . 

I C#-och F #-funktioner skickas alla ändringar du gör i Indataposten automatiskt tillbaka till tabellen när funktionen avslutas. Du kan inte ändra en post i JavaScript-funktioner.

## <a name="output"></a>Utdata

Använd Mobile Apps utgående bindning för att skriva en ny post i en Mobile Apps tabell.  

## <a name="output---example"></a>Utdata-exempel

Se språkspecifika exempel:

* [C#](#output---c-example)
* [C#-skript (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Utdata – C#-exempel

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som utlöses av ett köat meddelande och skapar en post i en mobilapp-tabell.

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

### <a name="output---c-script-example"></a>Utdata – C#-skript exempel

I följande exempel visas en Mobile Apps utgående bindning i en *function.jspå* filen och en [C#-skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen utlöses av ett köat meddelande och skapar en ny post med hårdkodat värde för `Text` egenskapen.

Här är bindnings data i *function.jspå* filen:

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
        "type": "queueTrigger",
        "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "out"
    }
]
}
```

I [konfigurations](#output---configuration) avsnittet förklaras dessa egenskaper.

Här är C#-skript koden:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Exempel på utdata-JavaScript

I följande exempel visas en Mobile Apps utgående bindning i en *function.jsi* filen och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen utlöses av ett köat meddelande och skapar en ny post med hårdkodat värde för `Text` egenskapen.

Här är bindnings data i *function.jspå* filen:

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
        "type": "queueTrigger",
        "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "out"
    }
],
"disabled": false
}
```

I [konfigurations](#output---configuration) avsnittet förklaras dessa egenskaper.

Här är JavaScript-koden:

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>Utdata-attribut

Använd attributet [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) i [C#-klass bibliotek](functions-dotnet-class-library.md).

Information om vilka egenskaper för attribut som du kan konfigurera finns i [utdata-Configuration](#output---configuration). Här är ett `MobileTable` attribut exempel i en metodsignatur:

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    ...
}
```

Ett fullständigt exempel finns i [exempel på utdata-C#](#output---c-example).

## <a name="output---configuration"></a>Utdata-konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `MobileTable` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
| **bastyp**| saknas | Måste anges till "mobileTable"|
| **position**| saknas |Måste anges till "out"|
| **Namn**| saknas | Namnet på Utdataparametern i Function Signature.|
|**tableName** |**TableName**|Namn på den mobila appens data tabell|
|**anslutningen**|**MobileAppUriSetting**|Namnet på en app-inställning som har appens URL för mobilapp. Funktionen använder denna URL för att skapa nödvändiga REST-åtgärder mot mobilappen. Skapa en app-inställning i din Function-app som innehåller webbappens URL och ange sedan namnet på appens inställning i `connection` egenskapen i den angivna bindningen. URL: en ser ut som `http://<appname>.azurewebsites.net` .
|**apiKey**|**ApiKeySetting**|Namnet på en app-inställning som har din Mobilapps API-nyckel. Ange API-nyckeln om du [implementerar en API-nyckel i din Node.js Server del för mobilappar](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)eller [IMPLEMENTERAr en API-nyckel i din server del för .net-mobilappar](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). För att tillhandahålla nyckeln skapar du en app-inställning i din Function-app som innehåller API-nyckeln och lägger sedan till `apiKey` egenskapen i din databindning med namnet på appens inställning. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Dela inte API-nyckeln med dina mobilappar. Den bör endast distribueras säkert till klienter på klient sidan, t. ex. Azure Functions. Azure Functions lagrar anslutnings information och API-nycklar som appinställningar så att de inte checkas in i lagrings platsen för käll kontroll. Detta skyddar känslig information.

## <a name="output---usage"></a>Utmatnings användning

I C#-skript funktioner använder du en namngiven utdataparameter av typen `out object` för att få åtkomst till den utgående posten. I C#-klass bibliotek `MobileTable` kan attributet användas med någon av följande typer:

* `ICollector<T>` eller `IAsyncCollector<T>` , där `T` är antingen `JObject` en typ med en `public string Id` egenskap.
* `out JObject`
* `out T` eller `out T[]` , där `T` är en typ med en `public string Id` egenskap.

Använd `context.bindings.<name>` för att komma åt output-posten i Node.js funktioner.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)
