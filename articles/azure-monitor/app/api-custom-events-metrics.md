---
title: Application Insights-API för anpassade händelser och mått | Microsoft Docs
description: Infoga några rader kod i din enhet eller Skriv bords app, webb sida eller tjänst, för att spåra användning och diagnostisera problem.
ms.topic: conceptual
ms.date: 05/11/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 881c657b25d04834d83221c738c578b8281752b7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593754"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>API för Application Insights för anpassade händelser och mått

Infoga några rader kod i ditt program för att ta reda på vad användarna gör med den, eller för att diagnostisera problem. Du kan skicka telemetri från enheter och skrivbordsappar, webb klienter och webb servrar. Använd API för [Azure Application Insights](./app-insights-overview.md) Core-telemetri för att skicka anpassade händelser och mått och dina egna versioner av standard telemetri. Detta API är samma API som standard Application Insights data insamlare använder.

## <a name="api-summary"></a>API-sammanfattning

Huvud-API: t är enhetligt för alla plattformar, förutom några varianter som `GetMetric` (endast .net).

| Metod | Används för |
| --- | --- |
| [`TrackPageView`](#page-views) |Sidor, skärmar, blad eller formulär. |
| [`TrackEvent`](#trackevent) |Användar åtgärder och andra händelser. Används för att spåra användar beteendet eller för att övervaka prestanda. |
| [`GetMetric`](#getmetric) |Noll och flerdimensionella mått, centralt konfigurerad agg regering, endast C#. |
| [`TrackMetric`](#trackmetric) |Prestanda mätningar, till exempel Kölängd-längder som inte är relaterade till vissa händelser. |
| [`TrackException`](#trackexception) |Loggnings undantag för diagnostik. Spåra var de sker i förhållande till andra händelser och undersök stack spår. |
| [`TrackRequest`](#trackrequest) |Loggning av frekvens och varaktighet för server begär Anden för prestanda analys. |
| [`TrackTrace`](#tracktrace) |Logg meddelanden för resurs Diagnostic. Du kan också fånga loggar från tredje part. |
| [`TrackDependency`](#trackdependency) |Loggning av varaktighet och frekvens för anrop till externa komponenter som din app är beroende av. |

Du kan [bifoga egenskaper och mått](#properties) till de flesta av dessa telemetri samtal.

## <a name="before-you-start"></a><a name="prep"></a>Innan du börjar

Om du inte har en referens på Application Insights SDK ännu:

* Lägg till Application Insights SDK i projektet:

  * [ASP.NET-projekt](./asp-net.md)
  * [ASP.NET Core projekt](./asp-net-core.md)
  * [Java-projekt](./java-get-started.md)
  * [Node.js projekt](./nodejs.md)
  * [Java Script på varje webb sida](./javascript.md) 
* I enhets-eller webb server koden inkluderar du:

    *C#:*`using Microsoft.ApplicationInsights;`

    *Visual Basic:*`Imports Microsoft.ApplicationInsights`

    *Java:*`import com.microsoft.applicationinsights.TelemetryClient;`

    *Node.js:*`var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Hämta en TelemetryClient-instans

Hämta en instans av `TelemetryClient` (förutom i Java Script på webb sidor):

För [ASP.net Core](asp-net-core.md#how-can-i-track-telemetry-thats-not-automatically-collected) appar och [icke-http/arbetare för .net/.net Core](worker-service.md#how-can-i-track-telemetry-thats-not-automatically-collected) -appar rekommenderar vi att du hämtar en instans av `TelemetryClient` från behållaren för beroende inmatning enligt beskrivningen i respektive dokumentation.

Om du använder AzureFunctions v2 + eller Azure WebJobs v3 +-följer du det här dokumentet: https://docs.microsoft.com/azure/azure-functions/functions-monitoring#version-2x-and-higher

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```
För alla som ser den här metoden är föråldrade meddelanden kan du besöka [Microsoft/ApplicationInsights-dotNet # 1](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1152) a mer information.

*Visual Basic*

```vb
Private Dim telemetry As New TelemetryClient
```

*Java*

```java
private TelemetryClient telemetry = new TelemetryClient();
``` 

*Node.js*

```javascript
var telemetry = applicationInsights.defaultClient;
```

TelemetryClient är tråd säker.

För ASP.NET-och Java-projekt registreras inkommande HTTP-begäranden automatiskt. Du kanske vill skapa ytterligare instanser av TelemetryClient för en annan modul i appen. Du kan till exempel ha en TelemetryClient-instans i din mellanliggande klass för att rapportera affärs logik händelser. Du kan ange egenskaper som UserId och DeviceId för att identifiera datorn. Den här informationen är kopplad till alla händelser som instansen skickar.

*C#*

```csharp
TelemetryClient.Context.User.Id = "...";
TelemetryClient.Context.Device.Id = "...";
```

*Java*

```java
telemetry.getContext().getUser().setId("...");
telemetry.getContext().getDevice().setId("...");
```

I Node.js-projekt kan du använda `new applicationInsights.TelemetryClient(instrumentationKey?)` för att skapa en ny instans, men det rekommenderas endast för scenarier som kräver isolerad konfiguration från singleton `defaultClient` .

## <a name="trackevent"></a>TrackEvent

I Application Insights är en *Anpassad händelse* en data punkt som du kan visa i [Metrics Explorer](../essentials/metrics-charts.md) som ett sammanställt antal och i [diagnostisk sökning](./diagnostic-search.md) som enskilda förekomster. (Den är inte relaterad till MVC eller andra ramverk "händelser.")

Infoga `TrackEvent` anrop i koden för att räkna olika händelser. Hur ofta användarna väljer en viss funktion, hur ofta de uppnår särskilda mål eller hur ofta de kan göra särskilda typer av misstag.

Till exempel, i en spelapp, skicka en händelse varje gång en användare vinner spelet:

*JavaScript*

```javascript
appInsights.trackEvent({name:"WinGame"});
```

*C#*

```csharp
telemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
telemetry.TrackEvent("WinGame")
```

*Java*

```java
telemetry.trackEvent("WinGame");
```

*Node.js*

```javascript
telemetry.trackEvent({name: "WinGame"});
```

### <a name="custom-events-in-analytics"></a>Anpassade händelser i Analytics

Telemetrin är tillgänglig i `customEvents` tabellen på [fliken Application Insights loggar](../logs/log-query-overview.md) eller i [användnings upplevelsen](usage-overview.md). Händelser kan komma från `trackEvent(..)` eller [Klicka på plugin-programmet för automatisk insamling av analys](javascript-click-analytics-plugin.md).

 

Om [sampling](./sampling.md) är i drift, Visar egenskapen itemCount ett värde som är större än 1. Till exempel itemCount = = 10 betyder 10 anrop till trackEvent (), men samplings processen överför bara en av dem. För att få rätt antal anpassade händelser bör du därför använda kod som `customEvents | summarize sum(itemCount)` .

## <a name="getmetric"></a>GetMetric

Om du vill lära dig hur du effektivt använder GetMetric ()-anropet för att avbilda lokalt församlade mått för .NET-och .NET Core-program går du till [GetMetric](./get-metric.md) -dokumentationen.

## <a name="trackmetric"></a>TrackMetric

> [!NOTE]
> Microsoft. ApplicationInsights. TelemetryClient. TrackMetric är inte den bästa metoden för att skicka mått. Mått bör alltid vara församlade över en tids period innan de skickas. Använd en av GetMetric (..)-överlagringarna för att få ett mått objekt för åtkomst till SDK-församlings funktioner. Om du implementerar din egen för insamlings logik kan du använda metoden TrackMetric () för att skicka de resulterande samlingarna. Om ditt program kräver att du skickar ett separat telemetri-objekt i varje tillfälle utan att behöva sammanställningen över tid, har du förmodligen ett användnings fall för telemetri. Se TelemetryClient. TrackEvent (Microsoft. ApplicationInsights. DataContracts. EventTelemetry).

Application Insights kan diagram mått som inte är kopplade till specifika händelser. Du kan till exempel övervaka en Kölängd med jämna mellanrum. Med mått är de enskilda mätningarna mindre intressanta än variationer och trender, och därför är statistik diagram användbara.

För att kunna skicka mått till Application Insights kan du använda `TrackMetric(..)` API: et. Det finns två sätt att skicka ett mått:

* Enskilt värde. Varje gång du utför en mätning i ditt program skickar du motsvarande värde till Application Insights. Anta till exempel att du har ett mått som beskriver antalet objekt i en behållare. Under en viss tids period sätter du först tre objekt i behållaren och tar sedan bort två objekt. Därför skulle du anropa `TrackMetric` två gånger: först skickar värdet `3` och sedan värdet `-2` . Application Insights lagrar båda värdena för din räkning.

* Aggregat. När du arbetar med mått är varje enskild mätning sällan intressant. I stället är en sammanfattning av vad som hände under en viss tids period viktigt. En sådan Sammanfattning kallas _agg regering_. I exemplet ovan är total summan för den tids perioden `1` och antalet mått värden `2` . När du använder agg regerings metoden anropar du bara `TrackMetric` en gång per tids period och skickar de sammanställda värdena. Detta är den rekommenderade metoden eftersom det kan minska kostnaderna och prestanda genom att skicka färre data punkter till Application Insights, samtidigt som all relevant information samlas in.

### <a name="examples"></a>Exempel

#### <a name="single-values"></a>Enstaka värden

Så här skickar du ett enskilt mått värde:

*JavaScript*

 ```javascript
appInsights.trackMetric("queueLength", 42.0);
 ```

*C#*

```csharp
var sample = new MetricTelemetry();
sample.Name = "metric name";
sample.Value = 42.3;
telemetryClient.TrackMetric(sample);
```

*Java*

```java
telemetry.trackMetric("queueLength", 42.0);
```

*Node.js*

 ```javascript
telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

### <a name="custom-metrics-in-analytics"></a>Anpassade mått i Analytics

Telemetrin är tillgänglig i `customMetrics` tabellen i [Application Insights Analytics](../logs/log-query-overview.md). Varje rad representerar ett anrop till `trackMetric(..)` i din app.

* `valueSum` – Det här är summan av måtten. Hämta medelvärdet genom att dividera med `valueCount` .
* `valueCount` -Antalet mått som samlats in i det här `trackMetric(..)` anropet.

## <a name="page-views"></a>Sid visningar

I en enhets-eller webb Side app skickas telemetri om sid visning som standard när varje skärm eller sida läses in. Men du kan ändra det för att spåra sid visningar vid ytterligare eller olika tidpunkter. I en app som till exempel visar flikar eller blad kan du vilja spåra en sida när användaren öppnar ett nytt blad.

Användar-och sessionsdata skickas som egenskaper tillsammans med sidvyer, så att användar-och session diagram kommer igång när det finns telemetri för sid visning.

### <a name="custom-page-views"></a>Anpassade sid visningar

*JavaScript*

```javascript
appInsights.trackPageView("tab1");
```

*C#*

```csharp
telemetry.TrackPageView("GameReviewPage");
```

*Visual Basic*

```vb
telemetry.TrackPageView("GameReviewPage")
```

*Java*

```java
telemetry.trackPageView("GameReviewPage");
```

Om du har flera flikar i olika HTML-sidor kan du ange URL: en:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Sid visningar för tidtagning

Som standard mäts de tider som rapporteras som **sid inläsnings tid** när webbläsaren skickar begäran, tills webbläsarens sid inläsnings händelse anropas.

I stället kan du antingen:

* Ange en explicit varaktighet i [trackPageView](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/API.md#trackpageview) -anropet: `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);` .
* Använd tids anrops anrop för sid visning `startTrackPage` och `stopTrackPage` .

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

Det namn som du använder som första parameter associerar start-och stopp anropen. Standardvärdet är det aktuella namnet på sidan.

Den resulterande sid inläsningens varaktighet som visas i Metrics Explorer härleds från intervallet mellan start-och stopp anropen. Det är upp till dig vilket intervall du faktiskt har tid.

### <a name="page-telemetry-in-analytics"></a>Sid telemetri i analys

I [Analytics](../logs/log-query-overview.md) två tabeller visas data från webb läsar åtgärder:

* `pageViews`Tabellen innehåller data om URL: en och sid rubriken
* `browserTimings`Tabellen innehåller data om klient prestanda, till exempel hur lång tid det tar att bearbeta inkommande data

Så här tar du reda på hur lång tid det tar för webbläsaren att bearbeta olika sidor:

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

Så här identifierar du de populäraste webbläsarna:

```kusto
pageViews
| summarize count() by client_Browser
```

Koppla sid visningar till AJAX-anrop genom att ansluta till beroenden:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest

Server-SDK: n använder TrackRequest för att logga HTTP-begäranden.

Du kan också anropa det själv om du vill simulera begär anden i ett sammanhang där du inte har webb tjänst modulen igång.

Det rekommenderade sättet att skicka telemetri för begäran är dock där begäran fungerar som en <a href="#operation-context">Åtgärds kontext</a>.

## <a name="operation-context"></a>Åtgärds kontext

Du kan korrelera telemetri-objekt tillsammans genom att associera dem med åtgärds kontext. Standardmodulen för fråge spårning gör detta för undantag och andra händelser som skickas medan en HTTP-begäran bearbetas. I [Sök](./diagnostic-search.md) och [analyser](../logs/log-query-overview.md)kan du enkelt hitta händelser som är associerade med begäran med hjälp av dess åtgärds-ID.

Se [telemetri-korrelation i Application Insights](./correlation.md) för mer information om korrelation.

När du spårar telemetri manuellt, är det enklaste sättet att garantera telemetri korrelation genom att använda det här mönstret:

*C#*

```csharp
// Establish an operation context and associated telemetry item:
using (var operation = telemetryClient.StartOperation<RequestTelemetry>("operationName"))
{
    // Telemetry sent in here will use the same operation ID.
    ...
    telemetryClient.TrackTrace(...); // or other Track* calls
    ...

    // Set properties of containing telemetry item--for example:
    operation.Telemetry.ResponseCode = "200";

    // Optional: explicitly send telemetry item:
    telemetryClient.StopOperation(operation);

} // When operation is disposed, telemetry item is sent.
```

Tillsammans med att ange en åtgärds kontext `StartOperation` skapar ett telemetri-objekt av den typ som du anger. Det skickar objektet telemetri när du tar bort åtgärden, eller om du uttryckligen anropar `StopOperation` . Om du använder `RequestTelemetry` som telemetri-typ anges dess varaktighet till det tidsinställda intervallet mellan start och stopp.

Telemetri-objekt som rapporter ATS inom ett åtgärds område blir underordnade till en sådan åtgärd. Åtgärds kontexter kan kapslas.

I sökningen används åtgärds kontexten för att skapa listan över **relaterade objekt** :

![Relaterade objekt](./media/api-custom-events-metrics/21.png)

Mer information om anpassad drift spårning finns i avsnittet [spåra anpassade åtgärder med Application Insights .NET SDK](./custom-operations-tracking.md) .

### <a name="requests-in-analytics"></a>Förfrågningar i analys

I [Application Insights Analytics](../logs/log-query-overview.md)visas begär anden i `requests` tabellen.

Om [sampling](./sampling.md) är i drift visar egenskapen itemCount ett värde som är större än 1. Till exempel itemCount = = 10 betyder 10 anrop till trackRequest (), men samplings processen överför bara en av dem. Använd kod som till exempel för att få ett korrekt antal begär Anden och genomsnittlig varaktighet per beställnings namn:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException

Skicka undantag till Application Insights:

* För att [räkna dem](../essentials/metrics-charts.md), som en indikering av frekvensen för ett problem.
* För att [granska enskilda förekomster](./diagnostic-search.md).

Rapporterna innehåller stack spåren.

*C#*

```csharp
try
{
    ...
}
catch (Exception ex)
{
    telemetry.TrackException(ex);
}
```

*Java*

```java
try {
    ...
} catch (Exception ex) {
    telemetry.trackException(ex);
}
```

*JavaScript*

```javascript
try
{
    ...
}
catch (ex)
{
    appInsights.trackException(ex);
}
```

*Node.js*

```javascript
try
{
    ...
}
catch (ex)
{
    telemetry.trackException({exception: ex});
}
```

SDK: erna fångar många undantag automatiskt, så du behöver inte alltid anropa TrackException explicit.

* ASP.NET: [Skriv kod till catch-undantag](./asp-net-exceptions.md).
* Java EE: [undantag fångas automatiskt](./java-get-started.md#exceptions-and-request-failures).
* Java Script: undantag fångas automatiskt. Om du vill inaktivera automatisk insamling lägger du till en rad i kodfragmentet som du infogar på dina webb sidor:

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Undantag i analys

I [Application Insights Analytics](../logs/log-query-overview.md)visas undantagen i `exceptions` tabellen.

Om [sampling](./sampling.md) är i drift `itemCount` Visar egenskapen ett värde som är större än 1. Till exempel itemCount = = 10 betyder 10 anrop till trackException (), men samplings processen överför bara en av dem. För att få rätt antal undantag segment av typen av undantag, Använd kod som:

```kusto
exceptions
| summarize sum(itemCount) by type
```

De flesta viktiga stack-uppgifter har redan extraherats i separata variabler, men du kan dra isär `details` strukturen för att få mer information. Eftersom den här strukturen är dynamisk bör du omvandla resultatet till den typ som du förväntar dig. Exempel:

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Om du vill associera undantag med relaterade begär Anden använder du en koppling:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace

Använd TrackTrace för att diagnostisera problem genom att skicka ett "spårande spår" till Application Insights. Du kan skicka segment med diagnostikdata och granska dem i [diagnostisk sökning](./diagnostic-search.md).

I .NET [log-kort](./asp-net-trace-logs.md) använder du detta API för att skicka loggar från tredje part till portalen.

I Java för [standard loggar som log4j använder Logback](./java-trace-logs.md) Application Insights log4j eller logback-tillägg för att skicka loggar från tredje part till portalen.

*C#*

```csharp
telemetry.TrackTrace(message, SeverityLevel.Warning, properties);
```

*Java*

```java
telemetry.trackTrace(message, SeverityLevel.Warning, properties);
```

*Node.js*

```javascript
telemetry.trackTrace({
    message: message,
    severity: applicationInsights.Contracts.SeverityLevel.Warning,
    properties: properties
});
```

*Klient-och webb läsar skript*

```javascript
trackTrace(message: string, properties?: {[string]:string}, severityLevel?: SeverityLevel)
```

Logga en diagnostisk händelse som att ange eller lämna en metod.

 Parameter | Beskrivning
---|---
`message` | Diagnostikdata. Kan vara mycket längre än ett namn.
`properties` | Mappning av sträng till sträng: ytterligare data som används för att [filtrera undantag](#properties) i portalen. Standardvärdet är tomt.
`severityLevel` | Värden som stöds: [SeverityLevel. TS](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/shared/AppInsightsCommon/src/Interfaces/Contracts/Generated/SeverityLevel.ts)

Du kan söka efter meddelande innehåll, men (till skillnad från egenskaps värden) Det går inte att filtrera på det.

Storleks gränsen på `message` är mycket högre än gränsen för egenskaper.
En fördel med TrackTrace är att du kan ställa in relativt långa data i meddelandet. Du kan till exempel koda POST-data där.  

Dessutom kan du lägga till en allvarlighets grad i meddelandet. Liksom andra telemetri kan du lägga till egenskaps värden som hjälper dig att filtrera eller söka efter olika uppsättningar med spår. Exempel:

*C#*

```csharp
var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
telemetry.TrackTrace("Slow database response",
                SeverityLevel.Warning,
                new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```java
Map<String, Integer> properties = new HashMap<>();
properties.put("Database", db.ID);
telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);
```

I [sökningen](./diagnostic-search.md)kan du sedan enkelt filtrera ut alla meddelanden med en viss allvarlighets grad som relaterar till en viss databas.

### <a name="traces-in-analytics"></a>Spår i analys

I [Application Insights Analytics](../logs/log-query-overview.md)visas anrop till TrackTrace i `traces` tabellen.

Om [sampling](./sampling.md) är i drift, Visar egenskapen itemCount ett värde som är större än 1. Till exempel itemCount = = 10 innebär att 10 anrop till `trackTrace()` , och att endast en av dem överförs av samplings processen. För att få ett korrekt antal spårnings anrop bör du använda kod som `traces | summarize sum(itemCount)` .

## <a name="trackdependency"></a>TrackDependency

Använd TrackDependency-anropet för att spåra svars tiderna och framgångs frekvensen för anrop till en extern kod. Resultaten visas i beroende diagram i portalen. Kodfragmentet nedan måste läggas till överallt där ett beroende anrop görs.

> [!NOTE]
> För .NET och .NET Core kan du alternativt använda `TelemetryClient.StartOperation` metoden (Extension) som fyller de `DependencyTelemetry` egenskaper som behövs för korrelationen och andra egenskaper som start tid och varaktighet, så att du inte behöver skapa en anpassad timer som i exemplen nedan. Mer information finns [i artikeln om utgående beroende spårning](./custom-operations-tracking.md#outgoing-dependencies-tracking).

*C#*

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
catch(Exception ex) 
{
    success = false;
    telemetry.TrackException(ex);
    throw new Exception("Operation went wrong", ex);
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("DependencyType", "myDependency", "myCall", startTime, timer.Elapsed, success);
}
```

*Java*

```java
boolean success = false;
Instant startTime = Instant.now();
try {
    success = dependency.call();
}
finally {
    Instant endTime = Instant.now();
    Duration delta = Duration.between(startTime, endTime);
    RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
    dependencyTelemetry.setTimeStamp(startTime);
    telemetry.trackDependency(dependencyTelemetry);
}
```

*Node.js*

```javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({
        dependencyTypeName: "myDependency",
        name: "myCall",
        duration: elapsed,
        success: success
    });
}
```

Kom ihåg att Server-SDK: erna innehåller en [beroende modul](./asp-net-dependencies.md) som identifierar och spårar vissa beroende anrop automatiskt, till exempel till databaser och REST-API: er. Du måste installera en agent på servern för att modulen ska fungera. 

I Java kan vissa beroende anrop spåras automatiskt med hjälp av [Java-agenten](./java-agent.md).

Du använder det här anropet om du vill spåra anrop som den automatiska spårningen inte fångar eller om du inte vill installera agenten.

Om du vill stänga av standard modulen för beroende spårning i C# redigerar du [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) och tar bort referensen till `DependencyCollector.DependencyTrackingTelemetryModule` . I Java ska du inte installera Java-agenten om du inte vill samla in standard beroenden automatiskt.

### <a name="dependencies-in-analytics"></a>Beroenden i analys

I [Application Insights Analytics](../logs/log-query-overview.md)visas trackDependency-anrop i `dependencies` tabellen.

Om [sampling](./sampling.md) är i drift, Visar egenskapen itemCount ett värde som är större än 1. Till exempel itemCount = = 10 betyder 10 anrop till trackDependency (), men samplings processen överför bara en av dem. Använd kod som till exempel för att få rätt antal beroenden som segmenteras av mål komponenten:

```kusto
dependencies
| summarize sum(itemCount) by target
```

Om du vill associera beroenden med relaterade begär Anden använder du en koppling:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Rensar data

Normalt skickar SDK data med fasta intervall (vanligt vis 30 sekunder) eller när bufferten är full (vanligt vis 500 objekt). Men i vissa fall kanske du vill tömma bufferten, till exempel om du använder SDK i ett program som avslutas.

*C#*

 ```csharp
telemetry.Flush();
// Allow some time for flushing before shutdown.
System.Threading.Thread.Sleep(5000);
```

*Java*

```java
telemetry.flush();
//Allow some time for flushing before shutting down
Thread.sleep(5000);
```

*Node.js*

```javascript
telemetry.flush();
```

Funktionen är asynkron för [serverns telemetri-kanal](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

Den bästa metoden Flush () ska användas i programmets avstängnings aktivitet.

## <a name="authenticated-users"></a>Autentiserade användare

I en webbapp identifieras användare (som standard) [av cookies](./usage-segmentation.md#the-users-sessions-and-events-segmentation-tool). En användare kan räknas mer än en gång om de får åtkomst till din app från en annan dator eller webbläsare, eller om de tar bort cookies.

Om användarna loggar in i din app kan du få ett mer exakt antal genom att ange det autentiserade användar-ID: t i webb läsar koden:

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

I ett ASP.NET-webbmvc-program, till exempel:

*Razor*

```cshtml
@if (Request.IsAuthenticated)
{
    <script>
        appInsights.setAuthenticatedUserContext("@User.Identity.Name
            .Replace("\\", "\\\\")"
            .replace(/[,;=| ]+/g, "_"));
    </script>
}
```

Du behöver inte använda användarens faktiska inloggnings namn. Det måste bara vara ett ID som är unikt för den användaren. Det får inte innehålla blank steg eller något av tecknen `,;=|` .

Användar-ID: t anges också i en sessions-cookie och skickas till servern. Om Server-SDK: n installeras skickas det autentiserade användar-ID: t som en del av kontext egenskaperna för både klient-och Server telemetri. Sedan kan du filtrera och söka efter den.

Om din app grupperar användare till konton kan du också skicka en identifierare för kontot (med samma begränsningar för tecknen).

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

I [Metrics Explorer](../essentials/metrics-charts.md)kan du skapa ett diagram som räknar **användare, autentiserade** och **användar konton**.

Du kan också [söka](./diagnostic-search.md) efter klient data punkter med vissa användar namn och konton.

## <a name="filtering-searching-and-segmenting-your-data-by-using-properties"></a><a name="properties"></a>Filtrera, söka och segmentera dina data med hjälp av egenskaper

Du kan koppla egenskaper och mått till dina händelser (och även till mått, Sidvyer, undantag och andra telemetridata).

*Egenskaper* är sträng värden som du kan använda för att filtrera Telemetrin i användnings rapporterna. Om din app till exempel tillhandahåller flera spel kan du koppla spelets namn till varje händelse så att du kan se vilka spel som är mer populära.

Det finns en gräns på 8192 på sträng längden. (Om du vill skicka stora data mängder använder du meddelande parametern TrackTrace.)

*Mått* är numeriska värden som kan visas grafiskt. Du kanske till exempel vill se om det finns en gradvis ökning av poängen som dina spelare uppnår. Graferna kan segmenteras av de egenskaper som skickas med händelsen, så att du kan få separata eller staplade diagram för olika spel.

För att mått värden ska visas korrekt ska de vara större än eller lika med 0.

Det finns vissa [begränsningar för antalet egenskaper, egenskaps värden och mått](#limits) som du kan använda.

*JavaScript*

```javascript
appInsights.trackEvent
    ("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );

appInsights.trackPageView
    ("page name", "http://fabrikam.com/pageurl.html",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );
```

*C#*

```csharp
// Set up some properties and metrics:
var properties = new Dictionary <string, string>
    {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
var metrics = new Dictionary <string, double>
    {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

// Send the event:
telemetry.TrackEvent("WinGame", properties, metrics);
```

*Node.js*

```javascript
// Set up some properties and metrics:
var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

// Send the event:
telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});
```

*Visual Basic*

```vb
' Set up some properties:
Dim properties = New Dictionary (Of String, String)
properties.Add("game", currentGame.Name)
properties.Add("difficulty", currentGame.Difficulty)

Dim metrics = New Dictionary (Of String, Double)
metrics.Add("Score", currentGame.Score)
metrics.Add("Opponents", currentGame.OpponentCount)

' Send the event:
telemetry.TrackEvent("WinGame", properties, metrics)
```

*Java*

```java
Map<String, String> properties = new HashMap<String, String>();
properties.put("game", currentGame.getName());
properties.put("difficulty", currentGame.getDifficulty());

Map<String, Double> metrics = new HashMap<String, Double>();
metrics.put("Score", currentGame.getScore());
metrics.put("Opponents", currentGame.getOpponentCount());

telemetry.trackEvent("WinGame", properties, metrics);
```

> [!NOTE]
> Tänk på att inte logga personligt identifierbar information i egenskaper.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>Alternativt sätt att ange egenskaper och mått

Om det är bekvämare kan du samla in parametrar för en händelse i ett separat objekt:

```csharp
var event = new EventTelemetry();

event.Name = "WinGame";
event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
event.Properties["game"] = currentGame.Name;
event.Properties["difficulty"] = currentGame.Difficulty;
event.Metrics["Score"] = currentGame.Score;
event.Metrics["Opponents"] = currentGame.Opponents.Length;

telemetry.TrackEvent(event);
```

> [!WARNING]
> Återanvänd inte den här instansen av telemetri ( `event` i det här exemplet) för att anropa Track * () flera gånger. Detta kan leda till att telemetri skickas med felaktig konfiguration.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Anpassade mätningar och egenskaper i Analytics

I [Analytics](../logs/log-query-overview.md)visas anpassade mått och egenskaper i `customMeasurements` `customDimensions` attributen och för varje telemetri-post.

Om du till exempel har lagt till en egenskap med namnet "Game" i din begäran om telemetri, räknar den här frågan förekomster av olika värden för "Game" och visar genomsnittet för det anpassade måttet "score":

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

Observera att:

* När du extraherar ett värde från customDimensions-eller customMeasurements-JSON har den dynamisk typ, så du måste konvertera det `tostring` eller `todouble` .
* För att ta hänsyn till möjligheten till [samplingen](./sampling.md)bör du `sum(itemCount)` inte använda `count()` .

## <a name="timing-events"></a><a name="timed"></a> Tids händelser

Ibland vill du skapa ett diagram över hur lång tid det tar att utföra en åtgärd. Du kanske till exempel vill veta hur lång tid det tar för användare att överväga alternativ i ett spel. Du kan använda mått parametern för detta.

*C#*

```csharp
var stopwatch = System.Diagnostics.Stopwatch.StartNew();

// ... perform the timed action ...

stopwatch.Stop();

var metrics = new Dictionary <string, double>
    {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

// Set up some properties:
var properties = new Dictionary <string, string>
    {{"signalSource", currentSignalSource.Name}};

// Send the event:
telemetry.TrackEvent("SignalProcessed", properties, metrics);
```

*Java*

```java
long startTime = System.currentTimeMillis();

// Perform timed action

long endTime = System.currentTimeMillis();
Map<String, Double> metrics = new HashMap<>();
metrics.put("ProcessingTime", (double)endTime-startTime);

// Setup some properties
Map<String, String> properties = new HashMap<>();
properties.put("signalSource", currentSignalSource.getName());

// Send the event
telemetry.trackEvent("SignalProcessed", properties, metrics);
```

## <a name="default-properties-for-custom-telemetry"></a><a name="defaults"></a>Standard egenskaper för anpassad telemetri

Om du vill ange standard egenskaps värden för några av de anpassade händelser som du skriver, kan du ställa in dem i en TelemetryClient-instans. De är kopplade till varje telemetri-objekt som skickas från den klienten.

*C#*

```csharp
using Microsoft.ApplicationInsights.DataContracts;

var gameTelemetry = new TelemetryClient();
gameTelemetry.Context.GlobalProperties["Game"] = currentGame.Name;
// Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
Dim gameTelemetry = New TelemetryClient()
gameTelemetry.Context.GlobalProperties("Game") = currentGame.Name
' Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame")
```

*Java*

```java
import com.microsoft.applicationinsights.TelemetryClient;
import com.microsoft.applicationinsights.TelemetryContext;
...


TelemetryClient gameTelemetry = new TelemetryClient();
TelemetryContext context = gameTelemetry.getContext();
context.getProperties().put("Game", currentGame.Name);

gameTelemetry.TrackEvent("WinGame");
```

*Node.js*

```javascript
var gameTelemetry = new applicationInsights.TelemetryClient();
gameTelemetry.commonProperties["Game"] = currentGame.Name;

gameTelemetry.TrackEvent({name: "WinGame"});
```

Enskilda telemetri samtal kan åsidosätta standardvärdena i sina egenskaps ord listor.

*För JavaScript-webbklienter* använder du JavaScript-telemetri för att initiera.

[Implementera `ITelemetryInitializer`](./api-filtering-sampling.md#add-properties)om du *vill lägga till egenskaper till all telemetri*, inklusive data från standardsamlings-moduler.

## <a name="sampling-filtering-and-processing-telemetry"></a>Sampling, filtrering och bearbetning av telemetri

Du kan skriva kod för att bearbeta din telemetri innan den skickas från SDK: n. Bearbetningen innehåller data som skickas från standardmodulerna för telemetri, till exempel insamling av HTTP-förfrågningar och beroende insamling.

[Lägg till egenskaper](./api-filtering-sampling.md#add-properties) till telemetri genom att implementera `ITelemetryInitializer` . Du kan till exempel lägga till versions nummer eller värden som beräknas från andra egenskaper.

[Filtrering](./api-filtering-sampling.md#filtering) kan ändra eller ta bort telemetri innan det skickas från SDK: n genom att implementera `ITelemetryProcessor` . Du styr vad som skickas eller ignoreras, men du måste ta hänsyn till påverkan på dina mått. Beroende på hur du tar bort objekt kan du förlora möjligheten att navigera mellan relaterade objekt.

[Sampling](./api-filtering-sampling.md) är en paketerad lösning för att minska mängden data som skickas från din app till portalen. Det gör det utan att påverka de visade måtten. Och det gör det utan att påverka din möjlighet att diagnostisera problem genom att navigera mellan relaterade objekt, till exempel undantag, förfrågningar och sid visningar.

[Läs mer](./api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Inaktivera telemetri

Så här *stoppar och startar* du insamling och överföring av telemetri dynamiskt:

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

För att *inaktivera valda standard insamlare*– till exempel prestanda räknare, HTTP-begäranden eller beroenden – ta bort eller kommentera ut relevanta rader i [ApplicationInsights.config](./configuration-with-applicationinsights-config.md). Du kan göra detta, till exempel om du vill skicka dina egna TrackRequest-data.

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

Om du vill *inaktivera valda standard insamlare*, till exempel prestanda räknare, HTTP-förfrågningar eller beroenden--vid initierings tiden, så kan du använda följande metoder för att ange din initierings kod för SDK:

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

Om du vill inaktivera de här insamlarna efter initiering använder du konfigurationsobjektet: `applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="developer-mode"></a><a name="debug"></a>Utvecklarläge

Under fel sökning är det praktiskt att låta din telemetri påskyndas genom pipelinen så att du kan se resultaten direkt. Du får också ytterligare meddelanden som hjälper dig att spåra eventuella problem med Telemetrin. Stäng av den i produktion, eftersom det kan göra att din app blir långsam.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

*Node.js*

För Node.js kan du aktivera utvecklarläge genom att aktivera intern loggning via `setInternalLogging` och ange `maxBatchSize` till 0, vilket gör att din telemetri skickas så fort den samlas in.

```js
applicationInsights.setup("ikey")
  .setInternalLogging(true, true)
  .start()
applicationInsights.defaultClient.config.maxBatchSize = 0;
```

## <a name="setting-the-instrumentation-key-for-selected-custom-telemetry"></a><a name="ikey"></a> Ange instrument ställnings nyckeln för vald anpassad telemetri

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> Dynamisk Instrumentation-nyckel

För att undvika att kombinera telemetri från utvecklings-, test-och produktions miljöer, kan du [skapa separata Application Insights resurser](./create-new-resource.md) och ändra deras nycklar, beroende på miljön.

I stället för att hämta Instrumentation-nyckeln från konfigurations filen kan du ange den i din kod. Ange nyckeln i en initierings metod, till exempel global.aspx.cs i en ASP.NET-tjänst:

*C#*

```csharp
protected void Application_Start()
{
    Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey =
        // - for example -
        WebConfigurationManager.Settings["ikey"];
    ...
}
```

*JavaScript*

```javascript
appInsights.config.instrumentationKey = myKey;
```

I webb sidor kanske du vill ställa in den från webb serverns tillstånd, i stället för att koda den i det här skriptet. Till exempel på en webb sida som skapats i en ASP.NET-app:

*Java Script i kniv*

```cshtml
<script type="text/javascript">
// Standard Application Insights webpage script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
    // Generate from server property:
    @Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey;
}) // ...
```

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="telemetrycontext"></a>TelemetryContext

TelemetryClient har en kontext egenskap som innehåller värden som skickas tillsammans med alla telemetridata. De anges normalt av standardmodulerna för telemetri, men du kan också ställa in dem själv. Exempel:

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Om du anger något av dessa värden själv kan du ta bort den relevanta raden från [ApplicationInsights.config](./configuration-with-applicationinsights-config.md), så att värdena och standardvärdena inte blir förvirrande.

* **Komponent**: appen och dess version.
* **Enhet**: data om enheten där appen körs. (I Web Apps är detta den server eller klient enhet som telemetri skickas från.)
* **InstrumentationKey**: Application Insights resurs i Azure där telemetri visas. Den hämtas vanligt vis från ApplicationInsights.config.
* **Plats**: enhetens geografiska plats.
* **Åtgärd**: den aktuella http-begäran i Web Apps. I andra typer av appar kan du ange att den ska gruppera händelser tillsammans.
  * **ID**: ett genererat värde som korrelerar olika händelser, så att när du inspekterar en händelse i diagnostisk sökning kan du hitta relaterade objekt.
  * **Namn**: en identifierare, vanligt vis URL: en för http-begäran.
  * **SyntheticSource**: om det inte är null eller tomt, en sträng som anger att källan till begäran har identifierats som ett robot-eller webbtest. Som standard undantas de från beräkningar i Metrics Explorer.
* **Egenskaper**: egenskaper som skickas med alla telemetridata. Den kan åsidosättas i enskilda spår * anrop.
* **Session**: användarens session. ID: t har angetts till ett genererat värde, vilket ändras när användaren inte har varit aktiv under ett tag.
* **Användare**: användar information.

## <a name="limits"></a>Gränser

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

Använd [sampling](./sampling.md)för att undvika att använda data hastighets gränsen.

Se [data kvarhållning och sekretess](./data-retention-privacy.md)för att avgöra hur länge data lagras.

## <a name="reference-docs"></a>Referens dokument

* [ASP.NET-referens](/dotnet/api/overview/azure/insights)
* [Java-referens](/java/api/overview/azure/appinsights)
* [JavaScript-referens](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)

## <a name="sdk-code"></a>SDK-kod

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Windows Server-paket](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="questions"></a>Frågor

* *Vilka undantag kan Track_ ()-anropen Throw?*

    Inga. Du behöver inte figursätta dem i try-catch-satser. Om SDK stöter på problem kommer den att logga meddelanden i fel söknings konsolens utdata och--om meddelandena går igenom i diagnostisk sökning.
* *Finns det ett REST API för att hämta data från portalen?*

    Ja, [API för data åtkomst](https://dev.applicationinsights.io/). Andra sätt att extrahera data är [att exportera från Analytics till Power BI](./export-power-bi.md) och [löpande export](./export-telemetry.md).

## <a name="next-steps"></a><a name="next"></a>Nästa steg

* [Sök efter händelser och loggar](./diagnostic-search.md)
* [Felsökning](../faq.md)
