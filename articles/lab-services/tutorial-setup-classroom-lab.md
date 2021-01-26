---
title: Konfigurera ett klassrumslabb med Azure Lab Services | Microsoft Docs
description: I den här självstudien använder du Azure Lab Services för att skapa ett klass rums labb med virtuella datorer som används av studenter i din klass.
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: 8093a1fd270cdba8bdccaf48737bf6737bdd394d
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787425"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Självstudie: Konfigurera ett klassrumslabb 
I självstudien konfigurerar du ett klassrumslabb med virtuella datorer som används av eleverna i klassrummet.  

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa ett klassrumslabb
> * Lägga till användare i labbet
> * Ange schema för labbet
> * Skicka inbjudan via e-post till studenter

## <a name="prerequisites"></a>Krav
I den här självstudien skapar du ett labb med virtuella datorer för din klass. Om du vill konfigurera ett klass rums labb i ett labb konto måste du vara medlem i någon av dessa roller i labb kontot: ägare, labb skapare eller deltagare. Det konto som du använde för att skapa ett labbkonto läggs automatiskt till i ägarrollen. Så du kan använda det användar konto som du använde för att skapa ett labb konto för att skapa ett klass rums labb. 

Här är det vanligaste arbets flödet när du använder Azure Lab Services:

1. Ett labb konto skapare lägger till andra användare i **labb skaparen** -rollen. Labb kontots skapare/administratör lägger till exempel till lärare till **labb skapare** rollen så att de kan skapa labb för sina klasser. 
2. Sedan skapar läraren labb med virtuella datorer för sina klasser och skickar registrerings länkar till studenter i klassen. 
3. Eleverna använder registrerings länken som de får från lärare att registrera sig på labbet. När de har registrerats kan de använda virtuella datorer i labbarna för att utföra klass- och hemarbete. 

## <a name="create-a-classroom-lab"></a>Skapa ett klassrumslabb
I det här steget skapar du ett labb för din klass i Azure. 

1. Gå till [webbplatsen för Azure Lab Services](https://labs.azure.com). Observera att Internet Explorer 11 inte stöds ännu. 
2. Välj **Logga in** och ange dina autentiseringsuppgifter. Azure Lab Services har stöd för organisationskonton och Microsoft-konton. 
3. Välj **nytt labb**. 
    
    ![Skärm bild som visar "Azure Lab Services" med knappen "nytt labb" valt.](./media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Utför följande i fönstret **Nytt labb**: 
    1. Ange ett **namn** på labbet och välj **Nästa**.  

        ![Skapa ett klassrumslabb](./media/tutorial-setup-classroom-lab/new-lab-window.png)
    2. På sidan **autentiseringsuppgifter för virtuell dator** anger du standardautentiseringsuppgifterna för alla virtuella datorer i labbet. Ange **namn** och **lösen ord** för användaren och välj sedan **Nästa**.  

        ![Nytt labb fönster](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Anteckna namnet och lösenordet. De kommer inte att visas igen.
    3. På sidan **labb principer** väljer du **Slutför**. 

        ![Kvot för varje användare](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Du bör se följande skärm bild som visar status för den mall för att skapa mallen. Den här åtgärden tar upp till 20 minuter. 

    ![Status för skapande av mall för virtuell dator](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Utför följande steg på sidan **mall** : de här stegen är **valfria** för självstudierna.

    1. Anslut till mallen för den virtuella datorn genom att välja **Anslut**. Om det är en virtuell Linux-mall kan du välja om du vill ansluta med SSH eller RDP (om RDP är aktiverat).
    3. Installera och konfigurera program vara som krävs för din klass på den virtuella dator mal len. 
    4. **Stoppa** mallen VM.  

    > [!NOTE]
    > Mall VM: ar **kostar** att köra, så se till att den virtuella datorns mall stängs av när du inte behöver den för att köras. 

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

    > [!NOTE]
    > När en lärare aktive ras på en elev-VM påverkas inte kvoten för studenten. Kvoten för en användare anger antalet Labb timmar som är tillgängliga för användaren utanför den schemalagda klass tiden. Mer information om kvoter finns i [Ange kvoter för användare](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="set-a-schedule-for-the-lab"></a>Ange ett schema för labbet
Skapa en schemalagd händelse för labbet så att virtuella datorer i labbet startas/stoppas automatiskt vid vissa tidpunkter. Användar kvoten (standard 10 timmar) som du angav tidigare är den ytterligare tid som tilldelats varje användare utanför den schemalagda tiden. 

1. Växla till sidan **scheman** och välj **Lägg till schemalagd händelse** i verktygsfältet. 

    ![Skärm bild som visar knappen "Lägg till schemalagd händelse" på sidan "scheman".](./media/how-to-create-schedules/add-schedule-button.png)
2. Utför följande steg på sidan **Lägg till schemalagd händelse** :
    1. Bekräfta att **standard** är valt **händelse typ**.  
    2. Välj **start datum** för klassen. 
    4. Välj **Start tiden** då du vill att de virtuella datorerna ska startas.
    5. Välj **stopp tiden** som de virtuella datorerna ska stängas av. 
    6. Välj tidszon **för start** -och stopp tider som du har angett. 
3. På sidan **Lägg till schemalagd händelse** väljer du det aktuella schemat i avsnittet **Upprepa** .  

    ![Knappen Lägg till schema på sidan scheman](./media/how-to-create-schedules/select-current-schedule.png)
5. Utför följande steg i dialog rutan **Upprepa** :
    1. Bekräfta att **varje vecka** har angetts för fältet **Upprepa** . 
    2. Välj de dagar som du vill att schemat ska börja gälla. I följande exempel är Monday-Friday markerat. 
    3. Välj ett **slutdatum** för schemat.
    8. Välj **Spara**. 

        ![Ange upprepnings schema](./media/how-to-create-schedules/set-repeat-schedule.png)

3. På sidan **Lägg till schemalagd händelse** för **anteckningar (valfritt)** anger du eventuellt en beskrivning eller information om schemat. 
4. På sidan **Lägg till schemalagd händelse** väljer du **Spara**. 

    ![Vecko schema](./media/how-to-create-schedules/add-schedule-page-weekly.png)
5. Kontrol lera att schemat har angetts genom att gå till Start datumet i kalendern.
    
    ![Schemalägg i kalendern](./media/how-to-create-schedules/schedule-calendar.png)

    Mer information om hur du skapar och hanterar scheman för en klass finns i [skapa och hantera schema för labb](how-to-create-schedules.md).


## <a name="add-users-to-the-lab"></a>Lägga till användare i labbet

När du lägger till användare är som standard alternativet **begränsa åtkomst** aktive rad och, om de inte finns med i listan över användare, kan eleverna inte registrera sig med labbet även om de har en registrerings länk. Endast listade användare kan registrera sig i labbet med hjälp av den registrerings länk som du skickar. Du kan inaktivera **begränsa åtkomst**, vilket gör att studenter kan registrera sig för labbet så länge de har registrerings länken. 

### <a name="add-users-from-an-azure-ad-group"></a>Lägga till användare från en Azure AD-grupp

Du kan synkronisera en labb användar lista med en befintlig Azure Active Directory (Azure AD)-grupp så att du inte behöver lägga till eller ta bort användare manuellt. 

En Azure AD-grupp kan skapas i din organisations Azure Active Directory för att hantera åtkomst till organisations resurser och molnbaserade appar. Mer information finns i [Azure AD-grupper](../active-directory/fundamentals/active-directory-manage-groups.md). Om din organisation använder Microsoft Office 365 eller Azure-tjänster, kommer din organisation redan ha administratörer som hanterar din Azure Active Directory. 

> [!IMPORTANT]
> Se till att användar listan är tom. Om det finns befintliga användare i ett labb som du har lagt till manuellt eller genom att importera en CSV-fil, visas inte alternativet för att synkronisera labbet med en befintlig grupp. 

1. I det vänstra fönstret väljer **du användare**. 
1. Klicka på **Synkronisera från grupp**. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-sync-group.png" alt-text="Lägg till användare genom att synkronisera från en Azure AD-grupp":::
    
1. Du uppmanas att välja en befintlig Azure AD-grupp för att synkronisera ditt labb med. 
    
    Om du inte ser en Azure AD-grupp i listan kan det bero på följande orsaker:

    -   Om du är gäst användare för en Azure Active Directory (vanligt vis om du befinner dig utanför organisationen som äger Azure AD) och du inte kan söka efter grupper i Azure AD. I det här fallet kan du inte lägga till en Azure AD-grupp i labbet i det här fallet. 
    -   Azure AD-grupper som skapats via team visas inte i den här listan. Du kan lägga till Azure Lab Services-appen i Teams för att skapa och hantera labb direkt inifrån. Se mer information om att [hantera en användar lista för labb inifrån Teams](how-to-manage-user-lists-within-teams.md). 
1. När du har valt Azure AD-gruppen att synkronisera labbet med klickar du på **Lägg till**.
1. När ett labb har synkroniserats, tar det emot alla i Azure AD-gruppen i labbet som användare och du ser att användar listan har uppdaterats. Endast personer i den här Azure AD-gruppen får åtkomst till ditt labb. Användar listan uppdateras var 24: e timme för att matcha det senaste medlemskapet i Azure AD-gruppen. Du kan också klicka på knappen Synkronisera på fliken användare för att synkronisera manuellt med de senaste ändringarna i Azure AD-gruppen.
1. Bjud in användarna till ditt labb genom att klicka på knappen **Bjud in alla** , som kommer att skicka ett e-postmeddelande till alla användare med registrerings länken till labbet. 

### <a name="add-users-manually-from-emails-or-csv-file"></a>Lägga till användare manuellt från e-post (er) eller CSV-fil

I det här avsnittet lägger du till studenter manuellt (via e-postadress eller genom att ladda upp en CSV-fil). 

#### <a name="add-users-by-email-address"></a>Lägg till användare via e-postadress

1. I det vänstra fönstret väljer **du användare**. 
1. Klicka på **Lägg till användare manuellt**. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-manually.png" alt-text="Lägg till användare manuellt":::
1. Välj **Lägg till per e-postadress** (standard), ange elevernas e-postadresser på separata rader eller på en enda rad som avgränsas med semikolon. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-email-addresses.png" alt-text="Lägg till användares e-postadresser":::
1. Välj **Spara**. 

    I listan visas e-postadresser och status för de aktuella användarna, oavsett om de är registrerade i labbet eller inte. 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="Användarlista":::

    > [!NOTE]
    > När eleverna har registrerats i labbet visas deras namn i listan. Namnet som visas i listan skapas med hjälp av de första och sista namnen på eleverna i Azure Active Directory. 

#### <a name="add-users-by-uploading-a-csv-file"></a>Lägg till användare genom att ladda upp en CSV-fil

Du kan också lägga till användare genom att ladda upp en CSV-fil som innehåller sina e-postadresser. 

En CSV-textfil används för att lagra kommaavgränsade tabell data (siffror och text). I stället för att lagra information i kolumn fält (till exempel i kalkyl blad) lagrar en CSV-fil information som avgränsas med kommatecken. Varje rad i en CSV-fil kommer att ha samma antal kommaavgränsade "fält". Du kan använda Excel för att enkelt skapa och redigera CSV-filer.

1. I Microsoft Excel skapar du en CSV-fil med en lista över studenters e-postadresser i en kolumn.

    :::image type="content" source="./media/how-to-configure-student-usage/csv-file-with-users.png" alt-text="Lista med användare i en CSV-fil":::
1. Välj **Lägg till användare** längst upp i fönstret **användare** och välj sedan **Ladda upp CSV**.
1. Välj den CSV-fil som innehåller elevernas e-postadresser och välj sedan **Öppna**.

    I fönstret **Lägg till användare** visas listan med e-POSTADRESSER från CSV-filen. 
1. Välj **Spara**. 
1. I fönstret **användare** kan du se listan med tillagda studenter. 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="Lista över tillagda användare i fönstret användare"::: 

## <a name="send-invitation-emails-to-users"></a>Skicka inbjudningar via e-post till användare

1. Växla till vyn **användare** om du inte redan är på sidan och välj **Bjud in alla** i verktygsfältet. 

    ![Välj studenter](./media/tutorial-setup-classroom-lab/invite-all-button.png)
1. På sidan **Skicka inbjudan via e-post** anger du ett valfritt meddelande och väljer sedan **Skicka**. E-postmeddelandet innehåller automatiskt registrerings länken. Du kan hämta den här registrerings länken genom att välja **... (tre punkter)** i verktygsfältet och **registrerings länken**. 

    ![Skicka registrerings länk via e-post](./media/tutorial-setup-classroom-lab/send-email.png)
4. Du ser status för **inbjudan** i listan **användare** . Statusen bör ändras till att **skickas** och sedan **skickas till &lt; datum &gt;**. 

Mer information om hur du lägger till studenter i en klass och hur du hanterar användningen av labbet finns i [så här konfigurerar du elev användning](how-to-configure-student-usage.md).

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du skapat ett labb för din klass i Azure. Om du vill veta hur en elev kan få åtkomst till en virtuell dator i labbet med hjälp av registreringslänken, går du vidare till nästa självstudie:

> [!div class="nextstepaction"]
> [Ansluta till en virtuell dator i klassrumslabbet](tutorial-connect-virtual-machine-classroom-lab.md)