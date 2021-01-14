---
title: Skapa en virtuell dator från en hanterad avbildning i Azure
description: Skapa en virtuell Windows-dator från en generaliserad hanterad avbildning med Azure PowerShell eller portalen.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: cefe427a7a6f7a46c838147f3d699423c9f19c7b
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201900"
---
# <a name="create-a-vm-from-a-managed-image"></a>Skapa en virtuell dator från en hanterad avbildning

Du kan skapa flera virtuella datorer från en Azure-hanterad VM-avbildning med hjälp av Azure Portal eller PowerShell. En hanterad virtuell dator avbildning innehåller den information som krävs för att skapa en virtuell dator, inklusive operativ system och data diskar. De virtuella hård diskar (VHD: er) som utgör avbildningen, inklusive både OS-diskar och data diskar, lagras som hanterade diskar. 

Innan du skapar en ny virtuell dator måste du [skapa en hanterad virtuell dator avbildning](capture-image-resource.md) som ska användas som käll avbildning och ge Läs behörighet på avbildningen till alla användare som ska ha åtkomst till avbildningen. 

En hanterad avbildning har stöd för upp till 20 samtidiga distributioner. Om du försöker skapa fler än 20 virtuella datorer samtidigt, från samma hanterade avbildning, kan timeout uppstå för etablerings tids gränsen på grund av lagrings prestanda begränsningarna för en enda virtuell hård disk. Om du vill skapa fler än 20 virtuella datorer samtidigt använder du en avbildning av [delade avbildnings gallerier](shared-image-galleries.md) som kon figurer ATS med 1 replik för varje 20 samtidiga VM-distributioner.

## <a name="use-the-portal"></a>Använda portalen

1. Gå till [Azure Portal](https://portal.azure.com) för att hitta en hanterad avbildning. Sök efter och välj **bilder**.
3. Välj den avbildning som du vill använda från listan. Sidan **Översikt över** avbildning öppnas.
4. Välj **Skapa virtuell dator** på menyn.
5. Ange informationen för den virtuella datorn. Användar namnet och lösen ordet som anges här kommer att användas för att logga in på den virtuella datorn. När du är klar väljer du **OK**. Du kan skapa den nya virtuella datorn i en befintlig resurs grupp eller välja **Skapa ny** för att skapa en ny resurs grupp för att lagra den virtuella datorn.
6. Välj en storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. 
7. Under **Inställningar**, gör du nödvändiga ändringar och väljer **OK**. 
8. På sidan Sammanfattning bör du se avbildnings namnet som en **privat bild**. Välj **OK** för att starta distributionen av virtuella datorer.


## <a name="use-powershell"></a>Använd PowerShell

Du kan använda PowerShell för att skapa en virtuell dator från en avbildning med hjälp av den förenklade parameter uppsättningen för cmdleten [New-AzVm](/powershell/module/az.compute/new-azvm) . Avbildningen måste finnas i samma resurs grupp där du skapar den virtuella datorn.

 

Den förenklade parametern som angetts för [New-AzVm](/powershell/module/az.compute/new-azvm) kräver bara att du anger ett namn, en resurs grupp och ett avbildnings namn för att skapa en virtuell dator från en avbildning. New-AzVm använder värdet för parametern **-Name** som namn på alla resurser som skapas automatiskt. I det här exemplet ger vi mer detaljerade namn för var och en av resurserna, men låt cmdleten skapa dem automatiskt. Du kan också skapa resurser i förväg, till exempel det virtuella nätverket och skicka resurs namnet till cmdleten. New-AzVm använder de befintliga resurserna om de kan hitta dem med deras namn.

I följande exempel skapas en virtuell dator med namnet *myVMFromImage* i resurs gruppen *myResourceGroup* från avbildningen med namnet *image*. 


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```



## <a name="next-steps"></a>Nästa steg
[Skapa och hantera virtuella Windows-datorer med modulen Azure PowerShell](tutorial-manage-vm.md)
