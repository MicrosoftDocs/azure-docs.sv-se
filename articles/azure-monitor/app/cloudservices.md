---
title: Application Insights för Azure Cloud Services | Microsoft Docs
description: Övervaka webb- och arbetsroller effektivt med Application Insights
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 09/05/2018
ms.openlocfilehash: 264d6d4b0b397a29b5dc1db4bb299297c1e30584
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419243"
---
# <a name="application-insights-for-azure-cloud-services"></a>Application Insights för Azure Cloud Services
[Application Insights][start] kan övervaka [Azure Cloud Service-appar](https://azure.microsoft.com/services/cloud-services/) för tillgänglighet, prestanda, haverier och användning genom att kombinera data från Application Insights sdk: er med [Azure-diagnostik](../agents/diagnostics-extension-overview.md) data från moln tjänsterna. Med den feedback du får om appens prestanda och effektivitet kan du fatta välgrundade beslut om designen i varje utvecklingslivscykel.

![Översikts instrument panel](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar behöver du:

* En [Azure](https://azure.com) -prenumeration. Logga in med din Microsoft-konto för Windows, Xbox Live eller andra moln tjänster från Microsoft. 
* Microsoft Azure Tools 2,9 eller senare.
* Developer Analytics tools 7,10 eller senare.

## <a name="get-started-quickly"></a>Kom igång snabbt
Det snabbaste och enklaste sättet att övervaka din molntjänst med Application Insights är att välja det här alternativet när du publicerar din tjänst till Azure.

![Exempel på sidan diagnostikinställningar](./media/cloudservices/azure-cloud-application-insights.png)

Det här alternativet styr din app vid körning och ger dig all telemetri som du behöver för att övervaka begär Anden, undantag och beroenden i din webb roll. Den övervakar också prestanda räknare från dina arbets roller. Eventuella diagnostiska spårningar som genereras av appen skickas också till Application Insights.

Om det här alternativet är allt du behöver så är du klar. 

Nästa steg är att [Visa mått från din app](../essentials/metrics-charts.md)och [fråga dina data med analyser](../logs/log-query-overview.md). 

Om du vill övervaka prestanda i webbläsaren kanske du också vill ställa in [tillgänglighets test](./monitor-web-app-availability.md) och [lägga till kod på dina webb sidor](./javascript.md).

I nästa avsnitt beskrivs följande ytterligare alternativ:

* Skicka data från olika komponenter och bygg konfigurationer till separata resurser.
* Lägg till anpassad telemetri från din app.

## <a name="sample-app-instrumented-with-application-insights"></a>Exempel på App instrumenterad med Application Insights
I den här [exempel appen](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)läggs Application Insights till i en moln tjänst med två arbets roller som finns i Azure. 

I nästa avsnitt får du lära dig hur du anpassar ditt eget moln tjänst projekt på samma sätt.

## <a name="plan-resources-and-resource-groups"></a>Planera resurser och resursgrupper
Telemetrin från din app lagras, analyseras och visas i en Azure-resurs av typen Application Insights. 

Varje resurs tillhör en resursgrupp. Resurs grupper används för att hantera kostnader, för att bevilja åtkomst till grupp medlemmar och för att distribuera uppdateringar i en enda koordinerad transaktion. Du kan till exempel [skriva ett skript för att distribuera](../../azure-resource-manager/templates/deploy-powershell.md) en moln tjänst i Azure och dess Application Insights övervakning av resurser i en enda åtgärd.

### <a name="resources-for-components"></a>Resurser för komponenter

Vi rekommenderar att du [lägger till en dimensions egenskap för varje telemetri-objekt](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) som identifierar dess käll roll. I den här metoden visar mått diagram, till exempel undantag, vanligt vis en agg regering av antalet från de olika rollerna, men du kan segmentera diagrammet utifrån roll-ID: t, om det behövs. Du kan också filtrera sökningar efter samma dimension. Det här alternativet gör det lättare att Visa allting samtidigt, men det kan också leda till viss förvirring mellan rollerna.

Webbläsartelemetri ingår vanligtvis i samma resurs som dess webbroll på serversidan.

Lägg Application Insights resurser för de olika komponenterna i en resurs grupp. Den här metoden gör det enkelt att hantera dem tillsammans. 

### <a name="separate-development-test-and-production"></a>Avgränsa utveckling, testning och produktion
Om du utvecklar anpassade händelser för din nästa funktion medan den tidigare versionen är aktiv, skickar du utvecklingstelemetrin till en separat Application Insights-resurs. Annars kan det vara svårt att hitta din test-telemetri bland all trafik från Live-platsen.

Undvik den här situationen genom att skapa separata resurser för varje build-konfiguration eller "stämpel" (utveckling, testning, produktion och så vidare) av systemet. Placera resurserna för varje versionskonfiguration i en separat resursgrupp. 

Om du vill skicka Telemetrin till lämpliga resurser kan du konfigurera Application Insights SDK så att den hämtar en annan Instrumentation-nyckel, beroende på Build-konfigurationen. 

Lär dig hur du [ställer in Instrumentation-nyckeln dynamiskt](./separate-resources.md#dynamic-ikey) för olika faser. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Skapa en Application Insights-resurs för varje roll

Om du har bestämt dig för att skapa en separat resurs för varje roll, och kanske en separat uppsättning för varje versions konfiguration, är det enklast att skapa dem på Application Insights Portal. Om du skapar resurser mycket kan du [automatisera processen](./powershell.md).

1. I [Azure Portal][portal]väljer du **nya**  >  **Developer Services**  >  **Application Insights**.  

    ![Application Insightss fönster](./media/cloudservices/01-new.png)

1. I list rutan **program typ** väljer du **ASP.NET-webbprogram**.

Varje resurs identifieras med en Instrumentation-nyckel. Du kan behöva den här nyckeln senare om du vill konfigurera eller verifiera konfigurationen av SDK manuellt.


## <a name="set-up-azure-diagnostics-for-each-role"></a>Konfigurera Azure Diagnostics för varje roll
Ange det här alternativet om du vill övervaka din app med Application Insights. För webb roller ger det här alternativet prestanda övervakning, varningar, diagnostik och användnings analys. För andra roller kan du söka efter och övervaka Azure-diagnostik, till exempel omstart, prestanda räknare och anrop till system. Diagnostics. trace. 

1. I Visual Studio Solution Explorer går **\<YourCloudService>**  >  du till **roller** och öppnar egenskaperna för varje roll.

1. I **konfiguration** markerar du kryss rutan **skicka diagnostikdata till Application Insights** och väljer sedan den Application Insights resurs som du skapade tidigare.

Om du har valt att använda en separat Application Insights-resurs för varje versionskonfiguration väljer du konfigurationen först.

![Konfigurera Application Insights](./media/cloudservices/configure-azure-diagnostics.png)

Detta innebär att du kan lägga till Application Insights Instrumentation-nycklar i filerna med namnet *ServiceConfiguration. \* . cscfg*. Här är [exempel koden](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg).

Om du vill variera graden av diagnostikinformation som skickas till Application Insights kan du göra det [genom att redigera *. cscfg* -filerna direkt](../agents/diagnostics-extension-to-application-insights.md).

## <a name="install-the-sdk-in-each-project"></a><a name="sdk"></a>Installera SDK i varje projekt
Med det här alternativet kan du lägga till anpassad affärstelemetri till vilken roll som helst. Alternativet ger en närmare analys av hur din app används och fungerar.

I Visual Studio konfigurerar du Application Insights SDK för varje molnapprojekt.

1. Om du vill konfigurera **webb roller** högerklickar du på projektet och väljer sedan **Konfigurera Application Insights** eller **Lägg till > Application Insights telemetri**.

1. Så här konfigurerar du **arbets roller**: 

    a. Högerklicka på projektet och välj sedan **Hantera NuGet-paket**.

    b. Lägg till [Application Insights för Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

1. Konfigurera SDK för att skicka data till Application Insights resursen:

    a. I en lämplig start funktion anger du Instrumentation-nyckeln från konfigurations inställningen i *. cscfg* -filen:
 
    ```csharp
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
        var telemetryClient = new TelemetryClient(configuration);
    ```
   
    b. Upprepa "steg a" för varje roll i din app. Se exemplen:
   
    * [Webbroll](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [Arbetsroll](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [För webb sidor](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. Ange *ApplicationInsights.config* -filen som ska kopieras alltid till utdatakatalogen.

   Ett meddelande i *. config* -filen uppmanar dig att placera instrument nyckeln där. Men för molnappar är det bättre att ställa in den från *. cscfg* -filen. Den här metoden säkerställer att rollen identifieras korrekt i portalen.

## <a name="set-up-status-monitor-to-collect-full-sql-queries-optional"></a>Konfigurera Statusövervakare för att samla in fullständiga SQL-frågor (valfritt)

Det här steget behövs bara om du vill samla in fullständiga SQL-frågor på .NET Framework. 

1. I `\*.csdef` filen Lägg till [Start åtgärd](../../cloud-services/cloud-services-startup-tasks.md) för varje roll som liknar 

    ```xml
    <Startup>
      <Task commandLine="AppInsightsAgent\InstallAgent.bat" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="ApplicationInsightsAgent.DownloadLink" value="http://go.microsoft.com/fwlink/?LinkID=522371" />
          <Variable name="RoleEnvironment.IsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
    
2. Ladda ned [InstallAgent.bat](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.bat) och [InstallAgent.ps1](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.ps1), Lägg in dem i `AppInsightsAgent` mappen på varje roll projekt. Se till att kopiera dem till utdata-katalogen via fil egenskaper i Visual Studio eller skapa skript.

3. Lägg till miljövariabler i alla arbets roller: 

    ```xml
      <Environment>
        <Variable name="COR_ENABLE_PROFILING" value="1" />
        <Variable name="COR_PROFILER" value="{324F817A-7420-4E6D-B3C1-143FBED6D855}" />
        <Variable name="MicrosoftInstrumentationEngine_Host" value="{CA487940-57D2-10BF-11B2-A3AD5A13CBC0}" />
      </Environment>
    ```
    
## <a name="run-and-publish-the-app"></a>Köra och publicera appen

1. Kör appen och logga in på Azure. 

1. Öppna Application Insights resurser som du har skapat.

   Enskilda data punkter visas i [sökningen][diagnostic]och sammanställda data visas i [Metric Explorer](../essentials/metrics-charts.md).

1. Lägg till mer telemetri (se nästa avsnitt) och publicera sedan din app för att få Live-diagnostik och användnings feedback. 

Om det inte finns några data gör du följande:

1. Öppna panelen [Sök][diagnostic] om du vill visa enskilda händelser.
1. Öppna olika sidor i appen så att den genererar viss telemetri.
1. Vänta några sekunder och klicka sedan på **Uppdatera**.  

Mer information finns i [Felsökning][qna].

## <a name="view-azure-diagnostics-events"></a>Visa Azure-diagnostik händelser
Du kan hitta [Azure-diagnostik](../agents/diagnostics-extension-overview.md) information i Application Insights på följande platser:

* Prestandaräknare visas som anpassade mått. 
* Windows-händelseloggar visas som spårningar och anpassade händelser.
* Programloggar, ETW-loggar och diagnostikinfrastrukturloggar visas som spårningar.

Om du vill visa prestanda räknare och antal händelser öppnar du [Metrics Explorer](../essentials/metrics-charts.md) och lägger till följande diagram:

![Azure-diagnostik data](./media/cloudservices/23-wad.png)

Om du vill söka bland de olika spårnings loggar som skickas av Azure-diagnostik använder du [Sök](./diagnostic-search.md) eller en [analys fråga](../logs/log-analytics-tutorial.md). Anta till exempel att du har ett ohanterat undantag som har orsakat en roll för krasch och åter användning. Den här informationen skulle visas i kanalen Program i Windows-händelseloggen. Du kan använda Sök för att visa händelse loggen för Windows och hämta fullständig stack spårning för undantags felet. Detta hjälper dig att hitta orsaken till problemet.

![Azure-diagnostik Sök](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Mer telemetri
I nästa avsnitt beskrivs hur du får ytterligare telemetri från olika aspekter av din app.

## <a name="track-requests-from-worker-roles"></a>Spåra begär Anden från arbets roller
I webbroller samlar modulen för begäranden automatiskt in data om HTTP-förfrågningar. Exempel på hur du kan åsidosätta standard beteendet för samlingar finns i [exemplet på MVCWebRole](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 

Du kan samla in prestanda från anrop till arbetsroller genom att spåra dem på samma sätt som HTTP-förfrågningar. I Application Insights mäter telemetritypen Begäran en enhet av det namngivna arbetet på serversidan som kan tidsbestämmas och som kan lyckas eller misslyckas separat. Även om HTTP-begäranden samlas in automatiskt av SDK kan du infoga din egen kod för att spåra begär anden till arbets roller.

Se de två exempel Worker-arbetsrollerna som instrumenteras till rapport begär Anden: 
* [WorkerRoleA](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Undantag
Information om hur du samlar in ohanterade undantag från olika typer av webbapp finns [i övervaknings undantag i Application Insights](./asp-net-exceptions.md).

Exempelwebbrollen har MVC5- och Web API 2-styrenheter. De ohanterade undantagen från de två samlas in med följande hanterare:

* [AiHandleErrorAttribute](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) har kon figurer ATS för MVC5-kontrollanter [som visas i det här exemplet](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) 
* [AiWebApiExceptionLogger](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) har kon figurer ATS för webb-API 2-styrenheter [som visas i det här exemplet](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) 

För arbets roller kan du spåra undantag på två sätt:

* Använd TrackException (t. ex.).
* Om du har lagt till NuGet-paketet för Application Insights trace Listener kan du använda system. Diagnostics. trace för att logga undantag [som visas i det här exemplet](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107).

## <a name="performance-counters"></a>Prestandaräknare
Följande räknare samlas in som standard:

* \Process (?? APP_WIN32_PROC??) \% Processor tid
* \Memory\Tillgängliga byte
* \.NET CLR-undantag(??APP_CLR_PROC??)\# undantag som utlöses/sekund
* \Process(??APP_WIN32_PROC??)\Privata byte
* \Process(??APP_WIN32_PROC??)\Byte i I/O-data per sekund
* \Processor(_Total)\% processortid

För webbroller samlas även dessa räknare in:

* \ASP.NET-program(??APP_W3SVC_PROC??)\Begäranden/sek
* \ASP.NET-program(??APP_W3SVC_PROC??)\Körningstid för begäran
* \ASP.NET-program(??APP_W3SVC_PROC??)\Begäranden i tillämpningskö

Du kan ange ytterligare anpassade eller andra Windows-prestandaräknare genom att redigera *ApplicationInsights.config* [som du ser i det här exemplet](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![Prestandaräknare](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Korrelerad telemetri för arbets roller
För en omfattande diagnostik kan du se vad som ledde till en misslyckad eller hög latens-begäran. Med webb roller ställer SDK automatiskt in en korrelation mellan relaterad telemetri. 

För att uppnå den här vyn för arbets roller kan du använda en anpassad telemetri för att ange ett gemensamt Operation.Id-sammanhang för all telemetri. På så sätt kan du snabbt se om svars tiden eller fel problemet orsakades av ett beroende eller koden. 

Gör så här:

* Ange correlationId i ett CallContext [som visas i det här exemplet](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). I det här fallet använder vi fråge-ID som correlationId.
* Lägg till en anpassad TelemetryInitializer-implementering för att ange Operation.Id till det correlationId som angavs tidigare. Ett exempel finns i [ItemCorrelationTelemetryInitializer](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13).
* Lägg till den anpassade telemetriinitieraren. Du kan göra det i *ApplicationInsights.config* -filen eller i koden [som visas i det här exemplet](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233).

## <a name="client-telemetry"></a>Klienttelemetri
Om du vill hämta webbläsarbaserad telemetri, till exempel antal sid visningar, sid inläsnings tider eller skript undantag och skriva anpassad telemetri i dina sid skript, se [Lägg till Java Script SDK på dina webb sidor][client].

## <a name="availability-tests"></a>Tillgänglighetstester
[Konfigurera webbtester][availability]för att se till att appen hålls Live och svarar.

## <a name="display-everything-together"></a>Visa allt tillsammans
För en övergripande bild av systemet kan du Visa viktiga övervaknings diagram på en [instrument panel](./overview-dashboard.md). Du kan till exempel fästa antalet begäranden och fel för varje roll. 

Om systemet använder andra Azure-tjänster, till exempel Stream Analytics, inkluderar du även övervaknings diagrammen. 

Om du har en klientmobilapp använder du [App Center](../app/mobile-center-quickstart.md). Skapa frågor i [Analytics](../logs/log-query-overview.md) för att visa antalet händelser och fäst dem på instrumentpanelen.

## <a name="example"></a>Exempel
[Exemplet](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) övervakar en tjänst som har en webbroll och två arbetsroller.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Undantag "metoden hittades inte" vid körning i Azure Cloud Services
Utvecklade du för .NET 4.6? .NET 4,6 stöds inte automatiskt i Azure Cloud Services-roller. [Installera .net 4,6 på varje roll](../../cloud-services/cloud-services-dotnet-install-dotnet.md) innan du kör din app.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Nästa steg
* [Konfigurera Azure Diagnostics-överföring till Application Insights](../agents/diagnostics-extension-to-application-insights.md)
* [Skapa Application Insights-resurser automatiskt](./powershell.md)
* [Automatisera Azure-diagnostik](./powershell-azure-diagnostics.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: ./api-custom-events-metrics.md
[availability]: ./monitor-web-app-availability.md
[azure]: ./app-insights-overview.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[netlogs]: ./asp-net-trace-logs.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md