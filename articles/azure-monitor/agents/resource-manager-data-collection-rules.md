---
title: Exempel på Resource Manager-mallar för data insamlings regler
description: Exempel Azure Resource Manager mallar för att skapa associationer mellan data insamlings regler och virtuella datorer i Azure Monitor.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: f98e22ba28dcd05d164f40c301a2e43df66898de
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100627952"
---
# <a name="resource-manager-template-samples-for-data-collection-rules-in-azure-monitor"></a>Exempel på Resource Manager-mallar för data insamlings regler i Azure Monitor
Den här artikeln innehåller exempel [Azure Resource Manager mallar](../../azure-resource-manager/templates/template-syntax.md) för att distribuera och konfigurera [Log Analytics agent](../platform/log-analytics-agent.md) och [diagnostiskt tillägg](../platform/diagnostics-extension-overview.md) för virtuella datorer i Azure Monitor. Varje exempel innehåller en mallfil och en parameter fil med exempel värden som du kan använda för mallen.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-association-with-azure-vm"></a>Skapa en Association med virtuell Azure-dator

Följande exempel skapar en association mellan en virtuell Azure-dator och en data insamlings regel.

### <a name="template-file"></a>Mallfil

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('vmName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this virtual machine.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameter fil

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```

## <a name="create-association-with-azure-arc"></a>Skapa en Association med Azure Arc

Följande exempel skapar en koppling mellan en Azure Arc-aktiverad server och en data insamlings regel.

### <a name="template-file"></a>Mallfil

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.HybridCompute/machines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('machineName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this Arc server.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameter fil

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```


## <a name="next-steps"></a>Nästa steg

* [Hämta andra exempel mallar för Azure Monitor](../samples/resource-manager-samples.md).
* [Läs mer om Log Analytics-agenten](../platform/log-analytics-agent.md).
* [Läs mer om diagnostiskt tillägg](../platform/diagnostics-extension-overview.md).
