---
title: Exempel på Resource Manager-mallar för Azure Monitor
description: Distribuera och konfigurera Azure Monitor funktioner med Resource Manager-mallar
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: sample
ms.date: 05/18/2020
ms.openlocfilehash: ac2ed571359958feddd4d7952ea3b5f549688c77
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046983"
---
# <a name="resource-manager-template-samples-for-azure-monitor"></a>Exempel på Resource Manager-mallar för Azure Monitor

Azure Monitor kan distribueras och konfigureras i skala med [Azure Resource Manager-mall](../azure-resource-manager/templates/template-syntax.md). Följande artiklar innehåller exempel på mallar för olika Azure Monitor-funktioner. De här exemplen kan ändras för dina specifika krav och distribueras med valfri standard metod för distribution av Resource Manager-mallar. 

## <a name="deploying-the-sample-templates"></a>Distribuera exempel mallarna
De grundläggande stegen för att använda exemplen är:

1. Kopiera mallen och Spara som en JSON-fil.
2. Ändra parametrarna för din miljö och Spara som en JSON-fil.
4. Distribuera mallen med [valfri distributions metod för Resource Manager-mallar](../azure-resource-manager/templates/deploy-powershell.md). 

Använd exempelvis följande kommandon för att distribuera mallen mall och parametrar till en resurs grupp med hjälp av PowerShell eller Azure CLI.


```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

```azurecli
az login
az deployment group create \
    --name AzureMonitorDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file azure-monitor-deploy.json \
    --parameters azure-monitor-deploy.parameters.json
```

## <a name="list-of-sample-templates"></a>Lista över exempel på mallar

- [Agenter](agents/resource-manager-agent.md) – distribuera och konfigurera Log Analytics agent och diagnostiskt tillägg.
- Aviseringar
  - [Logg varnings regler](alerts/resource-manager-alerts-log.md) – aviseringar från logg frågor och Azure aktivitets logg.
  - [Mått varnings regler](alerts/resource-manager-alerts-metric.md) – aviseringar från mått som använder olika typer av logik.
- [Application Insights](app/resource-manager-app-resource.md)
- [Diagnostikinställningar](essentials/resource-manager-diagnostic-settings.md) – skapa diagnostiska inställningar för att vidarebefordra loggar och mått från olika resurs typer.
- [Logg frågor](logs/resource-manager-log-queries.md) – skapa sparade logg frågor på en Log Analytics-arbetsyta.
- [Log Analytics arbets yta](logs/resource-manager-workspace.md) – skapa Log Analytics arbets yta och konfigurera insamling av olika data källor från Log Analytics agenten.
- [Arbets böcker](visualize/resource-manager-workbooks.md) – skapa arbets böcker.
- [Behållar insikter](containers/resource-manager-container-insights.md) – publicera kluster till behållar insikter.
- [Azure Monitor for VMS](vm/resource-manager-vminsights.md) virtuella datorer som ska Azure Monitor for VMS.



## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Resource Manager-mallar](../azure-resource-manager/templates/overview.md)