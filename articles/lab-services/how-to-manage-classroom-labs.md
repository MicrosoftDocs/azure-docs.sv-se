---
title: Hantera labb i Azure Lab Services | Microsoft Docs
description: Lär dig hur du skapar och konfigurerar ett klassrumslabb, visar alla labb, delar registreringslänken med en labbanvändare eller tar bort ett labb.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: c6acb9609abac15b9ff92250e3d5d44c585881cc
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481811"
---
# <a name="manage-labs-in-azure-lab-services"></a>Hantera labb i Azure Lab Services 
Den här artikeln beskriver hur du skapar och tar bort ett klassrumslabb. Den visar också hur du visar alla labb i ett labbkonto. 

## <a name="prerequisites"></a>Förutsättningar
Om du vill konfigurera ett klassrumslabb i ett labbkonto måste du vara medlem i rollen **Lab Creator** i labbkontot. Det konto som du använde för att skapa ett labbkonto läggs automatiskt till i den här rollen. Labbägaren kan lägga till andra användare till rollen Lab Creator genom att använda stegen i följande artikel: [Add a user to the Lab Creator role](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) (Lägg till en användare till rollen Lab Creator).

## <a name="create-a-classroom-lab"></a>Skapa ett klassrumslabb

1. Gå till [webbplatsen för Azure Lab Services](https://labs.azure.com).
1. Välj **Logga in** och ange dina autentiseringsuppgifter. Välj eller ange ett **användar-ID** som är medlem i **rollen Labbskapare** i labbkontot och ange lösenord. Azure Lab Services har stöd för organisationskonton och Microsoft-konton. 
1. Välj **Nytt labb.** 
    
    ![Skapa ett klassrumslabb](./media/tutorial-setup-classroom-lab/new-lab-button.png)
1. Utför följande i fönstret **Nytt labb**: 
    1. Ange ett **namn** på ditt labb. 
    1. Välj storleken **på de virtuella datorer** som du behöver för klassen. En lista över tillgängliga storlekar finns i avsnittet [VM-storlekar.](#vm-sizes) 
    1. Välj den **virtuella datoravbildning** som du vill använda för klassrumslabbet. Om du väljer en Linux-avbildning visas ett alternativ för **att aktivera anslutning till fjärrskrivbord.** Mer information finns i [Aktivera anslutning till fjärrskrivbord för Linux.](how-to-enable-remote-desktop-linux.md)

        Om du har loggat in med autentiseringsuppgifterna som labbkontoägare visas ett alternativ för att aktivera fler bilder för labbet. Mer information finns i Aktivera [avbildningar när labbet skapas.](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation)
    1. Granska det **totala priset per timme** som visas på sidan. 
    1. Välj **Spara**.

        ![Skärmbild som visar fönstret "Nytt labb".](./media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > Du ser ett alternativ för att välja en plats för labbet om labbkontot har konfigurerats för att tillåta att [labbskaparen väljer alternativet för labbplats.](allow-lab-creator-pick-lab-location.md) 
4. På sidan **Autentiseringsuppgifter för virtuell** dator anger du standardautentiseringsuppgifter för alla virtuella datorer i labbet.
    1. Ange **namnet på användaren** för alla virtuella datorer i labbet.
    2. Ange **lösenordet** för användaren. 

        > [!IMPORTANT]
        > Anteckna namnet och lösenordet. De kommer inte att visas igen.
    3. Inaktivera **alternativet Använd samma lösenord för alla virtuella** datorer om du vill att eleverna ska ange sina egna lösenord. Det här steget är **valfritt.** 

        En lärare kan välja att använda samma lösenord för alla virtuella datorer i labbet eller tillåta elever att ange lösenord för sina virtuella datorer. Som standard är den här inställningen aktiverad för alla Windows- och Linux-avbildningar utom Ubuntu. När du väljer **Virtuell Ubuntu-dator** inaktiveras den här inställningen, så eleverna uppmanas att ange ett lösenord när de loggar in för första gången.  

        ![Fönstret Nytt labb](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. Välj sedan Nästa **på** sidan **Autentiseringsuppgifter för virtuell** dator. 
5. Gör **följande på** sidan Labbprinciper:
    1. Ange antalet timmar som tilldelas varje användare (kvot **för varje användare) utanför** den schemalagda tiden för labbet. 
    2. För alternativet **Automatisk avstängning av virtuella datorer anger du** om du vill att den virtuella datorn ska stängas av automatiskt när användaren kopplar från. Du kan också ange hur länge den virtuella datorn ska vänta på att användaren ska återansluta innan den stängs av automatiskt. Mer information finns i Aktivera [automatisk avstängning av virtuella datorer vid frånkoppling.](how-to-enable-shutdown-disconnect.md)
    3. Välj sedan **Slutför**. 

        ![Kvot för varje användare](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. Du bör se följande skärm som visar status för skapandet av mallen för den virtuella datorn. Det tar upp till 20 minuter att skapa mallen i labbet. 

    ![Status för skapandet av den virtuella malldatorn](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Gör följande **på** sidan Mall: De här stegen är **valfria för** självstudien.

    1. Anslut till mallen för den virtuella datorn genom att välja **Anslut**. Om det är en virtuell Linux-mall väljer du om du vill ansluta med SSH eller ett grafiskt användargränssnitt.  Ytterligare installation krävs för att använda ett grafiskt användargränssnitt. Mer [information finns i Aktivera grafiskt fjärrskrivbord för virtuella Linux-datorer.](how-to-use-remote-desktop-linux-student.md)
    1. Välj **Återställ lösenord för** att återställa lösenordet för den virtuella datorn. 
    1. Installera och konfigurera programvaran på mallen för den virtuella datorn. 
    1. **Stoppa** den virtuella datorn.  
    1. Ange en **beskrivning** för mallen
9.  På **sidan** Mall väljer **du** Publicera i verktygsfältet. 

    ![Knappen Publicera mall](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > När du väl har publicerat kan du inte ångra publiceringen. 
10. På sidan **Publicera mall** anger du antalet virtuella datorer som du vill skapa i labbet och väljer sedan **Publicera.** 

    ![Publicera mall – antal virtuella datorer](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Du ser **statusen för att** publicera mallen på sidan. Den här processen kan ta upp till en timma. 

    ![Publicera mall – förlopp](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Växla till sidan **Pool för virtuella** datorer genom att välja Virtuella datorer på den vänstra menyn eller genom att välja panelen Virtuella datorer. Bekräfta att du ser virtuella datorer som är i tillståndet **Otilldelad**. De här virtuella datorerna har inte tilldelats till studenter ännu. De måste vara i tillstånd **Stoppad**. Du kan starta en virtuell dator för studenter, ansluta till den virtuella datorn, stoppa den virtuella datorn och ta bort den virtuella datorn på den här sidan. Du kan starta dem på den här sidan eller låta studenterna starta de virtuella datorerna. 

    ![Virtuella datorer i stoppat tillstånd](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Du utför följande uppgifter på den här sidan (utför inte de här stegen för självstudien. De här stegen är endast för din information.): 
    
    1. Om du vill ändra labbkapaciteten (antalet virtuella datorer i labbet) väljer **du Labbkapacitet** i verktygsfältet.
    2. Om du vill starta alla virtuella datorer samtidigt väljer **du Starta alla** i verktygsfältet. 
    3. Om du vill starta en specifik virtuell dator väljer du **nedåtpilen i Status** och väljer sedan **Starta.** Du kan också starta en virtuell dator genom att välja en virtuell dator i den första kolumnen och sedan genom att **välja Starta** i verktygsfältet.                

### <a name="vm-sizes"></a>VM-storlekar  

| Storlek | Kärnor | RAM | Beskrivning | 
| ---- | ----- | --- | ----------- | 
| Liten | 2 | 3,5 GB | Den här storleken passar bäst för kommandorad, öppna webbläsare, webbservrar med låg trafik, små till medelstora databaser. |
| Medel | 4 | 7 GB | Den här storleken passar bäst för relationsdatabaser, cachelagring i minnet och analys | 
| Medel (kapslad virtualisering) | 4 | 16 GB | Den här storleken passar bäst för relationsdatabaser, minnescachelagring och analys. Den här storleken stöder även kapslad virtualisering. <p>Den här storleken kan användas i scenarier där varje elev behöver flera virtuella datorer. Lärare kan använda kapslad virtualisering för att konfigurera några små kapslade virtuella datorer i den virtuella datorn. </p> |
| Liten GPU (beräkning) | 6 | 56 GB | <p>Den här storleken passar bäst för beräkningsintensiva och nätverksintensiva program som artificiell intelligens och djupinlärningsprogram.</p><p>Azure Lab Services installerar och konfigurerar automatiskt nödvändiga GPU-drivrutiner åt dig när du skapar ett labb med GPU-avbildningar. </p> | 
| Liten GPU (visualisering) | 6 | 56 GB | Den här storleken passar bäst för fjärrvisualisering, strömning, spel och kodning med ramverk som OpenGL och DirectX. | 
| Stor | 8 | 16 GB | Den här storleken passar bäst för program som behöver snabbare processorer, bättre prestanda för lokala diskar, stora databaser, stora minnescacheminnen. |
| Stor (kapslad virtualisering) | 8 | 32 GB | Den här storleken passar bäst för program som behöver snabbare processorer, bättre prestanda för lokala diskar, stora databaser, stora minnescacheminnen. Den här storleken stöder även kapslad virtualisering. |  
| Medelstor GPU (visualisering) | 12 | 112 GB | Den här storleken passar bäst för fjärrvisualisering, strömning, spel och kodning med ramverk som OpenGL och DirectX. | 

> [!NOTE]
> Du kanske inte ser några av dessa VM-storlekar i listan när du skapar ett klassrumslabb. Listan fylls i baserat på den aktuella kapaciteten för labbets plats. Om [labbkontots](allow-lab-creator-pick-lab-location.md)skapare tillåter att labbskapare väljer en plats för labbet kan du försöka välja en annan plats för labbet och se om VM-storleken är tillgänglig. 

## <a name="view-all-labs"></a>Visa alla labb

1. Gå till [Azure Lab Services portal](https://labs.azure.com).
1. Välj **Logga in**. Välj eller ange ett **användar-ID** som är medlem i rollen **Labbskapare** i labbkontot och ange lösenord. Azure Lab Services har stöd för organisationskonton och Microsoft-konton. 

    [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]
1. Bekräfta att du ser alla labb i det valda labbkontot. På labbets panel ser du antalet virtuella datorer i labbet och kvoten för varje användare (utanför den schemalagda tiden).

    ![Alla labb](./media/how-to-manage-classroom-labs/all-labs.png)
1. Använd listrutan längst upp för att välja ett annat labbkonto. Du ser labb i det valda labbkontot. 

## <a name="delete-a-classroom-lab"></a>Ta bort ett klassrumslabb

1. På panelen för labbet väljer du tre punkter (...) i hörnet och väljer sedan Ta **bort**. 

    ![Knappen Ta bort](./media/how-to-manage-classroom-labs/delete-button.png)
1. I dialogrutan **Ta bort labb** väljer du Ta **bort för** att fortsätta med borttagningen. 

## <a name="switch-to-another-classroom-lab"></a>Växla till ett annat klassrumslabb

Om du vill växla till ett annat klassrumslabb från det aktuella väljer du listrutan med labb i labbkontot högst upp.

![Välj labbet i listrutan längst upp](./media/how-to-manage-classroom-labs/switch-lab.png)

Du kan också skapa ett nytt labb med **hjälp av det nya labbet** i den här listrutan. 

> [!NOTE]
> Du kan också använda PowerShell-modulen Az.LabServices (förhandsversion) för att hantera labb. Mer information finns på [startsidan för Az.LabServices på GitHub.](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)

Om du vill växla till ett annat labbkonto väljer du listrutan bredvid labbkontot och väljer det andra labbkontot. 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
- [Som labbanvändare kan du komma åt labb](how-to-use-classroom-lab.md)

