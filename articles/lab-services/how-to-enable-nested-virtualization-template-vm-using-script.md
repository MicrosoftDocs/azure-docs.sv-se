---
title: Aktivera kapslad virtualisering på en virtuell mall i Azure Lab Services (skript) | Microsoft Docs
description: Lär dig hur du skapar en mall för virtuella datorer med flera virtuella datorer i med hjälp av ett skript.  Med andra ord aktiverar du kapslad virtualisering på en virtuell mall i Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5ae50bd11ab9a8adb769920f6d473a2ff2ce9342
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91251503"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>Aktivera kapslad virtualisering på en virtuell mall i Azure Lab Services att använda ett skript

Kapslad virtualisering gör att du kan skapa en miljö med flera virtuella datorer i en labbs mall för virtuella datorer. Genom att publicera mallen får du varje användare i labbet med en virtuell dator som är konfigurerad med flera virtuella datorer i den.  Mer information om kapslad virtualisering och Azure Lab Services finns i [Aktivera kapslad virtualisering på en mall virtuell dator i Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

Stegen i den här artikeln fokuserar på att konfigurera kapslad virtualisering för Windows Server 2016, Windows Server 2019 eller Windows 10. Du kommer att använda ett skript för att konfigurera en mall för datorer med Hyper-V.  Följande steg beskriver hur du använder [Hyper-V-skript i labb tjänster](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV).

>[!IMPORTANT]
>Välj **stor (kapslad virtualisering)** eller **medium (kapslad virtualisering)** för den virtuella dator storleken när du skapar labbet.  Kapslad virtualisering kommer inte att fungera på annat sätt.  

## <a name="run-script"></a>Köra skriptet

1. Om du använder Internet Explorer kan du behöva lägga till `https://github.com` i listan över betrodda platser.
    1. Öppna Internet Explorer.
    1. Välj kugg hjuls ikonen och välj **Internet alternativ**.  
    1. När dialog rutan **Internet alternativ** visas väljer du **säkerhet**, Välj **Betrodda platser**, klicka på **platser** .
    1. När dialog rutan **Betrodda platser** visas, Lägg till `https://github.com` i listan betrodda webbplatser och välj **Stäng**.

        ![Betrodda platser](./media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. Hämta git-databasfilerna som beskrivs i följande steg.
    1. Gå till  [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/) .
    1. Klicka på knappen **klona eller hämta** .
    1. Klicka på **Hämta zip**.
    1. Extrahera ZIP-filen

    >[!TIP]
    >Du kan också klona git-lagringsplatsen på [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git) .

1. Starta **PowerShell** i **Administratörs** läge.
1. I PowerShell-fönstret navigerar du till mappen med det nedladdade skriptet. Om du navigerar från den översta mappen i databasfilerna finns skriptet på `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\` .
1. Du kanske måste ändra körnings principen för att kunna köra skriptet. Kör följande kommando:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. Kör skriptet:

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > Skriptet kan kräva att datorn startas om. Följ instruktionerna från skriptet och kör skriptet igen tills **skriptet har slutförts** i utdata.
1. Glöm inte att återställa körnings principen. Kör följande kommando:

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Slutsats

Nu är din mall maskin redo att skapa virtuella Hyper-V-datorer. Instruktioner om hur du skapar virtuella Hyper-V-datorer finns i [skapa en virtuell dator i Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) . Se även [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) för att ta en titt på tillgängliga operativ system och program.  

## <a name="next-steps"></a>Nästa steg

Nästa steg är vanliga för att ställa in alla labb.

- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-postregistrering länkar till studenter](how-to-configure-student-usage.md#send-invitations-to-users)