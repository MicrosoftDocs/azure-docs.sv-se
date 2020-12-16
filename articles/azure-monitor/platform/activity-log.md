---
title: Azure-aktivitetslogg
description: Visa Azures aktivitets logg och skicka den till Azure Monitor loggar, Azure Event Hubs och Azure Storage.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 00e264cea34c7c3e7223b47217ecf5a59b76ba41
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592474"
---
# <a name="azure-activity-log"></a>Azure-aktivitetslogg
Aktivitetsloggen är en [plattformslogg](platform-logs-overview.md) i Azure som ger inblick i händelser på prenumerationsnivå. Bland annat loggas information som när en resurs ändras eller när en virtuell dator startas. Du kan visa aktivitets loggen i Azure Portal eller hämta poster med PowerShell och CLI. Om du vill ha ytterligare funktioner bör du skapa en diagnostisk inställning för att skicka aktivitets loggen till [Azure Monitor loggar](data-platform-logs.md), till Azure Event Hubs att vidarebefordra utanför Azure eller till Azure Storage för arkivering. Den här artikeln innehåller information om hur du visar aktivitets loggen och skickar den till olika destinationer.

Information om hur du skapar en diagnostisk inställning finns i [skapa diagnostikinställningar för att skicka plattforms loggar och mått till olika destinationer](diagnostic-settings.md) .

> [!NOTE]
> Posterna i aktivitetsloggen skapas i systemet och kan inte ändras eller tas bort.

## <a name="view-the-activity-log"></a>Visa aktivitets loggen
Du kan öppna aktivitetsloggen från de flesta menyer i Azure-portalen. Startfiltret beror på vilken meny du öppnar loggen från. Om du öppnar den från **Monitor** -menyn visas bara det enda filtret i prenumerationen. Om du öppnar den från en resurs meny, anges filtret till den resursen. Du kan alltid ändra filtret om du vill visa alla andra poster. Klicka på **Lägg till filter** om du vill lägga till ytterligare egenskaper till filtret.

![Visa aktivitets logg](./media/activity-logs-overview/view-activity-log.png)

En beskrivning av aktivitets loggs kategorier finns i [händelse schema för Azure aktivitets logg](activity-log-schema.md#categories).

### <a name="view-change-history"></a>Visa ändrings historik

För vissa händelser kan du Visa ändrings historiken, som visar vilka ändringar som skett under den händelse tiden. Välj en händelse från aktivitets loggen som du vill se djupare i. Välj fliken **ändrings historik (förhands granskning)** om du vill visa alla associerade ändringar med händelsen.

![Lista över ändrings historik för en händelse](media/activity-logs-overview/change-history-event.png)

Om det finns några associerade ändringar i händelsen visas en lista över ändringar som du kan välja. Då öppnas sidan **ändrings historik (förhands granskning)** . På den här sidan visas ändringarna i resursen. I följande exempel kan du inte bara se att den virtuella datorn har ändrat storlek, men att den tidigare storleken på den virtuella datorn var före ändringen och vad den ändrades till. Läs mer om ändrings historik i [Hämta resurs ändringar](../../governance/resource-graph/how-to/get-resource-changes.md).

![Sidan ändrings historik visar skillnader](media/activity-logs-overview/change-history-event-details.png)


### <a name="other-methods-to-retrieve-activity-log-events"></a>Andra metoder för att hämta aktivitets logg händelser
Du kan också komma åt aktivitets logg händelser med följande metoder.

- Använd cmdleten [Get-AzLog](/powershell/module/az.monitor/get-azlog) för att hämta aktivitets loggen från PowerShell. Se [Azure Monitor PowerShell-exempel](../samples/powershell-samples.md#retrieve-activity-log).
- Använd [AZ övervaka aktivitet – logg](/cli/azure/monitor/activity-log) för att hämta aktivitets loggen från cli.  Se [Azure Monitor CLI-exempel](../samples/cli-samples.md#view-activity-log).
- Använd [Azure Monitor REST API](/rest/api/monitor/) för att hämta aktivitets loggen från en rest-klient. 


## <a name="send-to-log-analytics-workspace"></a>Skicka till Log Analytics-arbetsytan
 Skicka aktivitets loggen till en Log Analytics-arbetsyta för att aktivera funktionerna i [Azure Monitor loggar](data-platform-logs.md) som innehåller följande:

- Korrelera aktivitets logg data med andra övervaknings data som samlas in av Azure Monitor.
- Konsolidera logg poster från flera Azure-prenumerationer och-klienter till en plats för analys av varandra.
- Använd logg frågor för att utföra komplexa analyser och få djupgående insikter om aktivitets logg poster.
- Använd logg aviseringar med aktivitets poster som tillåter mer komplex aviserings logik.
- Lagra aktivitets logg poster under längre tid än 90 dagar.
- Inga data inmatnings avgifter för aktivitets logg data som lagras i en Log Analytics-arbetsyta.
- Inga avgifter för datakvarhållning till 90 dagar för aktivitets logg data som lagras i en Log Analytics-arbetsyta.

[Skapa en diagnostisk inställning](diagnostic-settings.md) för att skicka aktivitets loggen till en Log Analytics-arbetsyta. Du kan skicka aktivitets loggen från en enskild prenumeration till upp till fem arbets ytor. Om du vill samla loggar för flera klientorganisationer behöver du [Azure Lighthouse](../../lighthouse/index.yml).

Aktivitets logg data i en Log Analytics arbets yta lagras i en tabell med namnet *AzureActivity* som du kan hämta med en [logg fråga](../log-query/log-query-overview.md) i [Log Analytics](../log-query/log-analytics-tutorial.md). Strukturen för den här tabellen varierar beroende på vilken [kategori logg posten](activity-log-schema.md)har. En beskrivning av tabell egenskaperna finns i [referens för Azure Monitor-data](/azure/azure-monitor/reference/tables/azureactivity).

Om du till exempel vill visa antalet aktivitets logg poster för varje kategori använder du följande fråga.

```kusto
AzureActivity
| summarize count() by Category
```

Använd följande fråga om du vill hämta alla poster i den administrativa kategorin.

```kusto
AzureActivity
| where Category == "Administrative"
```


## <a name="send-to-azure-event-hubs"></a>Skicka till Azure Event Hubs
Skicka aktivitets loggen till Azure Event Hubs för att skicka poster utanför Azure, till exempel för en tredjeparts-SIEM eller andra Log Analytics-lösningar. Aktivitets logg händelser från Event Hub används i JSON-format med ett `records` element som innehåller posterna i varje nytto Last. Schemat är beroende av kategorin och beskrivs i [schema från lagrings konto och händelse nav](activity-log-schema.md).

Följande är exempel på utdata från Event Hubs för en aktivitets logg:

``` JSON
{
    "records": [
        {
            "time": "2019-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


## <a name="send-to--azure-storage"></a>Skicka till Azure Storage
Skicka aktivitets loggen till ett Azure Storage konto om du vill behålla dina loggdata längre än 90 dagar för granskning, statisk analys eller säkerhets kopiering. Om du bara behöver behålla dina händelser i 90 dagar eller mindre behöver du inte konfigurera arkivering till ett lagrings konto eftersom aktivitets logg händelser behålls i Azure-plattformen för 90 dagar.

När du skickar aktivitets loggen till Azure skapas en lagrings behållare i lagrings kontot så snart en händelse inträffar. Blobarna i behållaren använder följande namngivnings konvention:

```
insights-activity-logs/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

En viss BLOB kan till exempel ha ett namn som liknar följande:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/y=2020/m=06/d=08/h=18/m=00/PT1H.json
```

Varje PT1H.json-blob innehåller en JSON-blob med händelser som inträffade inom den angivna timmen i blob-URL (till exempel h=12). Under den aktuella timmen läggs händelser till i filen PT1H.json allt eftersom de inträffar. Minut värdet (m = 00) är alltid 00, eftersom resurs logg händelser delas upp i enskilda blobbar per timme.

Varje händelse lagras i PT1H.jsi filen med följande format som använder ett gemensamt schema på högsta nivå, men som i övrigt är unikt för varje kategori enligt beskrivningen i  [aktivitets loggens schema](activity-log-schema.md).

``` JSON
{ "time": "2020-06-12T13:07:46.766Z", "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MV-VM-01", "correlationId": "0f0cb6b4-804b-4129-b893-70aeeb63997e", "operationName": "Microsoft.Resourcehealth/healthevent/Updated/action", "level": "Information", "resultType": "Updated", "category": "ResourceHealth", "properties": {"eventCategory":"ResourceHealth","eventProperties":{"title":"This virtual machine is starting as requested by an authorized user or process. It will be online shortly.","details":"VirtualMachineStartInitiatedByControlPlane","currentHealthStatus":"Unknown","previousHealthStatus":"Unknown","type":"Downtime","cause":"UserInitiated"}}}
```


## <a name="legacy-collection-methods"></a>Äldre samlings metoder
I det här avsnittet beskrivs äldre metoder för att samla in aktivitets loggen som användes före diagnostikinställningar. Om du använder dessa metoder bör du överväga att överföra till diagnostikinställningar som ger bättre funktioner och konsekvens med resurs loggar.

### <a name="log-profiles"></a>Logg profiler
Logg profiler är den äldre metoden för att skicka aktivitets loggen till Azure Storage eller Event Hub. Använd följande procedur för att fortsätta arbeta med en logg profil eller inaktivera den för att förbereda för migrering till en diagnostisk inställning.

1. Välj **aktivitets logg** på **Azure Monitor** -menyn i Azure Portal.
3. Klicka på **Diagnostikinställningar**.

   ![Diagnostikinställningar](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Klicka på den lila banderollen för den äldre upplevelsen.

    ![Äldre miljö](media/diagnostic-settings-subscription/legacy-experience.png)



### <a name="configure-log-profile-using-powershell"></a>Konfigurera logg profil med PowerShell

Om det redan finns en logg profil måste du först ta bort den befintliga logg profilen och sedan skapa en ny.

1. Används `Get-AzLogProfile` för att identifiera om det finns en logg profil.  Om det finns en logg profil noterar du egenskapen *namn* .

1. Använd `Remove-AzLogProfile` för att ta bort logg profilen med värdet från egenskapen *Name* .

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Använd `Add-AzLogProfile` för att skapa en ny logg profil:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Egenskap | Krävs | Beskrivning |
    | --- | --- | --- |
    | Name |Yes |Namn på din logg profil. |
    | StorageAccountId |No |Resurs-ID för det lagrings konto där aktivitets loggen ska sparas. |
    | serviceBusRuleId |No |Service Bus regel-ID för det Service Bus namn område som du vill ha händelse hubbar skapade i. Det här är en sträng med formatet: `{service bus resource ID}/authorizationrules/{key name}` . |
    | Plats |Ja |Kommaavgränsad lista över regioner för vilka du vill samla in aktivitets logg händelser. |
    | RetentionInDays |Yes |Antal dagar som händelser ska behållas i lagrings kontot, mellan 1 och 365. Värdet noll lagrar loggarna oändligt. |
    | Kategori |No |Kommaavgränsad lista över händelse kategorier som ska samlas in. Möjliga värden är _Write_, _Delete_ och _Action_. |

### <a name="example-script"></a>Exempelskript
Följande är ett exempel på PowerShell-skript för att skapa en logg profil som skriver aktivitets loggen till både ett lagrings konto och en Event Hub.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -StorageAccountId  $storageAccountId -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Konfigurera logg profil med Azure CLI

Om det redan finns en logg profil måste du först ta bort den befintliga logg profilen och sedan skapa en ny logg profil.

1. Används `az monitor log-profiles list` för att identifiera om det finns en logg profil.
2. Använd `az monitor log-profiles delete --name "<log profile name>` för att ta bort logg profilen med värdet från egenskapen *Name* .
3. Använd `az monitor log-profiles create` för att skapa en ny logg profil:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Egenskap | Krävs | Beskrivning |
    | --- | --- | --- |
    | name |Yes |Namn på din logg profil. |
    | lagrings konto-ID |Yes |Resurs-ID för det lagrings konto som aktivitets loggar ska sparas i. |
    | platser |Yes |Blankstegsavgränsad lista över regioner för vilka du vill samla in aktivitets logg händelser. Du kan visa en lista över alla regioner för din prenumeration med hjälp av `az account list-locations --query [].name` . |
    | antalet |Yes |Antal dagar som händelser ska behållas, mellan 1 och 365. Om värdet är noll lagras loggarna oändligt (för alltid).  Om värdet är noll ska parametern Enabled vara inställd på falskt. |
    |enabled | Yes |Sant eller falskt.  Används för att aktivera eller inaktivera bevarande principen.  Om värdet är true måste parametern Days vara ett värde som är större än 0.
    | kategorier |Yes |Blankstegsavgränsad lista över händelse kategorier som ska samlas in. Möjliga värden är Write, Delete och action. |


### <a name="log-analytics-workspace"></a>Log Analytics-arbetsyta
Den äldre metoden för att skicka aktivitets loggen till en Log Analytics arbets yta ansluter loggen i arbets ytans konfiguration. 

1. Från menyn **Log Analytics arbets ytor** i Azure Portal väljer du arbets ytan för att samla in aktivitets loggen.
1. I avsnittet **data källor för arbets yta** på menyn för arbets ytan väljer du **Azure aktivitets logg**.
1. Klicka på den prenumeration som du vill ansluta.

    ![Skärm bild som visar Log Analytics arbets yta med en Azure aktivitets logg vald.](media/activity-log-collect/workspaces.png)

1. Klicka på **Anslut** för att ansluta aktivitets loggen i prenumerationen till den valda arbets ytan. Om prenumerationen redan är ansluten till en annan arbets yta, klickar du på **Koppla från** först för att koppla bort den.

    ![Anslut arbets ytor](media/activity-log-collect/connect-workspace.png)


Om du vill inaktivera inställningen utför du samma procedur och klickar på **Koppla från** för att ta bort prenumerationen från arbets ytan.

### <a name="data-structure-changes"></a>Data struktur ändringar
Diagnostikinställningar skickar samma data som den äldre metoden som används för att skicka aktivitets loggen med vissa ändringar i strukturen i *AzureActivity* -tabellen.

Kolumnerna i följande tabell är föråldrade i det uppdaterade schemat. De finns fortfarande kvar i *AzureActivity* , men de kommer inte att ha några data. Ersättningen för de här kolumnerna är inte nya, men de innehåller samma data som den föråldrade kolumnen. De har ett annat format, så du kan behöva ändra logg frågor som använder dem. 

| Inaktuell kolumn | Ersättnings kolumn |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> I vissa fall kan värdena i de här kolumnerna anges med versaler. Om du har en fråga där de här kolumnerna ingår ska du använda [operatorn =~](/azure/kusto/query/datatypes-string-operators) så att jämförelsen blir skiftlägesokänslig.

Följande kolumn har lagts till i *AzureActivity* i det uppdaterade schemat:

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-analytics-monitoring-solution"></a>Aktivitetslogganalys övervaknings lösning
Lösningen för övervakning av Azure Log Analytics kommer snart att ersättas och ersätts av en arbets bok med det uppdaterade schemat i arbets ytan Log Analytics. Du kan fortfarande använda lösningen om du redan har den aktive rad, men den kan bara användas om du samlar in aktivitets loggen med hjälp av äldre inställningar. 



### <a name="use-the-solution"></a>Använd lösningen
Övervaknings lösningar nås från **Monitor** -menyn i Azure Portal. Välj **mer** i avsnittet om **insikter** för att öppna **översikts** sidan med lösnings panelerna. I panelen **Azure aktivitets loggar** visas antalet **AzureActivity** -poster i din arbets yta.

![Panel för Azure aktivitets loggar](media/collect-activity-logs/azure-activity-logs-tile.png)


Klicka på panelen **Azure aktivitets loggar** för att öppna vyn **Azure aktivitets loggar** . Vyn innehåller visualiserings delarna i följande tabell. Varje del visar upp till 10 objekt som matchar delarnas kriterier för det angivna tidsintervallet. Du kan köra en logg fråga som returnerar alla matchande poster genom att klicka på **Visa alla** längst ned i delen.

![Instrument panel för Azure aktivitets loggar](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Aktivera lösningen för nya prenumerationer
Du kommer snart inte längre att kunna lägga till aktivitets loggs analys lösningen i din prenumeration med hjälp av Azure Portal. Du kan lägga till den med hjälp av följande procedur med en Resource Manager-mall. 

1. Kopiera följande JSON till en fil med namnet *ActivityLogTemplate*. JSON.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. Distribuera mallen med följande PowerShell-kommandon:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```



## <a name="next-steps"></a>Nästa steg

* [Läs en översikt över plattforms loggar](platform-logs-overview.md)
* [Granska händelse schema för aktivitets logg](activity-log-schema.md)
* [Skapa diagnostisk inställning för att skicka aktivitets loggar till andra destinationer](diagnostic-settings.md)
