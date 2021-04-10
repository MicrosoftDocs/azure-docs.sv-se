---
title: Varaktiga entiteter – Azure Functions
description: Lär dig mer om de varaktiga enheterna och hur de används i Durable Functions-tillägget för Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: c898444659c2ce071163e9ab774a4534f8c51a9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632058"
---
# <a name="entity-functions"></a>Enhets funktioner

Entitets funktioner definierar åtgärder för att läsa och uppdatera små delar av tillstånd, så kallade *varaktiga entiteter*. Precis som med Orchestrator Functions är enhets funktionerna funktioner med en särskild utlösnings typ, *enhets utlösaren*. Till skillnad från Orchestrator-funktioner hanterar entiteter en entitets tillstånd explicit, i stället för implicit som representerar tillstånd via kontroll flödet.
Entiteter ger ett sätt att skala ut program genom att distribuera arbetet över flera entiteter, var och en med ett mycket stort tillstånd.

> [!NOTE]
> Enhets funktioner och relaterade funktioner är bara tillgängliga i [Durable Functions 2,0](durable-functions-versions.md#migrate-from-1x-to-2x) och senare. De stöds för närvarande i .NET, Java Script och python.

## <a name="general-concepts"></a>Allmänna begrepp

Entiteter fungerar på samma sätt som små tjänster som kommunicerar via meddelanden. Varje entitet har en unik identitet och ett internt tillstånd (om den finns). Precis som tjänster eller objekt utför entiteter åtgärder när de uppmanas att göra det. När en åtgärd körs kan den uppdatera entitetens interna status. Det kan också anropa externa tjänster och vänta på ett svar. Entiteter kommunicerar med andra entiteter, dirigeringar och klienter genom att använda meddelanden som är implicit skickade via pålitliga köer. 

För att förhindra konflikter garanterar vi att alla åtgärder på en enskild enhet körs seriellt, det vill säga en efter en annan.

> [!NOTE]
> När en entitet anropas bearbetar den sin nytto last och schemalägger sedan en ny körning så att den kan aktive ras när framtida indata kommer in. Därför kan din enhets körnings loggar Visa en extra körning efter varje enhets anrop. Detta är förväntat.

### <a name="entity-id"></a>Enhets-ID
Entiteter nås via en unik identifierare, *entitets-ID: t*. Ett entitets-ID är bara ett par med strängar som unikt identifierar en enhets instans. Det består av en:

* **Entitetsnamn**, som är ett namn som identifierar typen för entiteten. Ett exempel är "Counter". Namnet måste matcha namnet på den enhets funktion som implementerar entiteten. Det är inte känsligt för fall.
* **Enhets nyckel**, som är en sträng som unikt identifierar entiteten bland alla andra entiteter med samma namn. Ett exempel är ett GUID.

En `Counter` entitets funktion kan till exempel användas för att hålla poängen i ett online-spel. Varje instans av spelet har ett unikt entitets-ID, till exempel `@Counter@Game1` och `@Counter@Game2` . Alla åtgärder som är riktade till en viss entitet kräver att du anger ett entitets-ID som en parameter.

### <a name="entity-operations"></a>Entitetsåtgärder ###

Om du vill anropa en åtgärd på en entitet anger du:

* **Entitets-ID** för målentiteten.
* **Åtgärds namn**, som är en sträng som anger vilken åtgärd som ska utföras. `Counter`Entiteten kan till exempel stödja `add` -, `get` -eller- `reset` åtgärder.
* **Åtgärds information**, vilket är en valfri indataparameter för åtgärden. Till exempel kan åtgärden Lägg till ta ett heltal som inmatat värde.
* **Schemalagd tid**, vilket är en valfri parameter för att ange leverans tiden för åtgärden. En åtgärd kan till exempel vara en tillförlitlig schemaläggning att köra flera dagar i framtiden.

Åtgärder kan returnera ett resultat värde eller ett fel resultat, till exempel ett JavaScript-fel eller ett .NET-undantag. Det här resultatet eller felet kan observeras av dirigeringar som anropar åtgärden.

En entitets åtgärd kan också skapa, läsa, uppdatera och ta bort status för entiteten. Status för entiteten är alltid varaktigt beständig i lagrings utrymmet.

## <a name="define-entities"></a>Definiera entiteter

För närvarande är de två distinkta API: erna för att definiera entiteter:

**Function-baserad syntax** där entiteter representeras som funktioner och åtgärder uttryckligen skickas av programmet. Den här syntaxen fungerar bra för entiteter med enkel status, få åtgärder eller en dynamisk uppsättning åtgärder som i program ramverk. Den här syntaxen kan vara omständlig att underhålla eftersom den inte fångar in typ fel vid kompilering.

**Klass-baserad syntax (endast .net)**, där entiteter och åtgärder representeras av klasser och metoder. Den här syntaxen ger enklare läsbar kod och gör att åtgärder kan anropas på ett typ säkert sätt. Den klassbaserade syntaxen är ett tunt lager ovanpå den Function-baserade syntaxen, så att båda variantarna kan användas i samma program.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="example-function-based-syntax---c"></a>Exempel: Function-baserad syntax-C #

Följande kod är ett exempel på en enkel `Counter` entitet som implementeras som en varaktig funktion. Den här funktionen definierar tre åtgärder, `add` , `reset` , och `get` , som körs på ett heltals tillstånd.

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
            ctx.Return(ctx.GetState<int>());
            break;
    }
}
```

Mer information om den Function-baserade syntaxen och hur du använder den finns i [Function-based syntax](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="example-class-based-syntax---c"></a>Exempel: klass-baserad syntax-C #

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="example-javascript-entity"></a>Exempel: JavaScript-entitet

Varaktiga entiteter är tillgängliga i Java Script från och med version **1.3.0** av `durable-functions` NPM-paketet. Följande kod är `Counter` entiteten som implementeras som en varaktig funktion som skrivits i Java Script.

**Räknare/function.jspå**
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

**Räknare/index.js**
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
# <a name="python"></a>[Python](#tab/python)

### <a name="example-python-entity"></a>Exempel: python-entitet

Följande kod är `Counter` entiteten som implementeras som en varaktig funktion som skrivits i python.

**Räknare/function.jspå**
```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ]
}
```

**Counter/__init__. py**
```Python
import azure.functions as func
import azure.durable_functions as df


def entity_function(context: df.DurableEntityContext):
    current_value = context.get_state(lambda: 0)
    operation = context.operation_name
    if operation == "add":
        amount = context.get_input()
        current_value += amount
    elif operation == "reset":
        current_value = 0
    elif operation == "get":
        context.set_result(current_value)
    context.set_state(current_value)


main = df.Entity.create(entity_function)
```
---

## <a name="access-entities"></a>Åtkomst till entiteter

Entiteter kan nås med envägs-eller tvåvägs kommunikation. Följande terminologi särskiljer två former av kommunikation: 

* **Anrop** av en entitet använder tvåvägs kommunikation (tur och retur). Du skickar ett åtgärds meddelande till entiteten och väntar sedan på svars meddelandet innan du fortsätter. Svars meddelandet kan ge ett resultat värde eller ett fel resultat, till exempel ett JavaScript-fel eller ett .NET-undantag. Det här resultatet eller felet observeras sedan av anroparen.
* Att **signalera** en entitet använder enkelriktad (Fire och glömma) kommunikation. Du skickar ett åtgärds meddelande men väntar inte på svar. Även om meddelandet är garanterat levererat kan avsändaren inte känna till när och inte kan observera ett resultat värde eller fel.

Entiteter kan nås från klient funktioner, inifrån Orchestrator-funktioner eller inifrån enhets funktioner. Alla kontexter stöds inte för alla typer av kommunikation:

* Inifrån-klienter kan du signalera entiteter och du kan läsa enhets status.
* I Orchestration kan du signalera entiteter och du kan anropa entiteter.
* I entiteter kan du signalera entiteter.

Följande exempel illustrerar dessa olika sätt att komma åt entiteter.

### <a name="example-client-signals-an-entity"></a>Exempel: klienten signalerar en entitet

Om du vill komma åt entiteter från en vanlig Azure Function, som även kallas en klient funktion, använder du [enhets klient bindningen](durable-functions-bindings.md#entity-client). I följande exempel visas en köade funktion som signalerar en entitet som använder den här bindningen.

# <a name="c"></a>[C#](#tab/csharp)

> [!NOTE]
> För enkelhetens skull visar följande exempel den strikt skrivna syntaxen för åtkomst till entiteter. I allmänhet rekommenderar vi att du [kommer åt entiteter via gränssnitt](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) eftersom det ger mer typ kontroll.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await client.signalEntity(entityId, "add", 1);
};
```

# <a name="python"></a>[Python](#tab/python)

```Python
from azure.durable_functions import DurableOrchestrationClient
import azure.functions as func


async def main(req: func.HttpRequest, starter: str, message):
    client = DurableOrchestrationClient(starter)
    entityId = df.EntityId("Counter", "myCounter")
    await client.signal_entity(entityId, "add", 1)
```

---

Termen *signal* innebär att entitets-API-anropet är enkelriktat och asynkront. Det är inte möjligt att en klient funktion vet när entiteten har bearbetat åtgärden. Dessutom kan inte klient funktionen Observera resultat värden eller undantag. 

### <a name="example-client-reads-an-entity-state"></a>Exempel: klienten läser ett enhets tillstånd

Klient funktioner kan också fråga efter status för en entitet, som visas i följande exempel:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    EntityStateResponse<JObject> stateResponse = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, stateResponse.EntityState);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    const stateResponse = await client.readEntityState(entityId);
    return stateResponse.entityState;
};
```

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Python stöder för närvarande inte läsning av enhets tillstånd från en klient. Använd en Orchestrator `callEntity` i stället.

---

Frågor för enhets tillstånd skickas till lagrings platsen för beständig spårning och returnerar entitetens senast sparade tillstånd. Det här läget är alltid ett "dedikerat" tillstånd, det vill säga det är aldrig ett tillfälligt mellanliggande tillstånd som antas i mitten av körningen av en åtgärd. Det är dock möjligt att det här läget är inaktuellt jämfört med enhetens minnes tillstånd. Endast dirigering kan läsa en entitets minnes intern tillstånd enligt beskrivningen i följande avsnitt.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Exempel: Orchestration-signaler och anropa en entitet

Orchestrator-funktioner har åtkomst till entiteter med hjälp av API: er på [bindningen för Orchestration-utlösaren](durable-functions-bindings.md#orchestration-trigger) Följande exempel kod visar en Orchestrator-funktion som anropar och signalerar en `Counter` entitet.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
});
```

> [!NOTE]
> Java Script stöder för närvarande inte att signalera en entitet från en Orchestrator. Använd `callEntity` i stället.

# <a name="python"></a>[Python](#tab/python)

```Python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    entityId = df.EntityId("Counter", "myCounter")
    current_value = yield context.call_entity(entityId, "get")
    if current_value < 10:
        context.signal_entity(entityId, "add", 1)
    return state
```

---

Endast dirigering kan anropa entiteter och få svar, vilket kan vara antingen ett retur värde eller ett undantag. Klient funktioner som använder [klient bindningen](durable-functions-bindings.md#entity-client) kan bara signalerar entiteter.

> [!NOTE]
> Anrop av en entitet från en Orchestrator-funktion liknar att anropa en [aktivitets funktion](durable-functions-types-features-overview.md#activity-functions) från en Orchestrator-funktion. Den största skillnaden är att entitetens funktioner är beständiga objekt med en adress, vilket är entitets-ID: t. Enhets funktioner har stöd för att ange ett åtgärds namn. Aktivitets funktioner, å andra sidan, är tillstånds lösa och har inte begreppet drift.

### <a name="example-entity-signals-an-entity"></a>Exempel: entiteten signalerar en entitet

En entitets funktion kan skicka signaler till andra entiteter, eller till och med sig själv, medan en åtgärd körs.
Vi kan till exempel ändra föregående `Counter` entitet-exempel så att den skickar en "mil stolpe-nådd"-signal till en övervaknings enhet när räknaren når värdet 100.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
   case "add":
        var currentValue = ctx.GetState<int>();
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }

        ctx.SetState(currentValue + amount);
        break;
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
    case "add":
        const amount = context.df.getInput();
        if (currentValue < 100 && currentValue + amount >= 100) {
            const entityId = new df.EntityId("MonitorEntity", "");
            context.df.signalEntity(entityId, "milestone-reached", context.df.instanceId);
        }
        context.df.setState(currentValue + amount);
        break;
```

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Python stöder inte enhets-till-enhet-signaler än. Använd en Orchestrator för att signalera entiteter i stället.

---

## <a name="entity-coordination-currently-net-only"></a><a name="entity-coordination"></a>Organisations koordinering (endast för närvarande .NET)

Det kan finnas tillfällen när du behöver koordinera åtgärder över flera entiteter. I ett bank program kan du till exempel ha entiteter som representerar enskilda bank konton. När du överför betalningar från ett konto till ett annat, måste du se till att käll kontot har tillräckligt med penning belopp. Du måste också se till att uppdateringar av både käll-och mål kontona görs på ett transaktions konsekvent sätt.

### <a name="example-transfer-funds-c"></a>Exempel: Transfer Funds (C#)

I följande exempel kod överförs fonder mellan två konto enheter med hjälp av en Orchestrator-funktion. Koordinerande enhets uppdateringar kräver att du använder `LockAsync` metoden för att skapa ett _kritiskt avsnitt_ i dirigeringen.

> [!NOTE]
> För enkelhetens skull återanvänder det här exemplet den `Counter` entitet som definierats tidigare. I ett verkligt program är det bättre att definiera en mer detaljerad `BankAccount` entitet.

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

I .NET `LockAsync` returneras `IDisposable` , som avslutar det kritiska avsnittet när det tas bort. Det här `IDisposable` resultatet kan användas tillsammans med ett `using` block för att få en syntaktisk representation av det kritiska avsnittet.

I föregående exempel har en Orchestrator-funktion överfört fonder från en källentiteten till en målentitet. `LockAsync`Metoden låser både käll-och mål kontots entiteter. Den här låsningen säkerställer att ingen annan klient kan fråga eller ändra status för något av kontona tills Orchestration-logiken avbröt det kritiska avsnittet i slutet av `using` instruktionen. Det här beteendet förhindrar möjlighet att förhindra att käll kontot bearbetas.

> [!NOTE] 
> När en dirigering avslutas, antingen normalt eller med ett fel, avslutas alla kritiska delar som pågår implicit och alla Lås släpps.

### <a name="critical-section-behavior"></a>Beteende för kritiskt avsnitt

`LockAsync`Metoden skapar ett kritiskt avsnitt i en dirigering. Dessa kritiska avsnitt förhindrar andra dirigeringar från att göra överlappande ändringar till en angiven uppsättning entiteter. Internt `LockAsync` skickar API: et för att skicka "lås"-åtgärder till entiteterna och returnerar när de får ett meddelande om "Lås förvärvad" från var och en av dessa entiteter. Både lås och upplåsning är inbyggda åtgärder som stöds av alla entiteter.

Inga åtgärder från andra klienter tillåts för en entitet medan den är låst. Det här beteendet säkerställer att endast en Dirigerings instans kan låsa en entitet i taget. Om en anropare försöker anropa en åtgärd på en entitet när den har låsts av ett dirigering, placeras den åtgärden i en kö för väntande åtgärder. Väntande åtgärder bearbetas inte förrän innehavaren har låst det.

> [!NOTE] 
> Det här beteendet skiljer sig något från primitiva primitiver som används i de flesta programmeringsspråk, t `lock` . ex. instruktionen i C#. I C# `lock` måste exempelvis instruktionen användas av alla trådar för att säkerställa korrekt synkronisering över flera trådar. Entiteter kräver dock inte att alla anropare uttryckligen låser en entitet. Om någon anropare låser en entitet, blockeras alla andra åtgärder på den entiteten och placeras bakom det låset.

Lås på entiteter är varaktiga, så de kvarstår även om processen som körs återvinns. Lås är internt bestående som en del av en enhets varaktiga tillstånd.

Till skillnad från transaktioner återställer viktiga avsnitt inte automatiskt ändringar i händelse av fel. I stället måste en eventuell fel hantering, till exempel återställning eller omförsök, uttryckligen kodas, till exempel genom att fånga fel eller undantag. Det här design valet är avsiktligt. Det är svårt eller omöjligt att återställa alla effekterna av en dirigering automatiskt, eftersom dirigeringar kan köra aktiviteter och ringa till externa tjänster som inte kan återställas. Försök att återställa kan Miss lyckas och kräva ytterligare fel hantering.

### <a name="critical-section-rules"></a>Regler för kritiskt avsnitt

Till skillnad från primitiva primitiver på låg nivå i de flesta programmeringsspråk garanterar viktiga delar *inte död läge*. För att förhindra dödrar tvingar vi följande begränsningar: 

* Det går inte att kapsla kritiska avsnitt.
* Det går inte att skapa under dirigering med kritiska avsnitt.
* Kritiska avsnitt kan bara anropa entiteter som de har låst.
* Kritiska avsnitt kan inte anropa samma entitet med flera parallella anrop.
* Kritiska avsnitt kan bara signalera de entiteter de inte är låsta.

Överträdelser av dessa regler orsakar ett körnings fel, t. ex. `LockingRulesViolationException` i .net, som innehåller ett meddelande som förklarar vilken regel som har brutits.

## <a name="comparison-with-virtual-actors"></a>Jämförelse med virtuella aktörer

Många av de varaktiga entiteternas funktioner inspireras av [aktörs modellen](https://en.wikipedia.org/wiki/Actor_model). Om du redan är bekant med aktörer kan du känna igen många av de begrepp som beskrivs i den här artikeln. Varaktiga enheter är särskilt likartade för [virtuella aktörer](https://research.microsoft.com/projects/orleans/), eller kärnor, som är populärt av [Orleans-projektet](http://dotnet.github.io/orleans/). Exempel:

* Varaktiga entiteter kan adresseras via ett entitets-ID.
* Varaktiga enhets åtgärder körs seriellt, en i taget, för att förhindra tävlings förhållanden.
* Varaktiga entiteter skapas implicit när de anropas eller signaleras.
* När åtgärder inte körs inaktive ras varaktiga entiteter tyst från minnet.

Det finns några viktiga skillnader som är värda att notera:

* Varaktiga enheter prioriterar hållbarhet över svars tider och kan därför vara lämpligt för program med strikt latens krav.
* Varaktiga entiteter har inte inbyggda tids gränser för meddelanden. I Orleans nåddes alla meddelanden efter en konfigurerbar tid. Standardvärdet är 30 sekunder.
* Meddelanden som skickas mellan entiteter levereras på ett tillförlitligt sätt och i rätt ordning. I Orleans stöds tillförlitlig eller ordnad leverans för innehåll som skickas via strömmar, men är inte garanterat för alla meddelanden mellan olika kärnor.
* Mönster för begär ande svar i entiteter är begränsade till dirigering. I entiteter tillåts endast enkelriktade meddelanden (även kallat Signaling), som i den ursprungliga aktörs modellen och till skillnad från kärnor i Orleans. 
* Varaktiga entiteter är inte död läge. I Orleans kan död lägen ske och lösas inte förrän tids gränsen nåtts för meddelanden.
* Varaktiga entiteter kan användas tillsammans med varaktiga dirigeringar och stöd för distribuerade Lås metoder. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs Developer ' s Guide to varaktiga entiteter i .NET](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Lär dig mer om aktivitets nav](durable-functions-task-hubs.md)
