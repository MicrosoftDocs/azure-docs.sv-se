---
title: Skapa Azure Advisor för nya rekommendationer med hjälp av Resource Manager mall
description: Lär dig hur du ställer in en avisering för nya rekommendationer från Azure Advisor en Azure Resource Manager mall (ARM-mall).
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.date: 06/29/2020
ms.openlocfilehash: 716ab104ce6517aeb554b42522e5906829877259
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765677"
---
# <a name="quickstart-create-azure-advisor-alerts-on-new-recommendations-using-an-arm-template"></a>Snabbstart: Skapa Azure Advisor nya rekommendationer med hjälp av en ARM-mall

Den här artikeln visar hur du ställer in en avisering för nya rekommendationer från Azure Advisor med hjälp av en Azure Resource Manager mall (ARM-mall).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

När Azure Advisor identifierar en ny rekommendation för en av dina resurser lagras en händelse i [Azure-aktivitetsloggen](../azure-monitor/essentials/platform-logs-overview.md). Du kan ställa in aviseringar för dessa händelser från Azure Advisor med hjälp av en rekommendationsspecifik upplevelse för att skapa aviseringar. Du kan välja en prenumeration och eventuellt en resursgrupp för att ange vilka resurser du vill få aviseringar om.

Du kan också bestämma vilka typer av rekommendationer som ska användas med hjälp av följande egenskaper:

- Kategori
- Effektnivå
- Rekommendationstyp

Du kan också konfigurera den åtgärd som ska vidtas när en avisering utlöses av:

- Välja en befintlig åtgärdsgrupp
- Skapa en ny åtgärdsgrupp

Mer information om åtgärdsgrupper finns i [Skapa och hantera åtgärdsgrupper.](../azure-monitor/alerts/action-groups.md)

> [!NOTE]
> Advisor-aviseringar är för närvarande endast tillgängliga för rekommendationer för hög tillgänglighet, prestanda och kostnad. Säkerhetsrekommendationer stöds inte.

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Om du vill köra kommandon från den lokala datorn installerar du Azure CLI eller Azure PowerShell moduler. Mer information finns i [Installera Azure CLI och](/cli/azure/install-azure-cli) Installera [Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Granska mallen

Följande mall skapar en åtgärdsgrupp med ett e-postmål och aktiverar alla service health-meddelanden för målprenumerationen. Spara den här mallen *somCreateAdvisorAlert.jspå*.

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

- [Microsoft.Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft.Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>Distribuera mallen

Distribuera mallen med valfri standardmetod för att [distribuera en ARM-mall,](../azure-resource-manager/templates/deploy-portal.md) till exempel följande exempel med hjälp av CLI och PowerShell. Ersätt exempelvärdena **för Resursgrupp** och **emailAddress** med lämpliga värden för din miljö. Namnet på arbetsytan måste vara unikt bland alla Azure-prenumerationer.

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

Kontrollera att arbetsytan har skapats med något av följande kommandon. Ersätt exempelvärdena för **Resursgrupp med** det värde som du använde ovan.

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

Om du planerar att fortsätta arbeta med efterföljande snabbstarter och självstudier kanske du vill lämna dessa resurser på plats. Ta bort resursgruppen när den inte längre behövs, vilket tar bort aviseringsregeln och de relaterade resurserna. Ta bort resursgruppen med hjälp av Azure CLI eller Azure PowerShell

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

- Få en [översikt över aktivitetsloggaviseringar](../azure-monitor/alerts/alerts-overview.md)och lär dig hur du tar emot aviseringar.
- Läs mer om [åtgärdsgrupper.](../azure-monitor/alerts/action-groups.md)
