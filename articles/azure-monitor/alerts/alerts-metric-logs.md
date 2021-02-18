---
title: Skapa mått aviseringar för loggar i Azure Monitor
description: Själv studie kurs om hur du skapar statistik aviseringar i real tid i populära Log Analytics-data.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 06/17/2020
ms.subservice: alerts
ms.openlocfilehash: 7c7bb9df281a37ded51e3a35b90064d875cfd729
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100622073"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>Skapa mått varningar för loggar i Azure Monitor

## <a name="overview"></a>Översikt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor stöder [varnings typen mått](./alerts-metric-near-real-time.md) som har fördelar jämfört med de [klassiska aviseringarna](./alerts-classic-portal.md). Mått är tillgängliga för [stor lista över Azure-tjänster](../platform/metrics-supported.md). I den här artikeln beskrivs användningen av en delmängd (det vill säga) för resurs- `Microsoft.OperationalInsights/workspaces` .

Du kan använda mått varningar på populära Log Analytics loggar som har extraherats som mått som en del av mått från loggar, inklusive resurser i Azure eller lokalt. De Log Analytics lösningarna som stöds visas nedan:

- [Prestanda räknare](./../agents/data-sources-performance-counters.md) för Windows & Linux-datorer
- [Pulsslags poster för Agenthälsa](../insights/solution-agenthealth.md)
- [Uppdatera hanterings](../../automation/update-management/overview.md) poster
- [Händelse data](./../agents/data-sources-windows-events.md) loggar

Det finns många fördelar med att använda **mått aviseringar för loggar** över frågor baserade [logg aviseringar](./alerts-log.md) i Azure. en del av dem visas nedan:

- Mått aviseringar ger nära real tids övervakning och mått varningar för loggar av förgrenings data från logg källan för att säkerställa samma.
- Mått varningar är tillstånds känsliga – meddela en gång när aviseringen utlöses och när aviseringen har åtgärd ATS. i stället för att logga aviseringar, som är tillstånds lösa och hållas utlösare vid varje intervall om aviserings villkoret är uppfyllt.
- Mått varningar för loggen ger flera dimensioner, vilket möjliggör filtrering av vissa värden, t. ex. datorer, OS-typ osv. utan behovet av penning-fråga i Analytics.

> [!NOTE]
> Ett särskilt mått och/eller dimension visas bara om det finns data för den valda perioden. De här måtten är tillgängliga för kunder med Azure Log Analytics-arbetsytor.

## <a name="metrics-and-dimensions-supported-for-logs"></a>Mått och dimensioner som stöds för loggar

 Mått aviseringar stöder aviseringar för mått som använder dimensioner. Du kan använda dimensioner för att filtrera måttet till rätt nivå. En fullständig lista över mått som stöds för loggar från [Log Analytics arbets ytor](../platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) visas i listan. över lösningar som stöds.

> [!NOTE]
> Om du vill visa ett mått som stöds som har extraherats från en Log Analytics-arbetsyta via [Azure Monitor-mått](../platform/metrics-charts.md), måste en mått avisering för loggen skapas för det aktuella måttet. De dimensioner som väljs i mått-varningen för loggar – visas bara för utforskning via Azure Monitor-mått.

## <a name="creating-metric-alert-for-log-analytics"></a>Skapar mått avisering för Log Analytics

Mät data från populära loggar är skickas innan de bearbetas i Log Analytics i Azure Monitor-mått. Detta gör det möjligt för användare att använda funktionerna i mått plattformen samt mått avisering – inklusive att få aviseringar med frekvensen så låg som 1 minut.
Nedan visas hur man kan utforma en mått avisering för loggar.

## <a name="prerequisites-for-metric-alert-for-logs"></a>Krav för varnings mått för loggar

Innan Mät värdet för loggar som samlats in på Log Analytics data fungerar måste följande vara installerat och tillgängligt:

1. **Arbets ytan aktiv Log Analytics**: det måste finnas en giltig och Log Analytics aktiv arbets yta. Mer information finns i [skapa en Log Analytics arbets yta i Azure Portal](../learn/quick-create-workspace.md).
2. **Agenten har kon figurer ATS för Log Analytics arbets yta**: agenten måste konfigureras för virtuella Azure-datorer (och/eller) lokala virtuella datorer för att skicka data till arbets ytan Log Analytics som används i föregående steg. Mer information finns i [Översikt över Log Analytics-agenten](./../agents/agents-overview.md).
3. **Log Analytics lösningar som stöds är installerade**: Log Analytics lösning bör konfigureras och skicka data till Log Analytics lösningar för arbets ytor som stöds är [prestanda räknare för Windows & Linux](./../agents/data-sources-performance-counters.md), [pulsslags poster för agenthälsa](../insights/solution-agenthealth.md), [uppdaterings hantering](../../automation/update-management/overview.md)och [händelse data](./../agents/data-sources-windows-events.md).
4. **Log Analytics lösningar som har kon figurer ATS för att skicka loggar**: Log Analytics lösning ska ha nödvändiga loggar/data som motsvarar [mått som stöds för Log Analytics arbets ytor som](../platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) är aktiverade. Till exempel måste för den *% tillgängliga minnes* räknaren konfigureras i lösningen för [prestanda räknare](./../agents/data-sources-performance-counters.md) först.

## <a name="configuring-metric-alert-for-logs"></a>Konfigurera mått avisering för loggar

 Mått aviseringar kan skapas och hanteras med hjälp av Azure Portal, Resource Manager-mallar, REST API, PowerShell och Azure CLI. Eftersom mått aviseringar för loggar är en variant av mått aviseringar – när förutsättningarna är klara kan du skapa mått avisering för loggar för den angivna Log Analytics arbets ytan. Alla egenskaper och funktioner för [mått aviseringar](./alerts-metric-near-real-time.md) gäller även för mått aviseringar för loggar. inklusive nytto Last schema, tillämpliga kvot gränser och fakturerat pris.

Steg-för-steg-information och exempel – se [skapa och hantera mått aviseringar](./alerts-metric.md). Mer specifikt för mått aviseringar för loggar – Följ anvisningarna för att hantera mått varningar och se till att följande:

- Mål för mått avisering är giltig *Log Analytics arbets yta*
- Den signal som valts för mått avisering för den valda *Log Analytics arbets ytan* är av typen **mått**
- Filtrera efter vissa villkor eller resurser med dimensions filter; mått för loggar är flerdimensionella
- När du konfigurerar *signal logik* kan en enskild avisering skapas för att omfatta flera dimensions värden (t. ex. datorer)
- Om du **inte** använder Azure Portal för att skapa mått aviseringar för den valda *Log Analytics arbets ytan*; Därefter måste användaren manuellt skapa en explicit regel för att konvertera loggdata till ett mått med [Azure Monitor-schemalagda Frågeregler](/rest/api/monitor/scheduledqueryrules).

> [!NOTE]
> När du skapar mått avisering för Log Analytics arbets yta via Azure Portal motsvarande regel för att konvertera loggdata till mått via [Azure Monitor-schemalagda Frågeregler](/rest/api/monitor/scheduledqueryrules) skapas automatiskt i bakgrunden *utan att användaren behöver vidta några åtgärder eller åtgärder*. För mått varningar för skapande av loggar med andra metoder än Azure Portal, se [resurs mal len för mått aviseringar för](#resource-template-for-metric-alerts-for-logs) avsnittet om loggar i exempel på hur du skapar en ScheduledQueryRule baserad logg till mått konverterings regeln innan måttet skapas, annars kommer det inte att finnas några data för mått varningen för loggar som skapats.

## <a name="resource-template-for-metric-alerts-for-logs"></a>Resurs mal len för mått varningar för loggar

Som tidigare nämnts är processen för att skapa mått aviseringar från loggar två huvud:

1. Skapa en regel för att extrahera mått från loggar som stöds med scheduledQueryRule-API
2. Skapa en mått varning för mått som extraheras från logg (i steg 1) och Log Analytics arbets ytan som en mål resurs

### <a name="metric-alerts-for-logs-with-static-threshold"></a>Mått aviseringar för loggar med statiskt tröskelvärde

För att uppnå samma kan du använda exemplet Azure Resource Manager mallen nedan – där det skapas en varning om statisk tröskel som är beroende av att regeln har skapats för att extrahera mått från loggar via scheduledQueryRule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example: /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Anta att JSON-filen ovan sparas som metricfromLogsAlertStatic.jsd, kan den kopplas till en JSON-fil för att skapa en resurs mal len. En JSON-fil med exempel parametern visas nedan:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule" 
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterThan"
        },
        "threshold":{
            "value": "1"
        },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Förutsatt att parameter filen ovan sparas som metricfromLogsAlertStatic.parameters.jspå. sedan kan en måtta-aviseringar skapas för loggar med hjälp av [resurs mal len för att skapa i Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

Alternativt kan du även använda Azure PowerShell-kommandot nedan:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertStatic.json TemplateParameterFile metricfromLogsAlertStatic.parameters.json
```

Eller Använd distribuera resurs mal len med Azure CLI:

```azurecli
az deployment group create --resource-group myRG --template-file metricfromLogsAlertStatic.json --parameters @metricfromLogsAlertStatic.parameters.json
```

### <a name="metric-alerts-for-logs-with-dynamic-thresholds"></a>Mått aviseringar för loggar med dynamiska tröskelvärden

För att uppnå samma kan du använda exemplet Azure Resource Manager mallen nedan – där en måtta för dynamiska tröskelvärden skapas, beroende på lyckad skapande av regeln för extrahering av mått från loggar via scheduledQueryRule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example: /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Anta att JSON-filen ovan sparas som metricfromLogsAlertDynamic.jsd, kan den kopplas till en JSON-fil för att skapa en resurs mal len. En JSON-fil med exempel parametern visas nedan:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule"
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterOrLessThan"
          },
          "alertSensitivity": {
              "value": "Medium"
          },
          "numberOfEvaluationPeriods": {
              "value": "4"
          },
          "minFailingPeriodsToAlert": {
              "value": "3"
          },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Förutsatt att parameter filen ovan sparas som metricfromLogsAlertDynamic.parameters.jspå. sedan kan en måtta-aviseringar skapas för loggar med hjälp av [resurs mal len för att skapa i Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

Alternativt kan du även använda Azure PowerShell-kommandot nedan:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertDynamic.json TemplateParameterFile metricfromLogsAlertDynamic.parameters.json
```

Eller Använd distribuera resurs mal len med Azure CLI:

```azurecli
az deployment group create --resource-group myRG --template-file metricfromLogsAlertDynamic.json --parameters @metricfromLogsAlertDynamic.parameters.json
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [mått aviseringarna](../alerts/alerts-metric.md).
- Lär dig mer om [logg aviseringar i Azure](./alerts-unified-log.md).
- Lär dig mer om [aviseringar i Azure](../platform/alerts-overview.md).