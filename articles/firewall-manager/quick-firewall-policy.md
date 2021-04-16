---
title: 'Snabbstart: Skapa en Azure Firewall och en brandväggsprincip – Resource Manager mall'
description: I den här snabbstarten distribuerar du en Azure Firewall och en brandväggsprincip.
services: firewall-manager
author: vhorne
ms.author: victorh
ms.date: 02/17/2021
ms.topic: quickstart
ms.service: firewall-manager
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 43853d9e0b955167905af4777d533114a1d1f2ba
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529877"
---
# <a name="quickstart-create-an-azure-firewall-and-a-firewall-policy---arm-template"></a>Snabbstart: Skapa en Azure Firewall och en brandväggsprincip – ARM-mall

I den här snabbstarten använder du en Azure Resource Manager (ARM-mall) för att skapa en Azure Firewall och en brandväggsprincip. Brandväggsprincipen har en programregel som tillåter anslutningar till och en regel som tillåter anslutningar att Windows Update med hjälp av `www.microsoft.com` **taggen WindowsUpdate** FQDN. En nätverksregel tillåter UDP-anslutningar till en tidsserver på 13.86.101.172.

IP-grupper används också i reglerna för att definiera **källans** IP-adresser.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Mer information om Azure Firewall Manager finns i [Vad är Azure Firewall Manager?](overview.md).

Mer information om Azure Firewall finns i [Vad är Azure Firewall?](../firewall/overview.md).

Information om IP-grupper finns i [IP-grupper i Azure Firewall](../firewall/ip-groups.md).

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>Granska mallen

Den här mallen skapar ett virtuellt hubbnätverk, tillsammans med de resurser som krävs för att stödja scenariot.

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/azuredeploy.json":::

Flera Azure-resurser definieras i mallen:

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/firewallPolicies/ruleCollectionGroups**](/azure/templates/microsoft.network/firewallPolicies/ruleCollectionGroups)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)

## <a name="deploy-the-template"></a>Distribuera mallen

Distribuera ARM-mallen till Azure:

1. Välj **Distribuera till Azure** för att logga in på Azure och öppna mallen. Mallen skapar en Azure Firewall, ett virtuellt WAN och en virtuell hubb, nätverksinfrastrukturen och två virtuella datorer.

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

2. I portalen går du till sidan Skapa en brandvägg och FirewallPolicy med regler och **Ipgroups** och skriver eller väljer följande värden:
   - Prenumeration: Välj från befintliga prenumerationer.
   - Resursgrupp: Välj från befintliga resursgrupper eller **välj Skapa ny** och välj **OK.**
   - Region: Välj en region.
   - Brandväggsnamn: ange ett namn för brandväggen.

3. Välj **Granska + skapa** och välj sedan **Skapa**. Distributionen kan ta 10 minuter eller längre att slutföra.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

När distributionen är klar visas följande liknande resurser.

:::image type="content" source="media/quick-firewall-policy/qs-deployed-resources.png" alt-text="Distribuerade resurser":::

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen när du inte längre behöver de resurser som du skapade med brandväggen. Detta tar bort brandväggen och alla relaterade resurser.

Om du vill ta bort resursgruppen anropar du `Remove-AzResourceGroup` cmdleten :

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [översikt Azure Firewall Manager princip](policy-overview.md)
