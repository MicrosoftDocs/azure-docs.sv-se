---
title: Felsökning utan data, Application Insights för .NET
description: Ser du inte data i Azure Application Insights? Prova här.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/21/2020
ms.openlocfilehash: 40fbe4d08676d7cc56478d3740424fccaa7addc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103562203"
---
# <a name="troubleshooting-no-data---application-insights-for-netnet-core"></a>Fel sökning av inga data Application Insights för .NET/.NET Core

## <a name="some-of-my-telemetry-is-missing"></a>En del av min telemetri saknas
*I Application Insights ser jag bara en bråkdel av de händelser som genereras av appen.*

* Om du ständigt ser samma bråk är det förmodligen på grund av adaptiv [sampling](./sampling.md). Bekräfta detta genom att öppna search (från översikts bladet) och titta på en instans av en begäran eller en annan händelse. Längst ned i avsnittet Egenskaper klickar du på "..." för att få fullständig egenskaps information. Om antalet förfrågningar > 1 är samplingen i drift.
* Annars är det möjligt att du [påträffar en data hastighets gräns](./pricing.md#limits-summary) för din pris sättnings plan. Dessa gränser tillämpas per minut.

*Jag upplever data förlust slumpmässigt.*

* Kontrol lera om du har data förlust i [telemetri-kanalen](telemetry-channels.md#does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost)

* Sök efter kända problem i telemetri Channel [GitHub lagrings platsen](https://github.com/Microsoft/ApplicationInsights-dotnet/issues)

*Jag upplever data förlust i konsol programmet eller på en webbapp när appen håller på att stoppas.*

* SDK-kanalen behåller telemetri i bufferten och skickar dem i batchar. Om programmet stängs av kan du behöva anropa [Flush ()](api-custom-events-metrics.md#flushing-data)explicit. Beteendet `Flush()` beror på den faktiska [kanal](telemetry-channels.md#built-in-telemetry-channels) som används.

## <a name="no-data-from-my-server"></a>Inga data från min server
*Jag har installerat min app på min webb server och nu kan jag inte se någon telemetri från den. Den fungerade OK på min dev-dator.*

* Troligen en brand Väggs problem. [Ange brand Väggs undantag för Application Insights att skicka data](./ip-addresses.md).
* Vissa nödvändiga komponenter kanske saknas i IIS-servern: .NET utöknings Bart 4,5 och ASP.NET 4,5.

*Jag har [installerat statusövervakare](./monitor-performance-live-website-now.md) på min webb server för att övervaka befintliga appar. Jag ser inga resultat.*

* Se [fel sökning statusövervakare](./monitor-performance-live-website-now.md#troubleshoot).

> [!IMPORTANT]
> Nya Azure-regioner **kräver** att anslutnings strängar används i stället för instrument knappar. [Anslutnings strängen](./sdk-connection-string.md?tabs=net) identifierar den resurs som du vill associera dina telemetridata med. Du kan också ändra de slut punkter som resursen kommer att använda som mål för din telemetri. Du måste kopiera anslutnings strängen och lägga till den i programmets kod eller till en miljö variabel.


## <a name="filenotfoundexception-could-not-load-file-or-assembly-microsoftaspnet-telemetrycorrelation"></a>FileNotFoundException: det gick inte att läsa in filen eller sammansättningen "Microsoft. ASPNET TelemetryCorrelation

Mer information om det här felet finns i [GitHub Issue 1610] ( https://github.com/microsoft/ApplicationInsights-dotnet/issues/1610) .

När du uppgraderar från SDK: er som är äldre än (2,4) måste du se till att följande ändringar tillämpas på `web.config` och `ApplicationInsights.config` :

1. Två http-moduler i stället för en. I `web.config` bör du ha två http-moduler. Ordningen är viktig för vissa scenarier:

    ``` xml
    <system.webServer>
      <modules>
          <add name="TelemetryCorrelationHttpModule" type="Microsoft.AspNet.TelemetryCorrelation.TelemetryCorrelationHttpModule, Microsoft.AspNet.TelemetryCorrelation" preCondition="integratedMode,managedHandler" />
          <add name="ApplicationInsightsHttpModule" type="Microsoft.ApplicationInsights.Web.ApplicationInsightsHttpModule, Microsoft.AI.Web" preCondition="managedHandler" />
      </modules>
    </system.webServer>
    ```

2. `ApplicationInsights.config`Förutom `RequestTrackingTelemetryModule` bör du ha följande modul för telemetri:

    ``` xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Web.AspNetDiagnosticTelemetryModule, Microsoft.AI.Web"/>
    </TelemetryModules>
    ```

***Om det inte går att uppgradera korrekt kan det leda till oväntade undantag eller telemetri som inte samlas in.***


## <a name="no-add-application-insights-option-in-visual-studio"></a><a name="q01"></a>Alternativet Lägg till Application Insights i Visual Studio
*När jag högerklickar på ett befintligt projekt i Solution Explorer visas inga Application Insights alternativ.*

* Alla typer av .NET-projekt stöds inte av verktygen. Webb-och WCF-projekt stöds. För andra projekt typer, till exempel Skriv bords-eller tjänst program, kan du fortfarande [lägga till en Application Insights SDK i projektet manuellt](./windows-desktop.md).
* Kontrol lera att du har [Visual Studio 2013 Update 3 eller senare](/visualstudio/releasenotes/vs2013-update3-rtm-vs). Det levereras förinstallerat med Developer Analytics-verktyg, som tillhandahåller Application Insights SDK.
* Välj **verktyg**, **tillägg och uppdateringar** och kontrol lera att **Developer Analytics tools** är installerat och aktiverat. Om så är fallet klickar du på **uppdateringar** för att se om det finns en tillgänglig uppdatering.
* Öppna dialog rutan nytt projekt och välj ASP.NET-webbprogram. Om du ser det Application Insights alternativet installeras-verktygen. Annars kan du prova att avinstallera och sedan installera om Developer Analytics-verktygen.

## <a name="adding-application-insights-failed"></a><a name="q02"></a>Det gick inte att lägga till Application Insights
*När jag försöker lägga till Application Insights i ett befintligt projekt visas ett fel meddelande.*

Sannolika orsaker:

* Det gick inte att kommunicera med Application Insights portalen; eller
* Det finns ett problem med ditt Azure-konto.
* Du har bara [Läs behörighet till den prenumeration eller grupp där du försökte skapa den nya resursen](./resources-roles-access-control.md).

Löser

* Kontrol lera att du har angett inloggnings uppgifter för rätt Azure-konto.
* Kontrol lera att du har åtkomst till [Azure Portal](https://portal.azure.com)i webbläsaren. Öppna inställningar och se om det finns någon begränsning.
* [Lägg till Application Insights i ditt befintliga projekt](./asp-net.md): i Solution Explorer högerklickar du på projektet och väljer lägg till Application Insights.

## <a name="i-get-an-error-instrumentation-key-cannot-be-empty"></a><a name="emptykey"></a>Jag får ett fel meddelande om Instrumentation-nyckeln kan inte vara tom
Det verkar som om något gick fel när du installerade Application Insights eller kanske ett loggnings kort.

I Solution Explorer högerklickar du på projektet och väljer **Application Insights > konfigurera Application Insights**. Du får en dialog ruta som bjuder in dig för att logga in på Azure och antingen skapa en Application Insights resurs eller att använda en befintlig.

## <a name="nuget-packages-are-missing-on-my-build-server"></a><a name="NuGetBuild"></a> "NuGet-paket saknas" på min build-Server
*Allt bygger på OK när jag felsöker på min utvecklings dator, men jag får ett NuGet-fel på Build-servern.*

Se [NuGet Package Restore](https://docs.nuget.org/Consume/Package-Restore) och [Automatisk paket återställning](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Meny kommandot som saknas för att öppna Application Insights från Visual Studio
*När jag högerklickar på mitt projekt Solution Explorer ser jag inte några Application Insights-kommandon eller så visas inte kommandot Öppna Application Insights.*

Sannolika orsaker:

* Om du skapade Application Insights resursen manuellt, eller om projektet är av en typ som inte stöds av Application Insightss verktyg.
* Developer Analytics Tools är inaktiverade i Visual Studio.
* Visual Studio är äldre än 2013 uppdatering 3.

Löser

* Se till att Visual Studio-versionen är 2013 uppdatering 3 eller senare.
* Välj **verktyg**, **tillägg och uppdateringar** och kontrol lera att **Developer Analytics tools** är installerat och aktiverat. Om så är fallet klickar du på **uppdateringar** för att se om det finns en tillgänglig uppdatering.
* Högerklicka på ditt projekt i Solution Explorer. Om du ser kommandot **Application Insights > konfigurera Application Insights** använder du det för att ansluta projektet till resursen i Application Insightss tjänsten.

Annars stöds inte projekt typen direkt av Developer Analytics-verktygen. Om du vill se din telemetri loggar du in på [Azure Portal](https://portal.azure.com), väljer Application Insights i det vänstra navigerings fältet och väljer ditt program.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>"Åtkomst nekad" vid öppnande av Application Insights från Visual Studio
*Meny kommandot Öppna Application Insights tar mig till Azure Portal, men jag får ett fel av typen "åtkomst nekad".*

Den Microsoft-inloggning som du senast använde i din standard webbläsare har inte åtkomst till [den resurs som skapades när Application Insights lades till i den här appen](./asp-net.md). Det finns två möjliga orsaker:

* Har du mer än en Microsoft-konto – kanske ett arbete och en personlig Microsoft-konto? Den inloggning som du senast använde i din standard webbläsare var för ett annat konto än den som har åtkomst att [lägga till Application Insights i projektet](./asp-net.md).
  * KORRIGERA: Klicka på ditt namn längst upp till höger i webbläsarfönstret och logga ut. Logga sedan in med det konto som har åtkomst. Klicka sedan på Application Insights i det vänstra navigerings fältet och välj din app.
* Någon annan lade till Application Insights i projektet och glömde till att ge dig [åtkomst till resurs gruppen](./resources-roles-access-control.md) där den skapades.
  * KORRIGERA: om de använde ett organisations konto kan de lägga till dig i teamet. eller så kan de ge dig individuell åtkomst till resurs gruppen.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>"Till gången hittades inte vid öppnande av Application Insights från Visual Studio
*Meny kommandot Öppna Application Insights tar mig till Azure Portal, men jag får ett fel meddelande om att det inte går att hitta till gång.*

Sannolika orsaker:

* Application Insights resursen för ditt program har tagits bort. eller
* Instrumentation-nyckeln har ställts in eller ändrats i ApplicationInsights.config genom att den redige ras direkt, utan att projekt filen uppdaterades.

Instrumentation-nyckeln i ApplicationInsights.config styr var Telemetrin skickas. En rad i projekt filen styr vilken resurs som öppnas när du använder kommandot i Visual Studio.

Löser

* I Solution Explorer högerklickar du på projektet och väljer Application Insights, konfigurerar Application Insights. I dialog rutan kan du antingen välja att skicka telemetri till en befintlig resurs eller skapa en ny. Eller
* Öppna resursen direkt. Logga in på [Azure Portal](https://portal.azure.com), klicka på Application Insights i det vänstra navigerings fältet och välj sedan din app.

## <a name="where-do-i-find-my-telemetry"></a>Var hittar jag min telemetri?
*Jag har loggat in på [Microsoft Azure-portalen](https://portal.azure.com)och jag tittar på Azures start instrument panel. Så var hittar jag mina Application Insights-data?*

* I det vänstra navigerings fältet klickar du på Application Insights och sedan på ditt app-namn. Om du inte har några projekt där måste du [lägga till eller konfigurera Application Insights i ditt webb projekt](./asp-net.md).  
  Där visas några sammanfattnings diagram. Du kan klicka på dem för att se mer information.
* I Visual Studio, medan du felsöker appen, klickar du på knappen Application Insights.

## <a name="no-server-data-or-no-data-at-all"></a><a name="q03"></a> Inga Server data (eller inga data alls)
*Jag körde appen och öppnade Application Insights tjänsten i Microsoft Azure, men i alla diagram visas "Lär dig mer om att samla in..." eller "inte konfigurerad".* Eller, *endast sid visning och användar data, men inga Server data.*

* Kör programmet i fel söknings läge i Visual Studio (F5). Använd programmet så att du kan generera vissa telemetri. Kontrol lera att du kan se händelser som loggats i Visual Studio output-fönstret.  
  ![Skärm bild som visar hur du kör programmet i fel söknings läge i Visual Studio.](./media/asp-net-troubleshoot-no-data/output-window.png)
* Öppna [diagnostisk sökning](./diagnostic-search.md)i Application Insights-portalen. Data visas vanligt vis här först.
* Klicka på knappen Uppdatera. Bladet uppdateras regelbundet, men du kan också göra det manuellt. Uppdaterings intervallet är längre för större tidsintervall.
* Kontrol lera Instrumentation Keys-matchningar. På huvud bladet för din app i Application Insights portal går du till kombinations rutan **Essentials** och tittar på **Instrumentation-tangenten**. Öppna sedan ApplicationInsights.config i projektet i Visual Studio och leta upp `<instrumentationkey>` . Kontrol lera att de två nycklarna är lika. Om inte:  
  * I portalen klickar du på Application Insights och letar efter app-resursen med rätt nyckel. eller
  * I Visual Studio Solution Explorer högerklickar du på projektet och väljer Application Insights, konfigurera. Återställ appen för att skicka telemetri till rätt resurs.
  * Om du inte hittar matchande nycklar kontrollerar du att du använder samma inloggnings uppgifter i Visual Studio som i portalen.
* Titta på Service Health kartan i [Microsoft Azure start-instrumentpanelen](https://portal.azure.com). Om det finns några aviserings indikationer väntar du tills de har kommit tillbaka till OK och stänger sedan och öppnar bladet Application Insights program igen.
* Kontrol lera även [vår status blogg](https://techcommunity.microsoft.com/t5/azure-monitor-status/bg-p/AzureMonitorStatusBlog).
* Skrev du någon kod för SDK för [Server sidan](./api-custom-events-metrics.md) som kan ändra Instrumentation-nyckeln i `TelemetryClient` instanser eller i `TelemetryContext` ? Eller har du skrivit ett [filter eller en samplings konfiguration](./api-filtering-sampling.md) som kan filtrera ut alltför mycket?
* Om du har redigerat ApplicationInsights.config bör du noga kontrol lera konfigurationen av [TelemetryInitializers och TelemetryProcessors](./api-filtering-sampling.md). En felaktigt namngiven typ eller parameter kan orsaka att SDK inte skickar några data.

## <a name="no-data-on-page-views-browsers-usage"></a><a name="q04"></a>Inga data för sid visningar, webbläsare, användning
*Jag ser data i diagram över Server svars tid och server begär Anden, men inga data i sid inläsnings tiden, eller i webb sidor för webbläsare eller användning.*

Data kommer från skript på webb sidorna. 

* Om du har lagt till Application Insights till ett befintligt webb projekt måste [du lägga till skripten manuellt](./javascript.md).
* Se till att Internet Explorer inte visar din webbplats i kompatibilitetsläge.
* Använd webbläsarens fel söknings funktion (F12 på vissa webbläsare och välj sedan nätverk) för att kontrol lera att data skickas till `dc.services.visualstudio.com` .

## <a name="no-dependency-or-exception-data"></a>Inga beroenden eller undantags data
Se [beroende telemetri](./asp-net-dependencies.md) och [telemetri för undantag](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Inga prestanda data
Prestanda data (CPU, IO-taxa och så vidare) är tillgängliga för [Java-webbtjänster](./java-collectd.md), [Windows](./windows-desktop.md)-skrivbordsappar, [IIS-webbappar och-tjänster om du installerar status övervakaren](./monitor-performance-live-website-now.md)och [Azure Cloud Services](./app-insights-overview.md). du hittar det under Inställningar, servrar.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Inga (Server) data sedan jag publicerade appen på min server
* Kontrol lera att du verkligen har kopierat alla Microsoft. ApplicationInsights-dll: er till-servern, tillsammans med Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* I brand väggen kan du behöva [öppna vissa TCP-portar](./ip-addresses.md).
* Om du måste använda en proxyserver för att skicka ut företags nätverket anger du [defaultProxy](/previous-versions/dotnet/netframework-1.1/aa903360(v=vs.71)) i Web.config
* Windows Server 2008: kontrol lera att du har installerat följande uppdateringar: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://web.archive.org/web/20150129090641/http://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Jag har använt för att se data, men har stoppats
* Har du nått din månads kvot för data punkter? Öppna inställningarna/kvoten och prissättningen för att ta reda på det. I så fall kan du uppgradera planen eller betala för ytterligare kapacitet. Se [pris Planen](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Jag ser inte alla data jag förväntar mig
Om ditt program skickar mycket data och du använder Application Insights SDK för ASP.NET-version 2.0.0-beta3 eller senare, kan den [anpassningsbara samplings](./sampling.md) funktionen hantera och bara skicka en procent andel av din telemetri.

Du kan inaktivera det, men det rekommenderas inte. Sampling är utformad så att relaterad telemetri överförs korrekt, i syfte att utföra diagnostiken.

## <a name="client-ip-address-is-0000"></a>Klientens IP-adress är 0.0.0.0

Den 5 2018 februari meddelade vi att vi tog bort en loggning av klientens IP-adress. Detta påverkar inte geo-platsen.

> [!NOTE]
> Om du behöver de tre första oktetterna i IP-adressen kan du använda en [telemetri-initierare](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) för att lägga till ett anpassat attribut.
> Detta påverkar inte data som samlats in före den 5 februari 2018.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Fel geografisk data i telemetri för användare
Dimensionerna stad, region och land härleds från IP-adresser och är inte alltid korrekta. De här IP-adresserna bearbetas först och ändras sedan till 0.0.0.0 för att lagras.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Undantaget ”metoden hittades inte” vid körning i Azure Cloud Services
Utvecklade du för .NET 4.6? 4.6 stöds inte automatiskt i Azure Cloud Services-roller. [Installera 4.6 för varje roll](../../cloud-services/cloud-services-dotnet-install-dotnet.md) innan du kör din app.

## <a name="troubleshooting-logs"></a>Felsöka loggar

Följ de här anvisningarna för att avbilda fel söknings loggar för ditt ramverk.

### <a name="net-framework"></a>.NET Framework

1. Installera paketet [Microsoft. ASPNET. ApplicationInsights. HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) från NuGet. Versionen som du installerar måste matcha den aktuella installerade versionen av `Microsoft.ApplicationInsighs`

2. Ändra applicationinsights.config-filen så att den innehåller följande:

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.FileDiagnosticsTelemetryModule, Microsoft.ApplicationInsights">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    Ditt program måste ha Skriv behörighet till den konfigurerade platsen

3. Starta om processen så att dessa nya inställningar hämtas av SDK

4. Återställ ändringarna när du är färdig.

### <a name="net-core"></a>.NET Core

1. Installera [Application Insights SDK NuGet-paketet för ASP.net Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) -paketet från NuGet. Versionen som du installerar måste matcha den aktuella installerade versionen av `Microsoft.ApplicationInsights` .

   Den senaste versionen av Microsoft. ApplicationInsights. AspNetCore är 2.14.0 och refererar till Microsoft. ApplicationInsights version 2.14.0. Därför bör den version av Microsoft. ApplicationInsights. AspNetCore som ska installeras vara 2.14.0.

2. Ändra `ConfigureServices` metod i `Startup.cs` klassen.:

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    Ditt program måste ha Skriv behörighet till den konfigurerade platsen

3. Starta om processen så att dessa nya inställningar hämtas av SDK

4. Återställ ändringarna när du är färdig.


## <a name="collect-logs-with-perfview"></a><a name="PerfView"></a> Samla in loggar med PerfView
[PerfView](https://github.com/Microsoft/perfview) är ett kostnads fritt verktyg för diagnostik och prestanda analys som hjälper till att isolera CPU, minne och andra problem genom att samla in och visualisera diagnostikinformation från många källor.

Application Insights SDK-loggen EventSource själv fel söknings loggar som kan samlas in av PerfView.

Om du vill samla in loggar hämtar du PerfView och kör det här kommandot:
```cmd
PerfView.exe collect -MaxCollectSec:300 -NoGui /onlyProviders=*Microsoft-ApplicationInsights-Core,*Microsoft-ApplicationInsights-Data,*Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,*Microsoft-ApplicationInsights-Extensibility-DependencyCollector,*Microsoft-ApplicationInsights-Extensibility-HostingStartup,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,*Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,*Microsoft-ApplicationInsights-Extensibility-Web,*Microsoft-ApplicationInsights-Extensibility-WindowsServer,*Microsoft-ApplicationInsights-WindowsServer-Core,*Microsoft-ApplicationInsights-LoggerProvider,*Microsoft-ApplicationInsights-Extensibility-EventSourceListener,*Microsoft-ApplicationInsights-AspNetCore
```

Du kan ändra dessa parametrar efter behov:
- **MaxCollectSec**. Ange den här parametern för att förhindra att PerfView körs på obestämd tid och påverkar serverns prestanda.
- **OnlyProviders**. Ange den här parametern om du endast vill samla in loggar från SDK. Du kan anpassa listan baserat på dina speciella undersökningar. 
- **NoGui**. Ange den här parametern om du vill samla in loggar utan det grafiska användar gränssnittet.


Om du vill ha mer information läser du
- [Prestanda spår registreras med PerfView](https://github.com/dotnet/roslyn/wiki/Recording-performance-traces-with-PerfView).
- [Application Insights händelse källor](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/ETW)

## <a name="collect-logs-with-dotnet-trace"></a>Samla in loggar med dotNet-trace

En alternativ metod för att samla in loggar för fel sökning som kan vara särskilt användbart för Linux-baserade miljöer är [`dotnet-trace`](/dotnet/core/diagnostics/dotnet-trace)

```bash
dotnet-trace collect --process-id <PID> --providers Microsoft-ApplicationInsights-Core,Microsoft-ApplicationInsights-Data,Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,Microsoft-ApplicationInsights-Extensibility-DependencyCollector,Microsoft-ApplicationInsights-Extensibility-HostingStartup,Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,Microsoft-ApplicationInsights-Extensibility-Web,Microsoft-ApplicationInsights-Extensibility-WindowsServer,Microsoft-ApplicationInsights-WindowsServer-Core,Microsoft-ApplicationInsights-LoggerProvider,Microsoft-ApplicationInsights-Extensibility-EventSourceListener,Microsoft-ApplicationInsights-AspNetCore
```

## <a name="how-to-remove-application-insights"></a>Ta bort Application Insights

Lär dig hur du tar bort Application Insights i Visual Studio genom att följa anvisningarna i [artikeln](./remove-application-insights.md)om borttagning.

## <a name="still-not-working"></a>Fungerar fortfarande inte...
* [Sidan Microsoft Q&en fråga för Application Insights](/answers/topics/azure-monitor.html)

