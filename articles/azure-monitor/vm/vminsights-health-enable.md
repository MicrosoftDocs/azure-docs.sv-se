---
title: Aktivera Azure Monitor for VMs gäst hälsa (för hands version)
description: Beskriver hur du aktiverar Azure Monitor for VMs gäst hälsa i din prenumeration och hur du kan publicera virtuella datorer.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/16/2020
ms.custom: references_regions
ms.openlocfilehash: 5a65a986e95f333b6179c71a46edc69ca61acdea
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100623500"
---
# <a name="enable-azure-monitor-for-vms-guest-health-preview"></a>Aktivera Azure Monitor for VMs gäst hälsa (för hands version)
Azure Monitor for VMs gäst hälsa kan du se hälso tillståndet för en virtuell dator så som den definieras av en uppsättning prestanda mätningar som samplas med jämna mellanrum. I den här artikeln beskrivs hur du aktiverar den här funktionen i din prenumeration och hur du aktiverar gäst övervakning för varje virtuell dator.

## <a name="current-limitations"></a>Aktuella begränsningar
Azure Monitor for VMs gäst hälsa har följande begränsningar i offentlig för hands version:

- Endast virtuella Azure-datorer stöds för närvarande. Azure Arc för servrar stöds inte för närvarande.


## <a name="supported-operating-systems"></a>Operativsystem som stöds
Den virtuella datorn måste köra något av följande operativ system: 

  - Ubuntu 16,04 LTS, Ubuntu 18,04 LTS
  - Windows Server 2012 eller senare

## <a name="supported-regions"></a>Regioner som stöds

Den virtuella datorn måste finnas i någon av följande regioner:

- Australien, centrala
- Australien, östra
- Australien, sydöstra
- Indien, centrala
- Central US
- Asien, östra
- East US
- USA, östra 2
- USA, östra 2 EUAP
- Tyskland, västra centrala
- Japan, östra
- USA, norra centrala
- Europa, norra
- USA, södra centrala
- Sydostasien
- Storbritannien, södra
- USA, västra centrala
- Europa, västra
- USA, västra
- USA, västra 2


Log Analytics arbets ytan måste finnas i någon av följande regioner:

- Central US
- East US
- USA, östra 2
- USA, östra 2 EUAP
- Norra Europa
- Sydostasien
- Storbritannien, södra
- Regionen Europa, västra
- USA, västra 2

## <a name="prerequisites"></a>Förutsättningar

- Den virtuella datorn måste registreras Azure Monitor for VMs.
- Användare som kör onboarding-stegen måste ha en lägsta deltagar nivå åtkomst till prenumerationen där den virtuella datorn och data insamlings regeln finns.
- Nödvändiga Azure-resurs leverantörer måste registreras enligt beskrivningen i följande avsnitt.

## <a name="register-required-azure-resource-providers"></a>Registrera nödvändiga Azure-resurs leverantörer
Följande Azure-resurs leverantörer registreras för din prenumeration för att möjliggöra Azure Monitor for VMs gäst hälsa. 

- Microsoft. WorkloadMonitor
- Microsoft. Insights

Du kan använda någon av de tillgängliga metoderna för att registrera en resurs leverantör enligt beskrivningen i [Azure Resource providers och-typer](../../azure-resource-manager/management/resource-providers-and-types.md). Du kan också använda följande exempel kommando med armclient, Postman eller någon annan metod för att göra autentiserade anrop till Azure Resource Manager:

```
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.WorkloadMonitor/register?api-version=2019-10-01
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.Insights/register?api-version=2019-10-01
```


## <a name="enable-a-virtual-machine-using-the-azure-portal"></a>Aktivera en virtuell dator med hjälp av Azure Portal
När du aktiverar gästhälsa för en virtuell dator i Azure Portal utförs all konfiguration som krävs åt dig. Detta omfattar att skapa en regel för att kräva data insamling, installera gäst hälso tillägget på den virtuella datorn och skapa en Association med data insamlings regeln.

Från vyn **Kom igång** i Azure Monitor for VMS klickar du på länken bredvid uppgraderings meddelandet för en virtuell dator och klickar sedan på knappen **Uppgradera** . Du kan också välja flera virtuella datorer för att uppgradera dem tillsammans.

![Aktivera hälso funktion på virtuell dator](media/vminsights-health-enable/enable-agent.png)


## <a name="enable-a-virtual-machine-using-resource-manager-template"></a>Aktivera en virtuell dator med hjälp av en Resource Manager-mall
Det krävs tre steg för att aktivera virtuella datorer med hjälp av Azure Resource Manager.

- Skapa data insamlings regel.
- Installera gäst hälso tillägget på varje virtuell dator
- Skapa en association mellan den virtuella datorn och datainsamlingsregeln.

### <a name="create-data-collection-rule-dcr"></a>Skapa data insamlings regel (DCR)

> [!NOTE]
> Om du aktiverar en virtuell dator med hjälp av Azure Portal skapas den data insamlings regel som beskrivs här. I så fall behöver du inte utföra det här steget.

Konfiguration av övervakare i Azure Monitor for VMs gäst hälsa lagras i [data insamlings regler (DCR)](../agents/data-collection-rule-overview.md). Varje virtuell dator med gäst hälso tillägget behöver en Association med den här regeln.

> [!NOTE]
> Du kan skapa ytterligare data insamlings regler för att ändra standard konfigurationen för övervakare enligt beskrivningen i [Konfigurera övervakning i Azure Monitor for VMS gäst hälsa (för hands version)](vminsights-health-configure.md).

Mallen kräver värden för följande parametrar:

- **defaultHealthDataCollectionRuleName**: Behåll standard namnet som definierats i mallen.
- **destinationWorkspaceResourceId**: resurs-ID för den Log Analytics arbets ytan som används för data insamling av virtuell dator.
- **dataCollectionRuleLocation**: regionen för data insamlings regeln. Detta måste matcha regionen för Log Analytics-arbetsytan.


Distribuera mallen med valfri [distributions metod för Resource Manager-mallar](../../azure-resource-manager/templates/deploy-powershell.md). Följande kommandon distribuerar mall-och parameter filen med PowerShell eller Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDataCollectionRule -ResourceGroupName my-resource-group -TemplateFile Health.DataCollectionRule.template.json -TemplateParameterFile Health.DataCollectionRule.template.parameters.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create --name GuestHealthDataCollectionRule --resource-group my-resource-group --template-file Health.DataCollectionRule.template.json --parameters Health.DataCollectionRule.template.parameters.json
```

---

Den data insamlings regel som definierats i Resource Manager-mallen nedan aktiverar alla Övervakare för de virtuella datorerna med gäst hälso tillägget. Det måste innehålla data källor för varje prestanda räknare som används av övervakarna.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "defaultHealthDataCollectionRuleName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule to create."
      },
      "defaultValue": "Microsoft-VMInsights-Health"
    },
    "destinationWorkspaceResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the Azure resource ID of the Log Analytics workspace to use to store virtual machine health data."
      }
    },
    "dataCollectionRuleLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code in which the data collection rule should be deployed. Examples: eastus, westeurope, etc"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/dataCollectionRules",
      "name": "[parameters('defaultHealthDataCollectionRuleName')]",
      "location": "[parameters('dataCollectionRuleLocation')]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Data collection rule for VM Insights health.",
        "dataSources": {
          "performanceCounters": [
              {
                  "name": "VMHealthPerfCounters",
                  "streams": [ "Microsoft-Perf" ],
                  "scheduledTransferPeriod": "PT1M",
                  "samplingFrequencyInSeconds": 60,
                  "counterSpecifiers": [
                      "\\LogicalDisk(*)\\% Free Space",
                      "\\Memory\\Available Bytes",
                      "\\Processor(_Total)\\% Processor Time"
                  ]
              }
          ],
          "extensions": [
            {
              "name": "Microsoft-VMInsights-Health",
              "streams": [
                "Microsoft-HealthStateChange"
              ],
              "extensionName": "HealthExtension",
              "extensionSettings": {
                "schemaVersion": "1.0",
                "contentVersion": "",
                "healthRuleOverrides": [
                  {
                    "scopes": [ "*" ],
                    "monitors": ["root"],
                    "alertConfiguration": {
                      "isEnabled": true
                    }
                  }
                ]
              },
              "inputDataSources": [
                  "VMHealthPerfCounters"
              ]

            }
          ]
        },
        "destinations": {
          "logAnalytics": [
            {
              "workspaceResourceId": "[parameters('destinationWorkspaceResourceId')]",
              "name": "Microsoft-HealthStateChange-Dest"
            }
          ]
        },                  
        "dataFlows": [
          {
            "streams": [
              "Microsoft-HealthStateChange"
            ],
            "destinations": [
              "Microsoft-HealthStateChange-Dest"
            ]
          }
        ]
      }
    }
  ]
}
```

### <a name="sample-parameter-file"></a>Exempel parameter fil

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "destinationWorkspaceResourceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
      },
      "dataCollectionRuleLocation": {
        "value": "eastus"
      }
  }
}
```



### <a name="install-guest-health-extension-and-associate-with-data-collection-rule"></a>Installera gäst hälso tillägg och associera med data insamlings regel
Använd följande Resource Manager-mall för att aktivera en virtuell dator för gäst hälsa. Detta installerar gäst hälso tillägget och skapar associationen med data insamlings regeln. Du kan distribuera den här mallen med valfri [distributions metod för Resource Manager-mallar](../../azure-resource-manager/templates/deploy-powershell.md).


Använd exempelvis följande kommandon för att distribuera mallen mall och parametrar till en resurs grupp med hjälp av PowerShell eller Azure CLI.


# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create --name GuestHealthDeployment --resource-group my-resource-group --template-file Health.VirtualMachine.template.json --parameters Health.VirtualMachine.template.parameters.json
```

---

### <a name="template-file"></a>Mallfil

``` json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "virtualMachineName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the virtual machine."
      }
    },
    "virtualMachineLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code of the virtual machine region (location). Examples: eastus, westeurope, etc"
      }
    },
    "virtualMachineOsType": {
      "type": "string",
      "metadata": {
        "description": "Specifies operating system type of the target virtual machine."
      },
      "allowedValues": ["windows", "linux"]
    },
    "dataCollectionRuleAssociationName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule association to create."
      },
      "defaultValue": "VM-Health-Dcr-Association"
    },
    "healthDataCollectionRuleResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies resource id of Azure Monitor Data Collection Rule for virtual machine health data."
      }
    }
  },
  "variables": {
    "healthExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthWindowsAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthLinuxAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      }
    },
    "azureMonitorAgentExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorWindowsAgent", 
        "typeHandlerVersion": "1.0", 
        "autoUpgradeMinorVersion": false 
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorLinuxAgent", 
        "typeHandlerVersion": "1.5", 
        "autoUpgradeMinorVersion": false 
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('virtualMachineName')]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2017-03-30",
      "identity": {
        "type": "SystemAssigned"
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
      "name": "[concat(parameters('virtualMachineName'), '/Microsoft.Insights/', parameters('dataCollectionRuleAssociationName'))]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Association of data collection rule for VM Insights Health.",
        "dataCollectionRuleId": "[parameters('healthDataCollectionRuleResourceId')]"
      }
    },
    { 
      "type": "Microsoft.Compute/virtualMachines/extensions", 
      "apiVersion": "2019-12-01", 
      "name": "[concat(parameters('virtualMachineName'), '/', variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ], 
      "properties": "[variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')]]"
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('virtualMachineName'), '/', variables('healthExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2018-06-01",
      "dependsOn": [ 
        "[resourceId('Microsoft.Compute/virtualMachines/extensions', parameters('virtualMachineName'), variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ],    
      "properties": "[variables('healthExtensionProperties')[parameters('virtualMachineOsType')]]"
    }               
  ]
}
```

### <a name="sample-parameter-file"></a>Exempel parameter fil

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualMachineName": {
        "value": "myvm"
      },
      "virtualMachineLocation": {
        "value": "eastus"
      },
      "virtualMachineOsType": {
        "value": "linux"
      },
      "healthDataCollectionRuleResourceId": {
        "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/dataCollectionRules/Microsoft-VMInsights-Health"
      }
  }
}
```

## <a name="next-steps"></a>Nästa steg

- [Anpassa Övervakare som Aktiver ATS av Azure Monitor for VMs](vminsights-health-configure.md)