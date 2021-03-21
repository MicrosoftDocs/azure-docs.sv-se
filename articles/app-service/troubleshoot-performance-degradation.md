---
title: Felsöka prestanda försämring
description: Lär dig hur du felsöker problem med långsam program prestanda i Azure App Service, inklusive övervakning av appens beteende, insamling av data och åtgärder för att åtgärda problemet.
tags: top-support-issue
keywords: webb program prestanda, långsam app, appen är långsam
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.topic: article
ms.date: 08/03/2016
ms.custom: seodec18
ms.openlocfilehash: e048e4c17092c71a5adaf29f342546b9258837c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "100586235"
---
# <a name="troubleshoot-slow-app-performance-issues-in-azure-app-service"></a>Felsök problem med långsam program prestanda i Azure App Service
Den här artikeln hjälper dig att felsöka problem med långsam program prestanda i [Azure App Service](./overview.md).

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Alternativt kan du också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och klicka på alternativet för att **få support**.

## <a name="symptom"></a>Symptom
När du bläddrar i appen inaktive ras sidorna långsamt och ibland tids gräns.

## <a name="cause"></a>Orsak
Det här problemet orsakas ofta av problem på program nivå, till exempel:

* nätverks förfrågningar tar lång tid
* program kod eller databas frågor är ineffektiva
* program som använder högt minne/CPU
* programmet kraschar på grund av ett undantag

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
Fel sökning kan delas upp i tre olika uppgifter i nummerordning:

1. [Observera och övervaka programmets beteende](#observe)
2. [Samla in data](#collect)
3. [Åtgärda problemet](#mitigate)

[App Service](overview.md) ger dig olika alternativ i varje steg.

<a name="observe"></a>

### <a name="1-observe-and-monitor-application-behavior"></a>1. Observera och övervaka programmets beteende
#### <a name="track-service-health"></a>Spåra tjänstens hälsa
Microsoft Azure publicizes varje gången det uppstår avbrott i tjänsten eller prestanda försämring. Du kan spåra hälso tillståndet för tjänsten på [Azure Portal](https://portal.azure.com/). Mer information finns i [spåra tjänstens hälsa](../service-health/service-notifications.md).

#### <a name="monitor-your-app"></a>Övervaka din app
Med det här alternativet kan du ta reda på om ditt program har problem. Klicka på panelen **förfrågningar och fel** i appens blad. Bladet **mått** visar alla mått som du kan lägga till.

Några av de mått som du kanske vill övervaka för din app är

* Genomsnittlig arbets mängd för minne
* Genomsnittlig svars tid
* CPU-tid
* Minnes arbets mängd
* Begäranden

![övervaka app-prestanda](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Mer information finns i:

* [Övervaka appar i Azure App Service](web-sites-monitor.md)
* [Få varningsmeddelanden](../azure-monitor/alerts/alerts-overview.md)

#### <a name="monitor-web-endpoint-status"></a>Övervaka webb slut punkts status
Om du kör din app på **standard** pris nivån kan App Service övervaka två slut punkter från tre geografiska platser.

Slut punkts övervakning konfigurerar webbtester från geo-distribuerade platser som testar svars tid och drift tid för webb-URL: er. Testet utför en HTTP GET-åtgärd på webb-URL: en för att fastställa svars tid och drift tid från varje plats. Varje konfigurerad plats kör ett test var femte minut.

Drift tiden övervakas med HTTP-svars koder och svars tid mäts i millisekunder. Ett övervaknings test Miss lyckas om HTTP-svarskod är större än eller lika med 400 eller om svaret tar mer än 30 sekunder. En slut punkt anses tillgänglig om övervaknings testen lyckas från alla angivna platser.

Information om hur du konfigurerar den finns [i övervaka appar i Azure App Service](web-sites-monitor.md).

Se även att [hålla Azure Web Sites upp och slut punkts övervakning – med Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) för en video om slut punkts övervakning.

#### <a name="application-performance-monitoring-using-extensions"></a>Övervakning av program prestanda med hjälp av tillägg
Du kan också övervaka dina program prestanda genom att använda ett *webbplats tillägg*.

Varje App Service app tillhandahåller en utöknings bar hanterings slut punkt som gör att du kan använda en kraftfull uppsättning verktyg som distribueras som webbplats tillägg. Tilläggen omfattar: 

- Käll kods redigerare som [Azure-DevOps](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- Hanterings verktyg för anslutna resurser, till exempel en MySQL-databas som är ansluten till en app.

[Azure Application Insights](https://azure.microsoft.com/services/application-insights/) är en webbplats för prestanda övervakning som också är tillgänglig. Om du vill använda Application Insights återbygger du koden med ett SDK. Du kan också installera ett tillägg som ger åtkomst till ytterligare data. Med SDK kan du skriva kod för att övervaka användning och prestanda för din app i detalj. Mer information finns i [övervaka prestanda i webb program](../azure-monitor/app/app-insights-overview.md).

<a name="collect"></a>

### <a name="2-collect-data"></a>2. samla in data
App Service innehåller diagnostiska funktioner för loggning av information från både webb servern och webb programmet. Informationen är uppdelad i Web Server Diagnostics och Application Diagnostics.

#### <a name="enable-web-server-diagnostics"></a>Aktivera diagnostik för webb server
Du kan aktivera eller inaktivera följande typer av loggar:

* **Detaljerad fel loggning** -detaljerad fel information för HTTP-statuskod som indikerar ett fel (status kod 400 eller senare). Detta kan innehålla information som kan hjälpa dig att avgöra varför servern returnerade felkoden.
* **Spårning av misslyckade begär** Anden-detaljerad information om misslyckade förfrågningar, inklusive spårning av IIS-komponenter som används för att bearbeta begäran och den tid som tagits i varje komponent. Detta kan vara användbart om du försöker förbättra appens prestanda eller isolera vad som orsakar ett särskilt HTTP-fel.
* **Webb Server loggning** – information om http-transaktioner med utökat logg fils format för W3C. Detta är användbart när du fastställer generella app-mått, till exempel antalet begär Anden som hanteras eller hur många begär Anden som kommer från en speciell IP-adress.

#### <a name="enable-application-diagnostics"></a>Aktivera Application Diagnostics
Det finns flera alternativ för att samla in program prestanda data från App Service, profilera ditt program Live från Visual Studio eller ändra program koden för att logga mer information och spårningar. Du kan välja alternativ baserat på hur mycket åtkomst du har till programmet och vad du har observerat från övervaknings verktygen.

##### <a name="use-application-insights-profiler"></a>Använd Application Insights Profiler
Du kan aktivera Application Insights Profiler för att börja samla in detaljerade prestanda spårningar. Du kan komma åt spårningar som registrerats upp till fem dagar sedan när du behöver undersöka problem som har inträffat tidigare. Du kan välja det här alternativet så länge du har åtkomst till appens Application Insights resurs på Azure Portal.

Application Insights Profiler innehåller statistik över svars tider för varje webb anrop och spår som anger vilken kodrad som orsakade långsamma svar. Ibland är App Services appen långsam eftersom viss kod inte är skriven på ett utfört sätt. Exempel är sekventiell kod som kan köras parallellt och oönskade databas lås. Om du tar bort dessa Flask halsar i koden ökar appens prestanda, men det är svårt att identifiera utan att ställa in avancerade spår och loggar. De spår som samlas in av Application Insights Profiler hjälper till att identifiera de kodrader som saktar ned programmet och kan lösa den här utmaningen för App Service appar.

 Mer information finns i [profilering av Live Apps i Azure App Service med Application Insights](../azure-monitor/app/profiler.md).

##### <a name="use-remote-profiling"></a>Använd fjär profilering
I Azure App Service kan webbappar, API-appar, mobila Server delar och WebJobs fjärrlagras. Välj det här alternativet om du har åtkomst till app-resursen och du vet hur du kan återskapa problemet, eller om du vet det exakta tidsintervallet som prestanda problemet inträffar.

Fjärrprofilering är användbart om processor användningen för processen är hög och processen körs långsammare än förväntat, eller om svars tiden för HTTP-begäranden är högre än normalt, kan du fjärrregistrera din process och få CPU-anrop till anrops stackar för att analysera process aktivitet och kod aktiva sökvägar.

Mer information finns i [stöd för fjär profilering i Azure App Service](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

##### <a name="set-up-diagnostic-traces-manually"></a>Konfigurera diagnostiska spårningar manuellt
Om du har åtkomst till käll koden för webb program kan du använda Application Diagnostics för att avbilda information som produceras av ett webb program. ASP.NET-program kan använda- `System.Diagnostics.Trace` klassen för att logga information till Application Diagnostics-loggen. Du måste dock ändra koden och distribuera programmet igen. Den här metoden rekommenderas om din app körs i en test miljö.

Detaljerade anvisningar om hur du konfigurerar programmet för loggning finns i [Aktivera diagnostikloggning för appar i Azure App Service](troubleshoot-diagnostic-logs.md).

#### <a name="use-the-diagnostics-tool"></a>Använda verktyget diagnostik
App Service ger en intelligent och interaktiv upplevelse som hjälper dig att felsöka din app utan att det krävs någon konfiguration. När du stöter på problem med din app, pekar diagnostikverktyget ut vad som är fel för att hjälpa dig med rätt information för att enklare och snabbare felsöka och lösa problemet.

Om du vill komma åt App Service diagnostik navigerar du till din App Service app eller App Service-miljön i [Azure Portal](https://portal.azure.com). Klicka på **diagnostisera och lös problem** i det vänstra navigerings fältet.

#### <a name="use-the-kudu-debug-console"></a>Använda fel söknings konsolen kudu
App Service levereras med en fel söknings konsol som du kan använda för att felsöka, utforska, ladda upp filer, samt JSON-slutpunkter för att få information om din miljö. Den här konsolen kallas *kudu-konsolen* eller *SCM-instrumentpanelen* för din app.

Du kan komma åt den här instrument panelen genom att gå till länken **https:// &lt; ditt program namn>. scm.azurewebsites.net/**.

Några av de saker som kudu tillhandahåller är:

* miljö inställningar för ditt program
* logg ström
* diagnostisk dump
* fel söknings konsolen där du kan köra PowerShell-cmdlets och Basic DOS-kommandon.

En annan användbar funktion i kudu är att, om ditt program ska leda till undantag i första chansen, kan du använda kudu och SysInternals-verktyget ProcDump för att skapa minnes dum par. Dessa minnes dum par är ögonblicks bilder av processen och kan ofta hjälpa dig att felsöka mer komplicerade problem med din app.

Mer information om vilka funktioner som är tillgängliga i kudu finns i [Azure DevOps-verktyg som du bör känna](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/)till.

<a name="mitigate"></a>

### <a name="3-mitigate-the-issue"></a>3. åtgärda problemet
#### <a name="scale-the-app"></a>Skala appen
I Azure App Service kan du, för bättre prestanda och data flöde, ändra skalan då du kör programmet. Att skala upp en app innefattar två relaterade åtgärder: att ändra App Service plan till en högre pris nivå och konfigurera vissa inställningar när du har bytt till den högre pris nivån.

Mer information om skalning finns i [skala en app i Azure App Service](manage-scale-up.md).

Du kan också välja att köra programmet på fler än en instans. Genom att skala ut får du inte bara mer processor kapacitet, men du får också en viss mängd fel tolerans. Om processen går ned på en instans fortsätter de andra instanserna att betjäna förfrågningar.

Du kan ställa in skalningen på manuell eller automatisk.

#### <a name="use-autoheal"></a>Använd autoläka
Autoreparation återanvänder arbets processen för din app baserat på de inställningar du väljer (t. ex. konfigurations ändringar, begär Anden, minnesbaserade gränser eller tiden som krävs för att utföra en begäran). Det mesta av tiden är att återvinna processen är det snabbaste sättet att återställa från ett problem. Även om du alltid kan starta om appen direkt i Azure Portal gör autoläka det automatiskt åt dig. Allt du behöver göra är att lägga till vissa utlösare i rot web.config för din app. De här inställningarna fungerar på samma sätt även om ditt program inte är en .NET-app.

Mer information finns i [Automatisk återställning av Azure Web Sites](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Starta om appen
Att starta om är ofta det enklaste sättet att återställa från engångs problem. På sidan [Azure Portal](https://portal.azure.com/)har du möjlighet att stoppa eller starta om din app på bladet för din app.

 ![Starta om appen för att lösa prestanda problem](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Du kan också hantera din app med hjälp av Azure PowerShell. Mer information finns i [Använda Azure PowerShell med Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md).