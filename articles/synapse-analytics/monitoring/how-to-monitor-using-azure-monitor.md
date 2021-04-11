---
title: Så här övervakar du Synapse Analytics med hjälp av Azure Monitor
description: Lär dig hur du övervakar din Synapse Analytics-arbetsyta med hjälp av Azure Monitor mått, varningar och loggar
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: ba730adaa1e3b111586012ba1849c6dc84d6db00
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553999"
---
# <a name="use-azure-monitor-with-your-azure-synapse-analytics-workspace"></a>Använda Azure Monitor med din Azure Synapse Analytics-arbetsyta

Moln program är komplexa och har många rörliga delar. Övervakare tillhandahåller data för att se till att dina program håller sig igång i felfritt tillstånd. Övervakare hjälper dig också att undvika potentiella problem och felsöka tidigare. Du kan använda övervaknings data för att få djupgående insikter om dina program. Den här informationen hjälper dig att förbättra programmets prestanda och hanterbarhet. Det hjälper dig också att automatisera åtgärder som annars kräver manuell åtgärd.

Azure Monitor tillhandahåller infrastruktur mått, aviseringar och loggar på bas nivå för de flesta Azure-tjänster. Azure Diagnostic-loggar genereras av en resurs och ger omfattande, frekventa data om driften av resursen. Azure Synapse Analytics kan skriva diagnostikloggar i Azure Monitor.

Mer information finns i [Översikt av Azure Monitor](../../azure-monitor/overview.md).

## <a name="metrics"></a>Mått

Med övervakaren kan du få insyn i prestanda och hälsa för dina Azure-arbetsbelastningar. Den viktigaste typen av övervaknings data är måttet, som även kallas för prestanda räknaren. Måtten genereras av de flesta Azure-resurser. Övervakaren tillhandahåller flera olika sätt att konfigurera och använda dessa mått för övervakning och fel sökning.

Följ anvisningarna i [Azure Monitor data plattform](../../azure-monitor/data-platform.md)för att få åtkomst till dessa mått.

### <a name="workspace-level-metrics"></a>Mått på arbets ytans nivå

Här följer några av de mått som har spridits av arbets ytor:

| **Mått**                           | **Mått kategori, visnings namn**                  | **Enhet** | **Agg regerings typer** | **Beskrivning**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| IntegrationActivityRunsEnded         | Integrering, aktivitet kör mått                     | Antal    | Sum (standard), antal                | Det totala antalet aktivitets körningar som har inträffat/avslut ATS inom ett fönster på 1 minut. </br></br> Använd resultat dimensionen för det här måttet för att filtrera efter lyckad, misslyckad eller avbrutet slut tillstånd.|
| IntegrationPipelineRunsEnded         | Integrering, kör pipelinen att köra mått                     | Antal    | Sum (standard), antal                | Det totala antalet pipelines körningar som inträffat/slutade inom ett fönster på 1 minut. </br></br> Använd resultat dimensionen för det här måttet för att filtrera efter lyckad, misslyckad eller avbrutet slut tillstånd. |
| IntegrationTriggerRunsEnded          | Integrering, utlösare kör mått                      | Antal    | Sum (standard), antal                | Det totala antalet Utlös ande körningar som inträffat/slutade inom ett fönster på 1 minut. </br></br> Använd resultat dimensionen för det här måttet för att filtrera efter lyckad, misslyckad eller avbrutet slut tillstånd. |
| BuiltinSqlPoolDataProcessedBytes     | Inbyggd SQL-pool, bearbetade data (byte) | Byte | Sum (standard) | Mängden data som bearbetas av den inbyggda SQL-poolen utan server. |
| BuiltinSqlPoolLoginAttempts          | Inbyggd SQL-pool, inloggnings försök | Antal | Sum (standard) | Antal inloggnings försök för den inbyggda SQL-poolen utan server. |
| BuiltinSqlPoolDataRequestsEnded      | Inbyggd SQL-pool, slutförda begär Anden (byte) | Antal | Sum (standard) | Antal slutförda SQL-begäranden för den inbyggda SQL-poolen utan server. </br></br> Använd resultat dimensionen för det här måttet för att filtrera efter slutligt tillstånd. |

### <a name="dedicated-sql-pool-metrics"></a>Dedikerade mått för SQL-pool

Här följer några av de mått som har spridits av dedikerade SQL-pooler:

| **Mått**                           | **Visningsnamn**                  | **Enhet** | **Agg regerings typer** | **Beskrivning**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| DWULimit                            | DWU-gräns                       | Antal   | Max (standard), min, AVG | Konfigurerad storlek på SQL-poolen |
| DWUUsed                             | DWU som används                        | Antal   | Max (standard), min, AVG | Representerar en övergripande representation av användningen i SQL-poolen. Uppmätt av DWU-gränsen * DWU procent |
| DWUUsedPercent                      | Procent Använd DWU             | Procent | Max (standard), min, AVG | Representerar en övergripande representation av användningen i SQL-poolen. Mäts genom att dra max värdet mellan CPU-procent och data IO-procent |
| ConnectionsBlockedByFirewall        | Anslutningar blockerade av brand väggen | Antal   | Sum (standard)   | Antal anslutningar blockeras av brand Väggs regler. Gå tillbaka till principer för åtkomst kontroll för SQL-poolen och övervaka dessa anslutningar om antalet är högt |
| AdaptiveCacheHitPercent             | Procentuellt antal träffar i adaptiv cache   | Procent | Max (standard), min, AVG | Mäter hur bra arbets belastningar använder adaptiv cache. Använd det här måttet med värdet för cache träff i procent för att avgöra om du vill skala för ytterligare kapacitet eller köra arbets belastningar på nytt för att utföra cachen |
| AdaptiveCacheUsedPercent            | Procentuell användning av adaptiv cache  | Procent | Max (standard), min, AVG | Mäter hur bra arbets belastningar använder adaptiv cache. Använd det här måttet med den cachelagrade procentens mått för att avgöra om du vill skala för ytterligare kapacitet eller köra arbets belastningar på nytt för att utföra cachen |
| LocalTempDBUsedPercent              | Lokal tempdb Använd procent    | Procent | Max (standard), min, AVG | Lokal tempdb-användning över alla Compute-noder – värden genereras var femte minut |
| MemoryUsedPercent                   | Använt minne i procent          | Procent | Max (standard), min, AVG | Minnes användning för alla noder i SQL-poolen |
| CPUPercent                          | Procent andel processor användning             | Procent | Max (standard), min, AVG | CPU-användning över alla noder i SQL-poolen |
| Anslutningar                         | Anslutningar                     | Antal   | Sum (standard)  | Totalt antal inloggningar till SQL-poolen |
| ActiveQueries                      | Aktiva frågor                 | Antal   | Sum (standard)   | Aktiva frågor. Om du använder det här måttet ofiltrerad och unsplit visas alla aktiva frågor som körs i systemet |
| QueuedQueries                      | Köade frågor                  | Antal   | Sum (standard)   | Antal sammanställda begär anden i kö efter att max gränsen för samtidighet nåddes |
| WLGActiveQueries                   | Aktiva frågor för arbets belastnings grupp   | Antal   | Sum (standard)   | Aktiva frågor i arbets belastnings gruppen. Om du använder det här måttet ofiltrerad och unsplit visas alla aktiva frågor som körs i systemet |
| WLGActiveQueriesTimeouts           | Fråge tids gränser för arbets belastnings grupp   | Antal   | Sum (standard)   | Frågor för arbets belastnings gruppen som har nått tids gränsen. Tids gränser för frågor som rapporteras av detta mått sker bara när frågan har börjat köras (den innehåller inte vänte tid på grund av låsning eller väntande resurs avbrott) |
| WLGQueuedQueries                   | Köade frågor i arbets belastnings gruppen   | Antal   | Sum (standard)   | Antal sammanställda begär anden i kö efter att max gränsen för samtidighet nåddes |
| WLGAllocationBySystemPercent        | Allokering av arbets belastnings grupp efter system procent | Procent | Max (standard), min, AVG, sum | Procent beläggningen av resurser i förhållande till hela systemet |
| WLGAllocationByEffectiveCapResourcePercent   | Allokering av arbets belastnings grupper efter max resurs procent | Procent | Max (standard), min, AVG | Visar procent tilldelningen av resurser i förhållande till den effektiva Kap resurs procenten per arbets belastnings grupp. Detta mått ger arbets belastnings gruppens effektiva användning |
| WLGEffectiveCapResourcePercent      | Effektiv tak resurs procent  | Procent | Max (standard), min, AVG | Den effektiva Kap resurs procenten för arbets belastnings gruppen. Om det finns andra arbets belastnings grupper med min_percentage_resource > 0 sänks effective_cap_percentage_resource proportionellt |
| WLGEffectiveMinResourcePercent      | Effektiv minsta resurs procent  | Procent | Max (standard), min, AVG, sum | Inställningen för lägsta procent andel av resursen som tillåts överväger Service nivån och inställningarna för arbets belastnings gruppen. Effektiv min_percentage_resource kan justeras högre på lägre service nivåer |

### <a name="apache-spark-pool-metrics"></a>Mått för Apache Spark pool

Här följer några av de mått som har spridits av Apache Spark pooler:

| **Mått**                           | **Mått kategori, visnings namn**                  | **Enhet** | **Agg regerings typer** | **Beskrivning**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| BigDataPoolApplicationsEnded  | Avslutade Apache Spark program  | Antal | Sum (standard) | Antalet program för Apache Spark-pool avslutad |
| BigDataPoolAllocatedCores     | Antalet virtuella kärnor som allokerats till Apache Spark poolen                 | Antal | Max (standard), min, AVG | Allokerade virtuella kärnor för en Apache Spark pool |
| BigDataPoolAllocatedMemory    | Mängd minne (GB) som allokerats till Apache Spark poolen            | Antal | Max (standard), min, AVG | Allokerat minne för Apache Spark pool (GB) |
| BigDataPoolApplicationsActive | Aktiva Apache Spark program | Antal | Max (standard), min, AVG | Antal aktiva Apache Spark pool program |

## <a name="alerts"></a>Aviseringar

Logga in på Azure Portal och välj **övervaka**  >  **aviseringar** för att skapa aviseringar.

### <a name="create-alerts"></a>Skapa aviseringar

1. Välj **+ ny varnings regel** för att skapa en ny avisering.

1. Definiera **aviserings villkoret** för att ange när aviseringen ska utlösas.

    > [!NOTE]
    > Se till att markera **alla** i list rutan **Filtrera efter resurs typ** .

1. Definiera **aviserings informationen** för att ytterligare ange hur aviseringen ska konfigureras.

1. Definiera **Åtgärds gruppen** för att bestämma vem som ska ta emot aviseringarna (och hur).

## <a name="logs"></a>Loggar

### <a name="workspace-level-logs"></a>Loggar på arbets ytans nivå

Här följer de loggar som genereras av Azure Synapse Analytics-arbetsytor:

| Log Analytics tabell namn       | Logg kategori namn              | Description          |
|--------------------------------|--------------------------------|----------------------|
| SynapseGatewayApiRequests      | GatewayApiRequests             | API-begäranden för Azure Synapse Gateway. |
| SynapseRbacOperations          | SynapseRbacOperations          | Azure Synapse-rollbaserade åtkomst kontroll åtgärder (SRBAC). |
| SynapseBuiltinSqlReqsEnded     | BuiltinSqlReqsEnded            | Den inbyggda Azure Synapse SQL-poolen avslutade begär Anden. |
| SynapseIntegrationPipelineRuns | IntegrationPipelineRuns        | Azure Synapse integration pipeline körs. |
| SynapseIntegrationActivityRuns | IntegrationActivityRuns        | Azure Synapse integrations aktivitet körs. |
| SynapseIntegrationTriggerRuns  | IntegrationTriggerRuns         | Azure Synapse integration-Utlösare körs. |

### <a name="dedicated-sql-pool-logs"></a>Dedikerade SQL-pool loggar

Här följer de loggar som genereras av dedikerade SQL-pooler:

| Log Analytics tabell namn        | Logg kategori namn             | Description |
|----------------------|--------------------------------------|-------------|
| SynapseSqlPoolExecRequests  | ExecRequests | Information om SQL-begäranden/-frågor i en dedikerad Azure Synapse-pool.
| SynapseSqlPoolDmsWorkers    | DmsWorkers   | Information om anställda som slutför DMS-stegen i en dedikerad Azure Synapse-pool.
| SynapseSqlPoolRequestSteps  | RequestSteps | Information om begär Ande steg som skapar en specifik SQL-begäran/-fråga i en dedikerad Azure Synapse-pool.
| SynapseSqlPoolSqlRequests   | SqlRequests  | Information om fråge distributioner av stegen i SQL-begäranden/-frågor i en dedikerad Azure Synapse-pool.
| SynapseSqlPoolWaits         | Väntar        | Information om vänte lägen som påträffades vid körning av en SQL-begäran/-fråga i en dedikerad Azure Synapse-pool, inklusive lås och väntar på överförings köer.

Mer information om dessa loggar finns i följande information:
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="apache-spark-pool-log"></a>Logg för Apache Spark bassäng

Följande logg har avsänts av Apache Spark pooler:

| Log Analytics tabell namn               | Logg kategori namn              | Description                 |
|-----------------------------|---------------------------------------|-----------------------------|
| SynapseBigDataPoolApplicationsEnded | BigDataPoolAppsEnded | Information om avslutade Apache Spark program |

### <a name="diagnostic-settings"></a>Diagnostikinställningar

Använd diagnostikinställningar för att konfigurera diagnostikloggar för icke-Compute-resurser. Inställningarna för en resurs kontroll har följande funktioner:

* De anger var diagnostikloggar ska skickas. Exempel på detta är ett Azure Storage-konto, en Azure Event Hub eller övervaknings loggar.
* De anger vilka logg kategorier som skickas.
* De anger hur länge varje logg kategori ska behållas i ett lagrings konto.
* En kvarhållning på noll dagar innebär att loggar bevaras för alltid. Annars kan värdet vara ett valfritt antal dagar från 1 till 2 147 483 647.
* Om bevarande principer har angetts men lagring av loggar i ett lagrings konto är inaktive rad, har bevarande principerna ingen påverkan. Det här tillståndet kan till exempel inträffa när endast Event Hubs eller övervaka loggar alternativ är markerade.
* Bevarande principer tillämpas per dag. Gränsen mellan dagar sker vid midnatt Coordinated Universal Time (UTC). I slutet av en dag raderas loggar från dagar som överskrider bevarande principen. Om du till exempel har en bevarande princip på en dag tas loggarna från innan igår bort i början av i dag.

Med Azure Monitor diagnostikinställningar kan du vidarebefordra diagnostikloggar för analys till flera olika mål.

* **Lagrings konto**: Spara dina diagnostikloggar till ett lagrings konto för granskning eller manuell kontroll. Du kan använda diagnostikinställningar för att ange Retentions tiden i dagar.
* **Event Hub**: strömma loggarna till Azure Event Hubs. Loggarna blir inmatade i en partner tjänst/anpassad analys lösning som Power BI.
* **Log Analytics arbets yta**: analysera loggarna med Log Analytics. Azure Synapse-integreringen med Log Analytics är användbar i följande scenarier:
  * Du vill skriva komplexa frågor på en omfattande uppsättning mått som publiceras av Azure Synapse för att Log Analytics. Du kan skapa anpassade aviseringar för dessa frågor via Azure Monitor.
  * Du vill övervaka flera arbets ytor. Du kan dirigera data från flera arbets ytor till en enda Log Analytics-arbetsyta.

Du kan också använda ett lagrings konto eller ett Event Hub-namnområde som inte finns i prenumerationen på den resurs som avger loggar. Den användare som konfigurerar inställningen måste ha rätt åtkomst kontroll för Azure-rollbaserad åtkomst kontroll till båda prenumerationerna.

#### <a name="configure-diagnostic-settings"></a>Konfigurera diagnostikinställningar

Skapa eller Lägg till diagnostikinställningar för din arbets yta, dedikerad SQL-pool eller Apache Spark pool.

1. I portalen går du till övervaka. Välj **Inställningar**  >  **diagnostikinställningar**.

1. Välj arbets ytan Synapse, en dedikerad SQL-pool eller Apache Spark pool som du vill skapa en diagnostisk inställning för.

1. Om det inte finns några diagnostikinställningar på den valda arbets ytan uppmanas du att skapa en inställning. Välj **Aktivera diagnostik**.

   Om det finns befintliga diagnostikinställningar på arbets ytan visas en lista över inställningar som redan har kon figurer ATS på resursen. Välj **Lägg till diagnostikinställning**.

1. Ange ett namn, Välj **Skicka till Log Analytics** och välj sedan en arbets yta från **Log Analytics arbets yta**.

    > [!NOTE]
    > Eftersom en Azure-loggfil inte kan ha fler än 500 kolumner rekommenderar vi **starkt** att du väljer _resurs-/regionsspecifika läge_. Mer information finns i [referens för AzureDiagnostics-loggar](/azure/azure-monitor/reference/tables/azurediagnostics).

1. Välj **Spara**.

Efter en liten stund visas den nya inställningen i listan med inställningar för din arbets yta, dedikerad SQL-pool eller Apache Spark pool. Diagnostikloggar strömmas till den arbets ytan så snart som nya händelse data genereras. Upp till 15 minuter kan förflyta mellan när en händelse genereras och när den visas i Log Analytics.

## <a name="next-steps"></a>Nästa steg

Mer information om övervakning av pipeline-körningar finns i artikeln [övervaka pipelines körs i Synapse Studio](how-to-monitor-pipeline-runs.md) . 

Mer information om övervakning av Apache Spark program finns i artikeln [övervaka Apache Spark program i Synapse Studio](apache-spark-applications.md) .

Mer information om övervakning av SQL-begäranden finns i artikeln [Övervaka SQL-begäranden i Synapse Studio](how-to-monitor-sql-requests.md) .
