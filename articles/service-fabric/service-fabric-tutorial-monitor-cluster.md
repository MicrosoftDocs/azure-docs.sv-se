---
title: Övervaka ett Service Fabric kluster i Azure
description: 'I den här självstudien får du lära dig hur du övervakar ett kluster genom att Visa Service Fabric händelser, fråga EventStore-API: er, övervaka prestanda räknare och Visa hälso rapporter.'
author: srrengar
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: srrengar
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: ecd05a838425d57e0eaff2fa571d72b5a87e92a6
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791785"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>Självstudie: övervaka ett Service Fabric kluster i Azure

Övervakning och diagnostik är avgörande för att utveckla, testa och distribuera arbets belastningar i valfri moln miljö. Den här självstudien är del två i en serie och visar hur du övervakar och diagnostiserar ett Service Fabric kluster med hjälp av händelser, prestanda räknare och hälso rapporter.   Mer information finns i översikten om övervakning av [kluster](service-fabric-diagnostics-overview.md#platform-cluster-monitoring) och [övervakning av infrastruktur](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Visa Service Fabric händelser
> * Fråga EventStore-API: er för kluster händelser
> * Övervaka infrastruktur/samla in prestanda räknare
> * Visa kluster hälso rapporter

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * Skapa ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) i Azure med hjälp av en mall
> * Övervaka ett kluster
> * [Skala in eller ut ett kluster](service-fabric-tutorial-scale-cluster.md)
> * [uppgradera körningen för ett kluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Ta bort ett kluster](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

Innan du börjar den här självstudien:

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installera [Azure PowerShell](/powershell/azure/install-az-ps) eller [Azure CLI](/cli/azure/install-azure-cli).
* Skapa ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 
* Konfigurera [diagnostik-samling](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor) för klustret
* Aktivera [EventStore-tjänsten](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor) i klustret
* Konfigurera [Azure Monitor loggar och Log Analytics agent](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor) för klustret

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>Visa Service Fabric händelser med hjälp av Azure Monitor loggar

Azure Monitor loggar samlar in och analyserar telemetri från program och tjänster som finns i molnet och tillhandahåller analys verktyg som hjälper dig att maximera deras tillgänglighet och prestanda. Du kan köra frågor i Azure Monitor loggar för att få insikter och felsöka vad som händer i klustret.

Om du vill komma åt Service Fabric-analys-lösningen går du till [Azure Portal](https://portal.azure.com) och väljer den resurs grupp där du skapade Service Fabric-analyss lösningen.

Välj resurs **-ServiceFabric (mysfomsworkspace)**.

I **Översikt** ser du paneler i form av en graf för var och en av de lösningar som är aktiverade, inklusive en för Service Fabric. Fortsätt till Service Fabric-analys-lösningen genom att klicka på **Service Fabric** graf.

![Skärm bild som visar Service Fabric grafen.](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

Följande bild visar Service Fabric-analys lösningens start sida. Den här start sidan innehåller en Snapshot-vy över vad som händer i klustret.

![Service Fabric lösning](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 Om du har aktiverat diagnostik när klustret har skapats kan du se händelser för 

* [Service Fabric kluster händelser](service-fabric-diagnostics-event-generation-operational.md)
* [Reliable Actors programmerings modell händelser](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services programmerings modell händelser](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Utöver Service Fabric-händelser i rutan kan mer detaljerade system händelser samlas in genom att [du uppdaterar konfigurationen av tillägget för diagnostik](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Visa Service Fabric händelser, inklusive åtgärder på noder

På sidan Service Fabric-analys klickar du på grafen för **kluster händelser**.  Loggarna för alla system händelser som har samlats in visas. För referens är dessa från **WADServiceFabricSystemEventsTable** i Azure Storage-kontot, och på samma sätt som de Reliable Services-och skådespelare-händelser som du ser härnäst kommer du från respektive tabell.
    
![Frågans operativa kanal](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

Frågan använder Kusto-frågespråket, som du kan ändra för att förfina det du söker. Om du till exempel vill hitta alla åtgärder som vidtas på noder i klustret kan du använda följande fråga. De händelse-ID: n som används nedan finns i [referens för drift kanal händelser](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Frågespråket för Kusto är kraftfullt. Här är några andra användbara frågor.

Skapa en *ServiceFabricEvent* lookup-tabell som användardefinierad funktion genom att spara frågan som en funktion med alias ServiceFabricEvent:

```kusto
let ServiceFabricEvent = datatable(EventId: int, EventName: string)
[
    ...
    18603, 'NodeUpOperational',
    18604, 'NodeDownOperational',
    ...
];
ServiceFabricEvent
```

Returnera drift händelser som registrerats under den senaste timmen:
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

Returnera drift händelser med EventId = = 18604 och EventName = = ' NodeDownOperational ':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

Returnera drift händelser med EventId = = 18604 och EventName = = ' NodeUpOperational ':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 

Returnerar hälso rapporter med hälso tillstånd = = 3 (fel) och extraherar ytterligare egenskaper från fältet EventMessage:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| extend HealthStateId = extract(@"HealthState=(\S+) ", 1, EventMessage, typeof(int))
| where TaskName == 'HM' and HealthStateId == 3
| extend SourceId = extract(@"SourceId=(\S+) ", 1, EventMessage, typeof(string)),
         Property = extract(@"Property=(\S+) ", 1, EventMessage, typeof(string)),
         HealthState = case(HealthStateId == 0, 'Invalid', HealthStateId == 1, 'Ok', HealthStateId == 2, 'Warning', HealthStateId == 3, 'Error', 'Unknown'),
         TTL = extract(@"TTL=(\S+) ", 1, EventMessage, typeof(string)),
         SequenceNumber = extract(@"SequenceNumber=(\S+) ", 1, EventMessage, typeof(string)),
         Description = extract(@"Description='([\S\s, ^']+)' ", 1, EventMessage, typeof(string)),
         RemoveWhenExpired = extract(@"RemoveWhenExpired=(\S+) ", 1, EventMessage, typeof(bool)),
         SourceUTCTimestamp = extract(@"SourceUTCTimestamp=(\S+)", 1, EventMessage, typeof(datetime)),
         ApplicationName = extract(@"ApplicationName=(\S+) ", 1, EventMessage, typeof(string)),
         ServiceManifest = extract(@"ServiceManifest=(\S+) ", 1, EventMessage, typeof(string)),
         InstanceId = extract(@"InstanceId=(\S+) ", 1, EventMessage, typeof(string)),
         ServicePackageActivationId = extract(@"ServicePackageActivationId=(\S+) ", 1, EventMessage, typeof(string)),
         NodeName = extract(@"NodeName=(\S+) ", 1, EventMessage, typeof(string)),
         Partition = extract(@"Partition=(\S+) ", 1, EventMessage, typeof(string)),
         StatelessInstance = extract(@"StatelessInstance=(\S+) ", 1, EventMessage, typeof(string)),
         StatefulReplica = extract(@"StatefulReplica=(\S+) ", 1, EventMessage, typeof(string))
```

Returnera ett tids diagram med händelser med EventId! = 17523:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

Hämta Service Fabric operativa händelser som sammanställs med den angivna tjänsten och noden:

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

Rendera antalet Service Fabric händelser av EventId/EventName med en kors resurs fråga:

```kusto
app('PlunkoServiceFabricCluster').traces
| where customDimensions.ProviderName == 'Microsoft-ServiceFabric'
| extend EventId = toint(customDimensions.EventId), TaskName = tostring(customDimensions.TaskName)
| where EventId != 17523
| join kind=leftouter ServiceFabricEvent on EventId
| extend EventName = case(EventName != '', EventName, 'Undocumented')
| summarize ["Event Count"]= count() by bin(timestamp, 30m), EventName = strcat(tostring(EventId), " - ", EventName)
| render timechart
```

### <a name="view-service-fabric-application-events"></a>Visa Service Fabric program händelser

Du kan visa händelser för de Reliable Services-och pålitliga skådespelare-program som distribueras i klustret.  På sidan Service Fabric-analys klickar du på grafen för **program händelser**.

Kör följande fråga för att visa händelser från dina Reliable Services-program:
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

Du kan se olika händelser när tjänsten `runasync` startas och slutförs, vilket vanligt vis sker vid distributioner och uppgraderingar.

![Service Fabric lösning Reliable Services](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

Du kan också hitta händelser för den tillförlitliga tjänsten med ServiceName = = "Fabric:/övervaknings enhet/WatchdogService":

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
Pålitliga aktörs händelser kan visas på liknande sätt:

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
Om du vill konfigurera mer detaljerade händelser för tillförlitliga aktörer kan du ändra `scheduledTransferKeywordFilter` i konfigurationen för diagnostiskt tillägg i kluster mal len. Information om värdena för dessa finns i [händelse referens för pålitliga aktörer](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

## <a name="view-performance-counters-with-azure-monitor-logs"></a>Visa prestanda räknare med Azure Monitor loggar
Om du vill visa prestanda räknare går du till [Azure Portal](https://portal.azure.com) och resurs gruppen där du skapade Service Fabric-analys-lösningen. 

Välj resurs- **ServiceFabric (mysfomsworkspace)**, sedan **Log Analytics arbets yta** och sedan **Avancerade inställningar**.

Klicka på **data** och sedan på **Windows prestanda räknare**. Det finns en lista med standard räknare som du kan välja att aktivera och du kan ange intervallet för samling. Du kan också lägga till [ytterligare prestanda räknare](service-fabric-diagnostics-event-generation-perf.md) som ska samlas in. Rätt format refereras till i den här [artikeln](/windows/desktop/PerfCtrs/specifying-a-counter-path). Klicka på **Spara** och sedan på **OK**.

Stäng bladet avancerade inställningar och välj **Sammanfattning för arbets yta** under rubriken **Allmänt** . För var och en av de lösningar som har Aktiver ATS finns det en grafisk panel, inklusive en för Service Fabric. Fortsätt till Service Fabric-analys-lösningen genom att klicka på **Service Fabric** graf.

Det finns grafiska paneler för drift kanal och Reliable Services-händelser. Den grafiska åter givningen av de data som flödar in för de räknare du har valt visas under **Node-mått**. 

Välj diagrammet **container mått** om du vill visa mer information. Du kan också fråga om prestanda räknar data på liknande sätt som kluster händelser och filter på noderna, prestanda räknarens namn och värden med hjälp av Kusto-frågespråket.

## <a name="query-the-eventstore-service"></a>Fråga EventStore-tjänsten
[EventStore-tjänsten](service-fabric-diagnostics-eventstore.md) är ett sätt att förstå tillstånd för ditt kluster eller arbets belastningar vid en viss tidpunkt. EventStore är en tillstånds känslig Service Fabric tjänst som upprätthåller händelser från klustret. Händelserna exponeras via [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), rest och API: er. EventStore frågar klustret direkt för att hämta diagnostikdata för valfri entitet i klustret för att se en fullständig lista över händelser som är tillgängliga i EventStore, se [Service Fabric händelser](service-fabric-diagnostics-event-generation-operational.md).

EventStore-API: er kan frågas program mässigt med hjälp av [Service Fabric klient biblioteket](/dotnet/api/overview/azure/service-fabric#client-library).

Här är en exempel förfrågan för alla kluster händelser mellan 2018-04-03T18:00:00Z och 2018-04-04T18:00:00Z, via GetClusterEventListAsync-funktionen.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Här är ett annat exempel som frågar efter kluster hälsa och alla noder i september 2018 och skriver ut dem.

```csharp
const int timeoutSecs = 60;
var clusterUrl = new Uri(@"http://localhost:19080"); // This example is for a Local cluster
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl);

var clusterHealth = sfhttpClient.Cluster.GetClusterHealthAsync().GetAwaiter().GetResult();
Console.WriteLine("Cluster Health: {0}", clusterHealth.AggregatedHealthState.Value.ToString());


Console.WriteLine("Querying for node events...");
var nodesEvents = sfhttpClient.EventsStore.GetNodesEventListAsync(
    "2018-09-01T00:00:00Z",
    "2018-09-30T23:59:59Z",
    timeoutSecs,
    "NodeDown,NodeUp")
    .GetAwaiter()
    .GetResult()
    .ToList();
Console.WriteLine("Result Count: {0}", nodesEvents.Count());

foreach (var nodeEvent in nodesEvents)
{
    Console.Write("Node event happened at {0}, Node name: {1} ", nodeEvent.TimeStamp, nodeEvent.NodeName);
    if (nodeEvent is NodeDownEvent)
    {
        var nodeDownEvent = nodeEvent as NodeDownEvent;
        Console.WriteLine("(Node is down, and it was last up at {0})", nodeDownEvent.LastNodeUpAt);
    }
    else if (nodeEvent is NodeUpEvent)
    {
        var nodeUpEvent = nodeEvent as NodeUpEvent;
        Console.WriteLine("(Node is up, and it was last down at {0})", nodeUpEvent.LastNodeDownAt);
    }
}
```


## <a name="monitor-cluster-health"></a>Övervaka kluster hälsa
Service Fabric introducerar en [hälso modell](service-fabric-health-introduction.md) med hälsoentiteter där system komponenter och övervaknings enheter kan rapportera lokala villkor som de övervakar. [Hälso arkivet](service-fabric-health-introduction.md#health-store) sammanställer alla hälso data för att avgöra om entiteterna är felfria.

Klustret fylls i automatiskt med hälso rapporter som skickas av system komponenterna. Felsök genom att läsa mer i [använda system hälso rapporter](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric exponerar hälso frågor för var och en av de [typer av enheter](service-fabric-health-introduction.md#health-entities-and-hierarchy)som stöds. De kan nås via API: et med metoder på [FabricClient. HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager), PowerShell-cmdletar och rest. Dessa frågor returnerar fullständig hälso information om entiteten: det sammanställda hälso tillståndet, hälso tillstånds händelser för enheter, underordnade hälso tillstånd (i förekommande fall), felaktiga utvärderingar (om enheten inte är felfri) och hälso statistik för barn (i förekommande fall).

### <a name="get-cluster-health"></a>Hämta kluster hälsa
[Cmdleten Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) returnerar hälso tillståndet för entiteten kluster och innehåller hälso tillståndet för program och noder (underordnade kluster).  Anslut först till klustret med hjälp av [cmdleten Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster).

Klustrets tillstånd är 11 noder, system programmet och infrastruktur resursen:/röstning som kon figurer ATS enligt beskrivningen.

I följande exempel får du kluster hälsa genom att använda standard hälso principer. 11 noderna är felfria men det sammansatta hälso tillståndet är ett fel på grund av att programmet Fabric:/röstning är fel. Observera att utvärderings kraven ger information om de villkor som utlöste den aggregerade hälsan.

```powershell
Get-ServiceFabricClusterHealth

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            33% (1/3) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                          
NodeHealthStates        : 
                          NodeName              : _nt2vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_4
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt3vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_4
                          AggregatedHealthState : Ok
                          
ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok
                          
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
HealthStatistics        : 
                          Node                  : 11 Ok, 0 Warning, 0 Error
                          Replica               : 4 Ok, 0 Warning, 0 Error
                          Partition             : 2 Ok, 0 Warning, 0 Error
                          Service               : 2 Ok, 0 Warning, 0 Error
                          DeployedServicePackage : 3 Ok, 1 Warning, 1 Error
                          DeployedApplication   : 1 Ok, 1 Warning, 1 Error
                          Application           : 0 Ok, 0 Warning, 1 Error
```

I följande exempel hämtas hälso tillståndet för klustret med hjälp av en anpassad användnings princip. Den filtrerar resultaten så att endast program och noder visas i fel eller varning. I det här exemplet returneras inga noder, eftersom de är felfria. Endast programmet Fabric:/röstning uppfyller program filtret. Eftersom den anpassade principen anger att varningar ska övervägas som fel för programmet Fabric:/röstning, utvärderas programmet som fel och så är klustret.

```powershell
$appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/Voting"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            100% (5/5) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_2' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2466f2f9-d5fd-410c-a6a4-5b1e00630cca' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376486201388'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_4' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '5faa5201-eede-400a-865f-07f7f886aa32' is in Error.
                          
                                    'System.Hosting' reported Warning for property 'CodePackageActivation:Code:SetupEntryPoint:131959376207396204'. The evaluation treats 
                          Warning as Error.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_0' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '204f1783-f774-4f3a-b371-d9983afaf059' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959375885791093'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt3vm_0' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2533ae95-2d2a-4f8b-beef-41e13e4c0081' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376108346272'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1                         
                          
NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
```

### <a name="get-node-health"></a>Hämta nods hälsa
Cmdlet: en [Get-ServiceFabricNodeHealth](/powershell/module/servicefabric/get-servicefabricnodehealth) returnerar hälsan för en nod-entitet och innehåller de hälso händelser som rapporter ATS på noden. Anslut först till klustret med hjälp av [cmdleten Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster). I följande exempel hämtas hälsan för en speciell nod med hjälp av standard hälso principer:

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

I följande exempel får du hälsan för alla noder i klustret:
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>Hämta system tjänstens hälsa 

Hämta system tjänsternas sammanlagda hälsa:

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Visa Service Fabric händelser
> * Fråga EventStore-API: er för kluster händelser
> * Övervaka infrastruktur/samla in prestanda räknare
> * Visa kluster hälso rapporter

Fortsätt sedan till följande självstudie och lär dig hur du skalar ett kluster.
> [!div class="nextstepaction"]
> [Skala ett kluster](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
