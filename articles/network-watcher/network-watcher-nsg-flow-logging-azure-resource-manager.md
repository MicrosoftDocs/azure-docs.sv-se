---
title: Network Watcher – skapa NSG flödes loggar med en Azure Resource Manager-mall
description: Använd en Azure Resource Manager mall och PowerShell för att enkelt konfigurera NSG Flow-loggar.
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.custom: fasttrack-edit
ms.openlocfilehash: 2cae673dd319e55670357ae468d21ff63e4f9c72
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669448"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>Konfigurera NSG flödes loggar från en Azure Resource Manager mall

> [!div class="op_single_selector"]
> - [Azure-portalen](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST-API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) är Azures inbyggda och kraftfulla sätt att hantera [infrastrukturen som kod](/azure/devops/learn/what-is-infrastructure-as-code).

Den här artikeln visar hur du aktiverar [NSG Flow-loggar](./network-watcher-nsg-flow-logging-overview.md) via programmering med hjälp av en Azure Resource Manager-mall och Azure PowerShell. Vi börjar med att tillhandahålla en översikt över egenskaperna för NSG flödes logg objekt, följt av några exempel på mallar. Sedan distribuerar vi mallen med en lokal PowerShell-instans.


## <a name="nsg-flow-logs-object"></a>NSG flödes loggar objekt

Objektet NSG Flow-loggar med alla parametrar visas nedan.
För en fullständig översikt över egenskaperna kan du läsa [mallen för NSG Flow-referenser](/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters).

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
         "enabled": "boolean",
         "workspaceResourceId": "string",
          "trafficAnalyticsInterval": "integer"
        },
        "retentionPolicy": {
           "days": "integer",
           "enabled": "boolean"
         },
        "format": {
           "type": "string",
           "version": "integer"
         }
      }
    }
  }
```
Om du vill skapa en resurs för Microsoft. Network/networkWatchers/flowLogs lägger du till ovanstående JSON i avsnittet resurser i mallen.


## <a name="creating-your-template"></a>Skapa din mall

Om du använder Azure Resource Manager mallar för första gången kan du läsa mer om dem med hjälp av länkarna nedan.

* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template-or-bicep-file)
* [Självstudie: skapa och distribuera din första Azure Resource Manager-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell)


Nedan visas två exempel på kompletta mallar för att ställa in NSG Flow-loggar.

**Exempel 1**: den enklaste versionen av ovanstående med minsta parametrar som överförs. Mallen nedan aktiverar NSG flödes loggar på en mål-NSG och lagrar dem i ett angivet lagrings konto.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {},
      "retentionPolicy": {},
      "format": {}
    }

  }
  ]
}
```

> [!NOTE]
> * Resurs namnet har formatet "överordnad Resource_Child resurs". Här är den överordnade resursen den regionala Network Watcher-instansen (format: NetworkWatcher_RegionName. Exempel: NetworkWatcher_centraluseuap)
> * targetResourceId är resurs-ID för mål NSG
> * storageId är resurs-ID för mål lagrings kontot

**Exempel 2**: följande mallar som aktiverar NSG Flow-loggar (version 2) med kvarhållning i 5 dagar. Aktivera Trafikanalys med ett bearbetnings intervall på 10 minuter.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
        "enabled": true,
        "flowAnalyticsConfiguration": {
          "networkWatcherFlowAnalyticsConfiguration": {
            "enabled": true,
            "workspaceResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/defaultresourcegroup-wcus/providers/Microsoft.OperationalInsights/workspaces/1c4f42e5-3a02-4146-ac9b-3051d8501db0",
            "trafficAnalyticsInterval": 10
          }
        },
        "retentionPolicy": {
          "days": 5,
          "enabled": true
        },
        "format": {
          "type": "JSON",
          "version": 2
        }
      }
    }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Distribuera din Azure Resource Manager-mall

I den här självstudien förutsätter vi att du har en befintlig resurs grupp och en NSG. du kan aktivera flödes inloggning.
Du kan spara alla ovanstående exempel mallar lokalt som `azuredeploy.json` . Uppdatera egenskapsvärdena så att de pekar på giltiga resurser i din prenumeration.

Distribuera mallen genom att köra följande kommando i PowerShell.
```azurepowershell
$context = Get-AzSubscription -SubscriptionId <SubscriptionId>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Ovanstående kommandon distribuerar en resurs till resurs gruppen NetworkWatcherRG och inte den resurs grupp som innehåller NSG


## <a name="verifying-your-deployment"></a>Verifiera distributionen

Det finns ett par olika sätt att kontrol lera om distributionen har slutförts. PowerShell-konsolen ska Visa "ProvisioningState" som "lyckades". Dessutom kan du gå till [Portal sidan NSG Flow logs](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) för att bekräfta dina ändringar. Om det uppstod problem med distributionen kan du ta en titt på [Felsöka vanliga problem med Azure-distribution med Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="deleting-your-resource"></a>Tar bort resursen
Med Azure kan du ta bort resurser genom att slutföra distributions läget. Om du vill ta bort en flödes loggar resurs anger du en distribution i slutfört läge utan att inkludera den resurs som du vill ta bort. Läs mer om [hela distributions läget](../azure-resource-manager/templates/deployment-modes.md#complete-mode)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du visualiserar dina NSG-flödes data med:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Verktyg för öppen källkod](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure-Trafikanalys](./traffic-analytics.md)
