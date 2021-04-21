---
title: Skapa diagnostikinställningar för att skicka plattformsloggar och mått till olika målplatser
description: Skicka Azure Monitor plattformsmått och loggar till Azure Monitor loggar, Azure Storage eller Azure Event Hubs med hjälp av en diagnostikinställning.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 60ac56cfda026871afa1725bbd54625b7ce7585e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789203"
---
# <a name="create-diagnostic-settings-to-send-platform-logs-and-metrics-to-different-destinations"></a>Skapa diagnostikinställningar för att skicka plattformsloggar och mått till olika målplatser
[Plattformsloggar](./platform-logs-overview.md) i Azure, inklusive Azure-aktivitetsloggar och resursloggar, ger detaljerad diagnostik- och granskningsinformation för Azure-resurser och den Azure-plattform som de är beroende av. [Plattformsmått](./data-platform-metrics.md) samlas in som standard och lagras vanligtvis i Azure Monitor för mått. Den här artikeln innehåller information om hur du skapar och konfigurerar diagnostikinställningar för att skicka plattformsmått och plattformsloggar till olika mål.

> [!IMPORTANT]
> Innan du skapar en diagnostikinställning för aktivitetsloggen bör du först inaktivera en äldre konfiguration. Mer [information finns i Äldre samlingsmetoder.](../essentials/activity-log.md#legacy-collection-methods)

Varje Azure-resurs kräver en egen diagnostikinställning som definierar följande kriterier:

- Kategorier av loggar och måttdata som skickas till de mål som definierats i inställningen. De tillgängliga kategorierna kan variera för olika resurstyper.
- En eller flera destinationer att skicka loggarna till. Aktuella destinationer är Log Analytics-arbetsyta, Event Hubs och Azure Storage.

En enda diagnostikinställning kan inte definiera fler än ett av vart och ett av destinationerna. Om du vill skicka data till fler än en av en viss typ av mål (till exempel två olika Log Analytics-arbetsytor) skapar du flera inställningar. Varje resurs kan ha upp till fem diagnostiska inställningar.

Följande video visar hur du dirigerar plattformsloggar med diagnostikinställningar.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AvVO]

> [!NOTE]
> [Plattformsmått](./metrics-supported.md) skickas automatiskt till [Azure Monitor Mått](./data-platform-metrics.md). Diagnostikinställningar kan användas för att skicka mått för vissa Azure-tjänster till Azure Monitor loggar för analys med andra övervakningsdata med hjälp av loggfrågor [med](../logs/log-query-overview.md) vissa begränsningar. 
>  
>  
> Det går för närvarande inte att skicka flerdimensionella mätvärden via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden. *Exempel:* Måttet "IOReadBytes" på en Blockkedja kan utforskas och visas i diagram på nodnivå. Men när det exporteras via diagnostikinställningar representerar det exporterade måttet alla lästa byte för alla noder. På grund av interna begränsningar kan inte alla mått exporteras till Azure Monitor Loggar/Log Analytics. Mer information finns i listan [över exporterbara mått.](./metrics-supported-export-diagnostic-settings.md) 
>  
>  
> För att komma runt dessa begränsningar för specifika mått föreslår vi att du manuellt extraherar dem med hjälp av [Metrics REST API](/rest/api/monitor/metrics/list) och importerar dem till Azure Monitor-loggar med [hjälp av API:Azure Monitor Data Collector](../logs/data-collector-api.md).  


## <a name="destinations"></a>Mål
Plattformsloggar och mått kan skickas till målen i följande tabell. 

| Mål | Description |
|:---|:---|
| [Log Analytics-arbetsyta](../logs/design-logs-deployment.md) | Genom att skicka loggar och mått till en Log Analytics-arbetsyta kan du analysera dem med andra övervakningsdata som samlas in av Azure Monitor med hjälp av kraftfulla loggfrågor och även använda andra Azure Monitor-funktioner som aviseringar och visualiseringar. |
| [Event Hubs](../../event-hubs/index.yml) | Genom att skicka loggar och Event Hubs kan du strömma data till externa system, till exempel SIEM från tredje part och andra log analytics-lösningar.  |
| [Azure Storage-konto](../../storage/blobs/index.yml) | Arkivering av loggar och mått till ett Azure Storage-konto är användbart för granskning, statisk analys eller säkerhetskopiering. Jämfört med Azure Monitor-loggar och en Log Analytics-arbetsyta är Azure-lagring billigare och loggar kan förvaras där på obestämd tid.  |


### <a name="destination-requirements"></a>Målkrav

Alla mål för diagnostikinställningen måste skapas innan du skapar diagnostikinställningarna. Målet behöver inte finnas i samma prenumeration som resursen som skickar loggar så länge användaren som konfigurerar inställningen har lämplig Azure RBAC-åtkomst till båda prenumerationerna. Följande tabell innehåller unika krav för varje mål, inklusive eventuella regionala begränsningar.

| Mål | Krav |
|:---|:---|
| Log Analytics-arbetsyta | Arbetsytan behöver inte finnas i samma region som den resurs som övervakas.|
| Händelsehubbar | Principen för delad åtkomst för namnområdet definierar de behörigheter som strömningsmekanismen har. Strömma till Event Hubs kräver behörigheterna Hantera, Skicka och Lyssna. Om du vill uppdatera diagnostikinställningen så att den inkluderar strömning måste du ha ListKey-behörighet för den Event Hubs auktoriseringsregeln.<br><br>Händelsehubbens namnområde måste finnas i samma region som resursen som övervakas om resursen är regional. |
| Azure Storage-konto | Du bör inte använda ett befintligt lagringskonto som har andra icke-övervakningsdata som lagras där, så att du bättre kan styra åtkomsten till data. Om du arkiverar aktivitetsloggen och resursloggarna tillsammans kan du välja att använda samma lagringskonto för att lagra alla övervakningsdata på en central plats.<br><br>Om du vill skicka data till oföränderlig lagring anger du den oföränderliga principen för lagringskontot enligt beskrivningen i Ange och hantera oföränderlighetsprinciper [för Blob Storage](../../storage/blobs/storage-blob-immutability-policies-manage.md). Du måste följa alla steg i den här artikeln, inklusive att aktivera skrivningar till skyddade tilläggsblobar.<br><br>Lagringskontot måste finnas i samma region som den resurs som övervakas om resursen är regional. |

> [!NOTE]
> Azure Data Lake Storage Gen2-konton stöds för närvarande inte som mål för diagnostikinställningar även om de visas som ett giltigt alternativ i Azure-portalen.

> [!NOTE]
> Azure Monitor (diagnostikinställningar) kan inte komma åt Event Hubs resurser när virtuella nätverk är aktiverade. Du måste aktivera tillåt betrodda Microsoft-tjänster att kringgå den här brandväggsinställningen i Händelsehubb, så att tjänsten Azure Monitor (diagnostikinställningar) beviljas åtkomst till dina Event Hubs resurser. 


## <a name="create-in-azure-portal"></a>Skapa i Azure-portalen

Du kan konfigurera diagnostikinställningar i Azure Portal antingen från Azure Monitor eller från menyn för resursen.

1. Var du konfigurerar diagnostikinställningar i Azure Portal beror på resursen.

   - För en enskild resurs klickar du **på Diagnostikinställningar** **under** Övervaka på resursens meny.

        ![Skärmbild av avsnittet Övervakning på en resursmeny i Azure Portal diagnostikinställningar markerade.](media/diagnostic-settings/menu-resource.png)

   - För en eller flera resurser klickar du **på Diagnostikinställningar** under Inställningar Azure Monitor på menyn och klickar sedan på resursen. 

        ![Skärmbild av avsnittet Inställningar i menyn Azure Monitor med Diagnostikinställningar markerade.](media/diagnostic-settings/menu-monitor.png)

   - För aktivitetsloggen klickar du **på Aktivitetslogg** i **Azure Monitor** och sedan **på Diagnostikinställningar.** Se till att inaktivera alla äldre konfigurationer för aktivitetsloggen. Mer [information finns i Inaktivera](./activity-log.md#legacy-collection-methods) befintliga inställningar.

        ![Skärmbild av Azure Monitor med Aktivitetslogg valt och Diagnostikinställningar markerade i Monitor-Activity loggens menyrad.](media/diagnostic-settings/menu-activity-log.png)

2. Om det inte finns några inställningar för resursen som du har valt uppmanas du att skapa en inställning. Klicka på **Lägg till diagnostikinställning**.

   ![Lägg till diagnostikinställning – inga befintliga inställningar](media/diagnostic-settings/add-setting.png)

   Om det finns befintliga inställningar för resursen visas en lista över inställningar som redan har konfigurerats. Klicka **antingen på Lägg till diagnostikinställning** för att lägga till en ny **inställning eller redigera** inställningen för att redigera en befintlig. Varje inställning kan inte ha fler än en av varje måltyp.

   ![Lägg till diagnostikinställning – befintliga inställningar](media/diagnostic-settings/edit-setting.png)

3. Ge inställningen ett namn om den inte redan har ett.

    ![Lägga till diagnostikinställning](media/diagnostic-settings/setting-new-blank.png)

4. **Kategoriinformation (vad som ska dirigeras)** – Markera kryssrutan för varje kategori av data som du vill skicka till mål som anges senare. Listan över kategorier varierar för varje Azure-tjänst.

     - **AllMetrics** dirigerar en resurs plattformsmått till Azure Logs Store, men i loggformulär. Dessa mått skickas vanligtvis bara till Azure Monitor tidsseriedatabasen för mått. Genom att skicka dem Azure Monitor Logs Store (som är sökbart via Log Analytics) kan du integrera dem i frågor som söker i andra loggar. Det här alternativet kanske inte är tillgängligt för alla resurstyper. När det stöds visar [Azure Monitor mått som stöds](./metrics-supported.md) vilka mått som samlas in för vilka resurstyper.

       > [!NOTE]
       > Se begränsning för routningsmått för att Azure Monitor loggar tidigare i den här artikeln.  


     - **Loggar** visar de olika tillgängliga kategorierna beroende på resurstyp. Kontrollera alla kategorier som du vill dirigera till ett mål.

5. **Målinformation** – Markera kryssrutan för varje mål. När du markerar varje ruta visas alternativ som gör att du kan lägga till ytterligare information.

      ![Skicka till Log Analytics eller Event Hubs](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** – Ange prenumerationen och arbetsytan.  Om du inte har en arbetsyta måste du skapa [en innan du fortsätter.](../logs/quick-create-workspace.md)

    1. **Händelsehubbb** – Ange följande kriterier:
       - Den prenumeration som händelsehubben ingår i
       - Namnområdet för händelsehubben – Om du inte har ett namnområde än måste [du skapa ett](../../event-hubs/event-hubs-create.md)
       - Ett namn på en händelsehubb (valfritt) som alla data ska skickas till. Om du inte anger något namn skapas en händelsehubb för varje loggkategori. Om du skickar flera kategorier kan du ange ett namn som begränsar antalet skapade händelsehubbbar. Mer [Azure Event Hubs finns i Azure Event Hubs och gränser.](../../event-hubs/event-hubs-quotas.md)
       - En händelsehubbprincip (valfritt) En princip definierar de behörigheter som strömningsmekanismen har. Mer information finns i [Event-hubs-features](../../event-hubs/event-hubs-features.md#publisher-policy).

    1. **Lagring** – Välj prenumeration, lagringskonto och kvarhållningsprincip.

        ![Skicka till lagring](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Överväg att ange bevarandeprincipen till 0 och manuellt ta bort dina data från lagringen med hjälp av ett schemalagt jobb för att undvika eventuell förvirring i framtiden.
        >
        > Om du använder lagring för arkivering vill du normalt ha data i mer än 365 dagar. För det andra, om du väljer en bevarandeprincip som är större än 0, kopplas förfallodatumet till loggarna vid tidpunkten för lagringen. Du kan inte ändra datumet för loggarna när de har lagrats.
        >
        > Om du till exempel anger kvarhållningsprincipen för *WorkflowRuntime* till 180 dagar och sedan 24 timmar senare anger den till 365 dagar, tas loggarna som lagras under de första 24 timmarna bort automatiskt efter 180 dagar, medan alla efterföljande loggar av den typen tas bort automatiskt efter 365 dagar. Om du ändrar bevarandeprincipen senare blir inte de första 24 timmarna loggar kvar i 365 dagar.

6. Klicka på **Spara**.

Efter en liten stund visas den nya inställningen i listan med inställningar för den här resursen och loggar strömmas till de angivna målen när nya händelsedata genereras. Det kan ta upp till 15 minuter mellan när en händelse genereras och när den visas [på en Log Analytics-arbetsyta](../logs/data-ingestion-time.md).

## <a name="create-using-powershell"></a>Skapa med PowerShell

Använd [cmdleten Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) för att skapa en diagnostikinställning med [Azure PowerShell](../powershell-samples.md). Se dokumentationen för den här cmdleten för beskrivningar av dess parametrar.

> [!IMPORTANT]
> Du kan inte använda den här metoden för Azure-aktivitetsloggen. Använd i stället [Skapa diagnostikinställning i Azure Monitor med](./resource-manager-diagnostic-settings.md) en Resource Manager för att skapa en Resource Manager och distribuera den med PowerShell.

Följande är ett exempel på en PowerShell-cmdlet för att skapa en diagnostikinställning med alla tre mål.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>Skapa med Azure CLI

Använd kommandot [az monitor diagnostic-settings create för](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) att skapa en diagnostikinställning med Azure [CLI](/cli/azure/monitor). Beskrivningar av dess parametrar finns i dokumentationen för det här kommandot.

> [!IMPORTANT]
> Du kan inte använda den här metoden för Azure-aktivitetsloggen. Använd i stället Skapa diagnostikinställning i Azure Monitor med en [Resource Manager mall](./resource-manager-diagnostic-settings.md) för att skapa en Resource Manager och distribuera den med CLI.

Följande är ett CLI-exempelkommando för att skapa en diagnostikinställning med alla tre mål. Syntaxen är något annorlunda beroende på din klient.

# <a name="cmd"></a>[Cmd](#tab/CMD)
```azurecli
az monitor diagnostic-settings create  ^
--name KeyVault-Diagnostics ^
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault ^
--logs    "[{""category"": ""AuditEvent"",""enabled"": true}]" ^
--metrics "[{""category"": ""AllMetrics"",""enabled"": true}]" ^
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount ^
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace ^
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
# <a name="powershell"></a>[PowerShell](#tab/PowerShell)
```azurecli
az monitor diagnostic-settings create  `
--name KeyVault-Diagnostics `
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault `
--logs    '[{""category"": ""AuditEvent"",""enabled"": true}]' `
--metrics '[{""category"": ""AllMetrics"",""enabled"": true}]' `
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount `
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace `
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
# <a name="bash"></a>[Bash](#tab/Bash)
```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
---

## <a name="create-using-resource-manager-template"></a>Skapa med Resource Manager mall
Se [Resource Manager för diagnostikinställningar](./resource-manager-diagnostic-settings.md) i Azure Monitor för att skapa eller uppdatera diagnostikinställningar med en Resource Manager mall.

## <a name="create-using-rest-api"></a>Skapa med REST-API
Se [Diagnostikinställningar för](/rest/api/monitor/diagnosticsettings) att skapa eller uppdatera diagnostikinställningar med [hjälp av Azure Monitor REST API](/rest/api/monitor/).

## <a name="create-using-azure-policy"></a>Skapa med Azure Policy
Eftersom en diagnostikinställning måste skapas för varje Azure-resurs kan Azure Policy användas för att automatiskt skapa en diagnostikinställning när varje resurs skapas. Se [Distribuera Azure Monitor skala med hjälp Azure Policy](../deploy-scale.md) mer information.

## <a name="metric-category-is-not-supported-error"></a>Fel om måttkategori stöds inte
När du distribuerar en diagnostikinställning visas följande felmeddelande:

   "Måttkategori *" xxxx*" stöds inte"

Exempel: 

   "Måttkategorin "ActionsFailed" stöds inte"

där distributionen tidigare lyckades. 

Problemet uppstår när du använder en Resource Manager mall, diagnostikinställningarna för REST API, Azure CLI eller Azure PowerShell. Diagnostikinställningar som skapas via Azure Portal påverkas inte eftersom endast de kategorinamn som stöds visas.

Problemet orsakas av en nyligen genomförd ändring i det underliggande API:et. Andra måttkategorier än "AllMetrics" stöds inte och har aldrig varit förutom några få mycket specifika Azure-tjänster. Tidigare ignorerades andra kategorinamn när du distribuerade en diagnostikinställning. Det Azure Monitor här kategorierna omdirigerades helt enkelt till "AllMetrics".  Från och med februari 2021 uppdaterades backend för att specifikt bekräfta att den angivna måttkategorin är korrekt. Den här ändringen har orsakat att vissa distributioner misslyckas.

Om du får det här felet uppdaterar du dina distributioner så att de ersätter eventuella namn på måttkategorier med "AllMetrics" för att åtgärda problemet. Om distributionen tidigare har lagt till flera kategorier bör endast en med referensen "AllMetrics" behållas. Om problemet kvarstår kontaktar du Azure-supporten via Azure Portal. 



## <a name="next-steps"></a>Nästa steg

- [Läs mer om Azure-plattformsloggar](./platform-logs-overview.md)
