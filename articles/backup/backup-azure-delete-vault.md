---
title: Ta bort ett Microsoft Azure Recovery Services-valv
description: I den här artikeln lär du dig hur du tar bort beroenden och sedan tar bort ett Azure Backup Recovery Services-valv.
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: 41d0cbc8e1c59f33efc24f38b535aa9cf91b2cc9
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2020
ms.locfileid: "88257961"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Ta bort ett Azure Backup Recovery Services-valv

I den här artikeln beskrivs hur du tar bort ett [Azure Backup](backup-overview.md) Recovery Services-valv. Den innehåller instruktioner för att ta bort beroenden och sedan ta bort ett valv.

## <a name="before-you-start"></a>Innan du börjar

Du kan inte ta bort ett Recovery Services-valv med något av följande beroenden:

- Det går inte att ta bort ett valv som innehåller skyddade data källor (till exempel IaaS VM, SQL-databaser, Azure-filresurser osv.)  
- Du kan inte ta bort ett valv som innehåller säkerhets kopierings data. När säkerhetskopierade data tas bort hamnar de i tillståndet mjukt borttagna.
- Det går inte att ta bort ett valv som innehåller säkerhets kopierings data i läget Soft Deleted.
- Du kan inte ta bort ett valv som har registrerat lagrings konton.

Om du försöker ta bort valvet utan att ta bort beroenden, uppstår något av följande fel meddelanden:

- Det går inte att ta bort valvet eftersom det finns befintliga resurser i valvet. Se till att det inte finns några säkerhets kopierings objekt, skyddade servrar eller säkerhets kopierings hanterings servrar kopplade till det här valvet. Avregistrera följande behållare som är kopplade till valvet innan du fortsätter för borttagning.

- Recovery Services-valvet kan inte tas bort eftersom det finns säkerhetskopieringsobjekt i mjuk borttagning i valvet. De mjuka borttagna objekten tas bort permanent efter 14 dagars borttagnings åtgärd. Försök att ta bort valvet när säkerhets kopierings objekten har tagits bort permanent och att det inte finns något objekt i läget Soft Deleted kvar i valvet. Mer information finns i [mjuk borttagning för Azure Backup](./backup-azure-security-feature-cloud.md).

## <a name="proper-way-to-delete-a-vault"></a>Korrekt sätt att ta bort ett valv

>[!WARNING]
>Följande åtgärd är förstörande och kan inte återställas. Alla säkerhets kopierings data och säkerhets kopierings objekt som är associerade med den skyddade servern tas bort permanent. Tänk dig för innan du fortsätter.

Om du vill ta bort ett valv korrekt måste du följa stegen i den här ordningen:

- **Steg 1**: inaktivera funktionen för mjuk borttagning. Mer information om hur du inaktiverar mjuk borttagning [finns här](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) .

- **Steg 2**: när du har inaktiverat mjuk borttagning kontrollerar du om det finns några objekt som tidigare har tagits bort i läget Soft Deleted. Om det finns objekt i läget tyst Borttagning måste du ångra *borttagningen* och *ta bort* dem igen. [Följ de här stegen](./backup-azure-security-feature-cloud.md#permanently-deleting-soft-deleted-backup-items) för att hitta mjuka borttagnings objekt och ta bort dem permanent.

- **Steg 3**: du måste kontrol lera alla följande tre platser för att kontrol lera om det finns några skyddade objekt:

  - **Moln skydds objekt**: gå till instrument panelen för valv-menyn > **säkerhets kopierings objekt**. Alla objekt som anges här måste tas bort med **stoppa säkerhets kopiering** eller **ta bort säkerhetskopierade data** tillsammans med sina säkerhets kopierings data.  [Följ de här stegen](#delete-protected-items-in-the-cloud) för att ta bort dessa objekt.
  - **SQL Server instans**: gå till instrument panelen för valvet > **säkerhets kopierings infrastruktur**  >  **skyddade servrar**. I skyddade servrar väljer du den server som ska avregistreras. Om du vill ta bort valvet måste du avregistrera alla servrar. Högerklicka på den skyddade servern och välj **avregistrera**.
  - **Mars-skyddade servrar**: gå till instrument panelen för valv-menyn > **säkerhets kopierings infrastruktur**  >  **skyddade servrar**. Om du har MARS-skyddade servrar måste alla objekt som anges här tas bort tillsammans med sina säkerhets kopierings data. [Följ dessa steg](#delete-protected-items-on-premises) om du vill ta bort mars-skyddade servrar.
   - **Mabs-eller DPM-hanterings servrar**: gå till instrument panelen för valvet > **säkerhets kopierings**  >  **hanterings servrar**för säkerhets kopiering. Om du har DPM eller Azure Backup Server (MABS) måste alla objekt som anges här tas bort eller avregistreras tillsammans med sina säkerhets kopierings data. [Följ dessa steg](#delete-protected-items-on-premises) om du vill ta bort hanterings servrarna.

- **Steg 4**: du måste se till att alla registrerade lagrings konton tas bort. Gå till instrument panelen för valv-menyn > **säkerhets kopierings infrastruktur**  >  **lagrings konton**. Om du har lagrings konton som listas här måste du avregistrera alla. Information om hur du avregistrerar kontot finns i [avregistrera ett lagrings konto](manage-afs-backup.md#unregister-a-storage-account).

När du har slutfört de här stegen kan du fortsätta att [ta bort valvet](#delete-the-recovery-services-vault).

Om du inte har några skyddade objekt lokalt eller i molnet, men ändå får ta bort valvet, utför du stegen i [ta bort Recovery Services valvet med Azure Resource Manager](#delete-the-recovery-services-vault-by-using-azure-resource-manager)

## <a name="delete-protected-items-in-the-cloud"></a>Ta bort skyddade objekt i molnet

Börja med att läsa avsnittet **[innan du börjar](#before-you-start)** och förstå borttagnings processen för beroenden och valvet.

Utför följande steg för att stoppa skyddet och ta bort säkerhetskopierade data:

1. Gå till **Recovery Services valv**från portalen och gå sedan till **säkerhets kopierings objekt**. Välj sedan de skyddade objekten i molnet (till exempel Azure Virtual Machines Azure Storage [tjänsten Azure Files] eller SQL Server på Azure Virtual Machines).

    ![Välj typ av säkerhets kopiering.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Högerklicka för att välja säkerhets kopierings objekt. Beroende på om objektet för säkerhets kopian är skyddat eller inte, visar menyn antingen rutan **stoppa säkerhets kopiering** eller fönstret **ta bort säkerhets kopierings data** .

    - Om fönstret **stoppa säkerhets kopiering** visas väljer du **ta bort säkerhets kopierings data** på den nedrullningsbara menyn. Ange namnet på det säkerhetskopierade objektet (det här fältet är Skift läges känsligt) och välj sedan en orsak i den nedrullningsbara menyn. Ange dina kommentarer, om du har några. Välj sedan **stoppa säkerhets kopiering**.

        ![Fönstret stoppa säkerhets kopiering.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Om fönstret **ta bort säkerhets kopierings data** visas anger du namnet på objektet i säkerhets kopian (det här fältet är Skift läges känsligt) och väljer sedan en orsak i den nedrullningsbara menyn. Ange dina kommentarer, om du har några. Välj sedan **ta bort**.

         ![Rutan ta bort säkerhets kopierings data.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

3. Kontrol lera **meddelande** ikonen: ![ meddelande ikonen.](./media/backup-azure-delete-vault/messages.png) När processen har slutförts visar tjänsten följande meddelande: *stoppar säkerhets kopiering och tar bort säkerhetskopierade data för*säkerhets kopierings*objekt.* *Åtgärden har slutförts*.
4. Välj **Uppdatera** på menyn **säkerhets kopierings objekt** för att se till att säkerhets kopierings objekt har tagits bort.

      ![Sidan ta bort säkerhets kopierings objekt.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Ta bort skyddade objekt lokalt

Börja med att läsa avsnittet **[innan du börjar](#before-you-start)** och förstå borttagnings processen för beroenden och valvet.

1. På instrument panelen för valv väljer du **infrastruktur för säkerhets kopiering**.
2. Välj något av följande alternativ beroende på ditt lokala scenario:

      - För MARS väljer du **skyddade servrar** och  **Azure Backup sedan agent**. Välj sedan den server som du vill ta bort.

        ![För MARS väljer du ditt valv för att öppna instrument panelen.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - För MABS eller DPM väljer du **säkerhets kopierings hanterings servrar**. Välj sedan den server som du vill ta bort.

          ![För MABS eller DPM väljer du ditt valv för att öppna instrument panelen.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Rutan **ta bort** visas med ett varnings meddelande.

     ![Ta bort-fönstret.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Läs varnings meddelandet och instruktionerna i kryss rutan medgivande.
    > [!NOTE]
    >
    >- Om den skyddade servern är synkroniserad med Azure-tjänster och säkerhets kopierings objekt finns, visar kryss rutan medgivande antalet beroende säkerhets kopierings objekt och länken för att Visa säkerhets kopierings objekt.
    >- Om den skyddade servern inte är synkroniserad med Azure-tjänster och det finns säkerhets kopierings objekt visas bara antalet säkerhets kopierings objekt i kryss rutan medgivande.
    >- Om det inte finns några säkerhets kopierings objekt uppmanas du att ta bort den i kryss rutan medgivande.

4. Markera kryss rutan medgivande och välj sedan **ta bort**.

5. Kontrol lera **meddelande** ikonen ![ ta bort säkerhetskopierade data ](./media/backup-azure-delete-vault/messages.png) . När åtgärden har slutförts visar tjänsten meddelandet: *stoppar säkerhets kopiering och tar bort säkerhetskopierade data för säkerhets kopierings objekt.* *Åtgärden har slutförts*.
6. Välj **Uppdatera** på menyn **säkerhets kopierings objekt** för att se till att säkerhets kopierings objekt tas bort.

>[!NOTE]
>Om du tar bort ett lokalt skyddat objekt från en portal som innehåller beroenden får du en varning om att "ta bort serverns registrering är en destruktiv åtgärd och inte kan ångras. Alla säkerhets kopierings data (återställnings punkter som krävs för att återställa data) och säkerhets kopierings objekt som är associerade med den skyddade servern tas bort permanent. "

När den här processen är klar kan du ta bort säkerhets kopierings objekt från hanterings konsolen:

- [Ta bort säkerhets kopierings objekt från MARS Management Console](#delete-backup-items-from-the-mars-management-console)
- [Ta bort säkerhets kopierings objekt från MABS eller DPM Management Console](#delete-backup-items-from-the-mabs-or-dpm-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>Ta bort säkerhets kopierings objekt från MARS Management Console

>[!NOTE]
>Om du har tagit bort eller förlorat käll datorn utan att stoppa säkerhets kopieringen kommer nästa schemalagda säkerhets kopiering att Miss kopie ras. Den gamla återställnings punkten upphör att gälla enligt principen, men den sista enskilda återställnings punkten behålls alltid tills du stoppar säkerhets kopieringen och tar bort data. Du kan göra detta genom att följa stegen i [det här avsnittet](#delete-protected-items-on-premises).

1. Öppna hanterings konsolen för MARS, gå till **Åtgärds** fönstret och välj **Schemalägg säkerhets kopiering**.
2. På sidan **ändra eller stoppa en schemalagd säkerhets kopiering** väljer du **sluta använda detta schema för säkerhets kopiering och tar bort alla lagrade säkerhets kopior**. Välj sedan **Nästa**.

    ![Ändra eller stoppa en schemalagd säkerhets kopiering.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. På sidan **stoppa en schemalagd säkerhets kopiering** väljer du **Slutför**.

    ![Stoppa en schemalagd säkerhets kopiering.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Du uppmanas att ange en PIN-kod för säkerhet (personal Identification Number) som du måste generera manuellt. Det gör du genom att först logga in på Azure Portal.
5. Gå till inställningar för **Recovery Services valv**  >  **Settings**  >  **Properties**.
6. Under **säkerhets-PIN**väljer du **generera**. Kopiera den här PIN-koden. PIN-koden är bara giltig i fem minuter.
7. I hanterings konsolen klistrar du in PIN-koden och väljer sedan **OK**.

    ![Skapa en säkerhets kod.](./media/backup-azure-delete-vault/security-pin.png)

8. Följande meddelande visas på sidan **ändra säkerhets kopierings förlopp** : *borttagna säkerhets kopierings data bevaras i 14 dagar. Efter den tiden tas säkerhetskopierade data bort permanent.*  

    ![Ta bort infrastruktur för säkerhets kopiering.](./media/backup-azure-delete-vault/deleted-backup-data.png)

När du har tagit bort de lokala säkerhets kopierings objekten följer du stegen i portalen.

### <a name="delete-backup-items-from-the-mabs-or-dpm-management-console"></a>Ta bort säkerhets kopierings objekt från MABS eller DPM Management Console

>[!NOTE]
>Om du har tagit bort eller förlorat käll datorn utan att stoppa säkerhets kopieringen kommer nästa schemalagda säkerhets kopiering att Miss kopie ras. Den gamla återställnings punkten upphör att gälla enligt principen, men den sista enskilda återställnings punkten behålls alltid tills du stoppar säkerhets kopieringen och tar bort data. Du kan göra detta genom att följa stegen i [det här avsnittet](#delete-protected-items-on-premises).

Det finns två metoder som du kan använda för att ta bort säkerhets kopierings objekt från MABS eller DPM Management Console.

#### <a name="method-1"></a>Metod 1

Gör så här om du vill stoppa skyddet och ta bort säkerhetskopierade data:

1. Öppna DPM-administratörskonsol och välj sedan **skydd** i navigerings fältet.
2. I visningsfönstret väljer du den skydds grupps medlem som du vill ta bort. Högerklicka för att markera alternativet **sluta skydda grupp medlemmar** .
3. I dialog rutan **stoppa skydd** väljer du **ta bort skyddade data**och markerar sedan kryss rutan **ta bort lagring online** . Välj sedan **stoppa skydd**.

    ![Välj Ta bort skyddade data från fönstret sluta skydda.](./media/backup-azure-delete-vault/delete-storage-online.png)

    Den skyddade medlems statusen ändras till *inaktiv replikering är tillgänglig*.

4. Högerklicka på den inaktiva skydds gruppen och välj **ta bort inaktivt skydd**.

    ![Ta bort inaktivt skydd.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. I fönstret **ta bort inaktivt skydd** markerar du kryss rutan **ta bort onlinelagring** och väljer sedan **OK**.

    ![Ta bort online-lagring.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Metod 2

Öppna hanterings konsolen för **Mabs** eller **DPM** . Under **Välj data skydds metod**avmarkerar du kryss rutan  **Jag vill ha onlineskydd** .

  ![Välj data skydds metod.](./media/backup-azure-delete-vault/data-protection-method.png)

När du har tagit bort de lokala säkerhets kopierings objekten följer du stegen i portalen.

## <a name="delete-the-recovery-services-vault"></a>Ta bort Recovery Services-valvet

1. När alla beroenden har tagits bort bläddrar du till fönstret **Essentials** i menyn valv.
2. Kontrol lera att det inte finns några säkerhets kopierings objekt, säkerhets kopierings hanterings servrar eller replikerade objekt i listan. Om objekten fortfarande visas i valvet, se avsnittet [innan du börjar](#before-you-start) .

3. När det inte finns några fler objekt i valvet väljer du **ta bort** på instrument panelen för valvet.

    ![Välj Ta bort på instrument panelen för valvet.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Välj **Ja** för att kontrol lera att du vill ta bort valvet. Valvet har tagits bort. Portalen återgår till den **nya** tjänst menyn.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>Ta bort Recovery Services valvet med PowerShell

Börja med att läsa avsnittet **[innan du börjar](#before-you-start)** och förstå borttagnings processen för beroenden och valvet.

Stoppa skyddet och ta bort säkerhets kopierings data:

- Om du använder SQL i Azure VM backup och aktiverat automatiskt skydd för SQL-instanser ska du först inaktivera det automatiska skyddet.

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

  [Läs mer](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) om hur du inaktiverar skydd för ett Azure Backup-skyddat objekt.

- Stoppa skyddet och ta bort data för alla skyddade objekt i molnet (till exempel: IaaS VM, Azure-filresurs osv.):

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

    [Läs mer](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection)   om inaktiverar skydd för ett skyddat objekt med säkerhets kopiering.

- För lokala filer och mappar som skyddas med hjälp av Azure Backup Agent (MARS) säkerhetskopiera till Azure använder du följande PowerShell-kommando för att ta bort säkerhetskopierade data från varje MARS PowerShell-modul:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Post där följande meddelande visas:

    *Microsoft Azure Backup är du säker på att du vill ta bort den här säkerhets kopierings principen? Borttagna säkerhets kopierings data sparas i 14 dagar. Efter den tiden tas säkerhetskopierade data bort permanent. <br/> [J] Ja [A] ja till alla [N] nej [L] nej till alla [S] gör uppehåll [?] Hjälp (standard är "Y"):*

- Använd följande kommando för att ta bort säkerhetskopierade data i Azure för lokala datorer som skyddas med MABS (Microsoft Azure Backup Server) eller DPM (System Center Data Protection Manager) till Azure.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Post där följande meddelande visas:

   *Microsoft Azure Backup* Är du säker på att du vill ta bort den här säkerhets kopierings principen? Borttagna säkerhets kopierings data sparas i 14 dagar. Därefter tas de bort permanent. <br/>
   [J] Ja [A] ja till alla [N] nej [L] nej till alla [S] gör uppehåll [?] Hjälp (standard är "Y"):*

När du har tagit bort säkerhetskopierade data avregistrerar du alla lokala behållare och hanterings servrar.

- För lokala filer och mappar som skyddas med hjälp av Azure Backup Agent (MARS) säkerhetskopiera till Azure:

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

    [Läs mer](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) om att avregistrera en Windows-Server eller annan behållare från valvet.

- För lokala datorer som skyddas med MABS (Microsoft Azure Backup Server) eller DPM till Azure (System Center Data Protection hantera:

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

    [Läs mer](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) om att avregistrera en behållare för säkerhets kopierings hantering från valvet.

När du har tagit bort säkerhetskopierade data permanent och avregistrerat alla behållare fortsätter du att ta bort valvet.

Ta bort ett Recovery Services-valv:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[Läs mer](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) om att ta bort ett Recovery Services-valv.

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>Ta bort Recovery Services valvet med CLI

Börja med att läsa avsnittet **[innan du börjar](#before-you-start)** och förstå borttagnings processen för beroenden och valvet.

> [!NOTE]
> För närvarande stöder Azure Backup CLI bara hantering av virtuella Azure-säkerhetskopieringar, så följande kommando för att ta bort valvet fungerar bara om valvet innehåller virtuella Azure-säkerhetskopieringar. Du kan inte ta bort ett valv som använder Azure Backup CLI, om valvet innehåller några säkerhets kopierings objekt av andra typer än virtuella Azure-datorer.

Utför följande om du vill ta bort det befintliga Recovery Services-valvet:

- Stoppa skyddet och ta bort säkerhetskopierade data

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

    Mer information finns i den här [artikeln](/cli/azure/backup/protection#az-backup-protection-disable).

- Ta bort ett befintligt Recovery Services-valv:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    Mer information finns i den här [artikeln](/cli/azure/backup/vault?view=azure-cli-latest)

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Ta bort Recovery Services valvet med Azure Resource Manager

Det här alternativet för att ta bort Recovery Services-valvet rekommenderas endast om alla beroenden tas bort och du fortfarande får ett *fel meddelande om borttagning av valvet*. Prova någon eller några av följande tips:

- I fönstret **Essentials** i menyn valv kontrollerar du att det inte finns några säkerhets kopierings objekt, säkerhets kopierings hanterings servrar eller replikerade objekt i listan. Om det finns säkerhets kopierings objekt, se avsnittet [innan du börjar](#before-you-start) .
- Försök [att ta bort valvet från portalen](#delete-the-recovery-services-vault) igen.
- Om alla beroenden tas bort och du fortfarande får *borttagnings felet för valvet*, använder du verktyget ARMClient för att utföra följande steg (efter kommentaren).

1. Gå till [Chocolatey.org](https://chocolatey.org/) för att ladda ned och installera choklad. Installera sedan ARMClient genom att köra följande kommando:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Logga in på ditt Azure-konto och kör sedan följande kommando:

    `ARMClient.exe login [environment name]`

3. I Azure Portal kan du samla in prenumerations-ID och resurs gruppens namn för valvet som du vill ta bort.

Mer information om kommandot ARMClient finns i README för [ARMClient](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Använd Azure Resource Manager-klienten för att ta bort ett Recovery Services-valv

1. Kör följande kommando med ditt prenumerations-ID, resurs gruppens namn och namnet på valvet. Om du inte har några beroenden tas valvet bort när du kör följande kommando:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```

2. Om valvet inte är tomt får du följande fel meddelande: det *går inte att ta bort valvet eftersom det finns befintliga resurser i det här valvet.* Om du vill ta bort ett skyddat objekt eller en behållare i ett valv kör du följande kommando:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. I Azure Portal kontrollerar du att valvet har tagits bort.

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om Recovery Services valv](backup-azure-recovery-services-vault-overview.md) 
 [Lär dig mer om att övervaka och hantera Recovery Services valv](backup-azure-manage-windows-server.md)
