---
title: Skapa en NAT-gateway – Resource Manager-mall
titleSuffix: Azure Virtual Network NAT
description: Den här snabb starten visar hur du skapar en NAT-gateway med hjälp av Azure Resource Manager-mallen (ARM-mallen).
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: allensu
ms.custom: subject-armqs, devx-track-azurecli
ms.openlocfilehash: d1d4cf5a5e616db38885077e70add817f26d125a
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060696"
---
# <a name="quickstart-create-a-nat-gateway---arm-template"></a>Snabb start: skapa en NAT-gateway-ARM-mall

Kom igång med Virtual Network NAT med hjälp av en Azure Resource Manager-mall (ARM-mall). Den här mallen distribuerar ett virtuellt nätverk, en NAT-gateway-resurs och en virtuell Ubuntu-dator. Den virtuella Ubuntu-datorn distribueras till ett undernät som är associerat med NAT gateway-resursen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm).

Den här mallen har kon figurer ATS för att skapa en:

* Virtuellt nätverk
* NAT-gatewayresurs
* Ubuntu virtuell dator

Den virtuella Ubuntu-datorn distribueras till ett undernät som är associerat med NAT gateway-resursen.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json":::

Nio Azure-resurser definieras i mallen:

* **[Microsoft. Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)**: skapar en nätverks säkerhets grupp.
* **[Microsoft. Network/networkSecurityGroups/securityRules](/azure/templates/microsoft.network/networksecuritygroups/securityrules)**: skapar en säkerhets regel.
* **[Microsoft. Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)**: skapar en offentlig IP-adress.
* **[Microsoft. Network/publicIPPrefixes](/azure/templates/microsoft.network/publicipprefixes)**: skapar ett offentligt IP-prefix.
* **[Microsoft. Compute/virtualMachines](/azure/templates/Microsoft.Compute/virtualMachines)**: skapar en virtuell dator.
* **[Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)**: skapar ett virtuellt nätverk.
* **[Microsoft. Network/natGateways](/azure/templates/microsoft.network/natgateways)**: skapar en NAT-gateway-resurs.
* **[Microsoft. Network/virtualNetworks/subnets](/azure/templates/microsoft.network/virtualnetworks/subnets)**: skapar ett undernät för virtuellt nätverk.
* **[Microsoft. Network/NetworkInterfaces](/azure/templates/microsoft.network/networkinterfaces)**: skapar ett nätverks gränssnitt.

## <a name="deploy-the-template"></a>Distribuera mallen

**Azure CLI**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupNAT"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json"

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

**Azure PowerShell**

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. westcentralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json"

$resourceGroupName = "myResourceGroupNAT"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

**Azure-portalen**

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **resurs grupper** i det vänstra fönstret.

1. Välj den resurs grupp som du skapade i föregående avsnitt. Standard resurs grupps namnet är **myResourceGroupNAT**

1. Kontrol lera att följande resurser har skapats i resurs gruppen:

    ![Virtual Network NAT-resurs grupp](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>Rensa resurser

**Azure CLI**

När de inte längre behövs kan du använda kommandot [AZ Group Delete](/cli/azure/group#az-group-delete) för att ta bort resurs gruppen och alla resurser som ingår i.

```azurecli-interactive
  az group delete \
    --name myResourceGroupNAT
```

**Azure PowerShell**

När de inte längre behövs kan du använda kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen och alla resurser som ingår i.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupNAT
```

**Azure-portalen**

Ta bort resurs gruppen, NAT-gatewayen och alla relaterade resurser när de inte längre behövs. Välj den resurs grupp **myResourceGroupNAT** som innehåller NAT-gatewayen och välj sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en:

* NAT-gatewayresurs
* Virtuellt nätverk
* Ubuntu virtuell dator

Den virtuella datorn distribueras till ett virtuellt nätverks under nät som är associerat med NAT-gatewayen.

Om du vill veta mer om Virtual Network NAT och Azure Resource Manager fortsätter du till artiklarna nedan.

* Läs en [Översikt över Virtual Network NAT](nat-overview.md)
* Läs om [NAT-gatewayens resurs](nat-gateway-resource.md)
* Läs mer om [Azure Resource Manager](../azure-resource-manager/management/overview.md)
