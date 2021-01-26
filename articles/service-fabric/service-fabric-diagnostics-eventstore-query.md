---
title: 'Fråga efter kluster händelser med EventStore-API: er'
description: 'Lär dig hur du använder Azure Service Fabric EventStore-API: er för att fråga efter plattforms händelser'
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.custom: devx-track-csharp
ms.openlocfilehash: 6bed26227542cbf3ffc13ecc018aef9e659d026e
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791636"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>Fråga EventStore-API: er för kluster händelser

Den här artikeln beskriver hur du frågar EventStore-API: er som är tillgängliga i Service Fabric version 6,2 och senare – om du vill lära dig mer om EventStore-tjänsten går du till [Översikt över EventStore-tjänsten](service-fabric-diagnostics-eventstore.md). För närvarande kan EventStore-tjänsten endast komma åt data under de senaste 7 dagarna (detta baseras på klustrets princip för data bevarande för diagnostikdata).

>[!NOTE]
>EventStore-API: erna är GA till Service Fabric version 6,4 för endast Windows-kluster som körs på Azure.

EventStore-API: er kan nås direkt via en REST-slutpunkt eller program mässigt. Beroende på frågan finns det flera parametrar som krävs för att samla in rätt data. Dessa parametrar omfattar vanligt vis:
* `api-version`: den version av EventStore-API: er som du använder
* `StartTimeUtc`: definierar början på den period som du är intresse rad av
* `EndTimeUtc`: slutet av tids perioden

Förutom dessa parametrar finns även valfria parametrar tillgängliga, till exempel:
* `timeout`: Åsidosätt standard tids gränsen på 60 sekund för att utföra åtgärden för begäran
* `eventstypesfilter`: Detta ger dig möjlighet att filtrera efter vissa händelse typer
* `ExcludeAnalysisEvents`: returnera inte analys händelser. Som standard returnerar EventStore-frågor med "analys"-händelser där det är möjligt. Analys händelser är rikare drift kanal händelser som innehåller ytterligare kontext eller information utöver en vanlig Service Fabric händelse och ger större djup.
* `SkipCorrelationLookup`: Sök inte efter potentiella korrelerade händelser i klustret. Som standard försöker EventStore korrelera händelser i ett kluster och länka dina händelser tillsammans när så är möjligt. 

Varje entitet i ett kluster kan vara frågor om händelser. Du kan också fråga efter händelser för alla entiteter av typen. Du kan till exempel fråga efter händelser för en speciell nod eller för alla noder i klustret. Den aktuella uppsättningen entiteter som du kan använda för att fråga efter händelser är (med hur frågan skulle struktureras):
* Flernodskluster `/EventsStore/Cluster/Events`
* Artikelnoder `/EventsStore/Nodes/Events`
* Nodfel `/EventsStore/Nodes/<NodeName>/$/Events`
* Ansökan `/EventsStore/Applications/Events`
* Applicering `/EventsStore/Applications/<AppName>/$/Events`
* Terminal `/EventsStore/Services/Events`
* Telefonitjänstprovider `/EventsStore/Services/<ServiceName>/$/Events`
* Partitioner `/EventsStore/Partitions/Events`
* Partitionstabellen `/EventsStore/Partitions/<PartitionID>/$/Events`
* Repliker `/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* Replica `/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>När du refererar till ett program eller tjänst namn behöver inte frågan inkludera "Fabric:/" protokollprefixet. Om ditt program eller tjänst namn har en "/" i dem, kan du även växla till "~" för att hålla frågan igång. Om ditt program exempelvis visas som "Fabric:/APP1/FrontendApp", struktureras dina app-specifika frågor som `/EventsStore/Applications/App1~FrontendApp/$/Events` .
>Dessutom visas hälso rapporter för tjänster i dag i motsvarande program, så du skulle fråga efter `DeployedServiceHealthReportCreated` händelser för rätt program enhet. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>Fråga EventStore via REST API-slutpunkter

Du kan fråga EventStore direkt via en REST-slutpunkt genom att göra `GET` förfrågningar till: `<your cluster address>/EventsStore/<entity>/Events/` .

För att till exempel fråga efter alla kluster händelser mellan `2018-04-03T18:00:00Z` och `2018-04-04T18:00:00Z` , skulle din begäran se ut så här:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

Detta kan antingen returnera inga händelser eller listan över händelser som returneras i JSON:

```json
Response: 200
Body:
[
  {
    "Kind": "ClusterUpgradeStart",
    "CurrentClusterVersion": "0.0.0.0:",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeType": "Rolling",
    "RollingUpgradeMode": "UnmonitoredAuto",
    "FailureAction": "Manual",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:18:59.4313064Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(0 1 2)",
    "UpgradeDomainElapsedTimeInMs": "78.5288",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:19:59.5729953Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(3 4)",
    "UpgradeDomainElapsedTimeInMs": "0",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.6271949Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeComplete",
    "TargetClusterVersion": "6.2:1.0",
    "OverallUpgradeElapsedTimeInMs": "120196.5212",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.8134457Z",
    "HasCorrelatedEvents": false
  }
]
```

Här kan vi se att `2018-04-03T18:00:00Z` `2018-04-04T18:00:00Z` det här klustret har slutfört sin första uppgradering när det var första Stood, från `"CurrentClusterVersion": "0.0.0.0:"` till `"TargetClusterVersion": "6.2:1.0"` , i `"OverallUpgradeElapsedTimeInMs": "120196.5212"` .

## <a name="query-the-eventstore-programmatically"></a>Fråga EventStore program mässigt

Du kan också fråga EventStore program mässigt via [Service Fabric klient biblioteket](/dotnet/api/overview/azure/service-fabric#client-library).

När du har konfigurerat Service Fabric-klienten kan du fråga efter händelser genom att komma åt EventStore så här: `sfhttpClient.EventStore.<request>`

Här är en exempel förfrågan för alla kluster händelser mellan `2018-04-03T18:00:00Z` och `2018-04-04T18:00:00Z` , via `GetClusterEventListAsync` funktionen.

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

## <a name="sample-scenarios-and-queries"></a>Exempel scenarier och frågor

Här följer några exempel på hur du kan anropa REST-API: erna för Event Store för att förstå status för klustret.

*Kluster uppgraderingar:*

Om du vill se den senaste gången klustret kördes eller försökte uppgradera förra veckan, kan du fråga API: erna för nyligen slutförda uppgraderingar till klustret genom att fråga efter "ClusterUpgradeCompleted"-händelser i EventStore: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*Problem med kluster uppgradering:*

Om det uppstod problem med en senaste kluster uppgradering kan du fråga efter alla händelser för entiteten kluster. Du ser olika händelser, inklusive initiering av uppgraderingar och varje UD för vilka uppgraderingen har slutförts. Du ser även händelser för den punkt där återställningen startade och motsvarande hälso händelser. Här är den fråga som du skulle använda för detta: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Status ändringar för nod:*

Om du vill se ändringar i din nods status under de senaste dagarna – när noderna gick upp eller ned, eller om de har Aktiver ATS eller inaktiverats (antingen av plattformen, kaos-tjänsten eller från användarindata) – använder du följande fråga: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Program händelser:*

Du kan också spåra dina senaste program distributioner och uppgraderingar. Använd följande fråga för att se alla program händelser i klustret: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Historisk hälso tillstånd för ett program:*

Förutom att bara se programmets livs cykel händelser kanske du också vill se historiska data om hälsan för ett särskilt program. Du kan göra detta genom att ange det program namn som du vill samla in data för. Använd den här frågan för att hämta alla program hälso händelser: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport` . Om du vill ta med hälso tillstånds händelser som kan ha upphört att gälla (passerat TTL-värdet (Time to Live)) lägger du till `,ApplicationHealthReportExpired` i slutet av frågan för att filtrera på två typer av händelser.

*Historisk hälsa för alla tjänster i "myApp":*

Hälso rapport händelser för tjänster visas för närvarande som `DeployedServicePackageNewHealthReport` händelser under motsvarande program enhet. Använd följande fråga för att se hur dina tjänster har utförts för "APP1": `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*Omkonfiguration av partition:*

Fråga efter händelsen om du vill se alla partitioner som har hänt i klustret `PartitionReconfigured` . Detta kan hjälpa dig att ta reda på vilka arbets belastningar som körs på den noden vid specifika tidpunkter när du diagnostiserar problem i klustret. Här är en exempel fråga som gör följande: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*Kaos-tjänst:*

Det finns en händelse när kaos-tjänsten startas eller stoppas och som exponeras på kluster nivå. Om du vill se din senaste användning av kaos-tjänsten använder du följande fråga: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`
