---
title: Azure Application insikter om JavaScript-webbappar
description: Hämta sid visning och antal sessioner, webb klient data, enstaka sid program (SPA) och spåra användnings mönster. Identifiera undantag och prestandaproblem på JavaScript-baserade webbsidor.
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: devx-track-js
ms.openlocfilehash: 04cda044b002e226c49f8647d4705d7c0f2a514e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565273"
---
# <a name="application-insights-for-web-pages"></a>Application Insights för webbsidor

Visa prestanda och användning för webbsidor eller appar. Om du lägger till [Application Insights](app-insights-overview.md) till sid skriptet får du tids inställningar för sid inläsningar och AJAX-anrop, antal och information om webb läsar undantag och AJAX-fel, samt antal användare och sessioner. Allt detta kan visas efter sida, klientoperativsystem- och webbläsarversion, geografisk plats och andra dimensioner. Du kan ställa in varningar för antal fel eller långsam sidinläsning. Och genom att infoga spårning av anrop i JavaScript-kod kan du spåra hur olika funktioner i ditt webbsideprogram används.

Application Insights kan användas med alla webbsidor – du lägger bara till ett stycke JavaScript-kod. Om din webb tjänst är [Java](java-get-started.md) eller [ASP.net](asp-net.md)kan du använda SDK: er för Server sidan tillsammans med SDK SDK för klient sidan för att få en heltäckande förståelse för appens prestanda.

## <a name="adding-the-javascript-sdk"></a>Lägga till JavaScript SDK

> [!IMPORTANT]
> Nya Azure-regioner **kräver** att anslutnings strängar används i stället för instrument knappar. [Anslutnings strängen](./sdk-connection-string.md?tabs=js) identifierar den resurs som du vill associera dina telemetridata med. Du kan också ändra de slut punkter som resursen kommer att använda som mål för din telemetri. Du måste kopiera anslutnings strängen och lägga till den i programmets kod eller till en miljö variabel.

1. Först behöver du en Application Insights-resurs. Om du inte redan har en resurs-och Instrumentation-nyckel följer du anvisningarna för att [skapa en ny resurs](create-new-resource.md).
2. Kopiera _Instrumentation-nyckeln_ (även kallat "iKey") eller [anslutnings sträng](#connection-string-setup) för den resurs där du vill att din JavaScript-telemetri ska skickas (från steg 1.) Du kommer att lägga till den i `instrumentationKey` eller- `connectionString` inställningen för Application Insights JavaScript SDK.
3. Lägg till Application Insights JavaScript SDK till din webb sida eller app via något av följande två alternativ:
    * [NPM-installation](#npm-based-setup)
    * [JavaScript-kodfragment](#snippet-based-setup)

> [!IMPORTANT]
> Använd bara en metod för att lägga till Java Script SDK i ditt program. Om du använder installations programmet för NPM ska du inte använda kodfragmentet och vice versa.

> [!NOTE]
> NPM-installationen installerar JavaScript SDK som ett beroende av ditt projekt, vilket aktiverar IntelliSense, medan kodfragmentet hämtar SDK vid körning. Båda har stöd för samma funktioner. Men utvecklare som vill ha mer anpassade händelser och konfiguration brukar välja NPM-installation, medan användare söker efter snabb aktivering av den färdiga Web Analytics-valet för kodfragmentet.

### <a name="npm-based-setup"></a>NPM-baserad installation

Installera via NPM.

```sh
npm i --save @microsoft/applicationinsights-web
```

> [!Note]
> **Inmatning ingår i det här paketet**, så du behöver **inte** installera ett separat Skriv paket.
    
```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>Kodfragment-baserad installation

Om din app inte använder NPM kan du direkt Instrumenta dina webb sidor med Application Insights genom att klistra in det här kodfragmentet överst på varje sida. Helst bör det vara det första skriptet i `<head>` avsnittet så att det kan övervaka eventuella eventuella problem med alla beroenden och eventuellt eventuella JavaScript-fel. Om du använder en program vara från en blixt Server lägger du till kodfragmentet överst i filen `_Host.cshtml` i `<head>` avsnittet.

För att hjälpa till med att spåra vilken version av kodfragmentet som ditt program använder, kommer 2.5.5 att innehålla den nya taggen "AI. Internal. fragment" som kommer att innehålla den identifierade kodfragments versionen, från och med version.

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
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    /* ...Other Configuration Options... */
}});
</script>
```

> [!NOTE]
> För läsbarhet och för att minska eventuella JavaScript-fel visas alla möjliga konfigurations alternativ på en ny rad i kodfragments koden ovan, om du inte vill ändra värdet för en kommenterad linje kan den tas bort.


#### <a name="reporting-script-load-failures"></a>Rapportera skript inläsnings problem

Den här versionen av kodfragmentet identifierar och rapporterar fel vid inläsning av SDK: n från CDN som ett undantag till Azure Monitor portalen (under fel söknings listan för fel &gt; &gt; ). detta undantag ger insyn i fel av den här typen så att du är medveten om att programmet inte rapporterar telemetri (eller andra undantag) som förväntat. Den här signalen är en viktig mätning i att du har förlorat telemetri eftersom SDK inte lästs in eller initieras, vilket kan leda till:
- Under rapportering av hur användare använder (eller försöker använda) din webbplats;
- Telemetri saknas för hur dina användare använder din webbplats.
- JavaScript-fel som saknas och som kan hindra dina slutanvändare från att lyckas använda din webbplats.

Mer information om det här undantaget finns på sidan för fel sökning av [SDK-inläsning](javascript-sdk-load-failure.md) .

Vid rapportering av det här felet som ett undantag till portalen används inte konfigurations alternativet ```disableExceptionTracking``` från Application Insights-konfigurationen. om felet inträffar rapporteras det alltid av kodfragmentet, även om fönstrets OnError-stöd är inaktiverat.

Rapportering av SDK-inläsnings problem stöds särskilt inte i IE 8 (eller mindre). Detta hjälper till att minska minified storlek genom att under förutsättning att de flesta miljöer inte är enbart IE 8 eller mindre. Om du har det här kravet och du vill ta emot dessa undantag måste du antingen ta med en Fetch-Poly fyllning eller skapa en egen version av kodfragmentet som använder ```XDomainRequest``` i stället för ```XMLHttpRequest``` , vi rekommenderar att du använder den [angivna käll koden för kodfragmentet](https://github.com/microsoft/ApplicationInsights-JS/blob/master/AISKU/snippet/snippet.js) som en start punkt.

> [!NOTE]
> Om du använder en tidigare version av kodfragmentet rekommenderar vi starkt att du uppdaterar till den senaste versionen så att du får de tidigare uppdelade problemen.

#### <a name="snippet-configuration-options"></a>Konfigurations alternativ för kodfragment

Alla konfigurations alternativ har nu flyttats till slutet av skriptet för att undvika att de råkar ut för JavaScript-fel som inte bara skulle orsaka att SDK: n inte kan läsas in, men även inaktivera rapportering av felet.

Varje konfigurations alternativ visas på en ny rad, om du inte vill åsidosätta standardvärdet för ett objekt som anges som [valfritt], kan du ta bort raden för att minimera den resulterande storleken på din returnerade sida.

De tillgängliga konfigurations alternativen är
 
| Namn | Typ | Beskrivning
|------|------|----------------
| src | sträng **[obligatoriskt]** | Den fullständiga URL: en för varifrån SDK: n ska läsas in. Det här värdet används för attributet src för ett dynamiskt tillagd &lt; skript/ &gt; tagg. Du kan använda den offentliga CDN-platsen eller en privat värd som är värd för en.
| name | sträng *[valfritt]* | Det globala namnet för den initierade SDK: n som standard `appInsights` . Det ```window.appInsights``` kommer att vara en referens till den initierade instansen. Obs! Om du anger ett namn värde eller om en föregående instans ska tilldelas (via det globala namnet appInsightsSDK), kommer detta namn värde också att definieras i det globala namn området som ```window.appInsightsSDK=<name value>``` , detta krävs av SDK-initierings koden för att säkerställa att den initierar och uppdaterar rätt kodfragments Skeleton och proxyservrar.
| LD | antal i MS *[valfritt]* | Definierar inläsnings fördröjning innan du försöker läsa in SDK. Standardvärdet är 0ms och ett negativt värde kommer omedelbart att lägga till en skript tagg till &lt; &gt; sidans huvud område, som sedan blockerar händelsen för sid inläsning tills skriptet läses in (eller Miss lyckas).
| useXhr | boolesk *[valfritt]* | Den här inställningen används endast för inläsnings problem med repor ting SDK. Om du väljer att inte använda Fetch () försöker du först använda Fetch () om det är tillgängligt och sedan återgår till XHR. om värdet anges till sant kringgås bara hämtnings kontrollen. Användning av det här värdet krävs bara om programmet används i en miljö där hämtningen Miss lyckas med att skicka misslyckade händelser.
| crossOrigin | sträng *[valfritt]* | Genom att inkludera den här inställningen inkluderar den skript kod som lagts till för att ladda ned SDK crossOrigin-attributet med det här strängvärdet. När det inte har definierats (standard) läggs inget crossOrigin-attribut till. Rekommenderade värden har inte definierats (standard). ""; eller "Anonym" (för alla giltiga värden se [HTML-attribut `crossorigin` :](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin) dokumentation)
| konfiguration | objekt **[obligatoriskt]** | Konfigurationen som skickas till Application Insights SDK under initieringen.

### <a name="connection-string-setup"></a>Konfiguration av anslutnings sträng

För antingen NPM-eller kodfragment-installationen kan du också konfigurera din instans av Application Insights med hjälp av en anslutnings sträng. Ersätt bara `instrumentationKey` fältet med `connectionString` fältet.
```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'YOUR_CONNECTION_STRING_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Skicka telemetri till Azure Portal

Som standard samlar Application Insights JavaScript SDK automatiskt in ett antal telemetridata som är användbara för att fastställa hälso tillståndet för ditt program och den underliggande användar upplevelsen. Dessa omfattar:

- Ej **fångade undantag** i appen, inklusive information om
    - Stack spårning
    - Undantags information och meddelande som medföljer felet
    - Rad & kolumn antal fel
    - URL där ett fel genererades
- Begär Anden om **nätverks beroenden** som görs av appen **XHR** och **hämtningen** (hämtnings samlingen är inaktive rad som standard) begär Anden, inkludera information om
    - URL för beroende källa
    - Kommando & metod som används för att begära beroendet
    - Varaktighet för begäran
    - Resultat kod och lyckad status för begäran
    - ID (om det finns) för användaren som gör begäran
    - Korrelations kontext (om det finns någon) där begäran görs
- **Användar information** (till exempel plats, nätverk, IP)
- **Enhets information** (till exempel webbläsare, OS, version, språk, modell)
- **Sessionsinformation**

### <a name="telemetry-initializers"></a>Telemetri-initierare
Telemetri initierare används för att ändra innehållet i insamlad telemetri innan det skickas från användarens webbläsare. De kan också användas för att förhindra att vissa telemetri skickas, genom att returnera `false` . Du kan lägga till flera telemetri-initierare i Application Insights-instansen och de körs när du lägger till dem.

Indataargumentet till `addTelemetryInitializer` är ett motanrop som tar ett [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) argument och returnerar ett `boolean` eller `void` . Om det returneras, `false` skickas inte objektet telemetri, annars går det vidare till nästa telemetri-initierare, om sådana finns, eller skickas till slut punkten för telemetri-samlingen.

Ett exempel på att använda telemetri-initierare:
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```

## <a name="configuration"></a>Konfiguration
De flesta konfigurations fälten får ett namn som är förfalskade som standard. Alla fält är valfria förutom för `instrumentationKey` .

| Name | Beskrivning | Standardvärde |
|------|-------------|---------|
| instrumentationKey | **Obligatoriskt**<br>Instrumentation-nyckel som du fick från Azure Portal. | sträng<br/>null |
| accountId | Ett valfritt konto-ID, om din app grupperar användare till konton. Inga blank steg, kommatecken, semikolon, likheter eller lodräta staplar | sträng<br/>null |
| sessionRenewalMs | En session loggas om användaren är inaktiv under den här tiden i millisekunder. | numeric<br/>1800000<br/>(30 minuter) |
| sessionExpirationMs | En session loggas om den fortsätter under den här tiden i millisekunder. | numeric<br/>86400000<br/>(24 timmar) |
| maxBatchSizeInBytes | Max storlek för telemetri batch. Om en batch överskrider den här gränsen skickas den omedelbart och en ny batch startas | numeric<br/>10000 |
| maxBatchInterval | Hur lång tid det tar att gruppera telemetri innan det skickas (millisekunder) | numeric<br/>15 000 |
| Inaktivera&#8203;ExceptionTracking | Om det här värdet är sant samlas undantag inte in igen. | boolean<br/> falskt |
| disableTelemetry | Om det här värdet är sant samlas ingen telemetri in eller skickas. | boolean<br/>falskt |
| enableDebug | Om det här värdet är sant genereras **interna** fel söknings data som ett undantag **i stället** för att loggas, oavsett inställningarna för SDK-loggning. Standardvärdet är false. <br>**_Obs:_** Om du aktiverar den här inställningen tas all telemetri bort när ett internt fel inträffar. Detta kan vara användbart för att snabbt identifiera problem med konfigurationen eller användningen av SDK. Om du inte vill förlora telemetri vid fel sökning kan du överväga att använda `consoleLoggingLevel` eller `telemetryLoggingLevel` i stället för `enableDebug` . | boolean<br/>falskt |
| loggingLevelConsole | Loggar **interna** Application Insights fel i konsolen. <br>0: av, <br>1: endast kritiska fel, <br>2: allt (fel & varningar) | numeric<br/> 0 |
| loggingLevelTelemetry | Skickar **interna** Application Insights fel som telemetri. <br>0: av, <br>1: endast kritiska fel, <br>2: allt (fel & varningar) | numeric<br/> 1 |
| diagnosticLogInterval | inhemska Avsöknings intervall (i MS) för intern loggnings kön | numeric<br/> 10000 |
| samplingPercentage | Procent andel av händelser som ska skickas. Standardvärdet är 100, vilket innebär att alla händelser skickas. Ange detta om du vill bevara din data Kap för storskaliga program. | numeric<br/>100 |
| autoTrackPageVisitTime | Om värdet är true, på en sid visningar, spåras och skickas den föregående instrumenterade sidans visnings tid och skickas som telemetri och en ny timer startas för den aktuella sid visningar. | boolean<br/>falskt |
| disableAjaxTracking | Om värdet är true samlas inga AJAX-anrop in. | boolean<br/> falskt |
| disableFetchTracking | Om det här värdet är sant samlas inga hämtnings förfrågningar in.|boolean<br/>true |
| overridePageViewDuration | Om värdet är true ändras standard beteendet för trackPageView till post end för sid visningens varaktighets intervall när trackPageView anropas. Om värdet är false och ingen anpassad varaktighet anges för trackPageView, beräknas sid visningens prestanda med hjälp av API: t för navigering. |boolean<br/>
| maxAjaxCallsPerView | Standard 500-styr hur många AJAX-anrop som ska övervakas per sid visning. Ange till-1 om du vill övervaka alla (obegränsat) AJAX-anrop på sidan. | numeric<br/> 500 |
| disableDataLossAnalysis | Om det här värdet är falskt kontrol leras den interna telemetri-buffertarna vid start för objekt som ännu inte har skickats. | boolean<br/> true |
| Inaktivera&#8203;CorrelationHeaders | Om värdet är False kommer SDK att lägga till två huvuden ("Request-ID" och "Request-context") till alla beroende begär Anden för att korrelera dem med motsvarande begär Anden på Server sidan. | boolean<br/> falskt |
| correlationHeader&#8203;ExcludedDomains | Inaktivera korrelations rubriker för vissa domäner | sträng []<br/>Odefinierad |
| correlationHeader&#8203;ExcludePatterns | Inaktivera korrelations rubriker med reguljära uttryck | regex []<br/>Odefinierad |
| correlationHeader&#8203;domäner | Aktivera korrelations rubriker för vissa domäner | sträng []<br/>Odefinierad |
| disableFlush&#8203;OnBeforeUnload | Om värdet är true anropas inte Flush-metoden när onBeforeUnload event triggers | boolean<br/> falskt |
| enableSessionStorageBuffer | Om värdet är true lagras bufferten med all telemetri som inte har skickats i session Storage. Bufferten återställs vid sid inläsning | boolean<br />true |
| cookieCfg | Standardinställningar för användning av cookies är aktiverat se [ICookieCfgConfig](#icookiemgrconfig) -inställningar för fullständiga standardvärden. | [ICookieCfgConfig](#icookiemgrconfig)<br>(Sedan 2.6.0)<br/>Odefinierad |
| ~~isCookieUseDisabled~~<br>disableCookiesUsage | Om värdet är true kommer SDK inte att lagra eller läsa data från cookies. Observera att detta inaktiverar cookies från användare och session och återger användnings blad och erfarenheter. isCookieUseDisable har ersatts av disableCookiesUsage, när båda anges disableCookiesUsage prioriteras.<br>(Sedan v-2.6.0) Och om `cookieCfg.enabled` också har definierats prioriteras det för dessa värden, och användningen av cookies kan aktive ras igen efter initieringen via Core. getCookieMgr (). SetEnabled (true). | alias för [`cookieCfg.enabled`](#icookiemgrconfig)<br>falskt |
| cookieDomain | Anpassad cookie-domän. Detta är användbart om du vill dela Application Insights cookies över under domäner.<br>(Sedan v-2.6.0) Om `cookieCfg.domain` har definierats prioriteras det här värdet. | alias för [`cookieCfg.domain`](#icookiemgrconfig)<br>null |
| cookiePath | Anpassad cookie-sökväg. Detta är användbart om du vill dela Application Insights cookies bakom en Programgateway.<br>Om `cookieCfg.path` har definierats prioriteras det här värdet. | alias för [`cookieCfg.path`](#icookiemgrconfig)<br>(Sedan 2.6.0)<br/>null |
| isRetryDisabled | Om det här värdet är falskt försöker du igen på 206 (delvis utfört), 408 (timeout), 429 (för många begär Anden), 500 (internt Server fel), 503 (tjänsten är inte tillgänglig) och 0 (offline, endast om det har identifierats) | boolean<br/>falskt |
| isStorageUseDisabled | Om värdet är true kommer SDK inte att lagra eller läsa data från lokal lagring och sessionstoken. | boolean<br/> falskt |
| isBeaconApiDisabled | Om det här värdet är falskt skickar SDK all telemetri med hjälp av [Beacon-API: et](https://www.w3.org/TR/beacon) | boolean<br/>true |
| onunloadDisableBeacon | När fliken är stängd skickar SDK all återstående telemetri med hjälp av Beacon- [API: et](https://www.w3.org/TR/beacon) | boolean<br/> falskt |
| sdkExtension | Anger namnet på SDK-tillägget. Endast alfabetiska tecken tillåts. Tilläggs namnet läggs till som ett prefix till taggen AI. Internal. sdkVersion (till exempel ext_javascript: 2.0.0). | sträng<br/> null |
| isBrowserLink&#8203;TrackingEnabled | Om värdet är true, kommer SDK att spåra alla förfrågningar om [webb läsar länkar](/aspnet/core/client-side/using-browserlink) . | boolean<br/>falskt |
| appId | AppId används för korrelationen mellan AJAX-beroenden på klient sidan med begär Anden på Server sidan. När Beacon-API är aktiverat kan det inte användas automatiskt, men det kan ställas in manuellt i konfigurationen. |sträng<br/> null |
| Aktivera&#8203;CorsCorrelation | Om värdet är true, kommer SDK att lägga till två huvuden ("Request-ID" och "Request-context") till alla CORS-begäranden för att korrelera utgående AJAX-beroenden med motsvarande begär Anden på Server sidan. | boolean<br/>falskt |
| namePrefix | Ett valfritt värde som ska användas som namn postfix för localStorage och cookie-namn. | sträng<br/>Odefinierad |
| Aktivera&#8203;AutoRoute&#8203;spårning | Spåra automatiskt väg ändringar i en enskild sida (SPA). Om värdet är true skickar varje väg ändring en ny sid visningar till Application Insights. Ändringar av hash-vägar ( `example.com/foo#bar` ) registreras också som nya sid visningar.| boolean<br/>falskt |
| enableRequest&#8203;HeaderTracking | Om värdet är true spåras AJAX-& Hämta begärandehuvuden. | boolean<br/> falskt |
| enableResponse&#8203;HeaderTracking | Om värdet är true spåras svars rubriker för AJAX-& Hämta. | boolean<br/> falskt |
| distributedTracingMode | Ställer in läget för distribuerad spårning. Om AI_AND_W3C läge eller W3C-läge är inställt, genereras W3C trace context-rubriker (traceparent/tracestate) och tas med i alla utgående begär Anden. AI_AND_W3C tillhandahålls för bakåtkompatibilitet med alla äldre Application Insights instrumenterade tjänster. Se exemplet [här](./correlation.md#enable-w3c-distributed-tracing-support-for-web-apps).| `DistributedTracingModes`eller<br/>numeric<br/>(Sedan v-2.6.0) `DistributedTracingModes.AI_AND_W3C`<br />(v-2.5.11 eller tidigare) `DistributedTracingModes.AI` |
| Aktivera&#8203;AjaxErrorStatusText | Om värdet är true, inkludera data text för svars fel i beroende händelse vid misslyckade AJAX-begäranden. | boolean<br/> falskt |
| Aktivera&#8203;AjaxPerfTracking |Flagga för att aktivera sökning efter och inklusive ytterligare ett webbläsarfönster. prestanda tider i rapporterade `ajax` (XHR och Fetch) mått. | boolean<br/> falskt |
| maxAjaxPerf&#8203;LookupAttempts | Det maximala antalet gånger som ska sökas efter fönstret. prestanda tider (om det är tillgängligt), krävs detta eftersom inte alla webbläsare fyller i fönstret. prestanda innan XHR-begäran rapporteras och för hämtnings begär Anden läggs till efter dess slut.| numeric<br/> 3 |
| ajaxPerfLookupDelay | Vänte tiden innan ett nytt försök att hitta Windows. prestanda tids inställningarna för en `ajax` begäran visas i millisekunder och skickas direkt till setTimeout (). | numeric<br/> 25 MS |
| enableUnhandled&#8203;PromiseRejection&#8203;spårning | Om värdet är true, kommer ohanterade löftes avslag att samlas in automatiskt och rapporteras som ett JavaScript-fel. När disableExceptionTracking är true (spåra inte undantag) ignoreras konfiguration svärdet och ohanterade löftes avslag rapporteras inte. | boolean<br/> falskt |
| Inaktivera&#8203;InstrumentationKey&#8203;verifiering | Om värdet är true kringgås kontroll av Instrumentation-nycklar. | boolean<br/>falskt |
| enablePerfMgr | När aktive rad (true) skapar den lokala perfEvents för kod som har instrumenterats för att generera perfEvents (via doPerf ()-hjälpen). Detta kan användas för att identifiera prestanda problem inom SDK: n baserat på din användning eller alternativt i din egen instrumenterade kod. [Mer information finns i den grundläggande dokumentationen](https://github.com/microsoft/ApplicationInsights-JS/blob/master/docs/PerformanceMonitoring.md). Sedan v-2.5.7 | boolean<br/>falskt |
| perfEvtsSendAll | När _enablePerfMgr_ är aktiverat och [IPerfManager](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/IPerfManager.ts) utlöser en [INotificationManager](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/INotificationManager.ts). perfEvent () avgör den här flaggan om en händelse utlöses (och skickas till alla lyssnare) för alla händelser (sant) eller endast för "överordnade" händelser (falskt &lt; Standardvärde &gt; ).<br />En överordnad [IPerfEvent](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/IPerfEvent.ts) är en händelse där inga andra IPerfEvent fortfarande körs vid den tidpunkt då händelsen skapas och dess _överordnade_ egenskap inte är null eller odefinierad. Sedan v-2.5.7 |  boolean<br />falskt |
| idLength | Identifierar den standard längd som används för att generera nya slumpmässiga sessions-och användar-ID-värden. Standardvärdet är 22, föregående standardvärdet var 5 (v 2.5.8 eller mindre). om du vill behålla den tidigare maximala längden bör du ange värdet 5. |  numeric<br />22 |

## <a name="cookie-handling"></a>Cookie-hantering

Från version 2.6.0 är cookie-hanteringen nu tillgänglig direkt från instansen och kan inaktive ras och aktive ras igen efter initieringen.

Om du har inaktiverat under initiering via `disableCookiesUsage` eller `cookieCfg.enabled` -konfigurationer kan du nu återaktivera via funktionen [ICookieMgr](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts) `setEnabled` .

Den instansbaserade cookie-hanteringen ersätter också de tidigare CoreUtils globala funktionerna `disableCookies()` i `setCookie(...)` , `getCookie(...)` och `deleteCookie(...)` . Och för att dra nytta av förbättringarna i träd skakningen lanseras också som en del av version 2.6.0. du bör inte längre använda de globala funktionerna.

### <a name="icookiemgrconfig"></a>ICookieMgrConfig

Cookie-konfiguration för instans baserad cookie-hantering har lagts till i version 2.6.0.

| Name | Beskrivning | Typ och standard |
|------|-------------|------------------|
| enabled | Ett booleskt värde som anger om användningen av cookies av SDK: n har Aktiver ATS av den aktuella instansen. Om värdet är False kommer instansen av SDK som initieras av den här konfigurationen inte lagra eller läsa data från cookies | boolean<br/> true |
| domän | Anpassad cookie-domän. Detta är användbart om du vill dela Application Insights cookies över under domäner. Om inget värde anges används värdet från root- `cookieDomain` värdet. | sträng<br/>null |
| path | Anger den sökväg som ska användas för cookien, om den inte anges används något värde från rotobjektet `cookiePath` . | sträng <br/> / |
| getCookie | Funktion för att hämta det namngivna cookie-värdet, om det inte anges kommer det att använda intern cookie-parsning/-cachelagring. | `(name: string) => string` <br/> null |
| setCookie | Funktion för att ange den namngivna cookien med det angivna värdet, som endast anropas när du lägger till eller uppdaterar en cookie. | `(name: string, value: string) => void` <br/> null |
| delCookie | Funktion för att ta bort den namngivna cookien med det angivna värdet, separerad från setCookie för att undvika att behöva parsa värdet för att avgöra om cookien läggs till eller tas bort. Om den inte anges kommer den att använda intern cookie-parsning/-cachelagring. | `(name: string, value: string) => void` <br/> null |

### <a name="simplified-usage-of-new-instance-cookie-manager"></a>Förenklad användning av den nya instansen cookie Manager

- appInsights. [getCookieMgr ()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts). SetEnabled (sant/falskt);
- appInsights. [getCookieMgr ()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts). Set ("min Cook", "% 20encoded% 20value");
- appInsights. [getCookieMgr ()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts). Get ("min Cook");
- appInsights. [getCookieMgr ()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts). del ("min Cook");

## <a name="enable-time-on-page-tracking"></a>Aktivera tids spårning på sidan

Genom att ställa in `autoTrackPageVisitTime: true` den tid som en användare lägger på varje sida spåras. På varje ny sid visningar skickas varaktigheten som användaren har använt på *föregående* sida som ett [anpassat mått](../essentials/metrics-custom-overview.md) med namnet `PageVisitTime` . Det här anpassade måttet visas i [Metrics Explorer](../essentials/metrics-getting-started.md) som ett "log-baserat mått".

## <a name="enable-correlation"></a>Aktivera korrelation

Korrelation genererar och skickar data som möjliggör distribuerad spårning och ger [program kartan](../app/app-map.md), [vyn från slut punkt till slut punkt](../app/app-map.md#go-to-details)och andra diagnostikverktyg.

I följande exempel visas alla möjliga konfigurationer som krävs för att aktivera korrelation, med scenario-/regionsspecifika anteckningar nedan:

```javascript
// excerpt of the config section of the JavaScript SDK snippet with correlation
// between client-side AJAX and server requests enabled.
cfg: { // Application Insights Configuration
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    disableFetchTracking: false,
    enableCorsCorrelation: true,
    enableRequestHeaderTracking: true,
    enableResponseHeaderTracking: true,
    correlationHeaderExcludedDomains: ['myapp.azurewebsites.net', '*.queue.core.windows.net']
    /* ...Other Configuration Options... */
}});
</script>

``` 

Om någon av de servrar från tredje part som klienten kommunicerar med inte accepterar `Request-Id` `Request-Context` -och-rubrikerna, och du inte kan uppdatera konfigurationen, måste du lägga dem i en exkluderings lista via `correlationHeaderExcludeDomains` konfigurations egenskapen. Den här egenskapen stöder jokertecken.

Server sidan måste kunna acceptera anslutningar med de aktuella huvudena. Beroende på `Access-Control-Allow-Headers` Server sidans konfiguration är det ofta nödvändigt att utöka listan på Server sidan genom att manuellt lägga till `Request-Id` och `Request-Context` .

Åtkomst – kontroll-Tillåt-rubriker: `Request-Id` , `Request-Context` , `<your header>`

> [!NOTE]
> Om du använder OpenTelemtry eller Application Insights SDK: er som publicerats i 2020 eller senare rekommenderar vi att du använder [WC3 spåra tracecontext](https://www.w3.org/TR/trace-context/). Se konfigurations vägledning [här](../app/correlation.md#enable-w3c-distributed-tracing-support-for-web-apps).

## <a name="single-page-applications"></a>Program med en sida

Som standard hanterar **inte** denna SDK tillstånds väg ändringar som inträffar i program med en enda sida. Om du vill aktivera automatisk väg ändrings spårning för ditt program på en sida kan du lägga till `enableAutoRouteTracking: true` i konfigurations konfigurationen.

För närvarande erbjuder vi ett separat [reagerar-plugin-program](javascript-react-plugin.md)som du kan initiera med det här SDK: t. Den utför även spårning av ändringar i flödet, samt att samla in andra reagera på en speciell telemetri.
> [!NOTE]
> Använd `enableAutoRouteTracking: true` endast om du **inte** använder den reagera plugin-programmet. Båda kan skicka nya PageViews när vägen ändras. Om båda är aktiverade kan duplicerade PageViews skickas.

## <a name="extensions"></a>Tillägg

| Tillägg |
|---------------|
| [React](javascript-react-plugin.md)|
| [React Native](javascript-react-native-plugin.md)|
| [Angular](javascript-angular-plugin.md)|
| [Klicka på Analytics Auto-Collection](javascript-click-analytics-plugin.md)|

## <a name="explore-browserclient-side-data"></a>Utforska data från webbläsare/klient Sidan

Du kan visa data från webbläsare/klient sidan genom att gå till **mått** och lägga till enskilda mått som du är intresse rad av:

![Skärm bild av sidan mått i Application Insights visar grafik visar statistik data för ett webb program.](./media/javascript/page-view-load-time.png)

Du kan också visa dina data från Java Script SDK via webbläsarens upplevelse i portalen.

Välj **webbläsare** och välj sedan **haverier** eller **prestanda**.

![Skärm bild av webb läsar sidan i Application Insights visar hur du lägger till webb läsar fel eller webb läsar prestanda till de mått som du kan visa för ditt webb program.](./media/javascript/browser.png)

### <a name="performance"></a>Prestanda

![Skärm bild av sidan prestanda i Application Insights visar bild visning av åtgärds mått för ett webb program.](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Beroenden

![Skärm bild av sidan prestanda i Application Insights visar bild visning av beroende mått för ett webb program.](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Analys

Om du vill fråga din telemetri som samlas in av JavaScript SDK väljer du knappen **Visa i loggar (analys)** . Genom att lägga till en `where` -sats i visas `client_Type == "Browser"` endast data från Java Script SDK och all telemetri på Server sidan som samlas in av andra SDK: er.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>Stöd för käll kartor

Minified-callstacken för din undantags telemetri kan vara unminified i Azure Portal. Alla befintliga integreringar på panelen undantags information fungerar med den nya unminified-callstacken.

#### <a name="link-to-blob-storage-account"></a>Länk till Blob Storage-konto

Du kan länka din Application Insights-resurs till din egen Azure Blob Storage-behållare för att automatiskt unminify anrops stackar. För att komma igång, se [stöd för automatiska käll kartor](./source-map-support.md).

### <a name="drag-and-drop"></a>Dra och släpp

1. Välj ett objekt för telemetri av undantag i Azure Portal om du vill visa dess "transaktions information från slut punkt till slut punkt"
2. Identifiera vilka käll mappningar som motsvarar den här anrops stacken. Käll kartan måste matcha en stack Rams käll fil, men med suffixet `.map`
3. Dra och släpp käll mappningarna till anrops stacken i Azure Portal ![ en animerad bild som visar hur du drar och släpper käll mappnings filer från en build-mapp till anrops stack-fönstret i Azure Portal.](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights Web Basic

För en låg upplevelse kan du i stället installera den grundläggande versionen av Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
Den här versionen har minimalt antal funktioner och funktioner och förlitar sig på att du ska kunna skapa dem på det sätt som passar dig bäst. Den utför till exempel ingen autoinsamling (ej fångade undantag, AJAX osv.). API: erna för att skicka vissa typer av telemetri, som `trackTrace` , `trackException` osv., ingår inte i den här versionen, så du måste ange ett eget gränssnitt. Den enda API som är tillgänglig är `track` . Ett [exempel](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) finns här.

## <a name="examples"></a>Exempel

Körbara-exempel finns i [Application Insights JavaScript SDK-exempel](https://github.com/Azure-Samples?q=applicationinsights-js-demo).

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Uppgradera från den gamla versionen av Application Insights

Bryta ändringar i SDK v2-versionen:
- Vissa API-anrop, till exempel trackPageView och trackException, har uppdaterats för att möjliggöra bättre API-signaturer. Det finns inte stöd för att köra i Internet Explorer 8 och tidigare versioner av webbläsaren.
- Telemetri-kuvertet har fält namn och struktur ändringar på grund av data schema uppdateringar.
- Flyttad `context.operation` till `context.telemetryTrace` . Vissa fält ändrades också ( `operation.id`  -->  `telemetryTrace.traceID` ).
  - Använd om du vill uppdatera aktuellt sid visningar-ID manuellt (till exempel i SPA-appar) `appInsights.properties.context.telemetryTrace.traceID = Microsoft.ApplicationInsights.Telemetry.Util.generateW3CId()` .
    > [!NOTE]
    > Om du vill behålla spårnings-ID: t unikt, där du tidigare använde `Util.newId()` , använder du nu `Util.generateW3CId()` . Båda i slut ändan är åtgärds-ID.

Om du använder den aktuella Application Insights PRODUCTion SDK (1.0.20) och vill se om den nya SDK: n fungerar i körnings miljön uppdaterar du URL: en beroende på ditt aktuella SDK-inläsnings scenario.

- Ladda ned via CDN-scenario: uppdatera kodfragmentet som du använder för att peka på följande URL:
   ```
   "https://js.monitor.azure.com/scripts/b/ai.2.min.js"
   ```

- NPM-scenario: anropa `downloadAndSetup` för att hämta det fullständiga ApplicationInsights-skriptet från CDN och initiera det med instrumentande nyckel:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://js.monitor.azure.com/scripts/b/ai.2.min.jss"
     });
   ```

Testa i intern miljö för att kontrol lera att övervakningsfunktionerna fungerar som förväntat. Om alla fungerar uppdaterar du API-signaturerna på lämpligt sätt till SDK v2-versionen och distribuerar i produktions miljöerna.

## <a name="sdk-performanceoverhead"></a>SDK-prestanda/-kostnad

På bara 36 KB-GZipped och bara tar ~ 15 MS att initiera, lägger Application Insights till en försumbar mängd loadtime till din webbplats. Genom att använda kodfragmentet läses minimala komponenter i biblioteket in snabbt. Under tiden laddas det fullständiga skriptet ned i bakgrunden.

Medan skriptet hämtas från CDN, är all spårning av sidan i kö. När det nedladdade skriptet har slutförts asynkront initieras spåras alla händelser som placerats i kö. Det innebär att du inte förlorar någon telemetri under hela sidans livs cykel. Den här installations processen ger din sida ett sömlöst analys system, osynligt för dina användare.

> Sammanfattning:
> - ![NPM-version](https://badge.fury.io/js/%40microsoft%2Fapplicationinsights-web.svg)
> - ![GZIP-komprimerad storlek](https://img.badgesize.io/https://js.monitor.azure.com/scripts/b/ai.2.min.js.svg?compression=gzip)
> - **15 MS** allmän initierings tid
> - **Noll** spårning saknas under sidans livs cykel

## <a name="browser-support"></a>Stöd för webbläsare

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Chrome senaste ✔ |  Senaste ✔ för Firefox | IE 9 + & Edge-✔<br>IE 8-kompatibel | Senaste ✔ för Opera | Senaste ✔ för Safari |

## <a name="es3ie8-compatibility"></a>ES3/IE8-kompatibilitet

Som SDK finns det många användare som inte kan styra vilka webbläsare kunderna använder. Vi måste därför se till att det här SDK: n fortsätter att "fungera" och inte bryter JS-körningen när den har lästs in av en äldre webbläsare. Även om det skulle vara idealiskt att inte stödja ES3-webbläsare (IE8 och äldre generation) finns det många stora kunder/användare som fortsätter att kräva att sidor "fungerar" och vilket anges att de kan eller inte kan styra vilken webbläsare som användarna väljer att använda.

Detta innebär inte att vi bara stöder den lägsta gemensamma uppsättningen funktioner, precis som vi behöver för att underhålla ES3 och när nya funktioner läggs till, måste de läggas till på ett sätt som inte skulle bryta ES3 JavaScript-parsning och läggas till som en valfri funktion.

[Se GitHub för fullständig information om support för IE8](https://github.com/Microsoft/ApplicationInsights-JS#es3ie8-compatibility)

## <a name="open-source-sdk"></a>SDK för öppen källkod

Application Insights JavaScript SDK är öppen källkod för att Visa käll koden eller för att bidra till projektet besök den [officiella GitHub-lagringsplatsen](https://github.com/Microsoft/ApplicationInsights-JS). 

De senaste uppdateringarna och fel korrigeringarna [finns i viktig information](./release-notes.md).

## <a name="next-steps"></a><a name="next"></a> Nästa steg
* [Spåra användning](usage-overview.md)
* [Anpassade händelser och mätvärden](api-custom-events-metrics.md)
* [Skapa – mät – lär](usage-overview.md)
* [Felsöka SDK-inläsnings fel](javascript-sdk-load-failure.md)
