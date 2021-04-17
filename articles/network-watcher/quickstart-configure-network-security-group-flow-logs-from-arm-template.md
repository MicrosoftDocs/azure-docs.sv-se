---
title: 'Snabbstart: Konfigurera flödesloggar för nätverkssäkerhetsgrupp med hjälp Azure Resource Manager mall (ARM-mall)'
description: Lär dig hur du aktiverar flödesloggar för nätverkssäkerhetsgrupp (NSG) programmatiskt med hjälp av en Azure Resource Manager mall (ARM-mall) och Azure PowerShell.
services: network-watcher
author: damendo
ms.author: damendo
ms.date: 01/07/2021
ms.topic: quickstart
ms.service: network-watcher
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: df0ccb5bf5ecd60d80526085983e35abf58e9966
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532445"
---
# <a name="quickstart-configure-network-security-group-flow-logs-by-using-an-arm-template"></a>Snabbstart: Konfigurera flödesloggar för nätverkssäkerhetsgrupp med hjälp av en ARM-mall

I den här snabbstarten lär du dig att aktivera flödesloggar för [nätverkssäkerhetsgrupp](../azure-resource-manager/management/overview.md) [(NSG)](network-watcher-nsg-flow-logging-overview.md) med hjälp av en Azure Resource Manager mall (ARM-mall) och Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Vi börjar med en översikt över egenskaperna för NSG-flödesloggobjektet. Vi tillhandahåller exempelmallar. Sedan använder vi en lokal Azure PowerShell för att distribuera mallen.

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas i Azure Portal.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Den mall som vi använder i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

Dessa resurser definieras i mallen:

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>NSG-flödesloggobjekt

Följande kod visar ett NSG-flödesloggobjekt och dess parametrar. Om du vill `Microsoft.Network/networkWatchers/flowLogs` skapa en resurs lägger du till den här koden i resursavsnittet i mallen:

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
        "enabled": "boolean",
        "workspaceResourceId": "string",
        "trafficAnalyticsInterval": "integer"
      },
      "retentionPolicy": {
        "days": "integer",
        "enabled": "boolean"
      },
      "format": {
        "type": "string",
        "version": "integer"
      }
    }
  }
}
```

En fullständig översikt över objektegenskaperna för NSG-flödesloggar finns [i Microsoft.Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs).

## <a name="create-your-template"></a>Skapa mallen

Om du använder ARM-mallar för första gången kan du läsa följande artiklar för att lära dig mer om ARM-mallar:

- [Distribuera resurser med ARM-mallar och Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template-or-bicep-file)
- [Självstudie: Skapa och distribuera din första ARM-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Följande exempel är en fullständig mall. Det är också den enklaste versionen av mallen. Exemplet innehåller de minsta parametrar som skickas för att konfigurera NSG-flödesloggar. Fler exempel finns i översiktsartikeln Konfigurera [NSG-flödesloggar från en Azure Resource Manager mall](network-watcher-nsg-flow-logging-azure-resource-manager.md).

### <a name="example"></a>Exempel

Följande mall aktiverar flödesloggar för en NSG och lagrar sedan loggarna i ett specifikt lagringskonto:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/<subscription Id>/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/<subscription Id>/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
        "enabled": true,
        "flowAnalyticsConfiguration": {},
        "retentionPolicy": {},
        "format": {}
      }
    }
  ]
}
```

> [!NOTE]
> - Resursnamnet använder formatet _ParentResource_ChildResource_. I vårt exempel är den överordnade resursen den regionala Azure Network Watcher instansen:
>    - **Format:** NetworkWatcher_RegionName
>    - **Exempel:** NetworkWatcher_centraluseuap
> - `targetResourceId` är resurs-ID för mål-NSG.
> - `storageId` är resurs-ID för mållagringskontot.

## <a name="deploy-the-template"></a>Distribuera mallen

Den här självstudien förutsätter att du har en befintlig resursgrupp och en NSG som du kan aktivera flödesloggning på.

Du kan spara någon av de exempelmallar som visas i den här artikeln lokalt som *azuredeploy.jspå*. Uppdatera egenskapsvärdena så att de pekar på giltiga resurser i din prenumeration.

Distribuera mallen genom att köra följande kommando i Azure PowerShell:

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> De här kommandona distribuerar en resurs till exempel resursgruppen NetworkWatcherRG och inte till resursgruppen som innehåller NSG:n.

## <a name="validate-the-deployment"></a>Verifiera distributionen

Du har två alternativ för att se om distributionen lyckades:

- PowerShell-konsolen visas `ProvisioningState` som `Succeeded` .
- Gå till [portalsidan för NSG-flödesloggar](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) för att bekräfta dina ändringar.

Om det uppstod problem med distributionen kan du [gå till Felsöka vanliga Azure-distributionsfel med Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort Azure-resurser med hjälp av fullständigt distributionsläge. Om du vill ta bort en flödesloggresurs anger du en distribution i fullständigt läge utan att inkludera den resurs som du vill ta bort. Läs mer om [fullständigt distributionsläge.](../azure-resource-manager/templates/deployment-modes.md#complete-mode)

Du kan också inaktivera en NSG-flödeslogg i Azure Portal:

1. Logga in på Azure-portalen.
1. Välj **Alla tjänster**. I rutan **Filter** anger du **network watcher**. I sökresultaten väljer du **Network Watcher**.
1. Under **Loggar** väljer du **NSG-flödesloggar.**
1. I listan över NSG:er väljer du den NSG som du vill inaktivera flödesloggar för.
1. Under **Flödeslogginställningar** väljer du **Av**.
1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du aktiverar NSG-flödesloggar med hjälp av en ARM-mall. Lär dig sedan att visualisera dina NSG-flödesdata med något av följande alternativ:

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Verktyg med öppen källkod](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Azure Trafikanalys](traffic-analytics.md)
