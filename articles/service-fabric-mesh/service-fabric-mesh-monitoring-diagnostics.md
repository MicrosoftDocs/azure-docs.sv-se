---
title: Övervakning och diagnostik i Azure Service Fabric Mesh appar
description: Lär dig mer om övervakning och diagnostisering av program i Service Fabric Mesh på Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: d8859293b4853cbfa8c3b3dd0e7d1bfe4f75fc40
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766177"
---
# <a name="monitoring-and-diagnostics"></a>Övervakning och diagnostik

> [!IMPORTANT]
> Förhandsversionen av Azure Service Fabric Mesh har dragits tillbaka. Nya distributioner tillåts inte längre via Service Fabric Mesh API. Stödet för befintliga distributioner fortsätter till och med 28 april 2021.
> 
> Mer information finns i [Azure Service Fabric Mesh förhandsversionen.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

Azure Service Fabric Mesh är en fullständigt hanterad tjänst som gör att utvecklare kan distribuera mikrotjänstprogram utan att hantera virtuella datorer, lagring eller nätverk. Övervakning och diagnostik för Service Fabric Mesh är indelade i tre huvudtyper av diagnostikdata:

- Programloggar – dessa definieras som loggar från dina containerbaserade program, baserat på hur du har instrumenterat programmet (t.ex. Docker-loggar)
- Plattformshändelser – händelser från Mesh-plattformen som är relevanta för din containeråtgärd, för närvarande inklusive containeraktivering, inaktivering och avslutning.
- Containermått – resursutnyttjande och prestandamått för dina containrar (dockerstatistik)

I den här artikeln beskrivs övervaknings- och diagnostikalternativen för den senaste tillgängliga förhandsversionen.

## <a name="application-logs"></a>Programloggar

Du kan visa dockerloggarna från dina distribuerade containrar per container. I den Service Fabric Mesh programmodellen är varje container ett kodpaket i ditt program. Om du vill se de associerade loggarna med ett kodpaket använder du följande kommando:

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Du kan använda kommandot "az mesh service-replica" för att hämta repliknamnet. Repliknamn ökar heltal från 0.

Så här ser det ut för att se loggarna från votingWeb.Code-containern från röstningsprogrammet:

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Containermått 

Mesh-miljön exponerar ett fåtal mått som anger hur dina containrar presterar. Följande mått är tillgängliga via azure-Azure Portal och Azure Monitor CLI:

| Metric | Beskrivning | Enheter|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu i procent | % |
| MemoryUtilization | ActualMem/AllocatedMem i procent | % |
| AllocatedCpu | Processor som allokerats enligt Azure Resource Manager mall | Cores |
| AllocatedMemory | Allokerat minne enligt Azure Resource Manager mall | MB |
| ActualCpu | CPU-användning | Cores |
| ActualMemory | Minnesanvändning | MB |
| ContainerStatus | 0 – Ogiltig: Containerstatusen är okänd <br> 1 – Väntar: Containern har schemalagts att starta <br> 2 – Startar: Containern startas <br> 3 – Startad: Containern har startats <br> 4 – Stoppa: Containern stoppas <br> 5 – Stoppad: Containern har stoppats | Ej tillämpligt |
| ApplicationStatus | 0 – Okänd: Statusen kan inte hämtas <br> 1 – Klart: Programmet körs <br> 2 – Uppgradering: En uppgradering pågår <br> 3 – Skapa: Programmet skapas <br> 4 – Ta bort: Programmet håller på att tas bort <br> 5 – Misslyckades: Programmet kunde inte distribueras | Ej tillämpligt |
| ServiceStatus | 0 – Ogiltig: Tjänsten har för närvarande inte hälsostatus <br> 1 – Ok: Tjänsten är felfri  <br> 2 – Varning: Det kan vara något fel som kräver undersökning <br> 3 – Fel: Det är något fel som behöver undersökas <br> 4 – Okänd: Statusen kan inte hämtas | Ej tillämpligt |
| ServiceReplicaStatus | 0 – Ogiltig: Repliken har för närvarande inte hälsostatus <br> 1 – Ok: Tjänsten är felfri  <br> 2 – Varning: Det kan vara något fel som kräver undersökning <br> 3 – Fel: Det är något fel som behöver undersökas <br> 4 – Okänd: Statusen kan inte hämtas | Ej tillämpligt | 
| RestartCount | Antal omstarter av containrar | Ej tillämpligt |

> [!NOTE]
> Värdena ServiceStatus och ServiceReplicaStatus är samma som [HealthState](/dotnet/api/system.fabric.health.healthstate) i Service Fabric.

Varje mått är tillgängligt för olika dimensioner så att du kan se aggregeringar på olika nivåer. Den aktuella listan med dimensioner är följande:

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> CodePackageName-dimensionen är inte tillgänglig för Linux-program. 

Varje dimension motsvarar olika komponenter i [Service Fabric programmodellen](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Azure Monitor CLI

En fullständig lista över kommandon finns i [cli Azure Monitor dokumenten,](/cli/azure/monitor/metrics#az_monitor_metrics_list) men vi har tagit med några användbara exempel nedan 

I varje exempel följer resurs-ID:t det här mönstret

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* CPU-användning av containrarna i ett program

```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Minnesanvändning för varje Service Replica
```azurecli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Omstarter för varje container i ett 1-timmarsfönster 
```azurecli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Genomsnittlig processoranvändning mellan tjänster med namnet "VotingWeb" i ett 1-timmarsfönster
```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Måttutforskare

Metrics Explorer är ett blad i portalen där du kan visualisera alla mått för ditt Mesh-program. Det här bladet är tillgängligt på programsidan i portalen och Azure Monitor-bladet. Det senare kan du använda för att visa mått för alla dina Azure-resurser som stöder Azure Monitor. 

![Metrics Explorer](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Nästa steg
* Mer information om Service Fabric Mesh finns i [översikten över Service Fabric Mesh](service-fabric-mesh-overview.md).
* Mer information om kommandon Azure Monitor mått finns i [CLI-Azure Monitor.](/cli/azure/monitor/metrics#az_monitor_metrics_list)
