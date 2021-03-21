---
title: Skapa avbildningar av virtuella datorer för din Azure Stack Edge Pro GPU-enhet
description: Beskriver hur du skapar virtuella Linux-eller Windows-avbildningar som ska användas med din Azure Stack Edge Pro GPU-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: ee0587063c0fac67068869c58471ada58354fab7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102437680"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-pro-device"></a>Skapa anpassade VM-avbildningar för din Azure Stack Edge Pro-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

För att distribuera virtuella datorer på din Azure Stack Edge Pro-enhet måste du kunna skapa anpassade VM-avbildningar som du kan använda för att skapa virtuella datorer. I den här artikeln beskrivs de steg som krävs för att skapa anpassade Linux-eller Windows VM-avbildningar som du kan använda för att distribuera virtuella datorer på din Azure Stack Edge Pro-enhet.

## <a name="vm-image-workflow"></a>Arbets flöde för VM-avbildning

Arbets flödet kräver att du skapar en virtuell dator i Azure, anpassar den virtuella datorn, generaliserar och sedan laddar ned den virtuella hård disken som motsvarar den virtuella datorn. Denna generaliserade virtuella hård disk överförs till Azure Stack Edge Pro. En hanterad disk skapas från den virtuella hård disken. En avbildning skapas från den hanterade disken. Slutligen skapas virtuella datorer från avbildningen.

Mer information finns i [distribuera en virtuell dator på din Azure Stack Edge Pro-enhet med Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).


## <a name="create-a-windows-custom-vm-image"></a>Skapa en anpassad VM-avbildning för Windows

Utför följande steg för att skapa en Windows VM-avbildning.

1. Skapa en virtuell Windows-dator. Mer information finns i [Självstudier: skapa och hantera virtuella Windows-datorer med Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md)

2. Ladda ned en befintlig operativ system disk.

    - Följ stegen i [Hämta en virtuell hård disk](../virtual-machines/windows/download-vhd.md).

    - Använd följande `sysprep` kommando i stället för vad som beskrivs i föregående procedur.
    
        `c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm`
   
       Du kan också läsa [Översikt över Sysprep (System preparation)](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Använd den här virtuella hård disken för att skapa och distribuera en virtuell dator på din Azure Stack Edge Pro-enhet.

## <a name="create-a-linux-custom-vm-image"></a>Skapa en anpassad virtuell dator avbildning för Linux

Utför följande steg för att skapa en virtuell Linux-avbildning.

1. Skapa en virtuell Linux-dator. Mer information finns i [Självstudier: skapa och hantera virtuella Linux-datorer med Azure CLI](../virtual-machines/linux/tutorial-manage-vm.md).

1. Avetablera den virtuella datorn. Använd den virtuella Azure-agenten för att ta bort datorspecifika filer och data. Använd `waagent` kommandot med `-deprovision+user` parametern på den virtuella käll Linux-datorn. Mer information finns i [förstå och använda Azure Linux-agenten](../virtual-machines/extensions/agent-linux.md).

    1. Anslut till din virtuella Linux-dator med en SSH-klient.
    2. I SSH-fönstret anger du följande kommando:
       
        ```bash
        sudo waagent -deprovision+user
        ```
       > [!NOTE]
       > Kör bara det här kommandot på en virtuell dator som du ska avbilda som en avbildning. Det här kommandot garanterar inte att avbildningen är klar med all känslig information eller är lämplig för omdistribution. `+user`Parametern tar också bort det senast etablerade användar kontot. Använd endast om du vill behålla autentiseringsuppgifterna för användar kontot på den virtuella datorn `-deprovision` .
     
    3. Fortsätt genom att ange **y** . Du kan lägga till `-force` parametern för att undvika det här bekräftelse steget.
    4. När kommandot har slutförts anger du **Avsluta** för att stänga SSH-klienten.  Den virtuella datorn kommer fortfarande att köras nu.


1. [Ladda ned befintlig OS-disk](../virtual-machines/linux/download-vhd.md).

Använd den här virtuella hård disken för att skapa och distribuera en virtuell dator på din Azure Stack Edge Pro-enhet. Du kan använda följande två Azure Marketplace-avbildningar för att skapa anpassade Linux-avbildningar:

|Objekt namn  |Beskrivning  |Publisher  |
|---------|---------|---------|
|[Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.ubuntuserver) |Ubuntu-servern är världens mest populära Linux för moln miljöer.|Canonical|
|[Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.debian) |Debian GNU/Linux är en av de mest populära Linux-distributionerna.     |credativ|

En fullständig lista över Azure Marketplace-avbildningar som kan fungera (för närvarande inte testas) finns på [Azure Marketplace-objekt som är tillgängliga för Azure Stack Hub](/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1910&preserve-view=true).


## <a name="next-steps"></a>Nästa steg

[Distribuera virtuella datorer på din Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).