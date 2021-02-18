---
title: Smart identifiering-prestanda avvikelser | Microsoft Docs
description: Application Insights utför Smart analys av din app-telemetri och varnar dig om potentiella problem. Den här funktionen behöver ingen installation.
ms.topic: conceptual
ms.date: 05/04/2017
ms.reviewer: antonfr
ms.openlocfilehash: a055a323001e0291d3cb5c1716e640b3c8b21dbf
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573729"
---
# <a name="smart-detection---performance-anomalies"></a>Smart identifiering-prestanda avvikelser

[Application Insights](./app-insights-overview.md) analyserar automatiskt prestanda för ditt webb program och kan varna dig om potentiella problem. Du kanske läser detta eftersom du har fått ett av våra meddelanden om Smart identifiering.

Den här funktionen kräver ingen särskild konfiguration, förutom att konfigurera din app för Application Insights för ditt [språk som stöds](./platforms.md). Den är aktiv när din app genererar tillräckligt telemetri.

## <a name="when-would-i-get-a-smart-detection-notification"></a>När får jag ett meddelande om Smart identifiering?

Application Insights har upptäckt att programmets prestanda har försämrats på något av följande sätt:

* **Minskning av svars tid** – appen har börjat svara på begär Anden långsammare än den som används. Ändringen kan ha varit snabb, till exempel eftersom det var en regression i din senaste distribution. Eller så kan det vara gradvis, vilket kan orsakas av en minnes läcka. 
* **Beroende varaktighets försämring** – appen gör anrop till ett REST API, en databas eller något annat beroende. Beroendet svarar långsammare än det som används för.
* **Mönster för långsamma prestanda** – appen verkar ha ett prestanda problem som endast påverkar vissa begär Anden. Sidor läses till exempel långsammare på en typ av webbläsare än andra. eller förfrågningar betjänas långsammare från en viss server. För närvarande tittar våra algoritmer på sid inläsnings tider, begär svars tider och beroende svars tider.  

Smart identifiering kräver minst 8 dagars telemetri på en fungerande volym för att kunna upprätta en bas linje för normal prestanda. När ditt program har körts under den perioden resulterar eventuella betydande problem i ett meddelande.


## <a name="does-my-app-definitely-have-a-problem"></a>Har min app definitivt ett problem?

Nej, en avisering innebär inte att din app definitivt har ett problem. Identifieringen är bara ett förslag på något som du kanske vill titta närmare på.

## <a name="how-do-i-fix-it"></a>Vad kan jag göra?

Aviseringarna innehåller diagnostikinformation. Här är ett exempel:


![Här är ett exempel på identifiering av Server svars tids försämring](media/proactive-performance-diagnostics/server_response_time_degradation.png)

1. **Prioritering**. Meddelandet visar hur många användare eller hur många åtgärder som påverkas. Detta kan hjälpa dig att tilldela en prioritet för problemet.
2. **Omfång**. Påverkar problemet all trafik eller bara vissa sidor? Är den begränsad till vissa webbläsare eller platser? Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera**. Den diagnostiska informationen i meddelandet kommer ofta att föreslå problemets typ. Om svars tiden till exempel saktar ned när begär ande frekvensen är hög, som föreslår att servern eller beroendena är överbelastade. 

    Annars öppnar du bladet prestanda i Application Insights. Där hittar du information om [profiler](profiler.md) . Om undantag genereras kan du också försöka med [fel sökning av ögonblicks bild](./snapshot-debugger.md).



## <a name="configure-email-notifications"></a>Konfigurera e-postaviseringar

Meddelanden om Smart identifiering är aktiverade som standard och skickas till dem som har [övervaknings läsare](../../role-based-access-control/built-in-roles.md#monitoring-reader) och [övervaknings deltagare](../../role-based-access-control/built-in-roles.md#monitoring-contributor) åtkomst till den prenumeration där Application Insights resursen finns. Ändra detta genom att antingen klicka på **Konfigurera** i e-postmeddelandet eller öppna Inställningar för smart identifiering i Application Insights. 
  
  ![Inställningar för smart identifiering](media/proactive-performance-diagnostics/smart_detection_configuration.png)
  
  * Du kan använda länken Avbryt **prenumeration** i e-postmeddelandet för smart identifiering om du inte vill ta emot e-postaviseringar.

E-postmeddelanden om Smart identifiering av prestanda avvikelser är begränsade till en e-postadress per dag per Application Insights resurs. E-postmeddelandet skickas endast om det finns minst ett nytt problem som har upptäckts den dagen. Du får inte upprepas upprepade meddelanden. 

## <a name="faq"></a>Vanliga frågor

* *Så kommer Microsoft-personal att titta på mina data?*
  * Nej. Tjänsten är helt automatisk. Endast du får meddelanden. Dina data är [privata](./data-retention-privacy.md).
* *Analyseras alla data som samlats in av Application Insights?*
  * Inte för närvarande. För närvarande analyserar vi svars tid för begäran, beroende svars tid och sid inläsnings tid. En analys av ytterligare mått finns på vår efter släpning.

* Vilka typer av program fungerar detta för?
  * Dessa degraderingar identifieras i alla program som genererar lämplig telemetri. Om du har installerat Application Insights i din webbapp spåras förfrågningar och beroenden automatiskt. Men i backend-tjänster eller andra appar, om du har infogat anrop till [TrackRequest ()](./api-custom-events-metrics.md#trackrequest) eller [TrackDependency](./api-custom-events-metrics.md#trackdependency), kommer Smart identifiering att fungera på samma sätt.

* *Kan jag skapa egna regler för avvikelse identifiering eller anpassa befintliga regler?*

  * Ännu inte, men du kan:
    * [Konfigurera aviseringar](../alerts/alerts-log.md) som anger när ett mått korsar ett tröskelvärde.
    * [Exportera telemetri](./export-telemetry.md) till en [databas](./code-sample-export-sql-stream-analytics.md) eller [till Power BI](./export-power-bi.md), där du kan analysera dem själv.
* *Hur ofta utförs analysen?*

  * Vi kör analysen dagligen på Telemetrin från föregående dag (hela dagen i UTC-tidszonen).
* *Detta ersätter [mått aviseringar](../alerts/alerts-log.md)?*
  * Nej.  Vi åtar mig inte att upptäcka varje beteende som du kan tänka på onormalt.


* *Får jag en påminnelse om jag inte gör något som svar på ett meddelande?*
  * Nej, du får ett meddelande om varje ärende bara en gång. Om problemet kvarstår kommer det att uppdateras i bladet Smart Avkännings matning.
* *Jag förlorade e-postmeddelandet. Var hittar jag meddelanden i portalen?*
  * Klicka på panelen **Smart avkänning** i Application Insights översikt över appen. Där kan du hitta alla meddelanden upp till 90 dagar tillbaka.

## <a name="how-can-i-improve-performance"></a>Hur kan jag förbättra prestanda?
Långsamma och misslyckade svar är en av de största synerna för webbplats användare, som du vet från din egen upplevelse. Det är därför viktigt att åtgärda problemen.

### <a name="triage"></a>Prioritering
Först spelar det någon roll? Om en sida alltid är långsam för att läsas in, men bara 1% av platsens användare någonsin måste titta på den, kanske du har fler viktiga saker att tänka på. Å andra sidan, om bara 1% av användarna öppnar den, men den genererar undantag varje gång, kan det vara värt att undersöka.

Använd instruktionen effekt (berörda användare eller% av trafik) som allmän vägledning, men tänk på att det inte är hela berättelsen. Samla in andra bevis för att bekräfta.

Ta hänsyn till problemets parametrar. Om det är geografi-beroende, ställer du in [tillgänglighets test](./monitor-web-app-availability.md) inklusive regionen: det kan bara finnas nätverks problem i det området.

### <a name="diagnose-slow-page-loads"></a>Diagnostisera långsamma sid inläsningar
Var är problemet? Svarar servern långsamt, är sidan mycket lång eller så måste webbläsaren göra mycket arbete för att kunna visa den?

Öppna bladet mått för webbläsare. Segment visning av sid inläsnings tid för webbläsare visar var tiden kommer. 

* Om **sändnings tiden för begäran** är hög, svarar servern långsamt, eller så är begäran ett inlägg med stor mängd data. Titta på [prestanda måtten](./performance-counters.md) för att undersöka svars tider.
* Konfigurera [beroende spårning](./asp-net-dependencies.md) för att se om det är långsamt på grund av externa tjänster eller databasen.
* Om **mottagnings svar** är dominerande är din sida och dess beroende delar – Java Script, CSS, bilder och så vidare (men inte asynkront laddade data) långa. Konfigurera ett [tillgänglighets test](./monitor-web-app-availability.md)och se till att ange alternativet för att läsa in beroende delar. När du får några resultat öppnar du detaljerna för ett resultat och expanderar det för att se inläsnings tider för olika filer.
* Hög **klient bearbetnings tid** föreslår att skript körs långsamt. Om orsaken inte är uppenbar kan du överväga att lägga till en tids kod och skicka tiderna i trackMetric-anrop.

### <a name="improve-slow-pages"></a>Förbättra långsamma sidor
Det finns en fullt råd om hur du kan förbättra Server svar och sid inläsnings tider, så vi försöker inte upprepa det här. Här följer några tips som du förmodligen redan känner till, bara för att komma ihåg följande:

* Långsam inläsning på grund av stora filer: Läs in skript och andra delar asynkront. Använd skript buntar. Bryt huvud sidan i widgetar som läser in deras data separat. Skicka inte oformaterad gammal HTML för långa tabeller: Använd ett skript för att begära data som JSON eller annat komprimerat format. Fyll sedan i tabellen på plats. Det finns fantastiska ramverk som hjälper dig med allt detta. (De medför också stora skript, naturligtvis.)
* Långsamma Server beroenden: Överväg dina komponenters geografiska platser. Om du till exempel använder Azure måste du kontrol lera att webb servern och databasen finns i samma region. Hämtar frågor mer information än vad de behöver? Skulle cachelagring eller batching hjälpa dig?
* Kapacitets problem: titta på Server måtten för svars tider och antal begär Anden. Om svars tiden är proportionerligt proportionerlig med toppar i antalet begär Anden, är det troligt att servrarna sträcks ut.


## <a name="server-response-time-degradation"></a>Tids försämring för Server svars tid

Meddelandet svars tid för försämring av svar visar att du:

* Svars tiden jämfört med normal svars tid för den här åtgärden.
* Hur många användare som påverkas.
* Genomsnittlig svars tid och 90: e percentils svars tid för den här åtgärden på dagen för identifieringen och 7 dagar innan. 
* Antal av den här åtgärds förfrågan på dagen för identifieringen och 7 dagar innan.
* Korrelation mellan försämring i den här åtgärden och försämringar i relaterade beroenden. 
* Länkar för att hjälpa dig att diagnostisera problemet.
  * Profiler-spår som hjälper dig att visa var åtgärds tiden ägnas (länken är tillgänglig om profilers trace-exempel har samlats in för den här åtgärden under identifierings perioden). 
  * Prestanda rapporter i Metric Explorer där du kan segmentera och tärning tidsintervall/filter för den här åtgärden.
  * Sök efter det här anropet för att visa vissa anrops egenskaper.
  * Felrapporter – om Count > 1 innebär det att det uppstod ett problem i den här åtgärden som kan ha bidragit till prestanda försämringen.

## <a name="dependency-duration-degradation"></a>Försämring av beroende varaktighet

Moderna program fler och fler antar en design metod för mikrotjänster, som i många fall leder till hög tillförlitlighet på externa tjänster. Om ditt program till exempel är beroende av en viss data plattform eller om du skapar en egen bot-tjänst kommer du förmodligen att kunna använda en viss kognitiv tjänst leverantör för att göra det möjligt för robotar att interagera på flera olika sätt och vissa data lagrings tjänster för bot för att hämta svaren från.  

Exempel på meddelande om beroende försämring:

![Här är ett exempel på identifiering av beroende varaktighets försämring](media/proactive-performance-diagnostics/dependency_duration_degradation.png)

Observera att det står:

* Varaktigheten jämfört med normal svars tid för den här åtgärden
* Hur många användare påverkas
* Genomsnittlig varaktighet och 90: e percentils varaktighet för det här beroende dagen för identifieringen och 7 dagar innan
* Antalet beroende anrop på dagen för identifieringen och 7 dagar innan
* Länkar för att hjälpa dig att diagnostisera problemet
  * Prestanda rapporter i Metric Explorer för detta beroende
  * Sök efter de här beroende anropen för att Visa anrops egenskaper
  * Fel rapporter – om Count > 1 innebär det att det inte gick att utföra beroende anrop under den identifierings period som kan ha bidragit till varaktighets försämringen. 
  * Öppna analys med frågor som beräknar den här beroende varaktigheten och antalet  

## <a name="smart-detection-of-slow-performing-patterns"></a>Smart identifiering av mönster med långsamma utföranden 

Application Insights hittar prestanda problem som bara kan påverka vissa delar av dina användare eller endast påverka användare i vissa fall. Till exempel är meddelande om inläsning av sidor långsammare i en viss typ av webbläsare än i andra typer av webbläsare, eller om begär Anden betjänas långsammare från en viss server. Det kan också identifiera problem som är kopplade till kombinationer av egenskaper, till exempel långsamma sid inläsningar i ett geografiskt område för klienter som använder ett visst operativ system.  

Avvikelser som dessa är mycket svåra att identifiera genom att granska data, men är vanligare än vad du kan tänka på. Oftast är de bara en yta när kunderna klagar. Efter den tiden är det för sent: de berörda användarna byter redan till dina konkurrenter!

För närvarande tittar våra algoritmer på sid inläsnings tider, begär svars tider på servern och beroende svars tider.  

Du behöver inte ange några tröskelvärden eller konfigurera regler. Algoritmer för maskin inlärning och data utvinning används för att identifiera onormala mönster.

![Från e-postaviseringen klickar du på länken för att öppna den diagnostiska rapporten i Azure](./media/proactive-performance-diagnostics/03.png)

* **När** visar den tidpunkt då problemet upptäcktes.
* **Vad** beskriver:

  * Det problem som upptäcktes.
  * Egenskaperna för den uppsättning händelser som vi hittade visade problem beteendet.
* Tabellen jämför den dåligt utförda uppsättningen med det genomsnittliga beteendet för alla andra händelser.

Klicka på länkarna om du vill öppna Metric Explorer och söka efter relevanta rapporter, filtrerat efter tid och egenskaper för den långsamma körnings uppsättningen.

Ändra tidsintervallet och filter för att utforska Telemetrin.

## <a name="next-steps"></a>Nästa steg
De här diagnostikverktygen hjälper dig att inspektera Telemetrin från din app:

* [Profilerare](profiler.md) 
* [fel sökning av ögonblicks bild](./snapshot-debugger.md)
* [Analys](../logs/log-analytics-tutorial.md)
* [Analytics-Smart diagnostik](../logs/log-query-overview.md)

Smart identifiering är helt automatisk. Men kanske vill du konfigurera ytterligare aviseringar?

* [Manuellt konfigurerade mått varningar](../alerts/alerts-log.md)
* [Webbtester för tillgänglighet](./monitor-web-app-availability.md)