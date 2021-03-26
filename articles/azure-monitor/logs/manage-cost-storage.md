---
title: Hantera användning och kostnader för Azure Monitor loggar
description: Lär dig hur du ändrar pris planen och hanterar data volymer och bevarande principer för Log Analytics arbets ytan i Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: bwren
ms.openlocfilehash: 5048364aed1eea8d0c32d9134a4ba5a22d28b989
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105560462"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Hantera användning och kostnader med Azure Monitor-loggar    

> [!NOTE]
> Den här artikeln beskriver hur du förstår och styr dina kostnader för Azure Monitor loggar. En relaterad artikel, [övervaknings användning och uppskattade kostnader](../usage-estimated-costs.md) beskriver hur du visar användning och uppskattade kostnader i flera Azure-övervakningsfunktioner för olika pris modeller. Alla priser och kostnader som visas i den här artikeln är till exempel endast avsedda. 

Azure Monitor loggar har utformats för att skala och stödja insamling, indexering och lagring av stora mängder data per dag från vilken källa som helst i företaget eller som distribueras i Azure.  Det kan vara en primär driv rutin för din organisation, kostnads effektivitet är i slut ändan av den underliggande driv rutinen. Därför är det viktigt att förstå att kostnaden för en Log Analytics-arbetsyta inte enbart baseras på mängden insamlade data, den är också beroende av den valda planen och hur länge du väljer att lagra data som genererats från dina anslutna källor.  

I den här artikeln går vi igenom hur du kan övervaka inmatade data volymer och lagrings tillväxten på ett proaktivt sätt och definiera gränser för att kontrol lera de associerade kostnaderna. 

## <a name="pricing-model"></a>Prismodell

Standard priset för Log Analytics är en modell där **du betalar per** användning baserat på data volym som matas in och eventuellt för längre data kvarhållning. Data volymen mäts som storleken på de data som ska lagras i GB (10 ^ 9 byte). Varje Log Analytics arbets yta debiteras som en separat tjänst och bidrar till fakturan för din Azure-prenumeration. Mängden data inmatning kan vara avsevärd beroende på följande faktorer: 

  - Antal aktiverade hanterings lösningar och deras konfiguration
  - Antal övervakade virtuella datorer
  - Typ av data som samlas in från varje övervakad virtuell dator 
  
Förutom modellen betala per användning har Log Analytics **kapacitets reservations** nivåer som gör att du kan spara så mycket som 25% jämfört med priset för betala per användning. Med kapacitets reservations priset kan du köpa en reservation som börjar på 100 GB/dag. All användning ovanför reservations nivån debiteras enligt priset för betala per användning. Kapacitets reservationens nivåer har en period på 31 dagar. Under perioden kan du ändra till en kapacitets reservations nivå på högre nivå (som startar om perioden på 31 dagar), men du kan inte gå tillbaka till betala per användning eller till en reservations nivå med lägre kapacitet förrän perioden är slut. Faktureringen för kapacitets nivåer för kapacitet görs per dag. [Läs mer](https://azure.microsoft.com/pricing/details/monitor/) om hur du Log Analytics priser för betala per användning och kapacitets reservationer. 

På alla pris nivåer beräknas en händelses data storlek från en sträng representation av de egenskaper som lagras i Log Analytics för den här händelsen, om data skickas från en agent eller läggs till under inmatnings processen. Detta inkluderar alla [anpassade fält](custom-fields.md) som läggs till som data samlas in och lagras sedan i Log Analytics. Flera egenskaper som är gemensamma för alla data typer, inklusive vissa [Log Analytics standard egenskaper](./log-standard-columns.md), undantas i beräkningen av händelse storleken. Detta inkluderar `_ResourceId` ,,, `_SubscriptionId` `_ItemId` `_IsBillable` `_BilledSize` och `Type` . Alla andra egenskaper som lagras i Log Analytics ingår i beräkningen av händelse storleken. Vissa data typer är kostnads fria från data inmatnings avgifter helt, till exempel AzureActivity, pulsslag och användnings typer. Du kan använda `_IsBillable` egenskapen som visas [nedan](#data-volume-for-specific-events)för att avgöra om en händelse uteslöts från faktureringen för data inmatning. Användningen rapporteras i GB (1,0 E9 byte). 

Observera också att vissa lösningar, till exempel [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/), [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/) och [konfigurations hantering](https://azure.microsoft.com/pricing/details/automation/) har sina egna pris modeller. 

### <a name="log-analytics-dedicated-clusters"></a>Log Analytics dedikerade kluster

Log Analytics dedikerade kluster är samlingar av arbets ytor i ett enda hanterat Azure Datautforskaren-kluster för att stödja avancerade scenarier, till exempel [Kundhanterade nycklar](customer-managed-keys.md).  Log Analytics dedikerade kluster använder en pris modell för kapacitets reservationer som måste konfigureras till minst 1000 GB/dag. Den här kapacitets nivån har 25% rabatt jämfört med priset för betala per användning. All användning ovanför reservations nivån debiteras enligt priset för betala per användning. Kluster kapacitets reservationen har en 31-dagars åtagande period efter att reservations nivån har ökat. Under åtagande perioden går det inte att minska kapacitets reservations nivån, men den kan ökas när som helst. När arbets ytor är kopplade till ett kluster görs data inmatnings faktureringen för dessa arbets ytor på kluster nivå med den konfigurerade kapacitets reservations nivån. Lär dig mer om hur du [skapar ett Log Analytics kluster](customer-managed-keys.md#create-cluster) och [kopplar arbets ytor till den](customer-managed-keys.md#link-workspace-to-cluster). Pris informationen för kapacitets reservationen finns på [sidan Azure Monitor priser]( https://azure.microsoft.com/pricing/details/monitor/).  

Reservations nivån för kluster kapaciteten konfigureras via programmering med Azure Resource Manager med hjälp av `Capacity` parametern under `Sku` . `Capacity`Anges i enheter om GB och kan ha värden på 1000 GB/dag eller mer i steg om 100 GB/dag. Detta beskrivs i [Azure Monitor kundhanterad nyckel](customer-managed-keys.md#create-cluster). Om ditt kluster behöver en reservation över 2000 GB/dag kontaktar du oss på [LAIngestionRate@microsoft.com](mailto:LAIngestionRate@microsoft.com) .

Det finns två fakturerings lägen för användning i ett kluster. Dessa kan anges av- `billingType` parametern när [du konfigurerar klustret](customer-managed-keys.md#customer-managed-key-operations). De två lägena är: 

1. **Kluster**: i det här fallet (som är standard) görs faktureringen för inmatade data på kluster nivå. De inmatade data mängderna från varje arbets yta som är kopplad till ett kluster sammanställs för att beräkna den dagliga fakturan för klustret. Observera att tilldelningar per nod från [Azure Security Center](../../security-center/index.yml) tillämpas på arbets ytans nivå före denna agg regering av sammanställda data för alla arbets ytor i klustret. 

2. **Arbets ytor**: kostnaderna för kapacitets reservationen för klustret anges i proportion till arbets ytorna i klustret (efter redovisningen av tilldelningar per nod från [Azure Security Center](../../security-center/index.yml) för varje arbets yta.) Om den totala data volymen som matas in i en arbets yta för en dag är lägre än kapacitets reservationen debiteras varje arbets yta för sina inmatade data med den effektiva reservations taxan per GB som faktureras en bråkdel av kapacitets reservationen och den oanvända delen av kapacitets reservationen debiteras till kluster resursen. Om den totala data volymen som matas in på en arbets yta för en dag är mer än kapacitets reservationen debiteras varje arbets yta för en bråkdel av kapacitets reservationen baserat på den inmatade data dagen och varje arbets yta för en bråkdel av inmatade data ovanför kapacitets reservationen. Det finns inget debiteras för kluster resursen om den totala data volymen som matas in på en arbets yta för en dag är över kapacitets reservationen.

I kluster fakturerings alternativ faktureras data kvarhållning per arbets yta. Observera att kluster faktureringen startar när klustret skapas, oavsett om arbets ytorna har kopplats till klustret. Observera också att arbets ytor som är kopplade till ett kluster inte längre har en pris nivå.

## <a name="estimating-the-costs-to-manage-your-environment"></a>Beräkna kostnaderna för att hantera din miljö 

Om du inte använder Azure Monitor loggar än kan du använda [pris Kalkylatorn för Azure Monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) för att beräkna kostnaden för att använda Log Analytics. Börja med att ange "Azure Monitor" i sökrutan och klicka på den resulterande Azure Monitor panelen. Bläddra nedåt på sidan för att Azure Monitor och välj Log Analytics i list rutan typ.  Här kan du ange antalet virtuella datorer och de GB data GB som du förväntar dig att samla in från varje virtuell dator. Normalt används 1 till 3 GB data månad från en typisk virtuell Azure-dator. Om du redan utvärderar Azure Monitor loggar redan kan du använda din data statistik från din egen miljö. Nedan finns information om hur du fastställer [antalet övervakade virtuella datorer](#understanding-nodes-sending-data) och [mängden data som din arbets yta](#understanding-ingested-data-volume)tar med. 

## <a name="understand-your-usage-and-estimate-costs"></a>Förstå din användning och beräkna kostnader

Om du använder Azure Monitor loggar nu är det enkelt att förstå vad kostnaderna beror på de senaste användnings mönstren. Det gör du genom att använda  **Log Analytics användning och beräknade kostnader** för att granska och analysera data användningen. Detta visar hur mycket data som samlas in av varje lösning, hur mycket data som behålls och en uppskattning av dina kostnader baserat på mängden data som matas in och eventuell ytterligare kvarhållning utöver den mängd som ingår.

:::image type="content" source="media/manage-cost-storage/usage-estimated-cost-dashboard-01.png" alt-text="Användning och uppskattade kostnader":::

Om du vill utforska dina data mer detaljerat klickar du på ikonen längst upp till höger i något av diagrammen på sidan **användning och uppskattade kostnader** . Nu kan du arbeta med den här frågan och utforska mer information om din användning.  

:::image type="content" source="media/manage-cost-storage/logs.png" alt-text="Vyn loggar":::

På sidan **användning och uppskattade kostnader** kan du granska din data volym för månaden. Detta inkluderar alla fakturerbara data som har tagits emot och sparats i Log Analytics-arbetsytan.  
 
Log Analytics avgifter läggs till på din Azure-faktura. Du kan se information om din Azure-faktura under fakturerings avsnittet i Azure Portal eller i [Azure-faktureringsportal](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Visa Log Analytics användning på din Azure-faktura 

Azure ger en fantastisk mängd användbara funktioner i [Azure Cost Management + fakturerings](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=%2fazure%2fbilling%2fTOC.json) hubben. Med funktionen "cost Analysis" kan du till exempel Visa dina utgifter för Azure-resurser. Lägg först till ett filter efter "resurs typ" (till Microsoft. operationalinsights/Workspace för Log Analytics och Microsoft. operationalinsights/Cluster for Log Analytics Clusters) så att du kan spåra dina Log Analytics utgifter. Välj sedan "mäta kategori" eller "mätare" för "Gruppera efter".  Observera att andra tjänster, till exempel Azure Security Center och Azure Sentinel, också fakturerar användningen mot Log Analytics arbets ytans resurser. Om du vill se mappningen till tjänst namnet kan du välja tabellvy i stället för ett diagram. 

Om du vill ha mer information om din användning kan du [ladda ned information om din användning från Azure-portalen](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal). I det nedladdade kalkylbladet visas användning per Azure-resurs (till exempel Log Analytics-arbetsytan) per dag. I det här Excel-kalkylbladet hittar du användning från dina Log Analytics-arbetsytor genom att först filtrera fram kolumnen "mätar kategori" för att Visa "Log Analytics", "Insight and Analytics" (används av några av de äldre pris nivåerna) och "Azure Monitor" (används av pris nivåer för kapacitets reservation) och Lägg sedan till ett filter i kolumnen "instance ID", som är "innehåller arbets yta" eller "innehåller kluster" (den senare för att inkludera Log Analytics kluster användning). Användningen visas i kolumnen "Förbrukat antal" och enheten för varje post visas i kolumnen "enhets mått".  Mer information som hjälper dig att [förstå Microsoft Azure-fakturan](../../cost-management-billing/understand/review-individual-bill.md). 

## <a name="changing-pricing-tier"></a>Ändra pris nivå

Om du vill ändra Log Analytics pris nivå för arbets ytan, 

1. I Azure Portal öppnar du **användning och uppskattade kostnader** från arbets ytan där du ser en lista över de pris nivåer som är tillgängliga för den här arbets ytan.

2. Granska de beräknade kostnaderna för var och en av pris nivåerna. Den här uppskattningen baseras på de senaste 31 användnings dagarna, så den här kostnads uppskattningen är beroende av de senaste 31 dagarna som är representativ för din typiska användning. I exemplet nedan kan du se hur, baserat på data mönstren från de senaste 31 dagarna, att den här arbets ytan kostar mindre i nivån betala per användning (#1) jämfört med kapacitets nivån 100 GB/dag för kapacitet (#2).  

:::image type="content" source="media/manage-cost-storage/pricing-tier-estimated-costs.png" alt-text="Prisnivåer":::
    
3. När du har granskat de uppskattade kostnaderna baserat på de senaste 31 dagarna av användningen klickar du på **Välj** för att ändra pris nivån.  

Du kan också [ställa in pris nivån via Azure Resource Manager](./resource-manager-workspace.md) med hjälp av `sku` parametern ( `pricingTier` i Azure Resource Manager mal len). 

## <a name="legacy-pricing-tiers"></a>Äldre prisnivåer

Prenumerationer som hade en Log Analytics arbets yta eller Application Insights resurs i den 2 april 2018, eller som är kopplade till en Enterprise-avtal som startades före den 1 februari 2019, fortsätter att ha åtkomst till de äldre pris nivåerna: **kostnads fri**, **fristående (per GB)** och **per nod (OMS)**.  Arbets ytor i den kostnads fria pris nivån har en daglig data inmatning som är begränsad till 500 MB (förutom säkerhets data typer som samlas in av [Azure Security Center](../../security-center/index.yml)) och datakvarhållning är begränsad till 7 dagar. Den kostnads fria pris nivån är endast avsedd för utvärderings ändamål. Arbets ytorna i de fristående eller per-nodens pris nivåer har användar konfigurerbar kvarhållning från 30 till 730 dagar.

Användningen på den fristående pris nivån debiteras av den inmatade data volymen. Den rapporteras i **log Analyticss** tjänsten och mätaren heter "data analysed". 

Pris nivå avgifter per nod per övervakad virtuell dator (nod) på en tids kornig het. För varje övervakad nod allokeras arbets ytan 500 MB data per dag som inte faktureras. Den här allokeringen beräknas med Tim kornig het och sammanställs på arbets ytans nivå varje dag. Data som matats in ovanför den sammanställda dagliga data tilldelningen faktureras per GB som överanvändning av data. Observera att tjänsten **Insight and Analytics** för att Log Analytics användning om arbets ytan finns på pris nivån per nod på din faktura. Användningen rapporteras enligt tre meter:

1. Nod: det här är användning för antalet övervakade noder (VM: ar) i antal noder * månader.
2. Överanvändning per nod: Detta är antalet GB data som matas in utöver den aggregerade data tilldelningen.
3. Data som inkluderas per nod: Detta är den mängd inmatade data som omfattades av den aggregerade data tilldelningen. Den här mätaren används också när arbets ytan finns på alla pris nivåer för att visa mängden data som omfattas av Azure Security Center.

> [!TIP]
> Om din arbets yta har åtkomst till pris nivån **per nod** , men du undrar om det skulle vara kostnads fritt i en nivå där du betalar per användning, kan du [använda frågan nedan](#evaluating-the-legacy-per-node-pricing-tier) för att enkelt få en rekommendation. 

Arbets ytor som skapats före april 2016 kan också komma åt de ursprungliga pris nivåerna **standard** och **Premium** som har en fast data lagring på 30 respektive 365 dagar. Det går inte att skapa nya arbets ytor på pris nivåerna **standard** eller **Premium** , och om en arbets yta flyttas ut från dessa nivåer går det inte att flytta tillbaka den. Mätare för data inmatning för dessa äldre nivåer kallas "data analyseras".

Det finns även vissa beteenden mellan användningen av äldre Log Analyticss nivåer och hur användning faktureras för [Azure Security Center](../../security-center/index.yml). 

1. Om arbets ytan finns på den äldre standard-eller Premium-nivån faktureras Azure Security Center bara för Log Analytics data inmatning, inte per nod.
2. Om arbets ytan är i bakåtkompatibelt läge per nod kommer Azure Security Center att faktureras med den aktuella [Azure Security Center-nodens pris modell](https://azure.microsoft.com/pricing/details/security-center/). 
3. I andra pris nivåer (inklusive kapacitets reservationer), om Azure Security Center har Aktiver ATS före den 19 juni 2017, faktureras Azure Security Center bara för Log Analytics data inmatning. Annars kommer Azure Security Center att faktureras med den aktuella Azure Security Center-nodens pris modell.

Mer information om begränsningar för pris nivåer finns i [Azure-prenumerationer, tjänst begränsningar, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces).

Ingen av de äldre pris nivåerna har regional-baserade priser.  

> [!NOTE]
> Om du vill använda rättigheterna som kommer från inköp av OMS E1 Suite, OMS E2 Suite eller OMS Add-On för System Center väljer du pris nivån Log Analytics *per nod* .

## <a name="log-analytics-and-security-center"></a>Log Analytics och Security Center

[Azure Security Center](../../security-center/index.yml) faktureringen är nära knuten till Log Analytics fakturering. Security Center ger 500 MB/nod/dag-allokeringar i följande delmängd av [säkerhets data typer](/azure/azure-monitor/reference/tables/tables-category#security) (WindowsEvent, SecurityAlert, SecurityBaseline, SecurityBaselineSummary, SecurityDetection, SecurityEvent, WindowsFirewall, MaliciousIPCommunication, LinuxAuditLog, SysmonEvent, ProtectionStatus) och data typerna Update och UpdateSummary när uppdateringshantering-lösningen inte körs på arbets ytan eller lösnings målet har Aktiver ATS. Om arbets ytan är på pris nivån bakåtkompatibelt per nod, kombineras Security Center-och Log Analytics tilldelningarna gemensamt för alla fakturerbara inmatade data.  

## <a name="change-the-data-retention-period"></a>Ändra kvarhållningsperioden för data

Följande steg beskriver hur du konfigurerar hur länge loggdata sparas i din arbets yta. Datakvarhållning på arbets ytans nivå kan konfigureras från 30 till 730 dagar (2 år) för alla arbets ytor om de inte använder den äldre pris nivån kostnads fri. Kvarhållning för enskilda data typer kan anges så lågt som 4 dagar. [Lär dig mer](https://azure.microsoft.com/pricing/details/monitor/) om priser för längre data kvarhållning.  Överväg att använda [Log Analytics data export för arbets ytan](logs-data-export.md)om du vill behålla data som är längre än 730 dagar.

### <a name="workspace-level-default-retention"></a>Standard kvarhållning på arbetsyte nivå

Ange standard kvarhållning för din arbets yta genom att 
 
1. I Azure Portal, från din arbets yta, väljer du **användning och uppskattade kostnader** i det vänstra fönstret.
2. På sidan **Användning och uppskattade kostnader** klickar du på **Datakvarhållning** högst upp på sidan.
3. I fönstret flyttar du reglaget för att öka eller minska antal dagar. Klicka sedan på **OK**.  Om du är på den *kostnads fria* nivån kan du inte ändra data lagrings perioden och du måste uppgradera till den betalda nivån för att kunna styra den här inställningen.

:::image type="content" source="media/manage-cost-storage/manage-cost-change-retention-01.png" alt-text="Ändra inställning för kvarhållning av data arbets yta":::

När kvarhållning sänks, finns det en tids period på flera dagar innan de data som är äldre än den nya inställningen för kvarhållning tas bort. 

Sidan **datakvarhållning** tillåter inställningar för kvarhållning på 30, 31, 60, 90, 120, 180, 270, 365, 550 och 730 dagar. Om det krävs en annan inställning, kan konfigureras med hjälp av [Azure Resource Manager](./resource-manager-workspace.md) med hjälp av `retentionInDays` parametern. När du ställer in data kvarhållning på 30 dagar kan du utlösa en omedelbar rensning av äldre data med hjälp av `immediatePurgeDataOn30Days` parametern (vilket eliminerar den flera dagars respitperioden). Detta kan vara användbart för kompatibilitets-relaterade scenarier där omedelbar data borttagning är tvingande. Den här funktionen för omedelbar rensning exponeras bara via Azure Resource Manager. 

Arbets ytor med en lagring på 30 dagar kan faktiskt behålla data i 31 dagar. Om det är absolut nödvändigt att data sparas i 30 dagar kan du använda Azure Resource Manager för att ange kvarhållning till 30 dagar och med `immediatePurgeDataOn30Days` parametern.  

Två data typer-- `Usage` och `AzureActivity` --bevaras i minst 90 dagar som standard och det kostar inget att debitera för denna 90-dagars kvarhållning. Om kvarhållning av arbets yta höjs över 90 dagar, kommer kvarhållning av dessa data typer också att öka.  Dessa data typer är också kostnads fria från data inmatnings kostnader. 

Data typer från arbets ytans baserade Application Insights resurser (,,,,,,,, `AppAvailabilityResults` `AppBrowserTimings` `AppDependencies` `AppExceptions` `AppEvents` `AppMetrics` `AppPageViews` `AppPerformanceCounters` `AppRequests` `AppSystemEvents` och `AppTraces` ) behålls också i 90 dagar som standard och det kostar inget att debitera för denna 90-dagars kvarhållning. Deras kvarhållning kan justeras med hjälp av funktionen kvarhållning av data typ. 

Observera att [rensnings-API](/rest/api/loganalytics/workspacepurge/purge) för Log Analytics inte påverkar fakturering av kvarhållning och är avsett att användas i mycket begränsade fall. För att minska din kvarhållning måste kvarhållningsperioden minskas antingen för arbets ytan eller för vissa data typer. 

### <a name="retention-by-data-type"></a>Kvarhållning efter datatyp

Det är också möjligt att ange olika inställningar för kvarhållning för enskilda data typer från 4 till 730 dagar (förutom för arbets ytor på den äldre kostnads fria pris nivån) som åsidosätter standard kvarhållning på arbets ytan. Varje datatyp är en under resurs till arbets ytan. Till exempel kan SecurityEvent-tabellen åtgärdas i [Azure Resource Manager](../../azure-resource-manager/management/overview.md) som:

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Observera att data typen (tabell) är Skift läges känslig.  Använd följande för att hämta de aktuella inställningarna för kvarhållning per data typ för en viss datatyp (i det här exemplet SecurityEvent):

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

> [!NOTE]
> Kvarhållning returneras bara för en datatyp om kvarhållning uttryckligen har angetts.  Data typer som inte hade kvarhållning uttryckligen har angetts (och därmed ärver kvarhållning av arbets yta) kommer inte att returnera något från det här anropet. 

Om du vill hämta de aktuella inställningarna för kvarhållning per datatyp för alla data typer på din arbets yta som har den angivna lagrings typen per data typ, behöver du bara utelämna den specifika data typen, till exempel:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Om du vill ange kvarhållning av en viss datatyp (i det här exemplet SecurityEvent) till 730 dagar gör du

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

Giltiga värden för `retentionInDays` är från 30 till 730.

Det `Usage` `AzureActivity` går inte att ange data typerna och med anpassad kvarhållning. De kommer att ta på max värdet för kvarhållning av standard arbets yta eller 90 dagar. 

Ett bra verktyg för att ansluta direkt till Azure Resource Manager för att ställa in kvarhållning enligt datatyp är OSS-verktyget [ARMclient](https://github.com/projectkudu/ARMClient).  Lär dig mer om ARMclient från artiklar av [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) och [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Här är ett exempel på hur du använder ARMClient, ställer in SecurityEvent-data till en 730-dagars kvarhållning:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> Inställning av kvarhållning för enskilda data typer kan användas för att minska kostnaderna för datakvarhållning.  För data som samlas in från och med oktober 2019 (när den här funktionen släpptes) kan en minskning av kvarhållning av vissa data typer minska din bevarande kostnad över tid.  För data som samlas in tidigare, påverkar inte en lägre kvarhållning för en enskild typ dina bevarande kostnader.  

## <a name="manage-your-maximum-daily-data-volume"></a>Hantera din maximala dagliga data volym

Du kan konfigurera en daglig begränsning och begränsa den dagliga inmatningen för din arbets yta, men Använd bryr sig om ditt mål inte ska överskrida den dagliga gränsen.  Annars förlorar du data under resten av dagen, vilket kan påverka andra Azure-tjänster och-lösningar vars funktioner kan vara beroende av uppdaterade data som är tillgängliga i arbets ytan.  Det gör att möjligheten att se och få aviseringar påverkas när hälsotillstånden för de resurser som stöder IT-tjänster påverkas.  Den dagliga gränsen är avsedd att användas som ett sätt att hantera en **oväntad ökning** av data volymen från dina hanterade resurser och hålla dig inom gränsen, eller när du vill begränsa oplanerade kostnader för din arbets yta. Det är inte lämpligt att ställa in ett dagligt tak så att det nås varje dag på en arbetsyta.

Varje arbets yta har en daglig begränsning på en annan timme på dagen. Återställnings tiden visas på sidan för **dagligt tak** (se nedan). Det går inte att konfigurera den här återställnings timmen. 

Snart när den dagliga gränsen har uppnåtts stoppas insamlingen av fakturerbara data typer för resten av dagen. Svars tiden som används för att tillämpa den dagliga begränsningen innebär att höljet inte appliceras på exakt den angivna nivån för dagligt tak. En varnings banderoll visas överst på sidan för den valda Log Analytics-arbetsytan och en åtgärds händelse skickas till *Åtgärds* tabellen under kategorin **LogManagement** . Data insamlingen återupptas efter det att återställnings tiden som definierats under den *dagliga gränsen ställs in på*. Vi rekommenderar att du definierar en varnings regel baserat på den här åtgärds händelsen, konfigurerat för att meddela när den dagliga data gränsen har nåtts (se [nedan](#alert-when-daily-cap-reached)). 

> [!NOTE]
> Den dagliga gränsen kan inte stoppa data insamlingen så exakt som den angivna gräns nivån och vissa överflödiga data förväntas, särskilt om arbets ytan tar emot stora mängder data. Se [nedan](#view-the-effect-of-the-daily-cap) för en fråga som är till hjälp när du ska studera det dagliga höljet. 

> [!WARNING]
> Den dagliga gränsen stoppar inte insamling av data typer WindowsEvent, SecurityAlert, SecurityBaseline, SecurityBaselineSummary, SecurityDetection, SecurityEvent, WindowsFirewall, MaliciousIPCommunication, LinuxAuditLog, SysmonEvent, ProtectionStatus, Update och UpdateSummary, förutom för arbets ytor där Azure Security Center installerades före den 19 juni 2017. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identifiera vilka dagliga data gränser som ska definieras

Granska [Log Analytics användning och beräknade kostnader](../usage-estimated-costs.md) för att förstå data inmatnings trenden och det dagliga volym taket för att definiera. Det bör anses vara viktigt eftersom du vann? t kan övervaka dina resurser när gränsen har uppnåtts. 

### <a name="set-the-daily-cap"></a>Ange dagligt tak

Följande steg beskriver hur du konfigurerar en gräns för att hantera den data volym som Log Analytics arbets ytan kommer att ta in per dag.  

1. Välj **Användning och uppskattade kostnader** i det vänstra fönstret på arbetsytan.
2. På sidan **användning och uppskattade kostnader** för den valda arbets ytan klickar du på **data Kap** överst på sidan. 
3. Är dagligt tak **inaktiverat** som standard? Aktivera det genom att klicka på **på** . Ange sedan data volym gränsen i GB/dag.

:::image type="content" source="media/manage-cost-storage/set-daily-volume-cap-01.png" alt-text="Log Analytics konfigurera data gräns":::
    
Den dagliga begränsningen kan konfigureras via ARM genom att ange `dailyQuotaGb` parametern under `WorkspaceCapping` som beskrivs på [arbets ytor – skapa eller uppdatera](/rest/api/loganalytics/workspaces/createorupdate#workspacecapping). 

### <a name="view-the-effect-of-the-daily-cap"></a>Visa resultatet av den dagliga begränsningen

Om du vill visa resultatet av den dagliga gränsen är det viktigt att kontona för säkerhets data typerna inte ingår i den dagliga gränsen och återställnings tiden för din arbets yta. Den dagliga återställnings timmen visas på sidan för **dagliga tak** .  Följande fråga kan användas för att spåra data volymer som omfattas av den dagliga begränsningen mellan dagliga återställningar. I det här exemplet är arbets ytans återställnings timme 14:00.  Du måste uppdatera den för din arbets yta.

```kusto
let DailyCapResetHour=14;
Usage
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend TimeGenerated=datetime_add("hour",-1*DailyCapResetHour,TimeGenerated)
| where TimeGenerated > startofday(ago(31d))
| where IsBillable
| summarize IngestedGbBetweenDailyCapResets=sum(Quantity)/1000. by day=bin(TimeGenerated, 1d) | render areachart  
```

(I användnings data typen `Quantity` är enheterna i MB.)

### <a name="alert-when-daily-cap-reached"></a>Avisering när dagligt hölje uppnås

Medan vi presenterar en visuell stack-ikon i Azure Portal när data gräns tröskeln uppfylls, justeras inte det här beteendet nödvändigt vis till hur du hanterar operativa problem som kräver omedelbar uppmärksamhet.  Om du vill få ett aviserings meddelande kan du skapa en ny aviserings regel i Azure Monitor.  Mer information finns i [skapa, Visa och hantera aviseringar](../alerts/alerts-metric.md).

För att komma igång är det här de rekommenderade inställningarna för aviseringen som frågar `Operation` tabellen med hjälp av `_LogOperation` funktionen. 

- Mål: Välj din Log Analytics-resurs
- Villkoren 
   - Signal namn: anpassad loggs ökning
   - Sök fråga: `_LogOperation | where Operation == "Data Collection Status" | where Detail contains "OverQuota"`
   - Baserat på: antal resultat
   - Villkor: större än
   - Tröskel: 0
   - Period: 5 (minuter)
   - Frekvens: 5 (minuter)
- Varnings regel namn: daglig data gräns har nåtts
- Allvarlighets grad: varning (allvarlighets grad 1)

När en avisering har definierats och gränsen har uppnåtts utlöses en avisering och det svar som definieras i åtgärds gruppen utförs. Det kan meddela ditt team via e-post och textmeddelanden, eller automatisera åtgärder med Webhooks, Automation runbooks eller [integrera med en extern ITSM-lösning](../alerts/itsmc-definition.md#create-itsm-work-items-from-azure-alerts). 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Felsökning varför användningen är större än förväntat

Högre användning orsakas av en eller båda:
- Fler noder än förväntat skicka data till Log Analytics-arbetsyta
- Fler data än vad som förväntas skickas till Log Analytics arbets yta (kanske på grund av att en ny lösning har startats eller en konfigurations ändring i en befintlig lösning)

## <a name="understanding-nodes-sending-data"></a>Förstå noder som skickar data

Om du vill förstå antalet noder som rapporterar pulsslag från agenten varje dag under den senaste månaden använder du

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
Antalet noder som skickar data under de senaste 24 timmarna använder frågan: 

```kusto
find where TimeGenerated > ago(24h) project Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

Om du vill hämta en lista över noder som skickar data (och mängden data som skickas av var och en) kan du använda följande fråga:

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

### <a name="nodes-billed-by-the-legacy-per-node-pricing-tier"></a>Noder debiteras av pris nivån Legacy per nod

Den [bakåtkompatibla pris nivån per nod](#legacy-pricing-tiers) faktureras för noder med timkostnad och räknar inte bara noder som bara skickar en uppsättning säkerhets data typer. Det dagliga antalet noder är nära följande fråga:

```kusto
find where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now()) project Computer, _IsBillable, Type, TimeGenerated
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| where _IsBillable == true
| summarize billableNodesPerHour=dcount(computerName) by bin(TimeGenerated, 1h)
| summarize billableNodesPerDay = sum(billableNodesPerHour)/24., billableNodeMonthsPerDay = sum(billableNodesPerHour)/24./31.  by day=bin(TimeGenerated, 1d)
| sort by day asc
```

Antalet enheter på fakturan är i antal noder * månader som representeras av `billableNodeMonthsPerDay` i frågan. Om den Uppdateringshantering lösningen är installerad på arbets ytan lägger du till data typerna Update och UpdateSummary i listan i WHERE-satsen i ovanstående fråga. Slutligen finns det ytterligare komplexitet i den faktiska fakturerings algoritmen när lösnings mål används som inte representeras i ovanstående fråga. 


> [!TIP]
> Använd dessa `find` frågor sparsamt eftersom genomsökningar över data typer är [resurs krävande](./query-optimization.md#query-performance-pane) att köra. Om du inte behöver några resultat **per dator** frågar du efter användnings data typen (se nedan).

## <a name="understanding-ingested-data-volume"></a>Förstå inmatad data volym

På sidan **användning och uppskattade kostnader** visar diagrammet *data inmatning per lösning* den totala mängden data som skickas och hur mycket som skickas av varje lösning. På så sätt kan du bestämma trender, till exempel om den övergripande data användningen (eller användningen av en viss lösning) växer, återstående konstant eller minskning. 

### <a name="data-volume-for-specific-events"></a>Data volym för vissa händelser

Om du vill titta på storleken på inmatade data för en viss uppsättning händelser kan du fråga den specifika tabellen (i det här exemplet `Event` ) och sedan begränsa frågan till händelser av intresse (i det här exemplet händelse-ID 5145 eller 5156):

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

Observera att-satsen `where _IsBillable = true` filtrerar bort data typer från vissa lösningar som det inte finns någon inmatnings avgift för. [Läs mer](./log-standard-columns.md#_isbillable) om `_IsBillable` .

### <a name="data-volume-by-solution"></a>Datavolym per lösning

Frågan som används för att visa den fakturerbara data volymen per lösning under den senaste månaden (exklusive den sista del dagen) är:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution 
| render columnchart
```

Satsen med `TimeGenerated` är endast för att säkerställa att frågans upplevelse i Azure Portal kommer att se tillbaka utöver standard 24 timmarna. När du använder data typen användning `StartTime` och representerar de tidsgrupper `EndTime` som resultat visas för. 

### <a name="data-volume-by-type"></a>Data volym efter typ

Du kan öka detalj nivån för att se data trender för data typen:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType 
| render columnchart
```

Eller för att se en tabell efter lösning och typ för den senaste månaden

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000 by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Data volym per dator

`Usage`Data typen innehåller inte information på dator nivå. Om du vill se **storleken** på inmatade data per dator, använder du `_BilledSize` [egenskapen](./log-standard-columns.md#_billedsize)som anger storlek i byte:

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, Computer
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName 
| sort by BillableDataBytes nulls last
```

`_IsBillable` [Egenskapen](./log-standard-columns.md#_isbillable) anger om inmatade data kommer att debiteras. 

Om du vill se **antalet** inmatade fakturerbara händelser per dator använder du 

```kusto
find where TimeGenerated > ago(24h) project _IsBillable, Computer
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  
| sort by eventCount nulls last
```

> [!TIP]
> Använd dessa `find` frågor sparsamt eftersom genomsökningar över data typer är [resurs krävande](./query-optimization.md#query-performance-pane) att köra. Om du inte behöver några resultat **per dator** frågar du efter typen användnings data.

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Data volym per Azure-resurs, resurs grupp eller prenumeration

För data från noder som finns i Azure kan du hämta **storleken** på inmatade data __per dator__, använda [egenskapen](./log-standard-columns.md#_resourceid)_ResourceId som ger den fullständiga sökvägen till resursen:

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by BillableDataBytes nulls last
```

För data från noder som finns i Azure kan du hämta **storleken** på inmatade data __per Azure-prenumeration__, Hämta `_SubscriptionId` egenskapen som:

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, _SubscriptionId
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _SubscriptionId | sort by BillableDataBytes nulls last
```

För att hämta data volym per resurs grupp kan du parsa `_ResourceId` :

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| extend resourceGroup = tostring(split(_ResourceId, "/")[4] )
| summarize BillableDataBytes = sum(BillableDataBytes) by resourceGroup | sort by BillableDataBytes nulls last
```

Du kan också tolka det `_ResourceId` mer fullständigt om det behövs och använda

```Kusto
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
```

> [!TIP]
> Använd dessa `find` frågor sparsamt eftersom genomsökningar över data typer är [resurs krävande](./query-optimization.md#query-performance-pane) att köra. Om du inte behöver resultat per prenumeration, kan du ändra resurs grupp eller resurs namn och sedan fråga efter typen användnings data.

> [!WARNING]
> Några av fälten i användnings data typen, men fortfarande i schemat, är inaktuella och de kommer inte längre att fyllas i. Dessa är både **datorer** och fält som rör inmatning (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** och **AverageProcessingTimeMs**.


### <a name="querying-for-common-data-types"></a>Fråga efter vanliga data typer

Här är några användbara exempel frågor för att gå djupare i data källan för en viss datatyp:

+ **Arbets yta-baserade Application Insights** resurser
  - Läs mer i [Hantera användning och kostnader för Application Insights](../app/pricing.md#data-volume-for-workspace-based-application-insights-resources)
+ **Security**-lösningen
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ **Log Management**-lösningen
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ **Perf**-datatypen
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ **Event**-datatypen
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ **Syslog**-datatypen
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Datatypen **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

## <a name="tips-for-reducing-data-volume"></a>Tips för att minska datavolymen

Några förslag på hur du minskar mängden loggar som samlas in är:

| Källan för hög datavolym | Hur du minskar datavolym |
| -------------------------- | ------------------------- |
| Containerinsikter         | [Konfigurera behållar insikter](../containers/container-insights-cost.md#controlling-ingestion-to-reduce-cost) för att endast samla in de data du behöver. |
| Säkerhetshändelser            | Välj [vanliga eller minimala säkerhetshändelser](../../security-center/security-center-enable-data-collection.md#data-collection-tier) <br> Ändra principen för säkerhetsgranskning för att endast samla in händelser som behövs. Du kan särskilt se över behovet att samla in händelser för att <br> - [granska filtreringplattform](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772749(v=ws.10)) <br> - [granska register](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [granska filsystem](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [granska kernelobjekt](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [granska hantering av manipulering](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> – granska flyttbara lagrings enheter |
| Prestandaräknare       | Ändra [prestandaräknarens konfiguration](../agents/data-sources-performance-counters.md) för att: <br> - Minska insamlingsfrekvensen <br> - Minska antalet prestandaräknare |
| Händelseloggar                 | Ändra [händelseloggens konfiguration](../agents/data-sources-windows-events.md) för att: <br> - Minska antalet händelseloggar som samlas in <br> - Endast samla in obligatoriska händelsenivåer. Till exempel, samla inte in händelser på *Informationsnivå* |
| Syslog                     | Ändra [systemloggkonfigurationen](../agents/data-sources-syslog.md) för att: <br> - Minska antalet anläggningar som samlas in <br> - Endast samla in obligatoriska händelsenivåer. Till exempel, samla inte in händelser på *Informations-* eller *Felsökningsnivå* |
| AzureDiagnostics           | Ändra [resurs logg samling](../essentials/diagnostic-settings.md#create-in-azure-portal) till: <br> – Minska antalet resursloggar som skickas till Log Analytics <br> – Endast samla in nödvändiga loggar |
| Lösningsdata från datorer som inte behöver lösningen | Använd [lösnings mål](../insights/solution-targeting.md) om du endast vill samla in data från nödvändiga grupper av datorer. |
| Application Insights | Granska alternativ för [https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume](managing Application Insights data volume) |
| [SQL-analys](../insights/azure-sql.md) | Använd [set-AzSqlServerAudit](/powershell/module/az.sql/set-azsqlserveraudit) för att finjustera gransknings inställningarna. |
| Azure Sentinel | Granska alla [Sentinel-datakällor](../../sentinel/connect-data-sources.md) som du nyligen har aktiverat som källor till ytterligare data volym. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Hämtar noder som faktureras i pris nivån per nod

Om du vill hämta en lista över datorer som kommer att faktureras som noder om arbets ytan är i pris nivån bakåtkompatibelt per nod, letar du efter noder som skickar **fakturerings data typer** (vissa data typer är kostnads fria). Det gör du genom att använda `_IsBillable` [egenskapen](./log-standard-columns.md#_isbillable) längst till vänster i det fullständigt kvalificerade domän namnet. Detta returnerar antalet datorer med fakturerade data per timme (vilket är den kornig het med vilken noder räknas och faktureras):

```kusto
find where TimeGenerated > ago(24h) project Computer, TimeGenerated
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>Antalet säkerhets-och automation-noder hämtas

Om du vill se antalet distinkta säkerhets noder kan du använda frågan:

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

Om du vill se antalet distinkta Automation-noder använder du frågan:

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>Utvärdera den äldre pris nivån per nod

Beslutet om arbets ytor med till gång till den äldre pris nivån **per nod** är bättre än på den nivån eller med den aktuella nivån **betala per** användning eller **kapacitets reservation** . det är ofta svårt för kunder att utvärdera.  Detta omfattar förståelse av kompromisser mellan den fasta kostnaden per övervakad nod i pris nivån per nod och den inkluderade dataallokeringen på 500 MB/Node/Day och kostnaden för att betala för inmatade data i nivån betala per användning (per GB). 

För att under lätta den här utvärderingen kan följande fråga användas för att skapa en rekommendation för den optimala pris nivån baserat på arbets ytans användnings mönster.  Den här frågan granskar de övervakade noderna och data som matats in i en arbets yta under de senaste 7 dagarna, och för varje dag utvärderas vilken pris nivå som skulle vara optimal. Om du vill använda frågan måste du ange

1. om arbets ytan använder Azure Security Center genom att ställa in `workspaceHasSecurityCenter` på `true` eller `false` , 
2. uppdatera priserna om du har vissa rabatter och
3. Ange hur många dagar du vill se tillbaka och analysera genom att ställa in `daysToEvaluate` . Detta är användbart om frågan tar för lång tid att försöka titta på 7 dagars data. 

Här är frågan om pris nivå rekommendation:

```kusto
// Set these parameters before running query
// Pricing details available at https://azure.microsoft.com/en-us/pricing/details/monitor/
let daysToEvaluate = 7; // Enter number of previous days to analyze (reduce if the query is taking too long)
let workspaceHasSecurityCenter = false;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your montly price per monitored nodes
let PerNodeOveragePrice = 2.30; // Enter your price per GB for data overage in the Per Node pricing tier
let PerGBPrice = 2.30; // Enter your price per GB in the Pay-as-you-go pricing tier
let CarRes100Price = 196.; // Enter your price for the 100 GB/day Capacity Reservation
let CarRes200Price = 368.; // Enter your price for the 200 GB/day Capacity Reservation
let CarRes300Price = 540.; // Enter your price for the 300 GB/day Capacity Reservation
let CarRes400Price = 704.; // Enter your price for the 400 GB/day Capacity Reservation
let CarRes500Price = 865.; // Enter your price for the 500 GB/day Capacity Reservation
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
let StartDate = startofday(datetime_add("Day",-1*daysToEvaluate,now()));
let EndDate = startofday(now());
union * 
| where TimeGenerated >= StartDate and TimeGenerated < EndDate
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join kind=leftouter (
    Heartbeat 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerNodeOveragePrice
| extend billableGB = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)), DataGB )
| extend PerGBDailyCost = billableGB * PerGBPrice
| extend CapRes100DailyCost = CarRes100Price + max_of(billableGB - 100, 0.)* PerGBPrice
| extend CapRes200DailyCost = CarRes200Price + max_of(billableGB - 200, 0.)* PerGBPrice
| extend CapRes300DailyCost = CarRes300Price + max_of(billableGB - 300, 0.)* PerGBPrice
| extend CapRes400DailyCost = CarRes400Price + max_of(billableGB - 400, 0.)* PerGBPrice
| extend CapResLevel500AndAbove = max_of(floor(billableGB, 100),500)
| extend CapRes500AndAboveDailyCost = CarRes500Price*CapResLevel500AndAbove/500 + max_of(billableGB - CapResLevel500AndAbove, 0.)* PerGBPrice
| extend MinCost = min_of(
    PerNodeDailyCost,PerGBDailyCost,CapRes100DailyCost,CapRes200DailyCost,
    CapRes300DailyCost, CapRes400DailyCost, CapRes500AndAboveDailyCost)
| extend Recommendation = case(
    MinCost == PerNodeDailyCost, "Per node tier",
    MinCost == PerGBDailyCost, "Pay-as-you-go tier",
    MinCost == CapRes100DailyCost, "Capacity Reservation (100 GB/day)",
    MinCost == CapRes200DailyCost, "Capacity Reservation (200 GB/day)",
    MinCost == CapRes300DailyCost, "Capacity Reservation (300 GB/day)",
    MinCost == CapRes400DailyCost, "Capacity Reservation (400 GB/day)",
    MinCost == CapRes500AndAboveDailyCost, strcat("Capacity Reservation (",CapResLevel500AndAbove," GB/day)"),
    "Error"
)
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, 
    CapRes100DailyCost, CapRes200DailyCost, CapRes300DailyCost, CapRes400DailyCost, CapRes500AndAboveDailyCost, Recommendation 
| sort by day asc
//| project day, Recommendation // Comment this line to see details
| sort by day asc
```

Den här frågan är inte en exakt replikering av hur användningen beräknas, men kommer att fungera för att tillhandahålla rekommendationer för pris nivåer i de flesta fall.  

> [!NOTE]
> Om du vill använda rättigheterna som kommer från inköp av OMS E1 Suite, OMS E2 Suite eller OMS Add-On för System Center väljer du pris nivån Log Analytics *per nod* .

## <a name="create-an-alert-when-data-collection-is-high"></a>Skapa en avisering när data insamlingen är hög

I det här avsnittet beskrivs hur du skapar en avisering som data volymen under de senaste 24 timmarna överskred en angiven mängd med hjälp av Azure Monitor [logg aviseringar](../alerts/alerts-unified-log.md). 

Följ dessa steg om du vill varna om den fakturerbara data volymen som matats in under de senaste 24 timmarna var större än 50 GB: 

- **Definiera aviseringsvillkor** ange Log Analytics-arbetsytan som mål för resursen.
- **Aviseringskriterier** ange följande:
   - **Signalnamn** välj **Anpassad loggsökning**
   - **Sök fråga** till `Usage | where IsBillable | summarize DataGB = sum(Quantity / 1000.) | where DataGB > 50` . Om du vill ha en annan 
   - **Aviseringslogik** är **Baserad på** *antal resultat* och **Villkor** som är *Större än* ett **Tröskelvärde** på *0*
   - **Tids period** på *1440* minuter och **aviserings frekvens** till var *1440* minut att köras en gång om dagen.
- **Definiera aviseringsinformation** ange följande:
   - **Namn** till *fakturerbar data volym större än 50 GB på 24 timmar*
   - **Allvarlighetsgrad** till *varning*

Ange en befintlig eller skapa en ny [Åtgärdsgrupp](../alerts/action-groups.md) så att när loggaviseringen matchar kriterierna, får du ett meddelande.

När du får en avisering använder du stegen i ovanstående avsnitt om hur du felsöker varför användningen är högre än förväntat.

## <a name="data-transfer-charges-using-log-analytics"></a>Avgifter för data överföring med Log Analytics

Att skicka data till Log Analytics kan medföra kostnader för data bandbredd, men de är begränsade till Virtual Machines där en Log Analytics agent är installerad och inte gäller när du använder diagnostikinställningar eller med andra anslutningar som är inbyggda i Azure Sentinel. Som det beskrivs i [prissättnings sidan för Azure bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/)är data överföringen mellan Azure-tjänster som finns i två regioner debiterad som utgående data överföring enligt normal taxa. Inkommande data överföring är kostnads fri. Den här avgiften är dock väldigt liten (några%) jämfört med kostnaderna för Log Analytics data inmatning. Därför bör du kontrol lera kostnaderna för Log Analytics behöver fokusera på din inmatade [data volym](#understanding-ingested-data-volume). 


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Felsöka varför Log Analytics inte längre samla in data

Om du befinner dig på den äldre kostnads fria pris nivån och har skickat mer än 500 MB data under en dag stannar data insamlingen under resten av dagen. Att nå den dagliga gränsen är en vanlig orsak till att Log Analytics slutar att samla in data eller att data verkar saknas.  Log Analytics skapar en händelse av typen åtgärd när data insamlingen startar och stoppar. Kör följande fråga i sökningen för att kontrol lera om du når den dagliga gränsen och data som saknas: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

När datainsamlingen stoppas är OperationStatus **Varning**. När datainsamlingen startar är OperationStatus **Lyckades**. I följande tabell beskrivs varför data insamling stoppas och en föreslagen åtgärd för att återuppta data insamling:  

|Orsaks insamling stoppas| Lösning| 
|-----------------------|---------|
|Arbets ytans dagliga tak har uppnåtts|Vänta tills insamlingen startar om automatiskt eller öka den dagliga data volym gränsen som beskrivs i hantera den maximala dagliga data volymen. Den dagliga återställnings tiden visas på sidan för **dagligt tak** . |
| Din arbets yta har nått [volym frekvensen för data inmatningar](../service-limits.md#log-analytics-workspaces) | Standardgränsen för datainmatningsvolym som skickas från Azure-resurser med hjälp av diagnostikinställningar är cirka 6 GB/min per arbetsyta. Detta är ett ungefärligt värde eftersom den faktiska storleken kan variera mellan olika datatyper beroende på logglängden och dess komprimeringsförhållande. Den här begränsningen gäller inte för data som skickas från agenter eller API för datainsamling. Om du skickar data med en högre hastighet till en enskild arbetsyta släpps vissa data, och en händelse skickas till åtgärdstabellen i arbetsytan var 6:e timme medan tröskelvärdet fortsätter att överskridas. Om din inmatningsvolym fortsätter att överskrida hastighetsgränsen eller om du förväntar dig att snart nå den, kan du begära en ökning för arbetsytan genom att skicka ett e-postmeddelande till LAIngestionRate@microsoft.com eller öppna en supportbegäran. Händelsen att söka efter som anger att en gräns för datainmatningsfrekvensen kan hittas av frågan `Operation | where OperationCategory == "Ingestion" | where Detail startswith "The rate of data crossed the threshold"`. |
|Den dagliga gränsen för den äldre kostnads fria pris nivån har uppnåtts |Vänta till följande dag för insamling av automatisk omstart eller ändra till en betald pris nivå.|
|Azure-prenumerationen är i ett inaktiverat tillstånd på grund av:<br> Den kostnads fria utvärderingen avslutades<br> Azure-pass har gått ut<br> Månads utgifts gräns har nåtts (till exempel för en MSDN-eller Visual Studio-prenumeration)|Konvertera till en betald prenumeration<br> Ta bort gräns eller vänta tills begränsningen återställs|

Om du vill få ett meddelande när data insamlingen stoppas, använder du stegen som beskrivs i *skapa daglig data Kap* -avisering för att bli informerad när data insamlingen stoppa Använd stegen som beskrivs i [skapa en åtgärds grupp](../alerts/action-groups.md) för att konfigurera en e-post, webhook eller Runbook-åtgärd för varnings regeln. 

## <a name="limits-summary"></a>Sammanfattning av gränser

Det finns ytterligare Log Analytics gränser, varav vissa är beroende av Log Analytics pris nivå. Dessa dokumenteras i [Azure-prenumerationen och tjänst begränsningar, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces).


## <a name="next-steps"></a>Nästa steg

- Se [loggs ökningar i Azure Monitor loggar](../logs/log-query-overview.md) för att lära dig hur du använder Sök språket. Du kan använda sökfrågor för att utföra ytterligare analys på användningsdata.
- Använd stegen som beskrivs i [Skapa en ny loggavisering](../alerts/alerts-metric.md) om du vill meddelas när ett sökvillkor har uppfyllts.
- Använd [lösnings mål](../insights/solution-targeting.md) om du endast vill samla in data från nödvändiga grupper av datorer.
- Om du vill konfigurera en princip för en effektiv händelse insamling granskar [Azure Security Center filtrerings princip](../../security-center/security-center-enable-data-collection.md).
- Ändra [prestandaräknarens konfiguration](../agents/data-sources-performance-counters.md).
- Om du vill ändra inställningarna för händelse samlingen granskar du [händelse logg konfigurationen](../agents/data-sources-windows-events.md).
- Om du vill ändra inställningarna för syslog-samlingen granskar du [syslog-konfigurationen](../agents/data-sources-syslog.md).
- Om du vill ändra inställningarna för syslog-samlingen granskar du [syslog-konfigurationen](../agents/data-sources-syslog.md).
