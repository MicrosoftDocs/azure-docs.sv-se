---
title: Simulera fel i Azure Service Fabric-appar
description: Lär dig mer om hur du kan förstärka dina Azure Service Fabric-tjänster mot ett korrekt och haveri fel.
ms.topic: conceptual
ms.date: 06/15/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: c714ae30c64ea073cbac521eac5e15a8d968b7ea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91531318"
---
# <a name="simulate-failures-during-service-workloads"></a>Simulera problem under tjänst arbets belastningar
Med testnings scenarier i Azure Service Fabric kan utvecklare inte bekymra sig om att hantera enskilda fel. Det finns dock scenarier där det kan krävas en uttrycklig överta klient arbets belastning och-haverier. Genom att överlåta klientens arbets belastning och fel ser du till att tjänsten faktiskt utför en åtgärd när fel inträffar. På grund av den kontroll nivå som testare tillhandahåller kan detta vara en exakt punkt för arbets belastnings körningen. Denna induktion av fel i olika tillstånd i programmet kan hitta buggar och förbättra kvaliteten.

## <a name="sample-custom-scenario"></a>Exempel på anpassat scenario
Det här testet visar ett scenario som överlåter affärs arbets belastningen med ett [korrekt och haveri avbrott](service-fabric-testability-actions.md#graceful-vs-ungraceful-fault-actions). Felen bör induceras i mitten av tjänst åtgärder eller beräkning för bästa resultat.

Låt oss gå igenom ett exempel på en tjänst som visar fyra arbets belastningar: A, B, C och D. Var och en motsvarar en uppsättning arbets flöden och kan vara beräkning, lagring eller en blandning. För enkelhetens skull kommer vi att sammansluta arbets belastningarna i vårt exempel. De olika felen som körs i det här exemplet är:

* RestartNode: ett fel som inte går att simulera en omstart av datorn.
* RestartDeployedCodePackage: ett fel som inte går att simulera tjänstens värd process kraschar.
* RemoveReplica: ett fel uppstår för att simulera borttagning av repliker.
* En moveprimary: fel vid simulering av repliker som utlöses av Service Fabric belastningsutjämnare.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        // Replace these strings with the actual version for your cluster and application.
        string clusterConnection = "localhost:19000";
        Uri applicationName = new Uri("fabric:/samples/PersistentToDoListApp");
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Workload Test...");
        try
        {
            RunTestAsync(clusterConnection, applicationName, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Workload Test failed: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Workload Test completed successfully.");
        return 0;
    }

    public enum ServiceWorkloads
    {
        A,
        B,
        C,
        D
    }

    public enum ServiceFabricFaults
    {
        RestartNode,
        RestartCodePackage,
        RemoveReplica,
        MovePrimary,
    }

    public static async Task RunTestAsync(string clusterConnection, Uri applicationName, Uri serviceName)
    {
        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);
        // Maximum time to wait for a service to stabilize.
        TimeSpan maxServiceStabilizationTime = TimeSpan.FromSeconds(120);

        // How many loops of faults you want to execute.
        uint testLoopCount = 20;
        Random random = new Random();

        for (var i = 0; i < testLoopCount; ++i)
        {
            var workload = SelectRandomValue<ServiceWorkloads>(random);
            // Start the workload.
            var workloadTask = RunWorkloadAsync(workload);

            // While the task is running, induce faults into the service. They can be ungraceful faults like
            // RestartNode and RestartDeployedCodePackage or graceful faults like RemoveReplica or MovePrimary.
            var fault = SelectRandomValue<ServiceFabricFaults>(random);

            // Create a replica selector, which will select a primary replica from the given service to test.
            var replicaSelector = ReplicaSelector.PrimaryOf(PartitionSelector.RandomOf(serviceName));
            // Run the selected random fault.
            await RunFaultAsync(applicationName, fault, replicaSelector, fabricClient);
            // Validate the health and stability of the service.
            await fabricClient.TestManager.ValidateServiceAsync(serviceName, maxServiceStabilizationTime);

            // Wait for the workload to finish successfully.
            await workloadTask;
        }
    }

    private static async Task RunFaultAsync(Uri applicationName, ServiceFabricFaults fault, ReplicaSelector selector, FabricClient client)
    {
        switch (fault)
        {
            case ServiceFabricFaults.RestartNode:
                await client.FaultManager.RestartNodeAsync(selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RestartCodePackage:
                await client.FaultManager.RestartDeployedCodePackageAsync(applicationName, selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RemoveReplica:
                await client.FaultManager.RemoveReplicaAsync(selector, CompletionMode.Verify, false);
                break;
            case ServiceFabricFaults.MovePrimary:
                await client.FaultManager.MovePrimaryAsync(selector.PartitionSelector);
                break;
        }
    }

    private static Task RunWorkloadAsync(ServiceWorkloads workload)
    {
        throw new NotImplementedException();
        // This is where you trigger and complete your service workload.
        // Note that the faults induced while your service workload is running will
        // fault the primary service. Hence, you will need to reconnect to complete or check
        // the status of the workload.
    }

    private static T SelectRandomValue<T>(Random random)
    {
        Array values = Enum.GetValues(typeof(T));
        T workload = (T)values.GetValue(random.Next(values.Length));
        return workload;
    }
}
```
