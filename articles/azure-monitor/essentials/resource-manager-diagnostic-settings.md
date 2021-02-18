---
title: Exempel på Resource Manager-mallar för diagnostikinställningar
description: Exempel Azure Resource Manager mallar som används för att tillämpa Azure Monitor diagnostikinställningar på en Azure-resurs.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/11/2020
ms.openlocfilehash: 87951c0366090c1ea4eba490d114771e47bf452c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100628042"
---
# <a name="resource-manager-template-samples-for-diagnostic-settings-in-azure-monitor"></a>Exempel på Resource Manager-mallar för diagnostikinställningar i Azure Monitor
Den här artikeln innehåller exempel [Azure Resource Manager mallar](../../azure-resource-manager/templates/template-syntax.md) för att skapa diagnostiska inställningar för en Azure-resurs. Varje exempel innehåller en mallfil och en parameter fil med exempel värden som du kan använda för mallen.

Om du vill skapa en diagnostisk inställning för en Azure-resurs lägger du till en resurs av typen `<resource namespace>/providers/diagnosticSettings` i mallen. Den här artikeln innehåller exempel på vissa resurs typer, men samma mönster kan användas för andra resurs typer. Samlingen av tillåtna loggar och mått varierar beroende på varje resurs typ.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="diagnostic-setting-for-activity-log"></a>Diagnostisk inställning för aktivitets logg
I följande exempel skapas en diagnostisk inställning för en aktivitets logg genom att lägga till en resurs av typen `Microsoft.Insights/diagnosticSettings` i mallen.

> [!IMPORTANT]
> Diagnostikinställningar för aktivitets loggar skapas för en prenumeration, inte för en resurs grupp som inställningar för Azure-resurser. Om du vill distribuera resurs hanterings mal len använder du `New-AzSubscriptionDeployment` för PowerShell eller `az deployment sub create` för Azure CLI.

### <a name="template-file"></a>Mallfil

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
          "type": "String"
        },
        "workspaceId": {
          "type": "String"
        },
        "storageAccountId": {
          "type": "String"
        },
        "eventHubAuthorizationRuleId": {
          "type": "String"
        },
        "eventHubName": {
          "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[parameters('settingName')]",
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "logs": [
                    {
                        "category": "Administrative",
                        "enabled": true
                    },
                    {
                        "category": "Security",
                        "enabled": true
                    },
                    {
                        "category": "ServiceHealth",
                        "enabled": true
                    },
                    {
                        "category": "Alert",
                        "enabled": true
                    },
                    {
                        "category": "Recommendation",
                        "enabled": true
                    },
                    {
                        "category": "Policy",
                        "enabled": true
                    },
                    {
                        "category": "Autoscale",
                        "enabled": true
                    },
                    {
                        "category": "ResourceHealth",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameter fil

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
        "value": "Send to all locations"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```


## <a name="diagnostic-setting-for-azure-key-vault"></a>Diagnostisk inställning för Azure Key Vault 
I följande exempel skapas en diagnostisk inställning för en Azure Key Vault genom att lägga till en resurs av typen `Microsoft.KeyVault/vaults/providers/diagnosticSettings` i mallen.

### <a name="template-file"></a>Mallfil

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "String"
        },
        "vaultName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }

    },
    "resources": [
        {
          "type": "Microsoft.KeyVault/vaults/providers/diagnosticSettings",
          "apiVersion": "2017-05-01-preview",
          "name": "[concat(parameters('vaultName'), '/Microsoft.Insights/', parameters('settingName'))]",
          "dependsOn": [],
          "properties": {
            "workspaceId": "[parameters('workspaceId')]",
            "storageAccountId": "[parameters('storageAccountId')]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "logs": [
              {
                "category": "AuditEvent",
                "enabled": true
              }
            ],
            "metrics": [
              {
                "category": "AllMetrics",
                "enabled": true
              }
            ]
          }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameter fil

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
          "value": "Send to all locations"
      },
      "vaultName": {
        "value": "MyVault"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```

## <a name="diagnostic-setting-for-azure-sql-database"></a>Diagnostisk inställning för Azure SQL Database
I följande exempel skapas en diagnostisk inställning för en Azure SQL-databas genom att lägga till en resurs av typen `microsoft.sql/servers/databases/providers/diagnosticSettings` i mallen.

### <a name="template-file"></a>Mallfil

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "String"
        },        
        "serverName": {
            "type": "String"
        },
        "dbName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }

    },
    "resources": [
        {
          "type": "microsoft.sql/servers/databases/providers/diagnosticSettings",
          "apiVersion": "2017-05-01-preview",
          "name": "[concat(parameters('serverName'),'/',parameters('dbName'),'/microsoft.insights/', parameters('settingName'))]",
          "dependsOn": [],
          "properties": {
            "workspaceId": "[parameters('workspaceId')]",
            "storageAccountId": "[parameters('storageAccountId')]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "logs": [
              {
                "category": "SQLInsights",
                "enabled": true
              },
              {
                "category": "AutomaticTuning",
                "enabled": true
              },
              {
                "category": "QueryStoreRuntimeStatistics",
                "enabled": true
              },
              {
                "category": "QueryStoreWaitStatistics",
                "enabled": true
              },
              {
                "category": "Errors",
                "enabled": true
              },
              {
                "category": "DatabaseWaitStatistics",
                "enabled": true
              },
              {
                "category": "Timeouts",
                "enabled": true
              },
              {
                "category": "Blocks",
                "enabled": true
              },
              {
                "category": "Deadlocks",
                "enabled": true
              }
            ],
            "metrics": [
              {
                "category": "Basic",
                "enabled": true
              },
              {
                "category": "InstanceAndAppAdvanced",
                "enabled": true
              },
              {
                "category": "WorkloadManagement",
                "enabled": true
              }
            ]
          }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameter fil

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
          "value": "Send to all locations"
      },
      "serverName": {
        "value": "MySqlServer"
      },
      "dbName": {
        "value": "MySqlDb"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```

## <a name="diagnostic-setting-for-recovery-services-vault"></a>Diagnostisk inställning för Recovery Services valv
I följande exempel skapas en diagnostisk inställning för ett Azure Recovery Services-valv genom att lägga till en resurs av typen `microsoft.recoveryservices/vaults/providers/diagnosticSettings` i mallen. I det här exemplet anges samlings läget enligt beskrivningen i [Azures resurs loggar](../platform/resource-logs.md#send-to-log-analytics-workspace). Ange `Dedicated` eller `AzureDiagnostics` för `logAnalyticsDestinationType` egenskapen.

### <a name="template-file"></a>Mallfil

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "recoveryServicesName": {
            "type": "String"
        },
        "settingName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "microsoft.recoveryservices/vaults/providers/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[concat(parameters('recoveryServicesName'), '/Microsoft.Insights/', parameters('settingName'))]",
            "dependsOn": [],
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "metrics": [],
                "logs": [
                    {
                        "category": "AzureBackupReport",
                        "enabled": false
                    },
                    {
                        "category": "CoreAzureBackup",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupJobs",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupAlerts",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupPolicy",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupStorage",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupProtectedInstance",
                        "enabled": true
                    },
                    {
                        "category": "AzureSiteRecoveryJobs",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryEvents",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryReplicatedItems",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryReplicationStats",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryRecoveryPoints",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryReplicationDataUploadRate",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryProtectedDiskDataChurn",
                        "enabled": false
                    }
                ],
                "logAnalyticsDestinationType": "Dedicated"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameter fil

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
          "value": "Send to all locations"
      },
      "recoveryServicesName": {
        "value": "my-vault"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```

## <a name="diagnostic-setting-for-log-analytics-workspace"></a>Diagnostisk inställning för Log Analytics arbets yta
I följande exempel skapas en diagnostisk inställning för ett Log Analytics arbets ytans valv genom att lägga till en resurs av typen `Microsoft.OperationalInsights/workspaces/providers/diagnosticSettings` i mallen. I det här exemplet skickas gransknings data om frågor som körs i arbets ytan till samma arbets yta.

### <a name="template-file"></a>Mallfil

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String"
        },
        "settingName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces/providers/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[concat(parameters('workspaceName'), '/Microsoft.Insights/', parameters('settingName'))]",
            "dependsOn": [],
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "metrics": [],
                "logs": [
                    {
                        "category": "Audit",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameter fil

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
          "value": "Send to all locations"
      },
      "workspaceName": {
        "value": "MyWorkspace"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```

## <a name="diagnostic-setting-for-azure-storage"></a>Diagnostisk inställning för Azure Storage
I följande exempel skapas en diagnostisk inställning för varje lagrings tjänst slut punkt som är tillgänglig i lagrings kontot. En inställning tillämpas på varje enskild lagrings tjänst som är tillgänglig på kontot. Vilka lagrings tjänster som är tillgängliga beror på lagrings kontots typ. Den här mallen skapar bara en diagnostisk inställning för en lagrings tjänst i kontot om den finns för kontot. För varje tillgänglig tjänst aktiverar Diagnostic-inställningen transaktions mått och samlingen av resurs loggar för Läs-, skriv-och borttagnings åtgärder.

### <a name="template-file"></a>Mallfil

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "type": "string"
        },
        "settingName": {
            "type": "string"
        },
        "storageSyncName": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-10-01",
            "name": "nested",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "expressionEvaluationOptions": {
                    "scope": "inner"
                },
                "parameters": {
                    "endpoints": {
                        "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01', 'Full').properties.primaryEndpoints]"
                    },
                    "settingName": {
                        "value": "[parameters('settingName')]"
                    },
                    "storageAccountName": {
                        "value": "[parameters('storageAccountName')]"
                    },
                    "storageSyncName": {
                        "value": "[parameters('storageSyncName')]"
                    },
                    "workspaceId": {
                        "value": "[parameters('workspaceId')]"
                    }
                },
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "endpoints": {
                            "type": "object"
                        },
                        "settingName": {
                            "type": "String"
                        },
                        "storageAccountName": {
                            "type": "String"
                        },
                        "storageSyncName": {
                            "type": "String"
                        },
                        "workspaceId": {
                            "type": "String"
                        }
                    },
                    "variables": {
                        "hasblob": "[contains(parameters('endpoints'),'blob')]",
                        "hastable": "[contains(parameters('endpoints'),'table')]",
                        "hasfile": "[contains(parameters('endpoints'),'file')]",
                        "hasqueue": "[contains(parameters('endpoints'),'queue')]"
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts/providers/diagnosticsettings",
                            "apiVersion": "2017-05-01-preview",
                            "name": "[concat(parameters('storageAccountName'),'/Microsoft.Insights/', parameters('settingName'))]",

                            "properties": {
                                "workspaceId": "[parameters('workspaceId')]",
                                "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageSyncName'))]",
                                "metrics": [
                                    {
                                        "category": "Transaction",
                                        "enabled": true
                                    }
                                ]
                            }
                        },
                        {
                            "condition": "[variables('hasblob')]",
                            "type": "Microsoft.Storage/storageAccounts/blobServices/providers/diagnosticsettings",
                            "apiVersion": "2017-05-01-preview",
                            "name": "[concat(parameters('storageAccountName'),'/default/Microsoft.Insights/', parameters('settingName'))]",
                            "properties": {
                                "workspaceId": "[parameters('workspaceId')]",
                                "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageSyncName'))]",
                                "logs": [
                                    {
                                        "category": "StorageRead",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageWrite",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageDelete",
                                        "enabled": true
                                    }
                                ],
                                "metrics": [
                                    {
                                        "category": "Transaction",
                                        "enabled": true
                                    }
                                ]
                            }
                        },
                        {
                            "condition": "[variables('hastable')]",
                            "type": "Microsoft.Storage/storageAccounts/tableServices/providers/diagnosticsettings",
                            "apiVersion": "2017-05-01-preview",
                            "name": "[concat(parameters('storageAccountName'),'/default/Microsoft.Insights/', parameters('settingName'))]",

                            "properties": {
                                "workspaceId": "[parameters('workspaceId')]",
                                "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageSyncName'))]",
                                "logs": [
                                    {
                                        "category": "StorageRead",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageWrite",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageDelete",
                                        "enabled": true
                                    }
                                ],
                                "metrics": [
                                    {
                                        "category": "Transaction",
                                        "enabled": true
                                    }
                                ]
                            }
                        },
                        {
                            "condition": "[variables('hasfile')]",
                            "type": "Microsoft.Storage/storageAccounts/fileServices/providers/diagnosticsettings",
                            "apiVersion": "2017-05-01-preview",
                            "name": "[concat(parameters('storageAccountName'),'/default/Microsoft.Insights/', parameters('settingName'))]",
                            "properties": {
                                "workspaceId": "[parameters('workspaceId')]",
                                "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageSyncName'))]",
                                "logs": [
                                    {
                                        "category": "StorageRead",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageWrite",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageDelete",
                                        "enabled": true
                                    }
                                ],
                                "metrics": [
                                    {
                                        "category": "Transaction",
                                        "enabled": true
                                    }
                                ]
                            }
                        },
                        {
                            "condition": "[variables('hasqueue')]",
                            "type": "Microsoft.Storage/storageAccounts/queueServices/providers/diagnosticsettings",
                            "apiVersion": "2017-05-01-preview",
                            "name": "[concat(parameters('storageAccountName'),'/default/Microsoft.Insights/', parameters('settingName'))]",
                            "properties": {
                                "workspaceId": "[parameters('workspaceId')]",
                                "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageSyncName'))]",
                                "logs": [
                                    {
                                        "category": "StorageRead",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageWrite",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageDelete",
                                        "enabled": true
                                    }
                                ],
                                "metrics": [
                                    {
                                        "category": "Transaction",
                                        "enabled": true
                                    }
                                ]
                            }
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameter fil

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "storageAccountName": {
          "value": "mymonitoredstorageaccount"
      },
      "settingName": {
          "value": "Send to all locations"
      },
      "storageSyncName": {
          "value": "mystorageaccount"
      },
      "workspaceId": {
          "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      }
    }
  }
```

## <a name="next-steps"></a>Nästa steg

* [Hämta andra exempel mallar för Azure Monitor](../samples/resource-manager-samples.md).
* [Läs mer om diagnostikinställningar](../essentials/diagnostic-settings.md).
