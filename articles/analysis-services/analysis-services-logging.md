---
title: Diagnostisk loggning för Azure Analysis Services | Microsoft Docs
description: Beskriver hur du konfigurerar loggning för att övervaka din Azure Analysis Services-server.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2bee856adef1208aabbe65ecd5fd11235579bb82
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582701"
---
# <a name="setup-diagnostic-logging"></a>Konfigurera diagnostisk loggning

En viktig del av en Analysis Services lösning är att övervaka hur servrarna presterar. Azure Analysis Services är integrerat med Azure Monitor. Med [Azure Monitor resurs loggar](../azure-monitor/essentials/platform-logs-overview.md)kan du övervaka och skicka loggar till [Azure Storage](https://azure.microsoft.com/services/storage/), strömma dem till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)och exportera dem till [Azure Monitor loggar](../azure-monitor/overview.md).

![Resurs loggning till lagring, Event Hubs eller Azure Monitor loggar](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Vad loggas?

Du kan välja **motor**-, **tjänst**-och **mått** kategorier.

### <a name="engine"></a>Motor

Om du väljer **motor** loggar alla [xEvents](/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Du kan inte välja enskilda händelser. 

|XEvent-kategorier |Händelse namn  |
|---------|---------|
|Säkerhetsgranskning    |   Granskning inloggning      |
|Säkerhetsgranskning    |   Granskning utloggning      |
|Säkerhetsgranskning    |   Gransknings servern startas och stoppas      |
|Förlopps rapporter     |   Förlopps rapport börjar      |
|Förlopps rapporter     |   Slutför rapport slut      |
|Förlopps rapporter     |   Aktuell förlopps rapport      |
|Frågor     |  Frågan börjar       |
|Frågor     |   Frågan slutar      |
|Kommandon     |  Kommando börjar       |
|Kommandon     |  Kommando slut       |
|Fel & varningar     |   Fel      |
|Identifiera     |   Identifiera slut      |
|Meddelande     |    Meddelande     |
|Session     |  Sessionsinitiering       |
|Lås    |  Dödläge       |
|Fråga bearbetning     |   VertiPaq SE-fråga börjar      |
|Fråga bearbetning     |   VertiPaq SE-end-fråga      |
|Fråga bearbetning     |   VertiPaq SE fråga cache-matchning      |
|Fråga bearbetning     |   Direkt fråga börjar      |
|Fråga bearbetning     |  Direkt frågans slut       |

### <a name="service"></a>Tjänst

|Åtgärdsnamn  |Inträffar när  |
|---------|---------|
|ResumeServer     |    Återuppta en server     |
|SuspendServer    |   Pausa en server      |
|DeleteServer     |    Ta bort en server     |
|RestartServer    |     Användaren startar om en server via SSMS eller PowerShell    |
|GetServerLogFiles    |    Användaren exporterar Server logg via PowerShell     |
|ExportModel     |   Användaren exporterar en modell i portalen genom att använda öppna i Visual Studio     |

### <a name="all-metrics"></a>Alla mått

Mått kategorin loggar samma [Server mått](analysis-services-monitor.md#server-metrics) för AzureMetrics-tabellen. Om du använder funktionen för att [skala ut](analysis-services-scale-out.md) och behöver separera mått för varje Läs replik, använder du tabellen AzureDiagnostics i stället där **OperationName** är lika med **LogMetric**.

## <a name="setup-diagnostics-logging"></a>Konfigurera diagnostikloggning

### <a name="azure-portal"></a>Azure Portal

1. I [Azure Portal](https://portal.azure.com) > Server klickar du på **diagnostikinställningar** i det vänstra navigerings fönstret och sedan på **Aktivera diagnostik**.

    ![Aktivera resurs loggning för Azure Cosmos DB i Azure Portal](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. I **Diagnostikinställningar** anger du följande alternativ: 

    * **Namn**. Ange ett namn för loggarna som ska skapas.

    * **Arkivera till ett lagrings konto**. Om du vill använda det här alternativet behöver du ett befintligt lagrings konto för att ansluta till. Se [skapa ett lagrings konto](../storage/common/storage-account-create.md). Följ instruktionerna för att skapa en resurs hanterare, ett allmänt konto och välj sedan ditt lagrings konto genom att gå tillbaka till den här sidan i portalen. Det kan ta några minuter innan nyligen skapade lagringskonton visas i den nedrullningsbara menyn.
    * **Strömma till en händelsehubben**. Om du vill använda det här alternativet behöver du en befintlig Event Hub-namnrymd och händelsehubben för att ansluta till. Mer information finns i [Skapa en namnrymd för en händelsehubb och en händelsehubb med Azure-portalen](../event-hubs/event-hubs-create.md). Gå sedan tillbaka till den här sidan i portalen för att välja namn området för Händelsehubben och princip namnet.
    * **Skicka till Azure Monitor (Log Analytics arbets yta)**. Om du vill använda det här alternativet använder du antingen en befintlig arbets yta eller [skapar en ny arbets ytans](../azure-monitor/logs/quick-create-workspace.md) resurs i portalen. Mer information om hur du visar loggarna finns i [Visa loggar i Log Analytics arbets yta](#view-logs-in-log-analytics-workspace) i den här artikeln.

    * **Motor**. Välj det här alternativet för att logga xEvents. Om du försöker arkivera till ett lagrings konto kan du välja kvarhållningsperioden för resurs loggarna. Loggarna tas bort när kvarhållningsperioden upphör att gälla.
    * **Tjänsten**. Välj det här alternativet om du vill logga händelser på service nivå. Om du arkiverar till ett lagrings konto kan du välja kvarhållningsperioden för resurs loggarna. Loggarna tas bort när kvarhållningsperioden upphör att gälla.
    * **Mått**. Välj det här alternativet om du vill lagra utförliga data i [mått](analysis-services-monitor.md#server-metrics). Om du arkiverar till ett lagrings konto kan du välja kvarhållningsperioden för resurs loggarna. Loggarna tas bort när kvarhållningsperioden upphör att gälla.

3. Klicka på **Spara**.

    Om du får ett fel meddelande om att diagnostiken inte kunde uppdateras för \<workspace name> . Prenumerationen \<subscription id> är inte registrerad för att använda Microsoft. Insights. " Följ anvisningarna i [fel söknings Azure-diagnostik](../azure-monitor/essentials/resource-logs.md) för att registrera kontot och gör sedan om proceduren.

    Om du vill ändra hur dina resurs loggar sparas när som helst i framtiden kan du gå tillbaka till den här sidan om du vill ändra inställningarna.

### <a name="powershell"></a>PowerShell

Här är de grundläggande kommandona som du kan använda för att komma igång. Om du vill veta mer om hur du konfigurerar loggning till ett lagrings konto med hjälp av PowerShell kan du läsa kursen längre fram i den här artikeln.

Använd följande kommandon om du vill aktivera mått och resurs loggning med hjälp av PowerShell:

- Om du vill aktivera lagring av resurs loggar i ett lagrings konto använder du följande kommando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Ditt lagringskonto-ID är resurs-ID för det lagringskonto dit du vill skicka loggarna.

- Använd följande kommando för att aktivera strömning av resurs loggar till en händelsehubben:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Regel-ID för Azure Service Bus är en sträng med det här formatet:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Om du vill aktivera sändning av resurs loggar till en Log Analytics arbets yta använder du följande kommando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Du kan hämta resurs-ID för Log Analytics-arbetsytan med hjälp av följande kommando:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Du kan kombinera dessa parametrar om du vill aktivera flera utdataalternativ.

### <a name="rest-api"></a>REST-API

Lär dig hur du [ändrar diagnostikinställningar med hjälp av REST-API i Azure Monitor](/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Resource Manager-mall

Lär dig hur du [aktiverar diagnostikinställningar när resursen skapas med hjälp av en Resource Manager-mall](../azure-monitor/essentials/resource-manager-diagnostic-settings.md). 

## <a name="manage-your-logs"></a>Hantera dina loggar

Loggar är vanligt vis tillgängliga inom några timmar efter att du ställer in loggning. Det är upp till dig att hantera loggarna på ditt lagringskonto:

* Använd åtkomstkontrollsmetoder för Azure av standardtyp för att skydda loggarna genom att begränsa vem som kan komma åt dem.
* Ta bort loggar som du inte längre vill behålla på ditt lagringskonto.
* Se till att ange en kvarhållningsperiod för så att gamla loggar tas bort från ditt lagrings konto.

## <a name="view-logs-in-log-analytics-workspace"></a>Visa loggar i Log Analytics arbets yta

Mått och Server händelser är integrerade med xEvents i din Log Analytics arbets ytans resurs för analys sida vid sida. Log Analytics arbets ytan kan också konfigureras för att ta emot händelser från andra Azure-tjänster som ger en heltäckande vy över diagnostisk loggnings data i din arkitektur.

Om du vill visa dina diagnostikdata går du till Log Analytics arbets yta och öppnar **loggar**  på den vänstra menyn.

![Loggs öknings alternativ i Azure Portal](./media/analysis-services-logging/aas-logging-open-log-search.png)

I Frågeverktyget expanderar du **LogManagement**  >  **AzureDiagnostics**. AzureDiagnostics innehåller motor-och tjänst händelser. Lägg märke till att en fråga skapas direkt. Fältet EventClass \_ s innehåller xEvent-namn, som kan se bekant ut om du har använt xEvents för lokal loggning. Klicka på **EventClass \_ s** eller något av händelse namnen och Log Analytics arbets ytan fortsätter att konstruera en fråga. Glöm inte att spara dina frågor så att du kan återanvända dem senare.

### <a name="example-queries"></a>Exempelfrågor

#### <a name="example-1"></a>Exempel 1

Följande fråga returnerar varaktigheter för varje frågas slut-/uppdaterings slut händelse för en modell databas och server. Om den skalas ut, delas resultatet ut av repliken eftersom replik numret ingår i ServerName_s. Gruppering efter RootActivityId_g minskar antalet rader som hämtas från Azure-diagnostik REST API och hjälper till att hålla sig inom gränserna enligt beskrivningen i [Log Analytics hastighets begränsningar](https://dev.loganalytics.io/documentation/Using-the-API/Limits).

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName" and DatabaseName_s =~ "MyDatabaseName" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs
| order by StartTime_t asc
```

#### <a name="example-2"></a>Exempel 2

Följande fråga returnerar minnes-och QPU-förbrukningen för en server. Om den skalas ut, delas resultatet ut av repliken eftersom replik numret ingår i ServerName_s.

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where name_s == "memory_metric" or name_s == "qpu_metric"
| project ServerName_s, TimeGenerated, name_s, value_s
| summarize avg(todecimal(value_s)) by ServerName_s, name_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

#### <a name="example-3"></a>Exempel 3

Följande fråga returnerar Analysis Services-motorns prestanda räknare för en server.

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where parse_json(tostring(parse_json(perfobject_s).counters))[0].name == "Rows read/sec" 
| extend Value = tostring(parse_json(tostring(parse_json(perfobject_s).counters))[0].value) 
| project ServerName_s, TimeGenerated, Value
| summarize avg(todecimal(Value)) by ServerName_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

Det finns hundratals frågor som du kan använda. Mer information om frågor finns i [Kom igång med Azure Monitor logg frågor](../azure-monitor/logs/get-started-queries.md).


## <a name="turn-on-logging-by-using-powershell"></a>Aktivera loggning med hjälp av PowerShell

I den här snabb självstudien skapar du ett lagrings konto i samma prenumeration och resurs grupp som Analysis Services-servern. Du använder sedan Set-AzDiagnosticSetting för att aktivera diagnostikloggning och skicka utdata till det nya lagrings kontot.

### <a name="prerequisites"></a>Förutsättningar
För att slutföra den här självstudien måste du ha följande resurser:

* En befintlig Azure Analysis Services-server. Anvisningar om hur du skapar en server resurs finns [i skapa en server i Azure Portal](analysis-services-create-server.md)eller [skapa en Azure Analysis Services-server med hjälp av PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Ansluta till dina prenumerationer

Starta en Azure PowerShell-session och logga in på ditt Azure-konto med följande kommando:  

```powershell
Connect-AzAccount
```

Ange användarnamnet och lösenordet för ditt Azure-konto i popup-fönstret i webbläsaren. Azure PowerShell identifierar alla prenumerationer som är associerade med det här kontot och använder den första som standard.

Om du har flera prenumerationer kan du behöva ange en som användes för att skapa Azure Key Vault. Skriv följande för att visa prenumerationerna för ditt konto:

```powershell
Get-AzSubscription
```

Om du sedan vill ange den prenumeration som är kopplad till Azure Analysis Services kontot som du loggar in, skriver du:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Om du har flera prenumerationer som är kopplade till ditt konto är det viktigt att du anger prenumerationen.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Skapa ett nytt lagringskonto för dina loggar

Du kan använda ett befintligt lagrings konto för loggarna, förutsatt att det är i samma prenumeration som servern. I den här självstudien skapar du ett nytt lagrings konto som är dedikerat för Analysis Services loggar. För att göra det enkelt lagrar du lagrings konto informationen i en variabel med namnet **sa**.

Du använder också samma resurs grupp som den som innehåller din Analysis Services-server. Ersätt värden för `awsales_resgroup` , `awsaleslogs` och `West Central US` med dina egna värden:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identifiera Server kontot för loggarna

Ange konto namnet till en variabel med namnet **konto**, där resourceName är namnet på kontot.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Aktivera loggning

Om du vill aktivera loggning använder du Set-AzDiagnosticSetting cmdlet tillsammans med variablerna för det nya lagrings kontot, Server kontot och kategorin. Kör följande kommando och ange flaggan **-Enabled** till **$True**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

Utdata bör se ut ungefär som i följande exempel:

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

Det här resultatet bekräftar att loggning nu har Aktiver ATS för servern och sparar information till lagrings kontot.

Du kan också ange bevarande princip för dina loggar så att äldre loggar tas bort automatiskt. Du kan till exempel ange bevarande princip med flaggan **-RetentionEnabled** till **$True** och ange parametern **RetentionInDays** till **90**. Loggar som är äldre än 90 dagar tas automatiskt bort.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du [Azure Monitor resurs loggning](../azure-monitor/essentials/platform-logs-overview.md).

Se [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) i PowerShell-hjälpen.