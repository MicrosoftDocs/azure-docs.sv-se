---
title: 'Snabb start: Använd en Resource Manager-mall för att skapa en Ubuntu Linux virtuell dator'
description: I den här snabb starten får du lära dig hur du använder en Resource Manager-mall för att skapa en virtuell Linux-dator
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/04/2020
ms.author: cynthn
ms.custom: subject-armqs
ms.openlocfilehash: 97a9d3632cb0e78b899844b1aaa84a030c6da23e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549625"
---
# <a name="quickstart-create-an-ubuntu-linux-virtual-machine-using-an-arm-template"></a>Snabb start: skapa en Ubuntu Linux virtuell dator med en ARM-mall

Den här snabb starten visar hur du använder en Azure Resource Manager mall (ARM-mall) för att distribuera en Ubuntu Linux virtuell dator (VM) i Azure.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-linux%2fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/).

:::code language="json" source="~/quickstart-templates/101-vm-simple-linux/azuredeploy.json":::


Flera resurser definieras i mallen:

- [**Microsoft. Network/virtualNetworks/subnets**](/azure/templates/Microsoft.Network/virtualNetworks/subnets): skapa ett undernät.
- [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts): skapa ett lagrings konto.
- [**Microsoft. Network/networkInterfaces**](/azure/templates/Microsoft.Network/networkInterfaces): skapa ett nätverkskort.
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/Microsoft.Network/networkSecurityGroups): skapa en nätverks säkerhets grupp.
- [**Microsoft. Network/virtualNetworks**](/azure/templates/Microsoft.Network/virtualNetworks): skapa ett virtuellt nätverk.
- [**Microsoft. Network/publicIPAddresses**](/azure/templates/Microsoft.Network/publicIPAddresses): skapa en offentlig IP-adress.
- [**Microsoft. Compute/virtualMachines**](/azure/templates/Microsoft.Compute/virtualMachines): skapa en virtuell dator.

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett nyckelvalv och en hemlighet.

    [![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-linux%2fazuredeploy.json)

1. Välj eller ange följande värden. Använd standardvärdena om det är tillgängligt.

    - **Prenumeration**: välj en Azure-prenumeration.
    - **Resurs grupp**: Välj en befintlig resurs grupp i list rutan eller Välj **Skapa ny**, ange ett unikt namn för resurs gruppen och klicka sedan på **OK**.
    - **Plats**: välj en plats.  Välj till exempel **USA, centrala**.
    - **Admin-användar namn**: Ange ett användar namn, till exempel *azureuser*.
    - **Autentiseringstyp**: du kan välja mellan att använda en SSH-nyckel eller ett lösen ord.
    - **Administratörs lösen ord eller nyckel** beroende på vad du väljer för autentiseringstypen:
        - Om du väljer **lösen ord** måste lösen ordet vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](faq.md#what-are-the-password-requirements-when-creating-a-vm).
        - Om du väljer **sshPublicKey** klistrar du in innehållet i den offentliga nyckeln.
    - **DNS-etikett prefix**: Ange en unik identifierare som ska användas som en del av DNS-etiketten.
    - **Ubuntu OS-version**: Välj vilken version av Ubuntu som du vill köra på den virtuella datorn.
    - **Plats**: standardvärdet är samma plats som resurs gruppen, om den redan finns.
    - **VM-storlek**: Välj den [storlek](../sizes.md) som ska användas för den virtuella datorn.
    - **Virtual Network namn**: namn som ska användas för vNet.
    - **Under näts namn**: namnet på det undernät som den virtuella datorn ska använda.
    - **Nätverks säkerhets gruppens namn**: namnet på NSG.
1. Välj **Granska + skapa**. När verifieringen är klar väljer du **skapa** för att skapa och distribuera den virtuella datorn.


Azure-portalen används för att distribuera mallen. Förutom Azure Portal kan du också använda Azure CLI, Azure PowerShell och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Du kan använda Azure Portal för att kontrol lera den virtuella datorn och en annan resurs som har skapats. När distributionen är färdig väljer du **gå till resurs grupp** för att se den virtuella datorn och andra resurser.


## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort resurs gruppen, som tar bort den virtuella datorn och alla resurser i resurs gruppen. 

1. Välj **resurs gruppen**.
1. Välj **ta bort** på sidan för resurs gruppen.
1. När du uppmanas till det anger du namnet på resurs gruppen och väljer sedan **ta bort**.


## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat en enkel virtuell dator med en ARM-mall. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Linux-datorer.


> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Linux-datorer](./tutorial-manage-vm.md)