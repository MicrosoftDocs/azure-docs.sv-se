---
title: 'Snabbstart: Dirigera webbtrafik med en Resource Manager mall'
titleSuffix: Azure Application Gateway
description: I den här snabbstarten får du lära dig hur du använder en Resource Manager-mall för att skapa en Azure Application Gateway som dirigerar webbtrafik till virtuella datorer i en serverdelspool.
services: application-gateway
author: vhorne
ms.author: victorh
ms.date: 01/20/2021
ms.topic: quickstart
ms.service: application-gateway
ms.custom:
- mvc
- subject-armqs
- mode-arm
ms.openlocfilehash: dd100361ba5d4ff175e340ced782999e52c720c4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538434"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---arm-template"></a>Snabbstart: Dirigera webbtrafik med Azure Application Gateway – ARM-mall

I den här snabbstarten använder du en Azure Resource Manager (ARM-mall) för att skapa en Azure Application Gateway. Sedan testar du programgatewayen för att kontrollera att den fungerar korrekt.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Du kan också slutföra den här snabbstarten [med hjälp Azure Portal,](quick-create-portal.md) [Azure PowerShell](quick-create-powershell.md)eller [Azure CLI.](quick-create-cli.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>Granska mallen

För enkelhetens skull skapar den här mallen en enkel konfiguration med en offentlig IP-adress i frontend, en grundläggande lyssnare som är värd för en enda plats på programgatewayen, en grundläggande regel för routning av förfrågningar och två virtuella datorer i serverpoolen.

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/ag-docs-qs/)

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json":::

Flera Azure-resurser definieras i mallen:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/publicIPAddresses:**](/azure/templates/microsoft.network/publicipaddresses) en för programgatewayen och två för de virtuella datorerna.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines:**](/azure/templates/microsoft.compute/virtualmachines) två virtuella datorer
- [**Microsoft.Network/networkInterfaces:**](/azure/templates/microsoft.network/networkinterfaces) två för de virtuella datorerna
- [**Microsoft.Compute/virtualMachine/extensions:**](/azure/templates/microsoft.compute/virtualmachines/extensions) för att konfigurera IIS och webbsidor

## <a name="deploy-the-template"></a>Distribuera mallen

Distribuera ARM-mallen till Azure:

1. Välj **Distribuera till Azure** för att logga in på Azure och öppna mallen. Mallen skapar en programgateway, nätverksinfrastrukturen och två virtuella datorer i serverpoolen som kör IIS.

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

2. Välj eller skapa resursgruppen och ange användarnamn och lösenord för den virtuella datorns administratör.
3. Välj **Granska + skapa** och välj sedan **Skapa.**

   Distributionen kan ta 20 minuter eller längre att slutföra.

## <a name="validate-the-deployment"></a>Verifiera distributionen

Även om IIS inte krävs för att skapa programgatewayen, installeras det för att kontrollera om Azure har skapat programgatewayen. Använd IIS för att testa programgatewayen:

1. Hitta den offentliga IP-adressen för programgatewayen på **sidan** Översikt. ![ Registrera programgatewayens offentliga IP-adress Eller så kan du välja Alla resurser, ange ](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) *myAGPublicIPAddress* i sökrutan och sedan välja den i sökresultatet.  Azure visar den offentliga IP-adressen på sidan **Översikt**.
2. Kopiera den offentliga IP-adressen och klistra sedan in den i webbläsarens adressfält för att bläddra igenom IP-adressen.
3. Kontrollera svaret. Ett giltigt svar verifierar att programgatewayen har skapats och kan ansluta till backend-datorn.

   ![Testa programgatewayen](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Uppdatera webbläsaren flera gånger så bör du se anslutningar till både myVM1 och myVM2.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen när du inte längre behöver de resurser som du skapade med programgatewayen. Detta tar bort programgatewayen och alla relaterade resurser.

Om du vill ta bort resursgruppen anropar du `Remove-AzResourceGroup` cmdleten :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera webbtrafik med en programgateway som använder Azure CLI](./tutorial-manage-web-traffic-cli.md)
