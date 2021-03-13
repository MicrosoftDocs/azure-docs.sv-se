---
title: 'Ändra D:-enheten för en virtuell dator till en datadisk '
description: 'Beskriver hur du ändrar enhets beteckningar för en virtuell Windows-dator så att du kan använda enheten D: som en data enhet.'
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: 87aa1344b3fbe0d11c1c5cdfa8a56560d67eb54f
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555558"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Använda D:-enheten som en dataenhet på en virtuell Windows-dator
Om ditt program behöver använda D-enheten för att lagra data, följer du dessa instruktioner för att använda en annan enhets beteckning för den tillfälliga disken. Använd aldrig den temporära disken för att lagra data som du behöver behålla.

Om du ändrar storlek på eller **stoppar (frigör)** en virtuell dator kan detta utlösa placeringen av den virtuella datorn till en ny hypervisor. En planerad eller oplanerad underhålls händelse kan också utlösa denna placering. I det här scenariot omtilldelas den temporära disken till den första tillgängliga enhets beteckningen. Om du har ett program som specifikt kräver D: Drive måste du följa de här stegen för att tillfälligt flytta pagefile.sys, koppla en ny datadisk och pagefile.sys tilldela den till den temporära enheten. När den är klar kommer Azure inte att ta tillbaka D: om den virtuella datorn flyttas till en annan hypervisor.

Mer information om hur Azure använder den temporära disken finns i [förstå den temporära enheten på Microsoft Azure Virtual Machines](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines)

## <a name="attach-the-data-disk"></a>Koppla data disken
Först måste du koppla data disken till den virtuella datorn. Information om hur du gör detta med hjälp av portalen finns i [så här ansluter du en hanterad datadisk i Azure Portal](attach-managed-disk-portal.md).

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Flytta pagefile.sys tillfälligt till C-enheten
1. Ansluta till den virtuella datorn. 
2. Högerklicka på **Start** -menyn och välj **system**.
3. På den vänstra menyn väljer du **Avancerade systeminställningar**.
4. I avsnittet **prestanda** väljer du **Inställningar**.
5. Välj fliken **Avancerat**.
6. I avsnittet **virtuellt minne** väljer du **ändra**.
7. Välj enhet **C** och klicka sedan på **Systemhanterad storlek** och klicka sedan på **Ange**.
8. Välj enheten **D** och klicka sedan på **ingen växlings fil** och klicka sedan på **Ange**.
9. Klicka på Applicera. Du får en varning om att datorn måste startas om för att ändringarna ska börja gälla.
10. Starta om den virtuella datorn.

## <a name="change-the-drive-letters"></a>Ändra enhets beteckningar
1. När den virtuella datorn har startats om loggar du in på den virtuella datorn igen.
2. Klicka på **Start** -menyn och skriv **diskmgmt. msc** och tryck på RETUR. Disk hantering startar.
3. Högerklicka på **D**, den tillfälliga lagrings enheten och välj **ändra enhets beteckning och sökvägar**.
4. Under enhets beteckning väljer du en ny enhet, till exempel **t** och klickar sedan på **OK**. 
5. Högerklicka på data disken och välj **ändra enhets beteckning och sökvägar**.
6. Under enhets beteckning väljer du enhet **D** och klickar sedan på **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Flytta pagefile.sys tillbaka till den tillfälliga lagrings enheten
1. Högerklicka på **Start** -menyn och välj **system**
2. På den vänstra menyn väljer du **Avancerade systeminställningar**.
3. I avsnittet **prestanda** väljer du **Inställningar**.
4. Välj fliken **Avancerat**.
5. I avsnittet **virtuellt minne** väljer du **ändra**.
6. Välj OS-enhet **C** och klicka på **ingen växlings fil** och klicka sedan på **Ange**.
7. Välj den tillfälliga lagrings enheten **T** och klicka sedan på **Systemhanterad storlek** och klicka sedan på **Ange**.
8. Klicka på **Applicera**. Du får en varning om att datorn måste startas om för att ändringarna ska börja gälla.
9. Starta om den virtuella datorn.

## <a name="next-steps"></a>Nästa steg
* Du kan öka lagrings utrymmet som är tillgängligt för den virtuella datorn genom att [koppla ytterligare en data disk](attach-managed-disk-portal.md).
