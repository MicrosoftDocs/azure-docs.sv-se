---
title: Övervakning av arbets belastnings hanterings portalen
description: Vägledning för övervakning av hanterings portalen för arbets belastning i Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/01/2021
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 702cc1fc8b135b2eb4af9106a81946873918c4fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694401"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring"></a>Azure Synapse Analytics – övervakning av arbets belastnings Hanteringsportal

Den här artikeln förklarar hur du övervakar resursutnyttjande för [arbets belastnings grupper](sql-data-warehouse-workload-isolation.md#workload-groups) och frågor.
Mer information om hur du konfigurerar Azure-Metrics Explorer finns i artikeln [komma igång med Azure Metrics Explorer](../../azure-monitor/essentials/metrics-getting-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .  Mer information om hur du övervakar användningen av system resurser finns i avsnittet [resursutnyttjande](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) i dokumentationen för Azure Synapse Analytics-övervakning.
Det finns två olika kategorier av mått för arbets belastnings grupper som tillhandahålls för övervakning av arbets belastnings hantering: resurstilldelning och fråga aktivitet.  Dessa mått kan delas och filtreras efter arbets belastnings grupp.  Måtten kan delas och filtreras baserat på om de är systemdefinierade (arbets belastnings grupper för resurs klass) eller användardefinierade (skapas av användare med [skapa arbets belastnings gruppens](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) syntax).

## <a name="workload-management-metric-definitions"></a>Mått definitioner för arbets belastnings hantering

|Måttnamn                    |Beskrivning  |Sammansättningstyp |
|-------------------------------|-------------|-----------------|
|Effektiv tak resurs procent | Effektiv slut för ande *resurs procent* är en hård gräns för den procent andel av resurser som kan nås av arbets belastnings gruppen, med hänsyn till den *lägsta resurs procent* som allokerats för andra arbets belastnings grupper. Det effektiva måttet för *Cap-resursen procent* konfigureras med hjälp av `CAP_PERCENTAGE_RESOURCE` parametern i SYNTAXEN [create arbets belastnings grupp](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Det effektiva värdet beskrivs här.<br><br>Exempel: om en arbets belastnings grupp `DataLoads` har skapats med `CAP_PERCENTAGE_RESOURCE` = 100 och en annan arbets belastnings grupp skapas med en effektiv minsta resurs procent på 25%, är den *effektiva Kap resurs procenten* för `DataLoads` arbets belastnings gruppen 75%.<br><br>Den *effektiva Kap resurs procenten* avgör den övre gränsen för samtidighet (och därmed möjligt data flöde) som en arbets belastnings grupp kan uppnå.  Om ytterligare data flöde behövs utöver vad som för närvarande rapporteras av det effektiva måttet för *kap. Resource procent* , ökar `CAP_PERCENTAGE_RESOURCE` , minskar du `MIN_PERCENTAGE_RESOURCE` antalet andra arbets belastnings grupper eller skalar upp instansen för att lägga till fler resurser.  Minskning av `REQUEST_MIN_RESOURCE_GRANT_PERCENT` kan öka samtidigheten, men kanske inte ökar det totala data flödet.| Min, medel, max |
|Effektiv minsta resurs procent |*Effektiv minsta resurs procent* är den lägsta procent andelen av resurser som är reserverade och isolerade för arbets belastnings gruppen, med hänsyn till den lägsta tjänste nivån.  Det effektiva måttet för lägsta resurs procent konfigureras med hjälp av `MIN_PERCENTAGE_RESOURCE` parametern i syntaxen [create arbets belastnings grupp](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Det effektiva värdet beskrivs [här](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json?view=azure-sqldw-latest&preserve-view=true#effective-values).<br><br>Använd agg regerings typen sum när måttet är ofiltrerat och unsplit för att övervaka den totala arbets belastnings isolering som kon figurer ATS i systemet.<br><br>Den *effektiva lägsta resurs procenten* avgör den lägre gränserna för den garanterade samtidigheten (och därmed garanterad genom strömning) att en arbets belastnings grupp kan uppnå.  Om ytterligare garanterade resurser behövs utöver vad som för närvarande rapporteras av måttet *effektiv minsta resurs procent* , ökar du den `MIN_PERCENTAGE_RESOURCE` parameter som kon figurer ATS för arbets belastnings gruppen.  Minskning av `REQUEST_MIN_RESOURCE_GRANT_PERCENT` kan öka samtidigheten, men kanske inte ökar det totala data flödet. |Min, medel, max|
|Aktiva frågor för arbets belastnings grupp  |Det här måttet rapporterar aktiva frågor i arbets belastnings gruppen.  Om du använder det här måttet ofiltrerad och unsplit visas alla aktiva frågor som körs i systemet.|Sum         |
|Allokering av arbets belastnings grupp per Cap-resurs procent |Det här måttet visar procent tilldelningen av resurser i förhållande till den *effektiva Kap resurs procenten* per arbets belastnings grupp.  Detta mått ger arbets belastnings gruppens effektiva användning.<br><br>Överväg en arbets belastnings grupp `DataLoads` med en *effektiv Kap resurs procent* på 75% och en `REQUEST_MIN_RESOURCE_GRANT_PERCENT` konfigurerad på 25%.  *Tilldelningen av arbets belastnings gruppen efter procent* värde för Cap-resurs som är filtrerad till `DataLoads` skulle vara 33% (25%/75%) om en enskild fråga kördes i den här arbets belastnings gruppen.<br><br>Använd det här måttet för att identifiera användningen av en arbets belastnings grupp.  Ett värde nära 100% anger att alla resurser som är tillgängliga för arbets belastnings gruppen används.  Dessutom skulle arbets belastnings *gruppen i kö med mått* för samma arbets belastnings grupp som visar ett värde som är större än noll innebära att arbets belastnings gruppen använder ytterligare resurser om de allokeras.  Om det här måttet är konsekvent lågt och de *aktiva frågorna för arbets belastnings gruppen* är små används inte arbets belastnings gruppen.  Den här situationen är särskilt problematisk om effektiv slut för ande *resurs procent* är större än noll eftersom det indikerar [underutnyttjad arbets belastnings isolering](#underutilized-workload-isolation).|Min, medel, max |
|Allokering av arbets belastnings grupp efter system procent | Det här måttet visar procent tilldelningen av resurser i förhållande till hela systemet.<br><br>Överväg en arbets belastnings grupp `DataLoads` med en `REQUEST_MIN_RESOURCE_GRANT_PERCENT` konfigurerad på 25%.  *Allokering av arbets belastnings grupper efter system procent* värde som filtrerats till `DataLoads` skulle vara 25% (25%/100%) om en enskild fråga kördes i den här arbets belastnings gruppen.|Min, medel, max |
|Fråge tids gränser för arbets belastnings grupp |Frågor för arbets belastnings gruppen som har nått tids gränsen.  Tids gränser för frågor som rapporteras av detta mått sker bara när frågan har börjat köras (den innehåller inte vänte tid på grund av låsning eller väntande resurser).<br><br>Tids gränsen för frågan konfigureras med hjälp av `QUERY_EXECUTION_TIMEOUT_SEC` parametern i syntaxen [create arbets belastnings grupp](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Att öka värdet kan minska antalet tids gränser för frågor.<br><br>Överväg att öka `REQUEST_MIN_RESOURCE_GRANT_PERCENT` parametern för arbets belastnings gruppen för att minska antalet tids gränser och allokera fler resurser per fråga.  Obs! ökning `REQUEST_MIN_RESOURCE_GRANT_PERCENT` minskar mängden samtidighet för arbets belastnings gruppen. |Sum |
|Köade frågor i arbets belastnings gruppen | Frågor för den arbets belastnings grupp som för närvarande köas väntar på att starta körning.  Frågor kan vara kö eftersom de väntar på resurser eller lås.<br><br>Frågor kan vänta på grund av flera olika orsaker.  Om systemet är överbelastat och den samtidiga efter frågan är större än vad som är tillgängligt, kommer frågorna att placeras i kö.<br><br>Överväg att lägga till fler resurser i arbets belastnings gruppen genom att öka `CAP_PERCENTAGE_RESOURCE` parametern i instruktionen [skapa arbets belastnings grupp](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Om `CAP_PERCENTAGE_RESOURCE` är större än det effektiva måttet för slut *punkts resurs procent* påverkar den konfigurerade arbets belastnings isoleringen för andra arbets belastnings grupper resurserna som allokeras till den här arbets belastnings gruppen.  Överväg att sänka `MIN_PERCENTAGE_RESOURCE` andra arbets belastnings grupper eller skala upp instansen för att lägga till fler resurser. |Sum |

## <a name="monitoring-scenarios-and-actions"></a>Övervaknings scenarier och åtgärder

Nedan visas en serie diagram konfigurationer för att markera användnings mått för arbets belastnings hantering för fel sökning tillsammans med associerade åtgärder för att åtgärda problemet.

### <a name="underutilized-workload-isolation"></a>Underutnyttjad arbets belastnings isolering

Överväg följande arbets belastnings grupp och klassificerings konfiguration där en arbets belastnings grupp med namnet `wgPriority` skapas och *TheCEO* `membername` mappas till den med hjälp av `wcCEOPriority` arbets belastnings klassificeringen.  Arbets `wgPriority` belastnings gruppen har 25% arbets belastnings isolering konfigurerad för den ( `MIN_PERCENTAGE_RESOURCE` = 25).  Varje fråga som skickats av *TheCEO* ges 5% av system resurserna ( `REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5).

```sql
CREATE WORKLOAD GROUP wgPriority
WITH ( MIN_PERCENTAGE_RESOURCE = 25
      ,CAP_PERCENTAGE_RESOURCE = 50
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5);

CREATE WORKLOAD CLASSIFIER wcCEOPriority
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```

Diagrammet nedan är konfigurerat enligt följande:<br>
Mått 1: *effektiv minsta resurs procent* (genomsnittlig agg regering `blue line` )<br>
Metric 2: *allokering av arbets belastnings grupp efter system procent* (genomsnittlig agg regering `purple line` )<br>
Filter: [arbets belastnings grupp] = `wgPriority`<br>
![Skärm bild som visar ett diagram med de två måtten och filtret.](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png)
Diagrammet visar att med 25% arbets belastnings isolering används endast 10% i genomsnitt.  I det här fallet `MIN_PERCENTAGE_RESOURCE` kan parametervärdet sänkas till mellan 10 eller 15 och andra arbets belastningar på systemet kan förbruka resurserna.

### <a name="workload-group-bottleneck"></a>Flask hals för arbets belastnings grupp

Överväg följande arbets belastnings grupp och klassificerings konfiguration där en arbets belastnings grupp med namnet `wgDataAnalyst` skapas och att *DataAnalyst* `membername` är mappad till den med hjälp av `wcDataAnalyst` arbets belastnings klassificeringen.  Arbets `wgDataAnalyst` belastnings gruppen har 6% arbets belastnings isolering konfigurerad för den ( `MIN_PERCENTAGE_RESOURCE` = 6) och en resurs gräns på 9% ( `CAP_PERCENTAGE_RESOURCE` = 9).  Varje fråga som skickats av *DataAnalyst* ges 3% av system resurserna ( `REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3).

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6
      ,CAP_PERCENTAGE_RESOURCE = 9
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3);

CREATE WORKLOAD CLASSIFIER wcDataAnalyst
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```

Diagrammet nedan är konfigurerat enligt följande:<br>
Mått 1: *effektiv tak resurs procent* (genomsnittlig agg regering `blue line` )<br>
Metric 2: *allokering av arbets belastnings grupp per Kap resurs procent* (genomsnittlig agg regering `purple line` )<br>
Mått 3: *arbets belastnings grupp köade frågor* (summa agg regering, `turquoise line` )<br>
Filter: [arbets belastnings grupp] = `wgDataAnalyst`<br>
![Skärm bild som visar ett diagram med de tre måtten och filtret.](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png)
Diagrammet visar att arbets belastnings gruppen är 90% + som används (från arbets *belastnings grupp tilldelningen med ett mått på Cap-resursens procent*).  Det finns en stabil kö med frågor som visas i *arbets belastnings gruppens mått i kö*.  I det här fallet ökar `CAP_PERCENTAGE_RESOURCE` till ett värde som är högre än 9% att fler frågor körs samtidigt.  Det finns `CAP_PERCENTAGE_RESOURCE` tillräckligt med resurser som är tillgängliga och inte isolerade av andra arbets belastnings grupper.  Kontrol lera att taket har ökat genom att kontrol lera det *effektiva måttet för Kap. resurs procent*.  Om du vill ha mer data flöde bör du också överväga `REQUEST_MIN_RESOURCE_GRANT_PERCENT` att öka till ett värde som är större än 3.  Att öka `REQUEST_MIN_RESOURCE_GRANT_PERCENT` kan göra att frågor kan köras snabbare.

## <a name="next-steps"></a>Nästa steg

- [Snabb start: Konfigurera arbets belastnings isolering med T-SQL](quickstart-configure-workload-isolation-tsql.md)<br>
- [SKAPA arbets BELASTNINGs grupp (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)<br>
- [Skapa klassificering av arbets belastning (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)<br>
- [Övervaka resursutnyttjande](sql-data-warehouse-concept-resource-utilization-query-activity.md)
