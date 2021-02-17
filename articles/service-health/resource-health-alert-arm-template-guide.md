---
title: Mall för att skapa Resource Health aviseringar
description: Skapa aviseringar program mässigt som meddelar dig när dina Azure-resurser blir otillgängliga.
ms.topic: conceptual
ms.date: 9/4/2018
ms.openlocfilehash: 4f1cbe1e2d2c185906feb4ccba380cb31df864f5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588199"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Konfigurera resurshälsoaviseringar med Resource Manager-mallar

I den här artikeln får du lära dig hur du skapar Resource Health aktivitets logg aviseringar via programmering med hjälp av Azure Resource Manager mallar och Azure PowerShell.

Azure Resource Health håller dig informerad om aktuella och historiska hälso status för dina Azure-resurser. Azure Resource Health-avisering kan meddela dig i nästan realtid när dessa resurser har en ändring i sin hälsostatus. Skapa Resource Health aviseringar program mässigt tillåta att användare skapar och anpassar aviseringar i flera.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa instruktionerna på den här sidan måste du konfigurera några saker i förväg:

1. Du måste installera Azure PowerShell- [modulen](/powershell/azure/install-az-ps)
2. Du måste [skapa eller återanvända en åtgärds grupp](../azure-monitor/alerts/action-groups.md) som kon figurer ATS för att meddela dig

## <a name="instructions"></a>Instruktioner
1. Använd PowerShell för att logga in på Azure med ditt konto och välj den prenumeration som du vill interagera med

    ```azurepowershell
    Login-AzAccount
    Select-AzSubscription -Subscription <subscriptionId>
    ```

    > Du kan använda `Get-AzSubscription` för att visa en lista över de prenumerationer som du har åtkomst till.

2. Hitta och spara det fullständiga Azure Resource Manager-ID: t för din åtgärds grupp

    ```azurepowershell
    (Get-AzActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id
    ```

3. Skapa och spara en Resource Manager-mall för Resource Health aviseringar som `resourcehealthalert.json` ([se informationen nedan](#resource-manager-template-options-for-resource-health-alerts))

4. Skapa en ny Azure Resource Manager-distribution med hjälp av den här mallen

    ```azurepowershell
    New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>
    ```

5. Du uppmanas att ange det aviserings namn och den åtgärds grupp resurs-ID som du kopierade tidigare:

    ```azurepowershell
    Supply values for the following parameters:
    (Type !? for Help.)
    activityLogAlertName: <Alert Name>
    actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>
    ```

6. Om allt fungerade utan problem får du en bekräftelse i PowerShell

    ```output
    DeploymentName          : ExampleDeployment
    ResourceGroupName       : <resourceGroup>
    ProvisioningState       : Succeeded
    Timestamp               : 11/8/2017 2:32:00 AM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                            Name                     Type       Value
                            ===============          =========  ==========
                            activityLogAlertName     String     <Alert Name>
                            activityLogAlertEnabled  Bool       True
                            actionGroupResourceId    String     /...

    Outputs                 :
    DeploymentDebugLogLevel :
    ```

Observera att om du planerar att helt automatisera den här processen behöver du bara redigera Resource Manager-mallen för att inte fråga efter värdena i steg 5.

## <a name="resource-manager-template-options-for-resource-health-alerts"></a>Alternativ för Resource Manager-mallar för Resource Health aviseringar

Du kan använda den här bas mal len som utgångs punkt för att skapa Resource Health aviseringar. Den här mallen fungerar som skrivet och registrerar dig för att ta emot aviseringar om alla nyligen aktiverade resurs hälso händelser över alla resurser i en prenumeration.

> Längst ned i den här artikeln har vi också inkluderat en mer komplex varnings mall som ska öka signalen till brus förhållandet för Resource Health aviseringar jämfört med den här mallen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": true,
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ResourceHealth"
            },
            {
              "field": "status",
              "equals": "Active"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

En bred avisering som den här typen av sådan rekommenderas dock vanligt vis inte. Lär dig hur vi kan fokusera på den här aviseringen och fokusera på de händelser vi bryr oss om.

### <a name="adjusting-the-alert-scope"></a>Justera aviserings omfånget

Resource Health aviseringar kan konfigureras för övervakning av händelser i tre olika omfång:

 * Prenumerations nivå
 * Resurs grupps nivå
 * Resurs nivå

Varnings mal len konfigureras på prenumerations nivån, men om du vill konfigurera aviseringen så att den bara meddelar dig om vissa resurser eller resurser inom en viss resurs grupp, behöver du bara ändra `scopes` avsnittet i ovanstående mall.

För en resurs grupp nivå omfattning bör avsnittet omfattningar se ut så här:
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

För en resurs nivå omfattning bör avsnittet omfattning se ut så här:

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Exempelvis: `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Du kan gå till Azure-portalen och titta på URL: en när du visar din Azure-resurs för att hämta den här strängen.

### <a name="adjusting-the-resource-types-which-alert-you"></a>Justera resurs typerna som varnar dig

Aviseringar på prenumerations-eller resurs grupps nivå kan ha olika typer av resurser. Om du vill begränsa aviseringarna till att endast komma från en viss del av resurs typerna kan du definiera det i `condition` avsnittet i mallen:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.COMPUTE/VIRTUALMACHINES",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.STORAGE/STORAGEACCOUNTS",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

Här använder vi `anyOf` omslutningen för att tillåta resurs hälso aviseringen att matcha de villkor som vi anger, vilket ger aviseringar som riktar sig mot specifika resurs typer.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>Justera Resource Health händelser som varnar dig
När resurser genomgår en hälso händelse kan de gå igenom en serie steg som representerar hälso tillståndet: `Active` ,, `In Progress` `Updated` och `Resolved` .

Du kanske bara vill bli meddelad när en resurs blir skadad, i vilket fall du vill konfigurera aviseringen så att den endast meddelar när `status` är `Active` . Men om du vill att du även vill bli meddelad i de andra stegen kan du lägga till dessa uppgifter så här:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "status",
                    "equals": "Active"
                },
                {
                    "field": "status",
                    "equals": "In Progress"
                },
                {
                    "field": "status",
                    "equals": "Resolved"
                },
                {
                    "field": "status",
                    "equals": "Updated"
                }
            ]
        }
    ]
}
```

Om du vill bli meddelad om alla fyra stadier av hälso tillstånds händelser kan du ta bort det här villkoret tillsammans och aviseringen meddelar dig oberoende av `status` egenskapen.

> [!NOTE]
> Varje "anyOf"-avsnitt ska bara innehålla ett fält typs värde.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>Justera Resource Health aviseringar för att undvika "okända" händelser

Azure Resource Health kan rapportera till dig med de senaste hälso tillståndet för dina resurser genom att kontinuerligt övervaka dem med hjälp av test-löpare. De relevanta rapporterade hälso statusarna är: "tillgängliga", "otillgänglig" och "degraderad". Men i situationer där löpare och Azure-resursen inte kan kommunicera, rapporteras en "okänd" hälso status för resursen och det anses vara en "aktiv"-hälso händelse.

Men när en resurs rapporterar "okänd", är det troligt att dess hälso status inte har ändrats sedan den senaste korrekta rapporten. Om du vill eliminera varningar om "okända" händelser kan du ange den logiken i mallen:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
        {
            "anyOf": [
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
    ]
},
```

I det här exemplet meddelar vi bara händelser där aktuella och tidigare hälso status inte har "okänd". Den här ändringen kan vara ett användbart tillägg om aviseringarna skickas direkt till din mobil telefon eller e-post. 

Observera att det är möjligt att egenskaperna currentHealthStatus och previousHealthStatus är null i vissa händelser. Till exempel, när en uppdaterad händelse inträffar är det troligt att resursens hälso status inte har ändrats sedan den senaste rapporten, bara att ytterligare händelse information är tillgänglig (t. ex. orsak). Med hjälp av satsen ovan kan det därför leda till att vissa aviseringar inte utlöses, eftersom värdena Properties. currentHealthStatus och Properties. previousHealthStatus får värdet null.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>Justera aviseringen för att undvika händelser som initieras av användaren

Resource Health händelser kan utlösas av inaktiverade plattformar och händelser som initieras av användaren. Det kan vara klokt att bara skicka ett meddelande när hälso händelsen orsakas av Azure-plattformen.

Det är enkelt att konfigurera aviseringen så att den bara filtrerar för följande typer av händelser:

```json
"condition": {
    "allOf": [
        ...,
        {
            "field": "properties.cause",
            "equals": "PlatformInitiated",
            "containsAny": null
        }
    ]
}
```
Observera att det är möjligt att fältet orsak är null i vissa händelser. Det vill säga att en hälso över gång sker (t. ex. tillgänglig för otillgänglig) och att händelsen loggas omedelbart för att förhindra meddelande fördröjningar. Med hjälp av satsen ovan kan det därför leda till att en avisering inte utlöses eftersom egenskap svärdet Properties. sats kommer att anges till null.

## <a name="complete-resource-health-alert-template"></a>Slutför Resource Health aviserings mal len

Med hjälp av de olika justeringarna som beskrivs i föregående avsnitt är här en exempel mall som är konfigurerad för att maximera signalen till brus förhållandet. Tänk på de varningar som anges ovan där egenskapsvärdena currentHealthStatus, previousHealthStatus och orsak kan vara null i vissa händelser.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "activityLogAlertName": {
            "type": "string",
            "metadata": {
                "description": "Unique name (within the Resource Group) for the Activity log alert."
            }
        },
        "actionGroupResourceId": {
            "type": "string",
            "metadata": {
                "description": "Resource Id for the Action group."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/activityLogAlerts",
            "apiVersion": "2017-04-01",
            "name": "[parameters('activityLogAlertName')]",
            "location": "Global",
            "properties": {
                "enabled": true,
                "scopes": [
                    "[subscription().id]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ResourceHealth",
                            "containsAny": null
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.cause",
                                    "equals": "PlatformInitiated",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "status",
                                    "equals": "Active",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Resolved",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "In Progress",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Updated",
                                    "containsAny": null
                                }
                            ]
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[parameters('actionGroupResourceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

Du vet dock bäst vilka konfigurationer som är effektiva för dig, så Använd de verktyg som beskrivs i den här dokumentationen för att göra din egen anpassning.

## <a name="next-steps"></a>Nästa steg

Läs mer om Resource Health:
-  [Översikt över Azure Resource Health](Resource-health-overview.md)
-  [Resurstyper och hälsokontroller är tillgängliga genom Azure Resource Health](resource-health-checks-resource-types.md)


Skapa Service Health aviseringar:
-  [Konfigurera aviseringar för Service Health](./alerts-activity-log-service-notifications-portal.md) 
-  [Händelse schema för Azure aktivitets logg](../azure-monitor/essentials/activity-log-schema.md)
