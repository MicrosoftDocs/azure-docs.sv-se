---
title: Logg aviseringar i Azure Monitor
description: Utlös e-post, meddelanden, anropa webb adresser för webbplatser (Webhooks) eller automatisering när det logg frågevillkor som du anger är uppfyllt
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.subservice: alerts
ms.openlocfilehash: 88643663c2f14cb7d8883eb1210bdee07b00eece
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100621911"
---
# <a name="log-alerts-in-azure-monitor"></a>Logg aviseringar i Azure Monitor

## <a name="overview"></a>Översikt

Logg aviseringar är en av de aviserings typer som stöds i [Azure-aviseringar](../platform/alerts-overview.md). Logg aviseringar gör att användare kan använda en [Log Analytics](../log-query/log-analytics-tutorial.md) fråga för att utvärdera resurser loggar varje uppsättnings frekvens och utlösa en avisering baserat på resultaten. Regler kan utlösa en eller flera åtgärder med hjälp av [Åtgärds grupper](../platform/action-groups.md).

> [!NOTE]
> Loggdata från en [Log Analytics arbets yta](../log-query/log-analytics-tutorial.md) kan skickas till Azure Monitor Metrics-lagret. Mått aviseringar har [olika beteende](alerts-metric-overview.md), vilket kan vara mer önskvärt beroende på vilka data du arbetar med. Information om vad och hur du kan skicka loggar till mått finns i [mått avisering för loggar](alerts-metric-logs.md).

> [!NOTE]
> Det finns för närvarande inga ytterligare avgifter för API-versionen `2020-05-01-preview` och resursbaserade logg aviseringar.  Prissättningen för funktioner som finns i förhands granskning kommer att meddelas i framtiden och ett meddelande som visas innan faktureringen påbörjas. Om du väljer att fortsätta använda nya API-versioner och resursbaserade logg aviseringar efter meddelande perioden debiteras du enligt tillämplig taxa.

## <a name="prerequisites"></a>Förutsättningar

Logg aviseringar kör frågor om Log Analytics data. Först ska du börja [samla in loggdata](../platform/resource-logs.md) och skicka frågor till loggdata. Du kan använda [avsnittet exempel på aviserings frågor](../log-query/example-queries.md) i Log Analytics för att förstå vad du kan identifiera eller [komma igång med att skriva en egen fråga](../log-query/log-analytics-tutorial.md).

[Azure Monitoring Contributor](../platform/roles-permissions-security.md) är en gemensam roll som behövs för att skapa, ändra och uppdatera logg aviseringar. Du måste också ha åtkomst till & frågans körnings rättigheter för resurs loggarna. Partiell åtkomst till resurs loggar kan inte utföra frågor eller returnera ofullständiga resultat. [Läs mer om hur du konfigurerar logg aviseringar i Azure](./alerts-log.md).

> [!NOTE]
> Logg aviseringar för Log Analytics som används för att hanteras med hjälp av den äldre [Log Analytics varnings-API: et](../platform/api-alerts.md). [Läs mer om att växla till det aktuella ScheduledQueryRules-API: et](../alerts/alerts-log-api-switch.md).

## <a name="query-evaluation-definition"></a>Utvärderings definition för fråga

Villkors definitionen för loggs öknings regler börjar från:

- Vilken fråga ska köras?
- Hur använder du resultaten?

I följande avsnitt beskrivs olika parametrar som du kan använda för att ställa in ovanstående logik.

### <a name="log-query"></a>Logg fråga
Den [Log Analytics](../log-query/log-analytics-tutorial.md) -fråga som används för att utvärdera regeln. Resultaten som returneras av den här frågan används för att avgöra om en avisering ska utlösas. Frågan kan begränsas till:

- En speciell resurs, till exempel en virtuell dator.
- En resurs vid skalning, till exempel en prenumeration eller resurs grupp.
- Flera resurser som använder [frågor över flera resurser](../log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights). 
 
> [!IMPORTANT]
> Aviserings frågor har begränsningar för att säkerställa optimala prestanda och relevansen för resultaten. [Mer information finns här](./alerts-log-query.md).

> [!IMPORTANT]
> Resurs oberoende och [kors resurs fråga](../log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) stöds bara med den aktuella scheduledQueryRules-API: et. Om du använder det äldre [Log Analytics varnings-API: et](../platform/api-alerts.md)måste du växla. [Läs mer om att växla](./alerts-log-api-switch.md)

#### <a name="query-time-range"></a>Tidsintervall för fråga

Tidsintervallet anges i regel villkors definitionen. I arbets ytor och Application Insights kallas den **period**. I alla andra resurs typer kallas det för att **åsidosätta tidsintervallet för frågor**.

Precis som i Log Analytics begränsar tidsintervallet frågedata till det angivna intervallet. Även om kommandot **sedan** används i frågan, kommer tidsintervallet att gälla.

En fråga skannar till exempel 60 minuter, när tidsintervallet är 60 minuter, även om texten innehåller **sedan sedan (1d)**. Tidsintervallet och tids filtreringen för frågan måste matcha. I exempel fallet kan det   /  vara förväntat att ändra **tidsintervallet** för tids perioden för tids perioden till en dag.

### <a name="measure"></a>Mått

Logg aviseringar förvandlas till numeriska värden som kan utvärderas. Du kan mäta två olika saker:

#### <a name="count-of-the-results-table-rows"></a>Antal rader i resultat tabellen

Antal resultat är standard måttet. Idealisk för att arbeta med händelser som Windows-händelseloggar, syslog, program undantag. Utlöses när logg poster sker eller inte inträffar i fönstret utvärderad tid.

Logg aviseringar fungerar bäst när du försöker identifiera data i loggen. Det fungerar mindre bra när du försöker identifiera avsaknad av data i loggarna. Till exempel avisering om pulsslag för virtuella datorer.

För arbets ytor och Application Insights anropas det **baserat på** med urvals **antalet resultat**. I alla andra resurs typer kallas det **mått** med markerings **tabell rader**.

> [!NOTE]
> Eftersom loggar är halv strukturerade data är de mycket mer latenta än måttet, men du kan stöta på fel vid försök att identifiera brist på data i loggarna och du bör överväga att använda [mått varningar](alerts-metric-overview.md). Du kan skicka data till mått lagret från loggar med hjälp av [mått aviseringar för loggar](alerts-metric-logs.md).

##### <a name="example-of-results-table-rows-count-use-case"></a>Exempel på resultat tabell rader antal användnings fall

Du vill veta när ditt program svarar med felkoden 500 (internt Server fel). Du skapar en varnings regel med följande information:

- **Frågeterm** 

```Kusto
requests
| where resultCode == "500"
```

- **Tids period/agg regerings kornig het:** 15 minuter
- **Aviserings frekvens:** 15 minuter
- **Tröskel värde:** Större än 0

Sedan övervakar aviserings regler för förfrågningar som slutar med 500-felkoden. Frågan körs var 15: e minut under de senaste 15 minuterna. Om även en post hittas utlöses aviseringen och de åtgärder som har kon figurer ATS utlöses.

#### <a name="calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value"></a>Beräkning av mått baserat på en numerisk kolumn (till exempel värde för processor räknare)

För arbets ytor och Application Insights, anropas det **baserat på** med val **mått mått**. I alla andra resurs typer kallas det **mått** med val av ett tal kolumn namn.

### <a name="aggregation-type"></a>Sammansättningstyp

Beräkningen som görs på flera poster för att aggregera dem till ett numeriskt värde. Exempel:
- **Count** returnerar antalet poster i frågan
- **Medelvärde** returnerar medelvärdet för mått kolumnen [**agg regerings granularitet**](#aggregation-granularity) definierad.

I arbets ytor och Application Insights stöds det bara i mått mått typen **mått** . Frågeresultatet måste innehålla en kolumn med namnet AggregatedValue som anger ett numeriskt värde efter en användardefinierad agg regering. I alla andra resurs typer väljs **agg regerings typ** från fältet med det namnet.

### <a name="aggregation-granularity"></a>Agg regerings kornig het

Anger det intervall som används för att aggregera flera poster till ett numeriskt värde. Om du till exempel har angett **5 minuter** grupperas poster efter 5-minuters intervall med hjälp av den angivna **agg regerings typen** .

I arbets ytor och Application Insights stöds det bara i mått mått typen **mått** . Frågeresultatet måste innehålla [bin ()](/azure/kusto/query/binfunction) som anger intervall i frågeresultatet. I alla andra resurs typer kallas fältet som styr den här inställningen **agg regerings precision**.

> [!NOTE]
> Som [bin ()](/azure/kusto/query/binfunction) kan resultera i ojämna tidsintervall, konverterar aviserings tjänsten automatiskt [bin ()](/azure/kusto/query/binfunction) -funktionen till [bin_at ()](/azure/kusto/query/binatfunction) -funktionen med lämplig tid vid körning för att säkerställa resultat med en fast punkt.

### <a name="split-by-alert-dimensions"></a>Dela efter aviserings dimensioner

Dela upp aviseringar efter siffer-eller sträng kolumner i separata aviseringar genom att gruppera i unika kombinationer. När du skapar resurs drivna aviseringar i skala (prenumeration eller resurs gruppens omfång), kan du dela med kolumnen Azure Resource ID. Delning i kolumnen Azure Resource ID ändrar målet för aviseringen till den angivna resursen.

Delning av Azure Resource ID-kolumnen rekommenderas när du vill övervaka samma villkor på flera Azure-resurser. Du kan till exempel övervaka alla virtuella datorer för CPU-användning över 80%. Du kan också välja att inte dela upp när du vill ha ett villkor för flera resurser i omfattningen, till exempel att övervaka att minst fem datorer i resurs grupps omfånget har CPU-användning över 80%.

I arbets ytor och Application Insights stöds det bara i mått mått typen **mått** . Fältet kallas **agg regering på**. Det är begränsat till tre kolumner. Om du har fler än tre grupper av kolumner i frågan kan det leda till oväntade resultat. I alla andra resurs typer konfigureras den i avsnittet **dela efter dimensioner** i villkoret (begränsat till sex delningar).

#### <a name="example-of-splitting-by-alert-dimensions"></a>Exempel på delning av aviserings dimensioner

Du vill till exempel övervaka fel för flera virtuella datorer som kör webbplatsen/appen i en speciell resurs grupp. Du kan göra det med en logg aviserings regel på följande sätt:

- **Frågeterm** 

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    ```

    När du använder arbets ytor och Application Insights med **mått mätar** aviserings logik måste den här raden läggas till i frågetexten:

    ```Kusto
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

- **Kolumn för resurs-ID:** _ResourceId (delning per resurs-ID-kolumn i varnings regler är bara tillgänglig för prenumerationer och resurs grupper för närvarande)
- **Dimensioner/aggregerade på:**
  - Computer = VM1, VM2 (filtrerings värden i varnings regel definitionen är för närvarande inte tillgängligt för arbets ytor och Application Insights. Filtrera efter frågetext.)
- **Tids period/agg regerings kornig het:** 15 minuter
- **Aviserings frekvens:** 15 minuter
- **Tröskel värde:** Större än 0

Den här regeln övervakar om en virtuell dator har fel händelser under de senaste 15 minuterna. Varje virtuell dator övervakas separat och kommer att utlösa åtgärder individuellt.

> [!NOTE]
> Det finns endast en uppdelning av varnings dimensioner för det aktuella scheduledQueryRules-API: et. Om du använder det äldre [Log Analytics varnings-API: et](../platform/api-alerts.md)måste du växla. [Läs mer om att växla](./alerts-log-api-switch.md). Resurs-centrisk avisering i skala stöds bara i API-versionen `2020-05-01-preview` och senare.

## <a name="alert-logic-definition"></a>Definition av aviserings logik

När du har definierat frågan som ska köras och utvärderingen av resultaten måste du definiera aviserings logiken och när du ska starta åtgärder. I följande avsnitt beskrivs olika parametrar som du kan använda:

### <a name="threshold-and-operator"></a>Tröskel och operatör

Frågeresultatet omvandlas till ett tal som jämförs med tröskelvärdet och operatorn.

### <a name="frequency"></a>Frekvens

Intervallet då frågan körs. Kan ställas in på 5 minuter till en dag. Måste vara lika med eller mindre än [frågans tidsintervall](#query-time-range) för att inte sakna logg poster.

Om du till exempel ställer in tids perioden på 30 minuter och frekvensen till 1 timme.  Om frågan körs vid 00:00 returneras poster mellan 23:30 och 00:00. Nästa gången frågan skulle köras är 01:00 som returnerar poster mellan 00:30 och 01:00. Poster som skapats mellan 00:00 och 00:30 utvärderas aldrig.

### <a name="number-of-violations-to-trigger-alert"></a>Antal överträdelser att utlösa avisering

Du kan ange utvärderings perioden för aviseringar och antalet problem som krävs för att utlösa en avisering. Gör att du bättre kan definiera en effekt tid för att utlösa en avisering. 

Om din regel [**agg regerings granularitet**](#aggregation-granularity) exempelvis definieras som 5 minuter kan du bara utlösa en avisering om tre fel (15 minuter) av den senaste timmen inträffade. Den här inställningen definieras av din program företags princip.

## <a name="state-and-resolving-alerts"></a>Tillstånd och lösning av aviseringar

Logg aviseringar är tillstånds lösa. Aviseringar som utlöses varje gången villkoret uppfylls, även om det har Aktiver ATS tidigare. Utlösta aviseringar löses inte. Du kan [Markera aviseringen som stängd](../alerts/alerts-managing-alert-states.md). Du kan också inaktivera åtgärder för att förhindra att de utlöses under en period efter att en varnings regel har Aktiver ATS.

I arbets ytor och Application Insights kallas det **Ignorera aviseringar**. I alla andra resurs typer kallas det att **stänga av åtgärder**. 

Se följande exempel på aviserings utvärdering:

| Tid    | Utvärdering av logg villkor | Resultat 
| ------- | ----------| ----------| ------- 
| 00:05 | FALSE | Aviseringen utlöses inte. Inga åtgärder har anropats.
| 00:10 | TRUE  | Aviserings Utlös och åtgärds grupper anropas. Nytt aviserings tillstånd aktivt.
| 00:15 | TRUE  | Aviserings Utlös och åtgärds grupper anropas. Nytt aviserings tillstånd aktivt.
| 00:20 | FALSE | Aviseringen utlöses inte. Inga åtgärder har anropats. Status för tidigare-aviseringar är aktiva.

## <a name="pricing-and-billing-of-log-alerts"></a>Priser och fakturering av logg aviseringar

Pris information finns på [sidan Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/). Logg aviseringar visas under Resource Provider `microsoft.insights/scheduledqueryrules` med:

- Logga aviseringar på Application Insights visas med exakt resurs namn tillsammans med resurs grupps-och aviserings egenskaper.
- Logga aviseringar på Log Analytics visas med exakt resurs namn tillsammans med resurs grupps-och aviserings egenskaper. När du skapar med [scheduledQueryRules-API](/rest/api/monitor/scheduledqueryrules).
- Logg aviseringar som skapats från [äldre Log Analytics API](../platform/api-alerts.md) spårar inte [Azure-resurser](../../azure-resource-manager/management/overview.md) och har inte tvingande unika resurs namn. De här aviseringarna skapas fortfarande på `microsoft.insights/scheduledqueryrules` som dolda resurser som har den här resurs namns strukturen `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` . Logg aviseringar på äldre API visas med ovanstående dolda resurs namn tillsammans med resurs grupps-och aviserings egenskaper.

> [!NOTE]
> Resurs tecken som inte stöds, som `<, >, %, &, \, ?, /` ersätts med `_` i dolda resurs namn och detta visas även i fakturerings informationen.

> [!NOTE]
> Logg aviseringar för Log Analytics som används för att hanteras med hjälp av äldre [Log Analytics aviserings-API](../platform/api-alerts.md) och äldre mallar för [Log Analytics sparade sökningar och aviseringar](../insights/solutions.md). [Läs mer om att växla till det aktuella ScheduledQueryRules-API: et](../alerts/alerts-log-api-switch.md). En varnings regel hantering bör utföras med hjälp av [äldre Log Analytics-API](../platform/api-alerts.md) tills du bestämmer dig för att växla och du kan inte använda de dolda resurserna.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att [skapa i logg aviseringar i Azure](./alerts-log.md).
* Förstå [webhookar i logg aviseringar i Azure](../alerts/alerts-log-webhook.md).
* Lär dig mer om [Azure-aviseringar](../platform/alerts-overview.md).
* Läs mer om [Log Analytics](../log-query/log-query-overview.md).
