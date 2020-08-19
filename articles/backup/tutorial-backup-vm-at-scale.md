---
title: Självstudie – Säkerhetskopiera flera virtuella Azure-datorer
description: I den här självstudien får du lära dig hur du skapar ett Recovery Services valv, definierar en säkerhets kopierings princip och samtidigt säkerhetskopierar flera virtuella datorer.
ms.date: 07/26/2020
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: ed91105488b812131d3a908c509998c955cec299
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263426"
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>Använda Azure Portal till att säkerhetskopiera flera virtuella datorer

När du säkerhetskopierar data i Azure lagrar du dessa data i en Azure-resurs som kallas för ett Recovery Services-valv. Recovery Services-valvresursen är tillgänglig på menyn Inställningar i de flesta Azure-tjänster. Fördelen med att använda det Recovery Services valvet som integreras i menyn Inställningar för de flesta Azure-tjänster är det enkelt att säkerhetskopiera data. Det är dock omständligt att arbeta individuellt med varje databas eller virtuell dator i din verksamhet. Vad händer om du vill säkerhetskopiera data för alla virtuella datorer på en avdelning eller på en plats? Det är enkelt att säkerhetskopiera flera virtuella datorer genom att skapa en säkerhets kopierings princip och tillämpa principen på önskade virtuella datorer. I den här självstudien beskrivs hur du:

> [!div class="checklist"]
>
> * skapar ett Recovery Services-valv
> * Definierar en säkerhetskopieringspolicy
> * applicerar säkerhetskopieringspolicyn för att skydda flera virtuella datorer
> * Utlösa ett säkerhetskopieringsjobb på begäran för de skyddade virtuella datorerna

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Recovery Services-valvet innehåller säkerhetsdata och säkerhetskopieringspolicyn som tillämpas på de skyddade virtuella datorerna. Säkerhetskopieringen av virtuella datorer är en lokal process. Du kan inte säkerhetskopiera en virtuell dator från en plats till ett Recovery Services-valv på en annan plats. För varje Azure-plats som har virtuella datorer som ska säkerhetskopieras måste det finnas minst ett Recovery Services-valv på platsen.

1. På menyn till vänster väljer du **Alla tjänster**.

    ![Välj Alla tjänster](./media/tutorial-backup-vm-at-scale/click-all-services.png)

1. I dialogrutan **Alla tjänster** anger du *Recovery Services*. Listan över resurser filtreras enligt dina inaktuella inaktuella. Välj **Recovery Services valv**i listan över resurser.

    ![Ange och välja Recovery Services-valv](./media/tutorial-backup-vm-at-scale/all-services.png)

    Listan över Recovery Services-valv i prenumerationen visas.

1. På instrument panelen för **Recovery Services valv** väljer du **Lägg till**.

    ![Lägg till ett Recovery Services-valv](./media/tutorial-backup-vm-at-scale/add-button-create-vault.png)

1. På menyn Recovery Services-valv

    * Skriv *myRecoveryServicesVault* i **Namn**.
    * Aktuellt prenumerations-ID visas i **Prenumeration**. Om du har ytterligare prenumerationer kan du välja en annan prenumeration för det nya valvet.
    * För **Resursgrupp** väljer du **Använd befintlig** och sedan *myResourceGroup*. Om *myResourceGroup* inte finns väljer du **Skapa ny** och skriver *myResourceGroup*.
    * Från den nedrullningsbara menyn **Plats** väljer du *Europa, västra*.

    ![Recovery Services valv värden](./media/tutorial-backup-vm-at-scale/review-and-create.png)

    Ett Recovery Services-valv måste vara på samma plats som de virtuella datorerna som skyddas. Om du har virtuella datorer i olika regioner skapar du ett Recovery Services-valv i varje region. I den här självstudien skapas ett Recovery Services-valv i *Europa, västra* eftersom det är där som *myVM* (den virtuella datorn som skapades med snabbstarten) skapades.

1. När du är redo att skapa Recovery Services-valvet väljer du **skapa**.

    ![Skapa Recovery Services-valvet](./media/tutorial-backup-vm-at-scale/click-create-button.png)

1. Det kan ta en stund att skapa Recovery Services-valvet. Övervaka status meddelanden i **meddelande** fältet i det övre högra hörnet i portalen. När valvet har skapats visas det i listan över Recovery Services-valv. Om du inte ser ditt valv väljer du **Uppdatera**.

     ![Uppdatera listan över säkerhets kopierings valv](./media/tutorial-backup-vm-at-scale/refresh-button.png)

När du skapar ett Recovery Services-valv får valvet som standard geo-redundant lagring. För att tillhandahålla dataåterhämtning replikerar geo-redundant lagring data flera gånger för två Azure-regioner.

## <a name="set-backup-policy-to-protect-vms"></a>Ställa in säkerhetskopieringspolicy för att skydda virtuella datorer

När du har skapat Recovery Services-valvet är nästa steg att konfigurera valvet för typ av data och att ange en säkerhetskopieringspolicy. Säkerhetsprincipen är schemat för hur ofta återställningspunkter skapas. Principen omfattar också kvarhållningsintervallet för återställningspunkterna. I den här självstudien antar vi att ditt företag är ett idrotts komplex med ett hotell, Stadium och restauranger och koncessioner, och att du skyddar data på de virtuella datorerna. Med följande steg skapas en säkerhetskopieringspolicy för finansiella data.

1. Från listan med Recovery Services-valv väljer du **myRecoveryServicesVault** för att öppna valvets instrumentpanel.

   ![Öppna menyn Scenario](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

1. På instrument panelen för valv väljer du **säkerhets kopiering** för att öppna säkerhets kopierings menyn.

1. På menyn Säkerhetskopieringsmål väljer du *Azure* i listrutan **Var körs din arbetsbelastning**. I list rutan **vad vill du säkerhetskopiera? väljer du** *virtuell dator*och sedan **säkerhets kopiering**.

    Dessa åtgärder förbereder Recovery Services-valvet för att interagera med en virtuell dator. Recovery Services-valv har en standardprincip som skapar en återställningspunkt varje dag och behåller återställningspunkterna under 30 dagar.

    ![Säkerhets kopierings mål](./media/tutorial-backup-vm-at-scale/backup-goal.png)

1. Om du vill skapa en ny princip går du till menyn säkerhets kopierings policy och väljer *skapa en ny princip*på den nedrullningsbara menyn **Välj säkerhets kopierings policy** .

    ![Skapa ny princip](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

1. Fönstret **säkerhets kopierings princip** öppnas. Fyll i följande information:
   * För **princip namn** skriver du *ekonomi*. Ange följande ändringar för säkerhetskopieringspolicyn:
   * För **Säkerhetskopieringsfrekvens** ställer du in tidszon på *Central Time*. Eftersom sportkomplexet ligger i Texas vill ägaren att tidsinställningen ska vara lokal. Ställ in säkerhetskopieringsfrekvensen på varje dag klockan 3:30.
   * För **Kvarhållning av daglig säkerhetskopieringspunkt** anger du 90 dagarsperiod.
   * För **Kvarhållning av veckovis säkerhetskopieringspunkt** använder du återställningspunkten *Måndag* och behåller den i 52 veckor.
   * För **Kvarhållning av månatlig säkerhetskopieringspunkt** använder du från den första söndagen i varje månad och behåller det i 36 månader.
   * Avmarkera alternativet **Kvarhållning av årlig säkerhetskopieringspunkt**. Den ansvariga för Finans vill inte behålla data längre än 36 månader.
   * Välj **OK** för att skapa säkerhets kopierings principen.

     ![Princip inställningar för säkerhets kopiering](./media/tutorial-backup-vm-at-scale/set-new-policy.png)

     När du har skapat principen för säkerhetskopiering associerar du principen med de virtuella datorerna.

1. Under **Virtual Machines**väljer du **Lägg till**.

     ![Lägg till virtuella datorer](./media/tutorial-backup-vm-at-scale/add-virtual-machines.png)

1. Fönstret **Välj virtuella datorer** öppnas. Välj *myVM* och klicka på **OK** för att distribuera säkerhets kopierings principen till de virtuella datorerna.

    Alla virtuella datorer som finns på samma plats och som inte redan är associerade med en säkerhets kopierings princip visas. *myVMH1* och *myVMR1* markeras för att associeras till principen *Finans*.

    ![Välj virtuella datorer som ska skyddas](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png)

1. När du har valt de virtuella datorerna väljer du **Aktivera säkerhets kopiering**.

    När distributionen är klar får du ett meddelande om att distributionen har slutförts.

## <a name="initial-backup"></a>Den första säkerhetskopieringen

Du har aktiverat säkerhets kopiering för Recovery Services valv, men en inledande säkerhets kopiering har inte skapats. Det är en bra metod för haveri beredskap för att utlösa den första säkerhets kopieringen, så att dina data skyddas.

Så här kör du en säkerhetskopiering på begäran:

1. På instrument panelen för valv väljer du **3** under **säkerhets kopierings objekt**för att öppna menyn säkerhets kopierings objekt.

    ![Säkerhetskopiera objekt](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    Menyn **Säkerhetskopieringsobjekt** öppnas.

1. På menyn **säkerhets kopierings objekt** väljer du **virtuell Azure-dator** för att öppna listan över virtuella datorer som är associerade med valvet.

    ![Lista över virtuella datorer](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

1. Listan **Säkerhetskopieringsobjekt** öppnas.

    ![Säkerhetskopieringsjobbet utlöses](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

1. I listan **säkerhets kopierings objekt** väljer du ellipserna **...** för att öppna snabb menyn.

1. Välj **Säkerhetskopiera nu** på snabbmenyn.

    ![Snabbmeny](./media/tutorial-backup-vm-at-scale/context-menu.png)

    Menyn Säkerhetskopiera nu öppnas.

1. På menyn Säkerhetskopiera nu anger du den sista dagen för att behålla återställnings punkten och väljer **OK**.

    ![Ange den sista dagen som återställnings punkten för säkerhets kopieringen behålls](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    Distributionsmeddelanden visas som anger att säkerhetskopieringsjobbet har initierats, och du kan övervaka förloppet för jobbet på sidan Säkerhetskopieringsjobb. Beroende på den virtuella datorns storlek kan det ta en stund att skapa den första säkerhetskopian.

    När det första säkerhetskopieringsjobbet är klart visas dess status på menyn för säkerhetskopieringsjobb. Säkerhetskopieringsjobbet på begäran skapade den första återställningspunkten för *myVM*. Upprepa dessa steg för varje virtuell dator om du vill säkerhetskopiera andra virtuella datorer.

    ![Panelen Säkerhetskopieringsjobb](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta att arbeta med efterföljande självstudier ska du inte rensa upp resurserna som du skapade i den här självstudien. Om du inte planerar att fortsätta kan du använda följande steg för att ta bort alla resurser som skapats i den här själv studie kursen i Azure Portal.

1. På instrument panelen **myRecoveryServicesVault** väljer du **3** under **säkerhets kopierings objekt** för att öppna menyn säkerhets kopierings objekt.

    ![Öppna menyn säkerhets kopierings objekt](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

1. På menyn **säkerhets kopierings objekt** väljer du **virtuell Azure-dator** för att öppna listan över virtuella datorer som är associerade med valvet.

    ![Lista över virtuella datorer](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    Listan **Säkerhetskopieringsobjekt** öppnas.

1. På menyn **säkerhets kopierings objekt** väljer du ellipsen för att öppna snabb menyn.

    ![Snabbmeny](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

1. I snabb menyn väljer du **stoppa säkerhets kopiering** för att öppna menyn stoppa säkerhets kopiering.

    ![Menyn stoppa säkerhets kopiering](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

1. På menyn **Stoppa säkerhetskopiering** väljer du den övre nedrullningsbara menyn och sedan **Ta bort säkerhetskopieringsdata**.

1. I dialogrutan **Type the name of the Backup item** (Skriv namnet på säkerhetskopieringsobjektet) skriver du *myVM*.

1. När säkerhets kopierings objekt har verifierats (en bock visas) är knappen **stoppa säkerhets kopiering** aktive rad. Välj **Avbryt säkerhets kopiering** för att stoppa principen och ta bort återställnings punkterna.

    ![Välj stoppa säkerhets kopiering för att ta bort valvet](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png)

    >[!NOTE]
    >Borttagna objekt behålls i läget för mjuk borttagning i 14 dagar. Det går bara att ta bort valvet efter den perioden. Mer information finns i [ta bort ett Azure Backup Recovery Services-valv](backup-azure-delete-vault.md).

1. När det inte finns några fler objekt i valvet väljer du **ta bort**.

    ![Välj Ta bort](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    När valvet har tagits bort återgår du till listan över Recovery Services valv.

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du Azure Portal för att:

> [!div class="checklist"]
>
> * skapar ett Recovery Services-valv
> * Ställa in valvet för att skydda virtuella datorer
> * Skapa en anpassad policy för säkerhetskopiering och kvarhållning
> * Tilldela principen för att skydda flera virtuella datorer
> * Utlösa en säkerhetskopiering på begäran för virtuella datorer

Fortsätta till nästa kurs för att återställa en virtuell Azure-dator från disken.

> [!div class="nextstepaction"]
> [Återställa virtuella datorer med CLI](./tutorial-restore-disk.md)
