---
title: Skapa och aktivera en Azure DDoS Protection-plan med hjälp av en Azure Resource Manager-mall (ARM-mall).
description: Lär dig hur du skapar och aktiverar en Azure DDoS Protection-plan med hjälp av en Azure Resource Manager-mall (ARM-mall).
services: ddos-protection
documentationcenter: na
author: mumian
ms.service: ddos-protection
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.custom: subject-armqs
ms.author: jgao
ms.date: 01/14/2021
ms.openlocfilehash: 75d6c484a0f1d6325aaa7894d8902ff78cadbd74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99092508"
---
# <a name="quickstart-create-an-azure-ddos-protection-standard-using-arm-template"></a>Snabb start: skapa en Azure DDoS Protection standard med ARM-mall

I den här snabb starten beskrivs hur du använder en Azure Resource Manager mall (ARM-mall) för att skapa en DDoS-skydds plan (distributed denial of Service) och ett virtuellt nätverk (VNet), och sedan aktiverar skydds planen för VNet. En Azure DDoS Protection standard plan definierar en uppsättning virtuella nätverk som har DDoS-skydd aktiverat över prenumerationer. Du kan konfigurera en DDoS skydds plan för din organisation och länka virtuella nätverk från flera prenumerationer till samma plan.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-create-and-enable-ddos-protection-plans).

:::code language="json" source="~/quickstart-templates/101-create-and-enable-ddos-protection-plans/azuredeploy.json":::

Mallen definierar två resurser:

- [Microsoft. Network/ddosProtectionPlans](/azure/templates/microsoft.network/ddosprotectionplans)
- [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)

## <a name="deploy-the-template"></a>Distribuera mallen

I det här exemplet skapar mallen en ny resurs grupp, en DDoS skydds plan och ett VNet.

1. Om du vill logga in på Azure och öppna mallen väljer du knappen **distribuera till Azure** .

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

1. Ange värdena för att skapa en ny resurs grupp, DDoS, skydds plan och VNet-namn.

    :::image type="content" source="media/manage-ddos-protection-template/ddos-template.png" alt-text="DDoS snabb starts mall.":::

    - **Prenumeration**: namnet på den Azure-prenumeration där resurserna ska distribueras.
    - **Resurs grupp**: Välj en befintlig resurs grupp eller skapa en ny resurs grupp.
    - **Region**: den region där resurs gruppen distribueras, t. ex. USA, östra.
    - **DDoS skydds plan namn**: namnet på den nya DDoS-skydds planen.
    - **Virtual Network namn**: skapar ett namn för det nya virtuella nätverket.
    - **Plats**: funktion som använder samma region som resurs gruppen för resurs distribution.
    - **VNet-** adressprefix: Använd standardvärdet eller ange din VNet-adress.
    - **Undernätsprefixet**: Använd standardvärdet eller ange ditt VNet-undernät.
    - **DDoS Protection Plan aktive rad**: standard är `true` att aktivera DDoS-skydds planen.

1. Välj **Granska + skapa**.
1. Kontrol lera att verifieringen av mallen har godkänts och välj **skapa** för att starta distributionen.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Kopiera Azure CLI-eller Azure PowerShell-kommandot genom att välja knappen **Kopiera** . Knappen **prova** öppnas Azure Cloud Shell att köra kommandot.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az network ddos-protection show \
  --resource-group MyResourceGroup \
  --name MyDdosProtectionPlan
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName 'MyResourceGroup' -Name 'MyDdosProtectionPlan'
```

---

Utdata visar de nya resurserna.

# <a name="cli"></a>[CLI](#tab/CLI)

```Output
{
  "etag": "W/\"abcdefgh-1111-2222-bbbb-987654321098\"",
  "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan",
  "location": "eastus",
  "name": "MyDdosProtectionPlan",
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "resourceGuid": null,
  "tags": null,
  "type": "Microsoft.Network/ddosProtectionPlans",
  "virtualNetworks": [
    {
      "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : MyDdosProtectionPlan
Id                : /subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan
Etag              : W/"abcdefgh-1111-2222-bbbb-987654321098"
ProvisioningState : Succeeded
VirtualNetworks   : [
                      {
                        "Id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet"
                      }
                    ]
```

---

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar kan du ta bort resurserna. Kommandot tar bort resurs gruppen och alla resurser den innehåller.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az group delete --name MyResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'MyResourceGroup'
```

---

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig att visa och konfigurera telemetri för din DDoS-skydds plan fortsätter du till självstudierna.

> [!div class="nextstepaction"]
> [Visa och konfigurera telemetri för DDoS-skydd](telemetry.md)
