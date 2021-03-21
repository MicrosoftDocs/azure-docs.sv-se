---
title: Vad är Azure Application Insights? | Microsoft Docs
description: Application Performance Management och användningsspårning av ditt live-webbprogram.  Identifiera, hantera och diagnostisera problem och förstå hur din app används.
ms.topic: overview
ms.date: 06/03/2019
ms.custom: mvc
ms.openlocfilehash: ac6b6f2b47df0bce9ae164c83df54adfdf00b184
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575707"
---
# <a name="what-is-application-insights"></a>Vad är Application Insights?
Application Insights är en funktion i [Azure Monitor](../overview.md), är en utöknings bar APM-tjänst (Application Performance Management) för utvecklare och DevOps-proffs. Använd den för att övervaka dina Live-program. Prestanda avvikelser identifieras automatiskt och det finns kraftfulla analys verktyg som hjälper dig att diagnostisera problem och förstå vad användare faktiskt gör med din app.  Den hjälper dig att kontinuerligt förbättra prestanda och användbarhet. Det fungerar för appar på en mängd olika plattformar, inklusive .NET, Node.js, Java och python lokalt, hybrid eller något offentligt moln. Den integrerar med din DevOps-process och kan användas med en rad olika utvecklingsverktyg. Den kan övervaka och analysera telemetri från mobilappar genom integrering med Visual Studio App Center.

## <a name="how-does-application-insights-work"></a>Hur fungerar Application Insights?
Du installerar ett litet instrumentande paket (SDK) i programmet eller aktiverar Application Insights med hjälp av Application Insights agenten när det [stöds](./platforms.md). Instrumentation övervakar din app och dirigerar telemetri-data till en Azure Application Insights-resurs med hjälp av ett unikt GUID som vi refererar till som en Instrumentation-nyckel.

Förutom att instrumentera webbtjänstprogrammet kan du även instrumentera bakgrundskomponenter och JavaScript-koden på själva webbsidorna. Programmet och dess komponenter kan köras var som helst – det behöver inte vara värd i Azure.

![Application Insights-instrumentationen i appen skickar telemetri till Application Insights-resursen.](./media/app-insights-overview/diagram.png)

Du kan också hämta telemetri från värdmiljöerna, t.ex. prestandaräknare, Azure Diagnostics eller Docker-loggar. Du kan även konfigurera webbtester som regelbundet skickar syntetiska begäranden till din webbtjänst.

Alla dessa telemetriströmmar är integrerade i Azure Monitor. På Azure-portalen kan du använda kraftfulla analys- och sökverktyg med rådata.

### <a name="whats-the-overhead"></a>Hur påverkas prestanda?
Påverkan på appens prestanda är liten. Anropsspårning är icke-blockerande, och grupperas och skickas i en separat tråd.

## <a name="what-does-application-insights-monitor"></a>Vad övervakar Application Insights?

Application Insights är avsett för utvecklingsteamet och gör det lättare att förstå hur appen fungerar och hur den används. Tjänsten övervakar:

* **Begärandefrekvens, svarstider och felfrekvens** – Ta reda på vilka sidor som är mest populära, vid vilka tidpunkter på dagen och var dina användare finns. Se vilka sidor som fungerar bäst. Om svarstiden och felfrekvensen är hög när det finns många begäranden kan det bero på ett resurstilldelningsproblem. 
* **Beroendefrekvens, svarstider och felfrekvens** – Ta reda på om externa tjänster gör systemet långsammare.
* **Undantag** – Analysera aggregerad statistik eller välj specifika instanser och visa detaljerad information om stackspårningen och relaterade begäranden. Både server- och webbläsarundantag rapporteras.
* **Sidvyer och inläsningsprestanda** – Rapporteras av användarnas webbläsare.
* **AJAX-anrop** från webbsidor – frekvens, svarstider och felfrekvens.
* **Antal användare och sessioner**.
* **Prestandaräknare** från dina Windows- eller Linux-serverdatorer, till exempel processor, minne och nätverksanvändning. 
* **Värddiagnostik** från Docker eller Azure. 
* **Diagnostikspårningsloggar** från din app – så att du kan jämföra spårningshändelser med begäranden.
* **Anpassade händelser och mätvärden** som du skriver själv i klient- eller serverkoden för att spåra affärshändelser, t.ex. sålda artiklar eller vunna spel.

## <a name="where-do-i-see-my-telemetry"></a>Var ser jag min telemetri?

Du kan utforska dina data på många sätt. Läs dessa artiklar:

|  |  |
| --- | --- |
| [**Smart identifiering och manuella aviseringar**](./proactive-diagnostics.md)<br/>Konfigurera automatiska aviseringar som anpassas efter appens normala mönster för telemetri och utlöses när det finns något utanför det vanliga mönstret. Du kan också [ställa in aviseringar](../alerts/alerts-log.md) på särskilda nivåer av anpassade eller standard mått. |![Aviseringsexempel](./media/app-insights-overview/alerts-tn.png) |
| [**Program karta**](./app-map.md)<br/>Utforska komponenterna i appen med viktiga mått och aviseringar. |![Programkarta](./media/app-insights-overview/appmap-tn.png)  |
| [**Profilerare**](./profiler.md)<br/>Granska körningsprofilerna för exempelbegäranden. |![Skärm dum par visar körnings profiler för exempel begär Anden.](./media/app-insights-overview/profiler.png) |
| [**Användnings analys**](./usage-overview.md)<br/>Analysera användarsegment och kvarhållning.|![Kvarhållningsverktyg](./media/app-insights-overview/retention.png) |
| [**Diagnostiksökning efter instansdata**](./diagnostic-search.md)<br/>Sök efter och filtrera händelser, till exempel begäranden, undantag, beroendeanrop, loggspårningar och sidvyer.  |![Telemetrisökning](./media/app-insights-overview/search-tn.png) |
| [**Metrics Explorer för aggregerade data**](../essentials/metrics-charts.md)<br/>Utforska, filtrera och segmentera aggregerade data, till exempel begärande-, fel- och undantagsfrekvens, svarstider och sidinläsningstider. |![Mått](./media/app-insights-overview/metrics-tn.png) |
| [**Instrumentpaneler**](./overview-dashboard.md)<br/>Kombinera data från flera resurser och dela med andra. Perfekt för program med flera komponenter och för kontinuerlig visning i teamutrymmet. |![Exempel på instrumentpaneler](./media/app-insights-overview/dashboard-tn.png) |
| [**Live-ström med mätvärden**](./live-stream.md)<br/>När du distribuerar en ny version kan du titta på dessa prestandaindikatorer som visas i realtid för att kontrollera att allt fungerar som förväntat. |![Exempel på live-mätvärden](./media/app-insights-overview/live-metrics-tn.png) |
| [**Analys**](../logs/log-query-overview.md)<br/>Besvara svåra frågor om appens prestanda och användning med hjälp av det här kraftfulla frågespråket. |![Analytics-exempel](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](./visual-studio.md)<br/>Visa prestandadata i koden. Gå till kod från stackspårningar.|![Skärm bilden visar undantags information i Visual Studio och ett exempel på att gå till kod från stack spår.](./media/app-insights-overview/visual-studio-tn.png) |
| [**Fel sökning av ögonblicks bild**](./snapshot-debugger.md)<br/>Felsök ögonblicksbilder från program som körs med parametervärden.|![Visual Studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](./export-power-bi.md)<br/>Integrera användningsmätvärden med annan Business Intelligence.| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**REST-API**](https://dev.applicationinsights.io/)<br/>Skriv kod för att köra frågor mot dina mätvärden och rådata.| ![REST-API](./media/app-insights-overview/rest-tn.png) |
| [**Löpande export**](./export-telemetry.md)<br/>Massexportera rådata till lagring så fort de anländer. |![Exportera](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>Hur använder jag Application Insights?

### <a name="monitor"></a>Monitor
Installera Application Insights i din app, konfigurera [webbtester för tillgänglighet](./monitor-web-app-availability.md) och:

* Kolla in standard [instrument panelen](./overview-dashboard.md) för ditt teamrum för att hålla ett öga på belastning, svars tider och prestanda för dina beroenden, sid inläsningar och AJAX-anrop.
* Identifiera de begäranden som är långsammast och som misslyckas oftast.
* Titta på [Live Stream](./live-stream.md) när du distribuerar en ny version så att du genast kan upptäcka en eventuell försämring.

### <a name="detect-diagnose"></a>Identifiera och diagnostisera
När du får en avisering eller identifierar ett problem:

* Utvärdera hur många användare som påverkas.
* Korrelera fel med undantag, beroendeanrop och spårningar.
* Undersök profilerare, ögonblicksbilder, stackdumpar och spårningsloggar.

### <a name="build-measure-learn"></a>Bygg, mät och lär
[Mät effektiviteten](./usage-overview.md) för varje ny funktion som du distribuerar.

* Mät hur kunder använder nya användargränssnitts- eller företagsfunktioner.
* Skriv in anpassad telemetri i din kod.
* Basera nästa utvecklingscykel på fakta från din telemetri.

## <a name="get-started"></a>Kom igång
Application Insights är en av de många tjänsterna som finns i Microsoft Azure, och telemetri skickas dit för analys och visning. Innan du gör något annat behöver du alltså en prenumeration på [Microsoft Azure](https://azure.com). Registreringen är gratis och om du väljer den grundläggande [prisplanen](https://azure.microsoft.com/pricing/details/application-insights/) för Application Insights betalar du inget förrän programmet har växt så pass mycket att det har en betydande användning. Om din organisation redan har en prenumeration kan du lägga till ditt Microsoft-konto till den.

Det finns flera sätt att komma igång. Börja på det sätt som passar dig bäst. Du kan fortsätta med de andra senare.

* **Vid körning: instrumentera webbappen på servern.** Perfekt för program som redan har distribuerats. Undviker uppdateringar av koden.
  * [**ASP.NET eller ASP.NET Core program som finns på Azure Web Apps**](./azure-web-apps.md)
  * [**ASP.NET program som finns i IIS på en virtuell Azure-dator eller skalnings uppsättning för virtuella Azure-datorer**](./azure-vm-vmss-apps.md)
  * [**ASP.NET program som finns i den lokala IIS-datorn**](./monitor-performance-live-website-now.md)
* **Vid utveckling: lägg till Application Insights i din kod.** Gör att du kan anpassa telemetri-samlingen och skicka ytterligare telemetri.
  * [ASP.NET-program](./asp-net.md)
  * [ASP.NET Core program](./asp-net-core.md)
  * [.NET-konsol program](./console.md)
  * [Java](./java-get-started.md)
  * [Node.js](./nodejs.md)
  * [Python](./opencensus-python.md)
  * [Andra plattformar](./platforms.md)
* **[Instrumentera dina webbplatser](./javascript.md)** för sidvisning, AJAX och annan telemetri på klientsidan.
* **[Analysera mobilappsanvändning](../app/mobile-center-quickstart.md)** genom integrering med Visual Studio App Center.
* **[Tillgänglighetstester](./monitor-web-app-availability.md)** –pinga din webbplats regelbundet från våra servrar.

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
* [JavaScript](./javascript.md)


## <a name="support-and-feedback"></a>Support och feedback
* Frågor och problem:
  * [Felsökning][qna]
  * [Sidan Microsoft Q&en fråga](/answers/topics/azure-monitor.html)
  * [StackOverflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* Dina förslag:
  * [UserVoice](https://feedback.azure.com/forums/357324-application-insights/filters/top)
* Blogg:
  * [Application Insights-blogg](https://azure.microsoft.com/blog/tag/application-insights)

<!--Link references-->

[android]: ../app/mobile-center-quickstart.md
[azure]: ../../insights-perf-analytics.md
[client]: ./javascript.md
[desktop]: ./windows-desktop.md
[greenbrown]: ./asp-net.md
[ios]: ../app/mobile-center-quickstart.md
[java]: ./java-get-started.md
[knowUsers]: app-insights-web-track-usage.md
[platforms]: ./platforms.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md

