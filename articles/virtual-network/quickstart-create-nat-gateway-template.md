---
title: Skapa en NAT-gateway – Resource Manager mall
titleSuffix: Azure Virtual Network NAT
description: Den här snabbstarten visar hur du skapar en NAT-gateway med hjälp Azure Resource Manager mallen (ARM-mall).
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
ms.openlocfilehash: ea214cb98e1f75daccf783333a67c6d1b0eacfeb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776615"
---
# <a name="quickstart-create-a-nat-gateway---arm-template"></a>Snabbstart: Skapa en NAT-gateway – ARM-mall

Kom igång med Virtual Network NAT med hjälp av en Azure Resource Manager mall (ARM-mall). Den här mallen distribuerar ett virtuellt nätverk, en NAT-gatewayresurs och en virtuell Ubuntu-dator. Den virtuella Ubuntu-datorn distribueras till ett undernät som är associerat med NAT-gatewayresursen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm).

Den här mallen är konfigurerad för att skapa en:

* Virtuellt nätverk
* NAT-gatewayresurs
* Virtuell Ubuntu-dator

Den virtuella Ubuntu-datorn distribueras till ett undernät som är associerat med NAT-gatewayresursen.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json":::

Nio Azure-resurser definieras i mallen:

* **[Microsoft.Network/networkSecurityGroups:](/azure/templates/microsoft.network/networksecuritygroups)** Skapar en nätverkssäkerhetsgrupp.
* **[Microsoft.Network/networkSecurityGroups/securityRules:](/azure/templates/microsoft.network/networksecuritygroups/securityrules)** Skapar en säkerhetsregel.
* **[Microsoft.Network/publicIPAddresses:](/azure/templates/microsoft.network/publicipaddresses)** Skapar en offentlig IP-adress.
* **[Microsoft.Network/publicIPPrefixes:](/azure/templates/microsoft.network/publicipprefixes)** Skapar ett offentligt IP-prefix.
* **[Microsoft.Compute/virtualMachines:](/azure/templates/Microsoft.Compute/virtualMachines)** Skapar en virtuell dator.
* **[Microsoft.Network/virtualNetworks:](/azure/templates/microsoft.network/virtualnetworks)** Skapar ett virtuellt nätverk.
* **[Microsoft.Network/natGateways:](/azure/templates/microsoft.network/natgateways)** Skapar en NAT-gatewayresurs.
* **[Microsoft.Network/virtualNetworks/subnets:](/azure/templates/microsoft.network/virtualnetworks/subnets)** Skapar ett undernät för ett virtuellt nätverk.
* **[Microsoft.Network/networkinterfaces:](/azure/templates/microsoft.network/networkinterfaces)** Skapar ett nätverksgränssnitt.

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

1. Välj **Resursgrupper** i det vänstra fönstret.

1. Välj den resursgrupp som du skapade i föregående avsnitt. Standardnamnet för resursgruppen är **myResourceGroupNAT**

1. Kontrollera att följande resurser har skapats i resursgruppen:

    ![Virtual Network NAT resursgrupp](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>Rensa resurser

**Azure CLI**

När den inte längre behövs kan du använda [kommandot az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen och alla resurser som finns i den.

```azurecli-interactive
  az group delete \
    --name myResourceGroupNAT
```

**Azure PowerShell**

När resursgruppen inte längre behövs kan du använda kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen och alla resurser som finns i den.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupNAT
```

**Azure-portalen**

Ta bort resursgruppen, NAT-gatewayen och alla relaterade resurser när de inte längre behövs. Välj den resursgrupp **myResourceGroupNAT** som innehåller NAT-gatewayen och välj sedan Ta **bort.**

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en:

* NAT-gatewayresurs
* Virtuellt nätverk
* Virtuell Ubuntu-dator

Den virtuella datorn distribueras till ett undernät för virtuellt nätverk som är associerat med NAT-gatewayen.

Om du vill veta Virtual Network NAT och Azure Resource Manager kan du fortsätta till artiklarna nedan.

* Läs en [översikt över Virtual Network NAT](nat-overview.md)
* Läs mer om [NAT Gateway resurs](nat-gateway-resource.md)
* Läs mer om [Azure Resource Manager](../azure-resource-manager/management/overview.md)
