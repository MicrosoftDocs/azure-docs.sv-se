---
title: Anslutnings strängar i Azure Application Insights | Microsoft Docs
description: Så här använder du anslutnings strängar.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.custom: devx-track-js, devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: df87b060423aeff9fa5f83f21634395fe30e0bbb
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486292"
---
# <a name="connection-strings"></a>Anslutningssträngar

## <a name="overview"></a>Översikt

Anslutnings strängar ger program insikter användare med en enda konfigurations inställning, vilket eliminerar behovet av flera proxyinställningar. Mycket användbart för webb servrar i intranät, suveräna eller hybrid moln miljöer som vill skicka data till övervaknings tjänsten.

Nyckel värdes paren ger ett enkelt sätt för användare att definiera en kombination av prefixet prefix för varje Application Insights (AI)-tjänst/produkt.

> [!IMPORTANT]
> Vi rekommenderar inte att du anger både anslutnings sträng och instrument nyckel. I händelse av att en användare anger båda, prioriteras det som angetts senast. 


## <a name="scenario-overview"></a>Översikt över scenario 

Kund scenarier där vi visualiserar detta med största påverkan:

- Brand Väggs undantag eller omdirigering av proxy 

    I de fall där övervakning av intranäts webb server krävs, uppmanade vi våra tidigare lösningar att lägga till enskilda tjänst slut punkter i konfigurationen. Mer information finns [här](../faq.md#can-i-monitor-an-intranet-web-server). 
    Anslutnings strängar ger ett bättre alternativ genom att minska den här ansträngningen till en enda inställning. Ett enkelt prefix innebär att suffixet ändras tillåter automatisk ifyllning och omdirigering av alla slut punkter till rätt tjänster. 

- Suveräna miljöer eller hybrid moln miljöer

    Användare kan skicka data till en definierad [Azure Government Region](../../azure-government/compare-azure-government-global-azure.md#application-insights).
    Med anslutnings strängar kan du definiera slut punkts inställningar för dina intranät servrar eller hybrid moln inställningar. 

## <a name="getting-started"></a>Komma igång

### <a name="finding-my-connection-string"></a>Hittar du min anslutnings sträng?

Anslutnings strängen visas på bladet översikt i Application Insights resursen.

![bladet anslutnings sträng på Översikt](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>Schema

#### <a name="max-length"></a>Högsta längd

Anslutningen har en maximal längd på 4096 tecken som stöds.

#### <a name="key-value-pairs"></a>Nyckel/värde-par

Anslutnings strängen består av en lista med inställningar som representeras som nyckel/värde-par avgränsade med semikolon: `key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>Syntax

- `InstrumentationKey` (t. ex.: 00000000-0000-0000-0000-000000000000)  Anslutnings strängen är ett **obligatoriskt** fält.
- `Authorization` (t. ex.: iKey) (Den här inställningen är valfri eftersom vi bara har stöd för iKey-auktorisering i dag.)
- `EndpointSuffix` (t. ex.: applicationinsights.azure.cn) Genom att ställa in Endpoint-suffixet instruerar du SDK: n som Azure-molnet ska ansluta till. SDK monterar resten av slut punkten för enskilda tjänster.
- Explicita slut punkter.
  Alla tjänster kan åsidosättas explicit i anslutnings strängen.
   - `IngestionEndpoint` (t. ex.: `https://dc.applicationinsights.azure.com` )
   - `LiveEndpoint` (t. ex.: `https://live.applicationinsights.azure.com` )
   - `ProfilerEndpoint` (t. ex.: `https://profiler.monitor.azure.com` )
   - `SnapshotEndpoint` (t. ex.: `https://snapshot.monitor.azure.com` )

#### <a name="endpoint-schema"></a>Slut punkts schema

`<prefix>.<suffix>`
- Prefix: definierar en tjänst. 
- Suffix: definierar det gemensamma domän namnet.

##### <a name="valid-suffixes"></a>Giltiga suffix

Här är en lista över giltiga suffix 
- applicationinsights.azure.cn
- applicationinsights.us


Se även: https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>Giltiga prefix

- Inmatning av [telemetri](./app-insights-overview.md):`dc`
- [Live-mått](./live-stream.md): `live`
- [Profiler](./profiler-overview.md): `profiler`
- [Ögonblicks bild](./snapshot-debugger.md): `snapshot`



## <a name="connection-string-examples"></a>Exempel på anslutnings strängar


### <a name="minimal-valid-connection-string"></a>Minimal giltig anslutnings sträng

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

I det här exemplet har endast Instrumentation-nyckeln angetts.

- Authorization Scheme-standardvärdet till "iKey" 
- Instrumentation-nyckel: 00000000-0000-0000-0000-000000000000
- De regionala tjänst-URI: erna baseras på [SDK-standardvärdena](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs) och kommer att ansluta till den offentliga globala Azure:
   - Inmatning `https://dc.services.visualstudio.com/`
   - Live-mått: `https://rt.services.visualstudio.com/`
   - Profiler `https://profiler.monitor.azure.com/`
   - Fel sökare `https://snapshot.monitor.azure.com/`



### <a name="connection-string-with-endpoint-suffix"></a>Anslutnings sträng med Endpoint-suffix

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

I det här exemplet anger den här anslutnings strängen slut punktens suffix och SDK skapar tjänstens slut punkter.

- Authorization Scheme-standardvärdet till "iKey" 
- Instrumentation-nyckel: 00000000-0000-0000-0000-000000000000
- De regionala tjänst-URI: erna baseras på angivet slut punkts suffix: 
   - Inmatning `https://dc.ai.contoso.com`
   - Live-mått: `https://live.ai.contoso.com`
   - Profiler `https://profiler.ai.contoso.com`
   - Fel sökare `https://snapshot.ai.contoso.com`  



### <a name="connection-string-with-explicit-endpoint-overrides"></a>Anslutnings sträng med explicit slut punkts åsidosättningar 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

I det här exemplet anger den här anslutnings strängen explicita åsidosättningar för varje tjänst. SDK använder de exakta slut punkter som tillhandahålls utan ändringar.

- Authorization Scheme-standardvärdet till "iKey" 
- Instrumentation-nyckel: 00000000-0000-0000-0000-000000000000
- De regionala tjänst-URI: erna baseras på de explicita värdena för åsidosättning: 
   - Inmatning `https://custom.com:111/`
   - Live-mått: `https://custom.com:222/`
   - Profiler `https://custom.com:333/`
   - Fel sökare `https://custom.com:444/`  


## <a name="how-to-set-a-connection-string"></a>Ange en anslutnings sträng

Anslutnings strängar stöds i följande SDK-versioner:
- .NET-och .NET Core v-2.12.0
- Java v-2.5.1 och Java 3,0
- Java Script v-2.3.0
- NodeJS v-1.5.0
- Python v-1.0.0

En anslutnings sträng kan anges antingen i kod, i en miljö variabel eller i en konfigurations fil.



### <a name="environment-variable"></a>Miljövariabel

- Anslutnings sträng: `APPLICATIONINSIGHTS_CONNECTION_STRING`

### <a name="code-samples"></a>Kodexempel

# <a name="netnetcore"></a>[.NET/. NetCore](#tab/net)

Ange egenskapen [TelemetryConfiguration. ConnectionString](https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274) eller [ApplicationInsightsServiceOptions. ConnectionString](https://github.com/microsoft/ApplicationInsights-dotnet/blob/81288f26921df1e8e713d31e7e9c2187ac9e6590/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs#L66-L69)

.NET har uttryckligen angetts:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

.NET-konfigurations fil:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```

NetCore explicit uppsättning:
```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions { ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;" };
    services.AddApplicationInsightsTelemetry(options: options);
}
```

NetCore-config.jspå: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)


Java (v 2.5. x) explicit angiven:
```java
TelemetryConfiguration.getActive().setConnectionString("InstrumentationKey=00000000-0000-0000-0000-000000000000");
```

ApplicationInsights.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000;</ConnectionString>
</ApplicationInsights>
```

# <a name="javascript"></a>[JavaScript](#tab/js)

Viktigt: Java Script stöder inte användning av miljövariabler.

Använda kodfragmentet:

Det aktuella kodfragmentet (visas nedan) är version "5". versionen kodas i kodfragmentet som sa: "#" och den [aktuella versionen är också tillgänglig på GitHub](https://go.microsoft.com/fwlink/?linkid=2156318).

```html
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}});
</script>
```

> [!NOTE]
> För läsbarhet och för att minska eventuella JavaScript-fel visas alla möjliga konfigurations alternativ på en ny rad i kodfragments koden ovan, om du inte vill ändra värdet för en kommenterad linje kan den tas bort.

Manuell installation:
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

# <a name="python"></a>[Python](#tab/python)

Vi rekommenderar att användarna anger miljövariabeln.

Ange anslutnings strängen explicit:

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```

---

## <a name="next-steps"></a>Nästa steg

Kom igång under körningsfasen med:

* [Virtuella Azure-datorer och skalnings uppsättningar i IIS-värdbaserade appar](./azure-vm-vmss-apps.md)
* [IIS-server](./monitor-performance-live-website-now.md)
* [Azure Web Apps](./azure-web-apps.md)

Kom igång under utvecklingsfasen med:

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-get-started.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)

