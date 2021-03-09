---
title: 'Snabb start: övervaka webbplatser med Azure Monitor Application Insights'
description: I den här snabb starten får du lära dig hur du konfigurerar övervakning av webbplatser på klient-och webb sidan med Azure Monitor Application Insights.
ms.topic: quickstart
ms.date: 08/19/2020
ms.custom: mvc
ms.openlocfilehash: 1773ebb9c490420451a119c8343fb613ff50f029
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102488587"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Snabb start: börja övervaka din webbplats med Azure Monitor Application Insights

I den här snabb starten lär du dig att lägga till Application Insights Java Script SDK med öppen källkod på din webbplats. Du lär dig också hur du bättre förstår klient-och webb läsar upplevelsen för besökare på din webbplats.

Med Azure Monitor Application Insights kan du enkelt övervaka din webbplats med avseende på tillgänglighet, prestanda och användning. Du kan också snabbt identifiera och diagnostisera fel i appen utan att vänta på att en användare rapporterar dem. Application Insights tillhandahåller övervakning på både server sidan och klient-och webb läsar sidan.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* En webbplats där du kan lägga till Application Insights JavaScript SDK.

## <a name="enable-application-insights"></a>Aktivera Application Insights

Application Insights kan samla in telemetridata från alla Internet-anslutna program som körs lokalt eller i molnet. Använd följande steg för att visa dessa data:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Välj **skapa ett resurs**  >  **hanterings verktyg**  >  **Application Insights**.

   > [!NOTE]
   >Om det här är första gången du skapar en Application Insights-resurs, se [skapa en Application Insights resurs](./create-new-resource.md).
1. När konfigurations rutan visas, använder du följande tabell för att fylla i inmatade fält:

    | Inställningar        | Värde           | Beskrivning  |
   | ------------- |:-------------|:-----|
   | **Namn**      | Globalt unikt värde | Namnet som identifierar den app som du övervakar. |
   | **Resursgrupp**     | myResourceGroup      | Namnet på den nya resurs gruppen som ska vara värd för Application Insights data. Du kan skapa en ny resursgrupp eller välja en befintlig. |
   | **Plats** | East US | Välj en plats nära dig eller nära den plats där din app finns. |
1. Välj **Skapa**.

## <a name="create-an-html-file"></a>Skapa en HTML-fil

1. På din lokala dator skapar du en fil med namnet ``hello_world.html``. I det här exemplet skapar du filen i roten på enhet C så att den ser ut ``C:\hello_world.html`` .
1. Kopiera och klistra in följande skript i ``hello_world.html`` :

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations, visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-application-insights-sdk"></a>Konfigurera Application Insights SDK

1. Välj **Översikt**  >  **Essentials** och kopiera sedan programmets **Instrumentation-nyckel**.

   ![Nytt Application Insights-resursformulär](media/website-monitoring/instrumentation-key-001.png)

1. Lägg till följande skript i ``hello_world.html`` filen före den avslutande ``</head>`` taggen:

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
    > Det aktuella kodfragmentet (anges ovan) är version "5", versionen är kodad i kodfragmentet som sv: "#" och den [aktuella versionen och konfigurations informationen är tillgänglig på GitHub](https://go.microsoft.com/fwlink/?linkid=2156318).
   
1. Redigera ``hello_world.html`` och Lägg till din instrumentationsnyckel.

1. Öppna ``hello_world.html`` i en lokal webbläsarsession. Den här åtgärden skapar en enskild sid visning. Du kan uppdatera din webbläsare om du vill generera flera vyer på prov sidan.

## <a name="monitor-your-website-in-the-azure-portal"></a>Övervaka din webbplats i Azure Portal

1. Öppna sidan Application Insights **Översikt** i Azure Portal för att visa information om ditt program som körs. På sidan **Översikt** har du hämtat din Instrumentation-nyckel.

   De fyra standarddiagrammen på översiktssidan är begränsade till programdata på serversidan. Eftersom vi instrumenterar klient-och webb läsar interaktioner med Java Script SDK, gäller inte den här vyn om vi även har installerat SDK för Server sidan.

1. Välj ikonen **analys** ![ program karta ](media/website-monitoring/006.png) .  Den här åtgärden öppnar **analys**, som innehåller ett omfattande frågespråk för analys av alla data som samlas in av Application Insights. Kör följande fråga om du vill visa data som är relaterade till förfrågningar på klient sidan:

    ```kusto
    // average pageView duration by name
    let timeGrain=1s;
    let dataset=pageViews
    // additional filters can be applied here
    | where timestamp > ago(15m)
    | where client_Type == "Browser" ;
    // calculate average pageView duration for all pageViews
    dataset
    | summarize avg(duration) by bin(timestamp, timeGrain)
    | extend pageView='Overall'
    // render result in a chart
    | render timechart
    ```

   ![analysdiagram över användarbegäranden under en viss tidsperiod](./media/website-monitoring/analytics-query.png)

1. Gå tillbaka till sidan **Översikt**. Under **Undersök** rubriken väljer du **webbläsare** och sedan **prestanda**.  Mått som rör webbplatsens prestanda visas. Det finns en motsvarande vy för att analysera fel och undantag på din webbplats. Du kan välja **exempel** för att få åtkomst till [slut punkt till slut punkts transaktions information](./transaction-diagnostics.md).

   ![Diagram för servermått](./media/website-monitoring/browser-performance.png)

1. Gå till huvud Application Insights-menyn och välj [**användare**](./usage-segmentation.md) under **användnings** rubriken för att börja utforska [verktygen för analys av användar beteende](./usage-overview.md). Eftersom vi testar från en enda dator ser vi bara data för en användare. För en Live-webbplats kan distributionen av användare se ut så här:

     ![Användardiagram](./media/website-monitoring/usage-users.png)

1. För en mer komplex webbplats med flera sidor kan du använda [**användarflöden**](./usage-flows.md) -verktyget för att spåra den väg besökarna tar genom de olika delarna av webbplatsen.

   ![Visualisering av användarflöden](./media/website-monitoring/user-flows.png)

Mer avancerade konfigurationer för övervakning av webbplatser finns i [Java Script SDK API-referensen](./javascript.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med ytterligare snabb starter eller självstudier ska du inte rensa upp resurserna som du skapade i den här snabb starten. Annars kan du använda följande steg för att ta bort alla resurser som skapats i den här snabb starten i Azure Portal.

> [!NOTE]
> Om du använde en befintlig resurs grupp fungerar inte följande instruktioner. I stället kan du bara ta bort den enskilda Application Insights resursen. Tänk på att när du tar bort en resurs grupp tas alla underyling-resurser som är medlemmar i gruppen också bort.

1. Välj **resurs grupper** på den vänstra menyn på Azure Portal och välj sedan **myResourceGroup** eller namnet på den tillfälliga resurs gruppen.
1. På sidan resurs grupp väljer du **ta bort**, anger **myResourceGroup** i text rutan och väljer sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hitta och diagnostisera prestandaproblem](../logs/log-query-overview.md)

