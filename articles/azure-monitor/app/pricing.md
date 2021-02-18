---
title: Hantera användning och kostnader för Azure Application Insights | Microsoft Docs
description: Hantera telemetri volymer och övervaka kostnader i Application Insights.
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: DaleKoetke
ms.author: dalek
ms.date: 2/7/2021
ms.reviewer: mbullwin
ms.openlocfilehash: db5506f1f1fcabf3a922115c24aa64e35b888fbd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573746"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Hantera användning och kostnader för Application Insights

> [!NOTE]
> Den här artikeln beskriver hur du förstår och styr dina kostnader för Application Insights.  En relaterad artikel, [övervaknings användning och uppskattade kostnader](..//usage-estimated-costs.md) beskriver hur du visar användning och uppskattade kostnader i flera Azure-övervakningsfunktioner för olika pris modeller.

Application Insights har utformats för att få allt du behöver för att övervaka tillgänglighet, prestanda och användning av dina webb program, oavsett om de finns på Azure eller lokalt. Application Insights stöder populära språk och ramverk, till exempel .NET, Java och Node.js, och integreras med DevOps-processer och verktyg som Azure DevOps, JIRA och PagerDuty. Det är viktigt att förstå vad som bestämmer kostnaderna för att övervaka dina program. I den här artikeln granskar vi vad som finns på dina program övervaknings kostnader och hur du kan övervaka och kontrol lera dem proaktivt.

Om du har frågor om hur prissättningen fungerar för Application Insights kan du publicera en fråga på vår [sida för Microsoft Q&en fråga](/answers/topics/azure-monitor.html).

## <a name="pricing-model"></a>Prismodell

Prissättningen för [Azure Application insikter][start] är en modell där **du betalar per** användning baserat på data volym som matas in och eventuellt för längre data kvarhållning. Varje Application Insights resurs debiteras som en separat tjänst och bidrar till fakturan för din Azure-prenumeration. Data volymen mäts som storleken på det okomprimerade JSON-datapaketet som tas emot av Application Insights från ditt program. Det finns ingen data volym avgift för att använda [Live Metrics Stream](./live-stream.md).

[Webbtester med flera steg](./availability-multistep.md) debiteras ytterligare en kostnad. Webbtester med flera steg är webbtester som utför en sekvens med åtgärder. Det finns ingen separat avgift för *ping-test* av en enda sida. Telemetri från ping-test och multi-Step-tester debiteras på samma sätt som andra telemetri från din app.

Application Insights alternativet för att [aktivera aviseringar för anpassade mått dimensioner](./pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) kan också generera ytterligare kostnader eftersom detta kan leda till att ytterligare församlings mått skapas. [Lär dig mer](./pre-aggregated-metrics-log-metrics.md) om loggbaserade och föraggregerade mått i Application Insights och om [prissättning](https://azure.microsoft.com/pricing/details/monitor/) för Azure Monitor anpassade mått.

### <a name="workspace-based-application-insights"></a>Arbets yta-baserad Application Insights

För Application Insights resurser som skickar sina data till en Log Analytics arbets yta, som kallas [arbets ytans baserade Application Insights resurser](create-workspace-resource.md), görs faktureringen för data inmatning och kvarhållning av arbets ytan där Application Insights data finns. Detta gör det möjligt för kunderna att använda alla alternativ för Log Analytics [pris modell](../logs/manage-cost-storage.md#pricing-model) som inkluderar kapacitets reservationer förutom att betala per användning. Log Analytics har också fler alternativ för datakvarhållning, inklusive [kvarhållning efter datatyp](../logs/manage-cost-storage.md#retention-by-data-type). Application Insights data typer i arbets ytan får 90 dagars kvarhållning utan avgifter. Användningen av webbtester och aktivering av aviseringar för anpassade mått dimensioner rapporteras fortfarande via Application Insights. Lär dig hur du spårar data inmatnings-och lagrings kostnader i Log Analytics användning [och uppskattade kostnader](../logs/manage-cost-storage.md#understand-your-usage-and-estimate-costs), [Azure Cost Management + fakturerings](../logs/manage-cost-storage.md#viewing-log-analytics-usage-on-your-azure-bill) -och [log Analyticss frågor](#data-volume-for-workspace-based-application-insights-resources). 

## <a name="estimating-the-costs-to-manage-your-application"></a>Beräkna kostnaderna för att hantera ditt program

Om du inte använder Application Insights än kan du använda [pris Kalkylatorn för Azure Monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) för att beräkna kostnaden för att använda Application Insights. Börja med att ange "Azure Monitor" i sökrutan och klicka på den resulterande Azure Monitor panelen. Bläddra nedåt på sidan för att Azure Monitor och välj Application Insights i list rutan typ.  Här kan du ange det antal GB data som du förväntar dig att samla in per månad, så att frågan är hur mycket data som Application Insights Samla in övervakningen av ditt program.

Det finns två sätt att hantera detta: användning av standard övervakning och anpassningsbar sampling, som är tillgängligt i ASP.NET SDK, eller för att beräkna din sannolika data inmatning baserat på vad andra liknande kunder har sett.

### <a name="data-collection-when-using-sampling"></a>Data insamling när du använder sampling

Med ASP.NET SDK: s [anpassningsbara sampling](sampling.md#adaptive-sampling)justeras data volymen automatiskt så att den behålls inom en angiven högsta trafik hastighet för standard Application Insights övervakning. Om programmet genererar en liten mängd telemetri, till exempel vid fel sökning eller på grund av låg användning, kommer objekten inte att släppas av samplings processorn så länge volymen är lägre än de konfigurerade händelserna per sekund. För ett program med hög volym, med standard tröskelvärdet på fem händelser per sekund, begränsar adaptiv sampling antalet dagliga händelser till 432 000. Med en typisk genomsnittlig händelse storlek på 1 KB motsvarar detta 13,4 GB telemetri per 31-dagars månad per nod som är värd för ditt program (eftersom samplingen görs lokal för varje nod.) 

För SDK: er som inte stöder anpassningsbar sampling kan du använda [provtagnings sampling](./sampling.md#ingestion-sampling), som exempel på hur data tas emot av Application Insights baserat på en procent andel av data som ska behållas, eller [sampling av fast pris för ASP.net, ASP.net Core och Java-webbplatser](sampling.md#fixed-rate-sampling) för att minska trafiken som skickas från webb servern och webbläsare

### <a name="learn-from-what-similar-customers-collect"></a>Lär dig från vilka liknande kunder samlar in

I pris Kalkylatorn för Azure-övervakning för Application Insights, om du aktiverar funktionen "beräkna data volym baserat på program aktivitet", kan du ange indata om ditt program (begär Anden per månad och sid visningar per månad, om du vill samla in telemetri på klient sidan) och sedan visar kalkylatorn information om median och 90: e percentils mängden data som samlas in av liknande program. Dessa program sträcker sig över ett Application Insights konfigurations intervall (t. ex. att vissa har standard [sampling](./sampling.md), har ingen sampling osv.), så du har fortfarande kontrollen att minska mängden data som du matar in långt under median nivån med hjälp av sampling. Men det här är en utgångs punkt för att förstå vad andra, liknande kunder ser.

## <a name="understand-your-usage-and-estimate-costs"></a>Förstå din användning och beräkna kostnader

Application Insights är det enkelt att förstå vad dina kostnader sannolikt kommer att baseras på de senaste användnings mönstren. Kom igång genom att gå till sidan **användning och uppskattade kostnader** i Azure Portal för resursen Application Insights:

![Välj prissättning](./media/pricing/pricing-001.png)

A. Granska din data volym för månaden. Detta omfattar alla data som tas emot och bevaras (efter alla [samplingar](./sampling.md)) från Server-och klient program och från tillgänglighets test.  
B. En separat avgift görs för [webbtester med flera steg](./availability-multistep.md). (Detta omfattar inte test av enkel tillgänglighet, som ingår i data volym avgiften.)  
C. Visa data volym trender för den senaste månaden.  
D. Aktivera [sampling](./sampling.md)av data inmatning.
E. Ange daglig data volyms kap.  

(Observera att alla priser som visas i skärm bilder i den här artikeln endast är till exempel syfte. För aktuella priser i din valuta och region, se [Application Insights priser][pricing].)

Om du vill undersöka Application Insightss användningen mer djup, öppnar du sidan **mått** , lägger till måttet "data punkts volym" och väljer sedan alternativet *Använd delning* för att dela data efter "telemetri objekt typ".

Application Insights avgifter läggs till på din Azure-faktura. Du kan se information om din Azure-faktura i avsnittet **Cost Management + fakturering** i Azure Portal eller på [Azures fakturerings Portal](https://account.windowsazure.com/Subscriptions).  [Se nedan](#viewing-application-insights-usage-on-your-azure-bill) för information om hur du använder detta för Application Insights. 

![På den vänstra menyn väljer du fakturering](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>Använda data volym mått
<a id="understanding-ingested-data-volume"></a>

Om du vill veta mer om dina data volymer väljer du **mått** för din Application Insights resurs, lägger till ett nytt diagram. För diagram måttet under **loggbaserade mått** väljer du **data punkts volym**. Klicka på **tillämpa delning** och välj gruppera efter **`Telemetryitem` typ**.

![Använd mått för att titta på data volym](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>Frågor för att förstå data volym information

Det finns två metoder för att undersöka data volymer för Application Insights. Det första använder aggregerad information i `systemEvents` tabellen och den andra använder `_BilledSize` egenskapen som är tillgänglig för varje inmatad händelse. `systemEvents` kommer inte att ha information om data storlek för [arbets ytans baserade-Application-Insights](#data-volume-for-workspace-based-application-insights-resources).

#### <a name="using-aggregated-data-volume-information"></a>Använda information om sammanställd data volym

Du kan till exempel använda `systemEvents` tabellen för att se data volymen som matats in under de senaste 24 timmarna med frågan:

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Om du vill se ett diagram med data volym (i byte) efter datatyp under de senaste 30 dagarna kan du använda:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

Observera att den här frågan kan användas i en [Azure logg avisering](../alerts/alerts-unified-log.md) för att konfigurera aviseringar på data volymer.  

Om du vill veta mer om dina telemetridata, kan vi hämta antalet händelser efter typ med hjälp av frågan:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>Använda data storlek per händelse information

Om du vill ha mer information om källan till dina data volymer kan du använda den `_BilledSize` egenskap som finns på varje inmatad händelse.

Om du till exempel vill titta på vilka åtgärder som genererar mest data volym under de senaste 30 dagarna, kan vi summera `_BilledSize` för alla beroende händelser:

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

#### <a name="data-volume-for-workspace-based-application-insights-resources"></a>Data volym för arbets ytans baserade Application Insights resurser

Om du vill titta på data volym trenderna för alla [arbets ytans baserade Application Insights resurser](create-workspace-resource.md) på en arbets yta under den senaste veckan går du till arbets ytan Log Analytics och kör frågan:

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now())
| summarize sum(_BilledSize) by _ResourceId, bin(TimeGenerated, 1d)
| render areachart
```

Om du vill fråga data volym trenderna efter typ för en speciell arbets yta som baseras på arbets ytans Application Insights resurs, i Log Analytics arbets ytan använder du:

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now())
| where _ResourceId contains "<myAppInsightsResourceName>"
| summarize sum(_BilledSize) by Type, bin(TimeGenerated, 1d)
| render areachart
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Visa Application Insights användning på din Azure-faktura

Azure ger en fantastisk mängd användbara funktioner i [Azure Cost Management + fakturerings](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json) hubben. Med funktionen "cost Analysis" kan du till exempel Visa dina utgifter för Azure-resurser. Genom att lägga till ett filter efter resurs typ (till Microsoft. insikter/komponenter för Application Insights) kan du spåra dina utgifter. Välj sedan "mäta kategori" eller "mätare" för "Gruppera efter".  För Application Insights resurser i de aktuella pris planerna visas de flesta användnings områden som Log Analytics för mätar kategorin eftersom det finns en enda loggar Server del för alla Azure Monitor-komponenter. 

Om du vill ha mer information om din användning kan du [ladda ned information om din användning från Azure-portalen](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal).
I det hämtade kalkyl bladet kan du se användning per Azure-resurs per dag. I det här Excel-kalkylbladet kan du hitta användning från dina Application Insights-resurser genom att först filtrera fram kolumnen "mätnings kategori" för att Visa "Application Insights" och "Log Analytics" och sedan lägga till ett filter i kolumnen "instance ID" som innehåller Microsoft. Insights/komponenter.  De flesta Application Insights användningen rapporteras för mätare med Log Analyticss mätar kategori, eftersom det finns en enda loggar Server del för alla Azure Monitor-komponenter.  Endast Application Insights-resurser på äldre pris nivåer och webbtester med flera steg rapporteras med en mätnings kategori av Application Insights.  Användningen visas i kolumnen "Förbrukat antal" och enheten för varje post visas i kolumnen "enhets mått".  Mer information som hjälper dig att [förstå Microsoft Azure-fakturan](../../cost-management-billing/understand/review-individual-bill.md).

## <a name="managing-your-data-volume"></a>Hantera din data volym

Mängden data som du skickar kan hanteras med hjälp av följande tekniker:

* **Sampling**: du kan använda sampling för att minska mängden telemetri som skickas från servern och klientens appar, med minimal snedvridning av mått. Sampling är det primära verktyget som du kan använda för att justera mängden data som du skickar. Lär dig mer om [samplings funktioner](./sampling.md).

* **Begränsa AJAX-anrop**: du kan [begränsa antalet AJAX-anrop som kan rapporteras](./javascript.md#configuration) i varje sidvy eller inaktivera Ajax-rapportering. Observera att om du inaktiverar AJAX-anrop inaktive ras [JavaScript-korrelation](./javascript.md#enable-correlation).

* **Inaktivera onödiga moduler**: [Redigera ApplicationInsights.config](./configuration-with-applicationinsights-config.md) för att inaktivera insamlings moduler som du inte behöver. Du kan till exempel bestämma att prestanda räknare eller beroende data inte är nödvändiga.

* **Föraggregerade mått**: om du lägger till anrop till TrackMetric i din app kan du minska trafiken genom att använda överlagringen som godkänner beräkningen av medelvärdet och standard avvikelsen för en batch med mått. Eller så kan du använda ett [paket för](https://www.myget.org/gallery/applicationinsights-sdk-labs)församlings sammansättning.
 
* **Dagligt tak**: när du skapar en Application Insights-resurs i Azure Portal anges den dagliga gränsen till 100 GB/dag. När du skapar en Application Insights resurs i Visual Studio är standardvärdet liten (endast 32,3 MB/dag). Standardvärdet för dagligt tak är inställt för att under lätta testning. Det är avsett att användaren får den dagliga gränsen innan du distribuerar appen till produktion. 

    Den största gränsen är 1 000 GB/dag om du inte begär en högre gräns för ett program med hög trafik.
    
    Varnings meddelanden om den dagliga gränsen skickas till konton som är medlemmar i dessa roller för din Application Insights-resurs: "ServiceAdmin", "AccountAdmin", "innehavaradministratör", "ägare".

    Var försiktig när du anger den dagliga gränsen. Avsikten bör vara att *aldrig träffa den dagliga* gränsen. Om du når den dagliga gränsen går data för resten av dagen förlorade och du kan inte övervaka ditt program. Om du vill ändra den dagliga begränsningen använder du alternativet för **daglig volym begränsning** . Du kan komma åt det här alternativet i fönstret **användning och uppskattade kostnader** (detta beskrivs mer detaljerat längre fram i artikeln).
    
    Vi har tagit bort begränsningen för vissa prenumerations typer som har kredit som inte kan användas för Application Insights. Om prenumerationen har en utgifts gräns har den dagliga Cap-dialog rutan instruktioner för att ta bort utgifts gränsen och göra det möjligt att öka den dagliga gränsen för mer än 32,3 MB/dag.
    
* **Begränsning**: begränsning begränsar data hastigheten till 32 000 händelser per sekund, i genomsnitt över 1 minut per Instrumentation-nyckel. Den data mängd som din app skickar utvärderas varje minut. Om det överstiger den per sekund som genomsnitts priset under minuten, vägrar-servern vissa begär Anden. SDK buffrar data och försöker sedan skicka det igen. Den sprider en överspänning på flera minuter. Om din app kontinuerligt skickar data med mer än begränsnings frekvensen kommer vissa data att tas bort. (ASP.NET-, Java-och JavaScript-SDK: er försöker skicka data på det här sättet igen. andra SDK: er kanske bara släpper begränsade data.) Om det uppstår en begränsning visas en varning om att ett meddelande har inträffat.

## <a name="manage-your-maximum-daily-data-volume"></a>Hantera din maximala dagliga data volym

Du kan använda den dagliga volym begränsningen för att begränsa de data som samlas in. Men om höljet uppfylls uppstår en förlust av all telemetri som skickas från ditt program för resten av dagen. Vi *rekommenderar inte* att programmet träffar det dagliga höljet. Du kan inte spåra programmets hälsa och prestanda när den når den dagliga gränsen.

I stället för att använda den dagliga volym gränsen använder du [sampling](./sampling.md) för att justera data volymen till önskad nivå. Använd sedan den dagliga begränsningen endast som "sista utväg" om ditt program inte börjar att skicka mycket större telemetri.

### <a name="identify-what-daily-data-limit-to-define"></a>Identifiera vilka dagliga data gränser som ska definieras

Granska Application Insights användning och beräknade kostnader för att förstå data inmatnings trenden och det dagliga volym taket för att definiera. Det bör övervägas varsamt eftersom du inte kan övervaka dina resurser när gränsen har uppnåtts.

### <a name="set-the-daily-cap"></a>Ange dagligt tak

Om du vill ändra den dagliga belastningen går du till avsnittet **Konfigurera** i Application Insights resurs, på sidan **användning och uppskattade kostnader** och väljer  **dagligt tak**.

![Justera volym begränsningen för daglig telemetri](./media/pricing/pricing-003.png)

Om du vill [ändra den dagliga gränsen via Azure Resource Manager](./powershell.md)är egenskapen som ska ändras `dailyQuota` .  Via Azure Resource Manager kan du också ställa in `dailyQuotaResetTime` och använda det dagliga taket `warningThreshold` .

### <a name="create-alerts-for-the-daily-cap"></a>Skapa aviseringar för det dagliga taket

Application Insights dagliga tak skapar en händelse i Azure aktivitets loggen när de inmatade data volymerna når varnings nivån eller den dagliga gräns nivån.  Du kan [skapa en avisering baserat på dessa aktivitets logg händelser](../alerts/alerts-activity-log.md#create-with-the-azure-portal). Signal namnen för dessa händelser är:

* Varnings tröskeln för dagliga tak för Application Insights komponent har nåtts

* Application Insights komponents dagliga tak har uppnåtts

## <a name="sampling"></a>Samling
[sampling](./sampling.md) är en metod för att minska den hastighet som telemetri skickas till din app, samtidigt som du behåller möjligheten att hitta relaterade händelser under diagnostiska sökningar. Du behåller också korrekta antal händelser.

Sampling är ett effektivt sätt att minska kostnaderna och hålla dig inom din månads kvot. Samplings algoritmen behåller relaterade objekt för telemetri, så om du till exempel använder Sök kan du hitta begäran som är relaterad till ett visst undantag. Algoritmen behåller också de korrekta värdena så att du ser rätt värden i Metric Explorer för begär ande frekvens, undantags frekvens och andra antal.

Det finns flera former av sampling.

* [Adaptiv sampling](./sampling.md) är standard för ASP.NET SDK. Anpassningsbara samplingar justerar automatiskt till den telemetri som appen skickar. Den fungerar automatiskt i SDK: n i din webbapp så att telemetri i nätverket minskas. 
* Inmatnings *sampling* är ett alternativ som fungerar på den plats där telemetri från din app går in i Application Insights tjänsten. Provtagnings sampling påverkar inte mängden telemetri som skickas från din app, men den minskar volymen som behålls av tjänsten. Du kan använda samplings sampling för att minska den kvot som används av telemetri från webbläsare och andra SDK: er.

Om du vill ange insamlings sampling går du till fönstret  **priser** :

![I fönstret kvot och priser väljer du panelen exempel och väljer sedan en samplings fraktion](./media/pricing/pricing-004.png)

> [!WARNING]
> **Data samplings** fönstret styr bara värdet för inmatnings sampling. Den visar inte samplings frekvensen som används av Application Insights SDK i din app. Om den inkommande Telemetrin redan har samplats i SDK: n, används inte insamlings sampling.
>

Använd en [analys fråga](../logs/log-query-overview.md)för att identifiera den faktiska samplings frekvensen, oavsett var den tillämpades. Frågan ser ut så här:

```kusto
requests | where timestamp > ago(1d)
| summarize 100/avg(itemCount) by bin(timestamp, 1h)
| render areachart
```

I varje kvarhållen post `itemCount` anger antalet ursprungliga poster som den representerar. Det är lika med 1 + antalet tidigare borttagna poster.

## <a name="change-the-data-retention-period"></a>Ändra kvarhållningsperioden för data

Standard kvarhållning av Application Insights resurser är 90 dagar. Olika bevarande perioder kan väljas för varje Application Insights resurs. Den fullständiga uppsättningen tillgängliga kvarhållningsperiod är 30, 60, 90, 120, 180, 270, 365, 550 eller 730 dagar. [Lär dig mer](https://azure.microsoft.com/pricing/details/monitor/) om priser för längre data kvarhållning. 

Om du vill ändra kvarhållning går du till sidan **användning och uppskattade kostnader** från Application Insights resursen och väljer alternativet **data kvarhållning** :

![Skärm bild som visar var du kan ändra data lagrings perioden.](./media/pricing/pricing-005.png)

När kvarhållning sänks, finns det en flera dagars respitperiod innan de äldsta data tas bort.

Kvarhållning kan också [ställas in program mässigt med PowerShell](powershell.md#set-the-data-retention) med hjälp av `retentionInDays` parametern. Om du ställer in datakvarhållning på 30 dagar kan du utlösa en omedelbar rensning av äldre data med hjälp av `immediatePurgeDataOn30Days` parametern, vilket kan vara användbart för scenarier som rör kompatibilitet. Den här rensnings funktionen exponeras bara via Azure Resource Manager och bör användas med extrem noggrannhet. Den dagliga återställnings tiden för data volymens tak kan konfigureras med hjälp av Azure Resource Manager för att ange `dailyQuotaResetTime` parametern.

## <a name="data-transfer-charges-using-application-insights"></a>Avgifter för data överföring med Application Insights

Att skicka data till Application Insights kan medföra avgifter för data bandbredd. Som det beskrivs i [prissättnings sidan för Azure bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/)är data överföringen mellan Azure-tjänster som finns i två regioner debiterad som utgående data överföring enligt normal taxa. Inkommande data överföring är kostnads fri. Den här avgiften är dock väldigt liten (några%) jämfört med kostnaderna för Application Insights logg data inmatning. Därför bör du kontrol lera kostnaderna för Log Analytics behöver fokusera på din inmatade data volym och vi har vägledning som hjälper dig att förstå den [här](#managing-your-data-volume).

## <a name="limits-summary"></a>Sammanfattning av gränser

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Inaktivera dagliga Cap-e-postmeddelanden

Om du vill inaktivera de dagliga e-postmeddelandena för volym tak går du till avsnittet **Konfigurera** i Application Insights-resursen och väljer **dagligt tak** i fönstret **användning och uppskattade kostnader** . Det finns inställningar för att skicka e-post när taket har nåtts, samt när en justerbar varnings nivå har uppnåtts. Avmarkera båda rutorna om du vill inaktivera alla dagliga e-postrelaterade volym meddelanden.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Pris nivå för Legacy Enterprise (per nod)

För tidiga antaganden av Azure Application insikter finns det fortfarande två möjliga pris nivåer: Basic och Enterprise. Den grundläggande pris nivån är densamma som beskrivs ovan och är standard nivån. Den innehåller alla funktioner på företags nivå utan extra kostnad. Basic-nivån faktureras främst på mängden data som matas in.

Dessa äldre pris nivåer har bytt namn. Pris nivån för företaget kallas nu **per nod** och den grundläggande pris nivån kallas nu **per GB**. Dessa nya namn används nedan och i Azure Portal.  

Nivån per nod (tidigare företag) har en avgift per nod och varje nod får en daglig data tilldelning. I pris nivån per nod debiteras du för inmatade data utöver den mängd som ingår. Om du använder Operations Management Suite bör du välja nivån per nod. I april 2018 [introducerade](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) vi en ny pris sättnings modell för Azure-övervakning. Den här modellen antar en enkel "betala per användning"-modell i hela portföljen av övervaknings tjänster. Läs mer om den [nya pris sättnings modellen](..//usage-estimated-costs.md).

För aktuella priser i din valuta och region, se [Application Insights priser](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="understanding-billed-usage-on-the-legacy-enterprise-per-node-tier"></a>Om fakturerad användning på den äldre nivån för Enterprise (per nod) 

Som beskrivs nedan i detalj är den äldre nivån för Enterprise (per nod) att kombinera användning från alla Application Insights resurser i en prenumeration för att beräkna antalet noder och överförbrukningen av data. På grund av den här kombinations processen **rapporteras användningen för alla Application Insights resurser i en prenumeration mot bara en av resurserna**.  Detta gör att du kan synkronisera den [fakturerade användningen](https://docs.microsoft.com/azure/azure-monitor/app/pricing#viewing-application-insights-usage-on-your-azure-bill) med den användning som du ser för varje Application Insights resurser mycket komplicerade. 

> [!WARNING]
> På grund av komplexiteten vid spårning och förståelse av användningen av Application Insights resurser på den äldre Enterprise-nivån (per nod) rekommenderar vi starkt att du använder den nuvarande pris nivån betala per användning. 

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Per nod-och Operations Management Suite-prenumerations rättigheter

Kunder som köper Operations Management Suite E1 och E2 kan få Application Insights per nod som ytterligare en komponent utan extra kostnad som [tidigare](/archive/blogs/msoms/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription)meddelats. Mer specifikt innehåller varje enhet av Operations Management Suite E1 och E2 en rättighet till en nod i Application Insights per nod-nivå. Varje Application Insights nod innehåller upp till 200 MB data som matas in per dag (separat från Log Analytics data inmatning), med 90 dagars data kvarhållning utan extra kostnad. Nivån beskrivs mer detaljerat längre fram i artikeln.

Eftersom den här nivån bara gäller för kunder med en Operations Management Suite-prenumeration, ser kunder som inte har någon Operations Management Suite-prenumeration inget alternativ för att välja den här nivån.

> [!NOTE]
> För att säkerställa att du får den här behörigheten måste Application Insights-resurserna finnas på pris nivån per nod. Den här rättigheten gäller endast för noder. Application Insights resurser på nivån per GB inte inser någon förmån. Den här rättigheten visas inte i de beräknade kostnader som visas i fönstret **användning och uppskattad kostnad** . Om du flyttar en prenumeration till den nya pris sättnings modellen för Azure-övervakning i april 2018 är nivån per GB den enda tillgängliga nivån. Att flytta en prenumeration till den nya pris sättnings modellen för Azure-övervakning är inte lämpligt om du har en Operations Management Suite-prenumeration.

### <a name="how-the-per-node-tier-works"></a>Så här fungerar nivån per nod

* Du betalar för varje nod som skickar telemetri för alla appar i per nod-nivå.
  * En *nod* är en fysisk eller virtuell serverdator eller en instans av en tjänst roll som är värd för din app.
  * Utvecklings datorer, klient webbläsare och mobila enheter räknas inte som noder.
  * Om din app har flera komponenter som skickar telemetri, till exempel en webb tjänst och en backend-anställd, räknas komponenterna separat.
  * [Live Metrics Stream](./live-stream.md) data räknas inte i prissättnings syfte. I en prenumeration är dina avgifter per nod, inte per app. Om du har fem noder som skickar telemetri för 12 appar, är avgiften för fem noder.
* Även om avgifter offereras per månad debiteras du bara för en timme där en nod skickar telemetri från en app. Tim avgiften är den noterade månads kostnaden dividerat med 744 (antalet timmar under en månad på 31 dagar).
* En data volym tilldelning på 200 MB per dag anges för varje nod som identifieras (med en timkostnad). Oanvänd data tilldelning överförs inte från en dag till nästa.
  * Om du väljer pris nivån per nod får varje prenumeration en daglig mängd data baserat på antalet noder som skickar telemetri till Application Insights resurser i den prenumerationen. Så om du har fem noder som skickar data hela dagen har du en pool med 1 GB som tillämpas på alla Application Insights resurser i den prenumerationen. Det spelar ingen roll om vissa noder skickar mer data än andra noder eftersom inkluderade data delas mellan alla noder. Om den Application Insights resurserna tar emot mer data än vad som ingår i den dagliga data tilldelningen för den här prenumerationen, gäller data avgifter per GB överanvändning. 
  * Den dagliga data tilldelningen beräknas som antalet timmar under dagen (med UTC) som varje nod skickar telemetri dividerat med 24 multiplicerat med 200 MB. Så om du har fyra noder som skickar telemetri under 15 av dygnets 24 timmar skulle de inkluderade data för den dagen vara ((4 &#215; 15)/24) &#215; 200 MB = 500 MB. Till priset 2,30 USD per GB för data överanvändning skulle avgiften bli 1,15 USD om noderna skickar 1 GB data som dag.
  * Dag traktamentet per nod delas inte med program som du har valt nivån per GB för. Outnyttjad justering överförs inte från dag till dag.

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Exempel på hur du bestämmer antalet distinkta noder

| Scenario                               | Totalt antal dagliga noder |
|:---------------------------------------|:----------------:|
| 1 program med 3 Azure App Service instanser och 1 virtuell server | 4 |
| 3 program som körs på två virtuella datorer; Application Insights resurser för dessa program finns i samma prenumeration och i per nod-nivå | 2 | 
| 4 program vars program Insights-resurser finns i samma prenumeration. varje program som kör två instanser under 16 timmar med låg belastning och fyra instanser under 8 högsta timmar | 13,33 |
| Moln tjänster med 1-arbets roll och 1 webbroll, varje som kör 2 instanser | 4 | 
| Ett Azure Service Fabric-kluster med 5 noder som kör 50 mikrotjänster; varje mikrotjänst som kör 3 instanser | 5|

* Den exakta beräkningen av noder beror på vilken Application Insights SDK ditt program använder. 
  * I SDK-versionerna 2,2 och senare rapporterar både Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) och [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) rapporten varje program värd som en nod. Exempel är dator namnet för fysiska servrar och virtuella dator värdar eller instans namnet för Cloud Services.  Det enda undantaget är ett program som bara använder [.net Core](https://dotnet.github.io/) och Application Insights Core SDK. I så fall rapporteras bara en nod för alla värdar eftersom värd namnet inte är tillgängligt.
  * För tidigare versioner av SDK fungerar [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) som nyare SDK-versioner, men [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) rapporterar bara en nod, oavsett antalet program värdar.
  * Om programmet använder SDK: n för att ange **roleInstance** till ett anpassat värde, används som standard samma värde för att fastställa antalet noder.
  * Om du använder en ny SDK-version med en app som kör från klient datorer eller mobila enheter kan antalet noder returnera ett tal som är stort (på grund av det stora antalet klient datorer eller mobila enheter).

## <a name="automation"></a>Automation

Du kan skriva ett skript för att ställa in pris nivån med hjälp av Azure Resource Management. [Lär dig hur](powershell.md#price).

## <a name="next-steps"></a>Nästa steg

* [ta](./sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ./app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
