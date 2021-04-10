---
title: Ta emot aktivitets logg aviseringar på Azure Service Notifications med Resource Manager-mall
description: Få ett meddelande via SMS, e-post eller webhook när Azure-tjänsten sker.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: 532fbae505e0bcaa6ab31a2e935362114537d134
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594951"
---
# <a name="quickstart-create-activity-log-alerts-on-service-notifications-using-an-arm-template"></a>Snabb start: skapa aktivitets logg aviseringar för tjänst meddelanden med en ARM-mall

Den här artikeln visar hur du konfigurerar aktivitets logg aviseringar för meddelanden om tjänstens hälso tillstånd med hjälp av en Azure Resource Manager mall (ARM-mall).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Meddelanden om tjänst hälsa lagras i [aktivitets loggen i Azure](../azure-monitor/essentials/platform-logs-overview.md). Med tanke på den stora mängden information som lagras i aktivitets loggen, finns det ett separat användar gränssnitt för att göra det enklare att visa och konfigurera aviseringar om meddelanden om tjänstens hälsa.

Du kan få en avisering när Azure skickar meddelanden om tjänst hälsa till din Azure-prenumeration. Du kan konfigurera aviseringen baserat på:

- Hälso tillstånds klassen för tjänsten (tjänste problem, planerat underhåll, hälso rekommendationer).
- Prenumerationen som påverkas.
- Den eller de tjänster som påverkas.
- De regioner som påverkas.

> [!NOTE]
> Meddelanden om tjänstens hälso tillstånd skickar ingen avisering om resurs hälso händelser.

Du kan också konfigurera vem aviseringen ska skickas till:

- Välj en befintlig åtgärds grupp.
- Skapa en ny åtgärds grupp (som kan användas för framtida aviseringar).

Mer information om åtgärds grupper finns i [skapa och hantera åtgärds grupper](../azure-monitor/alerts/action-groups.md).

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Om du vill köra kommandona från den lokala datorn installerar du Azure CLI eller Azure PowerShell modulerna. Mer information finns i [Installera Azure CLI](/cli/azure/install-azure-cli) och [Installera Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Granska mallen

Följande mall skapar en åtgärds grupp med ett e-postmål och aktiverar alla meddelanden om tjänst hälsa för mål prenumerationen. Spara den här mallen som *CreateServiceHealthAlert.jspå*.

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

- [Microsoft. Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft. Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>Distribuera mallen

Distribuera mallen med valfri standard metod för att [distribuera en arm-mall](../azure-resource-manager/templates/deploy-portal.md) som i följande exempel med CLI och PowerShell. Ersätt exempel värden för **resurs grupp** och **EmailAddress** med lämpliga värden för din miljö.

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

Kontrol lera att arbets ytan har skapats med något av följande kommandon. Ersätt exempel värden för **resurs grupp** med det värde som du använde ovan.

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

- Lär dig mer om [metod tips för att konfigurera Azure Service Health aviseringar](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Lär dig hur du [ställer in mobila push-meddelanden för Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Lär dig hur du [konfigurerar webhook-meddelanden för befintliga problem hanterings system](service-health-alert-webhook-guide.md).
- Läs mer om [meddelanden om tjänst hälsa](service-notifications.md).
- Lär dig mer om [begränsning av meddelande frekvens](../azure-monitor/alerts/alerts-rate-limiting.md).
- Granska [aktivitets logg aviseringens webhook-schema](../azure-monitor/alerts/activity-log-alerts-webhook.md).
- Få en [Översikt över aktivitets logg aviseringar](../azure-monitor/alerts/alerts-overview.md)och lär dig hur du tar emot aviseringar.
- Läs mer om [Åtgärds grupper](../azure-monitor/alerts/action-groups.md).
