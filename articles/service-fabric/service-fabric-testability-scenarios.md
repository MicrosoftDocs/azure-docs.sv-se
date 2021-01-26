---
title: Skapa kaos-och redundanstestning-test för Azure Service Fabric
description: Använd Service Fabric kaos test-och failover-test för att orsaka fel och kontrol lera tillförlitligheten för dina tjänster.
author: motanv
ms.topic: conceptual
ms.date: 10/1/2019
ms.author: motanv
ms.custom: devx-track-csharp
ms.openlocfilehash: 13e22633008a4a5df25d84920386a22696648c9a
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98789321"
---
# <a name="testability-scenarios"></a>Testnings scenarier
Stora distribuerade system som moln infrastrukturer är mycket otillförlitliga. Azure Service Fabric ger utvecklare möjlighet att skriva tjänster som kan köras ovanpå otillförlitliga infrastrukturer. För att kunna skriva tjänster med hög kvalitet måste utvecklare kunna inducera sådan tillförlitlig infrastruktur för att testa stabiliteten hos sina tjänster.

Tjänsten fel analys ger utvecklare möjlighet att inducera fel åtgärder för att testa tjänster i närvaro av fel. Riktade, simulerade fel kommer dock bara att få dig hittills. Om du vill utföra testningen ytterligare kan du använda test scenarierna i Service Fabric: ett kaos-test och ett redundanstest. De här scenarierna simulerar kontinuerliga överlagrade fel, både på ett smidigt och strömavbrott, i hela klustret under längre tids perioder. När ett test har kon figurer ATS med frekvensen och typen av fel kan du starta via antingen C# API: er eller PowerShell för att generera fel i klustret och tjänsten.

> [!WARNING]
> ChaosTestScenario ersätts av en mer elastisk, service-baserad kaos. Mer information finns i den nya artikeln [styrda kaos](service-fabric-controlled-chaos.md) .
> 
> 

## <a name="chaos-test"></a>Kaos-test
Kaos-scenariot genererar fel i hela Service Fabric klustret. Scenariot komprimerar fel som vanligt vis visas på månader eller år till några timmar. Kombinationen av överlagrade fel med den höga fel frekvensen hittar hörn fall som annars saknas. Detta leder till en betydande förbättring av tjänstens kod kvalitet.

### <a name="faults-simulated-in-the-chaos-test"></a>Fel simulerade i kaos-testet
* Starta om en nod
* Starta om ett distribuerat kod paket
* Ta bort en replik
* Starta om en replik
* Flytta en primär replik (valfritt)
* Flytta en sekundär replik (valfritt)

Kaos-testet kör flera iterationer av fel och kluster verifieringar under den angivna tids perioden. Den tid som krävs för att klustret ska stabiliseras och för att verifieringen ska lyckas kan också konfigureras. Scenariot fungerar inte när du träffar ett enstaka fel i kluster verifieringen.

Anta till exempel att en test uppsättning körs i en timme med högst tre samtidiga fel. Testet kommer att orsaka tre fel och sedan verifiera kluster hälsan. Testet itererar genom föregående steg till klustret blir ohälsosamt eller en timmes pass. Om klustret blir ohälsosamt i en iteration, dvs. det inte stabiliseras inom en konfigurerad tid, kommer testet inte att fungera med ett undantag. Detta undantag anger att något har gått fel och behöver ytterligare undersökning.

I det aktuella formuläret inducerar motorn för fel generering i kaos-testet endast säkra fel. Det innebär att om inga externa fel uppstår uppstår det aldrig något kvorum eller data förlust.

### <a name="important-configuration-options"></a>Viktiga konfigurations alternativ
* **TimeToRun**: den totala tid som testet ska köras innan det slutförs. Testet kan avslutas tidigare i stället för ett verifierings problem.
* **MaxClusterStabilizationTimeout**: maximal vänte tid för klustret att bli felfritt innan testet avbryts. De kontroller som utförs är om kluster hälsan är OK, om tjänst hälsan är OK, storleken på mål replik uppsättningen uppnås för tjänstepartitionen, och det finns inga inbyggda repliker.
* **Timestamputcinticks**: maximalt antal samtidiga fel som induceras i varje iteration. Ju högre siffra, desto mer aggressiva testet, vilket resulterar i mer komplexa redundanser och över gångs kombinationer. Testet garanterar att det inte uppstår något kvorum eller data förlust, oavsett hur hög den här konfigurationen är, i avsaknad av externa fel.
* **EnableMoveReplicaFaults**: aktiverar eller inaktiverar de fel som orsakar flyttningen av primära eller sekundära repliker. Dessa fel är inaktiverade som standard.
* **WaitTimeBetweenIterations**: hur lång tid som ska förflyta mellan iterationer, d.v.s. efter en runda av fel och motsvarande verifiering.

### <a name="how-to-run-the-chaos-test"></a>Så här kör du kaos-testet
C#-exempel

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunChaosTestScenarioAsync(clusterConnection).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunChaosTestScenarioAsync(string clusterConnection)
    {
        TimeSpan maxClusterStabilizationTimeout = TimeSpan.FromSeconds(180);
        uint maxConcurrentFaults = 3;
        bool enableMoveReplicaFaults = true;

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        ChaosTestScenarioParameters scenarioParameters = new ChaosTestScenarioParameters(
          maxClusterStabilizationTimeout,
          maxConcurrentFaults,
          enableMoveReplicaFaults,
          timeToRun);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        ChaosTestScenario chaosScenario = new ChaosTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```

PowerShell

Service Fabric PowerShell-modulen innehåller två sätt att starta ett kaos-scenario. `Invoke-ServiceFabricChaosTestScenario` är klientbaserade, och om klient datorn stängs mitt genom testet, kommer inga ytterligare fel att införas. Det finns också en uppsättning kommandon som är avsedda att hålla testet igång i händelse av att datorn stängs av. `Start-ServiceFabricChaos` använder en tillstånds känslig och tillförlitlig system tjänst med namnet FaultAnalysisService, vilket säkerställer att fel fortsätter att införas tills TimeToRun är igång. `Stop-ServiceFabricChaos` kan användas för att stoppa scenariot manuellt och `Get-ServiceFabricChaosReport` Hämta en rapport. Mer information finns i [Azure Service Fabric PowerShell-referensen](/powershell/module/servicefabric/) och [inducing kontrollerade kaos i Service Fabric-kluster](service-fabric-controlled-chaos.md).

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## <a name="failover-test"></a>Redundanstest
Test scenariot för redundans är en version av kaos som är riktad mot en speciell tjänstmall. Den testar effekten av redundans på en speciell tjänstmall samtidigt som andra tjänster inte påverkas. När den har kon figurer ATS med information om mål partitionen och andra parametrar, körs den som ett verktyg på klient sidan som använder antingen C# API: er eller PowerShell för att generera fel för en tjänstmall. Scenariot upprepas genom en sekvens av simulerade fel och tjänst verifiering medan affärs logiken körs på sidan för att tillhandahålla en arbets belastning. Ett fel i tjänst verifieringen indikerar ett problem som kräver ytterligare undersökning.

### <a name="faults-simulated-in-the-failover-test"></a>Fel simulerade i redundanstestning
* Starta om ett distribuerat kod paket där partitionen finns
* Ta bort en primär/sekundär replik eller tillstånds lös instans
* Starta om en primär sekundär replik (om en beständig tjänst)
* Flytta en primär replik
* Flytta en sekundär replik
* Starta om partitionen

Redundansväxlingen inducerar ett valt fel och kör sedan verifieringen av tjänsten för att säkerställa dess stabilitet. Testet av redundansen inducerar bara ett fel i taget, i stället för möjliga flera fel i kaos-testet. Testet Miss lyckas om tjänstepartitionen inte stabiliseras inom den konfigurerade tids gränsen efter varje fel. Testet inducerar endast säkra fel. Det innebär att det inte uppstår något kvorum eller data förlust vid brist på externa fel.

### <a name="important-configuration-options"></a>Viktiga konfigurations alternativ
* **PartitionSelector**: Selector-objektet som anger partitionen som måste vara mål.
* **TimeToRun**: den totala tid som testet ska köras innan det slutförs.
* **MaxServiceStabilizationTimeout**: maximal vänte tid för klustret att bli felfritt innan testet avbryts. De kontroller som utförs är om service hälsan är OK, mål replik uppsättningens storlek uppnås för alla partitioner, och det finns inga inbyggda repliker.
* **WaitTimeBetweenFaults**: vänte tiden mellan varje fel och validerings cykel.

### <a name="how-to-run-the-failover-test"></a>Så här kör du redundanstestning
**C#**

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunFailoverTestScenarioAsync(clusterConnection, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunFailoverTestScenarioAsync(string clusterConnection, Uri serviceName)
    {
        TimeSpan maxServiceStabilizationTimeout = TimeSpan.FromSeconds(180);
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        FailoverTestScenarioParameters scenarioParameters = new FailoverTestScenarioParameters(
          randomPartitionSelector,
          timeToRun,
          maxServiceStabilizationTimeout);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        FailoverTestScenario failoverScenario = new FailoverTestScenario(fabricClient, scenarioParameters);

        try
        {
            await failoverScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```


**PowerShell**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```
