---
title: Hantera en mall för ett klass rums labb i Azure Lab Services | Microsoft Docs
description: Lär dig hur du skapar och hanterar en labb-mall för klass rum i Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: be84a05fd8b278f5176cd768191d10a20fc719e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434285"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Skapa och hantera en klass rums mall i Azure Lab Services
En mall i ett labb är en basavbildning av en virtuell dator som alla användares virtuella datorer skapas från. Konfigurera mallen för virtuella datorer så att den är konfigurerad med exakt det som du vill förse labbanvändarna med. Du kan ange ett namn och en beskrivning av mallen som visas för labbanvändarna. Sedan publicerar du mallen för att skapa instanser av den virtuella mallen som är tillgänglig för dina labb användare. När du publicerar en mall skapar Azure Lab Services virtuella datorer i labbet med hjälp av mallen. Antalet virtuella datorer som skapas i den här processen är samma som det högsta antalet användare som tillåts i labbet. Du kan definiera antalet i användningsprincipen för labbet. Alla virtuella datorer har samma konfiguration som mallen.

Den här artikeln beskriver hur du skapar och hanterar en mall för virtuella datorer i ett klass rum labb med Azure Lab Services. 

> [!NOTE]
> När du skapar ett labb skapas mallen VM, men den är inte igång. Du kan starta den, ansluta till den och installera all nödvändig program vara för labbet och sedan publicera den. När du publicerar mallen VM stängs den automatiskt av för dig om du inte har gjort det. 
> 
> Mall VM: ar **kostar** att köra, så se till att den virtuella datorns mall stängs av när du inte behöver den för att köras. 


## <a name="set-or-update-template-title-and-description"></a>Ange eller uppdatera mallens rubrik och beskrivning
Använd följande steg för att ange titel och beskrivning för första gången och uppdatera dem senare. 

1. Ange den nya **rubriken** för labbet på sidan **mall** .  
2. Ange den nya **beskrivningen** för mallen. När du flyttar fokus från text rutan sparas den automatiskt. 

    ![Mallens namn och beskrivning](./media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Uppdatera en mall för virtuell dator
Använd följande steg för att uppdatera en mall-VM.  

1. Vänta tills mallen VM startas och välj sedan **Anslut till mall** i verktygsfältet för att ansluta till mallen VM och följ instruktionerna. Om det är en Windows-dator visas ett alternativ för att ladda ned RDP-filen. 
1. När du ansluter till mallen och gör ändringar kommer den inte längre att ha samma konfiguration som de virtuella datorer som senast publicerades till användarna. Ändringar i mallen visas inte på användarnas befintliga virtuella datorer förrän du har publicerat dem igen.

    ![Ansluta till den virtuella malldatorn](./media/how-to-create-manage-template/connect-template-vm.png)
    
1. Installera all programvara som krävs för studenterna ska kunna utföra labbet (till exempel Visual Studio, Azure Storage Explorer osv.). 
1. Koppla från (stäng fjärrskrivbordssessionen) den virtuella malldatorn. 
1. **Stoppa** mallen VM genom att välja **stoppa mall**. 
1. Följ stegen i nästa avsnitt för att **publicera** den uppdaterade mallen VM. 

## <a name="publish-the-template-vm"></a>Publicera den virtuella malldatorn  
I det här steget publicerar du mallen VM. När du publicerar mallen VM skapar Azure Lab Services virtuella datorer i labbet med hjälp av mallen. Alla virtuella datorer har samma konfiguration som mallen.


1. På sidan **mall** väljer du **publicera** i verktygsfältet. 

    ![Knappen publicera mall](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > När du väl har publicerat kan du inte ångra publiceringen. 
2. På sidan **publicera mall** anger du det antal virtuella datorer som du vill skapa i labbet och väljer sedan **publicera**. 

    ![Publicera mall – antal virtuella datorer](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. Du ser **statusen för att publicera** mallen på sidan. Den här processen kan ta upp till en timma. 

    ![Publicera mall – förlopp](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Vänta tills publiceringen är klar och växla sedan till sidan **Virtual Machines-pool** genom att välja **virtuella datorer** på den vänstra menyn eller genom att välja panelen **virtuella datorer** . Bekräfta att du ser virtuella datorer som är i tillståndet **Otilldelad**. De här virtuella datorerna har inte tilldelats till studenter ännu. De måste vara i tillstånd **Stoppad**. Du kan starta en virtuell dator för studenter, ansluta till den virtuella datorn, stoppa den virtuella datorn och ta bort den virtuella datorn på den här sidan. Du kan starta dem på den här sidan eller låta studenterna starta de virtuella datorerna. 

    ![Virtuella datorer i stoppat tillstånd](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)
## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labbkonton som administratör](how-to-manage-lab-accounts.md)
- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
- [Som labb användare, åtkomst labb](how-to-use-classroom-lab.md)
