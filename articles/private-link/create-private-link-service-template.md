---
title: 'Snabb start: skapa en privat länk-tjänst i en privat Azure-länk'
description: I den här snabb starten använder du en Azure Resource Manager mall (ARM-mall) för att skapa en privat länk-tjänst.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: 34993ad3d3d0494f89bd264a8b7194f52129ad7c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555065"
---
# <a name="quickstart-create-a-private-link-service-by-using-an-arm-template"></a>Snabb start: skapa en privat länk-tjänst med en ARM-mall

I den här snabb starten använder du en Azure Resource Manager mall (ARM-mall) för att skapa en privat länk-tjänst.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Du kan också slutföra den här snabb starten genom att använda [Azure Portal](create-private-link-service-portal.md), [Azure POWERSHELL](create-private-link-service-powershell.md)eller [Azure CLI](create-private-link-service-cli.md).

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Du behöver ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Granska mallen

Den här mallen skapar en privat länk-tjänst.

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-privatelink-service/).

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json":::

Flera Azure-resurser definieras i mallen:

- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): det finns ett virtuellt nätverk för varje virtuell dator.
- [**Microsoft. Network/belastningsutjämnare**](/azure/templates/microsoft.network/loadBalancers): belastningsutjämnaren som exponerar de virtuella datorer som är värdar för tjänsten.
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): det finns två nätverks gränssnitt, ett för varje virtuell dator.
- [**Microsoft. Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): det finns två virtuella datorer, en som är värd för tjänsten och en som testar anslutningen till den privata slut punkten.
- [**Microsoft. Compute/virtualMachines/Extensions**](/azure/templates/Microsoft.Compute/virtualMachines/extensions): det tillägg som installerar en webb server.
- [**Microsoft. Network/privateLinkServices**](/azure/templates/microsoft.network/privateLinkServices): tjänsten för privata Länkar för att exponera tjänsten.
- [**Microsoft. Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses): det finns två offentliga IP-adresser, en för varje virtuell dator.
- [**Microsoft. Network/privateendpoints**](/azure/templates/microsoft.network/privateendpoints): den privata slut punkten för att få åtkomst till tjänsten.

## <a name="deploy-the-template"></a>Distribuera mallen

Så här distribuerar du ARM-mallen till Azure:

1. Om du vill logga in på Azure och öppna mallen väljer du **distribuera till Azure**. Mallen skapar en virtuell dator, standard belastningsutjämnare, privat länk tjänst, privat slut punkt, nätverk och en virtuell dator som ska verifieras.

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

2. Välj eller skapa en resurs grupp.
3. Ange administratörs användar namn och lösen ord för den virtuella datorn.
4. Läs villkors satsen. Om du samtycker väljer du **Jag accepterar villkoren som anges ovan**  >  **köpet**.

## <a name="validate-the-deployment"></a>Verifiera distributionen

> [!NOTE]
> ARM-mallen genererar ett unikt namn för den virtuella datorns myConsumerVm<b>{UniqueID}</b> -resurs. Ersätt ditt genererade värde för **{UniqueID}**.

### <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

Anslut till VM- _myConsumerVm {UniqueID}_ från Internet på följande sätt:

1.  I portalens Sök fält anger du _myConsumerVm {UniqueID}_.

2.  Välj **Anslut**. **Anslut till den virtuella datorn** öppnas.

3.  Välj **Hämta RDP-fil**. Azure skapar en Remote Desktop Protocol-fil (_. RDP_) och laddar ned den till datorn.

4.  Öppna den nedladdade RDP-filen.

    a. Välj **Anslut** om du uppmanas att göra det.

    b. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.
    
    > [!NOTE]
    > Du kan behöva välja **fler alternativ**  >  **Använd ett annat konto** för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn.

5.  Välj **OK**.

6.  Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

7.  När den virtuella datorns skriv bord visas kan du minimera det och gå tillbaka till det lokala Skriv bordet.

### <a name="access-the-http-service-privately-from-the-vm"></a>Få åtkomst till http-tjänsten privat från den virtuella datorn

Så här ansluter du till http-tjänsten från den virtuella datorn med hjälp av den privata slut punkten.

1.  Gå till fjärr skrivbordet för _myConsumerVm {UniqueID}_.
2.  Öppna en webbläsare och ange adressen till den privata slut punkten: `http://10.0.0.5/` .
3.  Standard sidan för IIS visas.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen när du inte längre behöver de resurser som du skapade med den privata länk tjänsten. Detta tar bort den privata länk tjänsten och alla relaterade resurser.

Anropa cmdleten om du vill ta bort resurs gruppen `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nästa steg


Mer information om de tjänster som stöder en privat slut punkt finns i:
> [!div class="nextstepaction"]
> [Tillgänglighet för privat länk](private-link-overview.md#availability)
