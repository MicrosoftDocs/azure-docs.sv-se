---
title: Telemetri för Sök trafik analys
titleSuffix: Azure Cognitive Search
description: Aktivera Sök trafik analys för Azure Kognitiv sökning, samla in telemetri och händelser som initieras av användaren med hjälp av Application Insights och analysera sedan resultaten i en Power BI rapport.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/29/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 403426f7b166af16b4ef9cf1be0ae23ee03f8ae8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694027"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Samla in telemetridata för Sök trafik analys

Sök trafik analys är ett mönster för insamling av telemetri om användar interaktioner med ditt Azure Kognitiv sökning-program, till exempel användare som initieras genom att klicka på händelser och tangent bords indata. Med hjälp av den här informationen kan du fastställa effektiviteten hos din Sök lösning, inklusive populära Sök villkor, klickningar på genomklickning och vilka frågor som indata ger noll resultat.

Det här mönstret tar ett beroende på [Application Insights](../azure-monitor/app/app-insights-overview.md) (en funktion i [Azure Monitor](../azure-monitor/index.yml)) för att samla in användar data. Det kräver att du lägger till instrumentering till din klient kod enligt beskrivningen i den här artikeln. Slutligen behöver du en rapporterings mekanism för att analysera data. Vi rekommenderar Power BI, men du kan använda instrument panelen för programmet eller ett verktyg som ansluter till Application Insights.

> [!NOTE]
> Mönstret som beskrivs i den här artikeln är för avancerade scenarier och klick Ströms-data som genererats av kod som du lägger till i klienten. Tjänst loggar är däremot enkla att konfigurera, ange ett intervall med mått och kan göras i portalen utan att behöva kod. Aktivering av loggning rekommenderas för alla scenarier. Mer information finns i [samla in och analysera loggdata](search-monitor-logs.md).

## <a name="identify-relevant-search-data"></a>Identifiera relevanta Sök data

Om du vill ha användbara mått för Sök trafik analys, är det nödvändigt att logga vissa signaler från användarna av ditt sökprogram. Dessa signaler indikerar innehåll som användarna är intresserade av och som de anser relevanta. För Sök trafik analys ingår följande:

+ Användar genererade Sök händelser: endast Sök frågor som initieras av en användare är intressanta. Andra Sök begär Anden, till exempel de som används för att fylla ansikte eller hämta intern information, är inte viktiga. Se till att endast instrumenterade användar initierade händelser för att undvika skevning eller förskjutning i resultatet.

+ Händelser som genereras av användaren: på sidan Sök resultat innebär händelsen Klickning att ett dokument är ett relevant resultat för en speciell Sök fråga.

Genom att länka sökningen och klicka på händelser med ett korrelations-ID får du en djupare förståelse för hur väl programmets Sök funktioner fungerar.

## <a name="add-search-traffic-analytics"></a>Lägg till Sök trafik analys

På [Portal](https://portal.azure.com) sidan för din Azure kognitiv sökning-tjänst öppnar du sidan Sök trafikanalys för att få åtkomst till ett lathund-blad för följande telemetri-mönster. På den här sidan kan du välja eller skapa en Application Insights resurs, Hämta Instrumentation-nyckeln, kopiera kod avsnitt som du kan anpassa för din lösning och hämta en Power BI rapport som är byggd över schemat som visas i mönstret.

![Sidan Sök Trafikanalys i portalen](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Sidan Sök Trafikanalys i portalen")

## <a name="1---set-up-application-insights"></a>1 – konfigurera Application Insights

Välj en befintlig Application Insights resurs eller [skapa en](../azure-monitor/app/create-new-resource.md) om du inte redan har en. Om du använder sidan Sök Trafikanalys kan du kopiera Instrumentation-nyckeln som programmet behöver för att ansluta till Application Insights.

När du har en Application Insights resurs kan du följa [instruktionerna för språk och plattformar som stöds](../azure-monitor/app/platforms.md) för att registrera din app. Registreringen lägger helt enkelt till Instrumentation-nyckeln från Application Insights till din kod, som konfigurerar associationen. Du kan hitta nyckeln i portalen eller från sidan Sök Trafikanalys när du väljer en befintlig resurs.

En genväg som fungerar för vissa projekt typer av Visual Studio avspeglas i följande steg. Den skapar en resurs och registrerar din app på bara några få klick.

1. För Visual Studio-och ASP.net-utveckling öppnar du din lösning och väljer **projekt**  >  **Lägg till Application Insights Telemetry**.

1. Klicka på **Kom igång**.

1. Registrera din app genom att tillhandahålla en Microsoft-konto, en Azure-prenumeration och en Application Insights resurs (en ny resurs är standard). Klicka på **Registrera**.

I det här läget är ditt program konfigurerat för program övervakning, vilket innebär att alla sid inläsningar spåras med standard mått. Mer information om föregående steg finns i [aktivera Application Insights telemetri på Server sidan](../azure-monitor/app/asp-net-core.md#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="2---add-instrumentation"></a>2 – Lägg till instrumentering

I det här steget ska du skapa ett eget sökprogram med hjälp av Application Insights resurs som du skapade i steget ovan. Det finns fyra steg i den här processen som börjar med att skapa en telemetri-klient.

### <a name="step-1-create-a-telemetry-client"></a>Steg 1: skapa en telemetri-klient

Skapa ett objekt som skickar händelser till Application Insights. Du kan lägga till instrumentering på Server sidans program kod eller kod på klient sidan som körs i en webbläsare, uttryckt här som C#-och JavaScript-varianter (för andra språk, se den fullständiga listan över [plattformar och ramverk som stöds](../azure-monitor/app/platforms.md). Välj den metod som ger dig det önskade informations djupet.

Telemetri på Server Sidan samlar in mått på applikations nivå, till exempel i program som körs som en webb tjänst i molnet eller som en lokal app i ett företags nätverk. Telemetri på Server Sidan samlar in Sök och klickar på händelser, placeringen av ett dokument i resultat och fråga efter information, men data insamlingen kommer att begränsas till den information som är tillgänglig på det lagret.

På klienten kan du ha ytterligare kod som ändrar indata för frågor, lägger till navigering eller inkluderar kontext (till exempel frågor som initieras från en start sida respektive en produkt sida). Om detta beskriver din lösning kan du välja att använda instrumentering på klient sidan så att din telemetri visar ytterligare information. Hur den här ytterligare informationen samlas in utanför det här mönstrets omfång, men du kan granska [Application Insights för webb sidor](../azure-monitor/app/javascript.md#explore-browserclient-side-data) för mer riktning. 

**Använda C#**

För C# ska **InstrumentationKey** definieras i program konfigurationen, t. ex. appsettings.jspå om projektet är ASP.net. Gå tillbaka till registrerings anvisningarna om du är osäker på nyckel platsen.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
{
    _telemetryClient = telemetry;
}
```

**Använda JavaScript**

Det aktuella kodfragmentet (visas nedan) är version "5". versionen kodas i kodfragmentet som sa: "#" och den [aktuella versionen är också tillgänglig på GitHub](https://go.microsoft.com/fwlink/?linkid=2156318).

```html
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{nConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[C]&&!0!==s[C]){method="onerror",t(["_"+method]);var c=T[method];T[method]=function(e,t,n,a,i){var r=c&&c(e,t,n,a,i);return!0!==r&&m["_"+method]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);(T[t]=n).queue&&0===n.queue.length&&n.trackPageView({})}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
}});
</script>
```

> [!NOTE]
> För läsbarhet och för att minska eventuella JavaScript-fel visas alla möjliga konfigurations alternativ på en ny rad i kodfragments koden ovan, om du inte vill ändra värdet för en kommenterad linje kan den tas bort.


### <a name="step-2-request-a-search-id-for-correlation"></a>Steg 2: begär ett Sök-ID för korrelation

Om du vill korrelera Sök begär Anden med klick måste du ha ett korrelations-ID som relaterar till dessa två distinkta händelser. Azure Kognitiv sökning ger dig ett Sök-ID när du begär det med ett HTTP-huvud.

Med Sök-ID: t tillåts korrelation av mått som genereras av Azure Kognitiv sökning för själva begäran, med de anpassade mått som du loggar in Application Insights.

**Använda C# (nyare V11 SDK)**

Den senaste SDK: n kräver att en http-pipeline används för att ange rubriken enligt beskrivningen i det här [exemplet](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Pipeline.md#implementing-a-syncronous-policy).

```csharp
// Create a custom policy to add the correct headers
public class SearchIdPipelinePolicy : HttpPipelineSynchronousPolicy
{
    public override void OnSendingRequest(HttpMessage message)
    {
        message.Request.Headers.SetValue("x-ms-azs-return-searchid", "true");
    }
}
```

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Azure.Search.Documents

SearchClientOptions clientOptions = new SearchClientOptions();
clientOptions.AddPolicy(new SearchIdPipelinePolicy(), HttpPipelinePosition.PerCall);

var client = new SearchClient("<SearchServiceName>", "<IndexName>", new AzureKeyCredential("<QueryKey>"), options: clientOptions);

Response<SearchResults<SearchDocument>> response = await client.SearchAsync<SearchDocument>(searchText: searchText, searchOptions: options);
string searchId = string.Empty;
if (response.GetRawResponse().Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**Använda C# (äldre v10 SDK)**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>));

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**Använda Java Script (anropa REST-API: er)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>Steg 3: Logga Sök händelser

Varje gång en sökbegäran utfärdas av en användare bör du logga in som en Sök händelse med följande schema i en Application Insights anpassad händelse. Kom ihåg att endast logga in användardefinierade Sök frågor.

+ **SearchServiceName**: (sträng) Sök tjänst namn
+ **SearchId**: (GUID) unik identifierare för Sök frågan (kommer i Sök svaret)
+ **IndexName**: (sträng) Sök tjänst index som ska frågas
+ **QueryTerms**: (sträng) Sök termer som anges av användaren
+ **Resultcount som**: (int) antal dokument som returnerades (kommer i Sök svaret)
+ **ScoringProfile**: (sträng) namnet på den bedömnings profil som används, om något

> [!NOTE]
> Begär antalet användardefinierade frågor genom att lägga till $count = true i Sök frågan. Mer information finns i [Sök efter dokument (rest)](/rest/api/searchservice/search-documents#query-parameters).
>

**Använda C#**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
};
_telemetryClient.TrackEvent("Search", properties);
```

**Använda JavaScript**

```javascript
appInsights.trackEvent("Search", {
  SearchServiceName: <service name>,
  SearchId: <search id>,
  IndexName: <index name>,
  QueryTerms: <search terms>,
  ResultCount: <results count>,
  ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>Steg 4: Logga klicknings händelser

Varje gång en användare klickar på ett dokument, är det en signal som måste loggas för söknings analys. Använd Application Insights anpassade händelser för att logga dessa händelser med följande schema:

+ **ServiceName**: (sträng) Sök tjänst namn
+ **SearchId**: (GUID) unik identifierare för den relaterade Sök frågan
+ **Fulltextdokument**: (sträng) dokument-ID
+ **Position**: (int) rangordning av dokumentet på sidan Sök Resultat

> [!NOTE]
> Position syftar på kardinal ordningen i ditt program. Du är kostnads fri att ange det här talet, så länge det alltid är detsamma, för att möjliggöra jämförelse.
>

**Använda C#**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
};
_telemetryClient.TrackEvent("Click", properties);
```

**Använda JavaScript**

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3-analysera i Power BI

När du har instrumenterat appen och kontrollerat att programmet är korrekt anslutet till Application Insights, laddar du ned en fördefinierad rapportmall för att analysera data i Power BI Desktop. Rapporten innehåller fördefinierade diagram och tabeller som är användbara för att analysera ytterligare data som registrerats för Sök trafik analys.

1. Klicka på **Sök trafik analys** under **inställningar** i Azure kognitiv sökning instrument panelens vänstra navigerings fönster.

1. På sidan **Sök trafik analys** går du till steg 3 och klickar på **Hämta Power BI Desktop** för att installera Power BI.

   ![Hämta Power BI rapporter](./media/search-traffic-analytics/get-use-power-bi.png "Hämta Power BI rapporter")

1. På samma sida klickar du på **hämta Power BI rapporten**.

1. Rapporten öppnas i Power BI Desktop och du uppmanas att ansluta till Application Insights och ange autentiseringsuppgifter. Du kan hitta anslutnings information i Azure Portals sidor för din Application Insights-resurs. Ange samma användar namn och lösen ord som du använder för Portal inloggning för autentiseringsuppgifter.

   ![Anslut till Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Anslut till Application Insights")

1. Klicka på **Läs in**.

Rapporten innehåller diagram och tabeller som hjälper dig att fatta mer välgrundade beslut för att förbättra Sök prestanda och relevans.

Måtten omfattar följande objekt:

+ Sök efter volym och vanligaste term dokument par: termer som resulterar i samma dokument som du klickar på, sorteras efter klickningar.
+ Sökningar utan klickningar: villkor för de vanligaste frågorna som registrerar inga klick

Följande skärm bild visar hur en inbyggd rapport kan se ut om du har använt alla schema element.

![Power BI instrument panel för Azure Kognitiv sökning](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Power BI instrument panel för Azure Kognitiv sökning")

## <a name="next-steps"></a>Nästa steg

Instrumentera ditt sökprogram för att få kraftfull och insiktad information om din Sök tjänst.

Du hittar mer information om [Application Insights](../azure-monitor/app/app-insights-overview.md) och besöker sidan med [priser](https://azure.microsoft.com/pricing/details/application-insights/) för att lära dig mer om deras olika tjänst nivåer.

Lär dig mer om att skapa fantastiska rapporter. Mer information finns i [komma igång med Power BI Desktop](/power-bi/fundamentals/desktop-getting-started) .
