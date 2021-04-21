---
title: Felsöka Azure-måttaviseringar
description: Vanliga problem med Azure Monitor måttaviseringar och möjliga lösningar.
author: harelbr
ms.author: harelbr
ms.topic: troubleshooting
ms.date: 04/12/2021
ms.openlocfilehash: fc9af94b07add5728201baaa8fa6992728a60a8c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786017"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Felsöka problem i Azure Monitor-måttaviseringar 

Den här artikeln beskriver vanliga problem i Azure Monitor [måttaviseringar](alerts-metric-overview.md) och hur du felsöker dem.

Azure Monitor aviseringar meddelar dig proaktivt när viktiga villkor finns i dina övervakningsdata. De gör att du kan identifiera och åtgärda problem innan användarna av systemet märker dem. Mer information om aviseringar finns i [Översikt över aviseringar i Microsoft Azure](./alerts-overview.md).

## <a name="metric-alert-should-have-fired-but-didnt"></a>Måttavisering bör ha uttskjutts men inte gjort det 

Om du tror att en måttavisering borde ha uttskjutts men inte utbrandades och inte finns i Azure Portal kan du prova följande steg:

1. **Konfiguration** – Granska konfigurationen av måttaviseringsregeln för att kontrollera att den är korrekt konfigurerad:
    - Kontrollera att **Sammansättningstyp och** **Sammansättningsdelade (period)** har konfigurerats som förväntat. **Sammansättningstyp** avgör hur måttvärden aggregeras (läs mer [här)](../essentials/metrics-aggregation-explained.md#aggregation-types)och Sammansättningskornighet **(period)** styr hur långt tillbaka utvärderingen aggregerar måttvärdena varje gång aviseringsregeln körs.
    -  Kontrollera att **tröskelvärdet** eller **Känslighet har** konfigurerats som förväntat.
    - För en aviseringsregel som använder dynamiska tröskelvärden kontrollerar du om avancerade inställningar har konfigurerats eftersom Antal överträdelser kan filtrera aviseringar och **Ignorera data** innan kan påverka hur tröskelvärdena **beräknas.**

       > [!NOTE] 
       > Dynamiska tröskelvärden kräver minst 3 dagar och 30 måttexempel innan de blir aktiva.

2. **Utströms men inget meddelande** – Granska listan [med uttade aviseringar för](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) att se om du kan hitta den utskjutna aviseringen. Om du kan se aviseringen i listan, men har problem med några av dess åtgärder eller meddelanden, kan du läsa mer [här.](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)

3. **Redan aktiv** – Kontrollera om det redan finns en utaktiv avisering för måtttidsserien som du förväntade dig att få en avisering för. Måttaviseringar är tillståndskänsliga, vilket innebär att när en avisering har aktiverats för en specifik måttidsserie så utlöses inte ytterligare aviseringar i den tidsserien förrän problemet inte längre observeras. Det här designvalet minskar bruset. Aviseringen löses automatiskt när aviseringsvillkoret inte uppfylls för tre på varandra följande utvärderingar.

4. **Dimensioner som** används – Om du har valt vissa [dimensionsvärden](./alerts-metric-overview.md#using-dimensions)för ett mått övervakar aviseringsregeln varje enskild tidsserie för mått (som definieras av en kombination av dimensionsvärden) för ett tröskelvärdesöverträdelse. Om du även vill övervaka tidsserien för aggregerade mått (utan valda dimensioner) konfigurerar du ytterligare en varningsregel för måttet utan att välja dimensioner.

5. **Sammansättning och tidskornighet** – Om du visualiserar måttet med hjälp av [måttdiagram](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)ser du till att:
    * Den valda **aggregeringen** i måttdiagrammet är samma som **Sammansättningstyp** i din aviseringsregel
    * Den valda **tidskornigheten** är samma som Sammansättningskornighet **(period)** i aviseringsregeln (och har inte angetts till Automatisk)

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>Måttavisering utades när den inte borde ha gjort det

Om du tror att din måttavisering inte borde ha utdata men gjorde det kan följande steg hjälpa dig att lösa problemet.

1. Granska listan med [ut sparken aviseringar](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) för att hitta den utskjutna aviseringen och klicka för att visa dess information. Granska informationen under Varför utlöstes den här aviseringen? för  att se måttdiagrammet, måttvärdet och tröskelvärdet vid den tidpunkt då aviseringen utlöstes.  

    > [!NOTE] 
    > Om du använder villkorstypen Dynamiska tröskelvärden och anser att tröskelvärdena som används inte var korrekta kan du ge feedback med ikonen för brösklar. Den här feedbacken påverkar maskininlärningsalgoritmforskningen och hjälper till att förbättra framtida identifieringar.

2. Om du har valt flera dimensionsvärden för ett mått  utlöses aviseringen när någon av måtttidsserien (som definieras av kombinationen av dimensionsvärden) överträder tröskelvärdet. Mer information om hur du använder dimensioner i måttaviseringar finns [här](./alerts-metric-overview.md#using-dimensions).

3. Granska aviseringsregelkonfigurationen för att kontrollera att den är korrekt konfigurerad:
    - Kontrollera att **Sammansättningstyp,** **Sammansättningskornighet (period)** och **Tröskelvärde** eller Känslighet har **konfigurerats** som förväntat
    - För en **aviseringsregel** som använder dynamiska tröskelvärden kontrollerar du om avancerade inställningar har konfigurerats eftersom Antal överträdelser kan filtrera aviseringar och **Ignorera data** innan kan påverka hur tröskelvärdena beräknas

   > [!NOTE]
   > Dynamiska tröskelvärden kräver minst 3 dagar och 30 måttexempel innan de blir aktiva.

4. Om du visualiserar måttet med hjälp av [måttdiagram ser du](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)till att:
    - Den valda **aggregeringen** i måttdiagrammet är samma som **Sammansättningstyp** i din aviseringsregel
    - Den valda **tidskornigheten** är samma som Sammansättningskornighet **(period)** i aviseringsregeln (och inte inställd på Automatisk)

5. Om aviseringen utlöstes när det redan finns utlösta aviseringar som övervakar samma villkor (som inte är lösta) kontrollerar du om aviseringsregeln har konfigurerats med egenskapen *autoMitigate* inställd på **falskt** (den här egenskapen kan bara konfigureras via REST/PowerShell/CLI, så kontrollera skriptet som används för att distribuera aviseringsregeln). I sådana fall löser inte aviseringsregeln utlösta aviseringar automatiskt och kräver inte att en utlöst avisering matchas innan den utlöstes igen.


## <a name="cant-find-the-metric-to-alert-on---virtual-machines-guest-metrics"></a>Det går inte att hitta måttet att avisering på – gästmått för virtuella datorer

Om du vill varna för mått för gästoperativsystemet för virtuella datorer (till exempel minne, diskutrymme) måste du ha installerat den agent som krävs för att samla in dessa data för att Azure Monitor Mått:
- [För virtuella Windows-datorer](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
- [För virtuella Linux-datorer](../essentials/collect-custom-metrics-linux-telegraf.md)

Mer information om hur du samlar in data från gästoperativsystemet på en virtuell dator finns [här.](../vm/monitor-vm-azure.md#guest-operating-system)

> [!NOTE] 
> Om du har konfigurerat att gästmått ska skickas till en Log Analytics-arbetsyta visas måtten under resursen för Log Analytics-arbetsytan och börjar visa **data** först när du har skapat en varningsregel som övervakar dem. Det gör du genom att följa stegen för att [konfigurera en måttavisering för loggar](./alerts-metric-logs.md#configuring-metric-alert-for-logs).

> [!NOTE] 
> Övervakning av ett gästmått för flera virtuella datorer med en enda aviseringsregel stöds inte av måttaviseringar för närvarande. Du kan åstadkomma detta med en [loggaviseringsregel](./alerts-unified-log.md). Det gör du genom att kontrollera att gästmåtten samlas in på en Log Analytics-arbetsyta och skapa en loggaviseringsregel på arbetsytan.

## <a name="cant-find-the-metric-to-alert-on"></a>Det går inte att hitta måttet som aviseringen ska visas för

Om du vill skapa aviseringar om ett visst mått men inte kan se det när du skapar en aviseringsregel kontrollerar du följande:
- Om du inte kan se mått för resursen [kontrollerar du om resurstypen har stöd för måttaviseringar](./alerts-metric-near-real-time.md).
- Om du kan se vissa mått för resursen, men inte ett specifikt mått, [kontrollerar du om måttet är tillgängligt](../essentials/metrics-supported.md). I så fall kontrollerar du måttbeskrivningen för att se om måttet bara är tillgängligt i vissa versioner eller utgåvor av resursen.
- Om måttet inte är tillgängligt för resursen kan det vara tillgängligt i resursloggarna och kan då övervakas med hjälp av loggaviseringar. Här kan du läsa mer om att [samla in och analysera resursloggar från en Azure-resurs](../essentials/tutorial-resource-logs.md).

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Det går inte att hitta måttdimensionen att avisering om

Observera följande om du vill varna för [specifika dimensionsvärden](./alerts-metric-overview.md#using-dimensions)för ett mått , men inte kan hitta dessa värden:

1. Det kan ta några minuter innan dimensionsvärden visas under listan **Dimensionsvärden**
2. Dimensionsvärden som visas baseras på måttdata som samlats in under den senaste dagen
3. Om dimensionsvärdet inte har skickats än eller inte visas, kan du använda alternativet Lägg till anpassat värde för att lägga till ett anpassat dimensionsvärde
4. Om du vill varna om alla möjliga värden för en dimension (inklusive framtida värden) väljer du alternativet Markera alla aktuella och framtida värden
5. Anpassade måttdimensioner för Application Insights resurser är inaktiverade som standard. Om du vill aktivera samlingen med dimensioner för dessa anpassade mått kan du se [här](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>Måttaviseringsregler som fortfarande har definierats för en borttagna resurs 

Vid borttagning av en Azure-resurs tas inte associerade måttaviseringsregler bort automatiskt. Ta bort aviseringsregler som är associerade med en resurs som har tagits bort:

1. Öppna den resursgrupp där den borttagna resursen definierades
1. I den lista som visar resurserna markerar du kryssrutan **Visa dolda typer**
1. Filtrera listan efter Typ == **microsoft.insights/metricalerts**
1. Välj relevanta aviseringsregler och välj Ta **bort**

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>Göra så att måttaviseringar inträffar varje gång mitt villkor uppfylls

Måttaviseringar är tillståndsfulla som standard och därför utlösts inte ytterligare aviseringar om det redan finns en utlöst avisering i en viss tidsserie. Om du vill göra en specifik måttaviseringsregel tillståndslös och få aviseringar för varje utvärdering där aviseringsvillkoret uppfylls skapar du aviseringsregeln programmatiskt (till exempel via [Resource Manager](./alerts-metric-create-templates.md), [PowerShell,](/powershell/module/az.monitor/) [REST](/rest/api/monitor/metricalerts/createorupdate), [CLI)](/cli/azure/monitor/metrics/alert)och anger egenskapen *autoMitigate* till False.

> [!NOTE] 
> Att göra en måttaviseringsregel tillståndslös förhindrar att utlösta aviseringar löses, så även om villkoret inte uppfylls längre, förblir de utlösta aviseringarna i ett utlöst tillstånd fram till kvarhållningsperioden på 30 dagar.

## <a name="define-an-alert-rule-on-a-custom-metric-that-isnt-emitted-yet"></a>Definiera en aviseringsregel för ett anpassat mått som inte har genereras ännu

När du skapar en måttaviseringsregel verifieras måttnamnet mot API:et [för](/rest/api/monitor/metricdefinitions/list) måttdefinitioner för att kontrollera att det finns. I vissa fall vill du skapa en aviseringsregel för ett anpassat mått även innan det genereras. När du till exempel skapar (med en Resource Manager mall) en Application Insights resurs som ger ett anpassat mått, tillsammans med en aviseringsregel som övervakar måttet.

För att undvika att distributionen misslyckas när du försöker verifiera definitionerna för det anpassade måttet kan du använda parametern *skipMetricValidation* i villkorsavsnittet i aviseringsregeln, vilket gör att måttverifieringen hoppas över. Se exemplet nedan för hur du använder den här parametern i en Resource Manager mall. Mer information finns i de fullständiga [Resource Manager för att skapa måttaviseringsregler.](./alerts-metric-create-templates.md)

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

## <a name="export-the-azure-resource-manager-template-of-a-metric-alert-rule-via-the-azure-portal"></a>Exportera mallen Azure Resource Manager en måttaviseringsregel via Azure Portal

Genom att exportera Resource Manager mall för en måttaviseringsregel får du hjälp att förstå dess JSON-syntax och -egenskaper och kan användas för att automatisera framtida distributioner.
1. I Azure Portal du aviseringsregeln för att visa dess information.
2. Klicka på **Egenskaper**.
3. Under **Automation** väljer du **Exportera mall**.

## <a name="metric-alert-rules-quota-too-small"></a>Kvot för måttaviseringsregler är för liten

Det tillåtna antalet måttaviseringsregler per prenumeration omfattas av [kvotgränser.](../service-limits.md)

Om du har nått kvotgränsen kan följande steg hjälpa dig att lösa problemet:
1. Prova att ta bort eller inaktivera måttaviseringsregler som inte används längre.

2. Växla till att använda måttaviseringsregler som övervakar flera resurser. Med den här funktionen kan en enskild aviseringsregel övervaka flera resurser med bara en aviseringsregel som räknas mot kvoten. Mer information om den här funktionen och vilka resurstyper som stöds finns [här](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

3. Om du behöver öka kvotgränsen öppnar du en supportbegäran och anger följande information:

    - Prenumerations-ID:n som kvotgränsen måste ökas för
    - Resurstyp för kvotökningen: **Måttaviseringar** eller **måttaviseringar (klassisk)**
    - Begärd kvotgräns

## <a name="check-total-number-of-metric-alert-rules"></a>Kontrollera det totala antalet måttaviseringsregler

Följ stegen nedan om du vill kontrollera den aktuella användningen av måttaviseringsregler.

### <a name="from-the-azure-portal"></a>Från Azure-portalen

1. Öppna skärmen **Aviseringar** och klicka på **Hantera aviseringsregler**
2. Filtrera efter relevant prenumeration med hjälp av **listringskontrollen** Prenumeration
3. Se till ATT DU INTE filtrerar till en specifik resursgrupp, resurstyp eller resurs
4. I **listrutan Signaltyp** väljer du **Mått**
5. Kontrollera att **listreringskontrollen** Status är inställd på **Aktiverad**
6. Det totala antalet måttaviseringsregler visas ovanför listan med aviseringsregler

### <a name="from-api"></a>Från API

- PowerShell – [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2)
- REST API – [Lista efter prenumeration](/rest/api/monitor/metricalerts/listbysubscription)
- Azure CLI – [az monitor metrics alert list](/cli/azure/monitor/metrics/alert#az_monitor_metrics_alert_list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Hantera aviseringsregler med Resource Manager mallar, REST API, PowerShell eller Azure CLI

Om du får problem med att skapa, uppdatera, hämta eller ta bort måttaviseringar med hjälp av Resource Manager-mallar, REST API, PowerShell eller Azure-kommandoradsgränssnittet (CLI) kan följande steg hjälpa dig att lösa problemet.

### <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

- Titta på listan över [vanliga Azure-distributionsfel](../../azure-resource-manager/templates/common-deployment-errors.md) och felsök
- Se [måttaviseringarna Azure Resource Manager exempelmallen](./alerts-metric-create-templates.md) för att se till att du vidarebefordrar alla parametrar korrekt

### <a name="rest-api"></a>REST-API

Gå REST API [guiden](/rest/api/monitor/metricalerts/) för att kontrollera att du har godkänt alla parametrar

### <a name="powershell"></a>PowerShell

Kontrollera att du använder rätt PowerShell-cmdlets för måttaviseringar:

- PowerShell-cmdletar för måttaviseringar finns i modulen [AZ. Monitor](/powershell/module/az.monitor/)
- Se till att använda cmdletarna som slutar med "V2" för nya (icke-klassiska) måttaviseringar (till exempel [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2))

### <a name="azure-cli"></a>Azure CLI

Kontrollera att du använder rätt CLI-kommandon för måttaviseringar:

- CLI-kommandon för måttaviseringar börjar med `az monitor metrics alert`. Granska [Azure CLI-referensen](/cli/azure/monitor/metrics/alert) om du vill veta mer om syntaxen.
- Du kan se [det exempel som visar hur du använder CLI för måttaviseringar](./alerts-metric.md#with-azure-cli)
- Om du vill varna för ett anpassat mått måste du ange prefixet till måttets namn med relevant mått för namnområde: NAMESPACE.METRIC

### <a name="general"></a>Allmänt

- Om du får ett `Metric not found` felmeddelande:

   - För ett [plattformsmått:](../essentials/metrics-supported.md)Kontrollera att  du använder måttnamnet från Azure Monitor måttsidan som stöds och inte visningsnamnet **för mått**

   - För ett anpassat mått: Kontrollera att måttet redan genereras (du kan inte skapa en aviseringsregel för ett anpassat mått som ännu inte finns) och att du tillhandahåller det anpassade måttet namnområde (se ett exempel på Resource Manager-mall [här](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric))

- Om du skapar [måttaviseringar för loggar](./alerts-metric-logs.md)ser du till att lämpliga beroenden ingår. Se [exempelmallen](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs).

- Observera följande begränsningar om du skapar en aviseringsregel som innehåller flera kriterier:

   - Du kan bara välja ett värde per dimension i varje kriterium
   - Du kan inte använda ”\*” som dimensionsvärde
   - När mått som har konfigurerats i olika kriterier stöder samma dimension måste ett konfigurerat dimensionsvärde uttryckligen anges på samma sätt för alla dessa mått (se ett Resource Manager mallexempel [här](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria))


## <a name="no-permissions-to-create-metric-alert-rules"></a>Inga behörigheter att skapa måttaviseringsregler

Om du vill skapa en måttaviseringsregel måste du ha följande behörigheter:

- Läsbehörighet för aviseringsregelns målresurs
- Skrivbehörighet för resursgruppen där aviseringsregeln skapas (om du skapar aviseringsregeln från Azure Portal skapas aviseringsregeln som standard i samma resursgrupp där målresursen finns)
- Läsbehörighet för alla åtgärdsgrupper som är associerade med aviseringsregeln (om tillämpligt)


## <a name="naming-restrictions-for-metric-alert-rules"></a>Namngivningsbegränsningar för måttaviseringsregler

Överväg följande begränsningar för namn på måttaviseringsregel:

- Namn på måttaviseringsregel kan inte ändras (byta namn) när de har skapats
- Namn på måttaviseringsregel måste vara unika inom en resursgrupp
- Namn på måttaviseringsregel får inte innehålla följande tecken: * # & + : < > ? @ % { } \ / 
- Namn på måttaviseringsregel får inte sluta med ett blanksteg eller en punkt

> [!NOTE] 
> Om aviseringsregelns namn innehåller tecken som inte är alfabetiska eller numeriska (till exempel blanksteg, skiljetecken eller symboler) kan dessa tecken vara URL-kodade när de hämtas av vissa klienter.

## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>Begränsningar när du använder dimensioner i en måttaviseringsregel med flera villkor

Måttaviseringar stöder aviseringar för flerdimensionella mått samt stöd för att definiera flera villkor (upp till 5 villkor per varningsregel).

Tänk på följande begränsningar när du använder dimensioner i en aviseringsregel som innehåller flera villkor:
- Du kan bara välja ett värde per dimension i varje villkor.
- Du kan inte använda alternativet för att välja alla aktuella och framtida värden (Välj \* ).
- När mått som har konfigurerats i olika villkor stöder samma dimension måste ett konfigurerat dimensionsvärde uttryckligen anges på samma sätt för alla dessa mått (i relevanta villkor).
Exempel:
    - Överväg en måttaviseringsregel som har definierats för ett lagringskonto och övervakar två villkor:
        * Totalt **antal transaktioner** > 5
        * Average **SuccessE2ELatency** > 250 ms
    - Jag vill uppdatera det första villkoret och endast övervaka transaktioner där **ApiName-dimensionen** är lika med *"GetBlob"*
    - Eftersom både måtten **Transactions** och **SuccessE2ELatency** stöder en **ApiName-dimension** måste jag uppdatera båda villkoren och få båda att ange **ApiName-dimensionen** med värdet *"GetBlob".*

## <a name="setting-the-alert-rules-period-and-frequency"></a>Ange aviseringsregelns period och frekvens

Vi rekommenderar att du väljer en sammansättningskornighet *(period)* som är större än utvärderingsfrekvensen *för*, för att minska sannolikheten för att den första utvärderingen av tillagda tidsserier saknas i följande fall:
-   Måttaviseringsregel som övervakar flera dimensioner – När en ny kombination av dimensionsvärde läggs till
-   Måttaviseringsregel som övervakar flera resurser – När en ny resurs läggs till i omfånget
-   Regel för måttavisering som övervakar ett mått som inte genereras kontinuerligt (glesa mått) – När måttet genereras efter en period som är längre än 24 timmar då det inte har genereras

## <a name="the-dynamic-thresholds-borders-dont-seem-to-fit-the-data"></a>Kantlinjerna för dynamiska tröskelvärden verkar inte passa in i data

Om beteendet för ett mått har ändrats nyligen återspeglas ändringarna inte nödvändigtvis direkt i kantlinjerna för dynamiskt tröskelvärde (övre och nedre gränser), eftersom de beräknas baserat på måttdata från de senaste 10 dagarna. När du visar kantlinjerna för dynamiskt tröskelvärde för ett visst mått ska du titta på måtttrenden under den senaste veckan och inte bara för de senaste timmarna eller dagarna.

## <a name="why-is-weekly-seasonality-not-detected-by-dynamic-thresholds"></a>Varför identifieras inte säsongsvariationer varje vecka av dynamiska tröskelvärden?

För att kunna identifiera säsongsberoenden varje vecka kräver modellen dynamiska tröskelvärden minst tre veckors historiska data. När tillräckligt med historiska data är tillgängliga identifieras alla veckovisa säsongsvariationer som finns i måttdata och modellen justeras i enlighet med detta. 

## <a name="dynamic-thresholds-shows-a-negative-lower-bound-for-a-metric-even-though-the-metric-always-has-positive-values"></a>Dynamiska tröskelvärden visar en negativ nedre gräns för ett mått även om måttet alltid har positiva värden

När ett mått uppvisar stora variationer bygger dynamiska tröskelvärden en bredare modell kring måttvärdena, vilket kan resultera i att den nedre kantlinjen är under noll. Mer specifikt kan detta inträffa i följande fall:
1. Känsligheten är inställd på låg 
2. Medianvärdena är nära noll
3. Måttet uppvisar ett oregelbundet beteende med hög avvikelse (det finns toppar eller dalar i data)

När den nedre gränsen har ett negativt värde innebär det att det är möjligt för måttet att nå ett nollvärde givet måttens oregelbundna beteende. Du kan överväga att välja en högre känslighet eller en större sammansättningskornighet *(Period)* för att göra modellen mindre känslig, eller använda alternativet Ignorera *data* före för att undanta en nylig irregulaity från de historiska data som används för att skapa modellen.

## <a name="next-steps"></a>Nästa steg

- Allmän felsökningsinformation om aviseringar och meddelanden finns i Felsöka [problem i Azure Monitor aviseringar.](alerts-troubleshoot.md)
