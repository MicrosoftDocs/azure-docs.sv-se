---
title: Övervaka din Node.js-tjänster med Azure Application Insights | Microsoft Docs
description: Övervaka prestanda- och diagnostiseringsproblem i Node.js-tjänster med Application Insights.
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom: devx-track-js
ms.openlocfilehash: 0d414ce44a8d6ab308bd31f7372bb1c146fac9f5
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611023"
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Övervaka dina Node-js-tjänster och -appar med Application Insights

[Application Insights](./app-insights-overview.md) övervakar dina backend-tjänster och-komponenter efter distributionen för att hjälpa dig att upptäcka och snabbt diagnostisera prestanda och andra problem. Du kan använda Application Insights för Node.js tjänster som finns i ditt data Center, virtuella Azure-datorer och webbappar och även i andra offentliga moln.

För att ta emot, lagra, och utforska dina övervakade data inkluderar du SKD:t i koden och konfigurerar en motsvarande Application Insights-resurs i Azure. SDK:t skickar data till den resursen för ytterligare analys och undersökning.

Node.js-SDK:n kan automatiskt övervaka inkommande och utgående HTTP-begäran, undantag och vissa systemmått. Från och med version 0,20 kan SDK också övervaka vissa vanliga [paket från tredje part](https://github.com/microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers#currently-supported-modules), t. ex. MongoDB, MySQL och Redis. Alla händelser som relaterar till en inkommande HTTP-begäran korreleras för snabbare felsökning.

Med TelemetryClient API kan du manuellt instrumentera och övervaka ytterligare aspekter av din app och ditt system. Vi beskriver TelemetryClient-API:n mer ingående senare i den här artikeln.

## <a name="get-started"></a>Kom igång

Utför följande uppgifter för att konfigurera övervakning för en app eller tjänst.

### <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du se till att ha en Azure-prenumeration eller [så skaffar du en kostnadsfritt][azure-free-offer]. Om din organisation redan har en Azure-prenumeration kan en administratör följa [de här instruktionerna][add-aad-user] för att lägga till dig.

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: ../../active-directory/fundamentals/add-users-azure-active-directory.md

### <a name="set-up-an-application-insights-resource"></a><a name="resource"></a> Konfigurera en Application Insights resurs

1. Logga in på [Azure-portalen][portal].
2. [Skapa en Application Insights resurs](create-new-resource.md)

### <a name="set-up-the-nodejs-sdk"></a><a name="sdk"></a>Konfigurera Node.js-SDK:n

Inkludera SDK:n i din app så den kan samla in data.

> [!IMPORTANT]
> Nya Azure-regioner **kräver** att anslutnings strängar används i stället för instrument knappar. [Anslutnings strängen](./sdk-connection-string.md?tabs=nodejs) identifierar den resurs som du vill associera dina telemetridata med. Du kan också ändra de slut punkter som resursen kommer att använda som mål för din telemetri. Du måste kopiera anslutnings strängen och lägga till den i programmets kod eller till en miljö variabel.

1. Kopiera din resurs Instrumentation-nyckel (kallas även *iKey*) från den nya resursen. Application Insights använder ikey för att mappa data till Azure-resursen. Innan SDK:n kan använda din ikey måste du ange den i en miljövariabel eller i din kod.  

   ![Kopiera instrumenteringsnyckel](./media/nodejs/instrumentation-key-001.png)

2. Lägg till Node.js-SDK-biblioteket till appens beroenden via package.json. Från appens rotmapp kör du:

   ```bash
   npm install applicationinsights --save
   ```

    > [!NOTE]
    > Om du använder TypeScript ska du inte installera separata "Skriv"-paket. Det här NPM-paketet innehåller inbyggda indata.

3. Läs uttryckligen in biblioteket i din kod. Eftersom SDK:n lägger in instrumentationen i många andra bibliotek ska du läsa in biblioteket så tidigt som möjligt, till och med före andra `require`-uttryck.

   ```javascript
   let appInsights = require('applicationinsights');
   ```
4.  Du kan också tillhandahålla en iKey via miljövariabeln `APPINSIGHTS_INSTRUMENTATIONKEY` , i stället för att skicka den manuellt till  `setup()` eller `new appInsights.TelemetryClient()` . Med den här metoden kan du behålla ikeys utanför den allokerade källkoden och ange olika ikeys för olika miljöer. För att konfigurera manuellt anrop `appInsights.setup('[your ikey]');` .

    Ytterligare konfigurationsalternativ finns i följande avsnitt.

    Du kan testa SDK:n utan att skicka telemetri genom att ställa in `appInsights.defaultClient.config.disableAppInsights = true`.

5. Börja samla in och skicka data automatiskt genom att anropa `appInsights.start();` .

### <a name="monitor-your-app"></a><a name="monitor"></a> Övervaka din app

SDK: n samlar automatiskt in telemetri om Node.js Runtime och några vanliga moduler från tredje part. Använd ditt program för att skapa vissa av dessa data.

På [Azure Portal][portal] går du sedan till Application Insights och öppnar den resurs som du skapade tidigare. I **Översiktstidslinje** letar du efter dina första datapunkter. Välj olika komponenter i schemana för att se mer detaljerade data.

Om du vill visa topologin som identifierats för din app kan du använda [program avbildningen](app-map.md).

#### <a name="no-data"></a>Inga data

Eftersom SDK:n slår ihop data för sändning kan det uppstå en fördröjning innan objekt visas i portalen. Om du inte ser några data i resursen kan du prova något av följande:

* Fortsätta att använda programmet. Vidta fler åtgärder för att generera mer telemetri.
* Klicka på **Uppdatera** i portalens resursvy. Diagram uppdaterar sig själva regelbundet, men när du trycker på uppdateringsknappen manuellt tvingas de att uppdatera genast.
* Verifiera att [nödvändiga utgående portar](./ip-addresses.md) är öppna.
* Använd [Sök](./diagnostic-search.md) för att söka efter specifika händelser.
* Se [Vanliga frågor][FAQ].

## <a name="basic-usage"></a>Grundläggande användning

För färdiga samlingar av HTTP-begäranden, vanliga biblioteks händelser från tredje part, ohanterade undantag och system mått:

```javascript

let appInsights = require("applicationinsights");
appInsights.setup("[your ikey]").start();

```

> [!NOTE]
> Om Instrumentation-nyckeln anges i miljövariabeln `APPINSIGHTS_INSTRUMENTATIONKEY` `.setup()` kan anropas utan argument. Detta gör det enkelt att använda olika ikeys för olika miljöer.

Läs in Application Insights-biblioteket, `require("applicationinsights")` så tidigt som möjligt i dina skript innan du läser in andra paket. Detta krävs så att Application Insightss biblioteket kan förbereda senare paket för spårning. Om du stöter på konflikter med andra bibliotek som utför liknande förberedelser kan du försöka läsa in Application Insights-biblioteket efter dessa.

På grund av hur Java Script hanterar återanrop är ytterligare arbete nödvändigt för att spåra en begäran mellan externa beroenden och senare återanrop. Som standard är denna ytterligare spårning aktive rad. inaktivera det genom `setAutoDependencyCorrelation(false)` att anropa enligt beskrivningen i [konfigurations](#sdk-configuration) avsnittet nedan.

## <a name="migrating-from-versions-prior-to-022"></a>Migrera från versioner före 0,22

Det finns stora ändringar mellan versioner före version 0,22 och senare. Dessa ändringar är utformade för att öka konsekvensen med andra Application Insights SDK: er och möjliggöra framtida utökning.

I allmänhet kan du migrera med följande:

- Ersätt referenser till `appInsights.client` med `appInsights.defaultClient` .
- Ersätt referenser till `appInsights.getClient()` med `new appInsights.TelemetryClient()`
- Ersätt alla argument till client. Track *-metoder med ett enda objekt som innehåller namngivna egenskaper som argument. Se din IDE: s inbyggda typ tips eller [TelemetryTypes](https://github.com/Microsoft/ApplicationInsights-node.js/tree/develop/Declarations/Contracts/TelemetryTypes) för det undantagna objektet för varje typ av telemetri.

Om du har åtkomst till SDK-konfigurations funktioner utan att koppla dem till `appInsights.setup()` , kan du nu hitta dessa funktioner på `appInsights.Configurations` (till exempel `appInsights.Configuration.setAutoCollectDependencies(true)` ). Granska ändringarna i standard konfigurationen i nästa avsnitt.

## <a name="sdk-configuration"></a>SDK-konfiguration

`appInsights`Objektet innehåller ett antal konfigurations metoder. De anges i följande kodfragment med standardvärdena.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true, true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .setSendLiveMetrics(false)
    .setDistributedTracingMode(appInsights.DistributedTracingModes.AI)
    .start();
```

Ställ in `.setAutoDependencyCorrelation(true)` för att korrelera händelser i en tjänst fullständigt. När det alternativet är inställt kan SDK:n spåra innehåll över asynkrona återanrop i Node.js.

Granska deras beskrivningar i din IDE: s inbyggda typ tips eller [applicationinsights. TS](https://github.com/microsoft/ApplicationInsights-node.js/blob/develop/applicationinsights.ts) om du vill ha detaljerad information om vad dessa kontroller och valfria sekundära argument innehåller.

> [!NOTE]
>  Som standard `setAutoCollectConsole` konfigureras att *undanta* anrop till `console.log` (och andra konsol metoder). Endast anrop till stödda tredjeparts loggar (till exempel Winston och Bunyan) kommer att samlas in. Du kan ändra det här beteendet för att inkludera anrop till `console` metoder med hjälp av `setAutoCollectConsole(true, true)` .

### <a name="sampling"></a>Samling

Som standard skickar SDK alla insamlade data till Application Insightss tjänsten. Om du samlar in mycket data kanske du vill aktivera sampling för att minska mängden data som skickas. Ange `samplingPercentage` fältet på `config` objekt för en klient för att göra detta. Inställningen `samplingPercentage` till 100 (standard) innebär att alla data kommer att skickas och 0 innebär att inget kommer att skickas.

Om du använder automatisk korrelation tas alla data som är kopplade till en enskild begäran med eller undantas som en enhet.

Lägg till kod som följande för att aktivera sampling:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.config.samplingPercentage = 33; // 33% of all telemetry will be sent to Application Insights
appInsights.start();
```

### <a name="multiple-roles-for-multi-components-applications"></a>Flera roller för program med flera komponenter

Om ditt program består av flera komponenter som du vill använda med samma Instrumentation-nyckel och fortfarande se dessa komponenter som separata enheter i portalen, precis som om de använde separata Instrumentation-nycklar (till exempel som separata noder i program kartan), kan du behöva konfigurera fältet RoleName manuellt för att skilja en komponents telemetri från andra komponenter som skickar data till din Application Insights-resurs.

Använd följande för att ange fältet RoleName:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.context.tags[appInsights.defaultClient.context.keys.cloudRole] = "MyRoleName";
appInsights.start();
```

### <a name="automatic-third-party-instrumentation"></a>Automatisk instrumentering från tredje part

För att kunna spåra kontext över asynkrona samtal krävs vissa ändringar i bibliotek från tredje part, till exempel MongoDB och Redis. Som standard kommer Application Insights att använda [`diagnostic-channel-publishers`](https://github.com/Microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers) för att uppdatera vissa av dessa bibliotek. Detta kan inaktive ras genom att ställa in `APPLICATION_INSIGHTS_NO_DIAGNOSTIC_CHANNEL` miljövariabeln.

> [!NOTE]
> Genom att ange den miljövariabeln kanske händelser inte längre är korrekt kopplade till rätt åtgärd.

 Enskilda apa-patchar kan inaktive ras genom att ställa in `APPLICATION_INSIGHTS_NO_PATCH_MODULES` miljövariabeln på en kommaavgränsad lista med paket som ska inaktive ras (till exempel `APPLICATION_INSIGHTS_NO_PATCH_MODULES=console,redis` ) för att undvika korrigering av `console` and- `redis` paketen.

För närvarande finns det nio paket som instrumenteras:,,,,,,, `bunyan` `console` `mongodb` `mongodb-core` `mysql` `redis` `winston` `pg` och `pg-pool` . Besök [filen Diagnostic-Channel-Publishers README](https://github.com/Microsoft/node-diagnostic-channel/blob/master/src/diagnostic-channel-publishers/README.md) om du vill ha information om exakt vilken version av dessa paket som korrigeras.

`bunyan` `winston` Korrigeringarna, och `console` kommer att generera Application Insights spårnings händelser baserat på om `setAutoCollectConsole` är aktive rad. Resten kommer att generera Application Insights beroende händelser baserat på om `setAutoCollectDependencies` är aktive rad.

### <a name="live-metrics"></a>Live Metrics

Använd om du vill aktivera sändning av Live-mått från din app till Azure `setSendLiveMetrics(true)` . Filtrering av Live-mått i portalen stöds inte för närvarande.

### <a name="extended-metrics"></a>Utökade mått

> [!NOTE]
> Möjligheten att skicka utökade inbyggda mått har lagts till i version 1.4.0

Om du vill aktivera sändning av utökade inbyggda mått från din app till Azure installerar du det separata egna mått paketet. SDK kommer att läsas in automatiskt när det är installerat och börjar samla in Node.js inbyggda mått.

```bash
npm install applicationinsights-native-metrics
```

För närvarande utför det inbyggda mått paketet automatisk insamling av skräp insamlings CPU-tid, händelse slingor och heap-användning:

- **Skräp insamling**: mängden CPU-tid som har använts för varje typ av skräp insamling och hur många förekomster av varje typ.
- **Händelse slinga**: hur många Tick som uppstod och hur mycket processor tid som har använts totalt.
- **Heap vs icke-heap**: hur mycket av appens minnes användning som finns i heapen eller icke-heap.

### <a name="distributed-tracing-modes"></a>Distribuerade spårnings lägen

Som standard skickar SDK huvuden som förstås av andra program/tjänster som instrumentas med en Application Insights SDK. Du kan välja att aktivera sändning/mottagning av [W3C trace context](https://github.com/w3c/trace-context) -rubriker förutom de befintliga AI-rubrikerna, så att du inte kan avbryta korrelationen med någon av dina befintliga äldre tjänster. Om du aktiverar W3C-huvuden kan din app korreleras med andra tjänster som inte är instrumenterade med Application Insights, men Använd denna W3C-standard.

```Javascript
const appInsights = require("applicationinsights");
appInsights
  .setup("<your ikey>")
  .setDistributedTracingMode(appInsights.DistributedTracingModes.AI_AND_W3C)
  .start()
```

## <a name="telemetryclient-api"></a>TelemetryClient-API

En fullständig beskrivning av TelemetryClient-API:n finns i [Application Insights API for custom events and metrics](./api-custom-events-metrics.md) (Application Insights-API för anpassade händelser och mått).

Du kan spåra valfri begäran, händelse, mått eller undantag med Node.js-SDK:n i Application Insights. I följande kodexempel visas några av de tillgängliga API:erna som du kan använda:

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey in env var. start() can be omitted to disable any non-custom data
let client = appInsights.defaultClient;
client.trackEvent({name: "my custom event", properties: {customProperty: "custom property value"}});
client.trackException({exception: new Error("handled exceptions can be logged with this method")});
client.trackMetric({name: "custom metric", value: 3});
client.trackTrace({message: "trace message"});
client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL"});
client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});

let http = require("http");
http.createServer( (req, res) => {
  client.trackNodeHttpRequest({request: req, response: res}); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>Spåra dina beroenden

Du kan använda följande kod för att spåra dina beroenden:

```javascript
let appInsights = require("applicationinsights");
let client = new appInsights.TelemetryClient();

var success = false;
let startTime = Date.now();
// execute dependency call here....
let duration = Date.now() - startTime;
success = true;

client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:duration, resultCode:0, success: true, dependencyTypeName: "ZSQL"});;
```

Ett exempel verktyg `trackMetric` som använder för att mäta hur lång tid det tar för händelse slingor att schemalägga:  

```javascript
function startMeasuringEventLoop() {
  var startTime = process.hrtime();
  var sampleSum = 0;
  var sampleCount = 0;

  // Measure event loop scheduling delay
  setInterval(() => {
    var elapsed = process.hrtime(startTime);
    startTime = process.hrtime();
    sampleSum += elapsed[0] * 1e9 + elapsed[1];
    sampleCount++;
  }, 0);

  // Report custom metric every second
  setInterval(() => {
    var samples = sampleSum;
    var count = sampleCount;
    sampleSum = 0;
    sampleCount = 0;

    if (count > 0) {
      var avgNs = samples / count;
      var avgMs = Math.round(avgNs / 1e6);
      client.trackMetric({name: "Event Loop Delay", value: avgMs});
    }
  }, 1000);
}
```

### <a name="add-a-custom-property-to-all-events"></a>Lägga till en anpassad egenskap i alla händelser

Använd följande kod för att lägga till en anpassad egenskap i alla händelser:

```javascript
appInsights.defaultClient.commonProperties = {
  environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>Spåra HTTP GET-begäranden

Använd följande kod för att manuellt spåra HTTP GET-begär Anden:

> [!NOTE]
> Alla begär Anden spåras som standard. Om du vill inaktivera automatisk insamling, anropa. setAutoCollectRequests (false) innan du anropar start ().

```javascript
appInsights.defaultClient.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});
```

Du kan också spåra begär Anden med hjälp av `trackNodeHttpRequest` metoden:

```javascript
var server = http.createServer((req, res) => {
  if ( req.method === "GET" ) {
      appInsights.defaultClient.trackNodeHttpRequest({request:req, response:res});
  }
  // other work here....
  res.end();
});
```

### <a name="track-server-startup-time"></a>Spåra starttiden för server

Du kan använda följande kod för att spåra starttiden för servern:

```javascript
let start = Date.now();
server.on("listening", () => {
  let duration = Date.now() - start;
  appInsights.defaultClient.trackMetric({name: "server startup time", value: duration});
});
```

### <a name="flush"></a>Flush

Som standard buffras telemetri i 15 sekunder innan det skickas till inmatnings servern. Om ditt program har en kort livs längd (t. ex. ett CLI-verktyg), kan det vara nödvändigt att manuellt tömma din buffrade telemetri när programmet avslutas `appInsights.defaultClient.flush()` .

Om SDK känner av att ditt program kraschar, kommer det att anropa en tömning åt dig `appInsights.defaultClient.flush({ isAppCrashing: true })` . Med alternativet Flush `isAppCrashing` antas ditt program vara i ett onormalt tillstånd, inte lämpligt för att skicka telemetri. I stället sparar SDK all buffrad telemetri till [beständig lagring](./data-retention-privacy.md#nodejs) och låter programmet avslutas. När du startar programmet igen kommer det att försöka skicka telemetri som sparats till den permanenta lagringen.

### <a name="preprocess-data-with-telemetry-processors"></a>Förbearbeta data med telemetri-processorer

Du kan bearbeta och filtrera insamlade data innan de skickas för kvarhållning med hjälp av *telemetri-processorer*. Telemetri-processorer kallas en i den ordning som de lades till innan telemetri-objektet skickas till molnet.

```javascript
public addTelemetryProcessor(telemetryProcessor: (envelope: Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean)
```

Om en telemetri processor returnerar false skickas inte det telemetri-objektet.

Alla telemetri-processorer tar emot telemetridata och dess kuvert för att inspektera och ändra dem. De får också ett kontext objekt. Innehållet i det här objektet definieras av `contextObjects` parametern när du anropar en spårnings metod för manuell spårad telemetri. För automatisk insamlad telemetri fylls det här objektet med tillgänglig information om begäran och det beständiga begär ande innehållet som anges av `appInsights.getCorrelationContext()` (om automatiskt beroende korrelation är aktiverat).

TypeScript-typen för en telemetri processor är:

```javascript
telemetryProcessor: (envelope: ContractsModule.Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean;
```

Till exempel kan en processor som tar bort Stacks från undantags data från undantag skrivas och läggas till enligt följande:

```javascript
function removeStackTraces ( envelope, context ) {
  if (envelope.data.baseType === "Microsoft.ApplicationInsights.ExceptionData") {
    var data = envelope.data.baseData;
    if (data.exceptions && data.exceptions.length > 0) {
      for (var i = 0; i < data.exceptions.length; i++) {
        var exception = data.exceptions[i];
        exception.parsedStack = null;
        exception.hasFullStack = false;
      }
    }
  }
  return true;
}

appInsights.defaultClient.addTelemetryProcessor(removeStackTraces);
```

## <a name="use-multiple-instrumentation-keys"></a>Använd flera instrument knappar

Du kan skapa flera Application Insights-resurser och skicka olika data till var och en genom att använda deras respektive instrument knappar ("iKey").

 Till exempel:

```javascript
let appInsights = require("applicationinsights");

// configure auto-collection under one ikey
appInsights.setup("_ikey-A_").start();

// track some events manually under another ikey
let otherClient = new appInsights.TelemetryClient("_ikey-B_");
otherClient.trackEvent({name: "my custom event"});
```

## <a name="advanced-configuration-options"></a>Avancerade konfigurations alternativ

Klient-objektet innehåller en `config` egenskap med många valfria inställningar för avancerade scenarier. Dessa kan ställas in på följande sätt:

```javascript
client.config.PROPERTYNAME = VALUE;
```

De här egenskaperna är klient särskilda, så du kan konfigurera `appInsights.defaultClient` separat från klienter som skapats med `new appInsights.TelemetryClient()` .

| Egenskap                        | Beskrivning                                                                                                |
| ------------------------------- |------------------------------------------------------------------------------------------------------------|
| instrumentationKey              | En identifierare för din Application Insights-resurs.                                                      |
| endpointUrl                     | Inmatnings slut punkten för att skicka telemetri-nyttolaster till.                                                      |
| quickPulseHost                  | Live Metrics Stream-värden som du vill skicka telemetri om Live-mått till.                                            |
| proxyHttpUrl                    | En proxyserver för HTTP-trafik för SDK (valfritt, som standard hämtas från `http_proxy` miljövariabeln).     |
| proxyHttpsUrl                   | En proxyserver för HTTPS-trafik (valfritt, som standard hämtas från `https_proxy` miljövariabeln).   |
| httpAgent                       | En http. Agent som ska användas för SDK HTTP-trafik (valfritt, standardvärde är odefinierad).                                   |
| httpsAgent                      | En https. Agent som ska användas för SDK HTTPS-trafik (valfritt, standardvärde är odefinierad).                                 |
| maxBatchSize                    | Det maximala antalet telemetri-objekt som ska ingå i en nytto Last till inmatnings slut punkten (standard `250` ).   |
| maxBatchIntervalMs              | Den maximala tid det tar att vänta på en nytto last för att uppnå maxBatchSize (standard `15000` ).               |
| disableAppInsights              | En flagga som anger om telemetri-överföring är inaktive rad (standard `false` ).                                 |
| samplingPercentage              | Procent andelen telemetri objekt som har spårats som ska överföras (standard `100` ).                      |
| correlationIdRetryIntervalMs    | Vänte tiden innan ett nytt försök att hämta ID: t för Cross-Component-korrelation (standard `30000` ).     |
| correlationHeaderExcludedDomains| En lista över domäner som ska undantas från insprutning mellan komponent korrelations huvud (standard finns i [config. TS](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Config.ts)).|

## <a name="next-steps"></a>Nästa steg

* [Övervaka din telemetri i portalen](./overview-dashboard.md)
* [Skriv Analytics-frågor via din telemetri](../log-query/log-analytics-tutorial.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: ../faq.md
