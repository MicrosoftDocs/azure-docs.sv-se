---
title: Hantera virtuella datorer diskar på Azure Stack Edge Pro GPU, Pro R, Mini R via Azure Portal
description: Lär dig hur du hanterar diskar, inklusive hur du lägger till eller tar bort en datadisk på virtuella datorer som distribueras på Azure Stack Edge Pro GPU, Azure Stack Edge Pro R och Azure Stack Edge Mini R via Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage disks on a VM running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: dff3f4bafdb35d89e8f1792c1aca68824a9bc685
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080558"
---
# <a name="use-the-azure-portal-to-manage-disks-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Använd Azure Portal för att hantera diskar på de virtuella datorerna på din Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Du kan etablera diskar på de virtuella datorerna (VM) som distribueras på din Azure Stack Edge Pro-enhet med hjälp av Azure Portal. Diskarna är etablerade på enheten via den lokala Azure Resource Manager och använder enhets kapaciteten. Åtgärderna, till exempel att lägga till en disk, kan koppla från en disk via Azure Portal, vilket i sin tur gör anrop till den lokala Azure Resource Manager för att etablera lagringen. 

Den här artikeln beskriver hur du lägger till en datadisk till en befintlig virtuell dator, kopplar från en datadisk och ändrar sedan storlek på den virtuella datorn till sig själv via Azure Portal. 

        
## <a name="about-disks-on-vms"></a>Om diskar på virtuella datorer

Din virtuella dator kan ha en OS-disk och en datadisk. Alla virtuella datorer som distribueras på enheten har en ansluten operativ system disk. Den här OS-disken har ett förinstallerat operativ system som valdes när den virtuella datorn skapades. Den här disken innehåller start volymen.

> [!NOTE]
> Du kan inte ändra OS-diskens storlek för den virtuella datorn på enheten. OS-diskens storlek bestäms av storleken på den virtuella dator som du har valt. 


En datadisk å andra sidan är en hanterad disk som är ansluten till den virtuella dator som körs på enheten. En datadisk används för att lagra program data. Data diskar är vanligt vis SCSI-enheter. Storleken på den virtuella datorn avgör hur många data diskar som du kan ansluta till en virtuell dator. Som standard används Premium Storage som värd för diskarna.

En virtuell dator som distribuerats på enheten kan ibland innehålla en temporär disk. Den temporära disken ger kortsiktig lagring för program och processer och är avsedd att endast lagra data, till exempel sid-eller växlingsfiler. Data på den tillfälliga disken kan gå förlorade under en underhålls händelse eller när du distribuerar om en virtuell dator. Vid en lyckad standard omstart av den virtuella datorn kommer data på den temporära disken att kvarstå. 


## <a name="prerequisites"></a>Förutsättningar

Innan du börjar hantera diskar på de virtuella datorerna som körs på enheten via Azure Portal, se till att:


1. Du har åtkomst till en aktive rad Azure Stack Edge Pro GPU-enhet. Du har också aktiverat ett nätverks gränssnitt för beräkning på enheten. Den här åtgärden skapar en virtuell växel på det nätverks gränssnittet på den virtuella datorn. 
    1. I enhetens lokala användar gränssnitt går du till **Compute**. Välj det nätverks gränssnitt som du ska använda för att skapa en virtuell växel.

        > [!IMPORTANT] 
        > Du kan bara konfigurera en port för beräkning.

    1. Aktivera beräkning i nätverks gränssnittet. Azure Stack Edge Pro GPU skapar och hanterar en virtuell växel som motsvarar det nätverks gränssnittet.

1. Du har minst en virtuell dator som har distribuerats på enheten. Om du vill skapa den här virtuella datorn går du till anvisningarna i [distribuera VM på Azure Stack Edge Pro via Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).



## <a name="add-a-data-disk"></a>Lägg till en datadisk

Följ dessa steg om du vill lägga till en disk till en virtuell dator som har distribuerats på enheten. 

1. Gå till den virtuella dator som du vill lägga till en datadisk till och gå sedan till sidan **Översikt** . Välj **Diskar**.
    
    ![Sidan Välj diskar på översikten](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-1.png)

1. På bladet **diskar** , under **data diskar**, väljer du **skapa och koppla en ny disk**.

    ![Skapa och ansluta en ny disk](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-2.png)

1. Ange följande parametrar på bladet **skapa en ny disk** :

    
    |Fält  |Beskrivning  |
    |---------|---------|
    |Name     | Ett unikt namn inom resurs gruppen. Namnet kan inte ändras efter att data disken har skapats.     |
    |Storlek| Storleken på data disken i GiB. Den maximala storleken på en datadisk bestäms av den VM-storlek som du har valt. När du konfigurerar en disk bör du också ta hänsyn till det faktiska utrymmet på enheten och andra VM-arbetsbelastningar som kör med kapacitet.  |         

    ![Skapa ett nytt disk blad](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-3.png)

    Välj **OK** och fortsätt.

1. På sidan **Översikt** , under **diskar**, ser du en post som motsvarar den nya disken. Acceptera standardvärdet eller tilldela ett giltigt logiskt enhets nummer (LUN) till disken och välj **Spara**. En LUN är en unik identifierare för en SCSI-disk. Mer information finns i [Vad är en LUN?](../virtual-machines/linux/azure-to-guest-disk-mapping.md#what-is-a-lun).

    ![Sidan ny disk på Översikt](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-4.png)

1. Ett meddelande visas om att disken håller på att skapas. När disken har skapats uppdateras den virtuella datorn. 

    ![Meddelande för att skapa disk](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-5.png)

1. Gå tillbaka till **översikts** sidan. Listan över diskar som uppdateras för att visa den nya data disken.

    ![Uppdaterad lista över data diskar](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-6.png)


## <a name="change-a-data-disk"></a>Ändra en datadisk

Följ dessa steg om du vill ändra en disk som är kopplad till en virtuell dator som har distribuerats på enheten.

1. Gå till den virtuella dator som har data disken som ska ändras och gå till sidan **Översikt** . Välj **Diskar**.

1. I listan över data diskar väljer du den disk som du vill ändra. Längst till höger på den valda disken väljer du redigerings ikonen (blyertspenna).  

    ![Välj en disk som ska ändras](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-1.png)

1. På bladet **Ändra disk** kan du bara ändra storleken på disken. Det går inte att ändra namnet som är associerat med disken när det har skapats. Ändra **storlek** och spara ändringarna.

    ![Ändra storlek på data disken](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-2.png)

    > [!NOTE]
    > Du kan bara expandera en datadisk. det går inte att krympa disken.

1. På sidan **Översikt** uppdateras listan över diskar för att visa den uppdaterade disken.


## <a name="attach-an-existing-disk"></a>Ansluta en befintlig disk

Följ de här stegen för att koppla en befintlig disk till den virtuella dator som distribuerats på enheten.

1. Gå till den virtuella dator som du vill koppla den befintliga disken till och gå sedan till sidan **Översikt** . Välj **Diskar**.
    
    ![Välj diskar ](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. På bladet **diskar** , under **data diskar**, väljer du **Anslut en befintlig disk**.

    ![Välj Anslut en befintlig disk](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-1.png)

1. Acceptera standard-LUN eller tilldela ett giltigt LUN. Välj en befintlig datadisk i list rutan. Välj Spara

    ![Välj en befintlig disk](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-2.png)

    Välj **Spara** och fortsätt.

1. Ett meddelande visas om att den virtuella datorn har uppdaterats. När den virtuella datorn har uppdaterats går du tillbaka till sidan **Översikt** . Uppdatera sidan om du vill visa den nyligen anslutna disken i listan över data diskar.

    ![Visa uppdaterad lista över data diskar på översikts Sidan](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="detach-a-data-disk"></a>Koppla ifrån en datadisk

Följ de här stegen för att koppla från eller ta bort en datadisk som är kopplad till en virtuell dator som har distribuerats på enheten.

> [!NOTE]
> - Du kan ta bort en datadisk medan den virtuella datorn körs. Se till att inget aktivt använder disken innan du kopplar bort den från den virtuella datorn.
> - Om du kopplar från en disk tas den inte bort automatiskt.

1. Gå till den virtuella dator som du vill koppla från en datadisk från och gå till sidan **Översikt** . Välj **Diskar**.

    ![Välj diskar](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. I listan över diskar väljer du den disk som du vill koppla från. Längst till höger på den valda disken väljer du koppla från-ikonen (tvär). Den valda posten kommer att kopplas bort. Välj **Spara**. 

    ![Välj en disk som ska kopplas från](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/detach-data-disk-1.png)

1. När disken har kopplats från uppdateras den virtuella datorn. Uppdatera **översikts** sidan om du vill visa den uppdaterade listan över data diskar.

    ![Välj Spara](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="next-steps"></a>Nästa steg

Information om hur du distribuerar virtuella datorer på din Azure Stack Edge Pro-enhet finns i [distribuera virtuella datorer via Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
