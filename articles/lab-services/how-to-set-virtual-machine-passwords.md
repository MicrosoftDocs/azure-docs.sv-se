---
title: Ange lösen ord för virtuella datorer i Azure Lab Services | Microsoft Docs
description: Lär dig hur du ställer in och återställer lösen ord för virtuella datorer (VM) i labb med Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6ae577ee4c0c7e31760e0fb12afeaeac1ef8b7e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434234"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Konfigurera och hantera en pool för virtuella datorer 
Den här artikeln visar hur du utför följande uppgifter:

- Öka antalet virtuella datorer (VM) i labbet
- Starta alla virtuella datorer eller valda virtuella datorer 
- Återställ virtuella datorer

## <a name="update-the-lab-capacity"></a>Uppdatera labb kapaciteten
Gör så här för att öka eller minska labb kapaciteten (antal virtuella datorer i ett labb):

1. På sidan **pool för virtuell dator** väljer du **labb kapacitet: &lt; numrera &gt; datorer**.
2. Ange det nya **antal virtuella datorer** som du vill ha i labbet. Antalet måste vara större än eller lika med antalet användare som registrerats i labbet. 
3. Välj sedan **Spara**. 

    ![Skärm bild som visar fönstret "labb kapacitet" med knappen "maximalt antal datorer på labbet" och "Spara" vald.](./media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. Om du har ökat kapaciteten kan du se att den virtuella datorn eller de virtuella datorerna skapas. Om du inte ser den nya virtuella datorn i listan uppdaterar du sidan. 

    ![Virtuell dator skapas](./media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>Starta virtuella datorer

### <a name="start-ot-stop-all-vms"></a>Start, stoppa alla virtuella datorer
1. Växla till sidan för **poolen för virtuella datorer** . 
2. Välj **starta alla** från verktygsfältet. 

    ![Knappen starta alla](./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. När alla virtuella datorer har startats kan du stoppa alla virtuella datorer genom att välja knappen **stoppa alla** i verktygsfältet. 

    ![Knappen Stoppa alla](./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Starta valda virtuella datorer
Det finns två sätt att starta valda virtuella datorer (en eller flera). Det första sättet är att markera den virtuella datorn eller de virtuella datorerna i listan och sedan välja **Starta** i verktygsfältet. 

Det andra sättet är att välja en eller flera virtuella datorer i listan och växla knappen i kolumnen **State** . 

![Starta valda virtuella datorer](./media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

På samma sätt kan du stoppa en eller flera virtuella datorer genom att växla knappen i kolumnen **tillstånd** eller välja **stoppa** i verktygsfältet. 

> [!NOTE]
> När en lärare aktive ras på en elev-VM påverkas inte kvoten för studenten. Kvoten för en användare anger antalet Labb timmar som är tillgängliga för användaren utanför den schemalagda klass tiden. Mer information om kvoter finns i [Ange kvoter för användare](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="reset-vms"></a>Återställ virtuella datorer

Om du vill återställa en eller flera virtuella datorer markerar du dem i listan och väljer sedan **Återställ** i verktygsfältet. 

![Återställ valda virtuella datorer](./media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

I dialog rutan **Återställ virtuell dator (er)** väljer du **Återställ**. 

![Dialog rutan Återställ virtuell dator](./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)

## <a name="set-password-for-vms"></a>Ange lösen ord för virtuella datorer
En labb ägare (lärare) kan ange/återställa lösen ordet för virtuella datorer vid tidpunkten för att skapa labbet (guiden Skapa labb) eller när du har skapat labbet på sidan **mall** . 

### <a name="set-password-at-the-time-of-lab-creation"></a>Ange lösen ord vid tidpunkten för att skapa labbet
En labb ägare (lärare) kan ange ett lösen ord för virtuella datorer i labbet på sidan **autentiseringsuppgifter för virtuella datorer** i guiden Skapa labb.

![Nytt labb fönster](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

Genom att aktivera/inaktivera alternativet **Använd samma lösen ord för alla virtuella datorer** på den här sidan kan en lärare välja att använda samma lösen ord för alla virtuella datorer i labbet eller tillåta studenter att ange lösen ord för sina virtuella datorer. Den här inställningen är aktive rad som standard för alla Windows-och Linux-operativsystem avbildningar utom Ubuntu. När den här inställningen är inaktive rad uppmanas eleverna att ange ett lösen ord när de försöker ansluta till den virtuella datorn för första gången. 

### <a name="reset-password-later"></a>Återställ lösen ord senare

1. Välj **Återställ lösen ord** i verktygsfältet på sidan **mall** i labbet. 
1. I dialog rutan **Återställ lösen ord** anger du ett lösen ord och väljer **Återställ lösen ord**.
    
    ![Dialog rutan Ange lösen ord](./media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>Ansluta till student virtuella datorer
Labb skaparen (lärare) kan ansluta till en elev-VM om följande villkor är uppfyllda: 

- Alternativet **Använd samma lösen ord för alla virtuella datorer** valdes när labbet skapades
- Den virtuella datorn körs 

 För att ansluta till den virtuella student datorn, hovrar du över musen på den virtuella datorn i listan och väljer knappen dator.  

![Knappen Anslut till virtuell dator för student](./media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> När lärare startar den virtuella datorn och ansluter till den, påverkas inte student kvoten. 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>Exportera lista över virtuella datorer till en CSV-fil

1. Växla till fliken för **poolen för virtuella datorer** .
2. Välj **...** (ellips) i verktygsfältet och välj sedan **exportera CSV**. 

    ![Exportera lista över virtuella datorer](./media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>Nästa steg
Information om andra alternativ för elev användning som du (som labb ägare) kan konfigurera finns i följande artikel: [Konfigurera elev användning](how-to-configure-student-usage.md).

Information om hur studenter kan återställa lösen ord för sina virtuella datorer finns i [Ange eller återställa lösen ord för virtuella datorer i labb (studenter)](how-to-set-virtual-machine-passwords-student.md).