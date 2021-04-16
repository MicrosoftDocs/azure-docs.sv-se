---
title: 'Snabbstart: Skydda virtuell hubb med hjälp Azure Firewall Manager – Resource Manager mall'
description: I den här snabbstarten lär du dig att skydda din virtuella hubb med hjälp av Azure Firewall Manager.
services: firewall-manager
author: vhorne
ms.author: victorh
ms.date: 08/28/2020
ms.topic: quickstart
ms.service: firewall-manager
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 88b961f269c89a1e19c0fd4419ffdc33706423de
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529643"
---
# <a name="quickstart-secure-your-virtual-hub-using-azure-firewall-manager---arm-template"></a>Snabbstart: Skydda din virtuella hubb med hjälp Azure Firewall Manager – ARM-mall

I den här snabbstarten använder du en Azure Resource Manager mall (ARM-mall) för att skydda din virtuella hubb med Azure Firewall Manager. Den distribuerade brandväggen har en programregel som tillåter anslutningar till `www.microsoft.com` . Två virtuella Windows Server 2019-datorer distribueras för att testa brandväggen. En jump-server används för att ansluta till arbetsbelastningsservern. Från arbetsbelastningsservern kan du bara ansluta till `www.microsoft.com` .

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Mer information om Azure Firewall Manager finns i [Vad är Azure Firewall Manager?](overview.md).

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>Granska mallen

Den här mallen skapar en skyddad virtuell hubb med Azure Firewall Manager, tillsammans med de resurser som krävs för scenariot.

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/fwm-docs-qs/).

:::code language="json" source="~/quickstart-templates/fwm-docs-qs/azuredeploy.json":::

Flera Azure-resurser definieras i mallen:

- [**Microsoft.Network/virtualWans**](/azure/templates/microsoft.network/virtualWans)
- [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualHubs)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

## <a name="deploy-the-template"></a>Distribuera mallen

Distribuera ARM-mallen till Azure:

1. Välj **Distribuera till Azure** för att logga in på Azure och öppna mallen. Mallen skapar en Azure Firewall, ett virtuellt WAN och en virtuell hubb, nätverksinfrastrukturen och två virtuella datorer.

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

2. I portalen går du till **sidan Skyddade virtuella hubbar** och skriver eller väljer följande värden:
   - Prenumeration: Välj från befintliga prenumerationer 
   - Resursgrupp: Välj från befintliga resursgrupper eller **välj Skapa ny** och välj **OK.**
   - Plats: Välj en plats
   - Administratörsanvändarnamn: Ange användarnamn för administratörsanvändarkontot 
   - Administratörslösenord: Ange ett administratörslösenord eller en administratörsnyckel

3. Välj **Granska + skapa** och välj sedan **Skapa**. Distributionen kan ta 10 minuter eller längre att slutföra.

## <a name="validate-the-deployment"></a>Verifiera distributionen

Testa nu brandväggsreglerna för att bekräfta att den fungerar som förväntat.

1. Granska nätverksinställningarna för den virtuella datorn **Workload-Srv** från Azure Portal och anteckna den privata IP-adressen.
2. Anslut ett fjärrskrivbord till **den virtuella Jump-Srv-datorn** och logga in. Därifrån öppnar du en fjärrskrivbordsanslutning till den privata **IP-adressen workload-Srv.**

3. Öppna Internet Explorer och navigera till `www.microsoft.com`.
4. Välj **OK**  >  **Stäng** på Internet Explorer säkerhetsaviseringar.

   Du bör se Microsofts startsida.

5. Gå till `www.google.com`.

   Du bör blockeras av brandväggen.

Så nu har du verifierat att brandväggsreglerna fungerar:

- Du kan bläddra till en tillåten FQDN, men inte till andra.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen när du inte längre behöver de resurser som du skapade med brandväggen. Detta tar bort brandväggen och alla relaterade resurser.

Om du vill ta bort resursgruppen anropar du `Remove-AzResourceGroup` cmdleten :

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om säkerhetspartnerproviders](trusted-security-partners.md)
