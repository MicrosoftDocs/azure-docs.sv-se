---
title: 'Snabb start: Konfigurera flödes loggar för nätverks säkerhets grupper med hjälp av en Azure Resource Manager mall (ARM-mall)'
description: Lär dig hur du aktiverar flödes loggar för nätverks säkerhets grupper (NSG) genom programmering med hjälp av en Azure Resource Manager mall (ARM-mall) och Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the network security group flow logs by using an Azure Resource Manager template.
ms.service: network-watcher
ms.topic: quickstart
ms.date: 01/07/2021
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: bc075e5074fe39ad38e45235af932b40fef78fce
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521874"
---
# <a name="quickstart-configure-network-security-group-flow-logs-by-using-an-arm-template"></a>Snabb start: Konfigurera flödes loggar för nätverks säkerhets grupper med en ARM-mall

I den här snabb starten får du lära dig hur du aktiverar [flödes loggar för nätverks säkerhets grupper (NSG)](network-watcher-nsg-flow-logging-overview.md) med hjälp av en [Azure Resource Manager](../azure-resource-manager/management/overview.md) -mall (arm-mall) och Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Vi börjar med en översikt över egenskaperna för NSG flödes logg objekt. Vi tillhandahåller exempel mallar. Sedan använder vi en lokal Azure PowerShell-instans för att distribuera mallen.

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas i Azure Portal.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Den mall som vi använder i den här snabb starten är från [Azures snabb starts mallar](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

Dessa resurser definieras i mallen:

- [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft. Resources/Deployments](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>NSG flödes loggar objekt

Följande kod visar objektet NSG Flow-loggar och dess parametrar. Om du vill skapa en `Microsoft.Network/networkWatchers/flowLogs` resurs lägger du till den här koden i avsnittet resurser i mallen:

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

En fullständig översikt över objekt egenskaperna för NSG Flow-loggar finns i [Microsoft. Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs).

## <a name="create-your-template"></a>Skapa din mall

Om du använder ARM-mallar för första gången kan du läsa mer om ARM-mallar i följande artiklar:

- [Distribuera resurser med ARM-mallar och Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template-or-bicep-file)
- [Självstudie: skapa och distribuera din första ARM-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Följande exempel är en fullständig mall. Det är också den enklaste versionen av mallen. Exemplet innehåller de lägsta parametrar som skickas för att ställa in NSG flödes loggar. Fler exempel finns i översikts artikeln [Konfigurera NSG flödes loggar från en Azure Resource Manager-mall](network-watcher-nsg-flow-logging-azure-resource-manager.md).

### <a name="example"></a>Exempel

Följande mall aktiverar flödes loggar för en NSG och lagrar sedan loggarna i ett särskilt lagrings konto:

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
> - Resurs namnet använder formatet _ParentResource_ChildResource_. I vårt exempel är den överordnade resursen den regionala Azure Network Watcher-instansen:
>    - **Format**: NetworkWatcher_RegionName
>    - **Exempel**: NetworkWatcher_centraluseuap
> - `targetResourceId` är resurs-ID: t för mål-NSG.
> - `storageId` är resurs-ID: t för mål lagrings kontot.

## <a name="deploy-the-template"></a>Distribuera mallen

I den här självstudien förutsätter vi att du har en befintlig resurs grupp och en NSG som du kan aktivera flödes inloggning på.

Du kan spara alla exempel mallar som visas i den här artikeln lokalt som *azuredeploy.jspå*. Uppdatera egenskapsvärdena så att de pekar på giltiga resurser i din prenumeration.

Distribuera mallen genom att köra följande kommando i Azure PowerShell:

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> De här kommandona distribuerar en resurs till exempel NetworkWatcherRG-resurs gruppen och inte till resurs gruppen som innehåller NSG.

## <a name="validate-the-deployment"></a>Verifiera distributionen

Du har två alternativ för att se om distributionen är klar:

- PowerShell-konsolen visas `ProvisioningState` som `Succeeded` .
- Gå till [Portal sidan NSG Flow logs](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) för att bekräfta ändringarna.

Om det uppstod problem med distributionen läser du [Felsöka vanliga problem med Azure-distribution med Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort Azure-resurser med fullständig distributions läge. Om du vill ta bort en flödes loggar resurs anger du en distribution i slutfört läge utan att inkludera den resurs som du vill ta bort. Läs mer om [fullständigt distributions läge](../azure-resource-manager/templates/deployment-modes.md#complete-mode).

Du kan också inaktivera en NSG Flow-logg i Azure Portal:

1. Logga in på Azure-portalen.
1. Välj **Alla tjänster**. I rutan **filter** anger du **Network Watcher**. I Sök resultaten väljer du **Network Watcher**.
1. Under **loggar** väljer du **NSG flödes loggar**.
1. I listan över NSG: er väljer du den NSG som du vill inaktivera flödes loggar för.
1. Under **flödes loggar inställningar** väljer du **av**.
1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du aktiverar NSG Flow-loggar med hjälp av en ARM-mall. Sedan lär du dig hur du kan visualisera dina NSG-flödes data genom att använda något av följande alternativ:

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Verktyg med öppen källkod](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Azure-Trafikanalys](traffic-analytics.md)
