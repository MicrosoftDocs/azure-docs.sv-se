---
title: Övervakning Azure Files | Microsoft Docs
description: Lär dig hur du övervakar prestanda och tillgänglighet för Azure Files. Övervaka Azure Files data, lär dig mer om konfiguration och analysera mått- och loggdata.
author: normesta
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 3/02/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 620ee3bc5978da4b274aed9a412679ae0835f0b9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759835"
---
# <a name="monitoring-azure-files"></a>Övervakning Azure Files

När du har kritiska program och affärsprocesser som förlitar sig på Azure-resurser vill du övervaka resursernas tillgänglighet, prestanda och drift. Den här artikeln beskriver övervakningsdata som genereras av Azure Files och hur du kan använda funktionerna i Azure Monitor för att analysera aviseringar om dessa data.

## <a name="monitor-overview"></a>Översikt över övervakare

På **sidan** Översikt i Azure Portal för varje Azure Files resurs finns en kort vy över resursanvändningen, till exempel begäranden och fakturering per timme. Den här informationen är användbar, men endast en liten mängd övervakningsdata är tillgängliga. Vissa av dessa data samlas in automatiskt och är tillgängliga för analys så snart du skapar resursen. Du kan aktivera ytterligare typer av datainsamling med viss konfiguration.

## <a name="what-is-azure-monitor"></a>Vad är Azure Monitor?
Azure Files skapar övervakningsdata med hjälp [av Azure Monitor](../../azure-monitor/overview.md), som är en fullständig stackövervakningstjänst i Azure. Azure Monitor innehåller en komplett uppsättning funktioner för att övervaka dina Azure-resurser och resurser i andra moln och lokalt. 

Börja med artikeln [Övervaka Azure-resurser Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md), som beskriver följande:

- Vad är Azure Monitor?
- Kostnader som är kopplade till övervakning
- Övervakningsdata som samlas in i Azure
- Konfigurera datainsamling
- Standardverktyg i Azure för analys och avisering om övervakningsdata

Följande avsnitt bygger på den här artikeln genom att beskriva de specifika data som samlas in från Azure Files. Exempel visar hur du konfigurerar datainsamling och analyserar dessa data med Azure-verktyg.

## <a name="monitoring-data"></a>Övervaka data

Azure Files samlar in samma typer av övervakningsdata som andra Azure-resurser, som beskrivs i [Övervaka data från Azure-resurser.](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) 

Se [referensen för övervakningsdata för Azure](storage-files-monitoring-reference.md) File för detaljerad information om mått och loggmått som skapats av Azure Files.

Mått och loggar i Azure Monitor endast stöd Azure Resource Manager lagringskonton. Azure Monitor stöder inte klassiska lagringskonton. Om du vill använda mått eller loggar på ett klassiskt lagringskonto måste du migrera till ett Azure Resource Manager lagringskonto. Se [Migrera till Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md).

## <a name="collection-and-routing"></a>Insamling och routning

Plattformsmått och aktivitetsloggen samlas in automatiskt, men kan dirigeras till andra platser med hjälp av en diagnostikinställning. 

Om du vill samla in resursloggar måste du skapa en diagnostikinställning. När du skapar inställningen väljer du **fil som** den typ av lagring som du vill aktivera loggar för. Ange sedan någon av följande kategorier av åtgärder som du vill samla in loggar för. 

| Kategori | Beskrivning |
|:---|:---|
| StorageRead | Läsåtgärder för objekt. |
| StorageWrite | Skrivåtgärder för objekt. |
| StorageTa bort | Ta bort åtgärder för objekt. |

Information om hur du hämtar listan över SMB- och REST-åtgärder som loggas finns i Storage [logged operations and status messages](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) and Azure Files monitoring data [reference](storage-files-monitoring-reference.md).

## <a name="creating-a-diagnostic-setting"></a>Skapa en diagnostikinställning

Du kan skapa en diagnostikinställning med hjälp av Azure Portal, PowerShell, Azure CLI eller en Azure Resource Manager mall.

> [!NOTE]
> Azure Storage loggar i Azure Monitor är i offentlig förhandsversion och är tillgänglig för förhandsversionstestning i alla offentliga molnregioner. Den här förhandsversionen aktiverar loggar för blobar (som innehåller Azure Data Lake Storage Gen2), filer, köer och tabeller. Den här funktionen är tillgänglig för alla lagringskonton som skapas med Azure Resource Manager distributionsmodellen. Se [Översikt över lagringskonto.](../common/storage-account-overview.md)

Allmänna riktlinjer finns i Skapa [diagnostikinställning för att samla in plattformsloggar och mått i Azure.](../../azure-monitor/essentials/diagnostic-settings.md)

### <a name="azure-portal"></a>[Azure-portalen](#tab/azure-portal)

1. Logga in på Azure-portalen.

2. Navigera till ditt lagringskonto.

3. I avsnittet **Övervakning** klickar du på **Diagnostikinställningar (förhandsversion).**

   > [!div class="mx-imgBorder"]
   > ![portal – Diagnostikloggar](media/storage-files-monitoring/diagnostic-logs-settings-pane.png)   

4. Välj **fil** som den typ av lagring som du vill aktivera loggar för.

5. Klicka på **Lägg till diagnostikinställning**.

   > [!div class="mx-imgBorder"]
   > ![portal – Resursloggar – lägg till diagnostikinställning](media/storage-files-monitoring/diagnostic-logs-settings-pane-2.png)

   Sidan **Diagnostikinställningar** visas.

   > [!div class="mx-imgBorder"]
   > ![Sidan Resursloggar](media/storage-files-monitoring/diagnostic-logs-page.png)

6. I fältet **Namn** på sidan anger du ett namn för den här inställningen för resursloggen. Välj sedan vilka åtgärder du vill logga (läs-, skriv- och borttagningsåtgärder) och var du vill att loggarna ska skickas.

#### <a name="archive-logs-to-a-storage-account"></a>Arkivera loggar till ett lagringskonto

Om du väljer att arkivera loggarna till ett lagringskonto betalar du för mängden loggar som skickas till lagringskontot. Specifik prissättning finns i avsnittet **Plattformsloggar** på sidan [Azure Monitor prissättning.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

1. Markera kryssrutan **Arkivera till ett lagringskonto** och klicka sedan på **knappen** Konfigurera.

   > [!div class="mx-imgBorder"]   
   > ![Arkivlagring på sidan Diagnostikinställningar](media/storage-files-monitoring/diagnostic-logs-settings-pane-archive-storage.png)

2. I **listrutan Lagringskonto** väljer du det lagringskonto som du vill arkivera loggarna till, klickar på **knappen OK** och klickar sedan på **knappen** Spara.

   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > Innan du väljer ett lagringskonto som exportmål kan du se [Arkivera Azure-resursloggar](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) för att förstå kraven för lagringskontot.

#### <a name="stream-logs-to-azure-event-hubs"></a>Strömma loggar till Azure Event Hubs

Om du väljer att strömma dina loggar till en händelsehubb betalar du för mängden loggar som skickas till händelsehubben. Specifika priser finns i avsnittet **Plattformsloggar** på [sidan Azure Monitor prissättning.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

1. Markera kryssrutan **Strömma till en händelsehubb** och klicka sedan på **knappen** Konfigurera.

2. I fönstret **Välj en händelsehubb** väljer du namnområde, namn och principnamn för den händelsehubb som du vill strömma loggarna till. 

   > [!div class="mx-imgBorder"]
   > ![Händelsehubb för sidan Diagnostikinställningar](media/storage-files-monitoring/diagnostic-logs-settings-pane-event-hub.png)

3. Klicka på **OK** och sedan på **knappen** Spara.

#### <a name="send-logs-to-azure-log-analytics"></a>Skicka loggar till Azure Log Analytics

1. Markera kryssrutan **Skicka till Log Analytics,** välj en log analytics-arbetsyta och klicka sedan på och klicka på **knappen** Spara.

   > [!div class="mx-imgBorder"]   
   > ![Logganalys för diagnostikinställningar](media/storage-files-monitoring/diagnostic-logs-settings-pane-log-analytics.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Öppna ett Windows PowerShell kommandofönster och logga in på din Azure-prenumeration med hjälp av `Connect-AzAccount` kommandot . Följ sedan anvisningarna på skärmen.

   ```powershell
   Connect-AzAccount
   ```

2. Ange din aktiva prenumeration till prenumerationen på det lagringskonto som du vill aktivera loggning för.

   ```powershell
   Set-AzContext -SubscriptionId <subscription-id>
   ```

#### <a name="archive-logs-to-a-storage-account"></a>Arkivera loggar till ett lagringskonto

Om du väljer att arkivera loggarna till ett lagringskonto betalar du för mängden loggar som skickas till lagringskontot. Specifika priser finns i avsnittet **Plattformsloggar** på [sidan Azure Monitor prissättning.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Aktivera loggar med hjälp av [PowerShell-cmdleten Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) tillsammans med `StorageAccountId` parametern .

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -StorageAccountId <storage-account-resource-id> -Enabled $true -Category <operations-to-log> 
```

Ersätt `<storage-service-resource--id>` platshållaren i det här kodfragmentet med resurs-ID för Azure File-tjänsten. Du hittar resurs-ID:t i Azure Portal genom att öppna **sidan Egenskaper** för ditt lagringskonto.

Du kan använda `StorageRead` , och för värdet för `StorageWrite` `StorageDelete` **parametern** Kategori.

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Här är ett exempel:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

En beskrivning av varje parameter finns i Arkivera [Azure-resursloggar via Azure PowerShell](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Strömma loggar till en händelsehubb

Om du väljer att strömma loggarna till en händelsehubb betalar du för mängden loggar som skickas till händelsehubben. Specifik prissättning finns i avsnittet **Plattformsloggar** på sidan [Azure Monitor prissättning.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Aktivera loggar med hjälp av [PowerShell-cmdleten Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) med `EventHubAuthorizationRuleId` parametern .

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Här är ett exempel:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

En beskrivning av varje parameter finns i Stream [Data to Event Hubs via PowerShell-cmdlets](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Skicka loggar till Log Analytics

Aktivera loggar med hjälp av [PowerShell-cmdleten Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) med `WorkspaceId` parametern .

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Här är ett exempel:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

Mer information finns i Strömma [Azure-resursloggar till Log Analytics-arbetsyta i Azure Monitor](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Öppna först Azure Cloud Shell [,](../../cloud-shell/overview.md)eller om du [](/cli/azure/install-azure-cli) har installerat Azure CLI lokalt öppnar du ett kommandokonsolprogram, till exempel Windows PowerShell.

2. Om din identitet är associerad med mer än en prenumeration anger du den aktiva prenumerationen till prenumerationen på det lagringskonto som du vill aktivera loggar för.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Ersätt `<subscription-id>` platshållarvärdet med ID:t för din prenumeration.

#### <a name="archive-logs-to-a-storage-account"></a>Arkivera loggar till ett lagringskonto

Om du väljer att arkivera loggarna till ett lagringskonto betalar du för mängden loggar som skickas till lagringskontot. Specifik prissättning finns i avsnittet **Plattformsloggar** på sidan [Azure Monitor prissättning.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Aktivera loggar med kommandot [az monitor diagnostic-settings create.](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create)

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true}]'
```

Ersätt `<storage-service-resource--id>` platshållaren i det här kodfragmentet med bloblagringstjänsten för resurs-ID. Du hittar resurs-ID:t i Azure Portal genom att öppna **sidan Egenskaper** för ditt lagringskonto.

Du kan använda `StorageRead` , och för värdet för `StorageWrite` `StorageDelete` **kategoriparametern.**

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Här är ett exempel:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true}]'`

En beskrivning av varje parameter finns i Loggar [för arkivresurser via Azure CLI.](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

#### <a name="stream-logs-to-an-event-hub"></a>Strömma loggar till en händelsehubb

Om du väljer att strömma dina loggar till en händelsehubb betalar du för mängden loggar som skickas till händelsehubben. Specifika priser finns i avsnittet **Plattformsloggar** på [sidan Azure Monitor prissättning.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Aktivera loggar med hjälp av [kommandot az monitor diagnostic-settings create.](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create)

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Här är ett exempel:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

En beskrivning av varje parameter finns i [Stream-data som ska Event Hubs via Azure CLI.](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

#### <a name="send-logs-to-log-analytics"></a>Skicka loggar till Log Analytics

Aktivera loggar med hjälp av [kommandot az monitor diagnostic-settings create.](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create)

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Här är ett exempel:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 Mer information finns i Strömma [Azure-resursloggar till Log Analytics-arbetsytan i Azure Monitor](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

### <a name="template"></a>[Mall](#tab/template)

Information om hur Azure Resource Manager en mall som skapar en diagnostikinställning finns i [Diagnostikinställning för Azure Storage](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage).

---

## <a name="analyzing-metrics"></a>Analysera mått

Du kan analysera mått för att Azure Storage mått från andra Azure-tjänster med hjälp av Metrics Explorer. Öppna Metrics Explorer genom att **välja Mått** på **Azure Monitor** menyn. Mer information om hur du använder det här verktyget [finns i Komma igång med Azure Metrics Explorer](../../azure-monitor/essentials/metrics-getting-started.md). 

För mått som stöder dimensioner kan du filtrera måttet med önskat dimensionsvärde.  En fullständig lista över de dimensioner Azure Storage stöder finns i [Måttdimensioner.](storage-files-monitoring-reference.md#metrics-dimensions) Mått för Azure Files finns i följande namnområden: 

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/fileServices

En lista över alla mått Azure Monitor stöds, vilket omfattar Azure Files finns [i Azure Monitor mått som stöds.](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices)

### <a name="accessing-metrics"></a>Komma åt mått

> [!TIP]
> Om du vill visa Azure CLI- eller .NET-exempel väljer du motsvarande flikar som visas här.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Visa en lista med måttdefinitionen

Du kan lista måttdefinitionen för ditt lagringskonto eller Azure Files tjänsten. Använd [cmdleten Get-AzMetricDefinition.](/powershell/module/az.monitor/get-azmetricdefinition)

I det här exemplet ersätter du platshållaren med resurs-ID:t för hela lagringskontot eller `<resource-ID>` resurs-ID:t för Azure Files tjänsten.  Du hittar dessa resurs-ID:er på **egenskapssidorna** för ditt lagringskonto i Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Läsa måttvärden

Du kan läsa måttvärden på kontonivå för ditt lagringskonto eller Azure Files tjänsten. Använd [cmdleten Get-AzMetric.](/powershell/module/Az.Monitor/Get-AzMetric)

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Visa en lista över måttdefinitionen på kontonivå

Du kan lista måttdefinitionen för ditt lagringskonto eller Azure Files tjänsten. Använd kommandot [az monitor metrics list-definitions.](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions)
 
I det här exemplet ersätter du platshållaren med resurs-ID:t för hela lagringskontot eller `<resource-ID>` resurs-ID:t för Azure Files tjänsten. Du hittar dessa resurs-ID:er på **egenskapssidorna** för ditt lagringskonto i Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Läsa måttvärden på kontonivå

Du kan läsa måttvärdena för ditt lagringskonto eller Azure Files tjänsten. Använd kommandot [az monitor metrics list.](/cli/azure/monitor/metrics#az_monitor_metrics_list)

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/azure-portal)

Azure Monitor tillhandahåller [.NET SDK för att](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) läsa måttdefinition och -värden. Exempelkoden [visar](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) hur du använder SDK med olika parametrar. Du måste använda `0.18.0-preview` eller en senare version för lagringsmått.
 
I de här exemplen `<resource-ID>` ersätter du platshållaren med resurs-ID:t för hela lagringskontot eller Azure Files tjänsten. Du hittar dessa resurs-ID:er på **egenskapssidorna** för ditt lagringskonto i Azure Portal.

Ersätt `<subscription-ID>` variabeln med ID:t för din prenumeration. Anvisningar för hur du hämtar värden för , och finns i Använda portalen för att skapa ett Azure AD-program och tjänstens huvudnamn `<tenant-ID>` som kan komma åt `<application-ID>` `<AccessKey>` [resurser.](../../active-directory/develop/howto-create-service-principal-portal.md) 

#### <a name="list-the-account-level-metric-definition"></a>Visa en lista över måttdefinitionen på kontonivå

I följande exempel visas hur du listar en måttdefinition på kontonivå:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

#### <a name="reading-account-level-metric-values"></a>Läsa måttvärden på kontonivå

I följande exempel visas hur du läser `UsedCapacity` data på kontonivå:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            // Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

#### <a name="reading-multidimensional-metric-values"></a>Läsa flerdimensionella måttvärden

För flerdimensionella mått måste du definiera metadatafilter om du vill läsa måttdata för specifika dimensionsvärden.

I följande exempel visas hur du läser måttdata på måttet som stöder flerdimensionella:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for Azure Files
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

# <a name="template"></a>[Mall](#tab/template)

Ej tillämpligt.

---

## <a name="analyzing-logs"></a>Analysera loggar

Du kan komma åt resursloggar antingen som en blob i ett lagringskonto, som händelsedata eller via Log Analytic-frågor.

Information om hur du hämtar listan över SMB- och REST-åtgärder som loggas finns i Storage [logged operations and status messages](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) and Azure Files monitoring data [reference](storage-files-monitoring-reference.md).

> [!NOTE]
> Azure Storage loggar i Azure Monitor är i offentlig förhandsversion och är tillgänglig för förhandsversionstestning i alla offentliga molnregioner. I den här förhandsversionen kan du använda loggar för blobar (som innehåller Azure Data Lake Storage Gen2), filer, köer, tabeller, Premium Storage-konton i generell användning v1 och v2-lagringskonton för generell användning. Klassiska lagringskonton stöds inte.

Loggposter skapas bara om det finns begäranden som görs mot tjänstslutpunkten. Om ett lagringskonto till exempel har aktivitet i filslutpunkten, men inte i dess tabell- eller köslutpunkter, skapas endast loggar som hör till Azure File-tjänsten. Azure Storage innehåller detaljerad information om lyckade och misslyckade begäranden till en lagringstjänst. Den här informationen kan användas för att övervaka enskilda begäranden och för att diagnostisera problem med en lagringstjänst. Begäranden loggas efter bästa möjliga ansträngning.

### <a name="log-authenticated-requests"></a>Logga autentiserade begäranden

 Följande typer av autentiserade begäranden loggas:

- Lyckade begäranden
- Misslyckade begäranden, inklusive timeout, begränsning, nätverk, auktorisering och andra fel
- Begäranden som använder Kerberos, NTLM eller signatur för delad åtkomst (SAS), inklusive misslyckade och lyckade begäranden
- Begäranden till analysdata (klassiska loggdata i **$logs** och klassiska måttdata i **$metric tabellerna)**

Begäranden som görs Azure Files själva tjänsten, till exempel loggskapande eller borttagning, loggas inte. En fullständig lista över SMB- och REST-begäranden som loggas finns i Storage [logged operations and status messages](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) and Azure Files monitoring data [reference](storage-files-monitoring-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Komma åt loggar i ett lagringskonto

Loggar visas som blobar som lagras i en container i mållagringskontot. Data samlas in och lagras i en enda blob som en radavgränsad JSON-nyttolast. Namnet på bloben följer den här namngivningskonventionen:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/fileServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Här är ett exempel:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Komma åt loggar i en händelsehubb

Loggar som skickas till en händelsehubb lagras inte som en fil, men du kan kontrollera att händelsehubben tog emot logginformationen. I Azure Portal du till din händelsehubb och kontrollerar att antalet **inkommande meddelanden** är större än noll. 

![Granskningsloggar](media/storage-files-monitoring/event-hub-log.png)

Du kan komma åt och läsa loggdata som skickas till din händelsehubb med hjälp av säkerhetsinformation, händelsehantering och övervakningsverktyg. Mer information finns i [Vad kan jag göra med övervakningsdata som skickas till min händelsehubb?](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Komma åt loggar på en Log Analytics-arbetsyta

Du kan komma åt loggar som skickas till en Log Analytics-arbetsyta med hjälp Azure Monitor loggfrågor. Data lagras i **tabellen StorageFileLogs.** 

Mer information finns i [Log Analytics-självstudie.](../../azure-monitor/logs/log-analytics-tutorial.md)

#### <a name="sample-kusto-queries"></a>Exempel på Kusto-frågor

Här följer några frågor som du kan ange i **loggsökningsfältet** som hjälper dig att övervaka Azure Files. De här frågorna fungerar med det [nya språket](../../azure-monitor/logs/log-query-overview.md).

> [!IMPORTANT]
> När du väljer **Loggar** på resursgruppmenyn för lagringskontot öppnas Log Analytics med frågeomfånget inställt på den aktuella resursgruppen. Det innebär att loggfrågor endast innehåller data från den resursgruppen. Om du vill köra en fråga som innehåller data från andra  resurser eller data från andra Azure-tjänster väljer du **Loggar Azure Monitor** menyn. Mer information finns i Log query scope and time range in Azure Monitor Log Analytics (Loggfrågeomfång och tidsperiod [i Log Analytics).](../../azure-monitor/logs/scope.md)

Använd de här frågorna för att övervaka dina Azure-filresurser:

- Visa SMB-fel under den senaste veckan

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) and StatusCode contains "-"
| sort by StatusCode
```
- Skapa ett cirkeldiagram över SMB-åtgärder under den senaste veckan

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

- Visa REST-fel under den senaste veckan

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) and StatusText !contains "Success"
| sort by StatusText asc
```

- Skapa ett cirkeldiagram över REST-åtgärder under den senaste veckan

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

Om du vill visa listan över kolumnnamn och beskrivningar för Azure Files, se [StorageFileLogs](/azure/azure-monitor/reference/tables/storagefilelogs).

Mer information om hur du skriver frågor finns i Log [Analytics-självstudien](../../azure-monitor/logs/log-analytics-tutorial.md).

## <a name="alerts"></a>Aviseringar

Azure Monitor meddela dig proaktivt när viktiga villkor finns i dina övervakningsdata. De gör att du kan identifiera och åtgärda problem i systemet innan kunderna märker av dem. Du kan ställa in aviseringar [för mått,](../../azure-monitor/alerts/alerts-metric-overview.md) [loggar](../../azure-monitor/alerts/alerts-unified-log.md)och [aktivitetsloggen](../../azure-monitor/alerts/activity-log-alerts.md). 

I följande tabell visas några exempelscenarier för övervakning och rätt mått som ska användas för aviseringen:

| Scenario | Mått som ska användas för avisering |
|-|-|
| Filresursen begränsas. | Mått: Transaktioner<br>Dimensionsnamn: Svarstyp <br>Dimensionsnamn: Filresurs (endast premiumfilresurs) |
| Filresursstorleken är 80 % av kapaciteten. | Mått: Filkapacitet<br>Dimensionsnamn: Filresurs (endast premiumfilresurs) |
| Utgående filresurs har överskridit 500 GiB på en dag. | Mått: Utgående<br>Dimensionsnamn: Filresurs (endast premiumfilresurs) |

### <a name="how-to-create-alerts-for-azure-files"></a>Så här skapar du aviseringar för Azure Files

1. Gå till ditt **lagringskonto** i **Azure Portal**. 

2. Klicka **på Aviseringar** och sedan på + Ny **aviseringsregel.**

3. Klicka **på Redigera resurs,** välj **Filresurstyp och** klicka sedan på **Klar.** 

4. Klicka **på Lägg** till villkor och ange följande information för aviseringen: 

    - **Mått**
    - **Dimensionsnamn**
    - **Aviseringslogik**

5. Klicka **på Lägg till åtgärdsgrupper** och lägg till en åtgärdsgrupp (e-post, SMS osv.) till aviseringen antingen genom att välja en befintlig åtgärdsgrupp eller skapa en ny åtgärdsgrupp.

6. Fyll i **aviseringsinformationen** som **Aviseringsregelnamn,** **Beskrivning** och **Allvarlighetsgrad**.

7. Klicka **på Skapa aviseringsregel** för att skapa aviseringen.

> [!NOTE]  
> Om du skapar en avisering och den är för högljudd justerar du tröskelvärdet och aviseringslogiken.

### <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Så här skapar du en avisering om en filresurs begränsas

1. Gå till ditt **lagringskonto** i **Azure Portal**.
2. I avsnittet **Övervakning** klickar du på **Aviseringar** och sedan på **+ Ny aviseringsregel.**
3. Klicka **på Redigera** resurs, välj **Filresurstyp för** lagringskontot och klicka sedan på **Klar.** Om lagringskontots namn till exempel `contoso` är väljer du resursen `contoso/file` .
4. Klicka på **Lägg till villkor** för att lägga till ett villkor.
5. Du ser en lista över signaler som stöds för lagringskontot och väljer **måttet** Transaktioner.
6. På **bladet Konfigurera signallogik** klickar du **på listrutan Dimensionsnamn** och väljer **Svarstyp.**
7. Klicka på **listrutan** Dimensionsvärden och välj lämpliga svarstyper för filresursen.

    För standardfilresurser väljer du följande svarstyper:

    - SuccessWithShareIopsThrottling
    - SuccessWithThrottling
    - ClientShareIopsThrottlingError

    För premiumfilresurser väljer du följande svarstyper:

    - SuccessWithShareEgressThrottling
    - SuccessWithShareIngressThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

   > [!NOTE]
   > Om svarstyperna inte visas i **listrutan Dimensionsvärden** innebär det att resursen inte har begränsats. Om du vill lägga till  dimensionsvärdena bredvid listrutan Dimensionsvärden väljer du Lägg till anpassat **värde,** anger respone-typen (till exempel **SuccessWithThrottling),** väljer **OK** och upprepar sedan dessa steg för att lägga till alla tillämpliga svarstyper för filresursen.

8. För **premiumfilresurser** klickar du **på listrutan Dimensionsnamn** och väljer **Filresurs.** För **standardfilresurser går** du vidare till **steg #10**.

   > [!NOTE]
   > Om filresursen är en standardfilresurs visar inte filresursdimensionen filresursen eftersom mått per resurs inte är tillgängliga för standardfilresurser.  Begränsningsaviseringar för standardfilresurser utlöses om någon filresurs i lagringskontot begränsas och aviseringen inte identifierar vilken filresurs som har begränsats. Eftersom mått per resurs inte är tillgängliga för standardfilresurser rekommenderar vi att du har en filresurs per lagringskonto.

9. Klicka på **listrutan** Dimensionsvärden och välj de filresurs(er) som du vill avisering om.
10. Definiera **aviseringsparametrarna** (tröskelvärde, operator, sammansättningsdeklaritet och utvärderingsfrekvens) och klicka på **Klar.**

    > [!TIP]
    > Om du använder ett statiskt tröskelvärde kan måttdiagrammet hjälpa dig att fastställa ett rimligt tröskelvärde om filresursen för närvarande begränsas. Om du använder ett dynamiskt tröskelvärde visar måttdiagrammet beräknade tröskelvärden baserat på senaste data.

11. Klicka **på Lägg till åtgärdsgrupper** för att lägga till en åtgärdsgrupp (e-post, SMS osv.) till aviseringen antingen genom att välja en befintlig åtgärdsgrupp eller skapa en ny åtgärdsgrupp. 
12. Fyll i **aviseringsinformationen** som **Aviseringsregelnamn,** **Beskrivning** och **Allvarlighetsgrad**.
13. Klicka **på Skapa aviseringsregel** för att skapa aviseringen.

### <a name="how-to-create-an-alert-if-the-azure-file-share-size-is-80-of-capacity"></a>Så här skapar du en avisering om Storleken på Azure-filresursen är 80 % av kapaciteten

1. Gå till ditt **lagringskonto** i **Azure Portal**.
2. I avsnittet **Övervakning** klickar du på **Aviseringar och** sedan på + **Ny aviseringsregel.**
3. Klicka **på Redigera resurs,** välj **Filresurstyp för** lagringskontot och klicka sedan på **Klar.** Om lagringskontots namn till exempel `contoso` är väljer du resursen `contoso/file` .
4. Klicka på **Lägg till villkor** för att lägga till ett villkor.
5. Du ser en lista över signaler som stöds för lagringskontot och väljer **måttet Filkapacitet.**
6. För **premiumfilresurser** klickar du **på listrutan Dimensionsnamn** och väljer **Filresurs.** För **standardfilresurser** går du vidare **till steg #8**.

   > [!NOTE]
   > Om filresursen är en standardfilresurs visar inte filresursdimensionen filresursen eftersom mått per resurs inte är tillgängliga för standardfilresurser.  Aviseringar för standardfilresurser baseras på alla filresurser i lagringskontot. Eftersom mått per resurs inte är tillgängliga för standardfilresurser rekommenderar vi att du har en filresurs per lagringskonto.

7. Klicka på **listrutan** Dimensionsvärden och välj de filresurs(er) som du vill avisering om.
8. Ange **tröskelvärdet i** byte. Om filresursens storlek till exempel är 100 TiB och du vill få en avisering när filresursstorleken är 80 % av kapaciteten är tröskelvärdet i byte 87960930222080.
9. Definiera resten av **aviseringsparametrarna** (sammansättningskornighet och utvärderingsfrekvens) och klicka på **Klar.**
10. Klicka **på Lägg till åtgärdsgrupper** för att lägga till en åtgärdsgrupp (e-post, SMS osv.) i aviseringen genom att välja en befintlig åtgärdsgrupp eller skapa en ny åtgärdsgrupp. 
11. Fyll i **aviseringsinformationen** som **Aviseringsregelnamn,** **Beskrivning** och **Allvarlighetsgrad**.
12. Klicka **på Skapa aviseringsregel** för att skapa aviseringen.

### <a name="how-to-create-an-alert-if-the-azure-file-share-egress-has-exceeded-500-gib-in-a-day"></a>Så här skapar du en avisering om utgående Azure-filresursen har överskridit 500 GiB under en dag

1. Gå till ditt **lagringskonto** i **Azure Portal**.
2. I avsnittet Övervakning klickar du på **Aviseringar och** sedan på + **Ny aviseringsregel**.
3. Klicka **på Redigera** resurs, välj **Filresurstyp för** lagringskontot och klicka sedan på **Klar.** Om lagringskontonamnet till exempel är contoso väljer du resursen contoso/file.
4. Klicka på **Lägg till villkor** för att lägga till ett villkor.
5. Du ser en lista över signaler som stöds för lagringskontot och väljer **måttet Egress (Utgående).**
6. För **premiumfilresurser** klickar du **på listrutan Dimensionsnamn** och väljer **Filresurs.** För **standardfilresurser går** du vidare till **steg #8**.

   > [!NOTE]
   > Om filresursen är en standardfilresurs visar inte filresursdimensionen filresursen eftersom mått per resurs inte är tillgängliga för standardfilresurser.  Aviseringar för standardfilresurser baseras på alla filresurser i lagringskontot. Eftersom mått per resurs inte är tillgängliga för standardfilresurser rekommenderar vi att du har en filresurs per lagringskonto.

7. Klicka på **listrutan** Dimensionsvärden och välj de filresurs(er) som du vill avisering om.
8. Ange **536870912000** byte som Tröskelvärde. 
9. Klicka på **listrutan Sammansättningskornighet** och välj **24 timmar**.
10. Välj **Utvärderingsfrekvens och** klicka **på Klar.**
11. Klicka **på Lägg till åtgärdsgrupper** för att lägga till en åtgärdsgrupp (e-post, SMS osv.) till aviseringen antingen genom att välja en befintlig åtgärdsgrupp eller skapa en ny åtgärdsgrupp. 
12. Fyll i **aviseringsinformationen** som **Aviseringsregelnamn,** **Beskrivning** och **Allvarlighetsgrad**.
13. Klicka **på Skapa aviseringsregel** för att skapa aviseringen.

## <a name="next-steps"></a>Nästa steg

- [Azure Files datareferens för övervakning](storage-files-monitoring-reference.md)
- [Övervaka Azure-resurser med Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md)
- [Azure Storage migrering av mått](../common/storage-metrics-migration.md)
- [Planera för en Azure Files-distribution](./storage-files-planning.md)
- [Så här distribuerar du Azure Files](./storage-how-to-create-file-share.md)
- [Felsöka Azure Files på Windows](./storage-troubleshoot-windows-file-connection-problems.md)
- [Felsöka Azure Files på Linux](./storage-troubleshoot-linux-file-connection-problems.md)
