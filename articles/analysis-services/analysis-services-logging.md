---
title: Diagnostisk loggning för Azure Analysis Services | Microsoft Docs
description: Beskriver hur du loggning konfigureras för övervakning av din Azure Analysis Services server.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8ede7572079b6a54672234cbf9fe1445dafbad7b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769219"
---
# <a name="setup-diagnostic-logging"></a>Konfigurera diagnostisk loggning

En viktig del av Analysis Services är att övervaka hur servrarna fungerar. Azure Analysis Services är integrerat med Azure Monitor. Med [Azure Monitor resursloggar](../azure-monitor/essentials/platform-logs-overview.md)kan du övervaka och skicka loggar till [Azure Storage](https://azure.microsoft.com/services/storage/), strömma dem till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)och exportera dem till [Azure Monitor loggar](../azure-monitor/overview.md).

![Resursloggning till lagrings-, Event Hubs- eller Azure Monitor loggar](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Vad loggas?

Du kan välja **kategorier** **för Motor,** Tjänst **och** Mått.

### <a name="engine"></a>Motor

Om **du väljer** Motor [loggas alla xEvents](/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Du kan inte välja enskilda händelser. 

|XEvent-kategorier |Händelsenamn  |
|---------|---------|
|Säkerhetsgranskning    |   Granskning inloggning      |
|Säkerhetsgranskning    |   Granskning utloggning      |
|Säkerhetsgranskning    |   Granskningsservern startar och stoppar      |
|Lägesrapporter     |   Förloppsrapport börjar      |
|Lägesrapporter     |   Förloppsrapport – slut      |
|Lägesrapporter     |   Aktuell förloppsrapport      |
|Frågor     |  Frågan börjar       |
|Frågor     |   Frågan slutar      |
|Kommandon     |  Kommandot Börjar       |
|Kommandon     |  Kommandoslut       |
|Fel & varningar     |   Fel      |
|Identifiera     |   Identifiera slutet      |
|Meddelande     |    Meddelande     |
|Session     |  Sessionsinitiering       |
|Lås    |  Dödläge       |
|Frågebearbetning     |   VertiPaq SE-frågan börjar      |
|Frågebearbetning     |   VertiPaq SE-frågan avslutas      |
|Frågebearbetning     |   VertiPaq SE-frågecachematchning      |
|Frågebearbetning     |   Direct Query Begin      |
|Frågebearbetning     |  Direct Query End       |

### <a name="service"></a>Tjänst

|Åtgärdsnamn  |Inträffar när  |
|---------|---------|
|ResumeServer     |    Återuppta en server     |
|SuspendServer    |   Pausa en server      |
|DeleteServer     |    Ta bort en server     |
|RestartServer    |     Användaren startar om en server via SSMS eller PowerShell    |
|GetServerLogFiles    |    Användaren exporterar serverloggen via PowerShell     |
|ExportModel     |   Användaren exporterar en modell i portalen med hjälp av Öppna i Visual Studio     |

### <a name="all-metrics"></a>Alla mått

Kategorin Mått loggar samma [servermått i](analysis-services-monitor.md#server-metrics) tabellen AzureMetrics. Om du använder [](analysis-services-scale-out.md) frågeutskalning och behöver avgränsa mått för varje skrivskyddad replik använder du tabellen AzureDiagnostics i stället, där **OperationName** är lika med **LogMetric**.

## <a name="setup-diagnostics-logging"></a>Konfigurera diagnostikloggning

### <a name="azure-portal"></a>Azure Portal

1. I [Azure Portal](https://portal.azure.com) > server klickar du på **Diagnostikinställningar** i det vänstra navigeringsfönstret och klickar **sedan på Aktivera diagnostik.**

    ![Aktivera resursloggning för Azure Cosmos DB i Azure Portal](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. I **Diagnostikinställningar** anger du följande alternativ: 

    * **Namn**. Ange ett namn för loggarna som ska skapas.

    * **Arkivera till ett lagringskonto**. Om du vill använda det här alternativet behöver du ett befintligt lagringskonto att ansluta till. Se [Skapa ett lagringskonto.](../storage/common/storage-account-create.md) Följ instruktionerna för att Resource Manager ett allmänt konto och välj sedan ditt lagringskonto genom att gå tillbaka till den här sidan i portalen. Det kan ta några minuter innan nyligen skapade lagringskonton visas i den nedrullningsbara menyn.
    * **Strömma till en händelsehubb**. Om du vill använda det här alternativet behöver du ett befintligt namnområde för händelsehubben och en händelsehubb att ansluta till. Mer information finns i [Skapa en namnrymd för en händelsehubb och en händelsehubb med Azure-portalen](../event-hubs/event-hubs-create.md). Gå sedan tillbaka till den här sidan i portalen för att välja namnområdet för händelsehubben och principnamnet.
    * **Skicka till Azure Monitor (Log Analytics-arbetsyta)**. Om du vill använda det här alternativet använder du antingen en befintlig [arbetsyta eller skapar en ny](../azure-monitor/logs/quick-create-workspace.md) arbetsyteresurs i portalen. Mer information om hur du visar loggarna finns i [Visa loggar på Log Analytics-arbetsytan i](#view-logs-in-log-analytics-workspace) den här artikeln.

    * **Motor**. Välj det här alternativet för att logga xEvents. Om du arkiverar till ett lagringskonto kan du välja kvarhållningsperiod för resursloggarna. Loggarna försvinner automatiskt när kvarhållningsperioden har löpt ut.
    * **Tjänst**. Välj det här alternativet för att logga händelser på tjänstnivå. Om du arkiverar till ett lagringskonto kan du välja kvarhållningsperiod för resursloggarna. Loggarna försvinner automatiskt när kvarhållningsperioden har löpt ut.
    * **Mått**. Välj det här alternativet om du vill lagra utförliga data [i Mått](analysis-services-monitor.md#server-metrics). Om du arkiverar till ett lagringskonto kan du välja kvarhållningsperiod för resursloggarna. Loggarna försvinner automatiskt när kvarhållningsperioden har löpt ut.

3. Klicka på **Spara**.

    Om du får felmeddelandet "Det gick inte att uppdatera diagnostiken för \<workspace name> . Prenumerationen \<subscription id> har inte registrerats för användning av microsoft.insights." Följ [anvisningarna Azure Diagnostics](../azure-monitor/essentials/resource-logs.md) för att registrera kontot och försök sedan igen.

    Om du vill ändra hur dina resursloggar sparas när som helst i framtiden kan du gå tillbaka till den här sidan om du vill ändra inställningarna.

### <a name="powershell"></a>PowerShell

Här är de grundläggande kommandona som du kan använda för att komma igång. Om du vill ha stegvis hjälp med att konfigurera loggning till ett lagringskonto med hjälp av PowerShell kan du läsa självstudien senare i den här artikeln.

Om du vill aktivera mått och resursloggning med hjälp av PowerShell använder du följande kommandon:

- Om du vill aktivera lagring av resursloggar i ett lagringskonto använder du det här kommandot:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Ditt lagringskonto-ID är resurs-ID för det lagringskonto dit du vill skicka loggarna.

- Om du vill aktivera strömning av resursloggar till en händelsehubb använder du det här kommandot:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Regel-ID för Azure Service Bus är en sträng med det här formatet:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Om du vill skicka resursloggar till en Log Analytics-arbetsyta använder du det här kommandot:

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

Loggar är vanligtvis tillgängliga inom några timmar efter att loggning har konfigureras. Det är upp till dig att hantera loggarna på ditt lagringskonto:

* Använd åtkomstkontrollsmetoder för Azure av standardtyp för att skydda loggarna genom att begränsa vem som kan komma åt dem.
* Ta bort loggar som du inte längre vill behålla på ditt lagringskonto.
* Se till att ange en kvarhållningsperiod för så att gamla loggar tas bort från ditt lagringskonto.

## <a name="view-logs-in-log-analytics-workspace"></a>Visa loggar på Log Analytics-arbetsytan

Mått och serverhändelser är integrerade med xEvents i Log Analytics-arbetsytans resurs för analys sida vid sida. Log Analytics-arbetsytan kan också konfigureras för att ta emot händelser från andra Azure-tjänster, vilket ger en holistisk vy över diagnostikloggningsdata i din arkitektur.

Om du vill visa dina diagnostikdata går du till Log Analytics-arbetsytan och **öppnar Loggar**  på den vänstra menyn.

![Loggsökningsalternativ i Azure Portal](./media/analysis-services-logging/aas-logging-open-log-search.png)

I frågeverktyget expanderar du **LogManagement**  >  **AzureDiagnostics**. AzureDiagnostics innehåller motor- och tjänsthändelser. Observera att en fråga skapas i farten. Fältet EventClass innehåller xEvent-namn, som kan se bekant ut om du har använt \_ xEvents för lokal loggning. Klicka **på \_ EventClasss** eller något av händelsenamnen så fortsätter Log Analytics-arbetsytan att skapa en fråga. Glöm inte att spara dina frågor så att du kan återanvända dem senare.

### <a name="example-queries"></a>Exempelfrågor

#### <a name="example-1"></a>Exempel 1

Följande fråga returnerar varaktigheter för varje sluthändelse för frågans slut/uppdatering för en modelldatabas och -server. Om resultatet skalas ut delas resultatet upp av repliken eftersom repliknumret ingår i ServerName_s. Gruppering efter RootActivityId_g minskar antalet rader som hämtas från Azure Diagnostics REST API och hjälper till att hålla sig inom gränserna enligt beskrivningen i [Log Analytics-hastighetsbegränsningar.](https://dev.loganalytics.io/documentation/Using-the-API/Limits)

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

Följande fråga returnerar minnes- och QPU-förbrukning för en server. Om resultatet skalas ut delas resultatet upp av repliken eftersom repliknumret ingår i ServerName_s.

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

Följande fråga returnerar prestandaräknarna för radläsning/Analysis Services för en server.

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

Det finns hundratals frågor som du kan använda. Mer information om frågor finns i [Kom igång med Azure Monitor-loggfrågor.](../azure-monitor/logs/get-started-queries.md)


## <a name="turn-on-logging-by-using-powershell"></a>Aktivera loggning med hjälp av PowerShell

I den här snabbkursen skapar du ett lagringskonto i samma prenumeration och resursgrupp som Analysis Service-servern. Sedan använder du Set-AzDiagnosticSetting för att aktivera diagnostikloggning och skicka utdata till det nya lagringskontot.

### <a name="prerequisites"></a>Förutsättningar
För att kunna slutföra den här självstudien måste du ha följande resurser:

* En befintlig Azure Analysis Services server. Anvisningar om hur du skapar en serverresurs finns i Skapa en [server i Azure Portal](analysis-services-create-server.md)eller Skapa en [Azure Analysis Services-server med hjälp av PowerShell](analysis-services-create-powershell.md).

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

Om du sedan vill ange den prenumeration som är associerad med Azure Analysis Services som du loggar, skriver du:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Om du har flera prenumerationer som är kopplade till ditt konto är det viktigt att du anger prenumerationen.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Skapa ett nytt lagringskonto för dina loggar

Du kan använda ett befintligt lagringskonto för dina loggar, förutsatt att det finns i samma prenumeration som servern. I den här självstudien skapar du ett nytt lagringskonto som är dedikerat Analysis Services loggar. För att göra det enkelt lagrar du information om lagringskontot i en variabel med namnet **sa**.

Du använder också samma resursgrupp som den som innehåller din Analysis Services server. Ersätt värdena `awsales_resgroup` för , och med dina egna `awsaleslogs` `West Central US` värden:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identifiera serverkontot för dina loggar

Ange kontonamnet till en variabel med **namnet account**, där ResourceName är namnet på kontot.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Aktivera loggning

Om du vill aktivera loggning använder Set-AzDiagnosticSetting-cmdleten tillsammans med variablerna för det nya lagringskontot, serverkontot och kategorin. Kör följande kommando och ange flaggan **-Enabled** till **$true**:

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

Dessa utdata bekräftar att loggning nu är aktiverat för servern, vilket sparar information till lagringskontot.

Du kan också ange kvarhållningsprincip för dina loggar så att äldre loggar tas bort automatiskt. Ange till exempel bevarandeprincip med **flaggan -RetentionEnabled** **till $true** och ange parametern **-RetentionInDays** till **90**. Loggar som är äldre än 90 dagar tas bort automatiskt.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [att Azure Monitor resursloggning.](../azure-monitor/essentials/platform-logs-overview.md)

Se [Set-AzDiagnosticSetting i](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell-hjälpen.
