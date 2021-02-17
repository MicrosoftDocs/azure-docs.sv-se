---
title: 'Snabb start: skapa en Azure-brandvägg och en brand Väggs princip – Resource Manager-mall'
description: I den här snabb starten distribuerar du en Azure-brandvägg och en brand Väggs princip.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 26d7336d60449db11122c9921a3a59807bd82911
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562205"
---
# <a name="quickstart-create-an-azure-firewall-and-a-firewall-policy---arm-template"></a>Snabb start: skapa en Azure-brandvägg och en brand Väggs princip – ARM-mall

I den här snabb starten använder du en Azure Resource Manager mall (ARM-mall) för att skapa en Azure-brandvägg och en brand Väggs princip. Brand Väggs principen har en program regel som tillåter anslutningar till `www.microsoft.com` och en regel som tillåter anslutningar till Windows Update med hjälp av **windowsupdate** -FQDN-taggen. En nätverks regel tillåter UDP-anslutningar till en tids server på 13.86.101.172.

IP-grupper används också i reglerna för att definiera **käll** -IP-adresser.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Information om Azure Firewall Manager finns i [Vad är Azure Firewall Manager?](overview.md).

Information om Azure-brandväggen finns i [Vad är Azure Firewall?](../firewall/overview.md).

Information om IP-grupper finns [i IP-grupper i Azure-brandväggen](../firewall/ip-groups.md).

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Granska mallen

Den här mallen skapar en säker virtuell hubb med Azure Firewall Manager, tillsammans med nödvändiga resurser för att stödja scenariot.

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/azuredeploy.json":::

Flera Azure-resurser definieras i mallen:

- [**Microsoft. Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft. Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft. Network/firewallPolicies/ruleCollectionGroups**](/azure/templates/microsoft.network/firewallPolicies/ruleCollectionGroups)
- [**Microsoft. Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)

## <a name="deploy-the-template"></a>Distribuera mallen

Distribuera ARM-mallen till Azure:

1. Välj **distribuera till Azure** för att logga in på Azure och öppna mallen. Mallen skapar en Azure-brandvägg, ett virtuellt WAN-nätverk och en virtuell hubb, nätverks infrastrukturen och två virtuella datorer.

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

2. I portalen, på sidan **skapa en brand vägg och FirewallPolicy med regler och Ipgroups** , skriver eller väljer du följande värden:
   - Prenumeration: Välj från befintliga prenumerationer.
   - Resurs grupp: Välj från befintliga resurs grupper eller Välj **Skapa ny** och välj **OK**.
   - Region: Välj en region.
   - Brand Väggs namn: Ange ett namn för brand väggen.

3. Välj **Granska + skapa** och välj sedan **Skapa**. Distributionen kan ta 10 minuter eller längre att slutföra.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

När distributionen är klar visas följande liknande resurser.

:::image type="content" source="media/quick-firewall-policy/qs-deployed-resources.png" alt-text="Distribuerade resurser":::

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med brand väggen tar du bort resurs gruppen. Detta tar bort brand väggen och alla relaterade resurser.

Anropa cmdleten om du vill ta bort resurs gruppen `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Översikt över Azure Firewall Manager-principer](policy-overview.md)
