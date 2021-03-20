---
title: Konfigurera strömnings export av mått och resurs loggar
description: Lär dig hur du konfigurerar strömnings export av mått och resurs loggar, inklusive intelligent Diagnostic Analysis från Azure SQL Database och Azure SQL-hanterad instans till målet som du väljer för att lagra information om resursutnyttjande och statistik för körning av frågor.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: seoapril2019
ms.devlang: sqldbrb=2
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 04/06/2020
ms.openlocfilehash: 1de2c1ff02c799d04f2ab2c81e83dda5001a531f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "100592723"
---
# <a name="configure-streaming-export-of-azure-sql-database-and-sql-managed-instance-diagnostic-telemetry"></a>Konfigurera direkt uppspelnings export av Azure SQL Database-och SQL-hanterad instans Diagnostic-telemetri
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

I den här artikeln får du lära dig mer om prestanda mått och resurs loggar för Azure SQL Database som du kan exportera till en av flera mål för analys. Du får lära dig hur du konfigurerar strömnings exporten av denna diagnostiska telemetri via Azure Portal, PowerShell, Azure CLI, REST API och Azure Resource Manager mallar.

Du får också lära dig om de destinationer som du kan använda för att strömma denna diagnostiska telemetri och hur du väljer bland dessa alternativ. Dina mål alternativ är:

- [Log Analytics och SQL-analys](#stream-into-sql-analytics)
- [Event Hubs](#stream-into-event-hubs)
- [Azure Storage](#stream-into-azure-storage)

## <a name="diagnostic-telemetry-for-export"></a>Diagnostisk telemetri för export

Viktigast av den diagnostiska telemetri som du kan exportera är Intelligent Insights (SQLInsights)-loggen. [Intelligent Insights](intelligent-insights-overview.md) använder inbyggd intelligens för att kontinuerligt övervaka databas användningen via artificiell intelligens och identifiera störande händelser som orsakar dåliga prestanda. När den har identifierats utförs en detaljerad analys som genererar en Intelligent Insights logg med en intelligent utvärdering av problemet. Den här utvärderingen består av en rotor Saks analys av databasens prestanda problem och, om möjligt, rekommendationer för prestanda förbättringar. Du måste konfigurera strömnings exporten av den här loggen för att visa dess innehåll.

Förutom att strömma exporten av Intelligent Insights loggen kan du också exportera en mängd prestanda mått och ytterligare databas loggar. I följande tabell beskrivs de prestanda mått och resurs loggar som du kan konfigurera för strömnings export till en av flera destinationer. Denna diagnostiska telemetri kan konfigureras för enskilda databaser, elastiska pooler och databaser i pooler och hanterade instanser och instans databaser.

| Diagnostic-telemetri för databaser | Azure SQL Database support | Support för Azure SQL-hanterad instans |
| :------------------- | ----- | ----- |
| [Basic-mått](#basic-metrics): innehåller DTU/CPU-procent, DTU/CPU-gräns, fysisk data läsning i procent, logg skrivnings procent, lyckad/misslyckad/blockerad av brand Väggs anslutningar, procent andel av arbets tagare, lagring, lagrings procent och XTP lagrings procent. | Ja | Inga |
| [Instans och app Advanced](#advanced-metrics): innehåller tempdb-systemets databas data och logg fils storlek och tempdb-logg filen som används. | Ja | Inga |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): innehåller information om frågans körnings statistik, till exempel processor användning och statistik över fråge varaktighet. | Ja | Ja |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): innehåller information om frågan vänta i statistik (vad dina frågor väntar på), t. ex. CPU, logg och låsning. | Ja | Ja |
| [Fel](#errors-dataset): innehåller information om SQL-fel på en databas. | Ja | Ja |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): innehåller information om hur lång tid en databas har använt för att vänta på olika vänte typer. | Ja | Inga |
| [Timeout](#time-outs-dataset): innehåller information om tids gränser för en databas. | Ja | Inga |
| [Block](#blockings-dataset): innehåller information om hur du blockerar händelser i en databas. | Ja | Inga |
| [Död lägen](#deadlocks-dataset): innehåller information om deadlock-händelser på en databas. | Ja | Inga |
| [AutomaticTuning](#automatic-tuning-dataset): innehåller information om automatiska justerings rekommendationer för en databas. | Ja | Inga |
| [SQLInsights](#intelligent-insights-dataset): innehåller intelligent Insights till prestanda för en databas. Läs mer i [intelligent Insights](intelligent-insights-overview.md). | Ja | Ja |

> [!NOTE]
> Diagnostikinställningar kan inte konfigureras för **system databaser**, till exempel Master-, msdb-, Model-, Resource-och tempdb-databaser.

## <a name="streaming-export-destinations"></a>Strömmande export destinationer

Denna diagnostiska telemetri kan strömmas till någon av följande Azure-resurser för analys.

- **[Log Analytics arbets yta](#stream-into-sql-analytics)**:

  Data som strömmas till en [Log Analytics arbets yta](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) kan användas av [SQL Analytics](../../azure-monitor/insights/azure-sql.md). SQL Analytics är en övervaknings lösning för endast molnet som ger intelligent övervakning av dina databaser som innehåller prestanda rapporter, aviseringar och rekommendationer. Data som strömmas till en Log Analytics arbets yta kan analyseras med andra övervaknings data som samlas in och du kan använda andra Azure Monitor funktioner som aviseringar och visualiseringar
- **[Azure-Event Hubs](#stream-into-event-hubs)**:

  Data som strömmas till en [Azure Event Hub](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)innehåller följande funktioner:

  - **Strömma loggar till loggnings-och telemetri system från tredje part**: strömma alla dina mått och resurs loggar till en enda händelsehubben för att skicka loggdata till ett Siem-eller Log Analytics-verktyg från tredje part.
  - **Bygg en anpassad telemetri-och loggnings plattform**: den mycket skalbara publicerings prenumerations typen för Event Hub gör det möjligt att samla in mått och resurs loggar på ett flexibelt sätt i en anpassad telemetri-plattform. Mer information finns i [utforma och ändra storlek på en plattform för global skalnings telemetri på Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .
  - **Visa tjänstens hälsa genom att strömma data till Power BI**: Använd Event Hubs, Stream Analytics och Power BI för att omvandla dina diagnostikdata till nära real tids insikter om dina Azure-tjänster. Mer information om den här lösningen finns i [Stream Analytics och Power BI: en analys instrument panel i real tid för att strömma data](../../stream-analytics/stream-analytics-power-bi-dashboard.md) .
- **[Azure Storage](#stream-into-azure-storage)**:

  Data som strömmas till [Azure Storage](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) gör att du kan arkivera stora mängder diagnostiska telemetri för en bråkdel av kostnaden för föregående två strömnings alternativ.

Denna diagnostiska telemetri strömmas till någon av dessa destinationer och kan användas för att mäta resursutnyttjande och köra statistik för att få bättre prestanda övervakning.

![Diagrammet visar många SQL-databaser och databaser i hanterade instanser som skickar telemetri till Azure-diagnostik som vidarebefordrar information till Azure SQL-analys, Händelsehubben och lagring.](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/architecture.png)

## <a name="enable-and-configure-the-streaming-export-of-diagnostic-telemetry"></a>Aktivera och konfigurera den strömmande exporten av Diagnostic-telemetri

Du kan aktivera och hantera mått och loggning av diagnostisk telemetri med någon av följande metoder:

- Azure Portal
- PowerShell
- Azure CLI
- REST-API:et för Azure Monitor
- Azure Resource Manager-mall

> [!NOTE]
> Information om hur du aktiverar Gransknings logg strömning av säkerhetstelemetri finns i [Konfigurera granskning för databasen](./auditing-overview.md#setup-auditing) och [gransknings loggar i Azure Monitor loggar och Azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

## <a name="configure-the-streaming-export-of-diagnostic-telemetry"></a>Konfigurera strömnings exporten av Diagnostic-telemetri

Du kan använda menyn **diagnostikinställningar** på Azure Portal för att aktivera och konfigurera strömning av diagnostisk telemetri. Dessutom kan du använda PowerShell, Azure CLI, [REST API](/rest/api/monitor/diagnosticsettings)och [Resource Manager-mallar](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md) för att konfigurera strömning av diagnostisk telemetri. Du kan ställa in följande destinationer för att strömma Diagnostic-telemetri: Azure Storage, Azure Event Hubs och Azure Monitor loggar.

> [!IMPORTANT]
> Den strömmande exporten av Diagnostic-telemetri är inte aktive rad som standard.

Välj någon av följande flikar för att få stegvisa anvisningar om hur du konfigurerar strömnings exporten av Diagnostic-telemetri i Azure Portal och skript för att utföra samma med PowerShell och Azure CLI.

# <a name="azure-portal"></a>[Azure-portalen](#tab/azure-portal)

### <a name="elastic-pools-in-azure-sql-database"></a>Elastiska pooler i Azure SQL Database

Du kan konfigurera en resurs för elastisk pool för att samla in följande diagnostiska telemetri:

| Resurs | Övervaka telemetri |
| :------------------- | ------------------- |
| **Elastisk pool** | [Basic-mått](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics) innehåller EDTU/CPU-procent, EDTU/CPU-gräns, fysisk data Läs procent, logg skrivnings procent, sessioner, procent andel, lagring, lagrings utrymme, lagrings gräns och XTP lagrings procent. |

Om du vill konfigurera strömning av diagnostisk telemetri för elastiska pooler och databaser i pooler måste du separat konfigurera var och en separat:

- Aktivera strömning av diagnostisk telemetri för en elastisk pool
- Aktivera strömning av diagnostisk telemetri för varje databas i elastisk pool

Den elastiska poolens behållare har sin egen telemetri separat från varje enskild Pools databas telemetri.

Följ dessa steg om du vill aktivera strömning av diagnostisk telemetri för en elastisk pool-resurs:

1. Gå till resursen för **elastisk pool** i Azure Portal.
2. Välj **diagnostikinställningar**.
3. Välj **Aktivera diagnostik** om det inte finns några tidigare inställningar eller Välj **Redigera inställning** för att redigera en föregående inställning.

   ![Aktivera diagnostik för elastiska pooler](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-enable.png)

4. Ange ett inställnings namn för din egen referens.
5. Välj en mål resurs för data för strömmande diagnostik: **arkivera till lagrings konto**, **strömma till en Event Hub** eller **Skicka till Log Analytics**.
6. För Log Analytics väljer du **Konfigurera** och skapa en ny arbets yta genom att välja **+ Skapa ny arbets yta** eller Välj en befintlig arbets yta.
7. Markera kryss rutan för telemetri för elastisk pool: **grundläggande** mått.
   ![Konfigurera diagnostik för elastiska pooler](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-selection.png)

8. Välj **Spara**.
9. Dessutom kan du konfigurera strömning av diagnostisk telemetri för varje databas i den elastiska pool som du vill övervaka genom att följa stegen som beskrivs i nästa avsnitt.

> [!IMPORTANT]
> Förutom att konfigurera diagnostisk telemetri för en elastisk pool måste du också konfigurera diagnostisk telemetri för varje databas i den elastiska poolen.

### <a name="databases-in-azure-sql-database"></a>Databaser i Azure SQL Database

Du kan konfigurera en databas resurs för att samla in följande diagnostiska telemetri:

| Resurs | Övervaka telemetri |
| :------------------- | ------------------- |
| **Enkel eller poolad databas** | [Basic-mått](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics) innehåller DTU-procent, använt DTU, DTU-gräns, processor procent, Läs procent för fysisk data, logg skrivnings procent, slutförd/misslyckad/blockerad av brand Väggs anslutningar, procent andel av arbets tagare, lagring, lagrings procent, XTP lagrings procent och död lägen. |

Följ dessa steg om du vill aktivera strömning av diagnostisk telemetri för en enda eller en pool databas:

1. Gå till Azure **SQL Database** -resurs.
2. Välj **diagnostikinställningar**.
3. Välj **Aktivera diagnostik** om det inte finns några tidigare inställningar eller Välj **Redigera inställning** för att redigera en föregående inställning. Du kan skapa upp till tre parallella anslutningar för att strömma diagnostisk telemetri.
4. Välj **Lägg till diagnostisk inställning** för att konfigurera parallell strömning av diagnostikdata till flera resurser.

   ![Aktivera diagnostik för enskilda databaser och databaser i pooler](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-enable.png)

5. Ange ett inställnings namn för din egen referens.
6. Välj en mål resurs för data för strömmande diagnostik: **arkivera till lagrings konto**, **strömma till en Event Hub** eller **Skicka till Log Analytics**.
7. För den händelsebaserade övervaknings upplevelsen markerar du följande kryss rutor för telemetri för databas diagnostik: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **fel**, **DatabaseWaitStatistics**, **tids gränser**, **block** och **död lägen**.
8. För en avancerad övervaknings upplevelse med en minut markerar du kryss rutan för **grundläggande** mått.

   ![Konfigurera diagnostik för Azure SQL Database](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-selection.png)
9. Välj **Spara**.
10. Upprepa de här stegen för varje databas som du vill övervaka.

> [!TIP]
> Upprepa de här stegen för varje enskild databas som du vill övervaka.

### <a name="instances-in-azure-sql-managed-instance"></a>Instanser i Azure SQL-hanterad instans

Du kan konfigurera en hanterad instans resurs för att samla in följande diagnostiska telemetri:

| Resurs | Övervaka telemetri |
| :------------------- | ------------------- |
| **Hanterad instans** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) innehåller antal virtuella kärnor, genomsnittlig CPU-procent, IO-begäranden, byte, lästa/skrivna, reserverat lagrings utrymme och använt lagrings utrymme. |

Om du vill konfigurera strömning av diagnostisk telemetri för hanterade instanser och instans databaser måste du separat konfigurera var och en:

- Aktivera strömning av diagnostisk telemetri för hanterade instanser
- Aktivera strömning av diagnostisk telemetri för varje instans databas

Behållaren för hanterad instans har sin egen telemetri separat från varje instans databas telemetri.

Följ dessa steg om du vill aktivera strömning av diagnostisk telemetri för en hanterad instans resurs:

1. Gå till resursen för **hanterade instanser** i Azure Portal.
2. Välj **diagnostikinställningar**.
3. Välj **Aktivera diagnostik** om det inte finns några tidigare inställningar eller Välj **Redigera inställning** för att redigera en föregående inställning.

   ![Aktivera diagnostik för hanterad instans](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-enable.png)

4. Ange ett inställnings namn för din egen referens.
5. Välj en mål resurs för data för strömmande diagnostik: **arkivera till lagrings konto**, **strömma till en Event Hub** eller **Skicka till Log Analytics**.
6. För Log Analytics väljer du **Konfigurera** och skapa en ny arbets yta genom att välja **+ Skapa ny arbets yta** eller använda en befintlig arbets yta.
7. Markera kryss rutan för telemetri för instans diagnostik: **ResourceUsageStats**.

   ![Konfigurera diagnostik för hanterad instans](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-selection.png)

8. Välj **Spara**.
9. Dessutom kan du konfigurera strömning av diagnostisk telemetri för varje instans databas i den hanterade instans som du vill övervaka genom att följa stegen som beskrivs i nästa avsnitt.

> [!IMPORTANT]
> Förutom att konfigurera diagnostisk telemetri för en hanterad instans måste du också konfigurera diagnostisk telemetri för varje instans databas.

### <a name="databases-in-azure-sql-managed-instance"></a>Databaser i Azure SQL-hanterad instans

Du kan konfigurera en instans databas resurs för att samla in följande diagnostiska telemetri:

| Resurs | Övervaka telemetri |
| :------------------- | ------------------- |
| **Instans databas** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) innehåller antal virtuella kärnor, genomsnittlig CPU-procent, IO-begäranden, byte, lästa/skrivna, reserverat lagrings utrymme och använt lagrings utrymme. |

Följ dessa steg om du vill aktivera strömning av diagnostisk telemetri för en instans databas:

1. Gå till **instans databas** resursen inom en hanterad instans.
2. Välj **diagnostikinställningar**.
3. Välj **Aktivera diagnostik** om det inte finns några tidigare inställningar eller Välj **Redigera inställning** för att redigera en föregående inställning.
   - Du kan skapa upp till tre (3) parallella anslutningar för att strömma diagnostisk telemetri.
   - Välj **+ Lägg till diagnostisk inställning** för att konfigurera parallell strömning av diagnostikdata till flera resurser.

   ![Aktivera diagnostik för instans databaser](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-enable.png)

4. Ange ett inställnings namn för din egen referens.
5. Välj en mål resurs för data för strömmande diagnostik: **arkivera till lagrings konto**, **strömma till en Event Hub** eller **Skicka till Log Analytics**.
6. Markera kryss rutorna för telemetri för databas diagnostik: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** och **errors**.
   ![Konfigurera diagnostik för instans databaser](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-selection.png)
7. Välj **Spara**.
8. Upprepa de här stegen för varje instans databas som du vill övervaka.

> [!TIP]
> Upprepa de här stegen för varje instans databas som du vill övervaka.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

Du kan aktivera mått och diagnostikloggning genom att använda PowerShell.

- Om du vill aktivera lagring av mått och resurs loggar i ett lagrings konto använder du följande kommando:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
  ```

  Lagrings kontots ID är resurs-ID för mål lagrings kontot.

- Använd följande kommando för att aktivera strömning av mått och resurs loggar till en händelsehubben:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
  ```

  Regel-ID för Azure Service Bus är en sträng med det här formatet:

  ```powershell
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Använd följande kommando för att aktivera sändning av mått och resurs loggar till en Log Analytics arbets yta:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
  ```

- Du kan hämta resurs-ID för Log Analytics-arbetsytan med hjälp av följande kommando:

  ```powershell
  (Get-AzOperationalInsightsWorkspace).ResourceId
  ```

Du kan kombinera dessa parametrar om du vill aktivera flera utdataalternativ.

**Konfigurera flera Azure-resurser**

Använd PowerShell-skriptet från [Aktivera Azure Resource Metric-loggning med PowerShell](/archive/blogs/msoms/enable-azure-resource-metrics-logging-using-powershell)för att stödja flera prenumerationer.

Ange arbets ytans resurs-ID \<$WSID\> som en parameter när du kör skriptet `Enable-AzureRMDiagnostics.ps1` för att skicka diagnostikdata från flera resurser till arbets ytan.

- Använd följande skript för att hämta arbetsyte-ID \<$WSID\> : t för målet för dina diagnostikdata:

  ```powershell
  $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
  .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
  ```

  Ersätt \<subID\> med prenumerations-ID, \<RG_NAME\> med resurs gruppens namn och \<WS_NAME\> med namnet på arbets ytan.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan aktivera mått och diagnostikloggning genom att använda Azure CLI.

> [!IMPORTANT]
> Skript för att aktivera diagnostikloggning stöds för Azure CLI v 1.0. Azure CLI v 2.0 stöds inte för tillfället.

- Använd följande kommando för att aktivera lagring av mått och resurs loggar i ett lagrings konto:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
  ```

  Lagrings kontots ID är resurs-ID för mål lagrings kontot.

- Använd följande kommando för att aktivera strömning av mått och resurs loggar till en händelsehubben:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
  ```

  Service Bus regelns ID är en sträng med det här formatet:

  ```azurecli-interactive
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Om du vill aktivera sändning av mått och resurs loggar till en Log Analytics arbets yta använder du följande kommando:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
  ```

Du kan kombinera dessa parametrar om du vill aktivera flera utdataalternativ.

---

## <a name="stream-into-sql-analytics"></a>Strömma till SQL Analytics

Azure SQL Database och Azure SQL-hanterade instans mått och resurs loggar som strömmas till en Log Analytics arbets yta kan förbrukas av Azure SQL-analys. Azure SQL-analys är en moln lösning som övervakar prestanda för enskilda databaser, elastiska pooler och databaser i pooler och hanterade instanser och instans databaser i skala och över flera prenumerationer. Det kan hjälpa dig att samla in och visualisera prestanda mått och har inbyggd intelligens för fel sökning av prestanda.

![Översikt över Azure SQL-analys](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/azure-sql-sol-overview.png)

### <a name="installation-overview"></a>Installationsöversikt

Du kan övervaka en samling databaser och databas samlingar med Azure SQL-analys genom att utföra följande steg:

1. Skapa en Azure SQL-analys-lösning från Azure Marketplace.
2. Skapa en Log Analytics arbets yta i lösningen.
3. Konfigurera databaser för strömning av diagnostisk telemetri till arbets ytan.

Du kan konfigurera strömnings exporten av den här Diagnostic-Telemetrin genom att använda det inbyggda alternativet **Skicka till Log Analytics** på fliken diagnostikinställningar i Azure Portal. Du kan också aktivera strömning i en Log Analytics arbets yta genom att använda diagnostikinställningar via [PowerShell-cmdletar](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-powershell#configure-the-streaming-export-of-diagnostic-telemetry), [Azure CLI](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-cli#configure-the-streaming-export-of-diagnostic-telemetry), [Azure Monitor REST API](/rest/api/monitor/diagnosticsettings)eller [Resource Manager-mallar](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md).

### <a name="create-an-azure-sql-analytics-resource"></a>Skapa en Azure SQL-analys resurs

1. Sök efter Azure SQL-analys på Azure Marketplace och markera det.

   ![Sök efter Azure SQL-analys i portalen](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-in-marketplace.png)

2. Välj **skapa** på lösningens översikts skärm.

3. Fyll i Azure SQL-analys formuläret med ytterligare information som krävs: arbets ytans namn, prenumeration, resurs grupp, plats och pris nivå.

   ![Konfigurera Azure SQL-analys i portalen](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-configuration-blade.png)

4. Bekräfta genom att klicka på **OK** och välj sedan **skapa**.

### <a name="configure-the-resource-to-record-metrics-and-resource-logs"></a>Konfigurera resursen för att registrera mått och resurs loggar

Du måste separat konfigurera direkt uppspelning av diagnostisk telemetri för databaser med enkel och pool, elastiska pooler, hanterade instanser och instans databaser. Det enklaste sättet att konfigurera var en resurs registrerar mått är genom att använda Azure Portal. Detaljerade anvisningar finns i [Konfigurera strömnings exporten av Diagnostic-telemetri](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal#configure-the-streaming-export-of-diagnostic-telemetry).

### <a name="use-azure-sql-analytics-for-monitoring-and-alerting"></a>Använd Azure SQL-analys för övervakning och aviseringar

Du kan använda SQL Analytics som en hierarkisk instrument panel för att visa dina databas resurser.

- Information om hur du använder Azure SQL-analys finns i [övervaka med hjälp av SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Information om hur du konfigurerar aviseringar för i SQL Analytics finns i [skapa aviseringar för databas, elastiska pooler och hanterade instanser](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts).

## <a name="stream-into-event-hubs"></a>Strömma till Event Hubs

Du kan strömma Azure SQL Database-och Azure SQL-hanterade instans mått och resurs loggar till Event Hubs genom att använda den inbyggda **data strömmen till ett** alternativ för händelsehubben i Azure Portal. Du kan också aktivera ID för Service Bus regel med hjälp av diagnostikinställningar via PowerShell-cmdletar, Azure CLI eller Azure Monitor REST API. Se till att händelsehubben är i samma region som din databas och server.

### <a name="what-to-do-with-metrics-and-resource-logs-in-event-hubs"></a>Vad du kan göra med mått och resurs loggar i Event Hubs

När de valda data strömmas i Event Hubs är du ett steg närmare att aktivera avancerade övervaknings scenarier. Event Hubs fungerar som en front dörr för en händelse pipeline. När data har samlats in i en händelsehubben kan de omvandlas och lagras med hjälp av en analys av real tids analys eller ett lagrings kort. Event Hubs frikopplas produktionen av en data ström med händelser från användningen av dessa händelser. På så sätt kan händelse konsumenter komma åt händelserna i sitt eget schema. Mer information om Event Hubs finns i:

- [Vad är Azure Event Hubs?](../../event-hubs/event-hubs-about.md)
- [Kom igång med Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

Du kan använda strömmade mått i Event Hubs för att:

- **Visa tjänstens hälsa genom att strömma frekventa data till Power BI**

  Genom att använda Event Hubs, Stream Analytics och Power BI kan du enkelt omvandla dina mått och diagnostikdata till nära real tids insikter om dina Azure-tjänster. En översikt över hur du konfigurerar en Event Hub, bearbetar data med Stream Analytics och använder Power BI som utdata finns i [Stream Analytics och Power BI](../../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Strömma loggar till data strömmar och telemetri från tredje part**

  Genom att använda Event Hubs strömning kan du få mått och resurs loggar i olika övervaknings-och Log Analytics-lösningar från tredje part.

- **Skapa en anpassad plattform för telemetri och loggning**

  Har du redan en egen inbyggd telemetri plattform eller funderar du på att skapa en? Med den mycket skalbara publicerings prenumerations typen för Event Hubs kan du samla in mått och resurs loggar på ett flexibelt sätt. Se [Dan Rosanovas Guide to using Event Hubs in the Global-Scale-plattform för telemetri](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-azure-storage"></a>Strömma till Azure Storage

Du kan lagra mått och resurs loggar i Azure Storage med hjälp av det inbyggda **arkivet till ett lagrings konto** i Azure Portal. Du kan också aktivera lagring med hjälp av diagnostikinställningar via PowerShell-cmdletar, Azure CLI eller Azure Monitor REST API.

### <a name="schema-of-metrics-and-resource-logs-in-the-storage-account"></a>Schema för Mät värden och resurs loggar i lagrings kontot

När du har konfigurerat mått och resurs loggar samling skapas en lagrings behållare i det lagrings konto som du valde när de första raderna med data är tillgängliga. BLOB-strukturen är:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Eller mer enkelt:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Till exempel kan ett BLOB-namn för grundläggande mått vara:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Ett BLOB-namn för att lagra data från en elastisk pool ser ut så här:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Princip och prissättning för data lagring

Om du väljer Event Hubs eller ett lagrings konto kan du ange en bevarande princip. Den här principen tar bort data som är äldre än en vald tids period. Om du anger Log Analytics, är bevarande principen beroende av den valda pris nivån. I det här fallet kan de tillhandahållna kostnads fria enheterna av data inhämtningen möjliggöra kostnads fri övervakning av flera databaser varje månad. All användning av diagnostisk telemetri utöver de kostnads fria enheterna kan medföra kostnader.

> [!IMPORTANT]
> Aktiva databaser med tyngre arbets belastningar inhämtar mer data än inaktiva databaser. Mer information finns i [priser för Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Om du använder Azure SQL-analys kan du övervaka förbrukningen av data genom att välja OMS- **arbetsyta** i navigerings menyn i Azure SQL-analys och sedan välja **användning** och **beräknade kostnader**.

## <a name="metrics-and-logs-available"></a>Mått och loggar är tillgängliga

Övervakning av telemetri som är tillgängliga för enskilda databaser, databaser i pooler, elastiska pooler, hanterade instanser och instans databaser dokumenteras i det här avsnittet i artikeln. Insamlade övervakande telemetri i SQL Analytics kan användas för din egen anpassad analys och program utveckling med hjälp av [Azure Monitor logg frågor](../../azure-monitor/logs/get-started-queries.md) språk.

### <a name="basic-metrics"></a>Grundläggande mått

Se följande tabeller för information om grundläggande mått per resurs.

> [!NOTE]
> Alternativet grundläggande mått var tidigare känt som alla mått. Ändringen har gjorts enbart till namngivning och det gick inte att ändra de mått som övervakades. Den här ändringen initierades för att möjliggöra introduktion av ytterligare mått kategorier i framtiden.

#### <a name="basic-metrics-for-elastic-pools"></a>Grundläggande mått för elastiska pooler

|**Resurs**|**Mått**|
|---|---|
|Elastisk pool|eDTU-procent, eDTU-använt, eDTU-gräns, processor procent, Läs procent för fysisk data, logg skrivnings procent, sessioner, procent, lagring, lagrings procent, lagrings gräns, XTP Storage-procent |

#### <a name="basic-metrics-for-single-and-pooled-databases"></a>Grundläggande mått för enskilda databaser och databaser i pooler

|**Resurs**|**Mått**|
|---|---|
|Enkel databas och databas i pooler|DTU-procent, använt DTU, DTU-gräns, processor procent, Läs procent för fysisk data, logg skrivnings procent, slutförd/misslyckad/blockerad av brand Väggs anslutningar, sessioner procent, procent för arbetare, lagring, lagrings procent, XTP lagrings procent och död läge |

### <a name="advanced-metrics"></a>Avancerade mått

I följande tabell finns mer information om avancerade mått.

|**Mått**|**Mått visnings namn**|**Beskrivning**|
|---|---|---|
|sqlserver_process_core_percent<sup>1</sup>|SQL process Core-procent|Procent andel CPU-användning för SQL-processen, mätt av operativ systemet.|
|sqlserver_process_memory_percent<sup>1</sup> |Minnes procent för SQL-process|Minnes användnings procent för SQL-processen, mätt av operativ systemet.|
|tempdb_data_size<sup>2</sup>| Data fil storlek i tempdb i KB |Data fil storlek för tempdb i KB.|
|tempdb_log_size<sup>2</sup>| TempDB-logg fils storlek kilobyte |TempDB-logg fils storlek kilobyte.|
|tempdb_log_used_percent<sup>2</sup>| Procent använt tempdb-logg |TempDB procent logg används.|

<sup>1</sup> det här måttet är tillgängligt för databaser som använder vCore inköps modell med 2 virtuella kärnor och högre, eller 200 DTU och högre för DTU-baserade inköps modeller.

<sup>2</sup> det här måttet är tillgängligt för databaser som använder vCore inköps modell med 2 virtuella kärnor och högre, eller 200 DTU och högre för DTU-baserade inköps modeller. Det här måttet är för närvarande inte tillgängligt för storskaliga databaser eller informations lager.

### <a name="basic-logs"></a>Basic-loggar

Information om telemetri som är tillgängliga för alla loggar finns dokumenterade i följande tabeller. Mer information finns i [diagnostisk telemetri som stöds](#diagnostic-telemetry-for-export).

#### <a name="resource-usage-stats-for-managed-instances"></a>Resursanvändnings statistik för hanterade instanser

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Always: Azure|
|TimeGenerated [UTC]|Tidstämpel när loggen registrerades |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Namnet på resurs leverantören. Always: MICROSOFT. SQL |
|Kategori|Kategorins namn. Always: ResourceUsageStats |
|Resurs|Namn på resursen |
|ResourceType|Namnet på resurs typen. Always: MANAGEDINSTANCES |
|SubscriptionId|Prenumerations-GUID för databasen |
|ResourceGroup|Namnet på resurs gruppen för databasen |
|LogicalServerName_s|Namnet på den hanterade instansen |
|ResourceId|Resurs-URI |
|SKU_s|Produkt-SKU för SQL-hanterad instans |
|virtual_core_count_s|Antal tillgängliga virtuella kärnor |
|avg_cpu_percent_s|Genomsnittlig CPU-procent |
|reserved_storage_mb_s|Reserverad lagrings kapacitet på den hanterade instansen |
|storage_space_used_mb_s|Använt lagrings utrymme på den hanterade instansen |
|io_requests_s|IOPS-antal |
|io_bytes_read_s|Lästa IOPS-byte |
|io_bytes_written_s|Skrivna IOPS-byte |

#### <a name="query-store-runtime-statistics"></a>Körnings statistik för Query Store

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Tidstämpel när loggen registrerades |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Namnet på resurs leverantören. Always: MICROSOFT. SQL |
|Kategori|Kategorins namn. Always: QueryStoreRuntimeStatistics |
|OperationName|Åtgärdens namn. Always: QueryStoreRuntimeStatisticsEvent |
|Resurs|Namn på resursen |
|ResourceType|Namnet på resurs typen. Always: SERVERs/DATABASes |
|SubscriptionId|Prenumerations-GUID för databasen |
|ResourceGroup|Namnet på resurs gruppen för databasen |
|LogicalServerName_s|Namnet på servern för databasen |
|ElasticPoolName_s|Namnet på den elastiska poolen för databasen, om det finns någon |
|DatabaseName_s|Namn på databasen |
|ResourceId|Resurs-URI |
|query_hash_s|Fråga hash |
|query_plan_hash_s|Fråga planera hash |
|statement_sql_handle_s|Instruktion för SQL-referens |
|interval_start_time_d|Starta datetimeoffset för intervallet i antal Tick från 1900-1-1 |
|interval_end_time_d|Slutet av datetimeoffset för intervallet i antal Tick från 1900-1-1 |
|logical_io_writes_d|Totalt antal logiska IO-skrivningar |
|max_logical_io_writes_d|Högsta antal logiska IO-skrivningar per körning |
|physical_io_reads_d|Totalt antal fysiska IO-läsningar |
|max_physical_io_reads_d|Högsta antal logiska IO-läsningar per körning |
|logical_io_reads_d|Totalt antal logiska IO-läsningar |
|max_logical_io_reads_d|Högsta antal logiska IO-läsningar per körning |
|execution_type_d|Körnings typ |
|count_executions_d|Antal körningar av frågan |
|cpu_time_d|Total CPU-tid som använts av frågan i mikrosekunder |
|max_cpu_time_d|Maximal CPU-tid för konsument med en enda körning i mikrosekunder |
|dop_d|Summan av grader av parallellitet |
|max_dop_d|Högsta grad av parallellitet som används för enskild körning |
|rowcount_d|Totalt antal returnerade rader |
|max_rowcount_d|Högsta antal rader som returneras i en enskild körning |
|query_max_used_memory_d|Total mängd minne som används i KB |
|max_query_max_used_memory_d|Maximal mängd minne som används av en enskild körning i KB |
|duration_d|Total körnings tid i mikrosekunder |
|max_duration_d|Maximal körnings tid för en enskild körning |
|num_physical_io_reads_d|Totalt antal fysiska läsningar |
|max_num_physical_io_reads_d|Högsta antal fysiska läsningar per körning |
|log_bytes_used_d|Total mängd logg byte som använts |
|max_log_bytes_used_d|Maximal mängd logg byte som använts per körning |
|query_id_d|ID för frågan i Frågearkivet |
|plan_id_d|ID för planen i Frågearkivet |

Läs mer om [körnings statistik data för Query Store](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

#### <a name="query-store-wait-statistics"></a>Väntande statistik för Query Store

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Tidstämpel när loggen registrerades |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Namnet på resurs leverantören. Always: MICROSOFT. SQL |
|Kategori|Kategorins namn. Always: QueryStoreWaitStatistics |
|OperationName|Åtgärdens namn. Always: QueryStoreWaitStatisticsEvent |
|Resurs|Namn på resursen |
|ResourceType|Namnet på resurs typen. Always: SERVERs/DATABASes |
|SubscriptionId|Prenumerations-GUID för databasen |
|ResourceGroup|Namnet på resurs gruppen för databasen |
|LogicalServerName_s|Namnet på servern för databasen |
|ElasticPoolName_s|Namnet på den elastiska poolen för databasen, om det finns någon |
|DatabaseName_s|Namn på databasen |
|ResourceId|Resurs-URI |
|wait_category_s|Kategorin väntar |
|is_parameterizable_s|Är frågan parameteriseringsbar |
|statement_type_s|Typ av instruktion |
|statement_key_hash_s|Utdrags nyckelns hash |
|exec_type_d|Typ av körning |
|total_query_wait_time_ms_d|Total vänte tid för frågan i en viss wait-kategori |
|max_query_wait_time_ms_d|Maximal vänte tid för frågan i en enskild körning i den specifika wait-kategorin |
|query_param_type_d|0 |
|query_hash_s|Fråga hash i Frågearkivet |
|query_plan_hash_s|Fråga planera hash i Frågearkivet |
|statement_sql_handle_s|Instruktions referens i Frågearkivet |
|interval_start_time_d|Starta datetimeoffset för intervallet i antal Tick från 1900-1-1 |
|interval_end_time_d|Slutet av datetimeoffset för intervallet i antal Tick från 1900-1-1 |
|count_executions_d|Antal körningar av frågan |
|query_id_d|ID för frågan i Frågearkivet |
|plan_id_d|ID för planen i Frågearkivet |

Läs mer om [väntande statistik data för Query Store](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

#### <a name="errors-dataset"></a>Data uppsättning fel

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Tidstämpel när loggen registrerades |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Namnet på resurs leverantören. Always: MICROSOFT. SQL |
|Kategori|Kategorins namn. Always: Errors |
|OperationName|Åtgärdens namn. Always: ErrorEvent |
|Resurs|Namn på resursen |
|ResourceType|Namnet på resurs typen. Always: SERVERs/DATABASes |
|SubscriptionId|Prenumerations-GUID för databasen |
|ResourceGroup|Namnet på resurs gruppen för databasen |
|LogicalServerName_s|Namnet på servern för databasen |
|ElasticPoolName_s|Namnet på den elastiska poolen för databasen, om det finns någon |
|DatabaseName_s|Namn på databasen |
|ResourceId|Resurs-URI |
|Meddelande|Fel meddelande i oformaterad text |
|user_defined_b|Är den användardefinierade biten för fel |
|error_number_d|Felkod |
|Allvarlighetsgrad|Felets allvarlighets grad |
|state_d|Tillstånd för felet |
|query_hash_s|Fråga hash för den misslyckade frågan, om den är tillgänglig |
|query_plan_hash_s|Fråga plan-hash för den misslyckade frågan, om den är tillgänglig |

Läs mer om [SQL-felmeddelanden](/sql/relational-databases/errors-events/database-engine-events-and-errors).

#### <a name="database-wait-statistics-dataset"></a>Data uppsättning för väntande statistik för databasen

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Tidstämpel när loggen registrerades |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Namnet på resurs leverantören. Always: MICROSOFT. SQL |
|Kategori|Kategorins namn. Always: DatabaseWaitStatistics |
|OperationName|Åtgärdens namn. Always: DatabaseWaitStatisticsEvent |
|Resurs|Namn på resursen |
|ResourceType|Namnet på resurs typen. Always: SERVERs/DATABASes |
|SubscriptionId|Prenumerations-GUID för databasen |
|ResourceGroup|Namnet på resurs gruppen för databasen |
|LogicalServerName_s|Namnet på servern för databasen |
|ElasticPoolName_s|Namnet på den elastiska poolen för databasen, om det finns någon |
|DatabaseName_s|Namn på databasen |
|ResourceId|Resurs-URI |
|wait_type_s|Namn på vänte typen |
|start_utc_date_t [UTC]|Uppmätt period start tid |
|end_utc_date_t [UTC]|Uppmätt period slut tid |
|delta_max_wait_time_ms_d|Högsta vänte tid per körning |
|delta_signal_wait_time_ms_d|Total signal vänte tid |
|delta_wait_time_ms_d|Total vänte tid i perioden |
|delta_waiting_tasks_count_d|Antal väntande uppgifter |

Läs mer om [väntande statistik för databaser](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

#### <a name="time-outs-dataset"></a>Data uppsättning för tids gränser

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Tidstämpel när loggen registrerades |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Namnet på resurs leverantören. Always: MICROSOFT. SQL |
|Kategori|Kategorins namn. Always: timeout |
|OperationName|Åtgärdens namn. Always: TimeoutEvent |
|Resurs|Namn på resursen |
|ResourceType|Namnet på resurs typen. Always: SERVERs/DATABASes |
|SubscriptionId|Prenumerations-GUID för databasen |
|ResourceGroup|Namnet på resurs gruppen för databasen |
|LogicalServerName_s|Namnet på servern för databasen |
|ElasticPoolName_s|Namnet på den elastiska poolen för databasen, om det finns någon |
|DatabaseName_s|Namn på databasen |
|ResourceId|Resurs-URI |
|error_state_d|Fel tillstånds kod |
|query_hash_s|Fråga hash, om tillgängligt |
|query_plan_hash_s|Fråga om prenumerations-hash, om tillgängligt |

#### <a name="blockings-dataset"></a>Blocker-datauppsättning

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Tidstämpel när loggen registrerades |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Namnet på resurs leverantören. Always: MICROSOFT. SQL |
|Kategori|Kategorins namn. Always: Blocks |
|OperationName|Åtgärdens namn. Always: BlockEvent |
|Resurs|Namn på resursen |
|ResourceType|Namnet på resurs typen. Always: SERVERs/DATABASes |
|SubscriptionId|Prenumerations-GUID för databasen |
|ResourceGroup|Namnet på resurs gruppen för databasen |
|LogicalServerName_s|Namnet på servern för databasen |
|ElasticPoolName_s|Namnet på den elastiska poolen för databasen, om det finns någon |
|DatabaseName_s|Namn på databasen |
|ResourceId|Resurs-URI |
|lock_mode_s|Lås läge som används av frågan |
|resource_owner_type_s|Ägare till låset |
|blocked_process_filtered_s|Rapport-XML för blockerad process |
|duration_d|Längden på låset i mikrosekunder |

#### <a name="deadlocks-dataset"></a>Data uppsättning för deadlock

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC] |Tidstämpel när loggen registrerades |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Namnet på resurs leverantören. Always: MICROSOFT. SQL |
|Kategori|Kategorins namn. Always: död lägen |
|OperationName|Åtgärdens namn. Always: DeadlockEvent |
|Resurs|Namn på resursen |
|ResourceType|Namnet på resurs typen. Always: SERVERs/DATABASes |
|SubscriptionId|Prenumerations-GUID för databasen |
|ResourceGroup|Namnet på resurs gruppen för databasen |
|LogicalServerName_s|Namnet på servern för databasen |
|ElasticPoolName_s|Namnet på den elastiska poolen för databasen, om det finns någon |
|DatabaseName_s|Namn på databasen |
|ResourceId|Resurs-URI |
|deadlock_xml_s|Deadlock-rapportens XML |

#### <a name="automatic-tuning-dataset"></a>Data uppsättning för automatisk justering

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Tidstämpel när loggen registrerades |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Namnet på resurs leverantören. Always: MICROSOFT. SQL |
|Kategori|Kategorins namn. Always: AutomaticTuning |
|Resurs|Namn på resursen |
|ResourceType|Namnet på resurs typen. Always: SERVERs/DATABASes |
|SubscriptionId|Prenumerations-GUID för databasen |
|ResourceGroup|Namnet på resurs gruppen för databasen |
|LogicalServerName_s|Namnet på servern för databasen |
|LogicalDatabaseName_s|Namn på databasen |
|ElasticPoolName_s|Namnet på den elastiska poolen för databasen, om det finns någon |
|DatabaseName_s|Namn på databasen |
|ResourceId|Resurs-URI |
|RecommendationHash_s|Unik hash för automatisk justerings rekommendation |
|OptionName_s|Automatisk justerings åtgärd |
|Schema_s|Databasschemat |
|Table_s|Tabell som påverkas |
|IndexName_s|Index namn |
|IndexColumns_s|Kolumnnamn |
|IncludedColumns_s|Inkluderade kolumner |
|EstimatedImpact_s|Beräknad effekt av JSON för automatisk justerings rekommendation |
|Event_s|Typ av händelse för automatisk justering |
|Timestamp_t|Senast uppdaterad tidsstämpel |

#### <a name="intelligent-insights-dataset"></a>Intelligent Insights data uppsättning

Läs mer om det [intelligent Insights logg formatet](intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Nästa steg

Information om hur du aktiverar loggning och förstår de mått och logg kategorier som stöds av de olika Azure-tjänsterna finns i:

- [Översikt över mått i Microsoft Azure](../../azure-monitor/data-platform.md)
- [Översikt över Azure-plattformsloggar](../../azure-monitor/essentials/platform-logs-overview.md)

Läs mer om Event Hubs:

- [Vad är Azure Event Hubs?](../../event-hubs/event-hubs-about.md)
- [Kom igång med Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

Information om hur du konfigurerar aviseringar baserat på telemetri från Log Analytics finns i:

- [Skapa aviseringar för Azure SQL Database och Azure SQL-hanterad instans](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)