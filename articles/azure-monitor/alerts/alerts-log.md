---
title: Skapa, Visa och hantera logg aviseringar med Azure Monitor | Microsoft Docs
description: Använd Azure Monitor för att skapa, Visa och hantera logg aviserings regler
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.subservice: alerts
ms.openlocfilehash: 579e86df23a0b6369f7ff5b6113c4972e054d5cf
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625834"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Skapa, Visa och hantera logg aviseringar med Azure Monitor

## <a name="overview"></a>Översikt

Logg aviseringar gör att användare kan använda en [Log Analytics](../log-query/log-analytics-tutorial.md) fråga för att utvärdera resurser loggar varje uppsättnings frekvens och utlösa en avisering baserat på resultaten. Regler kan utlösa en eller flera åtgärder med hjälp av [Åtgärds grupper](../platform/action-groups.md). [Lär dig mer om funktioner och terminologi för logg aviseringar](../platform/alerts-unified-log.md).

Den här artikeln visar hur du skapar och hanterar logg aviseringar med hjälp av Azure Monitor. Varnings regler definieras av tre komponenter:
- Mål: en specifika Azure-resurs som ska övervakas.
- Villkor: logik att utvärdera. Om det är uppfyllt utlöses aviseringen.  
- Åtgärd: meddelanden eller Automation-e-post, SMS, webhook och så vidare.

Du kan också skapa logg aviserings regler med Azure Resource Manager mallar, som beskrivs i [en separat artikel](../alerts/alerts-log-create-templates.md).

> [!NOTE]
> Loggdata från en [Log Analytics arbets yta](../log-query/log-analytics-tutorial.md) kan skickas till Azure Monitor Metrics-lagret. Mått aviseringar har [olika beteende](../platform/alerts-metric-overview.md), vilket kan vara mer önskvärt beroende på vilka data du arbetar med. Information om vad och hur du kan skicka loggar till mått finns i [mått avisering för loggar](../platform/alerts-metric-logs.md).

## <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Skapa en logg varnings regel med Azure Portal

Här är stegen för att komma igång med att skriva frågor för aviseringar:

1. Gå till den resurs som du vill Avisera om. Överväg att ställa in aviserings regler på flera resurser genom att välja en prenumeration eller en resurs grupp omfattning när det är möjligt. Aviseringar på flera resurser minskar kostnaderna och behovet av att hantera flera aviserings regler.
1. Under **övervaka** väljer du **loggar**.
1. Efterfråga de loggdata som kan indikera problemet. Du kan använda [aviserings frågans exempel ämne](../log-query/example-queries.md) för att förstå vad du kan identifiera eller [komma igång med att skriva en egen fråga](../log-query/log-analytics-tutorial.md). Du kan också [lära dig hur du skapar optimerade aviseringsfrågor](alerts-log-query.md).
1. Tryck på knappen + Ny varnings regel för att starta flödet för att skapa aviseringar.

    ![Log Analytics-Ställ in avisering](media/alerts-log/AlertsAnalyticsCreate.png)

> [!NOTE]
> Vi rekommenderar att du skapar aviseringar i skala när du använder resurs åtkomst läge för loggar, som körs på flera resurser med en resurs grupp eller ett prenumerations omfång. Aviseringar i skala minskar regel hanteringens kostnader. För att kunna rikta in resurserna ska du inkludera kolumnen resurs-ID i resultatet. [Läs mer om att dela upp aviseringar per dimension](../platform/alerts-unified-log.md#split-by-alert-dimensions).

### <a name="log-alert-for-log-analytics-and-application-insights"></a>Logg avisering för Log Analytics och Application Insights

1. Om frågesyntaxen är korrekt visas historiska data för frågan som en graf med alternativet att justera diagram perioden från de senaste sex timmarna till förra veckan.
 
    Om frågeresultaten innehåller sammanfattande [data eller projektspecifika](/azure/kusto/query/projectoperator) kolumner utan tids kolumn, visar diagrammet ett enda värde.

    ![Konfigurera aviserings regel](media/alerts-log/AlertsPreviewAlertLog.png)

1. Välj det tidsintervall under vilket du vill utvärdera det angivna villkoret med alternativet [**period**](../platform/alerts-unified-log.md#query-time-range) .

1. Logg aviseringar kan baseras på två typer av [**mått**](../platform/alerts-unified-log.md#measure):
    1. **Antal resultat** – antalet poster som returneras av frågan.
    1. **Mått mått**  -  *Sammanställt värde* som beräknas med sammanfatta grupperat efter uttrycks val och [bin ()](/azure/kusto/query/binfunction) . Exempel:

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

1. Du kan välja att ange hur aviserings logiken ska [delas upp per dimension](../platform/alerts-unified-log.md#split-by-alert-dimensions) med alternativet **samla in** . Rad grupps uttrycket måste vara unikt och sorterat.

    > [!NOTE]
    > Som [bin ()](/azure/kusto/query/binfunction) kan resultera i ojämna tidsintervall, konverterar aviserings tjänsten automatiskt [bin ()](/azure/kusto/query/binfunction) -funktionen till [bin_at ()](/azure/kusto/query/binatfunction) -funktionen med lämplig tid vid körning för att säkerställa resultat med en fast punkt.

    > [!NOTE]
    > Det finns endast en uppdelning av varnings dimensioner för det aktuella scheduledQueryRules-API: et. Om du använder det äldre [Log Analytics varnings-API: et](../platform/api-alerts.md)måste du växla. [Läs mer om att växla](./alerts-log-api-switch.md). Resurs-centrisk avisering i skala stöds bara i API-versionen `2020-05-01-preview` och senare.

    ![sammanställt alternativ](media/alerts-log/aggregate-on.png)

1. Sedan, baserat på förhands gransknings data anger du [ **operator**, **tröskelvärde**](../platform/alerts-unified-log.md#threshold-and-operator)och [**frekvens**](../platform/alerts-unified-log.md#frequency).

1. Du kan också ange [antalet överträdelser som utlöser en avisering](../platform/alerts-unified-log.md#number-of-violations-to-trigger-alert) med hjälp av **Totalt antal överträdelser eller i följd**.

1. Välj **Klar**. 

1. Definiera **aviserings regelns namn**, **Beskrivning** och välj aviseringens **allvarlighets grad**. Informationen används i alla aviserings åtgärder. Du kan dessutom välja att inte aktivera aviserings regeln när du skapar den genom att välja **Aktivera regel när** du har skapat den.

1. Välj om du vill utelämna regel åtgärder under en tid efter att en avisering har utlösts, använder du alternativet [**Dölj aviseringar**](../platform/alerts-unified-log.md#state-and-resolving-alerts) . Regeln körs fortfarande och skapar aviseringar, men åtgärder aktive ras inte för att förhindra brus. Värdet för att stänga av åtgärder måste vara större än aviserings frekvensen som ska vara effektiv.

    ![Ignorera aviseringar för logg aviseringar](media/alerts-log/AlertsPreviewSuppress.png)

1. Ange om varnings regeln ska utlösa en eller flera [**Åtgärds grupper**](../platform/action-groups.md#webhook) när aviserings villkoret är uppfyllt.

    > [!NOTE]
    > Se begränsningar för [Azure-prenumerationen](../../azure-resource-manager/management/azure-subscription-service-limits.md) för begränsningar för de åtgärder som kan utföras.  

1. Du kan också anpassa åtgärder i logg aviserings regler:

    - **Anpassat e-post ämne**: åsidosätter *e-postmeddelandets ämne* . Du kan inte ändra bröd texten i e-postmeddelandet och det här fältet **är inte för e-postadresser**.
    - **Inkludera anpassad JSON-nyttolast**: åsidosätter webhook-JSON som används av åtgärds grupper som antar att åtgärds gruppen innehåller en webhook-åtgärd. Läs mer om [webhook-åtgärd för logg aviseringar](./alerts-log-webhook.md).

    ![Åsidosättning av åtgärd för logg aviseringar](media/alerts-log/AlertsPreviewOverrideLog.png)

1. Om alla fält har angetts korrekt kan du klicka på knappen **skapa aviserings regel** och skapa en avisering.

    Inom några minuter är aviseringen aktiv och utlösare enligt beskrivningen ovan.

    ![Skapa regel](media/alerts-log/AlertsPreviewCreate.png)

#### <a name="creating-log-alert-for-log-analytics-and-application-insights-from-the-alerts-management"></a>Skapa logg avisering för Log Analytics och Application Insights från aviserings hantering

> [!NOTE]
> Det finns för närvarande inte stöd för att skapa från aviserings hantering för resursbaserade loggar

1. Välj **övervaka** i [portalen](https://portal.azure.com/)och välj sedan **aviseringar**.

    ![Övervakning](media/alerts-log/AlertsPreviewMenu.png)

1. Välj **ny aviserings regel**. 

    ![Lägg till avisering](media/alerts-log/AlertsPreviewOption.png)

1. Fönstret **skapa avisering** visas. Den innehåller fyra delar: 
    - Den resurs som aviseringen gäller.
    - Villkoret att kontrol lera.
    - De åtgärder som ska vidtas om villkoret är sant.
    - Information till namn och beskrivning av aviseringen. 

    ![Skapa regel](media/alerts-log/AlertsPreviewAdd.png)

1. Tryck på knappen **Välj resurs** . Filtrera genom att välja *prenumeration*, *resurs typ* och välj en resurs. Se till att resursen har tillgängliga loggar.

   ![Välj resurs](media/alerts-log/Alert-SelectResourceLog.png)

1. Använd sedan knappen Lägg till **villkor** för att visa en lista över tillgängliga signal alternativ för resursen. Välj alternativ för **anpassad loggs ökning** .

   ![Välj en resurs – anpassad loggs ökning](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > På aviserings portalen visas sparade frågor från Log Analytics och Application Insights och de kan användas som varnings frågor för mallar.

1. När du har valt, Skriv, klistra in eller redigera aviserings frågan i fältet **Sök fråga** .

1. Fortsätt till nästa steg som beskrivs i det [sista avsnittet](#log-alert-for-log-analytics-and-application-insights).

### <a name="log-alert-for-all-other-resource-types"></a>Logg avisering för alla andra resurs typer

> [!NOTE]
> Det finns för närvarande inga ytterligare avgifter för API-versionen `2020-05-01-preview` och resursbaserade logg aviseringar.  Prissättningen för funktioner som finns i förhands granskning kommer att meddelas i framtiden och ett meddelande som visas innan faktureringen påbörjas. Om du väljer att fortsätta använda nya API-versioner och resursbaserade logg aviseringar efter meddelande perioden debiteras du enligt tillämplig taxa.

1. Starta från fliken **villkor** :

    1. Kontrol lera att [**måttet**](../platform/alerts-unified-log.md#measure), [**sammansättnings typen**](../platform/alerts-unified-log.md#aggregation-type)och [**agg regerings precisionen**](../platform/alerts-unified-log.md#aggregation-granularity) är korrekta. 
        1. Regeln räknar som standard antalet resultat under de senaste 5 minuterna.
        1. Om vi identifierar sammanfattade frågeresultat kommer regeln att uppdateras automatiskt inom några sekunder för att avbilda.

    1. Välj [aviserings delning efter dimensioner](../platform/alerts-unified-log.md#split-by-alert-dimensions)om så behövs: 
       - **Kolumnen resurs-ID** väljs automatiskt, om den upptäcks och ändras kontexten för den utlöst aviseringen till postens resurs. 
       - **Kolumnen resurs-ID** kan vara avmarkerad för att utlösa aviseringar för prenumerationer eller resurs grupper. De som väljer är användbara när frågeresultaten baseras på mellan resurser. Till exempel är en fråga som kontrollerar om 80% av resurs gruppens virtuella datorer har hög CPU-användning.
       - Upp till sex fler delningar kan också väljas för alla typer av siffror och text kolumner med dimensions tabellen.
       - Aviseringar utlöses separat enligt delning baserat på unika kombinationer och aviserings nytto lasten innehåller denna information.
    
        ![Välj agg regerings parametrar och delning](media/alerts-log/select-aggregation-parameters-and-splitting.png)

    1. I **förhands gransknings** diagrammet visas resultat för frågeresultat över tid. Du kan ändra diagram perioden eller välja olika tids serier som resulterade i unika aviserings delningar per dimension.

        ![Förhandsgranska diagram](media/alerts-log/preview-chart.png)

    1. Sedan, baserat på förhands gransknings data, ställer du in **varnings logiken**. [ **Operatör**, **tröskel värde**](../platform/alerts-unified-log.md#threshold-and-operator)och [**frekvens**](../platform/alerts-unified-log.md#frequency).

        ![Förhandsgranska diagram med tröskel och aviserings logik](media/alerts-log/chart-and-alert-logic.png)

    1. Du kan också ange [**antalet överträdelser som ska utlösa aviseringen**](../platform/alerts-unified-log.md#number-of-violations-to-trigger-alert) i avsnittet **Avancerade alternativ** .
    
        ![Avancerade alternativ](media/alerts-log/advanced-options.png)

1. På fliken **åtgärder** väljer eller skapar du de nödvändiga [Åtgärds grupperna](../platform/action-groups.md).

    ![Fliken åtgärder](media/alerts-log/actions-tab.png)

1. Definiera **varnings regel informationen** och **projekt information** på fliken **information** . Du kan också ange om du vill **Starta körningen nu** eller [**inaktivera åtgärder**](../platform/alerts-unified-log.md#state-and-resolving-alerts) under en period efter att varnings regeln utlöses.

    > [!NOTE]
    > Logg varnings regler är för närvarande tillstånds lösa och utlöser en åtgärd varje gång en avisering skapas om inte avstängning har definierats.

    ![Fliken information](media/alerts-log/details-tab.png)

1. På fliken **taggar** anger du obligatoriska taggar i aviserings regel resursen.

    ![Fliken Taggar](media/alerts-log/tags-tab.png)

1. På fliken **Granska och skapa** körs en validering som informerar om eventuella problem. Granska och godkänn regel definitionen.
1. Om alla fält är korrekta väljer du knappen **skapa** och slutför skapandet av aviserings regeln. Alla aviseringar kan visas från aviserings hantering.
 
    ![Granska och skapa flik](media/alerts-log/review-and-create-tab.png)

## <a name="view--manage-log-alerts-in-azure-portal"></a>Visa & hantera logg aviseringar i Azure Portal

1. I [portalen](https://portal.azure.com/)väljer du relevant resurs eller **övervaknings** tjänsten. Välj sedan **aviseringar** i avsnittet övervaka.

1. Aviserings hanteringen visar alla aviseringar som har utlösts. [Läs mer om aviserings hantering](alerts-managing-alert-instances.md).

    > [!NOTE]
    > Logg varnings regler är för närvarande [tillstånds lösa och kan inte lösas](../platform/alerts-unified-log.md#state-and-resolving-alerts).

1. Redigera regler genom att klicka på **Hantera varnings regler** på översta raden:

    ![ hantera aviserings regler](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-powershell"></a>Hantera logg aviseringar med PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> PowerShell stöds för närvarande inte i API-versionen `2020-05-01-preview`

PowerShell-cmdletar som anges nedan är tillgängliga för att hantera regler med [API: et för schemalagda Frågeregler](/rest/api/monitor/scheduledqueryrules/).

- [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) : PowerShell-cmdlet för att skapa en ny logg aviserings regel.
- [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) : PowerShell-cmdleten för att uppdatera en befintlig logg aviserings regel.
- [New-AzScheduledQueryRuleSource](/powershell/module/az.monitor/new-azscheduledqueryrulesource) : PowerShell-cmdlet för att skapa eller uppdatera objekt som anger käll parametrar för en logg avisering. Används som inmatad av cmdleten [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) och [set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleSchedule](/powershell/module/az.monitor/new-azscheduledqueryruleschedule): PowerShell-cmdlet för att skapa eller uppdatera objekt som anger schema parametrar för en logg avisering. Används som inmatad av cmdleten [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) och [set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) : PowerShell-cmdlet för att skapa eller uppdatera objekt som anger åtgärds parametrar för en logg avisering. Används som inmatad av cmdleten [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) och [set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleAznsActionGroup](/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : PowerShell-cmdlet för att skapa eller uppdatera objekt som anger parametrar för åtgärds grupper för en logg avisering. Används som inmatad av [New-AzScheduledQueryRuleAlertingAction-](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) cmdleten.
- [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : PowerShell-cmdleten för att skapa eller uppdatera objekt som anger Utlös ande villkors parametrar för logg avisering. Används som inmatad av [New-AzScheduledQueryRuleAlertingAction-](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) cmdleten.
- [New-AzScheduledQueryRuleLogMetricTrigger](/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : PowerShell-cmdlet för att skapa eller uppdatera objekt som anger mått för mått utlösare för måttet för mått [mått typ logg avisering](../platform/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value). Används som inmatad av [New-AzScheduledQueryRuleTriggerCondition-](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) cmdleten.
- [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule) : PowerShell-cmdleten för att visa en lista över befintliga logg aviserings regler eller en speciell logg aviserings regel
- [Update-AzScheduledQueryRule](/powershell/module/az.monitor/update-azscheduledqueryrule) : PowerShell-cmdlet för att aktivera eller inaktivera logg aviserings regel
- [Remove-AzScheduledQueryRule](/powershell/module/az.monitor/remove-azscheduledqueryrule): PowerShell-cmdlet för att ta bort en befintlig logg aviserings regel

> [!NOTE]
> ScheduledQueryRules PowerShell-cmdletar kan bara hantera regler som skapats i det aktuella [API: et för schemalagda Frågeregler](/rest/api/monitor/scheduledqueryrules/). Logg varnings regler som skapats med äldre [Log Analytics aviserings-API: t](../platform/api-alerts.md) kan endast hanteras med PowerShell efter [växling till schemalagda Frågeregler API](../alerts/alerts-log-api-switch.md).

Här är exempel steg för att skapa en logg aviserings regel med hjälp av PowerShell:

```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

Du kan också skapa logg aviseringen med hjälp av en [mall och parametrar](./alerts-log-create-templates.md) filer med hjälp av PowerShell:

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile mylogalerttemplate.json -TemplateParameterFile mylogalerttemplate.parameters.json
```

## <a name="managing-log-alerts-using-cli"></a>Hantera logg aviseringar med CLI

> [!NOTE]
> Stöd för Azure CLI är bara tillgängligt för scheduledQueryRules API-versionen `2020-05-01-preview` och senare. Tidigare-API-versionen kan använda Azure Resource Manager CLI med mallar enligt beskrivningen nedan. Om du använder det äldre [Log Analytics varnings-API: et](../platform/api-alerts.md)måste du byta till Använd cli. [Läs mer om att växla](./alerts-log-api-switch.md).

I föregående avsnitt beskrivs hur du skapar, visar och hanterar logg aviserings regler med hjälp av Azure Portal. Det här avsnittet beskriver hur du gör på samma sätt som med plattforms oberoende [Azure CLI](/cli/azure/get-started-with-azure-cli). Det snabbaste sättet att börja använda Azure CLI är genom [Azure Cloud Shell](../../cloud-shell/overview.md). I den här artikeln använder vi Cloud Shell.

1. Gå till Azure Portal och välj **Cloud Shell**.

1. I kommando tolken kan du använda kommandon med ``--help`` alternativ för att lära dig mer om kommandot och hur du använder det. Följande kommando visar till exempel listan över kommandon som är tillgängliga för att skapa, Visa och hantera logg aviseringar:

    ```azurecli
    az monitor scheduled-query --help
    ```

1. Du kan skapa en logg aviserings regel som övervakar antal system händelse fel:

    ```azurecli
    az monitor scheduled-query create -g {ResourceGroup} -n {nameofthealert} --scopes {vm_id} --condition "count \'union Event, Syslog | where TimeGenerated > ago(1h) | where EventLevelName == \"Error\" or SeverityLevel== \"err\"\' > 2" --description {descriptionofthealert}
    ```

1. Du kan visa alla logg aviseringar i en resurs grupp med hjälp av följande kommando:

    ```azurecli
    az monitor scheduled-query list -g {ResourceGroup}
    ```

1. Du kan se information om en viss logg aviserings regel med hjälp av namnet eller resurs-ID: t för regeln:

    ```azurecli
    az monitor scheduled-query show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor scheduled-query show --ids {RuleResourceId}
    ```

1. Du kan inaktivera en logg aviserings regel med hjälp av följande kommando:

    ```azurecli
    az monitor scheduled-query update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

1. Du kan ta bort en logg aviserings regel med hjälp av följande kommando:

    ```azurecli
    az monitor scheduled-query delete -g {ResourceGroup} -n {AlertRuleName}
    ```

Du kan också använda Azure Resource Manager CLI med [mallfiler](./alerts-log-create-templates.md) :

```azurecli
az login

az deployment group create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file mylogalerttemplate.json \
    --parameters @mylogalerttemplate.parameters.json
```

Vid lyckad generering returneras 201. Vid lyckad uppdatering returneras 200.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [logg aviseringar](../platform/alerts-unified-log.md).
* Skapa logg aviseringar med [Azure Resource Manager mallar](./alerts-log-create-templates.md).
* Förstå [webhook-åtgärder för logg aviseringar](./alerts-log-webhook.md).
* Läs mer om [logg frågor](../log-query/log-query-overview.md).
