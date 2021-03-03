---
title: Azure Functions HTTP-utlösare
description: Lär dig att anropa en Azure-funktion via HTTP.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: a9bb87206ccb0dca56c1744d5578eac7a17418c7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726401"
---
# <a name="azure-functions-http-trigger"></a>Azure Functions HTTP-utlösare

Med HTTP-utlösaren kan du anropa en funktion med en HTTP-begäran. Du kan använda en HTTP-utlösare för att bygga API: er utan server och svara på Webhooks.

Standard retur värdet för en HTTP-utlöst funktion är:

- `HTTP 204 No Content` med en tom brödtext i funktionerna 2. x och högre
- `HTTP 200 OK` med en tom brödtext i funktionerna 1. x

Konfigurera en [utgående bindning](./functions-bindings-http-webhook-output.md)för att ändra http-svaret.

Mer information om HTTP-bindningar finns i [Översikt](./functions-bindings-http-webhook.md) och [utgående bindnings referens](./functions-bindings-http-webhook-output.md).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som söker efter en `name` parameter antingen i frågesträngen eller i bröd texten i http-begäran. Observera att returvärdet används för utgående bindning, men ett attribut för retur värde är inte obligatoriskt.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];
    
    string requestBody = String.Empty;
    using (StreamReader streamReader =  new  StreamReader(req.Body))
    {
        requestBody = await streamReader.ReadToEndAsync();
    }
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

I följande exempel visas en trigger-bindning i en *function.jspå* filen och en [C#-skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen söker efter en `name` parameter i frågesträngen eller bröd texten i HTTP-begäran.

Här är *function.jspå* filen:

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

Här är C#-skript kod som binder till `HttpRequest` :

```cs
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];
    
    string requestBody = String.Empty;
    using (StreamReader streamReader =  new  StreamReader(req.Body))
    {
        requestBody = await streamReader.ReadToEndAsync();
    }
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

Du kan binda till ett anpassat objekt i stället för `HttpRequest` . Det här objektet skapas från bröd texten i begäran och tolkas som JSON. På samma sätt kan en typ skickas till HTTP-svarets utgående bindning och returneras som svars text, tillsammans med en `200` status kod.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(Person person, ILogger log)
{   
    return person.Name != null
        ? (ActionResult)new OkObjectResult($"Hello, {person.Name}")
        : new BadRequestObjectResult("Please pass an instance of Person.");
}

public class Person {
     public string Name {get; set;}
}
```

# <a name="java"></a>[Java](#tab/java)

* [Läs parameter från frågesträngen](#read-parameter-from-the-query-string)
* [Läs brödtext från en POST-begäran](#read-body-from-a-post-request)
* [Läs parameter från en väg](#read-parameter-from-a-route)
* [Läs POJO-brödtext från en POST-begäran](#read-pojo-body-from-a-post-request)

I följande exempel visas bindningen för HTTP-utlösare.

#### <a name="read-parameter-from-the-query-string"></a>Läs parameter från frågesträngen

Det här exemplet läser en parameter, som heter `id` , från frågesträngen och använder den för att bygga ett JSON-dokument som returneras till klienten, med innehålls typ `application/json` .

```java
@FunctionName("TriggerStringGet")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<String>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("GET parameters are: " + request.getQueryParameters());

    // Get named parameter
    String id = request.getQueryParameters().getOrDefault("id", "");

    // Convert and display
    if (id.isEmpty()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String name = "fake_name";
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-body-from-a-post-request"></a>Läs brödtext från en POST-begäran

Det här exemplet läser bröd texten i en POST-begäran, som en `String` , och använder den för att bygga ett JSON-dokument som returneras till klienten, med innehålls typ `application/json` .

```java
    @FunctionName("TriggerStringPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(""));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String body = request.getBody().get();
            final String jsonDocument = "{\"id\":\"123456\", " + 
                                         "\"description\": \"" + body + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-parameter-from-a-route"></a>Läs parameter från en väg

Det här exemplet läser en obligatorisk parameter, med namnet `id` och en valfri parameter `name` från väg Sök vägen, och använder dem för att bygga ett JSON-dokument som returneras till klienten, med innehålls typ `application/json` . T

```java
@FunctionName("TriggerStringRoute")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS,
            route = "trigger/{id}/{name=EMPTY}") // name is optional and defaults to EMPTY
        HttpRequestMessage<Optional<String>> request,
        @BindingName("id") String id,
        @BindingName("name") String name,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Route parameters are: " + id);

    // Convert and display
    if (id == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-pojo-body-from-a-post-request"></a>Läs POJO-brödtext från en POST-begäran

Här är koden för klassen som `ToDoItem` refereras till i det här exemplet:

```java

public class ToDoItem {

  private String id;
  private String description;  

  public ToDoItem(String id, String description) {
    this.id = id;
    this.description = description;
  }

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}

```

I det här exemplet läses bröd texten i en POST-begäran. Begär ande texten avserialiseras automatiskt till ett `ToDoItem` -objekt och returneras till klienten med innehålls typ `application/json` . `ToDoItem`Parametern serialiseras av Functions-körningen när den tilldelas till `body` egenskapen för `HttpMessageResponse.Builder` klassen.

```java
@FunctionName("TriggerPojoPost")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.POST}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<ToDoItem>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Request body is: " + request.getBody().orElse(null));

    // Check request body
    if (!request.getBody().isPresent()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final ToDoItem body = request.getBody().get();
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(body)
                        .build();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en trigger-bindning i en *function.jsi* filen och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen söker efter en `name` parameter i frågesträngen eller bröd texten i HTTP-begäran.

Här är *function.jspå* filen:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

Här är JavaScript-koden:

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

I följande exempel visas en trigger-bindning i en *function.jsi* filen och en [PowerShell-funktion](functions-reference-node.md). Funktionen söker efter en `name` parameter i frågesträngen eller bröd texten i HTTP-begäran.

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    }
  ]
}
```

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

$body = "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."

if ($name) {
    $body = "Hello, $name. This HTTP triggered function executed successfully."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body       = $body
})
```


# <a name="python"></a>[Python](#tab/python)

I följande exempel visas en trigger-bindning i en *function.jsi* filen och en [python-funktion](functions-reference-python.md) som använder bindningen. Funktionen söker efter en `name` parameter i frågesträngen eller bröd texten i HTTP-begäran.

Här är *function.jspå* filen:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "$return"
        }
    ]
}
```

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

Här är python-koden:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

I [C#-klass bibliotek](functions-dotnet-class-library.md) och Java `HttpTrigger` är attributet tillgängligt för att konfigurera funktionen.

Du kan ställa in en autentiseringsnivå och tillåta HTTP-metoder i parametrar för attributhierarkier, webhook-typ och en Route-mall. Mer information om de här inställningarna finns i [konfiguration](#configuration).

# <a name="c"></a>[C#](#tab/csharp)

Det här exemplet visar hur du använder attributet [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) .

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Ett fullständigt exempel finns i [utlösaren exempel](#example).

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Attribut stöds inte av C#-skript.

# <a name="java"></a>[Java](#tab/java)

Det här exemplet visar hur du använder attributet [HttpTrigger](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java) .

```java
@FunctionName("HttpTriggerJava")
public HttpResponseMessage<String> HttpTrigger(
        @HttpTrigger(name = "req",
                     methods = {"get"},
                     authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<String> request,
        final ExecutionContext context) {

    ...
}
```

Ett fullständigt exempel finns i [utlösaren exempel](#example).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Attribut stöds inte av PowerShell.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av python.

---

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `HttpTrigger` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
| **bastyp** | saknas| Required-måste anges till `httpTrigger` . |
| **position** | saknas| Required-måste anges till `in` . |
| **Namn** | saknas| Obligatoriskt – variabel namnet som används i funktions koden för begäran eller begär ande texten. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Bestämmer vilka nycklar, om sådana, som måste finnas på begäran för att kunna anropa funktionen. Behörighets nivån kan vara något av följande värden: <ul><li><code>anonymous</code>&mdash;Ingen API-nyckel krävs.</li><li><code>function</code>&mdash;En Function-/regionsspecifika API-nyckel krävs. Detta är standardvärdet om inget anges.</li><li><code>admin</code>&mdash;Huvud nyckeln måste anges.</li></ul> Mer information finns i avsnittet om [auktoriseringsregler](#authorization-keys). |
| **indatametod** |**Metoder** | En matris med de HTTP-metoder som funktionen svarar på. Om inget anges svarar funktionen på alla HTTP-metoder. Se [anpassa HTTP-slutpunkten](#customize-the-http-endpoint). |
| **route** | **Styra** | Definierar den vägfil som styr till vilka URL-adresser din funktion svarar. Standardvärdet om inget värde anges är `<functionname>` . Mer information finns i [anpassa HTTP-slutpunkten](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Stöds endast för version 1. x-körningsmiljön._<br/><br/>Konfigurerar HTTP-utlösaren så att den fungerar som en [webhook](https://en.wikipedia.org/wiki/Webhook) -mottagare för den angivna providern. Ange inte `methods` egenskapen om du anger den här egenskapen. Webhook-typen kan vara något av följande värden:<ul><li><code>genericJson</code>&mdash;En allmän webhook-slutpunkt utan logik för en speciell Provider. Den här inställningen begränsar begär anden till endast de som använder HTTP POST och med `application/json` innehålls typen.</li><li><code>github</code>&mdash;Funktionen svarar på GitHub- [webhookar](https://developer.github.com/webhooks/). Använd inte egenskapen  _authLevel_ med GitHub-webhookar. Mer information finns i avsnittet GitHub Webhooks senare i den här artikeln.</li><li><code>slack</code>&mdash;Funktionen svarar på slack- [webhookar](https://api.slack.com/outgoing-webhooks). Använd inte _authLevel_ -egenskapen med slack-webhookar. Mer information finns i avsnittet om slack-webhookar längre fram i den här artikeln.</li></ul>|

## <a name="payload"></a>Innehållet

Indatatypen trigger deklareras som antingen `HttpRequest` eller som en anpassad typ. Om du väljer `HttpRequest` får du fullständig åtkomst till objektet Request. För en anpassad typ försöker körningen parsa JSON-begärantext för att ange objekt egenskaperna.

## <a name="customize-the-http-endpoint"></a>Anpassa HTTP-slutpunkten

Som standard när du skapar en funktion för en HTTP-utlösare kan funktionen adresseras med en Route i formuläret:

```http
http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>
```

Du kan anpassa den här vägen med hjälp av den valfria `route` egenskapen i HTTP-utlösaren inkommande bindning. Följande *function.jsi* filen definierar till exempel en `route` egenskap för en http-utlösare:

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

Med den här konfigurationen kan funktionen nu adresseras med följande väg i stället för den ursprungliga vägen.

```
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

Den här konfigurationen tillåter att funktions koden stöder två parametrar i adress, _kategori_ och _ID_.

# <a name="c"></a>[C#](#tab/csharp)

Du kan använda alla [begränsningar för webb-API-routning](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) med parametrarna. Följande C#-funktions kod använder båda parametrarna.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Du kan använda alla [begränsningar för webb-API-routning](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) med parametrarna. Följande C#-funktions kod använder båda parametrarna.

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="java"></a>[Java](#tab/java)

Funktions körnings kontexten är egenskaper som deklarerats i `HttpTrigger` attributet. Med attributet kan du definiera väg parametrar, auktoriseringsregler, HTTP-verb och instansen för inkommande begäran.

Väg parametrar definieras via `HttpTrigger` attributet.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerJava {
    public HttpResponseMessage<String> HttpTrigger(
            @HttpTrigger(name = "req",
                         methods = {"get"},
                         authLevel = AuthorizationLevel.FUNCTION,
                         route = "products/{category:alpha}/{id:int}") HttpRequestMessage<String> request,
            @BindingName("category") String category,
            @BindingName("id") int id,
            final ExecutionContext context) {

        String message = String.format("Category  %s, ID: %d", category, id);
        return request.createResponseBuilder(HttpStatus.OK).body(message).build();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I Node tillhandahåller Functions-körningen begär ande texten från `context` objektet. Mer information finns i exempel på [JavaScript-utlösare](#example).

I följande exempel visas hur du läser väg parametrar från `context.bindingData` .

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;
    var message = `Category: ${category}, ID: ${id}`;

    context.res = {
        body: message;
    }

    context.done();
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Väg parametrar som deklarerats i *function.jsi* filen är tillgängliga som en egenskap för `$Request.Params` objektet.

```powershell
$Category = $Request.Params.category
$Id = $Request.Params.id

$Message = "Category:" + $Category + ", ID: " + $Id

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body = $Message
})
```

# <a name="python"></a>[Python](#tab/python)

Kontexten för körning av funktioner exponeras via en parameter som har deklarerats som `func.HttpRequest` . Den här instansen gör att en funktion kan komma åt data väg parametrar, fråga sträng värden och metoder som gör att du kan returnera HTTP-svar.

När de har definierats är väg parametrarna tillgängliga för funktionen genom att anropa- `route_params` metoden.

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

---

Som standard har alla funktions vägar prefixet med *API*. Du kan också anpassa eller ta bort prefixet med hjälp av `extensions.http.routePrefix` egenskapen i [host.js](functions-host-json.md) i filen. I följande exempel tar vi bort *API* -prefixet genom att använda en tom sträng för prefixet i *host.js* filen.

```json
{
    "extensions": {
        "http": {
            "routePrefix": ""
        }
    }
}
```

## <a name="using-route-parameters"></a>Använda väg parametrar

Väg parametrar som definierar en funktions `route` mönster är tillgängliga för varje bindning. Om du till exempel har en väg definierad som, `"route": "products/{id}"` kan en tabell lagrings bindning använda värdet för `{id}` parametern i bindnings konfigurationen.

Följande konfiguration visar hur `{id}` parametern skickas till bindningens `rowKey` .

```json
{
    "type": "table",
    "direction": "in",
    "name": "product",
    "partitionKey": "products",
    "tableName": "products",
    "rowKey": "{id}"
}
```

När du använder väg parametrar `invoke_URL_template` skapas automatiskt en för din funktion. Dina klienter kan använda URL-mallen för att förstå de parametrar som de behöver för att skicka in URL: en när funktionen anropas med hjälp av dess URL. Navigera till en av dina HTTP-utlösta funktioner i [Azure Portal](https://portal.azure.com) och välj **Hämta funktions webb adress**.

Du kan via programmering få åtkomst till `invoke_URL_template` med hjälp av Azure Resource Manager-API: er för [list funktioner](/rest/api/appservice/webapps/listfunctions) eller [Hämta funktion](/rest/api/appservice/webapps/getfunction).

## <a name="working-with-client-identities"></a>Arbeta med klient identiteter

Om din Function-app använder [App Service autentisering/auktorisering](../app-service/overview-authentication-authorization.md)kan du Visa information om autentiserade klienter från din kod. Den här informationen är tillgänglig som [begär ande huvuden som injiceras av plattformen](../app-service/app-service-authentication-how-to.md#access-user-claims).

Du kan också läsa denna information från data bindnings data. Den här funktionen är endast tillgänglig för functions-körningen i 2. x och högre. Det är också för närvarande endast tillgängligt för .NET-språk.

# <a name="c"></a>[C#](#tab/csharp)

Information om autentiserade klienter är tillgänglig som en [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal är tillgänglig som en del av kontexten för begäran som visas i följande exempel:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Alternativt kan ClaimsPrincipal bara inkluderas som en ytterligare parameter i funktions under skriften:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Information om autentiserade klienter är tillgänglig som en [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal är tillgänglig som en del av kontexten för begäran som visas i följande exempel:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Alternativt kan ClaimsPrincipal bara inkluderas som en ytterligare parameter i funktions under skriften:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="java"></a>[Java](#tab/java)

Den autentiserade användaren är tillgänglig via [http-huvuden](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Den autentiserade användaren är tillgänglig via [http-huvuden](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Den autentiserade användaren är tillgänglig via [http-huvuden](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="python"></a>[Python](#tab/python)

Den autentiserade användaren är tillgänglig via [http-huvuden](../app-service/app-service-authentication-how-to.md#access-user-claims).


---

## <a name="function-access-keys"></a><a name="authorization-keys"></a>Funktions åtkomst nycklar

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

## <a name="obtaining-keys"></a>Hämta nycklar

Nycklar lagras som en del av din Function-app i Azure och är krypterade i vila. Om du vill visa dina nycklar, skapa nya eller rulla nycklar till nya värden, navigerar du till någon av dina HTTP-utlösta funktioner i [Azure Portal](https://portal.azure.com) och väljer **funktions nycklar**.

Du kan också hantera värd nycklar. Navigera till Function-appen i [Azure Portal](https://portal.azure.com) och välj **app-nycklar**.

Du kan hämta funktions-och värd nycklar program mässigt med hjälp av Azure Resource Manager API: er. Det finns API: er för att [lista funktions nycklar](/rest/api/appservice/webapps/listfunctionkeys) och [lista värd nycklar](/rest/api/appservice/webapps/listhostkeys), och när du använder distributions platser motsvarar motsvarande API: er en [lista över funktions nycklar](/rest/api/appservice/webapps/listfunctionkeysslot) och [listar värd nycklar](/rest/api/appservice/webapps/listhostkeysslot).

Du kan också skapa nya funktions-och värd nycklar program mässigt med hjälp av [funktionen Skapa eller uppdatera funktion hemlighet](/rest/api/appservice/webapps/createorupdatefunctionsecret), [skapa eller uppdatera funktionens hemliga plats](/rest/api/appservice/webapps/createorupdatefunctionsecretslot), [skapa eller uppdatera värd hemlighet](/rest/api/appservice/webapps/createorupdatehostsecret) och [skapa eller uppdatera värd hemliga plats](/rest/api/appservice/webapps/createorupdatehostsecretslot) -API: er.

Funktions-och värd nycklar kan tas bort program mässigt med hjälp av [funktionen Ta bort funktion hemlighet](/rest/api/appservice/webapps/deletefunctionsecret), [ta bort funktionens hemliga plats](/rest/api/appservice/webapps/deletefunctionsecretslot), [ta bort värd hemlighet](/rest/api/appservice/webapps/deletehostsecret)och ta bort API: er för [värd hemliga platser](/rest/api/appservice/webapps/deletehostsecretslot) .

Du kan också använda de [äldre API: erna för nyckel hantering för att få funktions nycklar](https://github.com/Azure/azure-functions-host/wiki/Key-management-API), men med hjälp av Azure Resource Manager API: er rekommenderas i stället.

## <a name="api-key-authorization"></a>Auktorisering av API-nyckel

De flesta mallar för HTTP-utlösare kräver en API-nyckel i begäran. Så din HTTP-begäran normalt ser ut som följande URL:

```http
https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>
```

Nyckeln kan ingå i en frågesträng `code` som heter, som ovan. Det kan också ingå i ett `x-functions-key` http-huvud. Nyckelns värde kan vara vilken funktions nyckel som helst som har definierats för funktionen eller vilken värd nyckel som helst.

Du kan tillåta anonyma begär Anden som inte kräver nycklar. Du kan också kräva att huvud nyckeln används. Du ändrar standard nivån för autentisering med hjälp av `authLevel` egenskapen i bindnings-JSON. Mer information finns i avsnittet om [Utlösar-konfiguration](#configuration).

> [!NOTE]
> När funktioner körs lokalt är auktorisering inaktiverat oavsett den angivna inställningen för autentiseringsnivå. När du har publicerat till Azure `authLevel` tillämpas inställningen i utlösaren. Nycklar krävs fortfarande när de körs [lokalt i en behållare](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally).


## <a name="secure-an-http-endpoint-in-production"></a>Skydda en HTTP-slutpunkt i produktion

Om du vill skydda funktions slut punkter i produktion fullständigt bör du överväga att implementera någon av följande funktioner på App-nivå. När du använder någon av dessa säkerhets metoder på App-nivå bör du ställa in den HTTP-utlösta funktions verifierings nivån på `anonymous` .

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

#### <a name="deploy-your-function-app-in-isolation"></a>Distribuera din Function-app i isolering

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

## <a name="webhooks"></a>Webhooks

> [!NOTE]
> Webhook-läge är bara tillgängligt för version 1. x av Functions-körningen. Den här ändringen gjordes för att förbättra prestanda för HTTP-utlösare i version 2. x och högre.

I version 1. x tillhandahåller webhook-mallar ytterligare verifiering för webhook-nyttolaster. I version 2. x och högre fungerar bas-HTTP-utlösaren fortfarande och är den rekommenderade metoden för Webhooks. 

### <a name="github-webhooks"></a>GitHub-webhookar

Om du vill svara på GitHub Webhooks måste du först skapa en funktion med en HTTP-utlösare och ange egenskapen **webHookType** till `github` . Kopiera sedan dess URL och API-nyckel till sidan **Lägg till webhook** i GitHub-lagringsplatsen. 

![Skärm bild som visar hur du lägger till en webhook för din funktion.](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Slack-webhookar

Slack-webhooken genererar en token åt dig i stället för att låta dig ange den, så du måste konfigurera en funktions bestämd nyckel med token från slack. Se [auktoriseringsregler](#authorization-keys).

## <a name="webhooks-and-keys"></a>Webhooks och nycklar

Webhook-auktoriseringen hanteras av komponenten webhook receiver, en del av HTTP-utlösaren och mekanismen varierar beroende på typen av webhook. Varje mekanism förlitar sig på en nyckel. Som standard används funktions nyckeln med namnet "default". Om du vill använda en annan nyckel konfigurerar du webhook-providern för att skicka nyckel namnet med begäran på något av följande sätt:

* **Frågesträng**: providern överför nyckel namnet i `clientid` parametern frågesträng, till exempel `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>` .
* **Begär ande huvud**: providern överför nyckel namnet i `x-functions-clientid` rubriken.

## <a name="content-types"></a>Innehålls typer

Att skicka binära data och formulär data till en icke-C #-funktion kräver att du använder rätt innehålls typ rubrik. Innehålls typer som stöds är `octet-stream` för binära data-och [multipart-typer](https://www.iana.org/assignments/media-types/media-types.xhtml#multipart).

### <a name="known-issues"></a>Kända problem

I icke-C #-funktioner skickas begär Anden som skickas med innehålls typen `image/jpeg` till ett `string` värde som skickas till funktionen. I sådana fall kan du manuellt konvertera `string` värdet till en byte-matris för att få åtkomst till rå binära data.

## <a name="limits"></a>Gränser

Längden på HTTP-begäran är begränsad till 100 MB (104 857 600 byte) och URL-längden är begränsad till 4 KB (4 096 byte). De här gränserna anges av `httpRuntime` elementet i körningens [Web.config fil](https://github.com/Azure/azure-functions-host/blob/v3.x/src/WebJobs.Script.WebHost/web.config).

Om en funktion som använder HTTP-utlösaren inte slutförs inom 230 sekunder, tids gränsen [uppAzure load BALANCERS](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) och returnerar ett HTTP 502-fel. Funktionen fortsätter att köras men kan inte returnera ett HTTP-svar. För långvariga funktioner rekommenderar vi att du följer asynkrona mönster och returnerar en plats där du kan pinga status för begäran. Information om hur länge en funktion kan köras finns i [plan för skalning och värd förbrukning](functions-scale.md#timeout).


## <a name="next-steps"></a>Nästa steg

- [Returnera ett HTTP-svar från en funktion](./functions-bindings-http-webhook-output.md)