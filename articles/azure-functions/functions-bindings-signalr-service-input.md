---
title: Bindnings bindning för Azure Functions SignalR-tjänst
description: Lär dig att returnera en URL för en Signals service-slutpunkt och åtkomsttoken i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 3f3a99c83d4a18f3085419b91be947dd67f8eec4
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763327"
---
# <a name="signalr-service-input-binding-for-azure-functions"></a>Signal bindning för signal tjänst för Azure Functions

Innan en klient kan ansluta till Azure SignalR service måste den Hämta tjänstens slut punkts-URL och en giltig åtkomsttoken. *SignalRConnectionInfo* -databindningen genererar URL: en för SignalR-tjänstens slut punkt och en giltig token som används för att ansluta till tjänsten. Eftersom token är tidsbegränsad och kan användas för att autentisera en viss användare för en anslutning, ska du inte cachelagra token eller dela den mellan klienter. En HTTP-utlösare som använder denna bindning kan användas av klienter för att hämta anslutnings informationen.

Mer information om hur den här bindningen används för att skapa en "Negotiate"-funktion som kan användas av en Signals-klient-SDK finns i [artikeln Azure Functions utveckling och konfiguration](../azure-signalr/signalr-concept-serverless-development-config.md) i dokumentationen för SignalR-tjänstens begrepp.

Information om konfiguration och konfigurations information finns i [översikten](functions-bindings-signalr-service.md).

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar anslutnings information för signaler med hjälp av den inkommande bindningen och returnerar den över http.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

I följande exempel visas en signal för anslutnings information om signalering i en *function.jspå* fil och en [C#-skript funktion](functions-reference-csharp.md) som använder bindningen för att returnera anslutnings informationen.

Här är bindnings data i *function.jspå* filen:

Exempel function.jspå:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Här är C#-skript koden:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en signal för anslutnings information om signalering i en *function.jsi* filen och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen för att returnera anslutnings informationen.

Här är bindnings data i *function.jspå* filen:

Exempel function.jspå:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Här är JavaScript-koden:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas en signal för anslutnings information om signalering i en *function.jspå* fil och en [python-funktion](functions-reference-python.md) som använder bindningen för att returnera anslutnings informationen.

Här är bindnings data i *function.jspå* filen:

Exempel function.jspå:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Här är python-koden:

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

I följande exempel visas en [Java-funktion](functions-reference-java.md) som hämtar anslutnings information för signaler med hjälp av den inkommande bindningen och returnerar den över http.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="authenticated-tokens"></a>Autentiserade tokens

Om funktionen utlöses av en autentiserad klient kan du lägga till ett användar-ID-anspråk till den genererade token. Du kan enkelt lägga till autentisering i en Function-app med hjälp av [App Service autentisering](../app-service/overview-authentication-authorization.md).

App Service autentisering anger HTTP-huvuden med namnet `x-ms-client-principal-id` och `x-ms-client-principal-name` som innehåller den autentiserade användarens huvud-ID respektive namn.

# <a name="c"></a>[C#](#tab/csharp)

Du kan ange `UserId` egenskapen för bindningen till värdet från endera sidhuvudet med ett [bindnings uttryck](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` eller `{headers.x-ms-client-principal-name}` .

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Du kan ange `userId` egenskapen för bindningen till värdet från endera sidhuvudet med ett [bindnings uttryck](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` eller `{headers.x-ms-client-principal-name}` .

Exempel function.jspå:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Här är C#-skript koden:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Du kan ange `userId` egenskapen för bindningen till värdet från endera sidhuvudet med ett [bindnings uttryck](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` eller `{headers.x-ms-client-principal-name}` .

Exempel function.jspå:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Här är JavaScript-koden:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

Du kan ange `userId` egenskapen för bindningen till värdet från endera sidhuvudet med ett [bindnings uttryck](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` eller `{headers.x-ms-client-principal-name}` .

Exempel function.jspå:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Här är python-koden:

```python
def main(req: func.HttpRequest, connectionInfo: str) -> func.HttpResponse:
    # connectionInfo contains an access key token with a name identifier
    # claim set to the authenticated user
    return func.HttpResponse(
        connectionInfo,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

Du kan ange `userId` egenskapen för bindningen till värdet från endera sidhuvudet med ett [bindnings uttryck](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` eller `{headers.x-ms-client-principal-name}` .

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="next-steps"></a>Nästa steg

- [Hantera meddelanden från SignalR-tjänsten (Utlös bindning)](./functions-bindings-signalr-service-trigger.md)
- [Skicka signal tjänst meddelanden (utgående bindning)](./functions-bindings-signalr-service-output.md) 
