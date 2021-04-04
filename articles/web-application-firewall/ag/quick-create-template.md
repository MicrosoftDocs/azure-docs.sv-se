---
title: 'Snabb start: skapa en Azure WAF v2 på Application Gateway-Azure Resource Manager mall'
titleSuffix: Azure Application Gateway
description: Lär dig hur du använder en Azure Resource Manager snabb starts mall (ARM-mall) för att skapa en brand vägg för webbaserade program v2 på Azure Application Gateway.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: quickstart
ms.date: 09/16/2020
ms.author: victorh
ms.custom: subject-armqs
ms.openlocfilehash: 4c5d5c4cafee9402e2afd7ddc047fd3b68e84540
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92091329"
---
# <a name="quickstart-create-an-azure-waf-v2-on-application-gateway-using-an-arm-template"></a>Snabb start: skapa en Azure WAF v2 på Application Gateway med en ARM-mall

I den här snabb starten använder du en Azure Resource Manager mall (ARM-mall) för att skapa en Azure Web Application-brandvägg v2 på Application Gateway.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Granska mallen

Den här mallen skapar en enkel WebApplication-brandvägg v2 på Azure Application Gateway. Detta omfattar en offentlig IP-adress för IP-adress, HTTP-inställningar, en regel med en grundläggande lyssnare på port 80 och en backend-pool. En WAF-princip med en anpassad regel skapas för att blockera trafik till backend-poolen baserat på en matchnings typ för IP-adress.

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/ag-docs-wafv2/).

:::code language="json" source="~/quickstart-templates/ag-docs-wafv2/azuredeploy.json":::

Flera Azure-resurser definieras i mallen:

- [**Microsoft. Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies**](/azure/templates/microsoft.network/ApplicationGatewayWebApplicationFirewallPolicies)
- [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses) : ett för programgatewayen och två för de virtuella datorerna.
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : två virtuella datorer
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : två för de virtuella datorerna
- [**Microsoft. Compute/virtualMachine/Extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : så här konfigurerar du IIS och webb sidor

## <a name="deploy-the-template"></a>Distribuera mallen

Distribuera ARM-mallen till Azure:

1. Välj **distribuera till Azure** för att logga in på Azure och öppna mallen. Mallen skapar en Programgateway, nätverks infrastrukturen och två virtuella datorer i den backend-pool som kör IIS.

   [![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

2. Välj eller skapa en resurs grupp.
3. Välj **Jag accepterar de villkor som anges ovan** och välj sedan **köp**. Distributionen kan ta 10 minuter eller längre att slutföra.

## <a name="validate-the-deployment"></a>Verifiera distributionen

Även om IIS inte krävs för att skapa programgatewayen, installeras den på backend-servrarna för att kontrol lera om Azure har skapat en WAF v2 på Application Gateway.

Använd IIS för att testa programgatewayen:

1. Hitta den offentliga IP-adressen för Application Gateway på sidan **Översikt** . ![ Registrera den offentliga IP-adressen för Application Gateway ](../../application-gateway/media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) eller Välj **alla resurser**, ange *myAGPublicIPAddress* i sökrutan och välj sedan den i Sök resultaten. Azure visar den offentliga IP-adressen på sidan **Översikt**.
2. Kopiera den offentliga IP-adressen och klistra in den i adress fältet i webbläsaren för att söka efter IP-adressen.
3. Kontrol lera svaret. Ett svar på **403 förbjuden** verifierar att WAF har skapats och blockerar anslutningar till backend-poolen.
4. Ändra den anpassade regeln så att den **tillåter trafik**.
  Kör följande Azure PowerShell skript och ersätt resurs gruppens namn:
   ```azurepowershell
   $rg = "<your resource group name>"
   $AppGW = Get-AzApplicationGateway -Name myAppGateway -ResourceGroupName $rg
   $pol = Get-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg
   $pol[0].customrules[0].action = "allow"
   $rule = $pol.CustomRules
   Set-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg -CustomRule $rule
   $AppGW.FirewallPolicy = $pol
   Set-AzApplicationGateway -ApplicationGateway $AppGW
   ```

   Uppdatera webbläsaren flera gånger och du bör se anslutningar till både myVM1 och myVM2.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med Application Gateway, tar du bort resurs gruppen. Detta tar bort programgatewayen och alla relaterade resurser.

Anropa cmdleten om du vill ta bort resurs gruppen `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: skapa en Programgateway med en brand vägg för webbaserade program med hjälp av Azure Portal](application-gateway-web-application-firewall-portal.md)