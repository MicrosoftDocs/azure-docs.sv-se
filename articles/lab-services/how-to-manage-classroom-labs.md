---
title: Hantera labb i Azure Lab Services | Microsoft Docs
description: Lär dig hur du skapar och konfigurerar ett klass rums labb, visar alla labb, delar registrerings länken med en labb användare eller tar bort ett labb.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: db7a2f58f99252a7e5076dd86c37b65bbe8ea37a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434217"
---
# <a name="manage-labs-in-azure-lab-services"></a>Hantera labb i Azure Lab Services 
Den här artikeln beskriver hur du skapar och tar bort ett klass rums labb. Här visas också hur du visar alla labb i ett labb konto. 

## <a name="prerequisites"></a>Förutsättningar
Om du vill konfigurera ett klassrumslabb i ett labbkonto måste du vara medlem i rollen **Lab Creator** i labbkontot. Det konto som du använde för att skapa ett labbkonto läggs automatiskt till i den här rollen. Labbägaren kan lägga till andra användare till rollen Lab Creator genom att använda stegen i följande artikel: [Add a user to the Lab Creator role](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) (Lägg till en användare till rollen Lab Creator).

## <a name="create-a-classroom-lab"></a>Skapa ett klassrumslabb

1. Gå till [webbplatsen för Azure Lab Services](https://labs.azure.com). Internet Explorer 11 stöds inte ännu. 
1. Välj **Logga in** och ange dina autentiseringsuppgifter. Välj eller ange ett **användar-ID** som är medlem i rollen **labb skapare** i labb kontot och ange lösen ord. Azure Lab Services har stöd för organisationskonton och Microsoft-konton. 
1. Välj **nytt labb**. 
    
    ![Skapa ett klassrumslabb](./media/tutorial-setup-classroom-lab/new-lab-button.png)
1. Utför följande i fönstret **Nytt labb**: 
    1. Ange ett **namn** på ditt labb. 
    1. Välj **storlek på de virtuella datorer** som du behöver för klassen. En lista över tillgängliga storlekar finns i avsnittet [VM-storlekar](#vm-sizes) . 
    1. Välj den **avbildning av virtuell dator** som du vill använda för klass rums labbet. Om du väljer en Linux-avbildning ser du ett alternativ för att **aktivera anslutning till fjärr skrivbord**. Mer information finns i [aktivera anslutning till fjärr skrivbord för Linux](how-to-enable-remote-desktop-linux.md).

        Om du har loggat in med autentiseringsuppgifter för labb kontots ägare visas ett alternativ för att aktivera fler avbildningar för labbet. Mer information finns i [Aktivera avbildningar när labb skapas](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation).
    1. Granska det **totala priset per timme** som visas på sidan. 
    1. Välj **Spara**.

        ![Skärm bild som visar fönstret "nytt labb".](./media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > Du ser ett alternativ för att välja en plats för ditt labb om labb kontot har kon figurer ATS för att [tillåta labb skapare att välja alternativet labb plats](allow-lab-creator-pick-lab-location.md) . 
4. På sidan **autentiseringsuppgifter för virtuell dator** anger du standardautentiseringsuppgifterna för alla virtuella datorer i labbet.
    1. Ange **namnet på användaren** för alla virtuella datorer i labbet.
    2. Ange **lösenordet** för användaren. 

        > [!IMPORTANT]
        > Anteckna namnet och lösenordet. De kommer inte att visas igen.
    3. Inaktivera alternativet **Använd samma lösen ord för alla virtuella datorer** om du vill att eleverna ska ange sina egna lösen ord. Det här steget är **valfritt**. 

        En lärare kan välja att använda samma lösen ord för alla virtuella datorer i labbet eller tillåta studenter att ange lösen ord för sina virtuella datorer. Som standard är den här inställningen aktive rad för alla Windows-och Linux-avbildningar utom Ubuntu. När du väljer **Ubuntu** VM är den här inställningen inaktive rad, så att eleverna uppmanas att ange ett lösen ord när de loggar in för första gången.  

        ![Nytt labb fönster](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. Välj sedan **Nästa** på sidan **autentiseringsuppgifter för virtuella datorer** . 
5. Utför följande steg på sidan **labb principer** :
    1. Ange det antal timmar som tilldelas för varje användare (**kvot för varje användare**) utanför den schemalagda tiden för labbet. 
    2. För alternativet **Automatisk avstängning av virtuella datorer** anger du om du vill att den virtuella datorn ska stängas av automatiskt när användaren kopplar från. Du kan också ange hur länge den virtuella datorn ska vänta tills användaren ansluter igen innan den stängs av automatiskt. Mer information finns i [Aktivera automatisk avstängning av virtuella datorer vid från koppling](how-to-enable-shutdown-disconnect.md).
    3. Välj sedan **Slutför**. 

        ![Kvot för varje användare](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. Du bör se följande skärm bild som visar status för den mall för att skapa mallen. Det tar upp till 20 minuter att skapa mallen i labbet. 

    ![Status för skapande av mall för virtuell dator](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Utför följande steg på sidan **mall** : de här stegen är **valfria** för självstudierna.

    1. Anslut till mallen för den virtuella datorn genom att välja **Anslut**. Om det är en virtuell Linux-mall kan du välja om du vill ansluta med SSH eller ett användar gränssnitts fjärr skrivbord.  Det krävs ytterligare inställningar för att använda ett GUI-fjärrskrivbord. Mer information finns i [Aktivera grafiskt fjärr skrivbord för virtuella Linux-datorer](how-to-use-remote-desktop-linux-student.md) .
    1. Välj **Återställ lösen ord** för att återställa lösen ordet för den virtuella datorn. 
    1. Installera och konfigurera programvaran på mallen för den virtuella datorn. 
    1. **Stoppa** den virtuella datorn.  
    1. Ange en **beskrivning** för mallen
9.  Välj **publicera** i verktygsfältet på sidan **mall** . 

    ![Knappen publicera mall](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > När du väl har publicerat kan du inte ångra publiceringen. 
10. På sidan **publicera mall** anger du det antal virtuella datorer som du vill skapa i labbet och väljer sedan **publicera**. 

    ![Publicera mall – antal virtuella datorer](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Du ser **statusen för att publicera** mallen på sidan. Den här processen kan ta upp till en timma. 

    ![Publicera mall – förlopp](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Växla till sidan **Virtual Machines-pool** genom att välja virtuella datorer på den vänstra menyn eller genom att välja panelen virtuella datorer. Bekräfta att du ser virtuella datorer som är i tillståndet **Otilldelad**. De här virtuella datorerna har inte tilldelats till studenter ännu. De måste vara i tillstånd **Stoppad**. Du kan starta en virtuell dator för studenter, ansluta till den virtuella datorn, stoppa den virtuella datorn och ta bort den virtuella datorn på den här sidan. Du kan starta dem på den här sidan eller låta studenterna starta de virtuella datorerna. 

    ![Virtuella datorer i stoppat tillstånd](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Du utför följande åtgärder på den här sidan (utför inte de här stegen för självstudierna. De här stegen är endast för din information.): 
    
    1. Om du vill ändra labb kapaciteten (antal virtuella datorer i labbet) väljer du **labb kapacitet** i verktygsfältet.
    2. Om du vill starta alla virtuella datorer samtidigt väljer du **starta alla** i verktygsfältet. 
    3. Om du vill starta en speciell virtuell dator väljer du nedpilen i **status** och väljer sedan **Starta**. Du kan också starta en virtuell dator genom att välja en virtuell dator i den första kolumnen och sedan välja **Starta** i verktygsfältet.                

### <a name="vm-sizes"></a>VM-storlekar  

| Storlek | Kärnor | RAM | Beskrivning | 
| ---- | ----- | --- | ----------- | 
| Liten | 2 | 3,5 GB | Den här storleken passar bäst för kommando rad, öppna webbläsare, webb servrar med låg trafik, små till medel stora databaser. |
| Medel | 4 | 7 GB | Den här storleken passar bäst för Relations databaser, minnes intern cachelagring och analys | 
| Medium (kapslad virtualisering) | 4 | 16 GB | Den här storleken passar bäst för Relations databaser, minnes intern cachelagring och analys. Den här storleken stöder även kapslad virtualisering. <p>Den här storleken kan användas i scenarier där varje student behöver flera virtuella datorer. Lärare kan använda kapslad virtualisering för att konfigurera ett fåtal kapslade virtuella datorer med liten storlek inuti den virtuella datorn. </p> |
| Liten GPU (Compute) | 6 | 56 GB | <p>Den här storleken passar bäst för beräknings intensiva och nätverks intensiva program som artificiell intelligens och djup inlärnings program.</p><p>Azure Lab Services installerar och konfigurerar automatiskt de nödvändiga GPU-drivrutinerna åt dig när du skapar ett labb med GPU-avbildningar. </p> | 
| Liten GPU (visualisering) | 6 | 56 GB | Den här storleken passar bäst för fjärrvisualisering, strömning, spel, kodning med hjälp av ramverk som OpenGL och DirectX. | 
| Stor | 8 | 16 GB | Den här storleken lämpar sig bäst för program som behöver snabbare processorer, bättre prestanda för lokala diskar, stora databaser, stora cacheminnen. |
| Stor (kapslad virtualisering) | 8 | 32 GB | Den här storleken lämpar sig bäst för program som behöver snabbare processorer, bättre prestanda för lokala diskar, stora databaser, stora cacheminnen. Den här storleken stöder även kapslad virtualisering. |  
| Medelhög GPU (visualisering) | 12 | 112 GB | Den här storleken passar bäst för fjärrvisualisering, strömning, spel, kodning med hjälp av ramverk som OpenGL och DirectX. | 

> [!NOTE]
> Du kanske inte ser några av de här VM-storlekarna i listan när du skapar ett klass rums labb. Listan fylls i baserat på den aktuella kapaciteten för labbets plats. Om skaparen av labb kontot [gör det möjligt för labb skapare att välja en plats för labbet](allow-lab-creator-pick-lab-location.md)kan du prova att välja en annan plats för labbet och se om storleken på den virtuella datorn är tillgänglig. 

## <a name="view-all-labs"></a>Visa alla labb

1. Navigera till [Azure Lab Services Portal](https://labs.azure.com).
1. Välj **Logga in**. Välj eller ange ett **användar-ID** som är medlem i rollen **labb skapare** i labb kontot och ange lösen ord. Azure Lab Services har stöd för organisationskonton och Microsoft-konton. 

    [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]
1. Bekräfta att du ser alla labb i det valda labb kontot. På labb panelen ser du antalet virtuella datorer i labbet och kvoten för varje användare (utanför den schemalagda tiden).

    ![Alla labb](./media/how-to-manage-classroom-labs/all-labs.png)
1. Använd List rutan längst upp för att välja ett annat labb konto. Du ser labb i det valda labb kontot. 

## <a name="delete-a-classroom-lab"></a>Ta bort ett klass rums labb

1. I rutan för labbet väljer du tre punkter (...) i hörnet och väljer sedan **ta bort**. 

    ![Knappen Ta bort](./media/how-to-manage-classroom-labs/delete-button.png)
1. I dialog rutan **ta bort labb** väljer du **ta bort** för att fortsätta med borttagningen. 

## <a name="switch-to-another-classroom-lab"></a>Växla till ett annat klass rums labb

Om du vill växla till ett annat klass rums labb från den aktuella väljer du den nedrullningsbara listan med labb i labb kontot längst upp.

![Välj labbet i list rutan längst upp](./media/how-to-manage-classroom-labs/switch-lab.png)

Du kan också skapa ett nytt labb med hjälp av det **nya labbet** i den här List rutan. 

> [!NOTE]
> Du kan också använda PowerShell-modulen AZ. LabServices (för hands version) för att hantera labb. Mer information finns på [Start sidan för AZ. LabServices på GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

Om du vill växla till ett annat labb konto väljer du List rutan bredvid labb kontot och väljer det andra labb kontot. 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
- [Som labb användare, åtkomst labb](how-to-use-classroom-lab.md)

