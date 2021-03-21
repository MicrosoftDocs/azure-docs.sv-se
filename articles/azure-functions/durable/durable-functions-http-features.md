---
title: HTTP-funktioner i Durable Functions-Azure Functions
description: Lär dig mer om de integrerade HTTP-funktionerna i Durable Functions-tillägget för Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 64d40de50f21811a56318971de1836abc8fbf8c9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93027269"
---
# <a name="http-features"></a>HTTP-funktioner

Durable Functions har flera funktioner som gör det enkelt att införliva varaktiga dirigeringar och entiteter i HTTP-arbetsflöden. Den här artikeln beskriver några av dessa funktioner.

## <a name="exposing-http-apis"></a>Exponerar HTTP-API: er

Dirigeringar och entiteter kan anropas och hanteras med HTTP-begäranden. Durable Functions-tillägget visar inbyggda HTTP-API: er. Den innehåller också API: er för att interagera med Orchestration och entiteter från HTTP-utlösta funktioner.

### <a name="built-in-http-apis"></a>Inbyggda HTTP API: er

Durable Functions-tillägget lägger automatiskt till en uppsättning HTTP-API: er till Azure Functions värden. Med dessa API: er kan du interagera med och hantera dirigeringar och entiteter utan att skriva någon kod.

Följande inbyggda HTTP-API: er stöds.

* [Starta nytt dirigering](durable-functions-http-api.md#start-orchestration)
* [Fråga om Orchestration-instans](durable-functions-http-api.md#get-instance-status)
* [Avsluta Orchestration-instans](durable-functions-http-api.md#terminate-instance)
* [Skicka en extern händelse till ett dirigering](durable-functions-http-api.md#raise-event)
* [Rensa Orchestration-historik](durable-functions-http-api.md#purge-single-instance-history)
* [Skicka en åtgärds händelse till en entitet](durable-functions-http-api.md#signal-entity)
* [Hämta status för en entitet](durable-functions-http-api.md#get-entity)
* [Fråga listan över entiteter](durable-functions-http-api.md#list-entities)

I [artikeln om http-API: er](durable-functions-http-api.md) finns en fullständig beskrivning av alla inbyggda http-API: er som exponeras av Durable Functions-tillägget.

### <a name="http-api-url-discovery"></a>HTTP API URL-identifiering

[Dirigerings klientens bindning](durable-functions-bindings.md#orchestration-client) exponerar API: er som kan generera lämpliga nytto laster för HTTP-svar. Det kan till exempel skapa ett svar som innehåller länkar till hanterings-API: er för en angiven Dirigerings instans. I följande exempel visas en funktion för HTTP-utlösare som visar hur du använder det här API: et för en ny Dirigerings instans:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

**function.json**

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

# <a name="python"></a>[Python](#tab/python)

**__init__. py**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    function_name = req.route_params['functionName']
    event_data = req.get_body()

    instance_id = await client.start_new(function_name, instance_id, event_data)
    
    logging.info(f"Started orchestration with ID = '{instance_id}'.")
    return client.create_check_status_response(req, instance_id)
```

**function.json**

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}",
      "methods": [
        "post",
        "get"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```

---

Att starta en Orchestrator-funktion med hjälp av funktionerna för HTTP-utlösare som visas tidigare kan göras med valfri HTTP-klient. Följande spiral kommando startar en Orchestrator-funktion med namnet `DoWork` :

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Next är ett exempel på ett svar för en dirigering som har `abc123` som sitt ID. Viss information har tagits bort för tydlighets skull.

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

I föregående exempel motsvarar vart och ett av fälten som slutar på `Uri` ett inbyggt HTTP-API. Du kan använda dessa API: er för att hantera mål Dirigerings instansen.

> [!NOTE]
> Formatet för webhook-URL: erna beror på vilken version av Azure Functionss värden som du kör. Föregående exempel är för Azure Functions 2,0-värden.

En beskrivning av alla inbyggda HTTP API: er finns i [http API-referensen](durable-functions-http-api.md).

### <a name="async-operation-tracking"></a>Asynkron åtgärds spårning

Det HTTP-svar som tidigare nämnts är utformat för att hjälpa till att implementera långvariga HTTP asynkrona API: er med Durable Functions. Det här mönstret kallas ibland för *avsökningens konsument mönster*. Klient/server-flödet fungerar på följande sätt:

1. Klienten utfärdar en HTTP-begäran om att starta en långvarig process som en Orchestrator-funktion.
1. Målets HTTP-utlösare returnerar ett HTTP 202-svar med ett plats huvud som har värdet "statusQueryGetUri".
1. Klienten avsöker URL: en i plats rubriken. Klienten fortsätter att se HTTP 202-svar med ett plats huvud.
1. När instansen har slutförts eller misslyckats returnerar slut punkten i plats huvudet HTTP 200.

Det här protokollet möjliggör samordning av långvariga processer med externa klienter eller tjänster som kan avsöka en HTTP-slutpunkt och följa plats rubriken. Både klient-och Server implementeringarna av det här mönstret är inbyggda i Durable Functions HTTP-API: er.

> [!NOTE]
> Som standard har alla HTTP-baserade åtgärder som tillhandahålls av [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) stöd för standard mönstret för asynkrona åtgärder. Den här funktionen gör det möjligt att bädda in en långsiktig, varaktig funktion som en del av ett Logic Apps-arbetsflöde. Du hittar mer information om Logic Apps stöd för asynkrona HTTP-mönster i [dokumentationen för Azure Logic Apps arbets flödes åtgärder och utlösare](../../logic-apps/logic-apps-workflow-actions-triggers.md).

> [!NOTE]
> Interaktioner med Orchestration kan göras från alla funktions typer, inte bara HTTP-utlösta funktioner.

Mer information om hur du hanterar dirigeringar och entiteter med hjälp av klient-API: er finns i [artikeln instans hantering](durable-functions-instance-management.md).

## <a name="consuming-http-apis"></a>Använda HTTP API: er

Som beskrivs i [Orchestrator-funktionens kod begränsningar](durable-functions-code-constraints.md)kan Orchestrator-funktioner inte göra I/O direkt. I stället anropar de vanligt vis [aktivitets funktioner](durable-functions-types-features-overview.md#activity-functions) som i/O-åtgärder.

Från och med Durable Functions 2,0 kan dirigeringar använda HTTP-API: er med hjälp av [bindningen för Dirigerings utlösare](durable-functions-bindings.md#orchestration-trigger).

Följande exempel kod visar en Orchestrator-funktion som gör en utgående HTTP-begäran:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const url = context.df.getInput();
    const response = yield context.df.callHttp("GET", url)

    if (response.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    url = context.get_input()
    response = yield context.call_http('GET', url)
    
    if response["statusCode"] >= 400:
        # handling of error codes goes here

main = df.Orchestrator.create(orchestrator_function)
```

---

Genom att använda åtgärden "anropa HTTP" kan du utföra följande åtgärder i dina Orchestrator-funktioner:

* Anropa HTTP-API: er direkt från Orchestration-funktioner, med vissa begränsningar som nämns senare.
* Har automatiskt stöd för status avsöknings mönster för HTTP 202 på klient sidan.
* Använd [Azure Managed Identities](../../active-directory/managed-identities-azure-resources/overview.md) för att göra auktoriserade http-anrop till andra Azure-slutpunkter.

Möjligheten att använda HTTP-API: er direkt från Orchestrator-funktioner är avsedd som en bekvämlighet för en viss uppsättning vanliga scenarier. Du kan implementera alla dessa funktioner själv med hjälp av aktivitets funktioner. I många fall kan aktivitets funktioner ge dig större flexibilitet.

### <a name="http-202-handling"></a>HTTP 202-hantering

API: t "anropa HTTP" kan automatiskt implementera klient sidan för avsökningens konsument mönster. Om ett anropat API returnerar ett HTTP 202-svar med ett plats huvud, avsöker Orchestrator-funktionen automatiskt plats resursen tills du får ett annat svar än 202. Det här svaret är svaret som returneras till Orchestrator-funktions koden.

> [!NOTE]
> Orchestrator-funktioner stöder också klient mönstret för avsökning på Server sidan, enligt beskrivningen i [asynkron åtgärds spårning](#async-operation-tracking). Det här stödet innebär att dirigeringar i en Function-app enkelt kan koordinera Orchestrator-funktionerna i andra Function-appar. Detta liknar [under Orchestration-](durable-functions-sub-orchestrations.md) konceptet, men med stöd för kommunikation mellan appar. Detta stöd är särskilt användbart för utveckling av appar i mikrotjänster.

### <a name="managed-identities"></a>Hanterade identiteter

Durable Functions inbyggt stöder anrop till API: er som accepterar Azure Active Directory-token (Azure AD) för auktorisering. Det här stödet använder [Azure Managed identiteter](../../active-directory/managed-identities-azure-resources/overview.md) för att hämta dessa tokens.

Följande kod är ett exempel på en .NET Orchestrator-funktion. Funktionen gör autentiserade anrop för att starta om en virtuell dator med hjälp av Azure Resource Manager [virtuella datorer REST API](/rest/api/compute/virtualmachines).

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    string apiVersion = "2019-03-01";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net/.default
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net/.default"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const subscriptionId = "mySubId";
    const resourceGroup = "myRG";
    const vmName = "myVM";
    const apiVersion = "2019-03-01";
    const tokenSource = new df.ManagedIdentityTokenSource("https://management.core.windows.net/.default");

    // get a list of the Azure subscriptions that I have access to
    const restartResponse = yield context.df.callHttp(
        "POST",
        `https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroup}/providers/Microsoft.Compute/virtualMachines/${vmName}/restart?api-version=${apiVersion}`,
        undefined, // no request content
        undefined, // no request headers (besides auth which is handled by the token source)
        tokenSource);

    return restartResponse;
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    subscription_id = "mySubId"
    resource_group = "myRg"
    vm_name = "myVM"
    api_version = "2019-03-01"
    token_source = df.ManagedIdentityTokenSource("https://management.core.windows.net/.default")

    # get a list of the Azure subscriptions that I have access to
    restart_response = yield context.call_http("POST", 
        f"https://management.azure.com/subscriptions/{subscription_id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}/restart?api-version={api_version}",
        None,
        None,
        token_source)
    return restart_response

main = df.Orchestrator.create(orchestrator_function)
```

---

I föregående exempel `tokenSource` är parametern konfigurerad för att hämta Azure AD-token för [Azure Resource Manager](../../azure-resource-manager/management/overview.md). Tokens identifieras av resurs-URI `https://management.core.windows.net/.default` : n. Exemplet förutsätter att den aktuella Function-appen körs lokalt eller har distribuerats som en Function-app med en hanterad identitet. Den lokala identiteten eller den hanterade identiteten antas ha behörighet att hantera virtuella datorer i den angivna resurs gruppen `myRG` .

Vid körning returnerar den konfigurerade token-källan automatiskt en OAuth 2,0-åtkomsttoken. Källan lägger sedan till token som en Bearer-token i Authorization-huvudet för den utgående begäran. Den här modellen är en förbättring av manuellt tillägg av auktoriseringsarkiv till HTTP-förfrågningar av följande anledningar:

* Uppdatering av token hanteras automatiskt. Du behöver inte bekymra dig om utgångna token.
* Tokens lagras aldrig i det hållbara Orchestration-läget.
* Du behöver inte skriva någon kod för att hantera hämtning av token.

Du kan hitta ett mer komplett exempel i det [förkompilerade C# RestartVMs-exemplet](https://github.com/Azure/azure-functions-durable-extension/blob/dev/samples/precompiled/RestartVMs.cs).

Hanterade identiteter är inte begränsade till Azure Resource Management. Du kan använda hanterade identiteter för att få åtkomst till alla API: er som accepterar Azure AD Bearer-token, inklusive Azure-tjänster från Microsoft och Web Apps från partner. En partners webbapp kan till och med vara en annan Function-app. En lista över Azure-tjänster från Microsoft som stöder autentisering med Azure AD finns i [Azure-tjänster som stöder Azure AD-autentisering](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

### <a name="limitations"></a>Begränsningar

Det inbyggda stödet för att anropa HTTP API: er är en bekvämlighets funktion. Det är inte lämpligt för alla scenarier.

HTTP-förfrågningar som skickats av Orchestrator-funktioner och deras svar är serialiserade och beständiga som Queue meddelanden. Detta köat beteende garanterar att HTTP-anrop är [pålitliga och säkra för omuppspelning av dirigering](durable-functions-orchestrations.md#reliability). Köernas beteende har dock också begränsningar:

* Varje HTTP-begäran omfattar ytterligare svars tid jämfört med en intern HTTP-klient.
* Meddelanden om stora förfrågningar eller svar som inte får plats i ett Queue-meddelande kan kraftigt försämra Dirigerings prestanda. Omkostnaderna för att avlasta meddelande nytto laster till Blob Storage kan orsaka potentiell prestanda försämring.
* Strömnings-, segment-och binära nytto laster stöds inte.
* Möjligheten att anpassa HTTP-klientens beteende är begränsad.

Om någon av dessa begränsningar kan påverka ditt användnings fall bör du i stället använda aktivitets funktioner och språkspecifika HTTP-klient bibliotek för att göra utgående HTTP-anrop.

> [!NOTE]
> Om du är .NET-utvecklare kan du undra varför den här funktionen använder **DurableHttpRequest** -och **DurableHttpResponse** -typerna i stället för de inbyggda .net- **HttpRequestMessage** -och **HttpResponseMessage** -typerna.
>
> Det här design valet är avsiktligt. Den främsta orsaken är att anpassade typer hjälper till att se till att användarna inte gör felaktiga antaganden om de funktioner som stöds av den interna HTTP-klienten. Typer som är speciella för Durable Functions också göra det möjligt att förenkla API-utformningen. De kan också enklare göra tillgängliga special funktioner som [hantering av identitets integrering](#managed-identities) och [utsöknings konsument mönster](#http-202-handling). 

### <a name="extensibility-net-only"></a>Utökning (endast .NET)

Det går att anpassa beteendet för dirigeringens interna HTTP-klient med hjälp av [Azure Functions .net-beroende inmatning](../functions-dotnet-dependency-injection.md). Den här funktionen kan vara användbar för att göra små beteende ändringar. Det kan också vara användbart för enhet som testar HTTP-klienten genom att mata in de blå objekten.

I följande exempel visas hur du använder beroende inmatning för att inaktivera TLS/SSL-certifikat verifiering för Orchestrator-funktioner som anropar externa HTTP-slutpunkter.

```csharp
public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        // Register own factory
        builder.Services.AddSingleton<
            IDurableHttpMessageHandlerFactory,
            MyDurableHttpMessageHandlerFactory>();
    }
}

public class MyDurableHttpMessageHandlerFactory : IDurableHttpMessageHandlerFactory
{
    public HttpMessageHandler CreateHttpMessageHandler()
    {
        // Disable TLS/SSL certificate validation (not recommended in production!)
        return new HttpClientHandler
        {
            ServerCertificateCustomValidationCallback =
                HttpClientHandler.DangerousAcceptAnyServerCertificateValidator,
        };
    }
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om varaktiga entiteter](durable-functions-entities.md)
