---
title: Skicka Azure aktivitets logg till Log Analytics arbets ytan med Azure Resource Manager-mall
description: Använd ARM-mallar för att skapa en Log Analytics arbets yta och en diagnostisk inställning för att skicka aktivitets loggen till Azure Monitor loggar.
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 56810cffcb9665810c452276be34e6924fd992b2
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033290"
---
# <a name="quickstart-send-azure-activity-log-to-log-analytics-workspace-using-an-arm-template"></a>Snabb start: skicka Azure aktivitets logg till Log Analytics arbets yta med en ARM-mall

Aktivitetsloggen är en plattformslogg i Azure som ger inblick i händelser på prenumerationsnivå. Bland annat loggas information som när en resurs ändras eller när en virtuell dator startas. Du kan visa aktivitets loggen i Azure Portal eller hämta poster med PowerShell och CLI. Den här snabb starten visar hur du använder Azure Resource Manager mallar (ARM-mallar) för att skapa en Log Analytics arbets yta och en diagnostisk inställning för att skicka aktivitets loggen till Azure Monitor loggar där du kan analysera den med hjälp av [logg frågor](../logs/log-query-overview.md) och aktivera andra funktioner som [logg aviseringar](../alerts/alerts-log-query.md) och [arbets böcker](../visualize/workbooks-overview.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Om du vill köra kommandona från den lokala datorn installerar du Azure CLI eller Azure PowerShell modulerna. Mer information finns i [Installera Azure CLI](/cli/azure/install-azure-cli) och [Installera Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="create-a-log-analytics-workspace"></a>Skapa en Log Analytics-arbetsyta

### <a name="review-the-template"></a>Granska mallen

Följande mall skapar en tom Log Analytics-arbetsyta. Spara den här mallen som *CreateWorkspace.jspå*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the workspace."
      }
    },
    "sku": {
      "type": "string",
      "defaultValue": "pergb2018",
      "allowedValues": [
        "pergb2018",
        "Free",
        "Standalone",
        "PerNode",
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Pricing tier: PerGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "australiacentral",
        "australiaeast",
        "australiasoutheast",
        "brazilsouth",
        "canadacentral",
        "centralindia",
        "centralus",
        "eastasia",
        "eastus",
        "eastus2",
        "francecentral",
        "japaneast",
        "koreacentral",
        "northcentralus",
        "northeurope",
        "southafricanorth",
        "southcentralus",
        "southeastasia",
        "switzerlandnorth",
        "switzerlandwest",
        "uksouth",
        "ukwest",
        "westcentralus",
        "westeurope",
        "westus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for the workspace."
      }
    },
    "retentionInDays": {
      "type": "int",
      "defaultValue": 120,
      "metadata": {
        "description": "Number of days to retain data."
      }
    },
    "resourcePermissions": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "true to use resource or workspace permissions. false to require workspace permissions."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-08-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "properties": {
        "sku": {
          "name": "[parameters('sku')]"
        },
        "retentionInDays": "[parameters('retentionInDays')]",
        "features": {
          "searchVersion": 1,
          "legacy": 0,
          "enableLogAccessUsingOnlyResourcePermissions": "[parameters('resourcePermissions')]"
        }
      }
    }
  ]
}
```

Den här mallen definierar en resurs:

- [Microsoft. OperationalInsights/arbets ytor](/azure/templates/microsoft.operationalinsights/workspaces)

### <a name="deploy-the-template"></a>Distribuera mallen

Distribuera mallen med valfri standard metod för att [distribuera en arm-mall](../../azure-resource-manager/templates/deploy-portal.md) som i följande exempel med CLI och PowerShell. Ersätt exempel värden för **resurs grupp**, **workspaceName** och **plats** med lämpliga värden för din miljö. Arbets ytans namn måste vara unikt bland alla Azure-prenumerationer.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create \
    --name CreateWorkspace \
    --resource-group my-resource-group \
    --template-file CreateWorkspace.json \
    --parameters workspaceName='my-workspace-01' location='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile CreateWorkspace.json -workspaceName my-workspace-01 -location eastus
```

---

### <a name="validate-the-deployment"></a>Verifiera distributionen

Kontrol lera att arbets ytan har skapats med något av följande kommandon. Ersätt exempel värden för **resurs grupp** och **workspaceName** med de värden som du använde ovan.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor log-analytics workspace show --resource-group my-workspace-01 --workspace-name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzOperationalInsightsWorkspace -Name my-workspace-01 -ResourceGroupName my-resource-group
```

---

## <a name="create-diagnostic-setting"></a>Skapa diagnostiskinställning

### <a name="review-the-template"></a>Granska mallen

Följande mall skapar en diagnostisk inställning som skickar aktivitets loggen till en Log Analytics-arbetsyta. Spara den här mallen som *CreateDiagnosticSetting.jspå*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "settingName": {
        "type": "String"
    },
    "workspaceId": {
        "type": "String"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/diagnosticSettings",
      "apiVersion": "2017-05-01-preview",
      "name": "[parameters('settingName')]",
      "dependsOn": [],
      "properties": {
        "workspaceId": "[parameters('workspaceId')]",
        "logs": [
          {
          "category": "Administrative",
          "enabled": true
          },
          {
          "category": "Alert",
          "enabled": true
          },
          {
          "category": "Autoscale",
          "enabled": true
          },
          {
          "category": "Policy",
          "enabled": true
          },
          {
          "category": "Recommendation",
          "enabled": true
          },
          {
          "category": "ResourceHealth",
          "enabled": true
          },
          {
          "category": "Security",
          "enabled": true
          },
          {
          "category": "ServiceHealth",
          "enabled": true
          }
        ]
      }
    }
  ]
}
```

Den här mallen definierar en resurs:

- [Microsoft. Insights/diagnosticSettings](/azure/templates/microsoft.insights/diagnosticsettings)

### <a name="deploy-the-template"></a>Distribuera mallen

Distribuera mallen med valfri standard metod för att [distribuera en arm-mall](../../azure-resource-manager/templates/deploy-portal.md) som i följande exempel med CLI och PowerShell. Ersätt exempel värden för **resurs grupp**, **workspaceName** och **plats** med lämpliga värden för din miljö. Arbets ytans namn måste vara unikt bland alla Azure-prenumerationer.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az deployment sub create --name CreateDiagnosticSetting --location eastus --template-file CreateDiagnosticSetting.json --parameters settingName='Send Activity log to workspace' workspaceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace-01'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzSubscriptionDeployment -Name CreateDiagnosticSetting -location eastus -TemplateFile CreateDiagnosticSetting.json -settingName "Send Activity log to workspace" -workspaceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace-01"
```
---

### <a name="validate-the-deployment"></a>Verifiera distributionen

Kontrol lera att den diagnostiska inställningen har skapats med något av följande kommandon. Ersätt exempel värdena för prenumerationen och inställnings namnet med de värden som du använde ovan.

> [!NOTE]
> Du kan för närvarande inte hämta diagnostiska inställningar för prenumerations nivå med PowerShell.

```azurecli
az monitor diagnostic-settings show --resource '/subscriptions/00000000-0000-0000-0000-000000000000' --name 'Send Activity log to workspace'
```

## <a name="generate-log-data"></a>Generera logg data

Endast nya aktivitets logg poster skickas till Log Analytics arbets ytan, så utför vissa åtgärder i prenumerationen som ska loggas, till exempel starta eller stoppa en virtuell dator eller skapa eller ändra en annan resurs. Du kan behöva vänta några minuter för att den diagnostiska inställningen ska skapas och för att data ska kunna skrivas till arbets ytan. Efter den här fördröjningen kommer alla händelser som skrivs till aktivitets loggen att skickas till arbets ytan inom några sekunder.

## <a name="retrieve-data-with-a-log-query"></a>Hämta data med en logg fråga

Använd Azure Portal för att använda Log Analytics för att hämta data från arbets ytan. I Azure Portal söker du efter och väljer **övervaka**.

![Azure Portal](media/quick-collect-activity-log/azure-portal-monitor.png)

Välj **loggar** på **Azure Monitor** -menyn. Stäng sidan **exempel frågor** . Om omfattningen inte är inställd på den arbets yta som du har skapat klickar du på **Välj omfång** och letar upp den.

![Log Analytics omfång](media/quick-collect-activity-log/log-analytics-scope.png)

I fönstret fråga skriver du `AzureActivity` och klickar på **Kör**. Det här är en enkel fråga som returnerar alla poster i tabellen *AzureActivity* som innehåller alla poster som skickas från aktivitets loggen.

![Exempelfråga](media/quick-collect-activity-log/query-01.png)

Expandera en av posterna för att visa dess detaljerade egenskaper.

![Expandera egenskaper](media/quick-collect-activity-log/expand-properties.png)

Testa en mer komplex fråga, till exempel `AzureActivity | summarize count() by CategoryValue` vilken som ger ett antal händelser sammanfattade efter kategori.

![Komplex fråga](media/quick-collect-activity-log/query-02.png)

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

I den här snabb starten konfigurerade du aktivitets loggen så att den skickas till en Log Analytics-arbetsyta. Nu kan du konfigurera andra data så att de samlas in i arbets ytan där du kan analysera dem tillsammans med hjälp av [logg frågor](../logs/log-query-overview.md) i Azure Monitor och utnyttja funktioner som [logg aviseringar](../alerts/alerts-log-query.md) och [arbets böcker](../visualize/workbooks-overview.md). Du bör börja med att samla in [resurs loggar](../essentials/resource-logs.md) från dina Azure-resurser som kompletterar data i aktivitets loggen och ger insyn i de åtgärder som utfördes i varje resurs.

> [!div class="nextstepaction"]
> [Samla in och analysera resurs loggar med Azure Monitor](../essentials/tutorial-resource-logs.md)