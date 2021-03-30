---
title: Ta bort ett labb eller en virtuell dator i ett labb i Azure DevTest Labs
description: Den här artikeln visar hur du tar bort ett labb eller tar bort en virtuell dator i ett labb med hjälp av Azure Portal (Azure DevTest Labs).
ms.topic: article
ms.date: 01/24/2020
ms.openlocfilehash: 495fb98f3da41a47d316dd64554ba616ede0af47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85481229"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Ta bort ett labb eller en virtuell dator i ett labb i Azure DevTest Labs
Den här artikeln visar hur du tar bort ett labb eller en virtuell dator i ett labb.

## <a name="delete-a-lab"></a>Ta bort ett labb
När du tar bort en DevTest Labs-instans från en resurs grupp utför DevTest Labs följande åtgärder: 

- Alla resurser som skapades automatiskt när labbet skapades raderas automatiskt. Själva resurs gruppen tas inte bort. Om du har skapat några resurser manuellt kan tjänsten inte ta bort den här resurs gruppen. 
- Alla virtuella datorer i labb-och resurs grupperna som är kopplade till dessa virtuella datorer tas automatiskt bort. När du skapar en virtuell dator i ett labb skapar tjänsten resurser (disk, nätverks gränssnitt, offentlig IP-adress osv.) för den virtuella datorn i en separat resurs grupp. Men om du manuellt skapar ytterligare resurser i dessa resurs grupper tar DevTest Labs-tjänsten inte bort dessa resurser och resurs gruppen. 

Gör så här om du vill ta bort ett labb: 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **alla resurser** på menyn till vänster, Välj **DevTest Labs** för typ av tjänst och välj labbet.

    ![Välj ditt labb](media/devtest-lab-delete-lab-vm/select-lab.png)
3. På sidan **DevTest Lab** klickar du på **ta bort** i verktygsfältet. 

    ![Knappen Ta bort](media/devtest-lab-delete-lab-vm/delete-button.png)
4. På sidan **bekräftelse** anger du **namnet** på ditt labb och väljer **ta bort**. 

    ![Bekräfta](media/devtest-lab-delete-lab-vm/confirm-delete.png)
5. Om du vill se status för åtgärden väljer du **meddelande** ikon (Bell). 

    ![Meddelanden](media/devtest-lab-delete-lab-vm/delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>Ta bort en virtuell dator i ett labb
Om jag tar bort en virtuell dator i ett labb raderas vissa resurser (inte alla) som skapades när labbet skapades. Följande resurser tas inte bort: 

-   Nyckel valv i huvud resurs gruppen
-   Tillgänglighets uppsättning, belastningsutjämnare, offentlig IP-adress i den virtuella dator resurs gruppen. Dessa resurser delas av flera virtuella datorer i en resurs grupp. 

Den virtuella datorn, nätverks gränssnittet och den disk som är kopplad till den virtuella datorn tas bort. 

Gör så här om du vill ta bort en virtuell dator i ett labb: 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **alla resurser** på menyn till vänster, Välj **DevTest Labs** för typ av tjänst och välj labbet.

    ![Välj ditt labb](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Välj **... (tre punkter)** för den virtuella datorn i listan över virtuella datorer och välj **ta bort**. 

    ![Ta bort virtuell dator på menyn](media/devtest-lab-delete-lab-vm/delete-vm-menu-in-list.png)
4. I **bekräftelse** dialog rutan väljer du **OK**. 
5. Om du vill se status för åtgärden väljer du **meddelande** ikon (Bell). 

Om du vill ta bort en virtuell dator från **sidan virtuell dator** väljer du **ta bort** från verktygsfältet som du ser i följande bild:

![Ta bort virtuell dator från virtuell dator sida](media/devtest-lab-delete-lab-vm/delete-from-vm-page.png) 


## <a name="next-steps"></a>Nästa steg
Om du vill skapa ett labb kan du läsa följande artiklar: 

- [Skapa ett labb](devtest-lab-create-lab.md)
- [Lägga till en virtuell dator i labbet](devtest-lab-add-vm.md)