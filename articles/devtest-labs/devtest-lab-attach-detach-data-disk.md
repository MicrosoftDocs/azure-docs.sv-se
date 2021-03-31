---
title: Koppla eller koppla från en datadisk till en virtuell dator i Azure DevTest Labs
description: Lär dig hur du ansluter eller kopplar från en datadisk till en virtuell dator i Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 8eebfbda421233bcec780d441a4020acce740618
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91328521"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Koppla eller koppla från en datadisk till en virtuell dator i Azure DevTest Labs
[Azure Managed disks](../virtual-machines/managed-disks-overview.md) hanterar de lagrings konton som är kopplade till data diskar för virtuella datorer. En användare bifogar en ny datadisk till en virtuell dator, anger typen och storleken på den disk som behövs, och Azure skapar och hanterar disken automatiskt. Data disken kan sedan frånkopplas från den virtuella datorn och antingen anslutas senare till samma virtuella dator eller anslutas till en annan virtuell dator som tillhör samma användare.

Den här funktionen är praktisk för att hantera lagrings-eller program vara utanför varje enskild virtuell dator. Om lagrings-eller program varan redan finns i en datadisk, kan den enkelt anslutas, kopplas från och återanslutas till en virtuell dator som ägs av den användare som äger den data disken.

## <a name="attach-a-data-disk"></a>Anslut en datadisk
Innan du ansluter en datadisk till en virtuell dator bör du läsa följande tips:

- Storleken på den virtuella datorn styr hur många data diskar du kan koppla. Mer information finns i [storlekar för virtuella datorer](../virtual-machines/sizes.md).
- Du kan endast ansluta en datadisk till en virtuell dator som kör. Kontrol lera att den virtuella datorn körs innan du försöker ansluta en datadisk.

### <a name="attach-a-new-disk"></a>Koppla en ny disk
Följ de här stegen för att skapa och ansluta en ny hanterad datadisk till en virtuell dator i Azure DevTest Labs.

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **alla tjänster** och välj sedan **DevTest Labs** i listan.
1. I listan med labb väljer du önskat labb. 
1. I listan med **virtuella datorer** väljer du en virtuell dator som körs.
1. Från menyn till vänster väljer du **diskar**.
1. Välj **bifoga ny** för att skapa en ny datadisk och koppla den till den virtuella datorn.

    ![Koppla ny datadisk till en virtuell dator](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Slutför fönstret **Anslut ny disk** genom att ange ett namn på en datadisk, typ och storlek.

    ![Slutför formuläret "bifoga ny disk"](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Välj **OK**.

Efter en liten stund skapas den nya datadisken och kopplas till den virtuella datorn och visas i listan över **data diskar** för den virtuella datorn.

### <a name="attach-an-existing-disk"></a>Ansluta en befintlig disk
Följ dessa steg om du vill ansluta en befintlig tillgänglig datadisk till en virtuell dator som körs. 

1. Välj en virtuell dator som körs och som du vill ansluta en datadisk till.
1. Från menyn till vänster väljer du **diskar**.
1. Välj **bifoga befintlig** för att ansluta en tillgänglig datadisk till den virtuella datorn.

    ![Skärm bild som visar inställningen "diskar" markerad och "bifoga befintlig" vald.](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing-button.png)

1. I fönstret **bifoga befintlig disk** väljer du OK.

    ![Koppla befintlig datadisk till en virtuell dator](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Efter en liten stund kopplas data disken till den virtuella datorn och visas i listan över **data diskar** för den virtuella datorn.

## <a name="detach-a-data-disk"></a>Koppla ifrån en datadisk
När du inte längre behöver en datadisk som är ansluten till en virtuell dator kan du enkelt koppla bort den. Vid från koppling tas disken bort från den virtuella datorn, men den behålls i lagring för senare användning.

Om du vill använda befintliga data på disken igen kan du ansluta den till samma virtuella dator eller till en annan.

### <a name="detach-from-the-vms-management-pane"></a>Koppla från den virtuella datorns hanterings fönster
1. Välj en virtuell dator som har en ansluten datadisk i listan över virtuella datorer.
1. Från menyn till vänster väljer du **diskar**.
1. I listan över **data diskar** väljer du den datadisk som du vill koppla från.

    ![Välj data diskar för en virtuell dator](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-button.png) 
1. Välj **koppla** från överst i diskens informations fönster.

    ![Skärm bild som visar en disks informations fönster med åtgärden "koppla från" markerad.](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Välj **Ja** för att bekräfta att du vill koppla bort data disken.

Disken är frånkopplad och kan anslutas till en annan virtuell dator. 
### <a name="detach-from-the-labs-main-pane"></a>Koppla från Labbets huvud fönster
1. I ditt labbs huvud fönster väljer du **mina data diskar**.
1. Högerklicka på den datadisk som du vill koppla från – eller Välj dess ellips (**...**) – och välj sedan **Koppla från**.

    ![Koppla ifrån en datadisk](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Välj **Ja** för att bekräfta att du vill koppla bort den.

   > [!NOTE]
   > Om en datadisk redan är frånkopplad kan du välja att ta bort den från listan över tillgängliga data diskar genom att välja **ta bort**.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Uppgradera en ohanterad data disk
Om du har en befintlig virtuell dator som använder ohanterade data diskar kan du enkelt konvertera den virtuella datorn så att den använder hanterade diskar. Den här processen konverterar både OS-disken och anslutna data diskar.

Om du vill uppgradera en ohanterad data disk följer du stegen som beskrivs i den här artikeln för att [Koppla bort data disken](#detach-a-data-disk) från en OHANTERAD virtuell dator. Återanslut sedan [disken](#attach-an-existing-disk) till en HANTERAD virtuell dator för att automatiskt uppgradera data disken från ohanterad till hanterad.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du hanterar data diskar för [virtuella datorer som kan krävas](devtest-lab-add-claimable-vm.md#unclaim-a-vm).
