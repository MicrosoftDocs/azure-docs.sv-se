---
title: Azure Monitor PowerShell-exempel
description: Använd PowerShell för att få åtkomst till Azure Monitor funktioner som autoskalning, aviseringar, Webhooks och sökning av aktivitets loggar.
ms.subservice: ''
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 2/14/2018
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9f43531e6b81b9bd4a294d70e9b8091463cf507a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100627970"
---
# <a name="azure-monitor-powershell-samples"></a>Azure Monitor PowerShell-exempel
Den här artikeln visar exempel på PowerShell-kommandon som hjälper dig att komma åt Azure Monitor-funktioner.

> [!NOTE]
> Azure Monitor är det nya namnet för det som kallades "Azure Insights" fram till 25 september 2016. Dock innehåller namn områdena och därför innehåller följande kommandon fortfarande Word *Insights*.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-powershell"></a>Konfigurera PowerShell
Om du inte redan har gjort det konfigurerar du PowerShell att köras på datorn. Mer information finns i [så här installerar och konfigurerar du PowerShell](/powershell/azure/).

## <a name="examples-in-this-article"></a>Exempel i den här artikeln
I exemplen i artikeln visas hur du kan använda Azure Monitor-cmdletar. Du kan också granska hela listan med Azure Monitor PowerShell-cmdletar i [Azure Monitor (insikter)-cmdletar](/powershell/module/az.applicationinsights).

## <a name="sign-in-and-use-subscriptions"></a>Logga in och Använd prenumerationer
Börja med att logga in på din Azure-prenumeration.

```powershell
Connect-AzAccount
```

Du ser en inloggnings skärm. När du har loggat in på kontot visas TenantID och standard prenumerations-ID. Alla Azure-cmdletar fungerar i kontexten för din standard prenumeration. Använd följande kommando om du vill visa en lista över prenumerationer som du har åtkomst till:

```powershell
Get-AzSubscription
```

Använd följande kommando för att se din arbets kontext (vilken prenumeration dina kommandon körs mot):

```powershell
Get-AzContext
```
Om du vill ändra din arbets kontext till en annan prenumeration använder du följande kommando:

```powershell
Set-AzContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log"></a>Hämta aktivitets logg
Använd cmdleten [Get-AzLog](/powershell/module/az.monitor/get-azlog) .  Här följer några vanliga exempel. Aktivitetsloggen innehåller åtgärder från de senaste 90 dagarna. Om du använder datum före den här tiden resulterar det i ett fel meddelande.  

Se vad aktuellt datum/tid är för att kontrol lera vilka tider som ska användas i kommandona nedan:
```powershell
Get-Date
```

Hämta logg poster från denna tid/datum för att presentera:

```powershell
Get-AzLog -StartTime 2019-03-01T10:30
```

Hämta logg poster mellan ett tids-/datum intervall:

```powershell
Get-AzLog -StartTime 2019-01-01T10:30 -EndTime 2015-01-01T11:30
```

Hämta logg poster från en angiven resurs grupp:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Hämta logg poster från en speciell resurs leverantör mellan ett tids-/datum intervall:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Hämta alla logg poster med en angiven anropare:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Följande kommando hämtar de senaste 1000 händelserna från aktivitets loggen:

```powershell
Get-AzLog -MaxRecord 1000
```

`Get-AzLog` har stöd för många andra parametrar. `Get-AzLog`Mer information finns i referensen.

> [!NOTE]
> `Get-AzLog` innehåller endast 15 dagars historik. Med parametern **-MaxRecords** kan du köra frågor mot de senaste N händelserna längre än 15 dagar. Om du vill komma åt händelser som är äldre än 15 dagar använder du REST-API:et eller SDK:t (C#-exempel med SDK:t). Om du inte anger ett värde för **StartTime** är standardvärdet **EndTime** minus en timme. Om du inte anger ett värde för **EndTime** är standardvärdet aktuell tidpunkt. Alla tider anges i UTC-tid.
> 
> 

## <a name="retrieve-alerts-history"></a>Hämta aviserings historik
Om du vill visa alla aviserings händelser kan du fråga Azure Resource Manager loggar med hjälp av följande exempel.

```powershell
Get-AzLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Om du vill visa historiken för en speciell aviserings regel kan du använda `Get-AzAlertHistory` cmdleten och skicka aviserings regelns resurs-ID.

```powershell
Get-AzAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

`Get-AzAlertHistory`Cmdleten stöder olika parametrar. Mer information finns i [Get-AlertHistory](/previous-versions/azure/mt282453(v=azure.100)).

## <a name="retrieve-information-on-alert-rules"></a>Hämta information om aviserings regler
Alla följande kommandon fungerar på en resurs grupp med namnet "montest".

Visa alla egenskaper för aviserings regeln:

```powershell
Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Hämta alla aviseringar för en resurs grupp:

```powershell
Get-AzAlertRule -ResourceGroup montest
```

Hämta alla varnings regler för en mål resurs. Till exempel alla varnings regler som har angetts på en virtuell dator.

```powershell
Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzAlertRule` stöder andra parametrar. Mer information finns i [Get-AlertRule](/previous-versions/azure/mt282459(v=azure.100)) .

## <a name="create-metric-alerts"></a>Skapa måttaviseringar
Du kan använda `Add-AlertRule` cmdleten för att skapa, uppdatera eller inaktivera en varnings regel.

Du kan skapa e-post och webhook-egenskaper med respektive  `New-AzAlertRuleEmail` `New-AzAlertRuleWebhook` . I aviserings regelns cmdlet tilldelar du dessa egenskaper som åtgärder till egenskapen **åtgärder** för varnings regeln.

I följande tabell beskrivs de parametrar och värden som används för att skapa en avisering med hjälp av ett mått.

| parameter | värde |
| --- | --- |
| Name |simpletestdiskwrite |
| Plats för den här aviserings regeln |East US |
| ResourceGroup |montest |
| TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| MetricName för den avisering som skapas |\PhysicalDisk (_Total) \ disk skrivningar/s. Se `Get-MetricDefinitions` cmdleten om hur du hämtar de exakta mått namnen |
| operator |GreaterThan |
| Tröskelvärde (antal/SEK i för det här måttet) |1 |
| WindowSize (hh: mm: SS-format) |00:05:00 |
| aggregator (statistik för måttet, som använder genomsnitts antal, i det här fallet) |Genomsnitt |
| anpassade e-postmeddelanden (sträng mat ris) |'foo@example.com','bar@example.com' |
| Skicka e-post till ägare, deltagare och läsare |-SendToServiceOwners |

Skapa en e-poståtgärd

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
```

Skapa en webhook-åtgärd

```powershell
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Skapa varnings regeln på CPU%-måttet på en klassisk virtuell dator

```powershell
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Action $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Hämta varnings regeln

```powershell
Get-AzAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

I Lägg till aviserings-cmdlet uppdateras även regeln om det redan finns en varnings regel för de tilldelade egenskaperna. Om du vill inaktivera en varnings regel inkluderar du parametern **-DisableRule**.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Hämta en lista över tillgängliga mått för aviseringar
Du kan använda `Get-AzMetricDefinition` cmdleten för att visa en lista över alla mått för en bestämd resurs.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id>
```

I följande exempel skapas en tabell med mått namnet och enheten för den.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

En fullständig lista över tillgängliga alternativ för `Get-AzMetricDefinition` finns på [Get-MetricDefinitions](/previous-versions/azure/mt282458(v=azure.100)).

## <a name="create-and-manage-activity-log-alerts"></a>Skapa och hantera aktivitets logg aviseringar
Du kan använda `Set-AzActivityLogAlert` cmdleten för att ange en aktivitets logg avisering. En aktivitets logg avisering kräver att du först definierar dina villkor som ord listor och sedan skapar en avisering som använder dessa villkor.

```powershell

$condition1 = New-AzActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

De ytterligare webhook-egenskaperna är valfria. Du kan få tillbaka innehållet i en aktivitets logg avisering med hjälp av `Get-AzActivityLogAlert` .

## <a name="create-and-manage-autoscale-settings"></a>Skapa och hantera inställningar för autoskalning

> [!NOTE] 
> För Cloud Services (Microsoft. ClassicCompute) stöder autoskalning en tids kornig het på 5 minuter (PT5M). För de andra tjänsterna kan autoskalning stödja en tids kornig het på minst 1 minut (PT1M)

En resurs (en webbapp, virtuell dator, moln tjänst eller virtuell dators skalnings uppsättning) kan bara ha en inställning för autoskalning som kon figurer ATS för den.
Alla inställningar för autoskalning kan dock ha flera profiler. Till exempel en för en prestanda-baserad skalnings profil och en andra för en schema-baserad profil. Varje profil kan ha flera konfigurerade regler. Mer information om autoskalning finns i [så här skalar du ett program i autoskalning](../cloud-services/cloud-services-how-to-scale-portal.md).

Här är de steg du ska använda:

1. Skapa regel (er).
2. Skapa profil (er) mappar de regler som du skapade tidigare till profilerna.
3. Valfritt: skapa meddelanden för autoskalning genom att konfigurera webhook och e-postegenskaper.
4. Skapa en inställning för autoskalning med ett namn på mål resursen genom att mappa de profiler och meddelanden som du skapade i föregående steg.

I följande exempel visas hur du kan skapa en inställning för autoskalning för en skalnings uppsättning för virtuella datorer för ett Windows-operativsystem baserat på användnings måttet för processor användning.

Börja med att skapa en regel för att skala ut, med en ökning av antalet instanser.

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Skapa sedan en regel för skalning i, med en minskning av antalet instanser.

```powershell
$rule2 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Skapa sedan en profil för reglerna.

```powershell
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Skapa en webhook-egenskap.

```powershell
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Skapa meddelande egenskapen för den automatiska skalnings inställningen, inklusive e-post och webhook som du skapade tidigare.

```powershell
$notification1= New-AzAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Skapa slutligen den automatiska skalnings inställningen för att lägga till den profil som du skapade tidigare. 

```powershell
Add-AzAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Mer information om hur du hanterar inställningar för autoskalning finns i [Get-AutoscaleSetting](/previous-versions/azure/mt282461(v=azure.100)).

## <a name="autoscale-history"></a>Autoskalning-historik
I följande exempel visas hur du kan visa senaste automatiska skalnings-och aviserings händelser. Använd aktivitets loggs ökningen för att Visa autoskalning-historiken.

```powershell
Get-AzLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Du kan använda `Get-AzAutoScaleHistory` cmdleten för att hämta historik för autoskalning.

```powershell
Get-AzAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Mer information finns i [Get-AutoscaleHistory](/previous-versions/azure/mt282464(v=azure.100)).

### <a name="view-details-for-an-autoscale-setting"></a>Visa information om en autoskalningsinställning
Du kan använda `Get-Autoscalesetting` cmdleten för att hämta mer information om den automatiska skalnings inställningen.

I följande exempel visas information om alla inställningar för autoskalning i resurs gruppen "myrg1".

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

I följande exempel visas information om alla inställningar för autoskalning i resurs gruppen "myrg1" och särskilt den automatiska skalnings inställningen med namnet "MyScaleVMSSSetting".

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Ta bort en autoskalningsinställning
Du kan använda `Remove-Autoscalesetting` cmdleten för att ta bort en inställning för autoskalning.

```powershell
Remove-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Hantera logg profiler för aktivitets loggen
Du kan skapa en *logg profil* och exportera data från aktivitets loggen till ett lagrings konto och du kan konfigurera datakvarhållning av data. Alternativt kan du också strömma data till händelsehubben. Den här funktionen är för närvarande en för hands version och du kan bara skapa en logg profil per prenumeration. Du kan använda följande cmdletar med din aktuella prenumeration för att skapa och hantera logg profiler. Du kan också välja en viss prenumeration. Även om PowerShell är standardvärdet för den aktuella prenumerationen kan du alltid ändra det med `Set-AzContext` . Du kan konfigurera aktivitets loggen för att dirigera data till valfritt lagrings konto eller Händelsehubben i den prenumerationen. Data skrivs som BLOB-filer i JSON-format.

### <a name="get-a-log-profile"></a>Hämta en logg profil
Använd cmdleten för att hämta dina befintliga logg profiler `Get-AzLogProfile` .

### <a name="add-a-log-profile-without-data-retention"></a>Lägg till en logg profil utan data kvarhållning
```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Ta bort en logg profil
```powershell
Remove-AzLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Lägg till en logg profil med datakvarhållning
Du kan ange egenskapen **-RetentionInDays** med antalet dagar, som ett positivt heltal, där data bevaras.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Lägg till logg profil med kvarhållning och EventHub
Förutom att dirigera dina data till lagrings kontot kan du också strömma det till en Event Hub. I den här för hands versionen är lagrings konto konfigurationen obligatorisk men konfiguration av Händelsehubben är valfritt.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Konfigurera diagnostikloggar
Många Azure-tjänster ger ytterligare loggar och telemetri som kan göra något av följande: 
 - konfigureras för att spara data i ditt Azure Storage-konto
 - skickat till Event Hubs
 - skickas till en Log Analytics-arbetsyta. 

Åtgärden kan bara utföras på en resurs nivå. Lagrings kontot eller händelsehubben måste finnas i samma region som mål resursen där diagnostikinställningar är konfigurerat.

### <a name="get-diagnostic-setting"></a>Hämta diagnostisk inställning
```powershell
Get-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Inaktivera diagnostisk inställning

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Aktivera diagnostisk inställning utan kvarhållning

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Aktivera diagnostisk inställning med kvarhållning

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Aktivera diagnostisk inställning med kvarhållning för en viss logg kategori

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Aktivera diagnostisk inställning för Event Hubs

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Aktivera diagnostisk inställning för Log Analytics

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Observera att egenskapen WorkspaceId tar arbets ytans *resurs-ID* . Du kan hämta resurs-ID för din Log Analytics-arbetsyta med hjälp av följande kommando:

```powershell
(Get-AzOperationalInsightsWorkspace).ResourceId

```

Dessa kommandon kan kombineras för att skicka data till flera mål.
