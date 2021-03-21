---
title: Övervaka Azure Table Storage | Microsoft Docs
description: Lär dig hur du övervakar prestanda och tillgänglighet för Azure Table Storage. Övervaka lagrings data i Azure Table, lär dig mer om konfiguration och analys av mått-och loggdata.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: d2973d95cf797123e02fd48dc2dfab1b172e05f5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499481"
---
# <a name="monitoring-azure-table-storage"></a>Övervaka Azure Table Storage

När du har viktiga program och affärs processer som förlitar sig på Azure-resurser, vill du övervaka resurserna för deras tillgänglighet, prestanda och drift. Den här artikeln beskriver övervaknings data som genereras av Azure Table Storage och hur du kan använda funktionerna i Azure Monitor för att analysera aviseringar för dessa data.

> [!NOTE]
> Azure Storage loggar i Azure Monitor finns i offentlig för hands version och är tillgänglig för för hands testning i alla offentliga moln regioner. Den här för hands versionen aktiverar loggar för blobbar (som innehåller Azure Data Lake Storage Gen2), filer, köer och tabeller. Den här funktionen är tillgänglig för alla lagrings konton som skapas med Azure Resource Manager distributions modell. Se [Översikt över lagrings konto](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Övervaka översikt

**Översikts** sidan i Azure Portal för varje tabell lagrings resurs innehåller en kort översikt över resursanvändningen, till exempel begär Anden och fakturering per timme. Den här informationen är användbar, men bara en liten mängd av övervaknings data är tillgänglig. En del av dessa data samlas in automatiskt och är tillgängliga för analys så fort du skapar resursen. Du kan aktivera ytterligare typer av data insamling med en del konfiguration.

## <a name="what-is-azure-monitor"></a>Vad är Azure Monitor?
Azure Table Storage skapar övervaknings data med hjälp av [Azure Monitor](../../azure-monitor/overview.md), som är en fullständig stack övervaknings tjänst i Azure. Azure Monitor innehåller en fullständig uppsättning funktioner för att övervaka dina Azure-resurser och resurser i andra moln och lokalt. 

Börja med artikeln [övervakning av Azure-resurser med Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md) som beskriver följande:

- Vad är Azure Monitor?
- Kostnader för övervakning
- Övervaknings data som samlas in i Azure
- Konfigurerar data insamling
- Standard verktyg i Azure för analys och avisering om övervaknings data

Följande avsnitt bygger på den här artikeln genom att beskriva de specifika data som samlats in från Azure Storage. Exempel visar hur du konfigurerar data insamling och analyserar data med Azure-verktyg.

## <a name="monitoring-data"></a>Övervaka data

Azure Table Storage samlar in samma typer av övervaknings data som andra Azure-resurser, som beskrivs i [övervaknings data från Azure-resurser](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Se [data referens för Azure Table Storage-övervakning](monitor-table-storage-reference.md) för detaljerad information om mått och loggar mått som skapats av Azure Table Storage.

Mått och loggar i Azure Monitor endast stöd för Azure Resource Manager lagrings konton. Azure Monitor stöder inte klassiska lagrings konton. Om du vill använda mått eller loggar på ett klassiskt lagrings konto måste du migrera till ett Azure Resource Manager lagrings konto. Se [migrera till Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md).

Du kan fortsätta använda klassiska mått och loggar om du vill. Faktum är att klassiska mått och loggar är tillgängliga parallellt med mått och loggar i Azure Monitor. Stödet finns kvar tills Azure Storage avslutar tjänsten med äldre mått och loggar.

## <a name="collection-and-routing"></a>Samling och routning

Plattforms mått och aktivitets loggen samlas in automatiskt, men de kan dirigeras till andra platser med hjälp av en diagnostisk inställning. 

Om du vill samla in resurs loggar måste du skapa en diagnostisk inställning. När du skapar inställningen väljer du **tabell** som den typ av lagring som du vill aktivera loggar för. Ange sedan en av följande kategorier av åtgärder som du vill samla in loggar för. 

| Kategori | Beskrivning |
|:---|:---|
| StorageRead | Läs åtgärder för objekt. |
| StorageWrite | Skriv åtgärder för objekt. |
| StorageDelete | Ta bort åtgärder för objekt. |

## <a name="creating-a-diagnostic-setting"></a>Skapa en diagnostisk inställning

Du kan skapa en diagnostisk inställning med hjälp av Azure Portal, PowerShell, Azure CLI eller en Azure Resource Manager mall. 

Allmän vägledning finns i [skapa diagnostisk inställning för att samla in plattforms loggar och mått i Azure](../../azure-monitor/essentials/diagnostic-settings.md).

> [!NOTE]
> Azure Storage loggar i Azure Monitor finns i offentlig för hands version och är tillgänglig för för hands testning i alla offentliga moln regioner. Den här för hands versionen aktiverar loggar för blobbar (som innehåller Azure Data Lake Storage Gen2), filer, köer och tabeller. Den här funktionen är tillgänglig för alla lagrings konton som skapas med Azure Resource Manager distributions modell. Se [Översikt över lagrings konto](../common/storage-account-overview.md).

### <a name="azure-portal"></a>[Azure-portalen](#tab/azure-portal)

1. Logga in på Azure-portalen.

2. Navigera till ditt lagringskonto.

3. I avsnittet **övervakning** klickar du på **diagnostikinställningar (för hands version)**.

   > [!div class="mx-imgBorder"]
   > ![Portal – diagnostikloggar](media/monitor-table-storage/diagnostic-logs-settings-pane.png)   

4. Välj **tabell** som den typ av lagring som du vill aktivera loggar för.

5. Klicka på **Lägg till diagnostisk inställning**.

   > [!div class="mx-imgBorder"]
   > ![Portal – resurs loggar – Lägg till diagnostisk inställning](media/monitor-table-storage/diagnostic-logs-settings-pane-2.png)

   Sidan **diagnostikinställningar** visas.

   > [!div class="mx-imgBorder"]
   > ![Sidan resurs loggar](media/monitor-table-storage/diagnostic-logs-page.png)

6. Ange ett namn för den här resurs logg inställningen i fältet **namn** på sidan. Välj sedan vilka åtgärder du vill logga (Läs-, skriv-och borttagnings åtgärder) och var du vill att loggarna ska skickas.

#### <a name="archive-logs-to-a-storage-account"></a>Arkivera loggar till ett lagrings konto

Om du väljer att arkivera loggarna på ett lagrings konto betalar du för den volym loggar som skickas till lagrings kontot. För en speciell prissättning, se avsnittet **plattforms loggar** på sidan [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

1. Markera kryss rutan **arkivera till ett lagrings konto** och klicka sedan på knappen **Konfigurera** .

   > [!div class="mx-imgBorder"]   
   > ![Sidan diagnostikinställningar Arkiv Arkiv lagring](media/monitor-table-storage/diagnostic-logs-settings-pane-archive-storage.png)

2. I list rutan **lagrings konto** väljer du det lagrings konto som du vill arkivera loggarna till, klickar på **OK** -knappen och klickar sedan på knappen **Spara** .

   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > Innan du väljer ett lagrings konto som export mål, se [arkivera Azures resurs loggar](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) för att förstå krav på lagrings kontot.

#### <a name="stream-logs-to-azure-event-hubs"></a>Strömma loggar till Azure Event Hubs

Om du väljer att strömma dina loggar till en Event Hub, betalar du för den volym av loggar som skickas till händelsehubben. För en speciell prissättning, se avsnittet **plattforms loggar** på sidan [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

1. Markera kryss rutan **strömma till en Event Hub** och klicka sedan på knappen **Konfigurera** .

2. I fönstret **Välj en händelse hubb** väljer du namn område, namn och princip namn för den händelsehubben som du vill strömma loggarna till. 

   > [!div class="mx-imgBorder"]
   > ![Händelse hubb för sidan diagnostikinställningar](media/monitor-table-storage/diagnostic-logs-settings-pane-event-hub.png)

3. Klicka på knappen **OK** och klicka sedan på knappen **Spara** .

#### <a name="send-logs-to-azure-log-analytics"></a>Skicka loggar till Azure Log Analytics

1. Markera kryss rutan **Skicka till Log Analytics** , Välj en Log Analytics-arbetsyta och klicka sedan på knappen **Spara** .

   > [!div class="mx-imgBorder"]   
   > ![Sid logg analys för diagnostikinställningar](media/monitor-table-storage/diagnostic-logs-settings-pane-log-analytics.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Öppna ett Windows PowerShell-kommando fönster och logga in på din Azure-prenumeration med hjälp av `Connect-AzAccount` kommandot. Följ sedan anvisningarna på skärmen.

   ```powershell
   Connect-AzAccount
   ```

2. Ange din aktiva prenumeration på prenumerationen på det lagrings konto som du vill aktivera loggning för.

   ```powershell
   Set-AzContext -SubscriptionId <subscription-id>
   ```

#### <a name="archive-logs-to-a-storage-account"></a>Arkivera loggar till ett lagrings konto

Om du väljer att arkivera loggarna på ett lagrings konto betalar du för den volym loggar som skickas till lagrings kontot. För en speciell prissättning, se avsnittet **plattforms loggar** på sidan [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

Aktivera loggar med hjälp av [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell-cmdlet tillsammans med `StorageAccountId` parametern.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -StorageAccountId <storage-account-resource-id> -Enabled $true -Category <operations-to-log>
```

Ersätt `<storage-service-resource--id>` plats hållaren i det här kodfragmentet med resurs-ID för tabell tjänsten. Du hittar resurs-ID: t i Azure Portal genom att öppna sidan **Egenskaper** för ditt lagrings konto.

Du kan använda `StorageRead` , `StorageWrite` , och `StorageDelete` för värdet för parametern **Category** .

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Här är ett exempel:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/tableServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Mer information om hur du arkiverar resurs loggar till Azure Storage finns i [Azure resurs loggar](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Strömma loggar till en händelsehubb

Om du väljer att strömma dina loggar till en Event Hub, betalar du för den volym av loggar som skickas till händelsehubben. För en speciell prissättning, se avsnittet **plattforms loggar** på sidan [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

Aktivera loggar med hjälp av cmdleten [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell med `EventHubAuthorizationRuleId` parametern.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Här är ett exempel:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/tableServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

Mer information om hur du skickar resurs loggar till Event Hub finns i [Azure resurs loggar](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Skicka loggar till Log Analytics

Aktivera loggar med hjälp av cmdleten [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell med `WorkspaceId` parametern.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Här är ett exempel:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/tableServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

Mer information finns i [strömma Azure resurs loggar till Log Analytics arbets ytan i Azure Monitor](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Öppna först [Azure Cloud Shell](../../cloud-shell/overview.md), eller om du har [installerat](/cli/azure/install-azure-cli) Azure CLI lokalt öppnar du ett kommando konsol program, till exempel Windows PowerShell.

2. Om din identitet är associerad med fler än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på det lagrings konto som du vill aktivera loggar för.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Ersätt `<subscription-id>` placeholder-värdet med ID: t för din prenumeration.

#### <a name="archive-logs-to-a-storage-account"></a>Arkivera loggar till ett lagrings konto

Om du väljer att arkivera loggarna på ett lagrings konto betalar du för den volym loggar som skickas till lagrings kontot. För en speciell prissättning, se avsnittet **plattforms loggar** på sidan [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

Aktivera loggar genom att använda kommandot [AZ Monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Ersätt `<storage-service-resource--id>` plats hållaren i det här kodfragmentet med resurs-ID Table Storage-tjänsten. Du hittar resurs-ID: t i Azure Portal genom att öppna sidan **Egenskaper** för ditt lagrings konto.

Du kan använda `StorageRead` , `StorageWrite` , och `StorageDelete` för värdet för parametern **Category** .

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Här är ett exempel:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/tableServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true}]'`

#### <a name="stream-logs-to-an-event-hub"></a>Strömma loggar till en händelsehubb

Om du väljer att strömma dina loggar till en Event Hub, betalar du för den volym av loggar som skickas till händelsehubben. För en speciell prissättning, se avsnittet **plattforms loggar** på sidan [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

Aktivera loggar genom att använda kommandot [AZ Monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Här är ett exempel:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/tableServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

#### <a name="send-logs-to-log-analytics"></a>Skicka loggar till Log Analytics

Aktivera loggar genom att använda kommandot [AZ Monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Här är ett exempel:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/tableServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 Mer information finns i [strömma Azure resurs loggar till Log Analytics arbets ytan i Azure Monitor](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

### <a name="template"></a>[Mall](#tab/template)

Om du vill visa en Azure Resource Manager mall som skapar en diagnostisk inställning, se [diagnostisk inställning för Azure Storage](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage).

---

## <a name="analyzing-metrics"></a>Analyserar mått

Du kan analysera mått för Azure Storage med mått från andra Azure-tjänster med hjälp av Metrics Explorer. Öppna Metrics Explorer genom att välja **mått** på **Azure Monitor** -menyn. Mer information om hur du använder det här verktyget finns i [komma igång med Azure Metrics Explorer](../../azure-monitor/essentials/metrics-getting-started.md). 

Det här exemplet visar hur du visar **transaktioner** på konto nivå.

![Skärm bild av åtkomst till mått i Azure Portal](./media/monitor-table-storage/access-metrics-portal.png)

För mått som stöder dimensioner kan du filtrera måttet med det önskade dimension svärdet. Det här exemplet visar hur du visar **transaktioner** på konto nivå för en speciell åtgärd genom att välja värden för dimensionen **API-namn** .

![Skärm bild av åtkomst till mått med dimension i Azure Portal](./media/monitor-table-storage/access-metrics-portal-with-dimension.png)

En fullständig lista över de dimensioner som Azure Storage stöder finns i [mått dimensioner](monitor-table-storage-reference.md#metrics-dimensions).

Mått för Azure Table Storage finns i följande namn områden: 

- Microsoft. Storage/storageAccounts
- Microsoft. Storage/storageAccounts/tableServices

En lista över alla Azure Monitor support mått, som innehåller Azure Table Storage, finns i [Azure Monitor mått som stöds](../../azure-monitor/essentials/metrics-supported.md).


### <a name="accessing-metrics"></a>Åtkomst till mått

> [!TIP]
> Om du vill visa Azure CLI-eller .NET-exempel väljer du de motsvarande flikarna som visas här.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Lista mått definitionen

Du kan visa mått definitionen för ditt lagrings konto eller tabellen Storage-tjänst. Använd cmdleten [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition) .

I det här exemplet ersätter du `<resource-ID>` plats hållaren med resurs-ID för hela lagrings kontot eller resurs-ID: t för tabell lagrings tjänsten.  Du hittar dessa resurs-ID: n på **egenskaps** sidorna för ditt lagrings konto i Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Läser mått värden

Du kan läsa mått värden på konto nivå för ditt lagrings konto eller tabellen Storage-tjänst. Använd cmdleten [Get-AzMetric](/powershell/module/Az.Monitor/Get-AzMetric) .

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Lista mått definitionen på konto nivå

Du kan visa mått definitionen för ditt lagrings konto eller tabellen Storage-tjänst. Använd kommandot [AZ Monitor Metric List-definitions](/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions) .
 
I det här exemplet ersätter du `<resource-ID>` plats hållaren med resurs-ID för hela lagrings kontot eller resurs-ID: t för tabell lagrings tjänsten. Du hittar dessa resurs-ID: n på **egenskaps** sidorna för ditt lagrings konto i Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Läs mått värden på konto nivå

Du kan läsa mått värden för ditt lagrings konto eller tabellen Storage-tjänst. Använd kommandot [AZ Monitor Metric List](/cli/azure/monitor/metrics#az-monitor-metrics-list) .

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/azure-portal)

Azure Monitor tillhandahåller [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) för att läsa mått definition och-värden. [Exempel koden](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) visar hur du använder SDK med olika parametrar. Du måste använda `0.18.0-preview` eller en senare version för lagrings mått.
 
I de här exemplen ersätter du `<resource-ID>` plats hållaren med resurs-ID för hela lagrings kontot eller tabell lagrings tjänsten. Du hittar dessa resurs-ID: n på **egenskaps** sidorna för ditt lagrings konto i Azure Portal.

Ersätt `<subscription-ID>` variabeln med ID: t för din prenumeration. Anvisningar för hur du hämtar värden för `<tenant-ID>` , `<application-ID>` och `<AccessKey>` finns i [använda portalen för att skapa ett Azure AD-program och tjänstens huvud namn som kan komma åt resurser](../../active-directory/develop/howto-create-service-principal-portal.md). 

#### <a name="list-the-account-level-metric-definition"></a>Lista mått definitionen på konto nivå

I följande exempel visas hur du listar en mått definition på konto nivån:

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

#### <a name="reading-account-level-metric-values"></a>Läser mått värden på konto nivå

I följande exempel visas hur du läser `UsedCapacity` data på konto nivå:

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

#### <a name="reading-multidimensional-metric-values"></a>Läser flerdimensionella mått värden

För flerdimensionella mått måste du definiera metadata-filter om du vill läsa mått data för vissa dimensions värden.

I följande exempel visas hur du läser mått data för måttet som stöder multidimension:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for table storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default";
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

Du kan komma åt resurs loggar antingen som en BLOB i ett lagrings konto, som händelse data eller genom att logga analys frågor.

En detaljerad referens för de fält som visas i dessa loggar finns i [data referens för Azure Table Storage-övervakning](monitor-table-storage-reference.md).

> [!NOTE]
> Azure Storage loggar i Azure Monitor finns i offentlig för hands version och är tillgänglig för för hands testning i alla offentliga moln regioner. Den här för hands versionen aktiverar loggar för blobbar (som innehåller Azure Data Lake Storage Gen2), filer, köer, tabeller, Premium Storage-konton i generell användning v1 och lagrings konton för generell användning v2. Klassiska lagrings konton stöds inte.

Logg poster skapas endast om det finns begär Anden som görs mot tjänst slut punkten. Om ett lagrings konto till exempel har aktivitet i dess tabell slut punkt men inte i dess BLOB-eller Queue-slutpunkter, skapas bara loggar som hör till tabell tjänsten. Azure Storage loggar innehåller detaljerad information om lyckade och misslyckade förfrågningar till en lagrings tjänst. Den här informationen kan användas för att övervaka enskilda begäranden och för att diagnostisera problem med en lagringstjänst. Begär Anden loggas med bästa möjliga ansträngning.

### <a name="log-authenticated-requests"></a>Logga autentiserade begär Anden

 Följande typer av autentiserade begäranden loggas:

- Lyckade begäranden
- Misslyckade begäranden, inklusive timeout, begränsning, nätverk, auktorisering och andra fel
- Begär Anden som använder en signatur för delad åtkomst (SAS) eller OAuth, inklusive misslyckade och lyckade förfrågningar
- Begär Anden om analys av data (klassiska loggdata i **$logs** container-och klass mått data i **$Metric** tabeller)

Begär Anden som görs av själva tabellen Storage-tjänst, till exempel när loggen skapas eller tas bort, loggas inte. En fullständig lista över de loggade data som loggas finns i [lagrings loggade åtgärder och status meddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) och [logg format för lagring](monitor-table-storage-reference.md).

### <a name="log-anonymous-requests"></a>Logga anonyma begär Anden

 Följande typer av anonyma begär Anden loggas:

- Lyckade begäranden
- Serverfel
- Timeout-fel för både klienten och servern
- Misslyckade GET-begäranden med felkoden 304 (inte ändrad)

Alla andra misslyckade anonyma begär Anden loggas inte. En fullständig lista över de loggade data som loggas finns i [lagrings loggade åtgärder och status meddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) och [logg format för lagring](monitor-table-storage-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Åtkomst till loggar i ett lagrings konto

Loggar visas som blobbar som lagras i en behållare på mål lagrings kontot. Data samlas in och lagras i en enda blob som en linje avgränsad JSON-nyttolast. Namnet på blobben följer denna namngivnings konvention:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/tableServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Här är ett exempel:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/tableServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Komma åt loggar i en Event Hub

Loggar som skickas till en Event Hub lagras inte som en fil, men du kan kontrol lera att händelsehubben har fått logg informationen. I Azure Portal går du till händelsehubben och kontrollerar att antalet **inkommande meddelanden** är större än noll. 

![Granskningsloggar](media/monitor-table-storage/event-hub-log.png)

Du kan komma åt och läsa loggdata som skickas till händelsehubben med hjälp av säkerhets information och verktyg för händelse hantering och övervakning. Mer information finns i [Azure resurs loggar](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Komma åt loggar i en Log Analytics-arbetsyta

Du kan komma åt loggar som skickas till en Log Analytics arbets yta genom att använda Azure Monitor logg frågor.

Mer information finns i [Stream Azure Monitoring data to Event Hub och external partners](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).

Data lagras i tabellen **StorageTableLogs** . 

#### <a name="sample-kusto-queries"></a>Exempel på Kusto-frågor

Här följer några frågor som du kan ange i **logg Sök** fältet för att hjälpa dig att övervaka din tabell lagring. Dessa frågor fungerar med det [nya språket](../../azure-monitor/logs/log-query-overview.md).

> [!IMPORTANT]
> När du väljer **loggar** från menyn resurs grupp för lagrings konto öppnas Log Analytics med fråge omfånget som är inställt på den aktuella resurs gruppen. Det innebär att logg frågor bara innehåller data från den resurs gruppen. Om du vill köra en fråga som innehåller data från andra resurser eller data från andra Azure-tjänster väljer du **loggar** på **Azure Monitor** -menyn. Mer information finns i [logg frågans omfång och tidsintervall i Azure Monitor Log Analytics](../../azure-monitor/logs/scope.md) .

Använd de här frågorna för att hjälpa dig att övervaka dina Azure Storage-konton:

* För att visa en lista över de 10 vanligaste felen under de senaste tre dagarna.

    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* För att visa de 10 främsta åtgärder som orsakade flest fel under de senaste tre dagarna.

    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* För att visa en lista över de 10 främsta åtgärderna med längsta svars tid från slut punkt till slut punkt under de senaste tre dagarna.

    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* För att visa en lista över alla åtgärder som orsakade begränsnings fel på Server sidan under de senaste tre dagarna.

    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* För att visa en lista över alla begär Anden med anonym åtkomst under de senaste tre dagarna.

    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* För att skapa ett cirkel diagram med åtgärder som använts under de senaste tre dagarna.
    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>Vanliga frågor

**Stöder Azure Storage mått för Managed Disks eller ohanterade diskar?**

Nej. Azure Compute stöder mått på diskar. Mer information finns i [per disk mått för hanterade och ohanterade diskar](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

## <a name="next-steps"></a>Nästa steg

- En referens för loggar och mått som skapats av Azure Table Storage finns i [Azure Table Storage övervaknings data Reference](monitor-table-storage-reference.md).
- Mer information om övervakning av Azure-resurser finns i [övervaka Azure-resurser med Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md).
- Mer information om hur du migrerar mått finns i [Azure Storage metriska migreringar](../common/storage-metrics-migration.md).