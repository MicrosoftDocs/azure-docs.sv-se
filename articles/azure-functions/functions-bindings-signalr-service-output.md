---
title: Utgående bindning för Azure Functions SignalR-tjänst
description: Lär dig mer om utgående bindning för signal tjänst för Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 159df4c228f74bb605c745e899d99bfff2aa511b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97763293"
---
# <a name="signalr-service-output-binding-for-azure-functions"></a>Utgående bindning för signal tjänst för Azure Functions

Använd *signalens* utgående bindning för att skicka ett eller flera meddelanden med Azure SignalR-tjänsten. Du kan sända ett meddelande till:

- Alla anslutna klienter
- Anslutna klienter som autentiserats till en speciell användare

Med utgående bindning kan du också hantera grupper.

Information om konfiguration och konfigurations information finns i [översikten](functions-bindings-signalr-service.md).

## <a name="broadcast-to-all-clients"></a>Sänd till alla klienter

I följande exempel visas en funktion som skickar ett meddelande med hjälp av utgående bindning till alla anslutna klienter. *Målet* är namnet på den metod som ska anropas på varje klient. Egenskapen *arguments* är en matris med noll eller flera objekt som ska skickas till klient metoden.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Här är bindnings data i *function.jspå* filen:

Exempel function.jspå:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Här är C#-skript koden:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Här är bindnings data i *function.jspå* filen:

Exempel function.jspå:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Här är JavaScript-koden:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="python"></a>[Python](#tab/python)

Här är bindnings data i *function.jspå* filen:

Exempel function.jspå:

```json
{
  "type": "signalR",
  "name": "outMessage",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Här är python-koden:

```python
def main(req: func.HttpRequest, outMessage: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    outMessage.set(json.dumps({
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="java"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

## <a name="send-to-a-user"></a>Skicka till en användare

Du kan bara skicka ett meddelande till anslutningar som har autentiserats för en användare genom att ange *användar-ID* i signal meddelandet.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Exempel function.jspå:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Här är C#-skript koden:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Exempel function.jspå:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Här är JavaScript-koden:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="python"></a>[Python](#tab/python)

Här är bindnings data i *function.jspå* filen:

Exempel function.jspå:

```json
{
  "type": "signalR",
  "name": "outMessage",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Här är python-koden:

```python
def main(req: func.HttpRequest, outMessage: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    outMessage.set(json.dumps({
        #message will only be sent to this user ID
        'userId': 'userId1',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="java"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.userId = "userId1";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

## <a name="send-to-a-group"></a>Skicka till en grupp

Du kan bara skicka ett meddelande till anslutningar som har lagts till i en grupp genom att ange *grupp namnet* i signal meddelande meddelandet.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Exempel function.jspå:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Här är C#-skript koden:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Exempel function.jspå:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Här är JavaScript-koden:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="python"></a>[Python](#tab/python)

Här är bindnings data i *function.jspå* filen:

Exempel function.jspå:

```json
{
  "type": "signalR",
  "name": "outMessage",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Här är python-koden:

```python
def main(req: func.HttpRequest, outMessage: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    outMessage.set(json.dumps({
        #message will only be sent to this group
        'groupName': 'myGroup',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="java"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.groupName = "myGroup";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

## <a name="group-management"></a>Grupphantering

Med SignalR-tjänsten kan användare läggas till i grupper. Meddelanden kan sedan skickas till en grupp. Du kan använda `SignalR` utgående bindning för att hantera en användares grupp medlemskap.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="add-user-to-a-group"></a>Lägg till användare i en grupp

I följande exempel läggs en användare till i en grupp.

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

### <a name="remove-user-from-a-group"></a>Ta bort användare från en grupp

I följande exempel tas en användare bort från en grupp.

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> `ClaimsPrincipal`Du måste ha konfigurerat autentiseringsinställningarna i Azure Functions för att få rätt bindning.

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

### <a name="add-user-to-a-group"></a>Lägg till användare i en grupp

I följande exempel läggs en användare till i en grupp.

Exempel *function.jspå*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*Kör. CSX*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

### <a name="remove-user-from-a-group"></a>Ta bort användare från en grupp

I följande exempel tas en användare bort från en grupp.

Exempel *function.jspå*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*Kör. CSX*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> `ClaimsPrincipal`Du måste ha konfigurerat autentiseringsinställningarna i Azure Functions för att få rätt bindning.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="add-user-to-a-group"></a>Lägg till användare i en grupp

I följande exempel läggs en användare till i en grupp.

Exempel *function.jspå*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "add"
  }];
};
```

### <a name="remove-user-from-a-group"></a>Ta bort användare från en grupp

I följande exempel tas en användare bort från en grupp.

Exempel *function.jspå*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

# <a name="python"></a>[Python](#tab/python)

### <a name="add-user-to-a-group"></a>Lägg till användare i en grupp

I följande exempel läggs en användare till i en grupp.

Exempel *function.jspå*

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_init.py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'add'
    }))
```

### <a name="remove-user-from-a-group"></a>Ta bort användare från en grupp

I följande exempel tas en användare bort från en grupp.

Exempel *function.jspå*

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_init.py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'remove'
    }))
```

# <a name="java"></a>[Java](#tab/java)

### <a name="add-user-to-a-group"></a>Lägg till användare i en grupp

I följande exempel läggs en användare till i en grupp.

```java
@FunctionName("addToGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction addToGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "add";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

### <a name="remove-user-from-a-group"></a>Ta bort användare från en grupp

I följande exempel tas en användare bort från en grupp.

```java
@FunctionName("removeFromGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction removeFromGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "remove";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

---

## <a name="configuration"></a>Konfiguration

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `SignalRConnectionInfo` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**bastyp**| saknas | Måste anges till `signalRConnectionInfo` .|
|**position**| saknas | Måste anges till `in` .|
|**Namn**| saknas | Variabel namn som används i funktions kod för objektet anslutnings information. |
|**hubName**|**HubName**| Värdet måste anges till namnet på den Signals Hub som anslutnings informationen genereras för.|
|**userId**|**UserId**| Valfritt: värdet på det användar-ID-anspråk som ska anges i åtkomst nyckelns token. |
|**connectionStringSetting**|**ConnectionStringSetting**| Namnet på den app-inställning som innehåller signal tjänstens anslutnings sträng (Standardvärdet är "AzureSignalRConnectionString") |

### <a name="signalr"></a>SignalR

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `SignalR` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**bastyp**| saknas | Måste anges till `signalR` .|
|**position**| saknas | Måste anges till `out` .|
|**Namn**| saknas | Variabel namn som används i funktions kod för objektet anslutnings information. |
|**hubName**|**HubName**| Värdet måste anges till namnet på den Signals Hub som anslutnings informationen genereras för.|
|**connectionStringSetting**|**ConnectionStringSetting**| Namnet på den app-inställning som innehåller signal tjänstens anslutnings sträng (Standardvärdet är "AzureSignalRConnectionString") |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Nästa steg

- [Hantera meddelanden från SignalR-tjänsten (Utlös bindning)](./functions-bindings-signalr-service-trigger.md)
- [Returnera URL: en för tjänstens slut punkt och åtkomsttoken (ingående bindning)](./functions-bindings-signalr-service-input.md)
