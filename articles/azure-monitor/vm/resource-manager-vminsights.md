---
title: Exempel på Resource Manager-mallar för VM-insikter
description: Exempel Azure Resource Manager mallar för att distribuera och configureVM insikter.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/18/2020
ms.openlocfilehash: 2ee447b1e5f72ce916808a5832f999eea493994c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046847"
---
# <a name="resource-manager-template-samples-for-vm-insights"></a>Exempel på Resource Manager-mallar för VM-insikter
Den här artikeln innehåller exempel [Azure Resource Manager mallar](../../azure-resource-manager/templates/template-syntax.md) som möjliggör VM-insikter på virtuella datorer. Varje exempel innehåller en mallfil och en parameter fil med exempel värden som du kan använda för mallen.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="configure-workspace"></a>Konfigurera arbetsyta
I följande exempel aktive ras VM Insights för en Log Analytics-arbetsyta.


### <a name="template-file"></a>Mallfil

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "String",
            "metadata": {
                "description": "Resource ID of the workspace."
            }
        },
        "workspaceLocation": {
            "type": "String",
            "metadata": {
                "description": "Location of the workspace."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2017-05-10",
            "name": "VMISolutionDeployment",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceLocation')]",
                            "name": "[concat('VMInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('WorkspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[concat('VMInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[concat('OMSGallery/', 'VMInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                }
            },
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]"
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
        "workspaceResourceId": {
            "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
        },
        "workspaceLocation": {
            "value": "eastus"
        }
    }
}
```


## <a name="onboard-an-azure-virtual-machine"></a>Publicera en virtuell Azure-dator
Följande exempel lägger till en virtuell Azure-dator i VM-insikter.


### <a name="template-file"></a>Mallfil

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "VmResourceId": {
            "type": "String",
            "metadata": {
                "description": "VM Resource ID."
            }
        },
        "VmLocation": {
            "type": "String",
            "metadata": {
                "description": "The Virtual Machine Location."
            }
        },
        "osType": {
            "type": "String",
            "metadata": {
                "description": "OS Type, Example: Linux / Windows"
            }
        },
        "WorkspaceResourceId": {
            "type": "String",
            "metadata": {
                "description": "Workspace Resource ID."
            }
        }
    },
    "variables": {
        "VmName": "[split(parameters('VmResourceId'),'/')[8]]",
        "DaExtensionName": "[if(equals(toLower(parameters('osType')), 'windows'), 'DependencyAgentWindows', 'DependencyAgentLinux')]",
        "DaExtensionType": "[if(equals(toLower(parameters('osType')), 'windows'), 'DependencyAgentWindows', 'DependencyAgentLinux')]",
        "DaExtensionVersion": "9.5",
        "MmaExtensionName": "[if(equals(toLower(parameters('osType')), 'windows'), 'MMAExtension', 'OMSExtension')]",
        "MmaExtensionType": "[if(equals(toLower(parameters('osType')), 'windows'), 'MicrosoftMonitoringAgent', 'OmsAgentForLinux')]",
        "MmaExtensionVersion": "[if(equals(toLower(parameters('osType')), 'windows'), '1.0', '1.4')]"
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2018-10-01",
            "name": "[variables('VmName')]",
            "location": "[parameters('VmLocation')]",
            "resources": [
                {
                    "type": "extensions",
                    "apiVersion": "2018-10-01",
                    "name": "[variables('DaExtensionName')]",
                    "location": "[parameters('VmLocation')]",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/VirtualMachines/', variables('VmName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                        "type": "[variables('DaExtensionType')]",
                        "typeHandlerVersion": "[variables('DaExtensionVersion')]",
                        "autoUpgradeMinorVersion": true
                    }
                },
                {
                    "type": "extensions",
                    "apiVersion": "2018-10-01",
                    "name": "[variables('MmaExtensionName')]",
                    "location": "[parameters('VmLocation')]",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/VirtualMachines/', variables('VmName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "[variables('MmaExtensionType')]",
                        "typeHandlerVersion": "[variables('MmaExtensionVersion')]",
                        "autoUpgradeMinorVersion": "true",
                        "settings": {
                            "workspaceId": "[reference(parameters('WorkspaceResourceId'), '2015-03-20').customerId]",
                            "azureResourceId": "[parameters('VmResourceId')]",
                            "stopOnMultipleConnections": "true"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(parameters('WorkspaceResourceId'), '2015-03-20').primarySharedKey]"
                        }
                    }
                }
            ]
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
        "vmResourceId": {
            "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-linux-vm"
        },
        "vmLocation": {
            "value": "westus"
        },
        "osType": {
            "value": "linux"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
        }
    }
}

```


## <a name="onboard-an-azure-virtual-machine-scale-set"></a>Publicera en skalnings uppsättning för virtuella Azure-datorer
I följande exempel läggs en skalnings uppsättning för virtuella Azure-datorer till i VM-insikter.


### <a name="template-file"></a>Mallfil

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "VmssResourceId": {
            "type": "String",
            "metadata": {
                "description": "VM Resource ID."
            }
        },
        "VmssLocation": {
            "type": "String",
            "metadata": {
                "description": "The Virtual Machine Location."
            }
        },
        "osType": {
            "type": "String",
            "metadata": {
                "description": "OS Type, Example: Linux / Windows"
            }
        },
        "WorkspaceResourceId": {
            "type": "String",
            "metadata": {
                "description": "Workspace Resource ID."
            }
        }
    },
    "variables": {
        "VmssName": "[split(parameters('VmssResourceId'),'/')[8]]",
        "DaExtensionName": "[if(equals(toLower(parameters('osType')), 'windows'), 'DependencyAgentWindows', 'DependencyAgentLinux')]",
        "DaExtensionType": "[if(equals(toLower(parameters('osType')), 'windows'), 'DependencyAgentWindows', 'DependencyAgentLinux')]",
        "DaExtensionVersion": "9.5",
        "MmaExtensionName": "[if(equals(toLower(parameters('osType')), 'windows'), 'MMAExtension', 'OMSExtension')]",
        "MmaExtensionType": "[if(equals(toLower(parameters('osType')), 'windows'), 'MicrosoftMonitoringAgent', 'OmsAgentForLinux')]",
        "MmaExtensionVersion": "[if(equals(toLower(parameters('osType')), 'windows'), '1.0', '1.4')]"
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "apiVersion": "2018-10-01",
            "name": "[variables('VmssName')]",
            "location": "[parameters('VmssLocation')]",
            "resources": [
                {
                    "type": "extensions",
                    "apiVersion": "2018-10-01",
                    "name": "[variables('DaExtensionName')]",
                    "location": "[parameters('VmssLocation')]",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/virtualMachineScaleSets/', variables('VmssName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                        "type": "[variables('DaExtensionType')]",
                        "typeHandlerVersion": "[variables('DaExtensionVersion')]",
                        "autoUpgradeMinorVersion": true
                    }
                },
                {
                    "type": "extensions",
                    "apiVersion": "2018-10-01",
                    "name": "[variables('MmaExtensionName')]",
                    "location": "[parameters('VmssLocation')]",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/virtualMachineScaleSets/', variables('VmssName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "[variables('MmaExtensionType')]",
                        "typeHandlerVersion": "[variables('MmaExtensionVersion')]",
                        "autoUpgradeMinorVersion": "true",
                        "settings": {
                            "workspaceId": "[reference(parameters('WorkspaceResourceId'), '2015-03-20').customerId]",
                            "azureResourceId": "[parameters('VmssResourceId')]",
                            "stopOnMultipleConnections": "true"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(parameters('WorkspaceResourceId'), '2015-03-20').primarySharedKey]"
                        }
                    }
                }
            ]
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
        "VmssResourceId": {
            "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-windows-vmss"
        },
        "VmssLocation": {
            "value": "westus"
        },
        "OsType": {
            "value": "windows"
        },
        "WorkspaceResourceId": {
            "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
        }
    }
}
```


## <a name="next-steps"></a>Nästa steg

* [Hämta andra exempel mallar för Azure Monitor](../resource-manager-samples.md).
* [Läs mer om VM Insights](vminsights-overview.md).
