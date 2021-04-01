---
title: Guide för utvecklare till varaktiga entiteter i .NET-Azure Functions
description: Så här arbetar du med varaktiga entiteter i .NET med Durable Functions-tillägget för Azure Functions.
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 88d2a23104b67dae8fd480406eb9171e9f3d5652
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92740013"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>Guide för utvecklare till varaktiga entiteter i .NET

I den här artikeln beskrivs de tillgängliga gränssnitten för att utveckla beständiga entiteter med .NET i detalj, inklusive exempel och allmänna råd. 

Enhets funktionerna ger programutvecklare utan server ett bekvämt sätt att organisera program tillstånd som en samling detaljerade entiteter. Mer information om underliggande koncept finns i artikeln [beständiga entiteter: begrepp](durable-functions-entities.md) .

Vi erbjuder för närvarande två API: er för att definiera entiteter:

- Den **klassbaserade syntaxen** representerar entiteter och åtgärder som klasser och metoder. Den här syntaxen ger enkel läsbar kod och gör att åtgärder kan anropas på ett typ kontrollerat sätt via gränssnitt. 

- Den **Function-baserade syntaxen** är ett gränssnitt på lägre nivå som representerar entiteter som funktioner. Den ger exakt kontroll över hur enhets åtgärderna skickas och hur enhetens tillstånd hanteras.  

Den här artikeln fokuserar främst på den klassbaserade syntaxen, eftersom vi förväntar dig att den passar bättre för de flesta program. Den [Function-baserade syntaxen](#function-based-syntax) kan dock vara lämplig för program som vill definiera eller hantera egna abstraktioner för enhets tillstånd och åtgärder. Det kan också vara lämpligt för att implementera bibliotek som kräver allmänhet för närvarande inte stöds av den klassbaserade syntaxen. 

> [!NOTE]
> Den klassbaserade syntaxen är bara ett lager ovanpå den Function-baserade syntaxen så att båda variantarna kan användas i samma program. 
 
## <a name="defining-entity-classes"></a>Definiera enhets klasser

Följande exempel är en implementering av en `Counter` entitet som lagrar ett enda värde av typen heltal, och erbjuder fyra åtgärder `Add` , `Reset` , `Get` och `Delete` .

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int Value { get; set; }

    public void Add(int amount) 
    {
        this.Value += amount;
    }

    public Task Reset() 
    {
        this.Value = 0;
        return Task.CompletedTask;
    }

    public Task<int> Get() 
    {
        return Task.FromResult(this.Value);
    }

    public void Delete() 
    {
        Entity.Current.DeleteState();
    }

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

`Run`Funktionen innehåller den standard som krävs för att använda den klassbaserade syntaxen. Det måste vara en *statisk* Azure-funktion. Den körs en gång för varje åtgärds meddelande som bearbetas av entiteten. När `DispatchAsync<T>` anropas och entiteten inte redan finns i minnet, konstrueras ett objekt av typen `T` och fyller i fälten från den senast beständiga JSON som finns i lagrings utrymmet (om det finns några). Sedan anropas metoden med det matchande namnet.

> [!NOTE]
> Statusen för en klassbaserade entitet **skapas implicit** innan enheten bearbetar en åtgärd och kan tas **bort explicit** i en åtgärd genom att anropa `Entity.Current.DeleteState()` .

### <a name="class-requirements"></a>Klass krav
 
Enhets klasser är POCOs (vanliga gamla CLR-objekt) som inte kräver några särskilda superklasser, gränssnitt eller attribut. Ändå

- Klassen måste vara constructible (se [entitetens konstruktion](#entity-construction)).
- Klassen måste vara JSON-serialiserbar (se [enhets serialisering](#entity-serialization)).

Dessutom måste alla metoder som är avsedda att anropas som en åtgärd uppfylla ytterligare krav:

- En åtgärd måste ha högst ett argument och får inte ha några Överlagrings-eller generiska typ argument.
- En åtgärd som är avsedd att anropas från ett dirigering med ett gränssnitt måste returnera `Task` eller `Task<T>` .
- Argument och retur värden måste vara serialiserbara värden eller objekt.

### <a name="what-can-operations-do"></a>Vad kan åtgärder göra?

Alla entitets åtgärder kan läsa och uppdatera enhetens tillstånd, och ändringar i statusen sparas automatiskt till lagringen. Dessutom kan åtgärder utföra externa I/O eller andra beräkningar inom de allmänna gränser som är gemensamma för alla Azure Functions.

Åtgärder har också till gång till funktioner som tillhandahålls av `Entity.Current` kontexten:

* `EntityName`: namnet på entiteten som körs för tillfället.
* `EntityKey`: nyckeln för entiteten som körs för tillfället.
* `EntityId`: ID: t för entiteten som körs (innehåller namn och nyckel).
* `SignalEntity`: skickar ett envägs meddelande till en entitet.
* `CreateNewOrchestration`: startar ett nytt dirigering.
* `DeleteState`: tar bort den här entitetens tillstånd.

Vi kan till exempel ändra entiteten Counter så att den startar en dirigering när räknaren når 100 och skickar enhets-ID: t som ett indataargument:

```csharp
    public void Add(int amount) 
    {
        if (this.Value < 100 && this.Value + amount >= 100)
        {
            Entity.Current.StartNewOrchestration("MilestoneReached", Entity.Current.EntityId);
        }
        this.Value += amount;      
    }
```

## <a name="accessing-entities-directly"></a>Åtkomst till entiteter direkt

Klassbaserade entiteter kan nås direkt, med hjälp av explicita sträng namn för entiteten och dess åtgärder. Vi tillhandahåller några exempel nedan. en djupare förklaring av underliggande koncept (till exempel signaler eller anrop) finns i diskussionen i [Access-entiteter](durable-functions-entities.md#access-entities). 

> [!NOTE]
> Om möjligt rekommenderar vi [att du använder entiteter via gränssnitt](#accessing-entities-through-interfaces), eftersom det ger mer typ kontroll.

### <a name="example-client-signals-entity"></a>Exempel: klient Signals-entitet

Följande Azure http-funktion implementerar en BORTTAGNINGs åtgärd med hjälp av REST-konventioner. Den skickar en Delete-signal till räknar enheten vars nyckel skickas i URL-sökvägen.

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync(entityId, "Delete");    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

### <a name="example-client-reads-entity-state"></a>Exempel: klienten läser enhets tillstånd

Följande Azure http-funktion implementerar en GET-åtgärd med hjälp av REST-konventioner. Den läser det aktuella läget för den räknar entitet vars nyckel skickas i URL-sökvägen.

```csharp
[FunctionName("GetCounter")]
public static async Task<HttpResponseMessage> GetCounter(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    var state = await client.ReadEntityStateAsync<Counter>(entityId); 
    return req.CreateResponse(state);
}
```

> [!NOTE]
> Objektet som returnerades av `ReadEntityStateAsync` är bara en lokal kopia, det vill säga en ögonblicks bild av enhets statusen från en tidigare tidpunkt. I synnerhet kan det vara inaktuellt, och om du ändrar det här objektet påverkas inte den faktiska entiteten. 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>Exempel: Orchestration First Signals och anropar sedan entiteten

Följande dirigering signalerar en Counter-entitet för att öka den och anropar sedan samma entitet för att läsa det senaste värdet.

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");

    // One-way signal to the entity - does not await a response
    context.SignalEntity(entityId, "Add", 1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");

    return currentValue;
}
```

## <a name="accessing-entities-through-interfaces"></a>Komma åt entiteter via gränssnitt

Gränssnitt kan användas för att komma åt entiteter via genererade proxyobjekt. Den här metoden säkerställer att namnet och argument typen för en åtgärd matchar vad som implementeras. Vi rekommenderar att du använder gränssnitt för att komma åt entiteter när det är möjligt.

Vi kan till exempel ändra Counter-exemplet enligt följande:

```csharp
public interface ICounter
{
    void Add(int amount);
    Task Reset();
    Task<int> Get();
    void Delete();
}

public class Counter : ICounter
{
    ...
}
```

Enhets klasser och enhets gränssnitt liknar kärnor och kornig het som är populära av [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). Mer information om likheter och skillnader mellan varaktiga entiteter och Orleans finns i [jämförelse med virtuella aktörer](durable-functions-entities.md#comparison-with-virtual-actors).

Förutom att tillhandahålla typ kontroll är gränssnitten användbara för en bättre uppdelning av problem i programmet. Till exempel, eftersom en entitet kan implementera flera gränssnitt, kan en enskild entitet betjäna flera roller. Eftersom ett gränssnitt kan implementeras av flera entiteter kan dessutom allmänna kommunikations mönster implementeras som återanvändbara bibliotek.

### <a name="example-client-signals-entity-through-interface"></a>Exempel: klient Signals-entitet via gränssnitt

Klient koden kan använda `SignalEntityAsync<TEntityInterface>` för att skicka signaler till entiteter som implementerar `TEntityInterface` . Exempel:

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync<ICounter>(entityId, proxy => proxy.Delete());    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

I det här exemplet `proxy` är parametern en dynamiskt genererad instans av `ICounter` , som internt översätter anropet till `Delete` en signal.

> [!NOTE]
> `SignalEntityAsync`API: erna kan endast användas för enkelriktade åtgärder. Även om en åtgärd returnerar `Task<T>` , kommer värdet för `T` parametern alltid vara null eller `default` , inte det faktiska resultatet.
Det är till exempel inte meningsfullt att signalera `Get` åtgärden, eftersom inget värde returneras. Klienter kan i stället använda antingen `ReadStateAsync` för att få åtkomst till räknarens tillstånd direkt, eller så kan starta en Orchestrator-funktion som anropar `Get` åtgärden. 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>Exempel: dirigerar första signaler och anropar sedan entitet via proxy

Om du vill anropa eller signalera en entitet från ett dirigering `CreateEntityProxy` kan du använda, tillsammans med gränssnitts typen, för att generera en proxy för entiteten. Den här proxyn kan sedan användas för att anropa eller signalera åtgärder:

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");
    var proxy = context.CreateEntityProxy<ICounter>(entityId);

    // One-way signal to the entity - does not await a response
    proxy.Add(1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await proxy.Get();

    return currentValue;
}
```

Implicit, alla åtgärder som returnerar `void` signaleras och alla åtgärder som returnerar `Task` eller `Task<T>` anropas. En kan ändra det här standard beteendet, och signal åtgärder även om de returnerar uppgiften, genom att använda `SignalEntity<IInterfaceType>` metoden explicit.

### <a name="shorter-option-for-specifying-the-target"></a>Kortare alternativ för att ange målet

När du anropar eller signalerar en entitet med ett gränssnitt måste det första argumentet ange målentiteten. Målet kan anges antingen genom att ange entitets-ID eller, i de fall där det bara finns en klass som implementerar entiteten, bara enhets nyckeln:

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

Om endast enhets nyckeln har angetts och det inte går att hitta en unik implementering vid körning, `InvalidOperationException` utlöses. 

### <a name="restrictions-on-entity-interfaces"></a>Begränsningar för enhets gränssnitt

Som vanligt måste alla parametrar och retur typer vara JSON-serialiserbar. Annars utlöses serialiserade undantag vid körning.

Vi tillämpar också vissa ytterligare regler:
* Enhets gränssnitt får bara definiera metoder.
* Enhets gränssnitt får inte innehålla generiska parametrar.
* Enhets gränssnitts metoder får inte ha mer än en parameter.
* Entitets gränssnitts metoder måste returnera `void` , `Task` eller `Task<T>` 

Om någon av dessa regler överträds `InvalidOperationException` utlöses en vid körning när gränssnittet används som ett typ argument till `SignalEntity` eller `CreateProxy` . I undantags meddelandet förklaras vilken regel som har brutits.

> [!NOTE]
> Gränssnitts metoder som returneras `void` kan bara signaleras (envägs), inte anropas (dubbelriktat). Gränssnitts metoder returnerar `Task` eller `Task<T>` kan antingen anropas eller signaleras. Om den anropas, returnerar de resultatet av åtgärden eller återskapar undantag som har utlösts av åtgärden. Men när en signal skickas returnerar de inte det faktiska resultatet eller undantaget från åtgärden, utan bara standardvärdet.

## <a name="entity-serialization"></a>Enhets serialisering

Eftersom status för en entitet är varaktigt, måste entitets klassen serialiseras. Durable Functions runtime använder [JSON.net](https://www.newtonsoft.com/json) -biblioteket för det här syftet, som har stöd för ett antal principer och attribut för att kontrol lera serialiseringen och avserialiserings processen. De vanligaste C#-data typerna (inklusive matriser och samlings typer) kan redan serialiseras och kan enkelt användas för att definiera statusen för varaktiga entiteter.

Json.NET kan till exempel enkelt serialisera och deserialisera följande klass:

```csharp
[JsonObject(MemberSerialization = MemberSerialization.OptIn)]
public class User
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("yearOfBirth")]
    public int YearOfBirth { get; set; }

    [JsonProperty("timestamp")]
    public DateTime Timestamp { get; set; }

    [JsonProperty("contacts")]
    public Dictionary<Guid, Contact> Contacts { get; set; } = new Dictionary<Guid, Contact>();

    [JsonObject(MemberSerialization = MemberSerialization.OptOut)]
    public struct Contact
    {
        public string Name;
        public string Number;
    }

    ...
}
```

### <a name="serialization-attributes"></a>Attribut för serialisering

I exemplet ovan valde vi att inkludera flera attribut för att göra den underliggande serialiseringen tydligare:
- Vi kommenterar klassen med `[JsonObject(MemberSerialization.OptIn)]` för att påminna oss om att klassen måste vara serialiserbar och att endast spara medlemmar som uttryckligen marker ATS som JSON-egenskaper.
-  Vi kommenterar fälten som ska behållas `[JsonProperty("name")]` för att påminna oss om att ett fält är en del av det beständiga enhets läget och att ange det egenskaps namn som ska användas i JSON-representationen.

Dessa attribut är dock inte obligatoriska. andra konventioner eller attribut är tillåtna så länge de fungerar med Json.NET. Till exempel kan en använda `[DataContract]` attribut eller inga attribut alls:

```csharp
[DataContract]
public class Counter
{
    [DataMember]
    public int Value { get; set; }
    ...
}

public class Counter
{
    public int Value;
    ...
}
```

Som standard lagras *inte* namnet på klassen som en del av JSON-representationen: det vill säga att vi använder `TypeNameHandling.None` standardinställningen. Det här standard beteendet kan åsidosättas med `JsonObject` eller- `JsonProperty` attribut.

### <a name="making-changes-to-class-definitions"></a>Göra ändringar i klass definitioner

En del Försiktighet krävs när du gör ändringar i en klass definition efter att ett program har körts, eftersom det inte längre är säkert att det lagrade JSON-objektet matchar den nya klass definitionen. Det är ofta möjligt att hantera korrekt ändring av data format så länge en som är en förståelse för deserialiseringen som används av `JsonConvert.PopulateObject` .

Här följer några exempel på ändringar och deras inverkan:

1. Om en ny egenskap läggs till, som inte finns i den lagrade JSON-filen, förutsätts dess standardvärde.
1. Om en egenskap tas bort, som finns i den lagrade JSON-filen, går det tidigare innehållet förlorat.
1. Om du byter namn på en egenskap är resultatet som om du tar bort den gamla och lägger till en ny.
1. Om typen för en egenskap ändras så att den inte längre kan avserialiseras från den lagrade JSON-filen genereras ett undantag.
1. Om typen för en egenskap ändras, men den fortfarande kan avserialiseras från den lagrade JSON-filen, kommer den att göra det.

Det finns många tillgängliga alternativ för att anpassa beteendet för Json.NET. Om du till exempel vill framtvinga ett undantag om den lagrade JSON-filen innehåller ett fält som inte finns i klassen, anger du attributet `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)` . Det är också möjligt att skriva anpassad kod för deserialisering som kan läsa JSON som lagras i godtyckligt format.

## <a name="entity-construction"></a>Enhets konstruktion

Ibland vill vi utöva mer kontroll över hur enhets objekt konstrueras. Nu beskriver vi flera alternativ för att ändra standard beteendet när du konstruerar objekt i entiteten. 

### <a name="custom-initialization-on-first-access"></a>Anpassad initiering vid första åtkomst

Ibland behöver vi utföra vissa särskilda initieringar innan de skickar en åtgärd till en entitet som aldrig har använts, eller som har tagits bort. Om du vill ange det här beteendet kan du lägga till ett villkor före `DispatchAsync` :

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    return ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>Bindningar i entitets klasser

Till skillnad från vanliga funktioner har klass metoder för entiteter inte direkt åtkomst till indata-och utgående bindningar. I stället måste bindnings data samlas in i deklarationen för ingångs punkt funktionen och sedan skickas till- `DispatchAsync<T>` metoden. Alla objekt som skickas till `DispatchAsync<T>` skickas automatiskt till konstruktorn för entity-klassen som ett argument.

I följande exempel visas hur en `CloudBlobContainer` referens från [BLOB-databindningen](../functions-bindings-storage-blob-input.md) kan göras tillgänglig för en klass baserad entitet.

```csharp
public class BlobBackedEntity
{
    [JsonIgnore]
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

Mer information om bindningar i Azure Functions finns i dokumentationen för [Azure Functions-utlösare och bindningar](../functions-triggers-bindings.md) .

### <a name="dependency-injection-in-entity-classes"></a>Beroende inmatning i entitets klasser

Entitets klasser stöder [Azure Functions beroende insprutning](../functions-dotnet-dependency-injection.md). Följande exempel visar hur du registrerar en `IHttpClientFactory` tjänst i en klass-baserad entitet.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

Följande kodfragment visar hur du införlivar den inmatade tjänsten i enhets klassen.

```csharp
public class HttpEntity
{
    [JsonIgnore]
    private readonly HttpClient client;

    public HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> Undvik problem med serialisering genom att undanta fält som är avsedda att lagra inmatade värden från serialiseringen.

> [!NOTE]
> Till skillnad från när du använder konstruktorn för att använda konstruktorn i vanliga .NET-Azure Functions, *måste* funktions plats metoden Functions för klassbaserade entiteter deklareras `static` . Om du deklarerar en icke-statisk funktions start punkt kan det orsaka konflikter mellan den normala Azure Functions objekt initieraren och den varaktiga entitetens objekt initierare.

## <a name="function-based-syntax"></a>Function-baserad syntax

Hittills har vi fokuserat på den klassbaserade syntaxen, eftersom vi förväntar sig att det passar bättre för de flesta program. Den Function-baserade syntaxen kan dock vara lämplig för program som vill definiera eller hantera egna abstraktioner för enhets tillstånd och åtgärder. Det kan också vara lämpligt när du implementerar bibliotek som kräver allmänhet för närvarande inte stöds av den klassbaserade syntaxen. 

Med den Function-baserade syntaxen hanterar entitets funktionen explicit åtgärds sändningen och hanterar explicit status för entiteten. Följande kod visar till exempel den *räknar* enhet som implementeras med hjälp av den Function-baserade syntaxen.  

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
        case "delete":
            ctx.DeleteState();
            break;
    }
}
```

### <a name="the-entity-context-object"></a>Entitetens kontext objekt

Entitet-/regionsspecifika funktioner kan nås via ett kontext objekt av typen `IDurableEntityContext` . Detta kontext objekt är tillgängligt som en parameter för entitet-funktionen och via den asynkrona lokala egenskapen `Entity.Current` .

Följande medlemmar ger information om den aktuella åtgärden och gör det möjligt för oss att ange ett retur värde. 

* `EntityName`: namnet på entiteten som körs för tillfället.
* `EntityKey`: nyckeln för entiteten som körs för tillfället.
* `EntityId`: ID: t för entiteten som körs (innehåller namn och nyckel).
* `OperationName`: namnet på den aktuella åtgärden.
* `GetInput<TInput>()`: hämtar InInformationen för den aktuella åtgärden.
* `Return(arg)`: returnerar ett värde till den dirigering som anropade åtgärden.

Följande medlemmar hanterar status för entiteten (skapa, läsa, uppdatera, ta bort). 

* `HasState`: huruvida entiteten finns, det har en viss status. 
* `GetState<TState>()`: hämtar entitetens aktuella status. Om den inte redan finns skapas den.
* `SetState(arg)`: skapar eller uppdaterar status för entiteten.
* `DeleteState()`: tar bort status för entiteten, om den finns. 

Om det tillstånd som returneras av `GetState` är ett objekt, kan det ändras direkt av program koden. Du behöver inte anropa `SetState` igen i slutet (men inte heller någon skada). Om `GetState<TState>` anropas flera gånger måste samma typ användas.

Slutligen används följande medlemmar för att signalera andra entiteter eller starta nya dirigeringar:

* `SignalEntity(EntityId, operation, input)`: skickar ett envägs meddelande till en entitet.
* `CreateNewOrchestration(orchestratorFunctionName, input)`: startar ett nytt dirigering.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om entitets-koncept](durable-functions-entities.md)
