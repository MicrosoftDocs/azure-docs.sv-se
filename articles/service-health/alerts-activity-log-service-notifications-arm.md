---
title: Ta emot aktivitetsloggaviseringar i Azure-tjänstmeddelanden med hjälp Resource Manager mall
description: Bli meddelad via SMS, e-post eller webhook när Azure-tjänsten inträffar.
ms.date: 06/29/2020
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 730c023de61275d95fe594642149770af42b34b9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535757"
---
# <a name="quickstart-create-activity-log-alerts-on-service-notifications-using-an-arm-template"></a>Snabbstart: Skapa aktivitetsloggaviseringar i tjänstmeddelanden med hjälp av en ARM-mall

Den här artikeln visar hur du ställer in aktivitetsloggaviseringar för aviseringar om tjänstens hälsotillstånd med hjälp av en Azure Resource Manager mall (ARM-mall).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Service Health-meddelanden lagras i [Azure-aktivitetsloggen](../azure-monitor/essentials/platform-logs-overview.md). Med tanke på den eventuellt stora mängden information som lagras i aktivitetsloggen finns det ett separat användargränssnitt som gör det enklare att visa och konfigurera aviseringar om aviseringar om tjänstens hälsotillstånd.

Du kan få en avisering när Azure skickar service health-meddelanden till din Azure-prenumeration. Du kan konfigurera aviseringen baserat på:

- Klassen för service health-meddelanden (tjänstproblem, planerat underhåll, hälsorekommendationer).
- Prenumerationen som påverkas.
- De tjänster som påverkas.
- De regioner som påverkas.

> [!NOTE]
> Service Health-meddelanden skickar ingen avisering om resource health-händelser.

Du kan också konfigurera vem aviseringen ska skickas till:

- Välj en befintlig åtgärdsgrupp.
- Skapa en ny åtgärdsgrupp (som kan användas för framtida aviseringar).

Mer information om åtgärdsgrupper finns i [Skapa och hantera åtgärdsgrupper.](../azure-monitor/alerts/action-groups.md)

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Om du vill köra kommandona från den lokala datorn installerar du Azure CLI eller Azure PowerShell moduler. Mer information finns i [Installera Azure CLI och](/cli/azure/install-azure-cli) Installera [Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Granska mallen

Följande mall skapar en åtgärdsgrupp med ett e-postmål och aktiverar alla aviseringar om tjänstens hälsotillstånd för målprenumerationen. Spara den här mallen *somCreateServiceHealthAlert.jspå*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "type": "String",
      "defaultValue": "SubHealth"
    },
    "activityLogAlerts_name": {
      "type": "String",
      "defaultValue": "ServiceHealthActivityLogAlert"
    },
    "emailAddress": {
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
      "comments": "Service Health Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ],
      "tags": {},
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ServiceHealth"
            },
            {
              "field": "properties.incidentType",
              "equals": "Incident"
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
      }
    }
  ]
}
```

Mallen definierar två resurser:

- [Microsoft.Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft.Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>Distribuera mallen

Distribuera mallen med valfri standardmetod för att [distribuera en ARM-mall,](../azure-resource-manager/templates/deploy-portal.md) till exempel följande exempel med hjälp av CLI och PowerShell. Ersätt exempelvärdena för **Resursgrupp och** **emailAddress** med lämpliga värden för din miljö.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateServiceHealthAlert --resource-group my-resource-group --template-file CreateServiceHealthAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateServiceHealthAlert -ResourceGroupName my-resource-group -TemplateFile CreateServiceHealthAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>Verifiera distributionen

Kontrollera att arbetsytan har skapats med något av följande kommandon. Ersätt exempelvärdena för **Resursgrupp med** värdet som du använde ovan.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name ServiceHealthActivityLogAlert
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name ServiceHealthActivityLogAlert
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

- Lär dig [mer om metodtips för att konfigurera Azure Service Health aviseringar](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Lär dig att [konfigurera mobila push-meddelanden för Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Lär dig hur du [konfigurerar webhook-meddelanden för befintliga problemhanteringssystem.](service-health-alert-webhook-guide.md)
- Läs mer om [service health-meddelanden.](service-notifications.md)
- Läs mer om [begränsning av meddelandefrekvens.](../azure-monitor/alerts/alerts-rate-limiting.md)
- Granska [webhookschemat för aktivitetsloggaviseringar.](../azure-monitor/alerts/activity-log-alerts-webhook.md)
- Få en [översikt över aktivitetsloggaviseringar](../azure-monitor/alerts/alerts-overview.md)och lär dig hur du tar emot aviseringar.
- Läs mer om [åtgärdsgrupper.](../azure-monitor/alerts/action-groups.md)
