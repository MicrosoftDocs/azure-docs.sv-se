---
title: 'Snabb start: direkt webb trafik med hjälp av en Resource Manager-mall'
titleSuffix: Azure Application Gateway
description: I den här snabb starten får du lära dig hur du använder en Resource Manager-mall för att skapa en Azure Application-Gateway som dirigerar webb trafik till virtuella datorer i en backend-pool.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 01/20/2021
ms.author: victorh
ms.custom: mvc, subject-armqs
ms.openlocfilehash: 05e7cd8d7018759a3c8670a610b4c98ac0a86a7a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98660063"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---arm-template"></a>Snabb start: direkt webb trafik med Azure Application Gateway-ARM-mall

I den här snabb starten använder du en Azure Resource Manager mall (ARM-mall) för att skapa en Azure Application Gateway. Sedan testar du programgatewayen för att kontrol lera att den fungerar som den ska.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Du kan också slutföra den här snabb starten med [Azure Portal](quick-create-portal.md), [Azure POWERSHELL](quick-create-powershell.md)eller [Azure CLI](quick-create-cli.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Granska mallen

För enkelhetens skull skapar den här mallen en enkel installation med en offentlig frontend-IP, en grundläggande lyssnare som är värd för en enda plats på programgatewayen, en grundläggande regel för routning av begäran och två virtuella datorer i backend-poolen.

Mallen som används i den här snabb starten är från [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/ag-docs-qs/)

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json":::

Flera Azure-resurser definieras i mallen:

- [**Microsoft. Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses) : ett för programgatewayen och två för de virtuella datorerna.
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : två virtuella datorer
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : två för de virtuella datorerna
- [**Microsoft. Compute/virtualMachine/Extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : så här konfigurerar du IIS och webb sidor

## <a name="deploy-the-template"></a>Distribuera mallen

Distribuera ARM-mallen till Azure:

1. Välj **distribuera till Azure** för att logga in på Azure och öppna mallen. Mallen skapar en Programgateway, nätverks infrastrukturen och två virtuella datorer i den backend-pool som kör IIS.

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

2. Välj eller skapa en resurs grupp, ange den virtuella datorns administratörs användar namn och lösen ord.
3. Välj **Granska + skapa** och välj sedan **skapa**.

   Distributionen kan ta 20 minuter eller längre att slutföra.

## <a name="validate-the-deployment"></a>Verifiera distributionen

Även om IIS inte krävs för att skapa programgatewayen, installeras den för att kontrol lera om Azure har skapat programgatewayen. Använd IIS för att testa programgatewayen:

1. Hitta den offentliga IP-adressen för Application Gateway på sidan **Översikt** . ![ Registrera den offentliga IP-adressen för Application Gateway ](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) eller Välj **alla resurser**, ange *myAGPublicIPAddress* i sökrutan och välj sedan den i Sök resultaten. Azure visar den offentliga IP-adressen på sidan **Översikt**.
2. Kopiera den offentliga IP-adressen och klistra in den i adress fältet i webbläsaren för att söka efter IP-adressen.
3. Kontrol lera svaret. Ett giltigt svar verifierar att Application Gateway har skapats och kan ansluta till Server delen.

   ![Testa programgatewayen](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Uppdatera webbläsaren flera gånger och du bör se anslutningar till både myVM1 och myVM2.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med Application Gateway, tar du bort resurs gruppen. Detta tar bort programgatewayen och alla relaterade resurser.

Anropa cmdleten om du vill ta bort resurs gruppen `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera webbtrafik med en programgateway som använder Azure CLI](./tutorial-manage-web-traffic-cli.md)
