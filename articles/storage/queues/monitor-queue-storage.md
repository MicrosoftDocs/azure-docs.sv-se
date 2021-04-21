---
title: Övervakning Azure Queue Storage
description: Lär dig hur du övervakar prestanda och tillgänglighet för Azure Queue Storage. Övervaka Azure Queue Storage data, lär dig mer om konfiguration och analysera mått- och loggdata.
author: normesta
services: storage
ms.author: normesta
ms.reviewer: fryu
ms.date: 10/26/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: queues
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b65aff45cc304f59e45fc3bed925b93ee6c622fd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788411"
---
# <a name="monitoring-azure-queue-storage"></a>Övervakning Azure Queue Storage

När du har kritiska program och affärsprocesser som förlitar sig på Azure-resurser vill du övervaka resursernas tillgänglighet, prestanda och drift. Den här artikeln beskriver övervakningsdata som genereras av Azure Queue Storage och hur du kan använda funktionerna i Azure Monitor för att analysera aviseringar om dessa data.

> [!NOTE]
> Azure Storage loggar i Azure Monitor är i offentlig förhandsversion och är tillgänglig för förhandsversionstestning i alla offentliga molnregioner. Den här förhandsversionen aktiverar loggar för blobar (som innehåller Azure Data Lake Storage Gen2), filer, köer och tabeller. Den här funktionen är tillgänglig för alla lagringskonton som skapas med Azure Resource Manager distributionsmodellen. Se [Översikt över lagringskonto.](../common/storage-account-overview.md)

## <a name="monitor-overview"></a>Översikt över övervakare

På **sidan** Översikt i Azure Portal för varje Queue Storage resurs finns en kort vy över resursanvändningen, till exempel begäranden och fakturering per timme. Den här informationen är användbar, men det är bara en liten mängd övervakningsdata som är tillgängliga. Vissa av dessa data samlas in automatiskt och är tillgängliga för analys så snart du skapar resursen. Du kan aktivera ytterligare typer av datainsamling med viss konfiguration.

## <a name="what-is-azure-monitor"></a>Vad är Azure Monitor?

Azure Queue Storage skapar övervakningsdata med hjälp [av Azure Monitor](../../azure-monitor/overview.md), som är en fullständig stackövervakningstjänst i Azure. Azure Monitor innehåller en komplett uppsättning funktioner för att övervaka dina Azure-resurser samt resurser i andra moln och lokalt.

Börja med [Övervaka Azure-resurser Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md) som beskriver följande:

- Vad är Azure Monitor?
- Kostnader som är kopplade till övervakning
- Övervakningsdata som samlas in i Azure
- Konfigurera datainsamling
- Standardverktyg i Azure för analys och avisering om övervakningsdata

Följande avsnitt bygger på den här artikeln genom att beskriva de specifika data som samlas in från Azure Storage. Exempel visar hur du konfigurerar datainsamling och analyserar dessa data med Azure-verktyg.

## <a name="monitoring-data"></a>Övervaka data

Azure Queue Storage samlar in samma typer av övervakningsdata som andra Azure-resurser, vilket beskrivs i [Övervaka data från Azure-resurser.](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)

Se [Azure Queue Storage för övervakningsdata](monitor-queue-storage-reference.md) för detaljerad information om mått och loggmått som skapats av Azure Queue Storage.

Mått och loggar i Azure Monitor endast stöd för Azure Resource Manager lagringskonton. Azure Monitor stöder inte klassiska lagringskonton. Om du vill använda mått eller loggar på ett klassiskt lagringskonto måste du migrera till ett Azure Resource Manager lagringskonto. Se [Migrera till Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md).

Du kan fortsätta att använda klassiska mått och loggar om du vill. I själva verket är klassiska mått och loggar tillgängliga parallellt med mått och loggar i Azure Monitor. Stödet är kvar tills Azure Storage avslutar tjänsten med äldre mått och loggar.

## <a name="collection-and-routing"></a>Insamling och routning

Plattformsmått och aktivitetsloggen samlas in automatiskt, men kan dirigeras till andra platser med hjälp av en diagnostikinställning.

Om du vill samla in resursloggar måste du skapa en diagnostikinställning. När du skapar inställningen väljer du **kö som** den typ av lagring som du vill aktivera loggar för. Ange sedan någon av följande kategorier av åtgärder som du vill samla in loggar för.

| Kategori | Beskrivning |
|:---|:---|
| **StorageRead** | Läsåtgärder för objekt. |
| **StorageWrite** | Skrivåtgärder för objekt. |
| **StorageDelete** | Ta bort åtgärder för objekt. |

## <a name="creating-a-diagnostic-setting"></a>Skapa en diagnostikinställning

Du kan skapa en diagnostikinställning med hjälp av Azure Portal, PowerShell, Azure CLI eller en Azure Resource Manager mall.

Allmänna riktlinjer finns i Skapa [diagnostikinställning för att samla in plattformsloggar och mått i Azure.](../../azure-monitor/essentials/diagnostic-settings.md)

> [!NOTE]
> Azure Storage loggar i Azure Monitor är i offentlig förhandsversion och är tillgänglig för förhandsversionstestning i alla offentliga molnregioner. Den här förhandsversionen aktiverar loggar för blobar (som innehåller Azure Data Lake Storage Gen2), filer, köer och tabeller. Den här funktionen är tillgänglig för alla lagringskonton som skapas med Azure Resource Manager distributionsmodellen. Se [Översikt över lagringskonto.](../common/storage-account-overview.md)

### <a name="azure-portal"></a>[Azure-portalen](#tab/azure-portal)

1. Logga in på Azure-portalen.

2. Navigera till ditt lagringskonto.

3. I avsnittet **Övervakning** klickar du på **Diagnostikinställningar (förhandsversion).**

   > [!div class="mx-imgBorder"]
   > ![portal – Diagnostikloggar](media/monitor-queue-storage/diagnostic-logs-settings-pane.png)

4. Välj **kö** som den typ av lagring som du vill aktivera loggar för.

5. Klicka på **Lägg till diagnostikinställning**.

   > [!div class="mx-imgBorder"]
   > ![portal – Resursloggar – lägg till diagnostikinställning](media/monitor-queue-storage/diagnostic-logs-settings-pane-2.png)

   Sidan **Diagnostikinställningar** visas.

   > [!div class="mx-imgBorder"]
   > ![Sidan Resursloggar](media/monitor-queue-storage/diagnostic-logs-page.png)

6. I fältet **Namn** på sidan anger du ett namn för den här resurslogginställningen. Välj sedan vilka åtgärder du vill logga (läs-, skriv- och borttagningsåtgärder) och var du vill att loggarna ska skickas.

#### <a name="archive-logs-to-a-storage-account"></a>Arkivera loggar till ett lagringskonto

Om du väljer att arkivera loggarna till ett lagringskonto betalar du för mängden loggar som skickas till lagringskontot. Specifika priser finns i avsnittet **Plattformsloggar** på [sidan Azure Monitor prissättning.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

1. Markera **kryssrutan Arkivera till ett lagringskonto** och välj sedan **knappen** Konfigurera.

   > [!div class="mx-imgBorder"]
   > ![Arkivlagring på sidan Diagnostikinställningar](media/monitor-queue-storage/diagnostic-logs-settings-pane-archive-storage.png)

2. I **listrutan Lagringskonto** väljer du det lagringskonto som du vill arkivera loggarna till, klickar på **knappen OK** och väljer **sedan knappen** Spara.
 
   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > Innan du väljer ett lagringskonto som exportmål kan du se [Arkivera Azure-resursloggar](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) för att förstå kraven för lagringskontot.

#### <a name="stream-logs-to-azure-event-hubs"></a>Strömma loggar till Azure Event Hubs

Om du väljer att strömma dina loggar till en händelsehubb betalar du för mängden loggar som skickas till händelsehubben. Specifika priser finns i avsnittet **Plattformsloggar** på [sidan Azure Monitor prissättning.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

1. Markera kryssrutan **Strömma till en händelsehubb** och välj sedan **knappen** Konfigurera.

2. I fönstret **Välj en händelsehubb** väljer du namnområde, namn och principnamn för den händelsehubb som du vill strömma loggarna till.

   > [!div class="mx-imgBorder"]
   > ![Händelsehubb för sidan Diagnostikinställningar](media/monitor-queue-storage/diagnostic-logs-settings-pane-event-hub.png)

3. Klicka på **OK** och välj sedan **knappen** Spara.

#### <a name="send-logs-to-azure-log-analytics"></a>Skicka loggar till Azure Log Analytics

1. Markera kryssrutan **Skicka till Log Analytics,** välj en Log Analytics-arbetsyta och välj sedan **knappen** Spara.

   > [!div class="mx-imgBorder"]
   > ![Logganalys för diagnostikinställningar](media/monitor-queue-storage/diagnostic-logs-settings-pane-log-analytics.png)

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

Ersätt `<storage-service-resource--id>` platshållaren i det här kodfragmentet med resurs-ID:t för kön. Du hittar resurs-ID:t i Azure Portal genom att öppna **sidan Egenskaper** för ditt lagringskonto.

Du kan använda `StorageRead` , och för värdet för `StorageWrite` `StorageDelete` **parametern** Kategori.

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Här är ett exempel:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

En beskrivning av varje parameter finns i Arkivera [Azure-resursloggar via Azure PowerShell](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Strömma loggar till en händelsehubb

Om du väljer att strömma dina loggar till en händelsehubb betalar du för mängden loggar som skickas till händelsehubben. Specifika priser finns i avsnittet **Plattformsloggar** på [sidan Azure Monitor prissättning.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Aktivera loggar med hjälp av [PowerShell-cmdleten Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) med `EventHubAuthorizationRuleId` parametern .

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Här är ett exempel:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

En beskrivning av varje parameter finns i Strömma [data som ska Event Hubs via PowerShell-cmdlets.](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

#### <a name="send-logs-to-log-analytics"></a>Skicka loggar till Log Analytics

Aktivera loggar med hjälp av [PowerShell-cmdleten Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) med `WorkspaceId` parametern .

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Här är ett exempel:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

Mer information finns i Strömma [Azure-resursloggar till Log Analytics-arbetsytan i Azure Monitor](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Öppna först [Azure Cloud Shell,](../../cloud-shell/overview.md)eller om du har installerat [Azure CLI](/cli/azure/install-azure-cli) lokalt öppnar du ett kommandokonsolprogram som PowerShell.

2. Om din identitet är associerad med mer än en prenumeration anger du den aktiva prenumerationen till prenumerationen på det lagringskonto som du vill aktivera loggar för.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Ersätt `<subscription-id>` platshållarvärdet med ID:t för din prenumeration.

#### <a name="archive-logs-to-a-storage-account"></a>Arkivera loggar till ett lagringskonto

Om du väljer att arkivera loggarna till ett lagringskonto betalar du för mängden loggar som skickas till lagringskontot. Specifik prissättning finns i avsnittet **Plattformsloggar** på sidan [Azure Monitor prissättning.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Aktivera loggar med hjälp av [`az monitor diagnostic-settings create`](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) kommandot .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true}]'
```

Ersätt `<storage-service-resource--id>` platshållaren i det här kodfragmentet med resurs-ID:t för kön. Du hittar resurs-ID:t i Azure Portal genom att öppna **sidan Egenskaper** för ditt lagringskonto.

Du kan använda `StorageRead` , och för värdet för `StorageWrite` `StorageDelete` `category` parametern .

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Här är ett exempel:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/queueServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true}]'`

En beskrivning av varje parameter finns i [Arkivera resursloggar via Azure CLI.](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

#### <a name="stream-logs-to-an-event-hub"></a>Strömma loggar till en händelsehubb

Om du väljer att strömma dina loggar till en händelsehubb betalar du för mängden loggar som skickas till händelsehubben. Specifik prissättning finns i avsnittet **Plattformsloggar** på sidan [Azure Monitor prissättning.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Aktivera loggar med hjälp av [`az monitor diagnostic-settings create`](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) kommandot .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Här är ett exempel:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/queueServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

En beskrivning av varje parameter finns i [Strömma data till Event Hubs via Azure CLI.](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

#### <a name="send-logs-to-log-analytics"></a>Skicka loggar till Log Analytics

Aktivera loggar med hjälp av [`az monitor diagnostic-settings create`](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) kommandot .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Här är ett exempel:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/queueServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 Mer information finns i Strömma [Azure-resursloggar till Log Analytics-arbetsytan i Azure Monitor](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

# <a name="template"></a>[Mall](#tab/template)

Om du vill Azure Resource Manager en mall som skapar en diagnostikinställning, se [Diagnostikinställning för Azure Storage](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage).

---

## <a name="analyzing-metrics"></a>Analysera mått

Du kan analysera mått för Azure Storage med mått från andra Azure-tjänster med hjälp av Azure Metrics Explorer. Öppna Metrics Explorer genom att **välja Mått** från **Azure Monitor menyn.** Mer information om hur du använder det här verktyget [finns i Komma igång med Azure Metrics Explorer](../../azure-monitor/essentials/metrics-getting-started.md).

Det här exemplet visar hur **du visar transaktioner** på kontonivå.

![Skärmbild av åtkomst till mått i Azure Portal](./media/monitor-queue-storage/access-metrics-portal.png)

För mått som stöder dimensioner kan du filtrera måttet med önskat dimensionsvärde. Det här exemplet visar hur du **visar transaktioner på** kontonivå för en specifik åtgärd genom att välja värden för **API-namndimensionen.**

![Skärmbild av åtkomst till mått med dimension i Azure Portal](./media/monitor-queue-storage/access-metrics-portal-with-dimension.png)

En fullständig lista över de dimensioner som Azure Storage stöder finns i [Måttdimensioner](monitor-queue-storage-reference.md#metrics-dimensions).

Mått för Azure Queue Storage finns i följande namnrymder:

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/queueServices

En lista över alla Azure Monitor stödmått, som innehåller Azure Queue Storage, finns [i Azure Monitor mått som stöds.](../../azure-monitor/essentials/metrics-supported.md)

### <a name="accessing-metrics"></a>Komma åt mått

> [!TIP]
> Om du vill visa Azure CLI- eller .NET-exempel väljer du motsvarande flikar som visas här.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Visa en lista över måttdefinitionen

Du kan lista måttdefinitionen för ditt lagringskonto eller Queue Storage tjänsten. Använd [cmdleten Get-AzMetricDefinition.](/powershell/module/az.monitor/get-azmetricdefinition)

I det här exemplet ersätter `<resource-ID>` du platshållaren med resurs-ID:t för hela lagringskontot eller resurs-ID:t för kön. Du hittar dessa resurs-ID:er på **egenskapssidorna** för ditt lagringskonto i Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Läsa måttvärden

Du kan läsa måttvärden på kontonivå för ditt lagringskonto eller Queue Storage tjänsten. Använd [cmdleten Get-AzMetric.](/powershell/module/az.monitor/get-azmetric)

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Visa en lista över måttdefinitionen på kontonivå

Du kan lista måttdefinitionen för ditt lagringskonto eller Queue Storage tjänsten. Använd [`az monitor metrics list-definitions`](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions) kommandot .

I det här exemplet ersätter `<resource-ID>` du platshållaren med resurs-ID:t för hela lagringskontot eller resurs-ID:t för kön. Du hittar dessa resurs-ID:er på **egenskapssidorna** för ditt lagringskonto i Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Läsa måttvärden på kontonivå

Du kan läsa måttvärdena för ditt lagringskonto eller Queue Storage tjänsten. Använd [`az monitor metrics list`](/cli/azure/monitor/metrics#az_monitor_metrics_list) kommandot .

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/azure-portal)

Azure Monitor tillhandahåller [.NET SDK för att](https://www.nuget.org/packages/microsoft.azure.management.monitor/) läsa måttdefinition och -värden. Exempelkoden [visar](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) hur du använder SDK med olika parametrar. Du måste använda `0.18.0-preview` eller en senare version för lagringsmått.

I de här exemplen `<resource-ID>` ersätter du platshållaren med resurs-ID:t för hela lagringskontot eller kön. Du hittar dessa resurs-ID:er på **egenskapssidorna** för ditt lagringskonto i Azure Portal.

Ersätt `<subscription-ID>` variabeln med ID:t för din prenumeration. Anvisningar för hur du hämtar värden för , och finns i Använda portalen för att skapa ett Azure AD-program och tjänstens huvudnamn `<tenant-ID>` som har åtkomst till `<application-ID>` `<AccessKey>` [resurser.](../../active-directory/develop/howto-create-service-principal-portal.md)

#### <a name="list-the-account-level-metric-definition"></a>Visa en lista över måttdefinitionen på kontonivå

I följande exempel visas hur du visar en måttdefinition på kontonivå:

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

I följande exempel visas hur du `UsedCapacity` läser data på kontonivå:

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

I följande exempel visas hur du läser måttdata på måttet som stöder flerdimensionellt:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for queue storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default";
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

### <a name="template"></a>[Mall](#tab/template)

Ej tillämpligt.

---

## <a name="analyzing-logs"></a>Analysera loggar

Du kan komma åt resursloggar antingen som en kö i ett lagringskonto, som händelsedata eller via Log Analytics-frågor.

En detaljerad referens till de fält som visas i dessa loggar finns i Azure Queue Storage [referens för övervakningsdata.](monitor-queue-storage-reference.md)

> [!NOTE]
> Azure Storage loggar i Azure Monitor är i offentlig förhandsversion och är tillgänglig för förhandsversionstestning i alla offentliga molnregioner. Den här förhandsversionen aktiverar loggar för blobar (som innehåller Azure Data Lake Storage Gen2), filer, köer, tabeller, premiumlagringskonton i generell användning v1 och allmänna v2-lagringskonton. Klassiska lagringskonton stöds inte.

Loggposter skapas bara om det finns begäranden som görs mot tjänstslutpunkten. Om ett lagringskonto till exempel har aktivitet i sin köslutpunkt, men inte i tabellen eller blobslutpunkterna, skapas endast loggar som Queue Storage. Azure Storage innehåller detaljerad information om lyckade och misslyckade begäranden till en lagringstjänst. Den här informationen kan användas för att övervaka enskilda begäranden och för att diagnostisera problem med en lagringstjänst. Begäranden loggas efter bästa möjliga resultat.

### <a name="log-authenticated-requests"></a>Logga autentiserade begäranden

Följande typer av autentiserade begäranden loggas:

- Lyckade begäranden
- Misslyckade begäranden, inklusive timeout, begränsning, nätverk, auktorisering och andra fel
- Begäranden som använder en signatur för delad åtkomst (SAS) eller OAuth, inklusive misslyckade och lyckade begäranden
- Begäranden till analysdata (klassiska loggdata i **$logs** containern och klassmåttdata i **$metric tabellerna)**

Begäranden som görs Queue Storage själva tjänsten, till exempel loggskapande eller borttagning, loggas inte. En fullständig lista över loggade data finns i [Lagringsloggade](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) åtgärder och statusmeddelanden och [Lagringsloggformat.](monitor-queue-storage-reference.md)

### <a name="log-anonymous-requests"></a>Logga anonyma begäranden

Följande typer av anonyma begäranden loggas:

- Lyckade begäranden
- Serverfel
- Time out-fel för både klient och server
- Misslyckade `GET` begäranden med felkoden 304 ( `Not Modified` )

Alla andra misslyckade anonyma begäranden loggas inte. En fullständig lista över loggade data finns i [Lagringsloggade](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) åtgärder och statusmeddelanden och [Lagringsloggformat.](monitor-queue-storage-reference.md)

### <a name="accessing-logs-in-a-storage-account"></a>Åtkomst till loggar i ett lagringskonto

Loggar visas som blobar som lagras i en container i mållagringskontot. Data samlas in och lagras i en enda blob som en radavgränsad JSON-nyttolast. Namnet på bloben följer den här namngivningskonventionen:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/queueServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Här är ett exempel:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Åtkomst till loggar i en händelsehubb

Loggar som skickas till en händelsehubb lagras inte som en fil, men du kan kontrollera att händelsehubben tog emot logginformationen. I Azure Portal du till din händelsehubb och kontrollerar att `incoming requests` antalet är större än noll.

![Granskningsloggar](media/monitor-queue-storage/event-hub-log.png)

Du kan komma åt och läsa loggdata som skickas till din händelsehubb med hjälp av säkerhetsinformation, händelsehantering och övervakningsverktyg. Mer information finns i Vad [kan jag göra med övervakningsdata som skickas till min händelsehubb?](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Komma åt loggar på en Log Analytics-arbetsyta

Du kan komma åt loggar som skickas till en Log Analytics-arbetsyta med Azure Monitor och loggfrågor.

Mer information finns i [Kom igång med Log Analytics i Azure Monitor](../../azure-monitor/logs/log-analytics-tutorial.md).

Data lagras i `StorageQueueLogs` tabellen.

#### <a name="sample-kusto-queries"></a>Exempel på Kusto-frågor

Här följer några frågor som du kan ange i **loggsökningsfältet** som hjälper dig att övervaka köerna. De här frågorna fungerar med det [nya språket](../../azure-monitor/logs/log-query-overview.md).

> [!IMPORTANT]
> När du väljer **Loggar** på resursgruppmenyn för lagringskontot öppnas Log Analytics med frågeomfånget inställt på den aktuella resursgruppen. Det innebär att loggfrågor endast innehåller data från den resursgruppen. Om du vill köra en fråga som innehåller data från andra  resurser eller data från andra Azure-tjänster väljer du **Loggar Azure Monitor** menyn. Mer information finns i Log query scope and time range in Azure Monitor Log Analytics (Loggfrågeomfång och tidsperiod [i Log Analytics).](../../azure-monitor/logs/scope.md)

Använd de här frågorna för att övervaka dina Azure Storage konton:

- För att lista de 10 vanligaste felen under de senaste tre dagarna.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```

- För att visa de 10 främsta åtgärderna som orsakade flest fel under de senaste tre dagarna.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```

- För att lista de 10 främsta åtgärderna med den längsta svarstiden från början till slut under de senaste tre dagarna.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```

- Om du vill visa en lista över alla åtgärder som har orsakat begränsningsfel på serversidan under de senaste tre dagarna.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```

- Om du vill visa en lista över alla begäranden med anonym åtkomst under de senaste tre dagarna.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```

- Så här skapar du ett cirkeldiagram över åtgärder som har använts under de senaste tre dagarna.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc
    | render piechart
    ```

## <a name="faq"></a>Vanliga frågor

**Stöder Azure Storage mått för hanterade diskar eller ohanterade diskar?**

Nej. Beräkningsinstanser stöder mått på diskar. Mer information finns i [Mått per disk för hanterade och ohanterade diskar.](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)

## <a name="next-steps"></a>Nästa steg

- En referens till loggar och mått som skapats av Azure Queue Storage finns i [Azure Queue Storage för övervakningsdata.](monitor-queue-storage-reference.md)
- Mer information om övervakning av Azure-resurser finns [i Övervaka Azure-resurser med Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md).
- Mer information om måttmigrering finns i [Azure Storage migrering av mått.](../common/storage-metrics-migration.md)
