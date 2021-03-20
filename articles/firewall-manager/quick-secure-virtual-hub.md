---
title: 'Snabb start: säker virtuell hubb med Azure Firewall Manager – Resource Manager-mall'
description: I den här snabb starten får du lära dig hur du skyddar din virtuella hubb med Azure Firewall Manager.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 050dfa737ad04127f8c72158fbecb2e8e70e05c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89075349"
---
# <a name="quickstart-secure-your-virtual-hub-using-azure-firewall-manager---arm-template"></a>Snabb start: skydda din virtuella hubb med Azure Firewall Manager – ARM-mall

I den här snabb starten använder du en Azure Resource Manager mall (ARM-mall) för att skydda den virtuella hubben med hjälp av Azure Firewall Manager. Den distribuerade brand väggen har en program regel som tillåter anslutningar till `www.microsoft.com` . Två virtuella Windows Server 2019-datorer distribueras för att testa brand väggen. En hopp Server används för att ansluta till arbets belastnings servern. Från arbets belastnings servern kan du bara ansluta till `www.microsoft.com` .

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Mer information om Azure Firewall Manager finns i [Vad är Azure Firewall Manager?](overview.md).

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Granska mallen

Den här mallen skapar en säker virtuell hubb med Azure Firewall Manager, tillsammans med nödvändiga resurser för att stödja scenariot.

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/fwm-docs-qs/).

:::code language="json" source="~/quickstart-templates/fwm-docs-qs/azuredeploy.json":::

Flera Azure-resurser definieras i mallen:

- [**Microsoft. Network/virtualWans**](/azure/templates/microsoft.network/virtualWans)
- [**Microsoft. Network/virtualHubs**](/azure/templates/microsoft.network/virtualHubs)
- [**Microsoft. Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft. Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/routeTables**](/azure/templates/microsoft.network/routeTables)

## <a name="deploy-the-template"></a>Distribuera mallen

Distribuera ARM-mallen till Azure:

1. Välj **distribuera till Azure** för att logga in på Azure och öppna mallen. Mallen skapar en Azure-brandvägg, ett virtuellt WAN-nätverk och en virtuell hubb, nätverks infrastrukturen och två virtuella datorer.

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

2. I portalen på sidan **skyddade virtuella hubbar** skriver eller väljer du följande värden:
   - Prenumeration: Välj från befintliga prenumerationer 
   - Resurs grupp: Välj från befintliga resurs grupper eller Välj **Skapa ny** och välj **OK**.
   - Plats: Välj en plats
   - Admin-användar namn: Ange användar namn för administratörs användar kontot 
   - Administratörs lösen ord: Ange ett administratörs lösen ord eller en nyckel

3. Välj **Granska + skapa** och välj sedan **Skapa**. Distributionen kan ta 10 minuter eller längre att slutföra.

## <a name="validate-the-deployment"></a>Verifiera distributionen

Testa nu brand Väggs reglerna för att bekräfta att den fungerar som förväntat.

1. Från Azure Portal granskar du nätverks inställningarna för den virtuella **arbets belastnings-SRV-** datorn och noterar den privata IP-adressen.
2. Anslut ett fjärr skrivbord till den virtuella datorn för att **hoppa över SRV** och logga in. Därifrån öppnar du en fjärr skrivbords anslutning till den privata IP-adressen för **arbets belastningen** .

3. Öppna Internet Explorer och navigera till `www.microsoft.com`.
4. Välj **OK**  >  **Stäng** i Internet Explorers säkerhets aviseringar.

   Du bör se Microsofts hem sida.

5. Gå till `www.google.com`.

   Du bör blockeras av brandväggen.

Nu har du verifierat att brand Väggs reglerna fungerar:

- Du kan bläddra till en tillåten FQDN, men inte till andra.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med brand väggen tar du bort resurs gruppen. Detta tar bort brand väggen och alla relaterade resurser.

Anropa cmdleten om du vill ta bort resurs gruppen `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om providers för säkerhets partner](trusted-security-partners.md)
