---
title: Felsöka Azure Metric-aviseringar
description: Vanliga problem med Azure Monitor metriska aviseringar och möjliga lösningar.
author: harelbr
ms.author: harelbr
ms.topic: troubleshooting
ms.date: 01/21/2021
ms.subservice: alerts
ms.openlocfilehash: 79cc7e1e4b574533fcad4592134109c52897e9ba
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737264"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Felsöka problem i Azure Monitor-måttaviseringar 

I den här artikeln beskrivs vanliga problem i Azure Monitor [Metric-aviseringar](alerts-metric-overview.md) och fel sökning.

Azure Monitor aviseringar proaktivt meddela dig när viktiga villkor finns i dina övervaknings data. De gör att du kan identifiera och åtgärda problem innan användarna av systemet ser dem. Mer information om aviseringar finns i [Översikt över aviseringar i Microsoft Azure](./alerts-overview.md).

## <a name="metric-alert-should-have-fired-but-didnt"></a>Mått aviseringen bör ha utlösts men inte 

Om du tror att en måtta avisering ska ha utlösts men den inte har startats och inte finns i Azure Portal kan du prova följande steg:

1. **Konfiguration** – granska varnings regel konfigurationen för att kontrol lera att den är korrekt konfigurerad:
    - Kontrol lera att **sammansättnings typen** och **agg regerings precisionen (period)** är konfigurerade som förväntat. **Sammansättnings typen** avgör hur mått värden aggregeras (Läs mer [här](../essentials/metrics-aggregation-explained.md#aggregation-types)) och **agg regerings kornig het (period)** styr hur långt tillbaka utvärderingen aggregerar mått värden varje gång som varnings regeln körs.
    -  Kontrol lera att **tröskelvärdet** eller **känslighets** värdet är konfigurerat som förväntat.
    - För en varnings regel som använder dynamiska tröskelvärden kontrollerar du om avancerade inställningar har kon figurer ATS. **antalet överträdelser** kan filtrera aviseringar och **Ignorera data innan** de kan påverka hur tröskelvärdena beräknas.

       > [!NOTE] 
       > Dynamiska tröskelvärden kräver minst 3 dagar och 30 mått exempel innan de blir aktiva.

2. **Utlöses men ingen avisering** – granska [listan över utlösta aviseringar](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) för att se om du kan hitta den utlöst aviseringen. Om du kan se aviseringen i listan, men har problem med några av dess åtgärder eller aviseringar, se mer information [här](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected).

3. **Redan aktiv** -kontrol lera om det redan finns en utlöst avisering på den Metric Time-serie som du förväntar dig att få en avisering för. Måttaviseringar är tillståndskänsliga, vilket innebär att när en avisering har aktiverats för en specifik måttidsserie så utlöses inte ytterligare aviseringar i den tidsserien förrän problemet inte längre observeras. Det här design alternativet minskar bruset. Aviseringen löses automatiskt när varnings villkoret inte uppfylls för tre efterföljande utvärderingar.

4. **Använda dimensioner** – om du har valt några [Dimensions värden för ett mått](./alerts-metric-overview.md#using-dimensions)övervakar varnings regeln varje individuell mått tids serie (som definieras av kombinationen av dimensions värden) för en tröskel överträdelse. För att övervaka den sammanställda mått tids serien (utan valda dimensioner) konfigurerar du ytterligare en varnings regel för måttet utan att välja dimensioner.

5. **Agg regerings-och tids kornig het** – om du visualiserar måttet med hjälp av [mått diagram](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)bör du se till att:
    * Den valda **agg regeringen** i mått diagrammet är samma som **agg regerings typ** i aviserings regeln
    * Den valda **tids kornig het** är samma som **agg regerings precisionen (period)** i aviserings regeln (och är inte inställd på automatiskt)

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>Mått aviseringen utlöses när den inte borde ha

Om du tror att din måtta avisering inte borde ha utlösts utan den gjorde kan följande steg hjälpa dig att lösa problemet.

1. Granska [listan över aktiverade varningar](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) för att hitta den utlöst aviseringen och klicka på för att visa information om den. Läs informationen under **Varför hjälpte den här aviseringen?** om du vill se mått diagrammet, **Metric-värdet** och **tröskelvärdet** vid den tidpunkt då aviseringen utlöstes.

    > [!NOTE] 
    > Om du använder en villkors typ för dynamiskt tröskelvärden och tror att de tröskelvärden som använts inte var korrekta, ger du feedback med hjälp av den bister ikonen. Den här feedbacken påverkar forskningen om Machine Learning-algoritmer och hjälper till att förbättra framtida identifieringar.

2. Om du har valt flera dimensions värden för ett mått utlöses aviseringen när **någon** av mått tids serien (som definieras av kombinationen av dimensions värden) bryter mot tröskelvärdet. Mer information om hur du använder dimensioner i måttaviseringar finns [här](./alerts-metric-overview.md#using-dimensions).

3. Granska varnings regel konfigurationen för att kontrol lera att den är korrekt konfigurerad:
    - Kontrol lera att **sammansättnings typen**, **agg regerings precisionen (period)** och **tröskelvärdet** eller **känslighets** värdet är konfigurerade som förväntat
    - För en varnings regel som använder dynamiska tröskelvärden kontrollerar du om avancerade inställningar har kon figurer ATS. **antalet överträdelser** kan filtrera aviseringar och **Ignorera data innan** de kan påverka hur tröskelvärdena beräknas

   > [!NOTE]
   > Dynamiska tröskelvärden kräver minst 3 dagar och 30 mått exempel innan de blir aktiva.

4. Om du visualiserar måttet med [mått diagram](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)bör du se till att:
    - Den valda **agg regeringen** i mått diagrammet är samma som **agg regerings typ** i aviserings regeln
    - Den valda **tids kornig het** är samma som **agg regerings precisionen (period)** i aviserings regeln (och är inte inställd på automatiskt)

5. Om aviseringen utlöses när det redan finns aviseringar som övervakar samma villkor (som inte är lösta) *kontrollerar du om* aviserings regeln har kon figurer ATS med egenskapen Autoformat till **false** (den här egenskapen kan bara konfigureras via REST/PowerShell/CLI, så kontrol lera skriptet som används för att distribuera varnings regeln). I detta fall löser varnings regeln inte autonotifieringar som skickats och kräver inte att en utlöst avisering stängs innan den utlöses igen.


## <a name="cant-find-the-metric-to-alert-on---virtual-machines-guest-metrics"></a>Det går inte att hitta måttet för avisering på virtuella datorer gäst mått

Se till att du har installerat den agent som krävs för att samla in data för att Azure Monitor måtten om du vill varna för gäst operativ systemets mått för virtuella datorer (till exempel minne, disk utrymme).
- [För virtuella Windows-datorer](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
- [För virtuella Linux-datorer](../essentials/collect-custom-metrics-linux-telegraf.md)

Mer information om att samla in data från gäst operativ systemet på en virtuell dator finns [här](../vm/monitor-vm-azure.md#guest-operating-system).

> [!NOTE] 
> Om du har konfigurerat gäst mått som ska skickas till en Log Analytics arbets yta visas måtten under arbets ytan Log Analytics arbets yta och kommer att börja visa **data när** du har skapat en varnings regel som övervakar dem. Det gör du genom att följa stegen för att [konfigurera en måttavisering för loggar](./alerts-metric-logs.md#configuring-metric-alert-for-logs).

> [!NOTE] 
> Övervakning av ett gäst mått för flera virtuella datorer med en enda varnings regel stöds för närvarande inte av mått aviseringar. Du kan åstadkomma detta med en [logg aviserings regel](./alerts-unified-log.md). Det gör du genom att se till att gäst måtten samlas in på en Log Analytics arbets yta och skapa en logg aviserings regel på arbets ytan.

## <a name="cant-find-the-metric-to-alert-on"></a>Det går inte att hitta det mått som ska aviseras

Om du vill skapa aviseringar om ett visst mått men inte kan se det när du skapar en aviseringsregel kontrollerar du följande:
- Om du inte kan se mått för resursen [kontrollerar du om resurstypen har stöd för måttaviseringar](./alerts-metric-near-real-time.md).
- Om du kan se vissa mått för resursen, men inte ett specifikt mått, [kontrollerar du om måttet är tillgängligt](../essentials/metrics-supported.md). I så fall kontrollerar du måttbeskrivningen för att se om måttet bara är tillgängligt i vissa versioner eller utgåvor av resursen.
- Om måttet inte är tillgängligt för resursen kan det vara tillgängligt i resursloggarna och kan då övervakas med hjälp av loggaviseringar. Här kan du läsa mer om att [samla in och analysera resursloggar från en Azure-resurs](../essentials/tutorial-resource-logs.md).

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Det går inte att hitta mått dimensionen som ska aviseras

Tänk på följande om du vill få [en avisering om ett måtts dimensions värden](./alerts-metric-overview.md#using-dimensions), men inte kan hitta dessa värden:

1. Det kan ta några minuter innan dimensionsvärden visas under listan **Dimensionsvärden**
1. Dimensionsvärden som visas baseras på måttdata som samlats in under den senaste dagen
1. Om dimensionsvärdet inte har skickats än eller inte visas, kan du använda alternativet Lägg till anpassat värde för att lägga till ett anpassat dimensionsvärde
1. Om du vill få aviseringar om alla möjliga värden för en dimension (inklusive framtida värden) väljer du alternativet "Välj alla aktuella och framtida värden"

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>Mått varnings regler som fortfarande har definierats för en borttagen resurs 

Vid borttagning av en Azure-resurs tas inte associerade måttaviseringsregler bort automatiskt. Ta bort aviserings regler som är kopplade till en resurs som har tagits bort:

1. Öppna den resursgrupp där den borttagna resursen definierades
1. I den lista som visar resurserna markerar du kryssrutan **Visa dolda typer**
1. Filtrera listan efter Typ == **microsoft.insights/metricalerts**
1. Välj relevanta aviserings regler och välj **ta bort**

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>Gör mått varningar varje gång mitt villkor är uppfyllt

Mått varningar är tillstånds känsliga som standard och därför utlöses inte ytterligare aviseringar om det redan finns en utlöst avisering på en specifik tids serie. Om du vill göra en speciell varnings regel för tillstånds begränsning, och få aviseringar om varje utvärdering som varnings villkoret är uppfyllt, skapar du aviserings regeln program mässigt (till exempel via [Resource Manager](./alerts-metric-create-templates.md), [PowerShell](/powershell/module/az.monitor/), [rest](/rest/api/monitor/metricalerts/createorupdate), [CLI](/cli/azure/monitor/metrics/alert)) och ställer in egenskapen *automildra* till "false".

> [!NOTE] 
> Att göra en regel för att varna regler förhindrar att skickade aviseringar löses, så även när villkoret inte är uppfyllt, kommer de aktiverade aviseringarna att behållas i ett utlöst tillstånd fram till den 30 dagarnas kvarhållningsperiod.

## <a name="define-an-alert-rule-on-a-custom-metric-that-isnt-emitted-yet"></a>Definiera en varnings regel för ett anpassat mått som inte har genererats än

När du skapar en regel för mått varningar verifieras mått namnet mot [mått definitions-API: et](/rest/api/monitor/metricdefinitions/list) för att se till att det finns. I vissa fall vill du skapa en varnings regel för ett anpassat mått även innan den släpps. Till exempel när du skapar (med en Resource Manager-mall) en Application Insights-resurs som genererar ett anpassat mått, tillsammans med en varnings regel som övervakar det måttet.

För att undvika att distributionen Miss fungerar när du försöker validera de anpassade måttets definitioner, kan du använda parametern *skipMetricValidation* i avsnittet villkor i varnings regeln, vilket gör att mått verifieringen hoppas över. Se exemplet nedan för hur du använder den här parametern i en Resource Manager-mall. Mer information finns i [exemplen på fullständiga Resource Manager-mallar för att skapa mått varnings regler](./alerts-metric-create-templates.md).

```json
"criteria": {
    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
        "allOf": [
            {
                "name" : "condition1",
                "metricName": "myCustomMetric",
                "metricNamespace": "myCustomMetricNamespace",
                "dimensions":[],
                "operator": "GreaterThan",
                "threshold" : 10,
                "timeAggregation": "Average",
                "skipMetricValidation": true
            }
        ]
    }
```

## <a name="export-the-azure-resource-manager-template-of-a-metric-alert-rule-via-the-azure-portal"></a>Exportera Azure Resource Manager mall för en mått varnings regel via Azure Portal

Genom att exportera Resource Manager-mallen för en mått varnings regel får du hjälp att förstå dess JSON-syntax och egenskaper och kan användas för att automatisera framtida distributioner.
1. Gå till avsnittet **resurs grupper** i portalen och välj den resurs grupp som innehåller regeln.
2. I avsnittet Översikt markerar du kryss rutan **Visa dolda typer** .
3. I filter **typ** väljer du *Microsoft. Insights/metricalerts*.
4. Välj den relevanta aviserings regeln om du vill visa information om den.
5. Under **Inställningar** väljer du **Exportera mall**.

## <a name="metric-alert-rules-quota-too-small"></a>Måttet för mått för varnings regler är för litet

Antalet mått för aviserings regler per prenumeration omfattas av [kvot gränser](../service-limits.md).

Om du har nått kvot gränsen kan följande steg hjälpa dig att lösa problemet:
1. Försök att ta bort eller inaktivera mått regler som inte används längre.

2. Växla till att använda måttaviseringsregler som övervakar flera resurser. Med den här funktionen kan en enda varnings regel övervaka flera resurser med bara en varnings regel som räknas mot kvoten. Mer information om den här funktionen och vilka resurstyper som stöds finns [här](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

3. Om du behöver öka kvot gränsen kan du öppna en supportbegäran och ange följande information:

    - Prenumerations-ID: n för vilka kvot gränsen måste ökas
    - Resurs typ för kvot ökningen: **mått varningar** eller **mått aviseringar (klassisk)**
    - Begärd kvotgräns

## <a name="check-total-number-of-metric-alert-rules"></a>Kontrol lera det totala antalet varnings regler för mått

Följ stegen nedan om du vill kontrol lera den aktuella användningen av mått varnings regler.

### <a name="from-the-azure-portal"></a>Från Azure-portalen

1. Öppna skärmen **Aviseringar** och klicka på **Hantera aviseringsregler**
2. Filtrera efter den relevanta prenumerationen med hjälp av list Rute kontrollen för **prenumerationen**
3. Se till att du inte filtrerar till en speciell resurs grupp, resurs typ eller resurs
4. Välj **mått** i list Rute kontrollen **signal typ**
5. Kontrol lera att List rutan **status** är inställd på **aktive rad**
6. Det totala antalet varnings regler för mått visas ovanför listan varnings regler

### <a name="from-api"></a>Från API

- PowerShell – [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2)
- REST API – [Lista efter prenumeration](/rest/api/monitor/metricalerts/listbysubscription)
- Azure CLI – [az monitor metrics alert list](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Hantera aviserings regler med Resource Manager-mallar, REST API, PowerShell eller Azure CLI

Om du får problem med att skapa, uppdatera, hämta eller ta bort mått aviseringar med hjälp av Resource Manager-mallar, REST API, PowerShell eller kommando rads gränssnittet för Azure (CLI), kan följande steg hjälpa dig att lösa problemet.

### <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

- Titta på listan över [vanliga Azure-distributionsfel](../../azure-resource-manager/templates/common-deployment-errors.md) och felsök
- Se [mått varningar Azure Resource Manager mal exemplen](./alerts-metric-create-templates.md) för att se till att du skickar alla parametrar korrekt

### <a name="rest-api"></a>REST-API

Granska [rest Apis guiden](/rest/api/monitor/metricalerts/) för att kontrol lera att du skickar alla parametrar korrekt

### <a name="powershell"></a>PowerShell

Kontrol lera att du använder rätt PowerShell-cmdlets för mått varningar:

- PowerShell-cmdletar för måttaviseringar finns i modulen [AZ. Monitor](/powershell/module/az.monitor/)
- Se till att använda cmdletarna som slutar med "v2" för nya (icke-klassiska) mått varningar (till exempel [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2))

### <a name="azure-cli"></a>Azure CLI

Kontrol lera att du använder rätt CLI-kommandon för mått varningar:

- CLI-kommandon för måttaviseringar börjar med `az monitor metrics alert`. Granska [Azure CLI-referensen](/cli/azure/monitor/metrics/alert) om du vill veta mer om syntaxen.
- Du kan se [det exempel som visar hur du använder CLI för måttaviseringar](./alerts-metric.md#with-azure-cli)
- Om du vill varna för ett anpassat mått måste du ange prefixet till måttets namn med relevant mått för namnområde: NAMESPACE.METRIC

### <a name="general"></a>Allmänt

- Om du får ett `Metric not found` fel meddelande:

   - För ett plattforms mått: kontrol lera att du använder **mått** namnet på [sidan Azure Monitor mått som stöds](../essentials/metrics-supported.md)och inte **måttets visnings namn**

   - För ett anpassat mått: kontrol lera att måttet redan har skickats (du kan inte skapa en varnings regel för ett anpassat mått som ännu inte finns) och att du tillhandahåller det anpassade måttets namnrymd (se ett exempel på en Resource Manager-mall [här](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric))

- Om du skapar [mått aviseringar för loggar](./alerts-metric-logs.md)bör du se till att lämpliga beroenden ingår. Se [exempelmallen](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs).

- Observera följande begränsningar om du skapar en varnings regel som innehåller flera villkor:

   - Du kan bara välja ett värde per dimension i varje kriterium
   - Du kan inte använda ”\*” som dimensionsvärde
   - När mått som kon figurer ATS i olika villkor stöder samma dimension måste ett konfigurerat dimensions värde uttryckligen anges på samma sätt för alla dessa mått (se ett exempel på en Resource Manager-mall [här](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria))


## <a name="no-permissions-to-create-metric-alert-rules"></a>Inga behörigheter för att skapa mått varnings regler

Om du vill skapa en regel för mått varningar måste du ha följande behörigheter:

- Läs behörighet för aviserings regelns mål resurs
- Skriv behörighet för resurs gruppen där varnings regeln skapas (om du skapar varnings regeln från Azure Portal skapas varnings regeln som standard i samma resurs grupp som mål resursen finns i)
- Läs behörighet för alla åtgärds grupper som är kopplade till aviserings regeln (om tillämpligt)


## <a name="naming-restrictions-for-metric-alert-rules"></a>Namngivnings begränsningar för mått varnings regler

Tänk på följande begränsningar för namn på mått för varnings regler:

- Det går inte att ändra regel namn för mått varningar (omdöpt) när den har skapats
- Mått för varnings regel namn måste vara unika inom en resurs grupp
- Mått varnings regel namn får inte innehålla följande tecken: * # & +:  < > ? @ % { } \ / 
- Mått varnings regel namn får inte sluta med blank steg eller punkt

> [!NOTE] 
> Om aviserings regelns namn innehåller tecken som inte är alfabetiska eller numeriska (till exempel: blank steg, skiljetecken eller symboler) kan dessa tecken vara URL-kodade när de hämtas av vissa klienter.

## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>Begränsningar när du använder dimensioner i en mått varnings regel med flera villkor

Mått aviseringar stöder aviseringar om flerdimensionella mått samt stöd för att definiera flera villkor (upp till 5 villkor per varnings regel).

Tänk på följande begränsningar när du använder dimensioner i en varnings regel som innehåller flera villkor:
- Du kan bara välja ett värde per dimension i varje villkor.
- Du kan inte använda alternativet "Välj alla aktuella och framtida värden" (Välj \* ).
- När mått som har kon figurer ATS på olika villkor stöder samma dimension måste ett konfigurerat dimensions värde uttryckligen anges på samma sätt för alla dessa mått (i de relevanta villkoren).
Exempel:
    - Överväg en regel för mått varningar som definieras på ett lagrings konto och övervakar två villkor:
        * Totalt antal **transaktioner** > 5
        * Genomsnittlig **SuccessE2ELatency** > 250 MS
    - Jag skulle vilja uppdatera det första villkoret och endast övervaka transaktioner där **ApiName** -dimensionen är lika med *"GetBlob"*
    - Eftersom både **transaktionerna** och **SuccessE2ELatency** -måtten har stöd för en **ApiName** -dimension måste jag uppdatera båda villkoren och låta båda ange dimensionen **ApiName** med värdet *"GetBlob"* .

## <a name="setting-the-alert-rules-period-and-frequency"></a>Ange period och frekvens för aviserings regeln

Vi rekommenderar att du väljer en *agg regerings kornig het (period)* som är större än *utvärderings frekvensen*, för att minska sannolikheten för att den första utvärderingen av tillagd tids serier saknas i följande fall:
-   Mått varnings regel som övervakar flera dimensioner – när en ny dimensions värde kombination läggs till
-   Mått varnings regel som övervakar flera resurser – när en ny resurs läggs till i omfånget
-   Regel varnings regel som övervakar ett mått som inte genereras kontinuerligt (glest mått) – när måttet släpps efter en period som är längre än 24 timmar där den inte har spridits

## <a name="the-dynamic-thresholds-borders-dont-seem-to-fit-the-data"></a>Kant linjerna för dynamiskt tröskelvärden verkar inte passa på data

Om beteendet för ett mått ändras nyligen kommer ändringarna inte nödvändigt vis att avspeglas i de dynamiska tröskelvärdena (övre och nedre gränser) direkt, eftersom de beräknas utifrån mått data från de senaste 10 dagarna. När du visar de dynamiska tröskelvärdena för ett angivet mått, se till att titta på mått trenden under den senaste veckan och inte bara för senaste timmar eller dagar.

## <a name="why-is-weekly-seasonality-not-detected-by-dynamic-thresholds"></a>Varför identifieras vecko säsongs beroende inte av dynamiska tröskelvärden?

För att identifiera veckovis säsongs beroende kräver modellen för dynamisk tröskel minst tre veckors historiska data. När tillräckligt med historiska data är tillgängliga identifieras varje vecko säsongs beroende som finns i mått data och modellen justeras därefter. 

## <a name="dynamic-thresholds-shows-a-negative-lower-bound-for-a-metric-even-though-the-metric-always-has-positive-values"></a>Dynamiska tröskelvärden visar ett negativt lägre gräns värde för ett mått trots att måttet alltid har positiva värden

När ett mått uppvisar stora variationer skapar dynamiska tröskelvärden en bredare modell runt mått värden, vilket kan leda till att den nedre kant linjen är lägre än noll. Mer specifikt kan detta inträffa i följande fall:
1. Känslighet är inställt på låg 
2. Median värdena är nära noll
3. Måttet uppvisar ett oregelbundet beteende med hög varians (det finns toppar eller dip i datan)

När den nedre kanten har ett negativt värde innebär det att det är plausible för måttet att uppnå ett nollvärde som anger måttets oregelbundna beteende. Du kan överväga att välja en högre känslighet eller en större *agg regerings kornig het (period)* för att göra modellen mindre känslig, eller använda alternativet *Ignorera data innan* för att undanta en nyligen irregulaity från historiska data som används för att skapa modellen.

## <a name="next-steps"></a>Nästa steg

- Allmän felsöknings information om aviseringar och meddelanden finns [i fel söknings problem i Azure Monitor aviseringar](alerts-troubleshoot.md).