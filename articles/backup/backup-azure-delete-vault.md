---
title: Ta bort ett Microsoft Azure Recovery Services-valv
description: I den här artikeln får du lära dig hur du tar bort beroenden och sedan tar bort Azure Backup Recovery Services-valv.
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: bb6be070ac0fb408ac37c8ae7b003b54da5d6dea
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773665"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Ta bort ett Azure Backup Recovery Services-valv

Den här artikeln beskriver hur du tar bort ett [Azure Backup](backup-overview.md) Recovery Services-valv. Den innehåller instruktioner för att ta bort beroenden och sedan ta bort ett valv.

## <a name="before-you-start"></a>Innan du börjar

Du kan inte ta bort ett Recovery Services-valv med något av följande beroenden:

- Du kan inte ta bort ett valv som innehåller skyddade datakällor (till exempel virtuella IaaS-datorer, SQL-databaser eller Azure-filresurser).
- Du kan inte ta bort ett valv som innehåller säkerhetskopierade data. När säkerhetskopierade data tas bort hamnar de i tillståndet mjukt borttagna.
- Du kan inte ta bort ett valv som innehåller säkerhetskopierade data i läget för mjuk borttagning.
- Du kan inte ta bort ett valv som har registrerade lagringskonton.

Om du försöker ta bort valvet utan att ta bort beroendena får du något av följande felmeddelanden:

- Det går inte att ta bort valvet eftersom det finns befintliga resurser i valvet. Kontrollera att det inte finns några säkerhetskopieringsobjekt, skyddade servrar eller hanteringsservrar för säkerhetskopiering som är associerade med det här valvet. Avregistrera följande containrar som är associerade med det här valvet innan du fortsätter för borttagning.

- Recovery Services-valvet kan inte tas bort eftersom det finns säkerhetskopieringsobjekt i mjuk borttagning i valvet. De mjukt borttagna objekten tas bort permanent efter 14 dagars borttagning. Försök att ta bort valvet när säkerhetskopieringsobjekten har tagits bort permanent och det inte finns något objekt i mjuk borttagning kvar i valvet. Mer information finns i [Mjuk borttagning för Azure Backup](./backup-azure-security-feature-cloud.md).

## <a name="proper-way-to-delete-a-vault"></a>Rätt sätt att ta bort ett valv

>[!WARNING]
>Följande åtgärd är destruktiv och kan inte ångras. Alla säkerhetskopierade data och säkerhetskopieringsobjekt som är associerade med den skyddade servern tas bort permanent. Tänk dig för innan du fortsätter.

Om du vill ta bort ett valv korrekt måste du följa stegen i den här ordningen:

- **Steg 1:** Inaktivera funktionen för mjuk borttagning. [Här finns](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) anvisningar för hur du inaktiverar mjuk borttagning.

- **Steg 2:** När du har inaktiverat mjuk borttagning kontrollerar du om det finns några objekt som tidigare är kvar i läget för mjuk borttagning. Om det finns objekt i läget för mjuk borttagning  måste du ta *bort dem igen.* [Följ de här stegen](./backup-azure-security-feature-cloud.md#permanently-deleting-soft-deleted-backup-items) för att hitta mjukt borttagna objekt och ta bort dem permanent.

- **Steg 3:** Du måste kontrollera alla följande tre platser för att kontrollera om det finns några skyddade objekt:

  - **Molnskyddade objekt:** Gå till valvinstrumentpanelens meny > **Säkerhetskopieringsobjekt**. Alla objekt som anges här måste tas bort med **Stoppa säkerhetskopiering** eller **Ta bort säkerhetskopieringsdata** tillsammans med deras säkerhetskopieringsdata.  [Följ dessa steg för](#delete-protected-items-in-the-cloud) att ta bort dessa objekt.
  - **SQL Server instans:** Gå till menyn för valvets instrumentpanel > **Backup Infrastructure**  >  **Protected Servers**. I Skyddade servrar väljer du den server som du vill avregistrera. Om du vill ta bort valvet måste du avregistrera alla servrar. Högerklicka på den skyddade servern och välj **Avregistrera**.
  - **MARS-skyddade servrar:** Gå till menyn för valvets instrumentpanel > **Backup Infrastructure**  >  **Protected Servers**. Om du har MARS-skyddade servrar måste alla objekt som anges här tas bort tillsammans med säkerhetskopierade data. [Följ dessa steg om du](#delete-protected-items-on-premises) vill ta bort MARS-skyddade servrar.
  - **MABS- eller DPM-hanteringsservrar:** Gå till menyn för valvets instrumentpanel > **Backup Infrastructure** Backup  >  **Management Servers**. Om du har DPM eller Azure Backup Server (MABS) måste alla objekt som anges här tas bort eller avregistreras tillsammans med deras säkerhetskopieringsdata. [Följ dessa steg för](#delete-protected-items-on-premises) att ta bort hanteringsservrarna.

- **Steg 4:** Du måste se till att alla registrerade lagringskonton tas bort. Gå till menyn på instrumentpanelen för valvet > **Backup Infrastructure Storage** Accounts (Lagringskonton för  >  **säkerhetskopieringsinfrastruktur).** Om du har lagringskonton som anges här måste du avregistrera alla. Information om hur du avregistrerar kontot finns i [Avregistrera ett lagringskonto.](manage-afs-backup.md#unregister-a-storage-account)
- **Steg 5:** Se till att det inte finns några privata slutpunkter som skapats för valvet. Gå till menyn Valvinstrumentpanel > Privata slutpunktsanslutningar **under** Inställningar > om valvet har några privata slutpunktsanslutningar som skapats eller försökt skapas, se till att de tas bort innan du fortsätter med borttagningen av valvet. 

När du har slutfört de här stegen kan du fortsätta att [ta bort valvet](#delete-the-recovery-services-vault).

Om du inte har några skyddade objekt lokalt eller i molnet, men fortfarande får felet för borttagning av valvet, utför du stegen i Ta bort Recovery Services-valvet med [hjälp av Azure Resource Manager](#delete-the-recovery-services-vault-by-using-azure-resource-manager)

## <a name="delete-protected-items-in-the-cloud"></a>Ta bort skyddade objekt i molnet

Läs först avsnittet **[Innan du börjar för](#before-you-start)** att förstå processen för beroenden och borttagning av valv.

Utför följande steg för att stoppa skyddet och ta bort säkerhetskopierade data:

1. Från portalen går du till **Recovery Services-valvet** och går sedan till **Säkerhetskopieringsobjekt.** I listan  Typ av säkerhetskopieringshantering väljer du sedan skyddade objekt i molnet (till exempel Azure Virtual Machines, Azure Storage [Azure Files-tjänsten] eller SQL Server på Azure Virtual Machines).

    ![Välj typ av säkerhetskopiering.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Du ser en lista över alla objekt för kategorin. Högerklicka för att välja säkerhetskopieringsobjektet. Beroende på om säkerhetskopieringsobjektet är skyddat eller inte visas antingen fönstret **Stoppa säkerhetskopiering** eller Fönstret Ta bort **säkerhetskopieringsdata** på menyn.

    - Om fönstret **Stoppa säkerhetskopiering** visas väljer du **Ta bort säkerhetskopieringsdata** i den nedrullningsna menyn. Ange namnet på säkerhetskopieringsobjektet (det här fältet är ärendekänsligt) och välj sedan en orsak i den nedrullningsna menyn. Ange dina kommentarer om du har några. Välj sedan Stoppa **säkerhetskopiering.**

        ![Fönstret Stoppa säkerhetskopiering.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Om fönstret **Ta bort säkerhetskopieringsdata** visas anger du namnet på säkerhetskopieringsobjektet (det här fältet är ärendekänsligt) och väljer sedan en orsak på den nedrullningsna menyn. Ange dina kommentarer om du har några. Välj sedan Ta **bort.**

         ![Fönstret Ta bort säkerhetskopieringsdata.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

   Det här alternativet tar bort schemalagda säkerhetskopieringar och tar även bort säkerhetskopieringar på begäran.
3. Markera **meddelandeikonen:** ![ Meddelandeikonen.](./media/backup-azure-delete-vault/messages.png) När processen är klar visar tjänsten följande meddelande: Stoppa säkerhetskopiering och *ta bort säkerhetskopierade data för "Säkerhetskopieringsobjekt".* *Åtgärden har slutförts.*
4. Välj **Uppdatera** på menyn **Säkerhetskopieringsobjekt** för att kontrollera att säkerhetskopieringsobjektet har tagits bort.

      ![Sidan Ta bort säkerhetskopieringsobjekt.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Ta bort skyddade objekt lokalt

Läs först avsnittet Innan **[du börjar för](#before-you-start)** att förstå processen för borttagning av beroenden och valv.

1. I menyn på instrumentpanelen för valvet väljer du **Infrastruktur för säkerhetskopiering.**
2. Beroende på ditt lokala scenario väljer du något av följande alternativ:

      - För MARS väljer du **Skyddade servrar** och sedan  **Azure Backup Agent**. Välj sedan den server som du vill ta bort.

        ![För MARS väljer du ditt valv för att öppna dess instrumentpanel.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - För MABS eller DPM väljer du **Säkerhetskopieringshanteringsservrar.** Välj sedan den server som du vill ta bort.

          ![För MABS eller DPM väljer du ditt valv för att öppna dess instrumentpanel.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Fönstret **Ta** bort visas med ett varningsmeddelande.

     ![Fönstret Ta bort.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Granska varningsmeddelandet och anvisningarna i kryssrutan för medgivande.
    > [!NOTE]
    >
    >- Om den skyddade servern synkroniseras med Azure-tjänster och det finns säkerhetskopieringsobjekt, visar kryssrutan medgivande antalet beroende säkerhetskopieringsobjekt och länken för att visa säkerhetskopieringsobjekten.
    >- Om den skyddade servern inte är synkroniserad med Azure-tjänster och det finns säkerhetskopieringsobjekt visar kryssrutan medgivande endast antalet säkerhetskopieringsobjekt.
    >- Om det inte finns några säkerhetskopieringsobjekt ber kryssrutan om medgivande om borttagning.

4. Markera kryssrutan för medgivande och välj sedan Ta **bort.**

5. Markera **meddelandeikonen** Ta ![ bort säkerhetskopierade ](./media/backup-azure-delete-vault/messages.png) data. När åtgärden har avslutats visar tjänsten meddelandet: Stoppa säkerhetskopiering och *ta bort säkerhetskopierade data för "Säkerhetskopieringsobjekt".* *Åtgärden har slutförts.*
6. Välj **Uppdatera** på menyn **Säkerhetskopieringsobjekt** för att kontrollera att säkerhetskopieringsobjektet har tagits bort.

>[!NOTE]
>Om du tar bort ett lokalt skyddat objekt från en portal som innehåller beroenden får du en varning om att "Borttagning av serverns registrering är en destruktiv åtgärd och kan inte ångras. Alla säkerhetskopierade data (återställningspunkter som krävs för att återställa data) och Säkerhetskopieringsobjekt som är associerade med den skyddade servern tas bort permanent."

När den här processen är klar kan du ta bort säkerhetskopieringsobjekten från hanteringskonsolen:

- [Ta bort säkerhetskopieringsobjekt från MARS-hanteringskonsolen](#delete-backup-items-from-the-mars-management-console)
- [Ta bort säkerhetskopierade objekt från MABS- eller DPM-hanteringskonsolen](#delete-backup-items-from-the-mabs-or-dpm-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>Ta bort säkerhetskopieringsobjekt från MARS-hanteringskonsolen

>[!NOTE]
>Om du har tagit bort eller förlorat källdatorn utan att stoppa säkerhetskopieringen misslyckas nästa schemalagda säkerhetskopiering. Den gamla återställningspunkten upphör att gälla enligt principen, men den sista enskilda återställningspunkten behålls alltid tills du stoppar säkerhetskopieringen och tar bort data. Du kan göra detta genom att följa stegen i [det här avsnittet](#delete-protected-items-on-premises).

1. Öppna MARS-hanteringskonsolen, gå till **fönstret Åtgärder** och välj **Schemalägg säkerhetskopiering.**
2. På sidan **Ändra eller stoppa en schemalagd säkerhetskopiering** väljer du Sluta använda det här **säkerhetskopieringsschemat och tar bort alla lagrade säkerhetskopior.** Välj **Nästa**.

    ![Ändra eller stoppa en schemalagd säkerhetskopiering.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. På sidan **Stoppa en schemalagd säkerhetskopiering** väljer du **Slutför**.

    ![Stoppa en schemalagd säkerhetskopiering.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Du uppmanas att ange en PIN-kod för säkerhet (personligt id-nummer) som du måste generera manuellt. Det gör du genom att först logga in på Azure Portal.
5. Gå till **Inställningar för Recovery Services-valvEgenskaper**  >    >  .
6. Under **Pin-kod för** säkerhet väljer du **Generera**. Kopiera PIN-koden. PIN-koden är bara giltig i fem minuter.
7. Klistra in PIN-koden i hanteringskonsolen och välj **sedan OK.**

    ![Generera en pin-kod för säkerhet.](./media/backup-azure-delete-vault/security-pin.png)

8. På sidan **Ändra säkerhetskopieringsförloppet** visas följande meddelande: *Borttagna säkerhetskopieringsdata sparas i 14 dagar. Efter det tas säkerhetskopierade data bort permanent.*  

    ![Ta bort infrastrukturen för säkerhetskopiering.](./media/backup-azure-delete-vault/deleted-backup-data.png)

När du har tagit bort de lokala säkerhetskopieringsobjekten följer du nästa steg från portalen.

### <a name="delete-backup-items-from-the-mabs-or-dpm-management-console"></a>Ta bort säkerhetskopieringsobjekt från MABS- eller DPM-hanteringskonsolen

>[!NOTE]
>Om du har tagit bort eller förlorat källdatorn utan att stoppa säkerhetskopieringen misslyckas nästa schemalagda säkerhetskopiering. Den gamla återställningspunkten upphör att gälla enligt principen, men den sista enskilda återställningspunkten behålls alltid tills du stoppar säkerhetskopieringen och tar bort data. Du kan göra detta genom att följa stegen i [det här avsnittet](#delete-protected-items-on-premises).

Det finns två metoder som du kan använda för att ta bort säkerhetskopierade objekt från MABS- eller DPM-hanteringskonsolen.

#### <a name="method-1"></a>Metod 1

Om du vill stoppa skyddet och ta bort säkerhetskopierade data gör du följande:

1. Öppna DPM-administratörskonsol och välj sedan **Skydd** i navigeringsfältet.
2. I visningsfönstret väljer du den skyddsgruppsmedlem som du vill ta bort. Högerklicka för att välja alternativet **Stoppa skydd av gruppmedlemmar.**
3. I dialogrutan **Stoppa skydd** väljer du Ta bort **skyddade data** och markerar sedan kryssrutan Ta bort **lagring online.** Välj sedan **Stoppa skydd.**

    ![Välj Ta bort skyddade data i fönstret Stoppa skydd.](./media/backup-azure-delete-vault/delete-storage-online.png)

    Den skyddade medlemsstatusen ändras till *Inactive replica available*.

4. Högerklicka på den inaktiva skyddsgruppen och välj Ta **bort inaktivt skydd.**

    ![Ta bort inaktivt skydd.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. I fönstret **Ta bort inaktivt** skydd markerar du kryssrutan Ta bort **onlinelagring** och väljer sedan **OK.**

    ![Ta bort onlinelagring.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Metod 2

Öppna **MABS-hanterings-** eller **DPM-hanteringskonsolen.** Avmarkera **kryssrutan Jag vill ha** **onlineskydd under Välj dataskyddsmetod.**

  ![Välj dataskyddsmetod.](./media/backup-azure-delete-vault/data-protection-method.png)

När du har tagit bort de lokala säkerhetskopieringsobjekten följer du nästa steg från portalen.

## <a name="delete-the-recovery-services-vault"></a>Ta bort Recovery Services-valvet

1. När alla beroenden har tagits bort bläddrar du till **fönstret Essentials** på valvmenyn.
2. Kontrollera att det inte finns några säkerhetskopieringsobjekt, servrar för säkerhetskopieringshantering eller replikerade objekt i listan. Om objekt fortfarande visas i valvet kan du gå till [avsnittet Innan du börjar.](#before-you-start)

3. När det inte finns några fler objekt i valvet väljer du Ta **bort på** valvets instrumentpanel.

    ![Välj Ta bort på instrumentpanelen för valvet.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Välj **Ja** för att kontrollera att du vill ta bort valvet. Valvet tas bort. Portalen återgår till **menyn Ny** tjänst.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>Ta bort Recovery Services-valvet med hjälp av PowerShell

Läs först avsnittet Innan **[du börjar för](#before-you-start)** att förstå processen för borttagning av beroenden och valv.

Så här stoppar du skyddet och tar bort säkerhetskopierade data:

- Om du använder SQL i säkerhetskopiering av virtuella Azure-datorer och aktiverat automatiskt skydd för SQL-instanser inaktiverar du först det automatiska skyddet.

    ```PowerShell
        Disable-AzRecoveryServicesBackupAutoProtection
           [-InputItem] <ProtectableItemBase>
           [-BackupManagementType] <BackupManagementType>
           [-WorkloadType] <WorkloadType>
           [-PassThru]
           [-VaultId <String>]
           [-DefaultProfile <IAzureContextContainer>]
           [-WhatIf]
           [-Confirm]
           [<CommonParameters>]
    ```

  [Läs mer](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) om hur du inaktiverar skydd för ett Azure Backup skyddat objekt.

- Stoppa skyddet och ta bort data för alla säkerhetskopierade objekt i molnet (till exempel: virtuell IaaS-dator, Azure-filresurs och så vidare):

    ```PowerShell
       Disable-AzRecoveryServicesBackupProtection
       [-Item] <ItemBase>
       [-RemoveRecoveryPoints]
       [-Force]
       [-VaultId <String>]
       [-DefaultProfile <IAzureContextContainer>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
    ```

    [Läs mer](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection)   om inaktiverar skydd för ett säkerhetskopierat objekt.

- För lokala filer och mappar som skyddas med Azure Backup Agent (MARS) säkerhetskopiering till Azure använder du följande PowerShell-kommando för att ta bort säkerhetskopierade data från varje MARS PowerShell-modul:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Därefter visas följande meddelande:

    *Microsoft Azure Backup Är du säker på att du vill ta bort den här säkerhetskopieringspolicyn? Borttagna säkerhetskopierade data bevaras i 14 dagar. Efter det tas säkerhetskopierade data bort permanent. <br/> [Y] Ja [A] Ja till alla [N] Nej [L] Nej till alla [S] Pausa [?] Hjälp (standard är "Y"):*

- För lokala datorer som skyddas med MABS (Microsoft Azure Backup Server) eller DPM (System Center Data Protection Manager) till Azure använder du följande kommando för att ta bort säkerhetskopierade data i Azure.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Därefter visas följande meddelande:

   *Microsoft Azure Backup* Är du säker på att du vill ta bort den här säkerhetskopieringspolicyn? Borttagna säkerhetskopierade data bevaras i 14 dagar. Därefter tas de bort permanent. <br/>
   [Y] Ja [A] Ja till alla [N] Nej [L] Nej till alla [S] Pausa [?] Hjälp (standard är "Y"):*

När du har tagit bort säkerhetskopierade data avregistrerar du alla lokala containrar och hanteringsservrar.

- För lokala filer och mappar som skyddas med hjälp Azure Backup Agent (MARS) som backar upp till Azure:

    ```PowerShell
    Unregister-AzRecoveryServicesBackupContainer
              [-Container] <ContainerBase>
              [-PassThru]
              [-VaultId <String>]
              [-DefaultProfile <IAzureContextContainer>]
              [-WhatIf]
              [-Confirm]
              [<CommonParameters>]
    ```

    [Läs mer](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) om att avregistrera en Windows Server eller en annan container från valvet.

- För lokala datorer som skyddas med MABS (Microsoft Azure Backup Server) eller DPM till Azure (System Center Data Protection Manage:

    ```PowerShell
        Unregister-AzRecoveryServicesBackupManagementServer
          [-AzureRmBackupManagementServer] <BackupEngineBase>
          [-PassThru]
          [-VaultId <String>]
          [-DefaultProfile <IAzureContextContainer>]
          [-WhatIf]
          [-Confirm]
          [<CommonParameters>]
    ```

    [Läs mer](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) om att avregistrera en container för säkerhetskopieringshantering från valvet.

När du har tagit bort säkerhetskopierade data permanent och avregistrerat alla containrar fortsätter du med att ta bort valvet.

Så här tar du bort ett Recovery Services-valv:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[Läs mer om](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) att ta bort ett Recovery Services-valv.

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>Ta bort Recovery Services-valvet med hjälp av CLI

Läs först avsnittet Innan **[du börjar för](#before-you-start)** att förstå processen för borttagning av beroenden och valv.

> [!NOTE]
> För närvarande Azure Backup CLI endast hantering av säkerhetskopior av virtuella Azure-datorer, så följande kommando för att ta bort valvet fungerar bara om valvet innehåller säkerhetskopior av virtuella Azure-datorer. Du kan inte ta bort ett valv med Azure Backup CLI om valvet innehåller något annat säkerhetskopieringsobjekt än virtuella Azure-datorer.

Utför följande steg för att ta bort ett befintligt Recovery Services-valv:

- Så här stoppar du skyddet och tar bort säkerhetskopierade data

    ```azurecli
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    Mer information finns i den här [artikeln.](/cli/azure/backup/protection#az_backup_protection_disable)

- Ta bort ett befintligt Recovery Services-valv:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    Mer information finns i den här [artikeln](/cli/azure/backup/vault)

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Ta bort Recovery Services-valvet med hjälp av Azure Resource Manager

Det här alternativet för att ta bort Recovery Services-valvet rekommenderas endast om alla beroenden tas bort och du fortfarande får *valvborttagningsfelet*. Prova något eller alla av följande tips:

- I fönstret **Essentials** på valvmenyn kontrollerar du att det inte finns några säkerhetskopieringsobjekt, servrar för säkerhetskopieringshantering eller replikerade objekt i listan. Om det finns säkerhetskopieringsobjekt kan du gå [till avsnittet Innan du börjar.](#before-you-start)
- Försök [att ta bort valvet från portalen](#delete-the-recovery-services-vault) igen.
- Om alla beroenden tas bort och du fortfarande får felet Valvborttagning använder du ARMClient-verktyget för att utföra följande steg (efter anteckningen).

1. Gå till [chocolatey.org hämta](https://chocolatey.org/) och installera Chocolatey. Installera sedan ARMClient genom att köra följande kommando:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Logga in på ditt Azure-konto och kör sedan följande kommando:

    `ARMClient.exe login [environment name]`

3. I Azure Portal du prenumerations-ID:t och resursgruppens namn för det valv som du vill ta bort.

Mer information om ARMClient-kommandot finns i [ARMClient README](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Använda klienten Azure Resource Manager för att ta bort ett Recovery Services-valv

1. Kör följande kommando med ditt prenumerations-ID, resursgruppsnamn och valvnamn. Om du inte har några beroenden tas valvet bort när du kör följande kommando:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>?api-version=2015-03-15
   ```

2. Om valvet inte är tomt visas följande felmeddelande: Valvet kan inte tas bort eftersom det finns *befintliga resurser i det här valvet.* Om du vill ta bort ett skyddat objekt eller en container i ett valv kör du följande kommando:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. I Azure Portal kontrollerar du att valvet har tagits bort.

## <a name="next-steps"></a>Nästa steg

[Läs mer om Recovery Services-valv](backup-azure-recovery-services-vault-overview.md) 
 [Lär dig mer om övervakning och hantering av Recovery Services-valv](backup-azure-manage-windows-server.md)
