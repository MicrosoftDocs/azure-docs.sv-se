---
title: Prestanda övervakning med Windows Azure-diagnostik
description: Använd Windows Azure-diagnostik för att samla in prestanda räknare för Azure Service Fabric-kluster.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: c2114f5392da788bb440589e69e704a148731e02
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86258614"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Prestanda övervakning med Windows Azure-diagnostik-tillägget

Det här dokumentet beskriver steg som krävs för att konfigurera insamling av prestanda räknare via Windows Azure-diagnostik-tillägget (WAD) för Windows-kluster. För Linux-kluster ställer du in [Log Analytics agenten](service-fabric-diagnostics-oms-agent.md) för att samla in prestanda räknare för noderna. 

 > [!NOTE]
> WAD-tillägget bör distribueras i klustret för att de här stegen ska fungera. Om den inte har kon figurer ATS ska du gå över till [händelse agg regering och insamling med Windows Azure-diagnostik](service-fabric-diagnostics-event-aggregation-wad.md).  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="collect-performance-counters-via-the-wadcfg"></a>Samla in prestanda räknare via WadCfg

Om du vill samla in prestanda räknare via WAD måste du ändra konfigurationen på lämpligt sätt i klustrets Resource Manager-mall. Följ dessa steg om du vill lägga till en prestanda räknare som du vill samla in i mallen och köra en resurs hanterings resurs uppgradering.

1. Hitta WAD-konfigurationen i klustrets mall – hitta `WadCfg` . Du kommer att lägga till prestanda räknare som ska samlas in under `DiagnosticMonitorConfiguration` .

2. Konfigurera konfigurationen för att samla in prestanda räknare genom att lägga till följande avsnitt i `DiagnosticMonitorConfiguration` . 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    `scheduledTransferPeriod`Definierar hur ofta värdena för de räknare som samlas in överförs till din Azure Storage-tabell och till alla konfigurerade mottagare. 

3. Lägg till de prestanda räknare som du vill samla in till `PerformanceCounterConfiguration` som har deklarerats i föregående steg. Varje räknare som du vill samla in definieras med en `counterSpecifier` ,, `sampleRate` , `unit` `annotation` och relevant `sinks` .

Här är ett exempel på en konfiguration med räknaren för den *totala processor tiden* (den tid som processorn användes för bearbetning) och *Service Fabric aktör metod anrop per sekund*, en av de Service Fabric anpassade prestanda räknarna. Se [tillförlitliga aktörs prestanda räknare](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) och [tillförlitliga tjänst prestanda räknare](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) för en fullständig lista över Service Fabric anpassade prestanda räknare.

 ```json
 "WadCfg": {
         "DiagnosticMonitorConfiguration": {
           "overallQuotaInMB": "50000",
           "EtwProviders": {
             "EtwEventSourceProviderConfiguration": [
               {
                 "provider": "Microsoft-ServiceFabric-Actors",
                 "scheduledTransferKeywordFilter": "1",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableActorEventTable"
                 }
               },
               {
                 "provider": "Microsoft-ServiceFabric-Services",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableServiceEventTable"
                 }
               }
             ],
             "EtwManifestProviderConfiguration": [
               {
                 "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                 "scheduledTransferLogLevelFilter": "Information",
                 "scheduledTransferKeywordFilter": "4611686018427387904",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricSystemEventTable"
                 }
               }
             ]
           },
           "PerformanceCounters": {
                 "scheduledTransferPeriod": "PT1M",
                 "PerformanceCounterConfiguration": [
                     {
                         "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                         "sampleRate": "PT1M",
                         "unit": "Percent",
                         "annotation": [
                         ],
                         "sinks": ""
                     },
                     {
                         "counterSpecifier": "\\Service Fabric Actor Method(*)\\Invocations/Sec",
                         "sampleRate": "PT1M",
                     }
                 ]
             }
         }
       },
  ```

 Samplings frekvensen för räknaren kan ändras efter behov. Formatet för det är `PT<time><unit>` , så om du vill att räknaren ska samlas in varje sekund ska du ange `"sampleRate": "PT15S"` .

 Du kan också använda variabler i ARM-mallen för att samla in en matris med prestanda räknare, vilket kan vara praktiskt när du samlar in prestanda räknare per process. I exemplet nedan samlar vi in processor tid och skräp insamlings tid per process och sedan 2 prestanda räknare på noderna alla med hjälp av variabler. 

 ```json
"variables": {
  "copy": [
      {
        "name": "processorTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\Process([parameters('monitoredProcesses')[copyIndex('processorTimeCounters')]])\\% Processor Time",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('processorTimeCounters')],' Processor Time')]",
              "locale": "en-us"
            }
          ]
        }
      },
      {
        "name": "gcTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\.NET CLR Memory([parameters('monitoredProcesses')[copyIndex('gcTimeCounters')]])\\% Time in GC",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('gcTimeCounters')],' Time in GC')]",
              "locale": "en-us"
            }
          ]
        }
      }
    ],
    "machineCounters": [
      {
        "counterSpecifier": "\\Memory\\Available Bytes",
        "sampleRate": "PT1M",
        "unit": "KB",
        "sinks": "applicationInsights",
        "annotation": [
          {
            "displayName": "Memory Available Kb",
            "locale": "en-us"
          }
        ]
      },
      {
        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
        "sampleRate": "PT15S",
        "unit": "percent",
        "annotation": [
          {
            "displayName": "Memory usage",
            "locale": "en-us"
          }
        ]
      }
    ]
  }
....
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": "50000",
      "Metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', variables('vmNodeTypeApp2Name'))]"
      },
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": "[concat(variables ('processorTimeCounters'), variables('gcTimeCounters'),  variables('machineCounters'))]"
      },
....
```

1. När du har lagt till lämpliga prestanda räknare som behöver samlas in måste du uppgradera kluster resursen så att dessa ändringar visas i det kluster som körs. Spara din ändrade `template.json` och öppna PowerShell. Du kan uppgradera klustret med hjälp av `New-AzResourceGroupDeployment` . Anropet kräver namnet på resurs gruppen, den uppdaterade mallfilen och parameter filen och begär Resource Manager för att göra lämpliga ändringar i de resurser som du har uppdaterat. När du har loggat in på ditt konto och har rätt prenumeration använder du följande kommando för att köra uppgraderingen:

    ```sh
    New-AzResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. När uppgraderingen är klar (tar mellan 15-45 minuter beroende på om det är den första distributionen och storleken på resurs gruppen) ska WAD samla in prestanda räknarna och skicka dem till tabellen med namnet WADPerformanceCountersTable i det lagrings konto som är associerat med klustret. Se dina prestanda räknare i Application Insights genom [att lägga till AI-Sink i Resource Manager-mallen](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Nästa steg
* Samla in fler prestanda räknare för klustret. Se [prestanda mått](service-fabric-diagnostics-event-generation-perf.md) för en lista med räknare som du bör samla in.
* [Använd övervakning och diagnostik med en virtuell Windows-dator och Azure Resource Manager mallar](../virtual-machines/extensions/diagnostics-template.md) för att göra ytterligare ändringar i `WadCfg` , inklusive att konfigurera ytterligare lagrings konton för att skicka diagnostikdata till.
* Besök [WadCfg Builder](https://azure.github.io/azure-diagnostics-tools/config-builder/) för att skapa en mall från grunden och kontrol lera att syntaxen är korrekt. ( https://azure.github.io/azure-diagnostics-tools/config-builder/) om du vill skapa en mall från grunden och kontrol lera att syntaxen är korrekt.
