---
title: Så här visar du Azure Service Fabric entiteternas aggregerade hälso tillstånd
description: Beskriver hur du frågar, visar och utvärderar Azure Service Fabric entiteternas samlade hälso tillstånd, via hälso frågor och allmänna frågor.
ms.topic: conceptual
ms.date: 2/28/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 261343537b6d18c98c27c9cc9453d2a64f064216
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628075"
---
# <a name="view-service-fabric-health-reports"></a>Visa Service Fabric hälso rapporter
Azure Service Fabric introducerar en [hälso modell](service-fabric-health-introduction.md) med hälsoentiteter där system komponenter och övervaknings enheter kan rapportera lokala villkor som de övervakar. [Hälso arkivet](service-fabric-health-introduction.md#health-store) sammanställer alla hälso data för att avgöra om entiteterna är felfria.

Klustret fylls i automatiskt med hälso rapporter som skickas av system komponenterna. Felsök genom att läsa mer i [använda system hälso rapporter](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric ger flera sätt att hämta enheternas aggregerade hälso tillstånd:

* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) eller andra visualiserings verktyg
* Hälso frågor (via PowerShell, API eller REST)
* Allmänna frågor som returnerar en lista över entiteter som har hälso tillstånd som en av egenskaperna (via PowerShell, API eller REST)

För att demonstrera de här alternativen ska vi använda ett lokalt kluster med fem noder och [Fabric:/WORDCOUNT-programmet](https://github.com/Azure-Samples/service-fabric-wordcount/raw/master/WordCountV1.sfpkg). Programmet **Fabric:/WORDCOUNT** innehåller två standard tjänster, en tillstånds känslig tjänst av typen `WordCountServiceType` och en tillstånds lös tjänst av typen `WordCountWebServiceType` . Jag ändrade för `ApplicationManifest.xml` att kräva sju mål repliker för den tillstånds känsliga tjänsten och en partition. Eftersom det bara finns fem noder i klustret, rapporterar system komponenterna en varning på-tjänstepartitionen eftersom den är lägre än antalet mål.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Hälso tillstånd i Service Fabric Explorer
Service Fabric Explorer ger en visuell vy av klustret. I bilden nedan kan du se att:

* Application **Fabric:/WORDCOUNT** är rött (i fel) eftersom det innehåller en fel händelse som rapporteras av min **övervaknings** enhet för egenskaps **tillgänglighet**.
* En av dess tjänster, **Fabric:/WORDCOUNT/WordCountService** är gult (i varning). Tjänsten har kon figurer ATS med sju repliker och klustret innehåller fem noder, så det går inte att placera två repliker. Även om den inte visas här är tjänstepartitionen gul på grund av en system rapport från `System.FM` att säga att `Partition is below target replica or instance count` . Den gula partitionen utlöser den gula tjänsten.
* Klustret är rött på grund av det röda programmet.

Utvärderingen använder standard principer från kluster manifestet och applikations manifestet. De är strikta principer och tolererar inte eventuella problem.

Vy över klustret med Service Fabric Explorer:

![Vy över klustret med Service Fabric Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Läs mer om [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Hälso frågor
Service Fabric exponerar hälso frågor för var och en av de [typer av enheter](service-fabric-health-introduction.md#health-entities-and-hierarchy)som stöds. De kan nås via API: et med metoder på [FabricClient. HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager), PowerShell-cmdletar och rest. Dessa frågor returnerar fullständig hälso information om entiteten: det sammanställda hälso tillståndet, hälso tillstånds händelser för enheter, underordnade hälso tillstånd (i förekommande fall), felaktiga utvärderingar (om enheten inte är felfri) och hälso statistik för barn (i förekommande fall).

> [!NOTE]
> En hälsoentitet returneras när den är fullständigt ifylld i hälso lagret. Entiteten måste vara aktiv (inte borttagen) och ha en system rapport. De överordnade entiteterna i hierarkin kedjan måste också ha system rapporter. Om något av dessa villkor inte är uppfyllt returnerar hälso frågorna en [FabricException](/dotnet/api/system.fabric.fabricexception) med [FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` som visar varför entiteten inte returneras.
>
>

Hälso frågorna måste klara i enhets-ID: n, vilket beror på entitetstypen. Frågorna accepterar valfria parametrar för hälso principer. Om inga hälso principer anges används [hälso principerna](service-fabric-health-introduction.md#health-policies) från klustret eller applikations manifestet för utvärdering. Om manifesten inte innehåller en definition för hälso principer används standard hälso principerna för utvärdering. Standard hälso principerna kan inte tolerera fel. Frågorna accepterar också filter för att enbart returnera delvis underordnade eller händelser – de som följer de angivna filtren. Ett annat filter tillåter utesluta underordnade statistik.

> [!NOTE]
> Utdatafilter används på Server sidan så att meddelandets svars storlek minskas. Vi rekommenderar att du använder utgående filter för att begränsa vilka data som returneras, i stället för att använda filter på klient sidan.
>
>

En entitets hälso tillstånd innehåller:

* Enhetens sammanställda hälso tillstånd. Beräknas av hälso lagret baserat på enhetens hälso rapporter, underordnade hälso tillstånd (om tillämpligt) och hälso principer. Läs mer om [utvärdering av enhets hälsa](service-fabric-health-introduction.md#health-evaluation).  
* Hälso händelser för entiteten.
* Insamling av hälso tillstånd för alla underordnade för entiteter som kan ha underordnade. Hälso tillstånden innehåller enhets identifierare och det aggregerade hälso tillståndet. För att få fullständig hälsa för ett underordnat certifikat, anropar du frågans hälso tillstånd för den underordnade entitetstypen och skickar den underordnade-ID: t.
* De defekta utvärderingarna som pekar på rapporten som utlöste status för entiteten, om entiteten inte är felfri. Utvärderingarna är rekursiva och innehåller de underordnade hälso utvärderingen som utlöste det aktuella hälso tillståndet. En övervaknings enhet rapporterade till exempel ett fel mot en replik. Program hälsan visar en felaktig utvärdering på grund av en skadad tjänst. tjänsten är inte felfri på grund av en felaktig partition. partitionen är inte felfri på grund av ett fel i en replik. repliken är inte felfri på grund av hälso rapporten för övervaknings enheten.
* Hälso statistik för alla underordnade typer av entiteter som har underordnade objekt. Kluster hälsa visar till exempel det totala antalet program, tjänster, partitioner, repliker och distribuerade entiteter i klustret. Tjänstens hälsa visar det totala antalet partitioner och repliker under den angivna tjänsten.

## <a name="get-cluster-health"></a>Hämta kluster hälsa
Returnerar hälso tillståndet för klustrets entitet och innehåller hälso tillståndet för program och noder (underordnade kluster). Indata:

* Valfritt Kluster hälso principen som används för att utvärdera noderna och kluster händelserna.
* Valfritt Princip kartan för program hälsa med de hälso principer som används för att åsidosätta program manifest principerna.
* Valfritt Filter för händelser, noder och program som anger vilka poster som är av intresse och som ska returneras i resultatet (till exempel endast fel eller både varningar och fel). Alla händelser, noder och program används för att utvärdera enhetens sammanlagda hälso tillstånd, oavsett filtret.
* Valfritt Filtrera för att undanta hälso statistik.
* Valfritt Filter för att inkludera infrastruktur resurser:/hälso statistik i hälso statistik. Endast tillämpligt om hälso statistik inte är exkluderad. Hälso statistiken innehåller som standard endast statistik för användar program och inte system tillämpningen.

### <a name="api"></a>API
För att få kluster hälsa skapar du en `FabricClient` och anropar [GetClusterHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) -metoden på dess **HealthManager**.

Följande anrop hämtar kluster tillståndet:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

Följande kod hämtar kluster hälsan genom att använda en anpassad kluster hälso princip och filter för noder och program. Det anger att hälso statistiken omfattar infrastruktur resursen:/system statistik. Den skapar [ClusterHealthQueryDescription](/dotnet/api/system.fabric.description.clusterhealthquerydescription), som innehåller information om indata.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var healthStatisticsFilter = new ClusterHealthStatisticsFilter()
{
    ExcludeHealthStatistics = false,
    IncludeSystemApplicationHealthStatistics = true
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
    HealthStatisticsFilter = healthStatisticsFilter
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Cmdleten för att hämta kluster hälsan är [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth). Anslut först till klustret med hjälp av cmdleten [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .

Klustrets tillstånd är fem noder, system programmet och Fabric:/WordCount som kon figurer ATS enligt beskrivningen.

Följande cmdlet får kluster hälsa genom att använda standard hälso principer. Det sammanställda hälso tillståndet är varning eftersom Fabric:/WordCount-programmet är i varning. Observera att utvärderings kraven ger information om de villkor som utlöste den aggregerade hälsan.

```xml
PS D:\ServiceFabric> Get-ServiceFabricClusterHealth


AggregatedHealthState   : Warning
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.


NodeHealthStates        : 
                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
HealthStatistics        : 
                          Node                  : 5 Ok, 0 Warning, 0 Error
                          Replica               : 6 Ok, 0 Warning, 0 Error
                          Partition             : 1 Ok, 1 Warning, 0 Error
                          Service               : 1 Ok, 1 Warning, 0 Error
                          DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                          DeployedApplication   : 5 Ok, 0 Warning, 0 Error
                          Application           : 0 Ok, 1 Warning, 0 Error
```

Följande PowerShell-cmdlet hämtar hälso tillståndet för klustret med hjälp av en anpassad användnings princip. Den filtrerar resultaten så att endast program och noder visas i fel eller varning. Därför returneras inga noder, eftersom de är felfria. Endast programmet Fabric:/WordCount respekterar program filtret. Eftersom den anpassade principen anger att varningar ska övervägas som fel för programmet Fabric:/WordCount, utvärderas programmet som i fel och så är klustret.

```powershell
PS D:\ServiceFabric> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics


AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None
```

### <a name="rest"></a>REST
Du kan få kluster hälsa med en [Get-begäran](/rest/api/servicefabric/get-the-health-of-a-cluster) eller en [post-begäran](/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) som innehåller hälso principer som beskrivs i bröd texten.

## <a name="get-node-health"></a>Hämta nods hälsa
Returnerar hälsan för en nod-entitet och innehåller de hälso händelser som rapporter ATS på noden. Indata:

* Kunna Det nodnamn som identifierar noden.
* Valfritt Princip inställningarna för kluster hälsa som används för att utvärdera hälsan.
* Valfritt Filter för händelser som anger vilka poster som är av intresse och som ska returneras i resultatet (till exempel endast fel eller både varningar och fel). Alla händelser används för att utvärdera enhetens sammanlagda hälso tillstånd, oavsett filtret.

### <a name="api"></a>API
Om du vill hämta en nods hälsa genom API: et skapar du en `FabricClient` och anropar [GetNodeHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) -metoden på dess HealthManager.

Följande kod hämtar nodens hälsa för angivet nodnamn:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

Följande kod hämtar nodens hälsa för angivet nodnamn och skickar händelse filtret och den anpassade principen till [NodeHealthQueryDescription](/dotnet/api/system.fabric.description.nodehealthquerydescription):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Cmdleten för att hämta nodens hälsa är [Get-ServiceFabricNodeHealth](/powershell/module/servicefabric/get-servicefabricnodehealth). Anslut först till klustret med hjälp av cmdleten [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .
Följande cmdlet hämtar nodens hälsa genom att använda standard hälso principer:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 7/13/2017 4:39:23 PM
                        ReceivedAt            : 7/13/2017 4:40:47 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 4:40:47 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Följande cmdlet hämtar hälso tillståndet för alla noder i klustret:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_4                     Ok
_Node_3                     Ok
_Node_2                     Ok
_Node_1                     Ok
_Node_0                     Ok
```

### <a name="rest"></a>REST
Du kan få en nods hälsa med en [Get-begäran](/rest/api/servicefabric/get-the-health-of-a-node) eller en post- [begäran](/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) som innehåller hälso principer som beskrivs i bröd texten.

## <a name="get-application-health"></a>Hämta program hälsa
Returnerar hälsan för en program enhet. Den innehåller hälso tillståndet för det distribuerade programmet och underordnad tjänst. Indata:

* Kunna Det program namn (URI) som identifierar programmet.
* Valfritt Den program hälso princip som används för att åsidosätta program manifest principerna.
* Valfritt Filter för händelser, tjänster och distribuerade program som anger vilka poster som är av intresse och som ska returneras i resultatet (till exempel endast fel eller både varningar och fel). Alla händelser, tjänster och distribuerade program används för att utvärdera enhetens sammanlagda hälso tillstånd, oavsett filtret.
* Valfritt Filtrera för att undanta hälso statistik. Om detta inte anges innehåller hälso statistik antalet OK, varning och fel för alla program underordnade: tjänster, partitioner, repliker, distribuerade program och distribuerade tjänst paket.

### <a name="api"></a>API
Skapa en `FabricClient` och anropa [GetApplicationHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) -metoden på dess HealthManager för att få program hälsan.

Följande kod hämtar program hälsan för angivet program namn (URI):

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

Följande kod hämtar program hälsan för angivet program namn (URI) med filter och anpassade principer som anges via [ApplicationHealthQueryDescription](/dotnet/api/system.fabric.description.applicationhealthquerydescription).

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Cmdleten för att hämta program hälsan är [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth). Anslut först till klustret med hjälp av cmdleten [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .

Följande cmdlet returnerar hälso tillståndet för **Fabric:/WORDCOUNT-** programmet:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

DeployedApplicationHealthStates : 
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/13/2017 5:57:05 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics                : 
                                  Replica               : 6 Ok, 0 Warning, 0 Error
                                  Partition             : 1 Ok, 1 Warning, 0 Error
                                  Service               : 1 Ok, 1 Warning, 0 Error
                                  DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                                  DeployedApplication   : 5 Ok, 0 Warning, 0 Error
```

Följande PowerShell-cmdlet skickar i anpassade principer. Den filtrerar också underordnade och händelser.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.

ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>REST
Du kan få program hälsa med en [Get-begäran](/rest/api/servicefabric/get-the-health-of-an-application) eller en [post-begäran](/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) som innehåller hälso principer som beskrivs i bröd texten.

## <a name="get-service-health"></a>Hämta tjänstens hälsa
Returnerar hälsan för en tjänste enhet. Den innehåller hälso tillståndet för partitionen. Indata:

* Kunna Tjänst namnet (URI) som identifierar tjänsten.
* Valfritt Den program hälso princip som används för att åsidosätta program manifest principen.
* Valfritt Filter för händelser och partitioner som anger vilka poster som är av intresse och som ska returneras i resultatet (till exempel endast fel eller både varningar och fel). Alla händelser och partitioner används för att utvärdera enhetens sammanlagda hälso tillstånd, oavsett filtret.
* Valfritt Filtrera för att undanta hälso statistik. Om detta inte anges visar hälso statistik antalet OK, varning och fel för alla partitioner och repliker av tjänsten.

### <a name="api"></a>API
Om du vill hämta tjänstens hälso tillstånd genom API: et skapar du en `FabricClient` och anropar [GetServiceHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) -metoden på dess HealthManager.

Följande exempel hämtar hälso tillståndet för en tjänst med angivet tjänste namn (URI):

```csharp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

Följande kod hämtar tjänst hälsan för angivet tjänste namn (URI) och anger filter och anpassad princip via [ServiceHealthQueryDescription](/dotnet/api/system.fabric.description.servicehealthquerydescription):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Cmdleten för att hämta tjänstens hälsa är [Get-ServiceFabricServiceHealth](/powershell/module/servicefabric/get-servicefabricservicehealth). Anslut först till klustret med hjälp av cmdleten [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .

Följande cmdlet hämtar tjänst hälsan genom att använda standard hälso principer:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                        Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                            Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

PartitionHealthStates : 
                        PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                        AggregatedHealthState : Warning

HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 15
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
                        Partition             : 0 Ok, 1 Warning, 0 Error
```

### <a name="rest"></a>REST
Du kan få service Health med en [Get-begäran](/rest/api/servicefabric/get-the-health-of-a-service) eller en [post-begäran](/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) som innehåller hälso principer som beskrivs i bröd texten.

## <a name="get-partition-health"></a>Hämta partitionera hälsa
Returnerar hälsan för en partition-entitet. Den innehåller replikens hälso tillstånd. Indata:

* Kunna Partitions-ID (GUID) som identifierar partitionen.
* Valfritt Den program hälso princip som används för att åsidosätta program manifest principen.
* Valfritt Filter för händelser och repliker som anger vilka poster som är av intresse och som ska returneras i resultatet (till exempel endast fel eller både varningar och fel). Alla händelser och repliker används för att utvärdera enhetens sammanlagda hälso tillstånd, oavsett filtret.
* Valfritt Filtrera för att undanta hälso statistik. Om detta inte anges visar hälso statistiken hur många repliker som är i OK, varning och fel tillstånd.

### <a name="api"></a>API
Om du vill hämta partitionens hälso tillstånd genom API: et skapar du en `FabricClient` och anropar [GetPartitionHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) -metoden på dess HealthManager. Skapa [PartitionHealthQueryDescription](/dotnet/api/system.fabric.description.partitionhealthquerydescription)om du vill ange valfria parametrar.

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
Cmdleten för att hämta partitionens hälso tillstånd är [Get-ServiceFabricPartitionHealth](/powershell/module/servicefabric/get-servicefabricpartitionhealth). Anslut först till klustret med hjälp av cmdleten [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .

Följande cmdlet hämtar hälso tillståndet för alla partitioner i tjänsten **Fabric:/WORDCOUNT/WordCountService** och filtrerar bort replik hälso tillstånd:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 72
                        SentAt                : 7/13/2017 5:57:29 PM
                        ReceivedAt            : 7/13/2017 5:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/P RD _Node_2 Up 131444422260002646
                          N/S RD _Node_4 Up 131444422293113678
                          N/S RD _Node_3 Up 131444422293113679
                          N/S RD _Node_1 Up 131444422293118720
                          N/S RD _Node_0 Up 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5.)

                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 7/13/2017 5:57:48 PM, LastError = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131444445174851664
                        SentAt                : 7/13/2017 6:35:17 PM
                        ReceivedAt            : 7/13/2017 6:35:18 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A

                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None

                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/13/2017 5:57:48 PM, LastOk = 1/1/0001 12:00:00 AM

HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Du kan få partitionera hälsa med en [Get-begäran](/rest/api/servicefabric/sfclient-api-getpartitionhealth) eller en [post-begäran](/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) som innehåller hälso principer som beskrivs i bröd texten.

## <a name="get-replica-health"></a>Hämta replik hälsa
Returnerar hälsan för en tillstånds känslig tjänst replik eller en tillstånds lös tjänst instans. Indata:

* Kunna Partitions-ID (GUID) och replik-ID som identifierar repliken.
* Valfritt Princip parametrarna för program hälso tillstånd som används för att åsidosätta program manifest principerna.
* Valfritt Filter för händelser som anger vilka poster som är av intresse och som ska returneras i resultatet (till exempel endast fel eller både varningar och fel). Alla händelser används för att utvärdera enhetens sammanlagda hälso tillstånd, oavsett filtret.

### <a name="api"></a>API
Om du vill hämta replik hälsan genom API: et skapar du en `FabricClient` och anropar [GetReplicaHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) -metoden på dess HealthManager. Använd [ReplicaHealthQueryDescription](/dotnet/api/system.fabric.description.replicahealthquerydescription)om du vill ange avancerade parametrar.

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
Cmdleten för att hämta replik hälsan är [Get-ServiceFabricReplicaHealth](/powershell/module/servicefabric/get-servicefabricreplicahealth). Anslut först till klustret med hjälp av cmdleten [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .

Följande cmdlet hämtar hälso tillståndet för den primära repliken för alla partitioner i tjänsten:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Du kan få replik hälsa med en [Get-begäran](/rest/api/servicefabric/get-the-health-of-a-replica) eller en [post-begäran](/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) som innehåller hälso principer som beskrivs i bröd texten.

## <a name="get-deployed-application-health"></a>Hämta distribuerad program hälsa
Returnerar hälso tillståndet för ett program som har distribuerats på en Node-entitet. Den innehåller de distribuerade tjänst paketens hälso tillstånd. Indata:

* Kunna Det program namn (URI) och nodnamn (sträng) som identifierar det distribuerade programmet.
* Valfritt Den program hälso princip som används för att åsidosätta program manifest principerna.
* Valfritt Filter för händelser och distribuerade tjänst paket som anger vilka poster som är av intresse och som ska returneras i resultatet (till exempel endast fel eller både varningar och fel). Alla händelser och distribuerade tjänst paket används för att utvärdera enhetens sammanlagda hälso tillstånd, oavsett filtret.
* Valfritt Filtrera för att undanta hälso statistik. Om detta inte anges visar hälso statistik antalet distribuerade tjänst paket i hälso tillstånden OK, varning och fel.

### <a name="api"></a>API
Om du vill få hälso tillståndet för ett program som distribuerats på en nod via API: et, skapar `FabricClient` du en och anropar [GetDeployedApplicationHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) -metoden på dess HealthManager. Använd [DeployedApplicationHealthQueryDescription](/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription)om du vill ange valfria parametrar.

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
Cmdleten för att hämta det distribuerade programmets hälsa är [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth). Anslut först till klustret med hjälp av cmdleten [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) . Om du vill ta reda på var ett program distribueras kör du [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth) och tittar på de distribuerade programmens underordnade.

Följande cmdlet hämtar hälso tillståndet för **Fabric:/WORDCOUNT-** programmet som distribueras på **_Node_2**.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_0


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_0
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok

HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131444422261848308
                                     SentAt                : 7/13/2017 5:57:06 PM
                                     ReceivedAt            : 7/13/2017 5:57:17 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/13/2017 5:57:17 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics                   : 
                                     DeployedServicePackage : 2 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Du kan få distribuerad program hälsa med en [Get-begäran](/rest/api/servicefabric/get-the-health-of-a-deployed-application) eller en [post-begäran](/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) som innehåller hälso principer som beskrivs i bröd texten.

## <a name="get-deployed-service-package-health"></a>Hämta hälso tillstånd för distribuerade tjänst paket
Returnerar hälsan för en distribuerad tjänst paket enhet. Indata:

* Kunna Det program namn (URI), nodnamn (sträng) och tjänst manifest namn (sträng) som identifierar det distribuerade tjänst paketet.
* Valfritt Den program hälso princip som används för att åsidosätta program manifest principen.
* Valfritt Filter för händelser som anger vilka poster som är av intresse och som ska returneras i resultatet (till exempel endast fel eller både varningar och fel). Alla händelser används för att utvärdera enhetens sammanlagda hälso tillstånd, oavsett filtret.

### <a name="api"></a>API
Om du vill hämta hälsan för ett distribuerat tjänst paket via API: et skapar du en `FabricClient` och anropar [GetDeployedServicePackageHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) -metoden på dess HealthManager. Använd [DeployedServicePackageHealthQueryDescription](/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription)om du vill ange valfria parametrar.

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
Cmdleten för att hämta det distribuerade tjänst paketets hälso tillstånd är [Get-ServiceFabricDeployedServicePackageHealth](/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth). Anslut först till klustret med hjälp av cmdleten [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) . Om du vill se var ett program har distribuerats kör du [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth) och tittar på de distribuerade programmen. Om du vill se vilka service packs som finns i ett program tittar du på de distribuerade tjänst paketets underordnade i [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth) -utdata.

Följande cmdlet hämtar hälso tillståndet för **WordCountServicePkg** -tjänst paketet för programmet **Fabric:/WORDCOUNT** som distribueras på **_Node_2**. Entiteten har **system. hosting** -rapporter för lyckad aktivering av service-paket och start punkt och lyckad registrering av tjänst typ.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_2
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131444422267693359
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131444422267903345
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131444422272458374
                             SentAt                : 7/13/2017 5:57:07 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Du kan hämta det distribuerade tjänst paketets hälsa med en [Get-begäran](/rest/api/servicefabric/get-the-health-of-a-service-package) eller en [post-begäran](/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) som innehåller hälso principer som beskrivs i bröd texten.

## <a name="health-chunk-queries"></a>Hälso segments frågor
Hälso segment frågor kan returnera kluster underordnade med flera nivåer (rekursivt), per inkommande filter. Det stöder avancerade filter som ger mycket flexibilitet när du väljer de underordnade som ska returneras. Filtren kan ange underordnade med den unika identifieraren eller av andra grupp identifierare och/eller hälso tillstånd. Som standard ingår inga underordnade, i stället för hälso kommandon som alltid innehåller underordnade på den första nivån.

[Hälso frågorna](service-fabric-view-entities-aggregated-health.md#health-queries) returnerar bara underordnade objekt på första nivån för den angivna entiteten enligt de filter som krävs. Du måste anropa ytterligare hälso-API: er för varje enhet av intresse för att få underordnade objekt. På samma sätt måste du anropa ett hälso-API för varje önskad entitet för att få hälso tillståndet för vissa entiteter. Med avancerad filtrering i segment frågan kan du begära flera intressanta objekt i en fråga, vilket minimerar meddelande storleken och antalet meddelanden.

Värdet för segment frågan är att du kan hämta hälso tillstånd för fler kluster enheter (eventuellt alla kluster enheter som börjar vid nödvändig rot) i ett anrop. Du kan uttrycka komplexa hälso frågor som:

* Returnera endast program med fel och för dessa program ingår alla tjänster i varnings-eller fel. Inkludera alla partitioner för returnerade tjänster.
* Returnera endast hälsan för fyra program, som anges med deras namn.
* Returnera endast hälso tillståndet för program av önskad program typ.
* Returnera alla distribuerade entiteter på en nod. Returnerar alla program, alla distribuerade program på den angivna noden och alla distribuerade tjänst paket på den noden.
* Returnera alla repliker i fel. Returnerar alla program, tjänster, partitioner och endast repliker i fel.
* Returnera alla program. Inkludera alla partitioner för en angiven tjänst.

Hälso segment frågan exponeras för närvarande endast för entiteten kluster. Den returnerar ett kluster hälso segment, som innehåller:

* Klustrets sammanlagda hälso tillstånd.
* Listan med hälso tillstånds segment för noder som följer indatafilter.
* Segment listan över hälso tillstånd för program som respekterar indatafilter. Varje program hälso tillstånds segment innehåller en segment lista med alla tjänster som följer filter för indata och en segment lista med alla distribuerade program som följer filtren. Samma för underordnade tjänster och distribuerade program. På så sätt kan alla entiteter i klustret eventuellt returneras om de begärs hierarkiskt.

### <a name="cluster-health-chunk-query"></a>Segment fråga för kluster hälsa
Returnerar hälso tillståndet för klustrets entitet och innehåller de hierarkiska hälso tillstånds segment som krävs för underordnade. Indata:

* Valfritt Kluster hälso principen som används för att utvärdera noderna och kluster händelserna.
* Valfritt Princip kartan för program hälsa med de hälso principer som används för att åsidosätta program manifest principerna.
* Valfritt Filter för noder och program som anger vilka poster som är av intresse och som ska returneras i resultatet. Filtren är speciella för en entitet/grupp av entiteter eller gäller för alla entiteter på den nivån. Listan med filter kan innehålla ett allmänt filter och/eller filter för vissa identifierare för att identifiera entiteter som returneras av frågan. Om den är tom returneras inte underordnade som standard.
  Läs mer om filtren på [NodeHealthStateFilter](/dotnet/api/system.fabric.health.nodehealthstatefilter) och [ApplicationHealthStateFilter](/dotnet/api/system.fabric.health.applicationhealthstatefilter). Program filter kan rekursivt ange avancerade filter för underordnade.

Segment resultatet innehåller de underordnade objekten som respekterar filtren.

För närvarande returnerar segment frågan inte felfria utvärderingar eller enhets händelser. Den extra informationen kan hämtas med hjälp av den befintliga kluster hälso frågan.

### <a name="api"></a>API
Om du vill hämta klustrets hälso tillstånd skapar du ett `FabricClient` och anropar [GetClusterHealthChunkAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) -metoden på dess **HealthManager**. Du kan skicka in [ClusterHealthQueryDescription](/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) för att beskriva hälso principer och avancerade filter.

Följande kod hämtar ett kluster hälso segment med avancerade filter.

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Cmdleten för att hämta kluster hälsan är [Get-ServiceFabricClusterChunkHealth](/powershell/module/servicefabric/get-servicefabricclusterhealthchunk). Anslut först till klustret med hjälp av cmdleten [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .

Följande kod hämtar bara noder om de är i fel syfte, förutom för en speciell nod, som alltid ska returneras.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters


HealthState                  : Warning
NodeHealthStateChunks        : 
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

Följande cmdlet hämtar kluster segment med program filter.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 1

                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks : 
                                TotalCount            : 1

                                ServiceName           : fabric:/WordCount/WordCountService
                                HealthState           : Error
                                PartitionHealthStateChunks : 
                                    TotalCount            : 1

                                    PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                                    HealthState           : Error
                                    ReplicaHealthStateChunks : 
                                        TotalCount            : 5

                                        ReplicaOrInstanceId   : 131444422293118720
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293118721
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293113678
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293113679
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422260002646
                                        HealthState           : Error
```

Följande cmdlet returnerar alla distribuerade entiteter på en nod.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 2

                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1

                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1

                                    ServiceManifestName   : FAS
                                    ServicePackageActivationId : 
                                    HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1

                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1

                                    ServiceManifestName   : WordCountServicePkg
                                    ServicePackageActivationId : 
                                    HealthState           : Ok
```

### <a name="rest"></a>REST
Du kan hämta kluster hälso segment med en [Get-begäran](/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) eller en [post-begäran](/rest/api/servicefabric/health-of-cluster) som innehåller hälso principer och avancerade filter som beskrivs i bröd texten.

## <a name="general-queries"></a>Allmänna frågor
Allmänna frågor returnerar en lista med Service Fabric entiteter av en angiven typ. De exponeras via API: et (via metoderna på **FabricClient. QueryManager**), PowerShell-cmdletar och rest. Dessa frågor sammanställer under frågor från flera komponenter. En av dem är [hälso arkivet](service-fabric-health-introduction.md#health-store), som fyller i det sammanställda hälso tillståndet för varje frågeresultat.  

> [!NOTE]
> Allmänna frågor returnerar det sammanställda hälso tillståndet för entiteten och innehåller inte omfattande hälso data. Om en entitet inte är felfri kan du följa upp med hälso frågor för att få all hälso information, inklusive händelser, underordnade hälso tillstånd och utvärderingar som inte är felfria.
>
>

Om allmänna frågor returnerar ett okänt hälso tillstånd för en entitet, är det möjligt att hälso lagret inte har fullständiga data om entiteten. Det är också möjligt att en under fråga till hälso lagret inte lyckades (till exempel ett kommunikations fel eller att hälso lagret har begränsats). Följ upp med en hälso fråga för entiteten. Om under frågan påträffade tillfälliga fel, t. ex. nätverks problem, kan denna uppföljnings fråga lyckas. Du kan också få mer information från Health Store om varför enheten inte visas.

Frågorna **som innehåller** hälsoattribut för entiteter är:

* Node-lista: returnerar List-noderna i klustret (växlat).
  * API: [FabricClient. QueryClient. GetNodeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * PowerShell: Get-ServiceFabricNode
* Program lista: returnerar listan över program i klustret (växlat).
  * API: [FabricClient. QueryClient. GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * PowerShell: Get-ServiceFabricApplication
* Tjänst lista: returnerar listan över tjänster i ett program (växlat).
  * API: [FabricClient. QueryClient. GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * PowerShell: Get-ServiceFabricService
* Partition lista: returnerar listan över partitioner i en tjänst (växlad).
  * API: [FabricClient. QueryClient. GetPartitionListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * PowerShell: Get-ServiceFabricPartition
* Replik lista: returnerar listan över repliker i en partition (växlad).
  * API: [FabricClient. QueryClient. GetReplicaListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * PowerShell: Get-ServiceFabricReplica
* Distribuerad program lista: returnerar listan över distribuerade program på en nod.
  * API: [FabricClient. QueryClient. GetDeployedApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication
* Distribuerad Service Pack-lista: returnerar listan över tjänst paket i ett distribuerat program.
  * API: [FabricClient. QueryClient. GetDeployedServicePackageListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> Några av frågorna returnerar växlade resultat. Att returnera dessa frågor är en lista som härletts [från \<T> PagedList](/dotnet/api/system.fabric.query.pagedlist-1). Om resultatet inte passar ett meddelande returneras bara en sida och en ContinuationToken som spårar där uppräkningen stoppas. Fortsätt att anropa samma fråga och skicka i fortsättnings-token från föregående fråga för att få nästa resultat.

### <a name="examples"></a>Exempel
Följande kod hämtar felaktiga program i klustret:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

Följande cmdlet hämtar programinformationen för programmet Fabric:/WordCount. Observera att hälso tillståndet är vid varning.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

Följande cmdlet hämtar tjänsterna med hälso tillståndet fel:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Error"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Error
```

## <a name="cluster-and-application-upgrades"></a>Kluster-och program uppgraderingar
Under en övervakad uppgradering av klustret och programmet kontrollerar Service Fabric hälso tillståndet för att se till att allt förblir felfritt. Om en entitet inte är felfri som utvärderas med hjälp av konfigurerade hälso principer, tillämpar uppgraderingen de uppgraderings bara principerna för att fastställa nästa åtgärd. Uppgraderingen kan pausas för att tillåta användar interaktion (till exempel att åtgärda fel villkor eller ändra principer), eller så kan den automatiskt återställas till den tidigare versionen.

Under en *kluster* uppgradering kan du hämta uppgraderings status för klustret. Uppgraderings statusen innehåller felaktiga utvärderingar som pekar på vad som är skadat i klustret. Om uppgraderingen återställs på grund av hälso problem, kommer uppgraderings statusen att återställa de senaste Felaktiga orsakerna. Den här informationen kan hjälpa administratörer att undersöka vad som gick fel efter att uppgraderingen har återställts eller stoppats.

På samma sätt ingår eventuella utvärderingar som är felaktiga i program uppgraderings status under en *program* uppgradering.

Följande visar program uppgraderings status för ett ändrat Fabric:/WordCount-program. En övervaknings enhet rapporterade ett fel på en av dess repliker. Uppgraderingen återställs eftersom hälso kontrollerna inte följs.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2017 5:23:26 PM
FailureTimestampUtc           : 4/21/2017 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Läs mer om [uppgraderingen av Service Fabric-programmet](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Felsöka med hjälp av hälso utvärderingar
När det uppstår ett problem med klustret eller ett program tittar du på klustret eller program hälsan för att hitta det som är fel. Utvärderingarna av felaktiga fel ger information om vad som utlöste det aktuella hälso tillståndet. Om du behöver kan du öka detalj nivån för underordnade entiteter som inte är felfria för att identifiera rotor saken.

Anta till exempel att ett program inte är felfritt eftersom det finns en fel rapport på någon av dess repliker. Följande PowerShell-cmdlet visar de felaktiga utvärderingarna:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount -EventsFilter None -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                        Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                        Unhealthy replica: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', ReplicaOrInstanceId='131444422260002646', AggregatedHealthState='Error'.

                                            Error event: SourceId='MyWatchdog', Property='Memory'.

ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : None
```

Du kan titta på repliken om du vill ha mer information:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricReplicaHealth -ReplicaOrInstanceId 131444422260002646 -PartitionId af2e3e44-a8f8-45ac-9f31-4093eb897600


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Error
UnhealthyEvaluations  : 
                        Error event: SourceId='MyWatchdog', Property='Memory'.

HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : MyWatchdog
                        Property              : Memory
                        HealthState           : Error
                        SequenceNumber        : 131444451657749403
                        SentAt                : 7/13/2017 6:46:05 PM
                        ReceivedAt            : 7/13/2017 6:46:05 PM
                        TTL                   : Infinite
                        Description           : 
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 7/13/2017 6:46:05 PM, LastOk = 1/1/0001 12:00:00 AM
```

> [!NOTE]
> Utvärderingarna visar den första anledningen som entiteten utvärderas till det aktuella hälso tillståndet. Det kan finnas flera andra händelser som utlöser detta tillstånd, men de visas inte i utvärderingarna. Om du vill ha mer information kan du öka detalj nivån i hälsoentiteterna för att ta reda på om det finns felaktiga rapporter i klustret.
>
>

## <a name="next-steps"></a>Nästa steg
[Felsök med hjälp av systemhälsorapporter](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Lägg till anpassade Service Fabric hälso rapporter](service-fabric-report-health.md)

[Rapportera och kontrol lera tjänstens hälsa](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Övervaka och diagnostisera tjänster lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric program uppgradering](service-fabric-application-upgrade.md)
