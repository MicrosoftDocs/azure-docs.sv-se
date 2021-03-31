---
title: Konfigurera inställningar för Autostart för en virtuell dator i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du konfigurerar inställningar för Autostart för virtuella datorer i ett labb. Med den här inställningen kan virtuella datorer i labbet startas automatiskt enligt ett schema.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 83e7b0836273a59eaaf66471bd0cb42d63ccf1c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91328490"
---
# <a name="auto-startup-lab-virtual-machines"></a>Virtuella datorer för automatisk start av labb  
Med Azure DevTest Labs kan du konfigurera virtuella datorer i labbet så att de startas och stängs av automatiskt baserat på ett schema. Information om hur du konfigurerar inställningar för automatisk avstängning finns i [Hantera principer för automatisk avstängning för ett labb i Azure DevTest Labs](devtest-lab-auto-shutdown.md). 

Till skillnad från automatisk avstängning, där alla virtuella datorer ingår när principen är aktive rad, kräver principen för automatisk start en labb användare att uttryckligen välja en virtuell dator och välja det här schemat. På så sätt kan du inte enkelt köra i situationen där oönskade virtuella datorer oavsiktligt startas automatiskt och orsakar oväntade utgifter.

Den här artikeln visar hur du konfigurerar Autostart-principen för ett labb.

## <a name="configure-autostart-settings-for-a-lab"></a>Konfigurera inställningar för Autostart för ett labb 
1. Gå till start sidan för ditt labb. 
2. Välj **konfiguration och principer** på den vänstra menyn. 

    ![Skärm bild som visar menyn "konfiguration och principer" i DevTest-labbet.](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. Utför följande steg på sidan **konfiguration och principer** :
    
    1. Välj **på** för att **tillåta att virtuella datorer schemaläggs för automatisk start** för att aktivera funktionen för automatisk start för det här labbet. 
    2. Välj en start tid (till exempel: 8:00:00 AM) för fältet **schemalagd start** . 
    3. Välj en **tidszon** som ska användas. 
    4. Välj **vecko dagar som de** virtuella datorerna måste startas automatiskt. 
    5. Välj sedan **Spara** i verktygsfältet för att spara inställningarna. 

        ![Inställningar för Autostart](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > Den här principen tillämpar inte automatiskt start automatiskt på alla virtuella datorer i labbet. Om du vill **välja** enskilda virtuella datorer går du till sidan virtuell dator och aktiverar **automatisk start** för den virtuella datorn.

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>Aktivera Autostart för en virtuell dator i labbet
Med följande procedur kan du steg för väljer i en virtuell dator till den första start principen i labbet. 

1. På Start sidan för ditt labb väljer du den **virtuella datorn** i listan **mina virtuella datorer** . 

    ![Menyn konfiguration och principer](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. På sidan **virtuell dator** väljer du **Starta** automatiskt på den vänstra menyn eller i listan **scheman** . 

    ![Välj Autostart-menyn](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. På sidan **Autostart** **väljer du för alternativet** Tillåt att **den här virtuella datorn schemaläggs för automatisk start** .

    ![Aktivera Autostart för den virtuella datorn](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. Välj sedan **Spara** i verktygsfältet för att spara inställningen. 


## <a name="next-steps"></a>Nästa steg
Information om hur du konfigurerar en princip för automatisk avstängning för ett labb finns i [Hantera principer för automatisk avstängning för ett labb i Azure DevTest Labs](devtest-lab-auto-shutdown.md)
