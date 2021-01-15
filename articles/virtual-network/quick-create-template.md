---
title: 'Snabb start: skapa ett virtuellt nätverk med en Resource Manager-mall'
titleSuffix: Azure Virtual Network
description: Lär dig hur du använder en Resource Manager-mall för att skapa ett virtuellt Azure-nätverk.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: bc0ac1a6e882f4197828bf79c7989c16b2eb16f7
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217676"
---
# <a name="quickstart-create-a-virtual-network---resource-manager-template"></a>Snabb start: skapa en mall för virtuellt nätverk – Resource Manager

I den här snabb starten får du lära dig hur du skapar ett virtuellt nätverk med två undernät med hjälp av Azure Resource Manager-mallen. Ett virtuellt nätverk är det grundläggande Bygg blocket för ditt privata nätverk i Azure. Den gör det möjligt för Azure-resurser, t. ex. virtuella datorer, att kommunicera på ett säkert sätt med varandra och med Internet.


[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Du kan också slutföra den här snabb starten med [Azure Portal](quick-create-portal.md), [Azure POWERSHELL](quick-create-powershell.md)eller [Azure CLI](quick-create-cli.md).

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten är från [Azure snabb starts mallar](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vnet-two-subnets/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-vnet-two-subnets/azuredeploy.json" range="001-96" highlight="56-92":::

Följande Azure-resurser har definierats i mallen:
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): skapa ett virtuellt Azure-nätverk.
-  [**Microsoft. Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets) – skapa ett undernät.

## <a name="deploy-the-template"></a>Distribuera mallen

Distribuera Resource Manager-mall till Azure:

1. Välj **distribuera till Azure** för att logga in på Azure och öppna mallen. Mallen skapar ett virtuellt nätverk med två undernät.

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-virtual-network-2vms-create%2Fazuredeploy.json)

2. I portalen, på sidan **skapa en Virtual Network med två undernät** , skriver eller väljer du följande värden:
   - **Resurs grupp**: Välj **Skapa ny**, Skriv ett namn för resurs gruppen och välj **OK**.
   - **Virtual Network namn**: Ange ett namn för det nya virtuella nätverket.
3. Välj **Granska + skapa** och välj sedan **Skapa**.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Utforska de resurser som har skapats med det virtuella nätverket.

Information om JSON-syntaxen och egenskaperna för ett virtuellt nätverk i en mall finns i [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks).

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med det virtuella nätverket tar du bort resurs gruppen. Detta tar bort det virtuella nätverket och alla relaterade resurser.

Anropa cmdleten om du vill ta bort resurs gruppen `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nästa steg
I den här snabb starten har du distribuerat ett virtuellt Azure-nätverk med två undernät. Om du vill veta mer om virtuella Azure-nätverk kan du fortsätta till självstudien för virtuella nätverk.

> [!div class="nextstepaction"]
> [Filtrering av nätverkstrafik](tutorial-filter-network-traffic.md)