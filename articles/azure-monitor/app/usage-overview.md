---
title: Användnings analys med Azure Application Insights | Microsoft-dokument
description: Förstå dina användare och vad de gör med din app.
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: d9de1e10363f2100b9dfe557dc12e0be951ce6b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102489046"
---
# <a name="usage-analysis-with-application-insights"></a>Användningsanalys med Application Insights

Vilka funktioner i din webb-eller mobilapp är mest populära? Uppnår användarna sina mål med din app? Släpps de vid vissa tidpunkter och returnerar dem senare?  [Azure Application Insights](./app-insights-overview.md) hjälper dig att få kraftfulla insikter om hur användarna använder din app. Varje gång du uppdaterar din app kan du utvärdera hur väl den fungerar för användare. Med den här kunskapen kan du fatta data drivna beslut om dina nästa utvecklings cykler.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Cijb]

## <a name="send-telemetry-from-your-app"></a>Skicka telemetri från din app

Den bästa upplevelsen erhålls genom att installera Application Insights både i appens Server kod och på dina webb sidor. Klient-och Server komponenterna i appen skickar telemetri tillbaka till Azure Portal för analys.

1. **Server kod:** Installera lämplig modul för din [ASP.net](./asp-net.md), [Azure](./app-insights-overview.md), [Java](./java-get-started.md), [Node.js](./nodejs.md)eller [annan](./platforms.md) app.

    * *Vill du inte installera Server koden? [Skapa bara en Azure Application Insights-resurs](./create-new-resource.md).*

2. **Webbs Ides kod:** Lägg till följande skript på din webb sida före stängningen ``</head>`` . Ersätt Instrumentation-nyckeln med lämpligt värde för din Application Insights-resurs:
    
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
      instrumentationKey:"INSTRUMENTATION_KEY"
    }});
    </script>
    ```

    Mer avancerade konfigurationer för övervakning av webbplatser finns i [referens artikeln för Java Script SDK](./javascript.md).

3. **Kod för mobilapp:** Använd App Center SDK för att samla in händelser från din app och skicka sedan kopior av dessa händelser till Application Insights för analys genom att [följa den här hand boken](../app/mobile-center-quickstart.md).

4. **Hämta telemetri:** Kör ditt projekt i fel söknings läge under några minuter och leta sedan efter resultat på bladet översikt i Application Insights.

    Publicera din app för att övervaka appens prestanda och ta reda på vad användarna gör med din app.

## <a name="explore-usage-demographics-and-statistics"></a>Utforska användnings demografiskhet och statistik
Ta reda på när användarna använder din app, vilka sidor de är mest intresserade av, var dina användare finns, vilka webbläsare och operativ system de använder. 

Rapporterna användare och sessioner filtrerar dina data efter sidor eller anpassade händelser och segmenterar dem efter egenskaper som plats, miljö och sida. Du kan också lägga till egna filter.

![Skärm dum par visar översikts sidan användare för ett fiktivt företag.](./media/usage-overview/users.png)  

Insikter på den högra punkten för att ta reda på intressanta mönster i data uppsättningen.  

* Rapporten **användare** räknar antalet unika användare som har åtkomst till dina sidor inom de valda tids perioderna. För Web Apps räknas användare med hjälp av cookies. Om någon kommer åt din webbplats med olika webbläsare eller klient datorer, eller rensar sina cookies, kommer de att räknas mer än en gång.
* I rapporten **sessioner** räknas antalet användarsessioner som har åtkomst till din webbplats. En session är en aktivitets period av en användare som avslutas med en period av inaktivitet på mer än en halvtimme.

[Mer om verktyg för användare, sessioner och händelser](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Kvarhållning – hur många användare kommer tillbaka?

Kvarhållning hjälper dig att förstå hur ofta dina användare kommer att använda sin app, baserat på kohorter av användare som utförde viss affärs åtgärd under en viss tids period. 

- Förstå vilka olika funktioner som gör att användarna kan komma tillbaka mer än andra 
- Formulär Hypotheses baserat på verkliga användar data 
- Ta reda på om kvarhållning är ett problem i din produkt 

![Skärm dum par visar sidan Översikt över kvarhållning som visar information om hur ofta användarna kommer att använda sina appar.](./media/usage-overview/retention.png) 

Med kontrollerna för kvarhållning i överkant kan du definiera enskilda händelser och tidsintervall för att beräkna kvarhållning. Grafen i mitten ger en visuell representation av den övergripande kvarhållning procenten med det angivna tidsintervallet. Diagrammet längst ned representerar individuell kvarhållning under en viss tids period. Den här detalj nivån gör att du kan förstå vad dina användare gör och vad som kan påverka användare med en mer detaljerad granularitet.  

[Mer om bevarande verktyget](usage-retention.md)

## <a name="custom-business-events"></a>Anpassade affärs händelser

För att få en tydlig förståelse av vad användarna gör med din app, är det praktiskt att infoga rader med kod för att logga anpassade händelser. Dessa händelser kan spåra allt från detaljerade användar åtgärder, till exempel att klicka på specifika knappar, till fler viktiga affärs händelser som att göra ett köp eller vinna ett spel.

Du kan också använda [plugin-programmet Klicka på Analytics Auto-Collection](javascript-click-analytics-plugin.md) för att samla in anpassade händelser.

I vissa fall kan sid visningar representera användbara händelser, men det är inte sant i allmänhet. En användare kan öppna en produkt sida utan att köpa produkten. 

Med specifika affärs händelser kan du rita upp användarnas status genom din webbplats. Du kan ta reda på deras inställningar för olika alternativ och var de än är eller har problem. Med den här kunskapen kan du fatta välgrundade beslut om prioriteringarna i din utvecklings efter släpning.

Händelser kan loggas från klient sidan av appen:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Eller från Server sidan:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Du kan koppla egenskaps värden till dessa händelser så att du kan filtrera eller dela händelser när du inspekterar dem i portalen. Dessutom är en standard uppsättning egenskaper kopplad till varje händelse, till exempel anonymt användar-ID, som gör att du kan spåra sekvensen av en enskild användares aktiviteter.

Läs mer om [anpassade händelser](./api-custom-events-metrics.md#trackevent) och [Egenskaper](./api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Händelser för segment och tärning

I verktygen användare, sessioner och händelser kan du segmentera och tärninga anpassade händelser efter användare, händelse namn och egenskaper.
![Skärm dum par visar översikts sidan användare för ett fiktivt företag.](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Utforma telemetri med appen

När du utformar varje funktion i din app bör du fundera på hur du ska mäta dess framgång med dina användare. Bestäm vilka affärs händelser du behöver registrera och koda spårnings anropen för dessa händelser till din app från start.

## <a name="a--b-testing"></a>En | B-testning
Om du inte vet vilken variant av en funktion som kommer att bli mer framgångs rik, frigör du båda dessa och gör dem tillgängliga för olika användare. Mät framgången och gå sedan vidare till en enhetlig version.

För den här metoden kopplar du distinkta egenskaps värden till all telemetri som skickas av varje version av din app. Det kan du göra genom att definiera egenskaper i den aktiva TelemetryContext. Dessa standard egenskaper läggs till i varje telemetri som programmet skickar – inte bara dina anpassade meddelanden, utan även standard-telemetri.

Filtrera och dela data på egenskapsvärdena i Application Insights-portalen, så att du kan jämföra olika versioner.

Det gör du genom att [Konfigurera en telemetri-initierare](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer):

**ASP.NET-appar**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry item)
            {
                var itemProperties = item as ISupportProperties;
                if (itemProperties != null && !itemProperties.Properties.ContainsKey("AppVersion"))
                {
                    itemProperties.Properties["AppVersion"] = "v2.1";
                }
            }
    }
```

I Web App-initieraren, till exempel global. asax. CS:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**ASP.NET Core-appar**

> [!NOTE]
> Det går inte att lägga till initieraren med `ApplicationInsights.config` eller använda `TelemetryConfiguration.Active` är ogiltig för ASP.net Core-program. 

Om du vill [ASP.net Core](asp-net-core.md#adding-telemetryinitializers) program lägger du till en ny `TelemetryInitializer` genom att lägga till den i behållaren för beroende insprutning, som du ser nedan. Detta görs i- `ConfigureServices` metoden för din `Startup.cs` klass.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Alla nya TelemetryClients lägger automatiskt till det egenskaps värde som du anger. Enskilda telemetri händelser kan åsidosätta standardvärdena.

## <a name="next-steps"></a>Nästa steg
   - [Användare, sessioner, händelser](usage-segmentation.md)
   - [Trattar](usage-funnels.md)
   - [Kvarhållning](usage-retention.md)
   - [Användarflöden](usage-flows.md)
   - [Arbetsböcker](../visualize/workbooks-overview.md)
