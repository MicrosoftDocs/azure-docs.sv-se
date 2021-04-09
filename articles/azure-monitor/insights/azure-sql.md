---
title: Azure SQL-analys lösning i Azure Monitor | Microsoft Docs
description: Azure SQL-analys lösning hjälper dig att hantera dina Azure SQL-databaser
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 09/19/2020
ms.reviewer: carlrab
ms.openlocfilehash: 54ef88e65925ba9c7e9fe2e44ef0c76fbc9ceb04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101717493"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Övervaka Azure SQL Database att använda Azure SQL-analys (förhands granskning)

![Azure SQL-analys symbol](./media/azure-sql/azure-sql-symbol.png)

Azure SQL-analys är en avancerad lösning för moln övervakning för att övervaka prestanda för alla dina Azure SQL-databaser i stor skala och över flera prenumerationer i en enda vy. Azure SQL-analys samlar in och visualiserar viktiga prestanda mått med inbyggd intelligens för prestanda fel sökning.

Genom att använda dessa insamlade mått kan du skapa anpassade övervaknings regler och aviseringar. Azure SQL-analys hjälper dig att identifiera problem på varje lager i program stacken. Azure Diagnostic-mått används tillsammans med Azure Monitor vyer för att presentera data om alla dina Azure SQL-databaser på en enda Log Analytics arbets yta. Azure Monitor hjälper dig att samla in, korrelera och visualisera strukturerade och ostrukturerade data.

En praktisk översikt över hur du använder Azure SQL-analys lösning och för vanliga användnings scenarier finns i inbäddad video:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Anslutna källor

Azure SQL-analys är en övervaknings lösning för endast moln som stöder strömning av diagnostisk telemetri för alla dina Azure SQL-databaser. Eftersom Azure SQL-analys inte använder agenter för att ansluta till Azure Monitor, stöder den inte övervakning av SQL Server som finns lokalt eller i virtuella datorer.

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| [Diagnostikinställningar](../essentials/diagnostic-settings.md) | **Ja** | Azure Metric-och loggdata skickas till Azure Monitor loggar direkt av Azure. |
| [Azure Storage-konto](../essentials/resource-logs.md#send-to-log-analytics-workspace) | Inga | Azure Monitor läser inte data från ett lagrings konto. |
| [Windows-agenter](../agents/agent-windows.md) | Inga | Direkta Windows-agenter används inte av Azure SQL-analys. |
| [Linux-agenter](../vm/quick-collect-linux-computer.md) | Inga | Direct Linux-agenter används inte av Azure SQL-analys. |
| [System Center Operations Manager-hanteringsgrupp](../agents/om-agents.md) | Inga | En direkt anslutning från Operations Manager agent till Azure Monitor används inte av Azure SQL-analys. |

## <a name="azure-sql-analytics-options"></a>Azure SQL-analys alternativ

I tabellen nedan visas vilka alternativ som stöds för två versioner av Azure SQL-analys instrument panelen, en för Azure SQL Database och den andra för Azure SQL-hanterade instans databaser.

| Azure SQL-analys alternativ | Beskrivning | SQL Database support | Stöd för SQL Managed Instance |
| --- | ------- | ----- | ----- |
| Resurs efter typ | Perspektiv som räknar alla övervakade resurser. | Ja | Ja |
| Insikter | Tillhandahåller hierarkisk visning av Intelligent Insights i prestanda. | Ja | Ja |
| Fel | Tillhandahåller hierarkisk visning av SQL-fel som har inträffat i databaserna. | Ja | Ja |
| Timeouter | Tillhandahåller hierarkisk visning av SQL-tidsgräns som har inträffat i databaserna. | Ja | Inga |
| Blockning | Tillhandahåller hierarkisk visning av SQL-block som har inträffat i databaserna. | Ja | Inga |
| Databasen väntar | Ger hierarkisk visning av SQL wait-statistik på databas nivå. Innehåller sammanfattningar av den totala vänte tiden och vänte tiden per wait-typ. |Ja | Inga |
| Frågans varaktighet | Tillhandahåller hierarkisk visning av statistik för frågekörning, till exempel fråge varaktighet, CPU-användning, data-i/o-användning, logg-i/o-användning. | Ja | Ja |
| Fråga väntar | Tillhandahåller hierarkisk visning i frågan vänta i statistiken efter kategorin vänta. | Ja | Ja |

## <a name="configuration"></a>Konfiguration

Använd processen som beskrivs i [Lägg till Azure Monitor lösningar från Lösningsgalleriet](./solutions.md) för att lägga till Azure SQL-analys (för hands version) i Log Analytics arbets ytan.

### <a name="configure-azure-sql-database-to-stream-diagnostics-telemetry"></a>Konfigurera Azure SQL Database till Stream Diagnostics-telemetri

När du har skapat Azure SQL-analys lösning i din arbets yta måste du **Konfigurera varje** resurs som du vill övervaka för att strömma sin diagnostik-telemetri till Azure SQL-analys. Följ detaljerade instruktioner på den här sidan:

- Aktivera Azure-diagnostik för att din databas ska kunna [strömma telemetri till Azure SQL-analys](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

Sidan ovan innehåller också instruktioner om hur du aktiverar stöd för övervakning av flera Azure-prenumerationer från en enda Azure SQL-analys arbets yta som ett enda fönster i glaset.

## <a name="using-azure-sql-analytics"></a>Använda Azure SQL-analys

När du lägger till Azure SQL-analys på din arbets yta läggs panelen Azure SQL-analys till i din arbets yta och visas i Översikt. Välj Visa sammanfattnings länk för att läsa in panelens innehåll.

![Azure SQL-analys sammanfattnings panel](./media/azure-sql/azure-sql-sol-tile-01.png)

När den har lästs in visar panelen antalet databaser och elastiska pooler i SQL Database och instanser och instans databaser i SQL-hanterad instans från vilka Azure SQL-analys ta emot telemetri för diagnostik.

![Azure SQL-analys panel](./media/azure-sql/azure-sql-sol-tile-02.png)

Azure SQL-analys innehåller två separata vyer – ett för att övervaka SQL Database och den andra vyn för övervakning av SQL-hanterad instans.

Om du vill visa Azure SQL-analys övervaknings instrument panel för SQL Database klickar du på den övre delen av panelen. Om du vill visa Azure SQL-analys övervaknings instrument panel för SQL-hanterad instans klickar du på den nedre delen av panelen.

### <a name="viewing-azure-sql-analytics-data"></a>Visa Azure SQL-analys data

Instrument panelen innehåller en översikt över alla databaser som övervakas via olika perspektiv. För att olika perspektiv ska fungera måste du aktivera lämpliga mått eller loggar på dina SQL-resurser för att strömma till Log Analytics arbets ytan.

Om vissa mått eller loggar inte strömmas i Azure Monitor, fylls inte panelerna i Azure SQL-analys med övervaknings information.

### <a name="sql-database-view"></a>SQL Database vy

När Azure SQL-analys panelen för databasen har valts visas övervaknings instrument panelen.

![Skärm bild som visar instrument panelen för övervakning.](./media/azure-sql/azure-sql-sol-overview.png)

Om du väljer någon av panelerna öppnas en detaljerad rapport i det aktuella perspektivet. När perspektivet är markerat öppnas rapporten för att öka detalj nivån.

![Skärm bild som visar den detaljerade rapporten i det aktuella perspektivet.](./media/azure-sql/azure-sql-sol-metrics.png)

Varje perspektiv i den här vyn innehåller sammanfattningar i prenumerationen, servern, elastisk pool och databas nivåer. Dessutom visar varje perspektiv ett perspektiv som är särskilt för rapporten till höger. Genom att välja prenumeration, Server, pool eller databas i listan fortsätter du att öka detalj nivån.

### <a name="sql-managed-instance-view"></a>Vy över SQL-hanterad instans

När Azure SQL-analys panelen för databaserna är markerad visas övervaknings instrument panelen.

![Översikt över Azure SQL-analys](./media/azure-sql/azure-sql-sol-overview-mi.png)

Om du väljer någon av panelerna öppnas en detaljerad rapport i det aktuella perspektivet. När perspektivet är markerat öppnas rapporten för att öka detalj nivån.

Om du väljer vyn SQL-hanterad instans visas information om instansen, instans databaser och telemetri för de frågor som körs över den hanterade instansen.

![Azure SQL-analys tids gränser](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Intelligent Insights rapport

Azure SQL Database [intelligent Insights](../../azure-sql/database/intelligent-insights-overview.md) kan du veta vad som händer med prestanda för alla Azure SQL-databaser. Alla Intelligent Insights som samlas in kan visualiseras och nås via insikter perspektivet.

![Azure SQL-analys insikter](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Elastiska pooler och databas rapporter

Både elastiska pooler och databaser har sina egna specifika rapporter som visar alla data som har samlats in för resursen under den angivna tiden.

![Azure SQL-analys databas](./media/azure-sql/azure-sql-sol-database.png)

![Elastisk Azure SQL-pool](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Fråga rapporter

Genom frågans varaktighet och frågan väntar dig perspektiv, kan du korrelera prestandan för alla frågor via fråge rapporten. I den här rapporten jämförs frågans prestanda i olika databaser och det är enkelt att hitta databaser som utför den valda frågan och de som är långsamma.

![Azure SQL-analys frågor](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Behörigheter

För att kunna använda Azure SQL-analys måste användarna beviljas en lägsta behörighet för rollen läsare i Azure. Den här rollen tillåter dock inte användare att se frågetexten eller utföra automatiska justerings åtgärder. Fler tillåtna roller i Azure som gör det möjligt att använda Azure SQL-analys i hela utsträckning är ägare, deltagare, SQL DB-deltagare eller SQL Server deltagare. Du kanske också vill överväga att skapa en anpassad roll i portalen med vissa behörigheter som krävs endast för att använda Azure SQL-analys och utan åtkomst till hantering av andra resurser.

### <a name="creating-a-custom-role-in-portal"></a>Skapa en anpassad roll i portalen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Att känna till att vissa organisationer tillämpar strikta behörighets kontroller i Azure, hittar du följande PowerShell-skript som aktiverar skapandet av en anpassad roll "SQL Analytics Monitoring-operatör" i Azure Portal med den lägsta Läs-och Skriv behörighet som krävs för att använda Azure SQL-analys i hela sin omfattning.

Ersätt "{SubscriptionId}" i skriptet nedan med ditt Azure-prenumerations-ID och kör skriptet som är inloggat som en ägare eller deltagar roll i Azure.

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

När den nya rollen har skapats tilldelar du rollen till varje användare som du behöver för att ge anpassade behörigheter att använda Azure SQL-analys.

## <a name="analyze-data-and-create-alerts"></a>Analysera data och skapa aviseringar

Data analys i Azure SQL-analys baseras på [Log Analytics språk](../logs/get-started-queries.md) för din anpassade fråga och rapportering. Hitta en beskrivning av tillgängliga data som samlats in från databas resursen för anpassade frågor i [mått och loggar tillgängliga](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#metrics-and-logs-available).

Automatiserad avisering i Azure SQL-analys baseras på att skriva en Log Analytics fråga som utlöser en avisering när ett villkor uppfylls. Det finns flera exempel på hur du Log Analytics frågor om vilka aviseringar som kan konfigureras i Azure SQL-analys.

### <a name="creating-alerts-for-azure-sql-database"></a>Skapa aviseringar för Azure SQL Database

Du kan enkelt [skapa aviseringar](../alerts/alerts-metric.md) med de data som kommer från Azure SQL Database-resurser. Här följer några användbara [logg frågor](../logs/log-query-overview.md) som du kan använda med en logg avisering:

#### <a name="high-cpu"></a>Hög CPU

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - Före kravet på att ställa in den här aviseringen är att övervakade databaser med grundläggande mått för att Azure SQL-analys.
> - Ersätt MetricName-värdet cpu_percent med dtu_consumption_percent för att få höga DTU-resultat i stället.

#### <a name="high-cpu-on-elastic-pools"></a>Hög CPU på elastiska pooler

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - Före kravet på att ställa in den här aviseringen är att övervakade databaser med grundläggande mått för att Azure SQL-analys.
> - Ersätt MetricName-värdet cpu_percent med dtu_consumption_percent för att få höga DTU-resultat i stället.

#### <a name="storage-in-average-above-95-in-the-last-1-hr"></a>Lagring i genomsnitt över 95% under de senaste 1 timmena

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
>
> - Före kravet på att ställa in den här aviseringen är att övervakade databaser med grundläggande mått för att Azure SQL-analys.
> - Den här frågan kräver att en varnings regel har kon figurer ATS för att utlösa en avisering när det finns resultat (> 0 resultat) från frågan, vilket anger att villkoret finns på vissa databaser. Utdata är en lista över databas resurser som är ovanför storage_threshold inom den time_range som definierats.
> - Utdata är en lista över databas resurser som är ovanför storage_threshold inom den time_range som definierats.

#### <a name="alert-on-intelligent-insights"></a>Avisering om intelligenta insikter

> [!IMPORTANT]
> Om en databas fungerar bra och ingen Intelligent Insights har skapats, kommer den här frågan att Miss lyckas med ett fel meddelande: det gick inte att lösa det skalära uttrycket med namnet rootCauseAnalysis_s. Det här beteendet förväntas för alla fall där det inte finns några intelligenta insikter för databasen.

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
>
> - Före kravet på att ställa in den här aviseringen är att den övervakade databaserna för SQLInsights-diagnostik i databasen Azure SQL-analys.
> - Den här frågan kräver att en varnings regel konfigureras att köras med samma frekvens som alert_run_interval för att undvika dubbla resultat. Regeln ska ställas in för att starta aviseringen när det finns resultat (> 0 resultat) från frågan.
> - Anpassa alert_run_interval för att ange tidsintervallet för att kontrol lera om tillståndet har inträffat på databaser som har kon figurer ATS att strömma SQLInsights-loggen till Azure SQL-analys.
> - Anpassa insights_string för att avbilda utdata från analys texten i rotor Saks analys. Det här är samma text som visas i användar gränssnittet för Azure SQL-analys som du kan använda från befintliga insikter. Du kan också använda frågan nedan om du vill se texten för alla insikter som genererats i din prenumeration. Använd utdata från frågan för att skörda de distinkta strängarna för att ställa in aviseringar för insikter.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-sql-managed-instance"></a>Skapa aviseringar för SQL-hanterad instans

#### <a name="storage-is-above-90"></a>Lagring är över 90%

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
>
> - Krav för att ställa in den här aviseringen är att den övervakade hanterade instansen har strömningen av ResourceUsageStats logg aktive rad till Azure SQL-analys.
> - Den här frågan kräver att en varnings regel har kon figurer ATS för att utlösa en avisering när det finns resultat (> 0 resultat) från frågan, vilket anger att villkoret finns på den hanterade instansen. Utdatan är lagrings procent användning på den hanterade instansen.

#### <a name="cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Genomsnittlig förbrukning för CPU är över 95% under de senaste 1 timme

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
>
> - Krav för att ställa in den här aviseringen är att den övervakade hanterade instansen har strömningen av ResourceUsageStats logg aktive rad till Azure SQL-analys.
> - Den här frågan kräver att en varnings regel har kon figurer ATS för att utlösa en avisering när det finns resultat (> 0 resultat) från frågan, vilket anger att villkoret finns på den hanterade instansen. Utdatan är genomsnitts förbrukningen i procent för processor användning i definierad period på den hanterade instansen.

### <a name="pricing"></a>Priser

Även om Azure SQL-analys är kostnads fritt att använda, kan förbrukningen av diagnostik över de kostnads fria enheterna för inmatningen av data som tilldelas varje månad användas, se [Log Analytics prissättning](https://azure.microsoft.com/pricing/details/monitor). De kostnads fria enheterna av data inmatning möjliggör kostnads fri övervakning av flera databaser varje månad. Fler aktiva databaser med tyngre arbets belastningar inhämtar mer data jämfört med inaktiva databaser. Du kan enkelt övervaka förbrukningen av data inmatning i Azure SQL-analys genom att välja OMS-arbetsyta på navigerings menyn i Azure SQL-analys och sedan välja användning och beräknade kostnader.

## <a name="next-steps"></a>Nästa steg

- Använd [logg frågor](../logs/log-query-overview.md) i Azure Monitor om du vill visa detaljerade Azure SQL-data.
- [Skapa egna instrument paneler](../visualize/tutorial-logs-dashboards.md) som visar Azure SQL-data.
- [Skapa aviseringar](../alerts/alerts-overview.md) när vissa Azure SQL-händelser inträffar.

