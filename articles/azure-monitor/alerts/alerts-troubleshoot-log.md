---
title: Felsöka logg aviseringar i Azure Monitor | Microsoft Docs
description: Vanliga problem, fel och lösningar för logg aviserings regler i Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 6b495e8e700f146f5e6c117413456311ac0cdd5a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037888"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Felsöka logg aviseringar i Azure Monitor  

Den här artikeln visar hur du löser vanliga problem med logg aviseringar i Azure Monitor. Den innehåller också lösningar på vanliga problem med funktioner och konfiguration av logg aviseringar.

Logg aviseringar gör att användare kan använda en [Log Analytics](../logs/log-analytics-tutorial.md) fråga för att utvärdera resurser loggar varje uppsättnings frekvens och utlösa en avisering baserat på resultaten. Regler kan utlösa en eller flera åtgärder med hjälp av [Åtgärds grupper](./action-groups.md). [Lär dig mer om funktioner och terminologi för logg aviseringar](alerts-unified-log.md).

> [!NOTE]
> Den här artikeln tar inte hänsyn till fall där Azure Portal visar en varnings regel som utlöses och en avisering inte utförs av en associerad åtgärds grupp. I sådana fall kan du läsa mer om fel sökning [här](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected).

## <a name="log-alert-didnt-fire"></a>Logg aviseringen startades inte

### <a name="data-ingestion-time-for-logs"></a>Data hämtnings tid för loggar

Azure Monitor bearbetar terabytes kunders loggar från hela världen, vilket kan orsaka [svars tid för loggar](../logs/data-ingestion-time.md).

Loggar är halv strukturerade data och mycket mer latenta än mått. Om du har mer än 4 minuters fördröjning i utlösta aviseringar bör du överväga att använda [mått aviseringar](alerts-metric-overview.md). Du kan skicka data till mått lagret från loggar med hjälp av [mått aviseringar för loggar](alerts-metric-logs.md).

Systemet försöker utföra aviserings utvärderingen flera gånger för att minimera svars tiden. När data har inträffat utlöses aviseringen, som i de flesta fall inte är lika med logg posten.

### <a name="incorrect-query-time-range-configured"></a>Felaktigt tidsintervall för fråga har kon figurer ATS

Tidsintervallet för frågor anges i regel villkors definitionen. Det här fältet kallas för **period** för arbets ytor och Application Insights, och det kallas **tidsintervallet för åsidosättningar** för alla andra resurs typer. Precis som i Log Analytics begränsar tidsintervallet frågedata till den angivna perioden. Även om kommandot **sedan** används i frågan, kommer tidsintervallet att gälla. 

En fråga skannar till exempel 60 minuter, när tidsintervallet är 60 minuter, även om texten innehåller **sedan sedan (1d)**. Tidsintervallet och tids filtreringen för frågan måste matcha. I exempel fallet kan det   /  vara förväntat att ändra **tidsintervallet** för tids perioden för tids perioden till en dag.

![Tidsperiod](media/alerts-troubleshoot-log/LogAlertTimePeriod.png)

### <a name="actions-are-muted-in-the-alert-rule"></a>Åtgärder är avstängda i aviserings regeln

Logg aviseringar ger ett alternativ för att stänga av utlöst aviserings åtgärder för en angiven tids period. Det här fältet kallas **Ignorera aviseringar** i arbets ytor och Application Insights. I alla andra resurs typer kallas det att **stänga av åtgärder**. 

Ett vanligt problem är att du tror att aviseringen inte kunde starta åtgärderna på grund av ett tjänst problem. Till och med svårt den stängdes av regel konfigurationen.

![Ignorera aviseringar](media/alerts-troubleshoot-log/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-with-splitting-using-the-legacy-log-analytics-api"></a>Mått mätnings varnings regel vid delning med hjälp av äldre Log Analytics-API

[Mått måttet](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) är en typ av logg avisering som baseras på sammanfattande tids serie resultat. Dessa regler tillåter gruppering efter kolumner att [dela upp aviseringar](alerts-unified-log.md#split-by-alert-dimensions). Om du använder det äldre Log Analytics-API: t fungerar inte delningen som förväntat. Det finns inte stöd för att välja gruppering i det äldre API: et.

Med det aktuella ScheduledQueryRules-API: et kan du ange **agg regering** i mått regler för [mått](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) , som fungerar som förväntat. [Läs mer om att växla till det aktuella ScheduledQueryRules-API: et](../alerts/alerts-log-api-switch.md).

## <a name="log-alert-fired-unnecessarily"></a>Logg aviseringen utlöstes i onödan

En konfigurerad [logg aviserings regel i Azure Monitor](./alerts-log.md) kan utlösas oväntad. I följande avsnitt beskrivs några vanliga orsaker.

### <a name="alert-triggered-by-partial-data"></a>Avisering utlöst av partiella data

Azure Monitor bearbetar terabytes kunders loggar från hela världen, vilket kan orsaka [svars tid för loggar](../logs/data-ingestion-time.md).

Loggar är halv strukturerade data och mycket mer latenta än mått. Om du har många fel vid fel tändning i notifieringar, bör du överväga att använda [mått aviseringar](alerts-metric-overview.md). Du kan skicka data till mått lagret från loggar med hjälp av [mått aviseringar för loggar](alerts-metric-logs.md).

Logg aviseringar fungerar bäst när du försöker identifiera data i loggarna. Det fungerar mindre bra när du försöker identifiera avsaknad av data i loggarna. Till exempel avisering om pulsslag för virtuella datorer. 

Även om det finns inbyggda funktioner för att förhindra falska varningar, kan de fortfarande förekomma på mycket latenta data (över cirka 30 minuter) och data med svars tider.

### <a name="query-optimization-issues"></a>Frågor om optimering av frågor

Aviserings tjänsten ändrar din fråga för att optimera för låg belastning och aviserings fördröjning. Aviserings flödet skapades för att transformera resultaten som indikerar ett problem med en avisering. Till exempel, i ett fall av en fråga som:

``` Kusto
SecurityEvent
| where EventID == 4624
```

Om syftet med användaren är att varna när den här händelse typen inträffar, lägger aviserings logiken till i `count` frågan. Frågan som ska köras är:

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

Du behöver inte lägga till aviserings logik i frågan och göra detta kan till och med orsaka problem. I exemplet ovan, om du inkluderar `count` i din fråga, resulterar det alltid i värdet 1, eftersom aviserings tjänsten kommer att göra `count` `count` .

Den optimerade frågan är den som används för att köra logg aviserings tjänsten. Du kan köra den ändrade frågan i Log Analytics [Portal](../logs/log-query-overview.md) eller [API](/rest/api/loganalytics/).

För arbets ytor och Application Insights kallas det **fråga som ska köras** i villkors fönstret. I alla andra resurs typer väljer du **Visa slutgiltig varnings fråga** på fliken villkor.

![Fråga som ska köras](media/alerts-troubleshoot-log/LogAlertPreview.png)

## <a name="log-alert-was-disabled"></a>Logg avisering har inaktiverats

I följande avsnitt visas några orsaker till varför Azure Monitor kan inaktivera en logg aviserings regel. Vi har också inkluderat ett [exempel på aktivitets loggen som skickas när en regel är inaktive rad](#activity-log-example-when-rule-is-disabled).

### <a name="alert-scope-no-longer-exists-or-was-moved"></a>Aviserings området finns inte längre eller flyttades

När omfångs resurserna för en varnings regel inte längre är giltiga, Miss lyckas körningen av regeln. I det här fallet stoppas även faktureringen.

Azure Monitor kommer att inaktivera logg aviseringen efter en vecka om den Miss lyckas kontinuerligt.

### <a name="query-used-in-a-log-alert-isnt-valid"></a>Frågan som används i en logg avisering är inte giltig

När en regel för logg avisering skapas, verifieras frågan för korrekt syntax. Men ibland kan det hända att frågan som anges i logg aviserings regeln börjar fungera. Några vanliga orsaker är:

- Reglerna har skapats via API: et och verifieringen hoppades över av användaren.
- Frågan [körs på flera resurser](../logs/cross-workspace-query.md) och en eller flera av resurserna har tagits bort eller flyttats.
- [Frågan misslyckades](https://dev.loganalytics.io/documentation/Using-the-API/Errors) på grund av följande:
    - Loggnings lösningen [distribuerades inte till arbets ytan](../insights/solutions.md#install-a-monitoring-solution), så tabellerna skapas inte.
    - Data slutade flöda till en tabell i frågan i mer än 30 dagar.
    - [Tabeller med anpassade loggar](../agents/data-sources-custom-logs.md) har ännu inte skapats eftersom data flödet inte har startats.
- Ändringar i [frågespråket](/azure/kusto/query/) innehåller ett ändrat format för kommandon och funktioner. Den angivna frågan tidigare är inte längre giltig.

[Azure Advisor](../../advisor/advisor-overview.md) varnar dig om det här beteendet. Den lägger till en rekommendation om logg varnings regeln som påverkas. Kategorin som används är hög tillgänglighet med medelhög påverkan och en beskrivning av "reparera din logg aviserings regel för att säkerställa övervakning".

## <a name="alert-rule-quota-was-reached"></a>Varnings regel kvoten nåddes

Antalet loggsökningsaviseringsregler per prenumeration regleras av kvotgränserna som beskrivs [här](../service-limits.md).

### <a name="recommended-steps"></a>Rekommenderade åtgärder
    
Om du har nått kvot gränsen kan följande steg hjälpa dig att lösa problemet.

1. Försök att ta bort eller inaktivera varnings regler för loggs ökning som inte används längre.
1. Försök att använda [delning av aviseringar per dimension](alerts-unified-log.md#split-by-alert-dimensions) för att minska antalet regler. Dessa regler kan övervaka många resurser och identifierings fall.
1. Om du behöver öka kvot gränsen kan du fortsätta att öppna en support förfrågan och ange följande information:

    - Prenumerations-ID och resurs-ID för vilka kvot gränsen måste ökas.
    - Orsak till kvot ökning.
    - Resurs typ för kvot ökningen: **Log Analytics**, **Application Insights** och så vidare.
    - Begärd kvot gräns.


### <a name="to-check-the-current-usage-of-new-log-alert-rules"></a>Kontrol lera den aktuella användningen av nya logg aviserings regler
    
#### <a name="from-the-azure-portal"></a>Från Azure-portalen

1. Öppna skärmen *aviseringar* och välj *Hantera aviserings regler*
2. Filtrera fram relevant prenumeration med hjälp av listrutekontrollen *Prenumeration*
3. Se till att du inte filtrerar till en speciell resurs grupp, resurs typ eller resurs
4. Välj ”Loggsökning” i listrutekontrollen *Signaltyp*
5. Kontrollera att listrutekontrollen *Status* är inställd på ”Aktiverad”
6. Det totala antalet loggsökningsaviseringsregler visas ovanför regellistan

#### <a name="from-api"></a>Från API

- PowerShell- [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule)
- REST API – [Lista efter prenumeration](/rest/api/monitor/scheduledqueryrules/listbysubscription)

## <a name="activity-log-example-when-rule-is-disabled"></a>Aktivitets logg exempel när regeln är inaktive rad

Om frågan Miss lyckas under sju dagar kontinuerligt, kommer Azure Monitor att inaktivera logg aviseringen och stoppa faktureringen av regeln. Du kan ta reda på exakt tid när Azure Monitor har inaktiverat logg aviseringen i [Azure aktivitets loggen](../../azure-resource-manager/management/view-activity-logs.md). Se det här exemplet:

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [logg aviseringar i Azure](./alerts-unified-log.md).
- Läs mer om hur du [konfigurerar logg aviseringar](../logs/log-query-overview.md).
- Läs mer om [logg frågor](../logs/log-query-overview.md).