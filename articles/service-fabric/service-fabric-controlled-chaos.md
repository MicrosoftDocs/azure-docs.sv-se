---
title: Inducera kaos i Service Fabric kluster
description: 'Använd API: er för fel inmatning och kluster analys tjänst för att hantera kaos i klustret.'
ms.topic: conceptual
ms.date: 02/05/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 72b8f7e9e4934b516f843ae8bc9bb7adc1c349ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720518"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Inducera kontrollerade kaos i Service Fabric kluster
Storskaliga distribuerade system som moln infrastrukturer är mycket otillförlitliga. Med Azure Service Fabric kan utvecklare skriva pålitliga distribuerade tjänster ovanpå en otillförlitlig infrastruktur. För att kunna skriva robusta distribuerade tjänster ovanpå en otillförlitlig infrastruktur måste utvecklare kunna testa stabiliteten hos sina tjänster medan den underliggande otillförlitliga infrastrukturen går igenom komplicerade tillstånds över gångar på grund av fel.

[Fel inmatningen och kluster analys tjänsten](./service-fabric-testability-overview.md) (även kallat fel analys tjänsten) ger utvecklare möjlighet att inducera fel för att testa sina tjänster. De här riktade, simulerade felen, som att [starta om en partition](/powershell/module/servicefabric/start-servicefabricpartitionrestart), kan hjälpa de vanligaste tillstånds över gångarna. De riktade simulerade felen är dock fördelade efter definition och kan därför missa buggar som bara visar upp till en svår och komplicerad sekvens av tillstånds över gångar. För en systematisk testning kan du använda kaos.

Kaos simulerar periodiska, överlagrade fel (både korrekt och oäkta) i hela klustret under en längre tid. Ett korrekt fel består av en uppsättning Service Fabric-API-anrop, till exempel om ett fel uppstår vid omstart av repliker, eftersom det här är en nära följd av en öppen på en replik. Ta bort replikering, flytta den primära repliken och flytta sekundär replik är de andra fel som genomförs av kaos. Inaktiva fel är process avslutningar, t. ex. Starta om nod och starta om kod paket. 

När du har konfigurerat kaos med frekvensen och typen av fel kan du starta kaos via C#, PowerShell eller REST API för att börja generera fel i klustret och i dina tjänster. Du kan konfigurera kaos så att det körs under en angiven tids period (till exempel i en timme), efter vilken kaos slutar automatiskt, eller så kan du anropa StopChaos API (C#, PowerShell eller REST) för att stoppa det när som helst.

> [!NOTE]
> I det aktuella formuläret inducerar kaos endast säkra fel, vilket innebär att om externa fel inträffar, eller om data förluster aldrig uppstår.
>

Medan kaos körs, genererar den olika händelser som avbildar körnings status för tillfället. Till exempel innehåller en ExecutingFaultsEvent alla fel som kaos har valt att köra i den iterationen. En ValidationFailedEvent innehåller information om ett verifierings fel (hälso tillstånd eller stabilitets problem) som påträffades under valideringen av klustret. Du kan anropa GetChaosReport-API: et (C#, PowerShell eller REST) för att hämta rapporten över kaos-körningar. Dessa händelser sparas i en [tillförlitlig ord lista](./service-fabric-reliable-services-reliable-collections.md), som har en trunkisk princip som styrs av två konfigurationer: **MaxStoredChaosEventCount** (standardvärdet är 25000) och **StoredActionCleanupIntervalInSeconds** (Standardvärdet är 3600). Varje *StoredActionCleanupIntervalInSeconds* kaos-kontroll och alla de senaste *MaxStoredChaosEventCount* -händelserna rensas bort från den tillförlitliga ord listan.

## <a name="faults-induced-in-chaos"></a>Fel som inducerats i kaos
Kaos genererar fel i hela Service Fabric kluster och komprimerar fel som visas på månader eller år till några timmar. Kombinationen av överlagrade fel med den höga fel frekvensen hittar hörn fall som annars kan missas. Den här övningen av kaos leder till en betydande förbättring av tjänstens kod kvalitet.

Kaos inducerar fel från följande kategorier:

* Starta om en nod
* Starta om ett distribuerat kod paket
* Ta bort en replik
* Starta om en replik
* Flytta en primär replik (konfigurerbar)
* Flytta en sekundär replik (kan konfigureras)

Kaos körs i flera iterationer. Varje iteration består av fel och kluster verifiering för den angivna perioden. Du kan konfigurera den tid som krävs för att klustret ska stabiliseras och för att verifieringen ska lyckas. Om ett fel upptäcks i kluster valideringen genererar kaos och sparar en ValidationFailedEvent med UTC-tidsstämpeln och fel information. Anta till exempel att en instans av kaos som är inställd på att köras i en timme med högst tre samtidiga fel. Kaos inducerar tre fel och validerar sedan kluster hälsan. Den upprepas genom föregående steg tills den explicit stoppas genom StopChaosAsync-API: et eller en timmes pass. Om klustret blir ohälsosamt i en iteration (dvs. det inte stabiliseras eller inte skadas i den skickade MaxClusterStabilizationTimeout), genererar kaos en ValidationFailedEvent. Den här händelsen anger att något har gått fel och kan kräva ytterligare undersökning.

Om du vill ta reda på vilka fel kaos som induceras kan du använda GetChaosReport API (PowerShell, C# eller REST). API: et hämtar nästa segment i kaos-rapporten baserat på den inskickade fortsättnings-token eller det angivna tidsintervallet. Du kan antingen ange ContinuationToken för att hämta nästa segment i kaos-rapporten eller ange tidsintervallet via StartTimeUtc och EndTimeUtc, men du kan inte ange både ContinuationToken och tidsintervallet i samma anrop. Om det finns fler än 100 kaos-händelser returneras kaos-rapporten i segment där ett segment inte innehåller fler än 100 kaos-händelser.

## <a name="important-configuration-options"></a>Viktiga konfigurations alternativ
* **TimeToRun**: sammanlagd tid som kaos körs innan den slutförs. Du kan stoppa kaos innan den har körts för TimeToRun-perioden via StopChaos-API: et.

* **MaxClusterStabilizationTimeout**: den längsta tid det tar att vänta på att klustret ska bli felfritt innan du skapar en ValidationFailedEvent. Detta väntar på att minska belastningen på klustret medan den återställs. De kontroller som utförs är:
  * Om klustrets hälsa är OK
  * Om tjänstens hälsa är OK
  * Om storleken på mål replik uppsättningen uppnås för-tjänstepartitionen
  * Det finns inga inbyggda repliker
* **Timestamputcinticks**: det maximala antalet samtidiga fel som induceras i varje iteration. Ju högre siffra, desto mer aggressiva kaos är och de kombinationer av tillstånds över gångar som klustret går igenom är också mer komplexa. 

> [!NOTE]
> Oavsett hur högt ett värde *timestamputcinticks* har, kaos garantier – i avsaknad av externa fel – det finns ingen förlust av kvorum eller data förlust.
>

* **EnableMoveReplicaFaults**: aktiverar eller inaktiverar de fel som orsakar att de primära eller sekundära replikerna flyttas. Dessa fel är aktiverade som standard.
* **WaitTimeBetweenIterations**: hur lång tid som ska förflyta mellan iterationer. Det vill säga hur lång tid som kaos pausas när du har kört en runda av fel och slutfört motsvarande verifiering av hälso tillståndet för klustret. Ju högre värde, desto lägre är den genomsnittliga frekvensen för fel inmatning.
* **WaitTimeBetweenFaults**: hur lång tid som ska förflyta mellan två efterföljande fel i en enda iteration. Ju högre värde, desto lägre samtidighet (eller överlappande) fel.
* **ClusterHealthPolicy**: kluster hälso princip används för att verifiera hälso tillståndet för klustret i mellan kaos-iterationer. Om kluster hälsan är ett fel eller om ett oväntat undantag uppstår under fel körningen, väntar kaos i 30 minuter innan nästa hälso kontroll-för att ge klustret en tid till recuperate.
* **Kontext**: en samling (sträng, sträng) typ nyckel/värde-par. Kartan kan användas för att registrera information om kaos-körningen. Det får inte finnas fler än 100 sådana par och varje sträng (nyckel eller värde) får innehålla högst 4095 tecken. Den här kartan anges av Start programmet för kaos-körningen för att eventuellt lagra kontexten för den aktuella körningen.
* **ChaosTargetFilter**: det här filtret kan endast användas för att rikta kaos-fel till vissa nodtyper eller endast till vissa program instanser. Om ChaosTargetFilter inte används, orsakar kaos fel alla kluster enheter. Om ChaosTargetFilter används, orsakar kaos bara de entiteter som uppfyller ChaosTargetFilter-specifikationen. NodeTypeInclusionList och ApplicationInclusionList tillåter endast unions semantik. Med andra ord går det inte att ange en skärning av NodeTypeInclusionList och ApplicationInclusionList. Det går till exempel inte att ange "fel det här programmet endast när det finns på den nodtypen". När en entitet ingår i antingen NodeTypeInclusionList eller ApplicationInclusionList kan den entiteten inte uteslutas med hjälp av ChaosTargetFilter. Även om applicationX inte visas i ApplicationInclusionList, kan en del kaos iteration applicationX vara fel eftersom det sker på en nod i nodeTypeY som ingår i NodeTypeInclusionList. Om både NodeTypeInclusionList och ApplicationInclusionList är null eller tomma genereras en ArgumentException.
    * **NodeTypeInclusionList**: en lista över nodtyper som ska ingå i kaos-fel. Alla typer av fel (starta om nod, starta om codepackage, ta bort replikering, starta om replikering, flytta primär och flytta sekundär) är aktiverade för noderna i dessa nodtyper. Om en NodeType (t. ex. NodeTypeX) inte visas i NodeTypeInclusionList aktive ras inte noder på radnivå (t. ex. NodeRestart) för noderna i NodeTypeX, men kod paketet och replik felen kan fortfarande aktive ras för NodeTypeX om ett program i ApplicationInclusionList sker på en nod i NodeTypeX. Högst 100 kan inkluderas i den här listan för att öka det här antalet, en konfigurations uppgradering krävs för MaxNumberOfNodeTypesInChaosTargetFilter-konfiguration.
    * **ApplicationInclusionList**: en lista över program-URI: er som ska ingå i kaos-fel. Alla repliker som hör till tjänster av dessa program är lämpar till replik fel (starta om replikering, ta bort replik, flytta primär och flytta sekundär) med kaos. Kaos kan bara starta om ett kod paket om kod paketet bara är värd för repliker av dessa program. Om ett program inte visas i listan kan det fortfarande uppstå fel i vissa kaos-iterationer om programmet avslutas på en nod i en nodtyp som ingår i NodeTypeInclusionList. Men om applicationX är knutet till nodeTypeY genom placerings begränsningar och applicationX saknas från ApplicationInclusionList och nodeTypeY inte finns i NodeTypeInclusionList, kommer applicationX aldrig att bli fel. Högst 1000-program namn kan inkluderas i den här listan för att öka det här antalet, så krävs en konfigurations uppgradering för MaxNumberOfApplicationsInChaosTargetFilter-konfigurationen.

## <a name="how-to-run-chaos"></a>Så här kör du kaos

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }
        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;

            // The maximum amount of time to wait for all cluster entities to become stable and healthy. 
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster
            // entities. 
            // During validation if a cluster entity is not stable and healthy within
            // MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex
            // series of states to uncover bugs.
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be
            // used to record information about the Chaos run. There cannot be more than 100 such pairs and
            // each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the
            // lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration.
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of
            // state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };

            // All types of faults, restart node, restart code package, restart replica, move primary replica,
            // and move secondary replica will happen for nodes of type 'FrontEndType'
            var nodetypeInclusionList = new List<string> { "FrontEndType"};

            // In addition to the faults included by nodetypeInclusionList,
            // restart code package, restart replica, move primary replica, move secondary replica faults will
            // happen for 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing
            // on a node which is not of type included in nodeypeInclusionList.
            var applicationInclusionList = new List<string> { "fabric:/TestApp2" };

            // List of cluster entities to target for Chaos faults.
            var chaosTargetFilter = new ChaosTargetFilter
            {
                NodeTypeInclusionList = nodetypeInclusionList,
                ApplicationInclusionList = applicationInclusionList
            };

            var parameters = new ChaosParameters(
                maxClusterStabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun,
                startContext,
                waitTimeBetweenIterations,
                waitTimeBetweenFaults,
                clusterHealthPolicy) {ChaosTargetFilter = chaosTargetFilter};

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            string continuationToken = null;

            while (true)
            {
                ChaosReport report;
                try
                {
                    report = string.IsNullOrEmpty(continuationToken)
                        ? client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult()
                        : client.TestManager.GetChaosReportAsync(continuationToken).GetAwaiter().GetResult();
                }
                catch (Exception e)
                {
                    if (e is FabricTransientException)
                    {
                        Console.WriteLine("A transient exception happened: '{0}'", e);
                    }
                    else if(e is TimeoutException)
                    {
                        Console.WriteLine("A timeout exception happened: '{0}'", e);
                    }
                    else
                    {
                        throw;
                    }

                    Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
                    continue;
                }

                continuationToken = report.ContinuationToken;

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.Add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```

```powershell
$clusterConnectionString = "localhost:19000"
$timeToRunMinute = 60

# The maximum amount of time to wait for all cluster entities to become stable and healthy.
# Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities.
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds,
# Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration.
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of
# states to uncover bugs.
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the
# fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration.
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state
# transitions that the cluster goes through.
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record
# information about the Chaos run.
# There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

#List of cluster entities to target for Chaos faults.
$chaosTargetFilter = new-object -TypeName System.Fabric.Chaos.DataStructures.ChaosTargetFilter
$chaosTargetFilter.NodeTypeInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# All types of faults, restart node, restart code package, restart replica, move primary replica, and move
# secondary replica will happen for nodes of type 'FrontEndType'
$chaosTargetFilter.NodeTypeInclusionList.AddRange( [string[]]@("FrontEndType") )
$chaosTargetFilter.ApplicationInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# In addition to the faults included by nodetypeInclusionList, 
# restart code package, restart replica, move primary replica, move secondary replica faults will happen for
# 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is
# not of type included in nodeypeInclusionList.
$chaosTargetFilter.ApplicationInclusionList.Add("fabric:/TestApp2")

Connect-ServiceFabricCluster $clusterConnectionString

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRunMinute -MaxConcurrentFaults $maxConcurrentFaults -MaxClusterStabilizationTimeoutSec $maxClusterStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ClusterHealthPolicy $clusterHealthPolicy -ChaosTargetFilter $chaosTargetFilter

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                Write-Host $e
                # When Chaos stops, a StoppedEvent is created.
                # If a StoppedEvent is found, exit the loop.
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    return
                }
            }
        }
    }

    Start-Sleep -Seconds 1
}
```
