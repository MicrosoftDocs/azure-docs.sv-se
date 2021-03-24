---
title: Skapa VM-avbildningar från generaliserad avbildning av Windows VHD för din Azure Stack Edge Pro GPU-enhet
description: Beskriver hur du skapar VM-avbildningar från generaliserade avbildningar som börjar från en Windows-VHD eller en VHDX. Använd den här generaliserade avbildningen för att skapa avbildningar av virtuella datorer som ska användas med virtuella datorer som distribuerats på Azure Stack Edge Pro GPU-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: 978099accd57d15c750a27f77b2e220f569a2dd0
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962710"
---
# <a name="use-generalized-image-from-windows-vhd-to-create-a-vm-image-for-your-azure-stack-edge-pro-device"></a>Använd generaliserad avbildning från Windows VHD för att skapa en avbildning av en virtuell dator för din Azure Stack Edge Pro-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

För att distribuera virtuella datorer på din Azure Stack Edge Pro-enhet måste du kunna skapa anpassade VM-avbildningar som du kan använda för att skapa virtuella datorer. I den här artikeln beskrivs de steg som krävs för att förbereda en virtuell Windows-VHD eller VHDX för att skapa en generaliserad avbildning. Den här generaliserade avbildningen används sedan för att skapa en avbildning av en virtuell dator för din Azure Stack Edge Pro-enhet. 

## <a name="about-preparing-windows-vhd"></a>Om att förbereda Windows-VHD

En Windows-VHD eller VHDX kan användas för att skapa en *generaliserad* avbildning eller en *specialiserad* avbildning. I följande tabell sammanfattas viktiga skillnader mellan de *generaliserade* och *specialiserade* bilderna.


|Avbildningstyp  |Generaliserade  |Specialiserade  |
|---------|---------|---------|
|Mål     |Distribuerat i alla system         | Riktat mot ett speciellt system        |
|Konfigurera efter start     | Installations programmet krävs vid första starten av den virtuella datorn.          | Installationen behövs inte. <br> Plattformen aktiverar den virtuella datorn.        |
|Konfiguration     |Hostname, admin-user och andra VM-inställningar krävs.         |Helt förkonfigurerad.         |
|Används när     |Skapa flera nya virtuella datorer från samma avbildning.         |Migrera en speciell dator eller återställa en virtuell dator från en tidigare säkerhets kopia.         |


Den här artikeln beskriver steg som krävs för att distribuera från en generaliserad avbildning. Information om hur du distribuerar från en specialiserad avbildning finns i [använda specialiserad Windows-VHD](azure-stack-edge-placeholder.md) för din enhet.

> [!IMPORTANT]
> Den här proceduren omfattar inte fall där den virtuella käll hård disken konfigureras med anpassade konfigurationer och inställningar. Ytterligare åtgärder kan till exempel krävas för att generalisera en virtuell hård disk som innehåller anpassade brand Väggs regler eller proxyinställningar. Mer information om dessa ytterligare åtgärder finns i [förbereda en virtuell Windows-hårddisk att ladda upp till Azure-azure Virtual Machines](../virtual-machines/windows/prepare-for-upload-vhd-image.md).


## <a name="vm-image-workflow"></a>Arbets flöde för VM-avbildning

Det övergripande arbets flödet för att förbereda en Windows-VHD för användning som en generaliserad avbildning har följande steg:

1. Konvertera VHD-eller VHDX-källan till en fast storleks-VHD.
1. Skapa en virtuell dator i Hyper-V med hjälp av den fasta virtuella hård disken.
1. Anslut till den virtuella Hyper-V-datorn.
1. Generalisera den virtuella hård disken med hjälp av *Sysprep* -verktyget. 
1. Kopiera den generaliserade avbildningen till Blob Storage.
1. Använd generaliserad avbildning för att distribuera virtuella datorer på enheten. Mer information finns i så här [distribuerar du en virtuell dator via Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md) eller [distribuerar en virtuell dator via PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).


## <a name="prerequisites"></a>Förutsättningar

Innan du förbereder en virtuell Windows-hårddisk som ska användas som en generaliserad avbildning på Azure Stack Edge, se till att:

- Du har en virtuell hård disk eller en VHDX som innehåller en version av Windows som stöds. Se de [gäst operativ system som stöds]() för Azure Stack Edge Pro. 
- Du har åtkomst till en Windows-klient med Hyper-V Manager installerad. 
- Du har åtkomst till ett Azure Blob Storage-konto för att lagra din virtuella hård disk när den har förberetts.

## <a name="prepare-a-generalized-windows-image-from-vhd"></a>Förbereda en generaliserad Windows-avbildning från en virtuell hård disk

## <a name="convert-to-a-fixed-vhd"></a>Konvertera till en fast virtuell hård disk 

För din enhet behöver du virtuella hård diskar med fast storlek för att skapa avbildningar av virtuella datorer. Du måste konvertera din käll-Windows VHD eller VHDX till en fast virtuell hård disk. Följ de här stegen:

1. Öppna Hyper-V Manager på klient systemet. Gå till **Redigera disk**.

    ![Öppna Hyper-V Manager](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-1.png)

1. Välj **nästa>** på sidan **innan du börjar** .

1. På sidan **hitta virtuell hård disk** bläddrar du till platsen för den Windows VHD-eller VHDX-källa som du vill konvertera. Välj **nästa>**.

    ![Sidan hitta virtuell hård disk](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-2.png)

1. På sidan **Välj åtgärd** väljer du **konvertera** och väljer **Nästa>**.

    ![Sidan Välj åtgärd](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-3.png)

1. På sidan **Välj disk format** väljer du **VHD** -format och väljer sedan **Nästa>**.

   ![Sidan Välj disk format](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-4.png)


1. På sidan **Välj disktyp** väljer du **fast storlek** och väljer **Nästa>**.

   ![Sidan Välj disktyp](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-5.png)


1. På sidan **Konfigurera disk** bläddrar du till platsen och anger ett namn för den fasta storleken på VHD-disken. Välj **nästa>**.

   ![Sidan Konfigurera disk](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-6.png)


1. Granska sammanfattningen och välj **Slutför**. Det tar några minuter att konvertera VHD-eller VHDX-konverteringen. Tiden för konvertering beror på käll diskens storlek. 

<!--
1. Run PowerShell on your Windows client.
1. Run the following command:

    ```powershell
    Convert-VHD -Path <source VHD path> -DestinationPath <destination-path.vhd> -VHDType Fixed 
    ```
-->
Du använder den här fasta virtuella hård disken för alla efterföljande steg i den här artikeln.


## <a name="create-a-hyper-v-vm-from-fixed-vhd"></a>Skapa en virtuell Hyper-V-dator från fast virtuell hård disk

1. I **Hyper-V Manager** högerklickar du på system-noden i fönstret omfång för att öppna snabb menyn och väljer sedan **ny**  >  **virtuell dator**.

    ![Välj ny virtuell dator i omfattnings fönstret](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-1.png)

1. På sidan **innan du börjar** i guiden Ny virtuell dator väljer du **Nästa**.

1. På sidan **Ange namn och plats** anger du ett **namn** och en **plats** för den virtuella datorn. Välj **Nästa**.

    ![Ange namn och plats för din virtuella dator](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-2.png)

1. På sidan **Ange generation** väljer du **generation 1** för avbildnings typen. VHD-enhet och väljer sedan **Nästa**.    

    ![Ange generation](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-3.png)

1. Tilldela önskade konfigurationer för minne och nätverk.

1. På sidan **Anslut virtuell hård disk** väljer du **Använd en befintlig virtuell hård disk**, anger platsen för den fasta Windows-hårddisk som vi skapade tidigare och väljer sedan **Nästa**.

    ![Sidan Anslut virtuell hård disk](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-4.png)

1. Granska **sammanfattningen** och välj sedan **Slutför** för att skapa den virtuella datorn.

Det tar flera minuter att skapa den virtuella datorn.
     

## <a name="connect-to-the-hyper-v-vm"></a>Anslut till den virtuella Hyper-V-datorn

Den virtuella datorn visas i listan över virtuella datorer i klient systemet. 


1. Välj den virtuella datorn och högerklicka och välj **Starta**. 

    ![Välj virtuell dator och starta den](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-2.png)

2. Den virtuella datorn visar Visa som **körs**. Välj den virtuella datorn och högerklicka sedan och välj **Anslut**.

    ![Ansluta till virtuell dator](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-4.png)

När du är ansluten till den virtuella datorn slutför du installations guiden för datorn och loggar sedan in på den virtuella datorn.


## <a name="generalize-the-vhd"></a>Generalisera den virtuella hård disken  

Använd *Sysprep* -verktyget för att generalisera den virtuella hård disken. 

1. Öppna en kommando tolk i den virtuella datorn.
1. Kör följande kommando för att generalisera den virtuella hård disken. 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    Mer information finns i  [Översikt över Sysprep (System preparation)](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).
1.  När kommandot har slutförts kommer den virtuella datorn att stängas av. **Starta inte om den virtuella datorn**.

## <a name="upload-the-vhd-to-azure-blob-storage"></a>Överför den virtuella hård disken till Azure Blob Storage

Din virtuella hård disk kan nu användas för att skapa en generaliserad avbildning på Azure Stack Edge. 

1. Överför den virtuella hård disken till Azure Blob Storage. Se de detaljerade anvisningarna i [Ladda upp en virtuell hård disk med Azure Storage Explorer](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md).
1. När uppladdningen är klar kan du använda den uppladdade avbildningen för att skapa VM-avbildningar och virtuella datorer. 

<!-- this should be added to deploy VM articles - If you experience any issues creating VMs from your new image, you can use VM console access to help troubleshoot. For information on console access, see [link].-->



## <a name="next-steps"></a>Nästa steg

Beroende på vilken typ av distribution du har kan du välja någon av följande procedurer.

- [Distribuera en virtuell dator från en generaliserad avbildning via Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Distribuera en virtuell dator från en generaliserad avbildning via Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)  
