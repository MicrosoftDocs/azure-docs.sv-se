---
title: 'Snabb start: Använd en Resource Manager-mall för att skapa en virtuell Windows-dator'
description: I den här snabb starten får du lära dig hur du använder en Resource Manager-mall för att skapa en virtuell Windows-dator
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/04/2020
ms.author: cynthn
ms.custom: subject-armqs
ms.openlocfilehash: b2856c51ccc31b57ea567c5d0940cd260797f770
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560760"
---
# <a name="quickstart-create-a-windows-virtual-machine-using-an-arm-template"></a>Snabb start: skapa en virtuell Windows-dator med en ARM-mall

Den här snabb starten visar hur du använder en Azure Resource Manager mall (ARM-mall) för att distribuera en virtuell Windows-dator (VM) i Azure.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-windows%2fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

:::code language="json" source="~/quickstart-templates/101-vm-simple-windows/azuredeploy.json":::


Flera resurser definieras i mallen:

- [**Microsoft. Network/virtualNetworks/subnets**](/azure/templates/Microsoft.Network/virtualNetworks/subnets): skapa ett undernät.
- [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts): skapa ett lagrings konto.
- [**Microsoft. Network/publicIPAddresses**](/azure/templates/Microsoft.Network/publicIPAddresses): skapa en offentlig IP-adress.
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/Microsoft.Network/networkSecurityGroups): skapa en nätverks säkerhets grupp.
- [**Microsoft. Network/virtualNetworks**](/azure/templates/Microsoft.Network/virtualNetworks): skapa ett virtuellt nätverk.
- [**Microsoft. Network/networkInterfaces**](/azure/templates/Microsoft.Network/networkInterfaces): skapa ett nätverkskort.
- [**Microsoft. Compute/virtualMachines**](/azure/templates/Microsoft.Compute/virtualMachines): skapa en virtuell dator.



## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett nyckelvalv och en hemlighet.

    [![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-windows%2fazuredeploy.json)

1. Välj eller ange följande värden. Använd standardvärdena om det är tillgängligt.

    - **Prenumeration**: välj en Azure-prenumeration.
    - **Resurs grupp**: Välj en befintlig resurs grupp i list rutan eller Välj **Skapa ny**, ange ett unikt namn för resurs gruppen och klicka sedan på **OK**.
    - **Plats**: välj en plats.  Välj till exempel **USA, centrala**.
    - **Admin-användar namn**: Ange ett användar namn, till exempel *azureuser*.
    - **Administratörs lösen ord**: Ange ett lösen ord som ska användas för administratörs kontot. Lösen ordet måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](faq.md#what-are-the-password-requirements-when-creating-a-vm).
    - **DNS-etikett prefix**: Ange en unik identifierare som ska användas som en del av DNS-etiketten.
    - **Windows OS-version**: Välj vilken version av Windows du vill köra på den virtuella datorn.
    - **VM-storlek**: Välj den [storlek](../sizes.md) som ska användas för den virtuella datorn.
    - **Plats**: standardvärdet är samma plats som resurs gruppen, om den redan finns.
1. Välj **Granska + skapa**. När verifieringen är klar väljer du **skapa** för att skapa och distribuera den virtuella datorn.


Azure-portalen används för att distribuera mallen. Förutom Azure Portal kan du också använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../../azure-resource-manager/templates/deploy-powershell.md).

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
> [Självstudier om virtuella Azure Windows-datorer](./tutorial-manage-vm.md)