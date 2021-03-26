---
title: Bindningar för Durable Functions – Azure
description: Använda utlösare och bindningar för Durable Functions-tillägget för Azure Functions.
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 26c9da85b27ba46e9c48bdc7872a65d452a7acb1
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607105"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Bindningar för Durable Functions (Azure Functions)

[Durable Functions](durable-functions-overview.md) -tillägget introducerar två nya Utlös ande bindningar som styr körningen av Orchestrator-och aktivitets funktioner. Den introducerar också en utgående bindning som fungerar som en klient för Durable Functions Runtime.

## <a name="orchestration-trigger"></a>Orchestration-utlösare

Genom Orchestration-utlösaren kan du skapa [robusta Orchestrator-funktioner](durable-functions-types-features-overview.md#orchestrator-functions). Den här utlösaren stöder start av nya Orchestrator Function-instanser och återupptar befintliga Orchestrator-funktioner som väntar på en aktivitet.

När du använder Visual Studio-verktygen för Azure Functions konfigureras Orchestration-utlösaren med hjälp av attributet [OrchestrationTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.orchestrationtriggerattribute) .net.

När du skriver Orchestrator-funktioner i skript språk (till exempel java script eller C#-skript) definieras Orchestration-utlösaren av följande JSON-objekt i `bindings` matrisen för *function.jspå* filen:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` är namnet på den dirigering som klienter måste använda när de vill starta nya instanser av den här Orchestrator-funktionen. Den här egenskapen är valfri. Om inget anges används namnet på funktionen.

Internt denna Utlös ande bindning avsöker en serie med köer i standard lagrings kontot för Function-appen. Dessa köer är interna implementerings information om tillägget, vilket är anledningen till att de inte uttryckligen konfigureras i bindnings egenskaperna.

### <a name="trigger-behavior"></a>Beteende för utlösare

Här följer några anmärkningar om Dirigerings utlösaren:

* **Enkel tråds** -en enskild dispatcher-tråd används för alla Orchestrator-funktioner på en enda värd instans. Därför är det viktigt att se till att Orchestrator-funktions koden är effektiv och inte utför någon I/O. Det är också viktigt att se till att den här tråden inte gör något asynkront arbete, förutom vid väntan på Durable Functions-angivna uppgifts typer.
* **Gift-meddelande hantering** – det finns inget stöd för Poison-meddelanden i Orchestration-utlösare.
* **Meddelande synlighet** – meddelanden om Orchestration-utlösare är i kö och förblir osynliga under en konfigurerbar varaktighet. Synligheten för dessa meddelanden förnyas automatiskt så länge Function-appen körs och är felfri.
* **RETUR värden** -retur värden serialiseras till JSON och sparas i tabellen Orchestration-historik i Azure Table Storage. Dessa retur värden kan frågas av Dirigerings klientens bindning, som beskrivs senare.

> [!WARNING]
> Orchestrator-funktioner bör aldrig använda eventuella indata eller utgående bindningar än bindningen för Dirigerings utlösaren. Detta har möjlighet att orsaka problem med det varaktiga aktivitets tillägget eftersom dessa bindningar inte kan följa reglerna för enkel trådning och I/O. Om du vill använda andra bindningar lägger du till dem i en aktivitets funktion som anropas från din Orchestrator-funktion.

> [!WARNING]
> JavaScript Orchestrator-funktioner ska aldrig deklareras `async` .

### <a name="trigger-usage-net"></a>Utlösnings användning (.NET)

Bindningen för Dirigerings utlösare stöder både indata och utdata. Här är några saker du behöver veta om indata och utdata:

* **indata** – .net-Orchestration-funktioner stöder endast `DurableOrchestrationContext` som parameter typ. Deserialisering av indata direkt i funktions signaturen stöds inte. Koden måste använda `GetInput<T>` (.net) eller `getInput` (Java Script)-metoden för att hämta data från Orchestrator-funktionen. Dessa indata måste vara JSON-serialiserbara typer.
* **utdata** -Orchestration-utlösare stöder utdataparametrar samt indata. Returvärdet för funktionen används för att tilldela utdata-värdet och måste vara JSON-serialiserbar. Om en .NET-funktion returnerar `Task` eller `void` , `null` kommer ett värde att sparas som utdata.

### <a name="trigger-sample"></a>Utlös exempel

Följande exempel kod visar vad den enklaste "Hello World" Orchestrator-funktionen kan se ut så här:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```
> [!NOTE]
> Föregående kod är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext` . Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

#### <a name="javascript-functions-20-only"></a>Java Script (endast Functions 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> `context`Objektet i Java Script representerar inte DurableOrchestrationContext, men [funktionen context som helhet](../functions-reference-node.md#context-object). Du kan komma åt Orchestration-metoder via `context` objektets `df` egenskap.

> [!NOTE]
> JavaScript-dirigering bör använda `return` . `durable-functions`Biblioteket tar hand om att anropa `context.done` metoden.

De flesta funktioner för att anropa aktiviteter i Orchestrator, så här är ett "Hello World"-exempel som visar hur du anropar en aktivitets funktion:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> Föregående kod är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext` . Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

#### <a name="javascript-functions-20-only"></a>Java Script (endast Functions 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Aktivitets utlösare

Med aktivitets utlösaren kan du redigera funktioner som anropas av Orchestrator functions, kallas [aktivitets funktioner](durable-functions-types-features-overview.md#activity-functions).

Om du använder Visual Studio konfigureras aktivitets utlösaren med hjälp av `ActivityTriggerAttribute` .NET-attributet.

Om du använder VS Code eller Azure Portal för utveckling definieras aktivitets utlösaren av följande JSON-objekt i `bindings` matrisen med *function.jspå*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` är namnet på aktiviteten. Det här värdet är det namn som Orchestrator Functions använder för att anropa den här aktivitets funktionen. Den här egenskapen är valfri. Om inget anges används namnet på funktionen.

Internt denna Utlös ande bindning avsöker en kö i standard lagrings kontot för Function-appen. Den här kön är en intern implementerings information om tillägget, vilket är orsaken till att den inte uttryckligen konfigureras i bindnings egenskaperna.

### <a name="trigger-behavior"></a>Beteende för utlösare

Här följer några anmärkningar om aktivitets utlösaren:

* **Trådning** – till skillnad från Orchestration-utlösaren har aktivitets utlösare inga begränsningar kring trådning eller I/O. De kan behandlas som vanliga funktioner.
* **Gift-meddelande hantering** – det finns inget stöd för Poison-meddelanden i aktivitets utlösare.
* **Meddelande synlighet** – meddelanden om aktivitets utlösare tas bort från kön och förblir osynliga under en konfigurerbar varaktighet. Synligheten för dessa meddelanden förnyas automatiskt så länge Function-appen körs och är felfri.
* **RETUR värden** -retur värden serialiseras till JSON och sparas i tabellen Orchestration-historik i Azure Table Storage.

> [!WARNING]
> Lagrings Server delen för aktivitets funktioner är en implementerings detalj och användar koden ska inte samverka med dessa lagrings enheter direkt.

### <a name="trigger-usage-net"></a>Utlösnings användning (.NET)

Bindningen för aktivitets utlösare stöder både indata och utdata, precis som Orchestration-utlösaren. Här är några saker du behöver veta om indata och utdata:

* **indata** -.net-aktivitets funktioner används internt `DurableActivityContext` som parameter typ. Alternativt kan en aktivitets funktion deklareras med valfri parameter typ som är JSON-serialiserbar. När du använder `DurableActivityContext` kan du anropa `GetInput<T>` för att hämta och deserialisera aktivitetens funktions ingångar.
* **utdata** – aktivitets funktioner stöder utdataparametrar samt indata. Returvärdet för funktionen används för att tilldela utdata-värdet och måste vara JSON-serialiserbar. Om en .NET-funktion returnerar `Task` eller `void` , `null` kommer ett värde att sparas som utdata.
* **metadata** – .net-aktivitets funktioner kan bindas till en `string instanceId` parameter för att hämta instans-ID: t för den överordnade dirigeringen.

### <a name="trigger-sample"></a>Utlös exempel

Följande exempel kod visar hur en enkel "Hello World"-aktivitets funktion kan se ut så här:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

> [!NOTE]
> Föregående kod är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableActivityContext` i stället för `IDurableActivityContext` . Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

Standard parameter typen för .NET- `ActivityTriggerAttribute` bindningen är `IDurableActivityContext` . .NET-aktivitets utlösare stöder dock också bindning direkt till JSON-serializeable typer (inklusive primitiva typer), så att samma funktion kan för enklas på följande sätt:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>Java Script (endast Functions 2,0)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

JavaScript-bindningar kan också skickas som ytterligare parametrar, så att samma funktion kan för enklas på följande sätt:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>Använda indata och utgående bindningar

Du kan använda vanliga indata och utgående bindningar utöver bindningen för aktivitets utlösare. Du kan till exempel ta indata till din aktivitets bindning och skicka ett meddelande till en EventHub med hjälp av EventHub-utdata-bindningen:

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>Orchestration-klient

Genom att dirigera klient bindningen kan du skriva funktioner som interagerar med Orchestrator functions. Dessa funktioner kallas ibland [klient funktioner](durable-functions-types-features-overview.md#client-functions). Du kan till exempel agera på Orchestration-instanser på följande sätt:

* Starta dem.
* Fråga efter status.
* Avsluta dem.
* Skicka händelser till dem när de är igång.
* Rensa instans historik.

Om du använder Visual Studio kan du binda till Orchestration-klienten genom att använda `OrchestrationClientAttribute` .NET-attributet för Durable Functions 1,0. Från och med Durable Functions 2,0 kan du binda till Orchestration-klienten med hjälp av .net- `DurableClientAttribute` attributet.

Om du använder skript språk (till exempel *. CSX* -eller *. js* -filer) för utveckling, definieras Orchestration-utlösaren av följande JSON-objekt i `bindings` matrisen med *function.jspå*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` – Används i scenarier där flera Functions-appar delar samma lagrings konto men måste isoleras från varandra. Om inget värde anges används standardvärdet från `host.json` . Värdet måste matcha det värde som används av målets Orchestrator-funktioner.
* `connectionName` – Namnet på en app-inställning som innehåller en anslutnings sträng för lagrings kontot. Det lagrings konto som representeras av den här anslutnings strängen måste vara samma som används av mål-Orchestrator-funktionerna. Om inget värde anges används standard anslutnings strängen för lagrings kontot för Function-appen.

> [!NOTE]
> I de flesta fall rekommenderar vi att du utelämnar dessa egenskaper och använder standard beteendet.

### <a name="client-usage"></a>Klient användning

I .NET Functions binder du normalt till `IDurableOrchestrationClient` , vilket ger dig fullständig åtkomst till alla Dirigerings klient-API: er som stöds av Durable functions. I äldre Durable Functions 2. x-versioner binder du i stället till `DurableOrchestrationClient` klassen. I Java Script visas samma API: er av det objekt som returneras från `getClient` . API: er för klient objekt är:

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

.NET-funktioner kan också bindas till `IAsyncCollector<T>` där `T` är `StartOrchestrationArgs` eller `JObject` .

Mer information om dessa åtgärder finns i API- `IDurableOrchestrationClient` dokumentationen.

### <a name="client-sample-visual-studio-development"></a>Klient exempel (Visual Studio-utveckling)

Här är ett exempel på en Queue-utlöst funktion som startar en "HelloWorld"-dirigering.

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Föregående C#-kod är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `OrchestrationClient` attribut i stället för `DurableClient` attributet och du måste använda `DurableOrchestrationClient` parameter typen i stället för `IDurableOrchestrationClient` . Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

### <a name="client-sample-not-visual-studio"></a>Klient exempel (inte Visual Studio)

Om du inte använder Visual Studio för utveckling kan du skapa följande *function.jsi* filen. Det här exemplet visar hur du konfigurerar en köade funktion som använder den varaktiga dirigeringen av klient bindning:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

> [!NOTE]
> Föregående JSON är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `orchestrationClient` i stället för `durableClient` som utlösnings typ. Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

Nedan visas språkspecifika exempel som startar nya Orchestrator Function-instanser.

#### <a name="c-script-sample"></a>C#-skript exempel

Följande exempel visar hur du använder den varaktiga Dirigerings klient bindningen för att starta en ny funktions instans från en kö – utlöst C#-funktion:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Föregående kod är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationClient` parameter typen i stället för `IDurableOrchestrationClient` . Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

#### <a name="javascript-sample"></a>JavaScript-exempel

Följande exempel visar hur du använder den varaktiga Dirigerings klient bindningen för att starta en ny funktions instans från en JavaScript-funktion:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Du hittar mer information om start instanser i [instans hantering](durable-functions-instance-management.md).

## <a name="entity-trigger"></a>Enhets utlösare

Med enhets utlösare kan du redigera [entitets funktioner](durable-functions-entities.md). Den här utlösaren stöder bearbetning av händelser för en angiven enhets instans.

När du använder Visual Studio-verktygen för Azure Functions konfigureras enhets utlösaren med hjälp av `EntityTriggerAttribute` .NET-attributet.

> [!NOTE]
> Enhets utlösare är tillgängliga från och med Durable Functions 2. x.

Internt denna Utlös ande bindning avsöker en serie med köer i standard lagrings kontot för Function-appen. Dessa köer är interna implementerings information om tillägget, vilket är anledningen till att de inte uttryckligen konfigureras i bindnings egenskaperna.

### <a name="trigger-behavior"></a>Beteende för utlösare

Här följer några anmärkningar om enhets utlösaren:

* **Enkel trådad**: en enskild dispatcher-tråd används för att bearbeta åtgärder för en viss entitet. Om flera meddelanden skickas till en enda entitet samtidigt bearbetas åtgärderna en i taget.
* **Gift-meddelande hantering** – det finns inget stöd för Poison-meddelanden i enhets utlösare.
* **Meddelande synlighet** – meddelanden om enhets utlösare är i kö och förblir osynliga under en konfigurerbar varaktighet. Synligheten för dessa meddelanden förnyas automatiskt så länge Function-appen körs och är felfri.
* **RETUR värden** -enhets funktioner stöder inte retur värden. Det finns vissa API: er som kan användas för att spara tillstånd eller skicka tillbaka värden till dirigeringar.

Alla tillstånds ändringar som görs i en entitet under körningen sparas automatiskt när körningen har slutförts.

### <a name="trigger-usage-net"></a>Utlösnings användning (.NET)

Varje entitets funktion har parameter typen `IDurableEntityContext` , som har följande medlemmar:

* **EntityName**: namnet på entiteten som körs för tillfället.
* **EntityKey**: nyckeln för entiteten som körs för tillfället.
* **EntityId**: ID för entiteten som körs för tillfället.
* **OperationName**: namnet på den aktuella åtgärden.
* **HasState**: huruvida entiteten finns, det har en viss status. 
* **GetState \<TState> ()**: hämtar entitetens aktuella status. Om den inte redan finns skapas och initieras den till `default<TState>` . `TState`Parametern måste vara en primitiv eller JSON-serializeable typ. 
* **GetState \<TState> (initfunction)**: hämtar entitetens aktuella status. Om den inte redan finns skapas den genom att anropa den angivna `initfunction` parametern. `TState`Parametern måste vara en primitiv eller JSON-serializeable typ. 
* **SetState (arg)**: skapar eller uppdaterar status för entiteten. `arg`Parametern måste vara ett JSON-serializeable-objekt eller en primitiv.
* **DeleteState ()**: tar bort status för entiteten. 
* **GetInput \<TInput> ()**: hämtar InInformationen för den aktuella åtgärden. `TInput`Typ parametern måste vara en primitiv eller JSON-serializeable typ.
* **Return (arg)**: returnerar ett värde till den dirigering som anropade åtgärden. `arg`Parametern måste vara ett primitivt eller JSON-serializeable-objekt.
* **SignalEntity (EntityId, scheduledTimeUtc, åtgärd, Indatatyp)**: skickar ett envägs meddelande till en entitet. `operation`Parametern måste vara en sträng som inte är null, det valfria `scheduledTimeUtc` måste vara en UTC-datum/tid för att åtgärden ska kunna anropas, och `input` parametern måste vara ett primitivt eller JSON-serializeable-objekt.
* **CreateNewOrchestration (orchestratorFunctionName, Indatatyp)**: startar ett nytt dirigering. `input`Parametern måste vara ett primitivt eller JSON-serializeable-objekt.

`IDurableEntityContext`Objektet som skickas till entitet-funktionen kan nås med hjälp av `Entity.Current` egenskapen async-Local. Den här metoden är praktisk när du använder den klassbaserade programmerings modellen.

### <a name="trigger-sample-c-function-based-syntax"></a>Utlösnings exempel (C# Function-baserad syntax)

Följande kod är ett exempel på en enkel *Counter* -entitet som implementeras som en varaktig funktion. Den här funktionen definierar tre åtgärder, `add` , `reset` och `get` , som körs på ett heltals tillstånd.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

Mer information om den Function-baserade syntaxen och hur du använder den finns i [Function-based syntax](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="trigger-sample-c-class-based-syntax"></a>Utlösnings exempel (C#-klass-baserad syntax)

Följande exempel är en motsvarande implementering av `Counter` entiteten med hjälp av klasser och metoder.

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

Status för den här entiteten är ett objekt av typen `Counter` , som innehåller ett fält som lagrar räknarens aktuella värde. För att spara objektet i lagret serialiseras det och deserialiseras av [JSON.net](https://www.newtonsoft.com/json) -biblioteket. 

Mer information om den klassbaserade syntaxen och hur du använder den finns i [definiera enhets klasser](durable-functions-dotnet-entities.md#defining-entity-classes).

> [!NOTE]
> Funktionens start punkt metod med `[FunctionName]` attributet *måste* deklareras `static` när du använder entitets klasser. Metoder som inte är statiska kan leda till att flera objekt initieras och potentiellt andra odefinierade beteenden.

Enhets klasser har särskilda metoder för att interagera med bindningar och .NET-beroende inmatning. Mer information finns i [entitets konstruktion](durable-functions-dotnet-entities.md#entity-construction).

### <a name="trigger-sample-javascript"></a>Utlösnings exempel (Java Script)

Följande kod är ett exempel på en enkel *Counter* -entitet som implementeras som en varaktig funktion som skrivits i Java Script. Den här funktionen definierar tre åtgärder, `add` , `reset` och `get` , som körs på ett heltals tillstånd.

**function.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

> [!NOTE]
> Varaktiga entiteter är tillgängliga i Java Script från och med version **1.3.0** av `durable-functions` NPM-paketet.

## <a name="entity-client"></a>Enhets klient

Med enhets klientens bindning kan du utlösa [enhets funktioner](#entity-trigger)asynkront. Dessa funktioner kallas ibland [klient funktioner](durable-functions-types-features-overview.md#client-functions).

Om du använder Visual Studio kan du binda till enhets klienten med hjälp av .net- `DurableClientAttribute` attributet.

> [!NOTE]
> `[DurableClientAttribute]`Kan också användas för att binda till Orchestration- [klienten](#orchestration-client).

Om du använder skript språk (till exempel *. CSX* -eller *. js* -filer) för utveckling, definieras enhets utlösaren av följande JSON-objekt i `bindings` matrisen med *function.jspå*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub` – Används i scenarier där flera Functions-appar delar samma lagrings konto men måste isoleras från varandra. Om inget värde anges används standardvärdet från `host.json` . Värdet måste matcha det värde som används av entitetens mål.
* `connectionName` – Namnet på en app-inställning som innehåller en anslutnings sträng för lagrings kontot. Det lagrings konto som representeras av den här anslutnings strängen måste vara samma som används av entiteten för målentiteten. Om inget värde anges används standard anslutnings strängen för lagrings kontot för Function-appen.

> [!NOTE]
> I de flesta fall rekommenderar vi att du utelämnar de valfria egenskaperna och använder standard beteendet.

### <a name="entity-client-usage"></a>Användning av enhets klient

I .NET-funktioner binder du normalt till `IDurableEntityClient` , vilket ger dig fullständig åtkomst till alla klient-API: er som stöds av varaktiga enheter. Du kan också binda till `IDurableOrchestrationClient` gränssnittet, som ger åtkomst till klient-API: er för både entiteter och dirigeringar. API: er för klient objekt är:

* **ReadEntityStateAsync \<T>**: läser status för en entitet. Det returnerar ett svar som anger om målentiteten finns, och i så fall, vad dess tillstånd är.
* **SignalEntityAsync**: skickar ett envägs meddelande till en entitet och väntar på att det ska placeras i kö.
* **ListEntitiesAsync**: frågar efter status för flera entiteter. Entiteter kan efter frågas efter *namn* och *senaste åtgärds tid*.

Du behöver inte skapa målentiteten innan du skickar en signal – enhets statusen kan skapas från den enhets funktion som hanterar signalen.

> [!NOTE]
> Det är viktigt att förstå att "signaler" som skickas från klienten bara placeras i kö för att bearbetas asynkront vid ett senare tillfälle. I synnerhet `SignalEntityAsync` returneras vanligt vis innan entiteten även startar åtgärden och det går inte att komma tillbaka till returvärdet eller följa undantag. Om det krävs starkare garantier (t. ex. för arbets flöden) bör *Orchestrator-funktioner* användas, vilket kan vänta på att entitets åtgärder ska slutföras och kan bearbeta retur värden och följa undantag.

### <a name="example-client-signals-entity-directly---c"></a>Exempel: klient Signals-entiteten direkt – C #

Här är ett exempel på en kö – utlöst funktion som anropar en "Counter"-entitet.

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

### <a name="example-client-signals-entity-via-interface---c"></a>Exempel: klient Signals-entitet via gränssnitt-C #

Om möjligt rekommenderar vi [att du använder entiteter via gränssnitt](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) eftersom det ger mer typ kontroll. Anta till exempel att `Counter` entiteten ovan implementerade ett `ICounter` gränssnitt, som definieras enligt följande:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    Task<int> Get();
}

public class Counter : ICounter
{
    // ...
}
```

Klient koden kan sedan användas `SignalEntityAsync<ICounter>` för att generera en typ säker proxy:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

`proxy`Parametern är en dynamiskt genererad instans av `ICounter` , som internt översätter anropet till `Add` till motsvarande (inte avskrivit) anrop till `SignalEntityAsync` .

> [!NOTE]
> `SignalEntityAsync`API: erna utgör enkelriktade åtgärder. Om ett enhets gränssnitt returnerar `Task<T>` , är värdet för `T` parametern alltid null eller `default` .

I synnerhet är det inte meningsfullt att signalera `Get` åtgärden, eftersom inget värde returneras. Klienter kan i stället använda antingen `ReadStateAsync` för att få åtkomst till räknarens tillstånd direkt, eller så kan starta en Orchestrator-funktion som anropar `Get` åtgärden.

### <a name="example-client-signals-entity---javascript"></a>Exempel: klient signaler entitet-Java Script

Här är ett exempel på en Queue-utlöst funktion som signalerar en "Counter"-entitet i Java Script.

**function.json**
```json
{
    "bindings": [
      {
        "name": "input",
        "type": "queueTrigger",
        "queueName": "durable-entity-trigger",
        "direction": "in",
      },
      {
        "name": "starter",
        "type": "durableClient",
        "direction": "in"
      }
    ],
    "disabled": false
  }
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await client.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> Varaktiga entiteter är tillgängliga i Java Script från och med version **1.3.0** av `durable-functions` NPM-paketet.

<a name="host-json"></a>
## <a name="hostjson-settings"></a>host.jspå Inställningar

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Inbyggd HTTP API-referens för instans hantering](durable-functions-http-api.md)
