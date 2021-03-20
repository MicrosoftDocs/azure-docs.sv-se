---
title: Skapa Azure Advisor aviseringar för nya rekommendationer med Resource Manager-mall
description: Lär dig hur du konfigurerar en avisering för nya rekommendationer från Azure Advisor med hjälp av en Azure Resource Manager-mall (ARM-mall).
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: 7b10ad06e5397ab733987373a3bd18de981c8c97
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "100590058"
---
# <a name="quickstart-create-azure-advisor-alerts-on-new-recommendations-using-an-arm-template"></a>Snabb start: Skapa Azure Advisor aviseringar om nya rekommendationer med en ARM-mall

Den här artikeln visar hur du ställer in en avisering för nya rekommendationer från Azure Advisor med hjälp av en Azure Resource Manager-mall (ARM-mall).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

När Azure Advisor identifierar en ny rekommendation för en av dina resurser lagras en händelse i [Azure aktivitets loggen](../azure-monitor/essentials/platform-logs-overview.md). Du kan ställa in aviseringar för dessa händelser från Azure Advisor med hjälp av en rekommendation för att skapa aviseringar. Du kan välja en prenumeration och eventuellt en resurs grupp för att ange de resurser som du vill ta emot aviseringar på.

Du kan också bestämma rekommendationernas typer genom att använda följande egenskaper:

- Kategori
- Effekt nivå
- Rekommendations typ

Du kan också konfigurera den åtgärd som ska vidtas när en avisering utlöses av:

- Välja en befintlig åtgärds grupp
- Skapa en ny åtgärds grupp

Mer information om åtgärds grupper finns i [skapa och hantera åtgärds grupper](../azure-monitor/alerts/action-groups.md).

> [!NOTE]
> Advisor-aviseringar är för närvarande endast tillgängliga för hög tillgänglighet, prestanda och kostnads rekommendationer. Säkerhets rekommendationer stöds inte.

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Om du vill köra kommandona från den lokala datorn installerar du Azure CLI eller Azure PowerShell modulerna. Mer information finns i [Installera Azure CLI](/cli/azure/install-azure-cli) och [Installera Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Granska mallen

Följande mall skapar en åtgärds grupp med ett e-postmål och aktiverar alla meddelanden om tjänst hälsa för mål prenumerationen. Spara den här mallen som *CreateAdvisorAlert.jspå*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "apiVersion": "2019-06-01",
      "name": "[parameters('actionGroups_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [],
      "tags": {},
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      }
    },
    {
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "tags": {},
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
```

Mallen definierar två resurser:

- [Microsoft. Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft. Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>Distribuera mallen

Distribuera mallen med valfri standard metod för att [distribuera en arm-mall](../azure-resource-manager/templates/deploy-portal.md) som i följande exempel med CLI och PowerShell. Ersätt exempel värden för **resurs grupp** och **EmailAddress** med lämpliga värden för din miljö. Arbets ytans namn måste vara unikt bland alla Azure-prenumerationer.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateAdvisorAlert --resource-group my-resource-group --template-file CreateAdvisorAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateAdvisorAlert -ResourceGroupName my-resource-group -TemplateFile CreateAdvisorAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>Verifiera distributionen

Kontrol lera att arbets ytan har skapats med något av följande kommandon. Ersätt exempel värden för **resurs grupp** med det värde som du använde ovan.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name AdvisorAlertsTest
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name AdvisorAlertsTest
```

---

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med efterföljande snabb starter och självstudier, kanske du vill lämna dessa resurser på plats. När de inte längre behövs tar du bort resurs gruppen, som tar bort aviserings regeln och relaterade resurser. Ta bort resurs gruppen med hjälp av Azure CLI eller Azure PowerShell

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Remove-AzResourceGroup -Name my-resource-group
```

---

## <a name="next-steps"></a>Nästa steg

- Få en [Översikt över aktivitets logg aviseringar](../azure-monitor/alerts/alerts-overview.md)och lär dig hur du tar emot aviseringar.
- Läs mer om [Åtgärds grupper](../azure-monitor/alerts/action-groups.md).
