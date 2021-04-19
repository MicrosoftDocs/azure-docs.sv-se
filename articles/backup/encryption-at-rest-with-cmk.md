---
title: Kryptering av säkerhetskopieringsdata med kundhanterade nycklar
description: Lär dig Azure Backup gör att du kan kryptera dina säkerhetskopierade data med hjälp av kund hanterade nycklar (CMK).
ms.topic: conceptual
ms.date: 04/19/2021
ms.openlocfilehash: bd51be06e707674f3e35b3478d7f99d096be912a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718781"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Kryptering av säkerhetskopieringsdata med kundhanterade nycklar

Azure Backup kan du kryptera dina säkerhetskopierade data med kund hanterade nycklar (CMK) i stället för att använda plattform hanterade nycklar, vilket är aktiverat som standard. Dina nycklar som används för att kryptera säkerhetskopierade data måste lagras i [Azure Key Vault](../key-vault/index.yml).

Krypteringsnyckeln som används för att kryptera säkerhetskopior kan vara en annan än den som används för källan. Data skyddas med en AES 256-baserad datakrypteringsnyckel (DEK), som i sin tur skyddas med hjälp av dina nycklar (KEK). Detta ger dig fullständig kontroll över data och nycklar. För att tillåta kryptering måste Recovery Services-valvet beviljas åtkomst till krypteringsnyckeln i Azure Key Vault. Du kan ändra nyckeln vid behov.

I den här artikeln beskrivs följande:

- Skapa ett Recovery Services-valv
- Konfigurera Recovery Services-valvet för att kryptera säkerhetskopierade data med kund hanterade nycklar
- Utföra säkerhetskopiering till valv som krypterats med kund hanterade nycklar
- Återställa data från säkerhetskopior

## <a name="before-you-start"></a>Innan du börjar

- Med den här funktionen kan du kryptera **nya Recovery Services-valv endast**. Valv som innehåller befintliga objekt som har registrerats eller som har försökt registreras till det stöds inte.

- När det har aktiverats för ett Recovery Services-valv går det inte att återställa kryptering med kund hanterade nycklar till att använda plattform hanterade nycklar (standard). Du kan ändra krypteringsnycklarna enligt dina krav.

- Den här funktionen stöder för närvarande inte säkerhetskopiering med **MARS-agenten** och du kanske inte kan använda ett CMK-krypterat valv för samma. MARS-agenten använder en användar lösenfrasbaserad kryptering. Den här funktionen stöder inte heller säkerhetskopiering av klassiska virtuella datorer.

- Den här funktionen är inte relaterad [till Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md), som använder gästbaserad kryptering av en virtuell dators diskar med BitLocker (för Windows) och DM-Crypt (för Linux)

- Recovery Services-valvet kan endast krypteras med nycklar som lagras i Azure Key Vault som finns i **samma region.** Dessutom måste nycklarna bara vara **RSA-nycklar** och ska vara i **aktiverat** tillstånd.

- Det finns för närvarande inte stöd för att flytta CMK-krypterade Recovery Services-valv mellan resursgrupper och prenumerationer.
- När du flyttar ett Recovery Services-valv som redan är krypterat med kund hanterade nycklar till en ny klientorganisation måste du uppdatera Recovery Services-valvet för att återskapa och konfigurera om valvets hanterade identitet och CMK (som ska finnas i den nya klientorganisationen). Om detta inte görs misslyckas säkerhetskopierings- och återställningsåtgärder. Dessutom måste alla RBAC-behörigheter (rollbaserad åtkomstkontroll) som konfigurerats i prenumerationen konfigureras om.

- Den här funktionen kan konfigureras via Azure Portal och PowerShell.

    >[!NOTE]
    >Använd Az-modulen 5.3.0 eller högre för att använda kund hanterade nycklar för säkerhetskopieringar i Recovery Services-valvet.
    
    >[!Warning]
    >Om du använder PowerShell för att hantera krypteringsnycklar för säkerhetskopiering rekommenderar vi inte att du uppdaterar nycklarna från portalen.<br>Om du uppdaterar nyckeln från portalen kan du inte använda PowerShell för att uppdatera krypteringsnyckeln ytterligare förrän en PowerShell-uppdatering som stöder den nya modellen är tillgänglig. Du kan dock fortsätta att uppdatera nyckeln från Azure Portal.

Om du inte har skapat och konfigurerat Recovery Services-valvet kan du [läsa hur du gör det här.](backup-create-rs-vault.md)

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Konfigurera ett valv för kryptering med kund hanterade nycklar

Det här avsnittet omfattar följande steg:

1. Aktivera hanterad identitet för Recovery Services-valvet

1. Tilldela behörigheter till valvet för att få åtkomst till krypteringsnyckeln i Azure Key Vault

1. Aktivera mjuk borttagning och rensningsskydd på Azure Key Vault

1. Tilldela krypteringsnyckeln till Recovery Services-valvet

Det är nödvändigt att alla dessa steg följs i den ordning som nämns ovan för att uppnå de avsedda resultaten. Varje steg beskrivs i detalj nedan.

## <a name="enable-managed-identity-for-your-recovery-services-vault"></a>Aktivera hanterad identitet för Recovery Services-valvet

Azure Backup använder system tilldelade hanterade identiteter och användar tilldelade hanterade identiteter för att autentisera Recovery Services-valvet för att få åtkomst till krypteringsnycklar som lagras i Azure Key Vault. Följ stegen nedan om du vill aktivera hanterad identitet för Recovery Services-valvet.

>[!NOTE]
>När den har aktiverats får den **hanterade identiteten** inte inaktiveras (inte ens tillfälligt). Inaktivering av den hanterade identiteten kan leda till inkonsekvent beteende.

### <a name="enable-system-assigned-managed-identity-for-the-vault"></a>Aktivera system tilldelad hanterad identitet för valvet

**I portalen:**

1. Gå till ditt Recovery Services-valv – > **identitet**

    ![Identitetsinställningar](media/encryption-at-rest-with-cmk/enable-system-assigned-managed-identity-for-vault.png)

1. Gå till fliken **System tilldelad.**

1. Ändra **Status till** **På.**

1. Klicka **på Spara** för att aktivera identiteten för valvet.

Ett objekt-ID genereras, som är valvets system tilldelade hanterade identitet.

>[!NOTE]
>När den har aktiverats får den hanterade identiteten inte inaktiveras (inte ens tillfälligt). Inaktivering av den hanterade identiteten kan leda till inkonsekvent beteende.


**Med PowerShell:**

Använd kommandot [Update-AzRecoveryServicesVault för](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault) att aktivera system tilldelad hanterad identitet för Recovery Services-valvet.

Exempel:

```AzurePowerShell
$vault=Get-AzRecoveryServicesVault -ResourceGroupName "testrg" -Name "testvault"

Update-AzRecoveryServicesVault -IdentityType SystemAssigned -VaultId $vault.ID

$vault.Identity | fl
```

Utdata:

```output
PrincipalId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
TenantId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Type        : SystemAssigned
```

### <a name="assign-user-assigned-managed-identity-to-the-vault"></a>Tilldela en användar tilldelad hanterad identitet till valvet

Utför följande steg för att tilldela den användar tilldelade hanterade identiteten för Recovery Services-valvet:

1.  Gå till ditt Recovery Services-valv – > **identitet**

    ![Tilldela en användar tilldelad hanterad identitet till valvet](media/encryption-at-rest-with-cmk/assign-user-assigned-managed-identity-to-vault.png)

1.  Gå till fliken **Användar tilldelad.**

1.  Klicka **på +Lägg till** för att lägga till en användar tilldelad hanterad identitet.

1.  På **bladet Lägg till användar tilldelad** hanterad identitet som öppnas väljer du prenumerationen för din identitet.

1.  Välj identiteten i listan. Du kan också filtrera efter namnet på identiteten eller resursgruppen.

1.  När du är klar klickar **du på** Lägg till för att slutföra tilldelning av identiteten.

## <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>Tilldela behörigheter till Recovery Services-valvet för att få åtkomst till krypteringsnyckeln i Azure Key Vault

>[!Note]
>Om du använder användar tilldelade identiteter måste samma behörigheter tilldelas till den användar tilldelade identiteten.

Nu måste du tillåta Recovery Services-valvet att komma åt Azure Key Vault som innehåller krypteringsnyckeln. Detta görs genom att tillåta att Recovery Services-valvets hanterade identitet får åtkomst till Key Vault.

**I portalen:**

1. Gå till Azure Key Vault -> **åtkomstprinciper**. Fortsätt till **+Lägg till åtkomstprinciper.**

    ![Lägga till åtkomstprinciper](./media/encryption-at-rest-with-cmk/access-policies.png)

1. Under **Nyckelbehörigheter** väljer du **Hämta,** **Lista,** **Packa upp nyckel och** **Omsluta** nyckelåtgärder. Detta anger vilka åtgärder på nyckeln som tillåts.

    ![Tilldela nyckelbehörigheter](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. Gå till **Välj huvudnamn** och sök efter ditt valv i sökrutan med dess namn eller hanterade identitet. När det visas väljer du valvet och **väljer Välj** längst ned i fönstret.

    ![Välj huvudnamn](./media/encryption-at-rest-with-cmk/select-principal.png)

1. När du är klar väljer du **Lägg till** för att lägga till den nya åtkomstprincipen.

1. Välj **Spara** för att spara ändringar som gjorts i åtkomstprincipen för Azure Key Vault.

## <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>Aktivera mjuk borttagning och rensningsskydd på Azure Key Vault

Du måste aktivera **skydd mot mjuk borttagning och rensning** på Azure Key Vault som lagrar krypteringsnyckeln. Du kan göra detta från Azure Key Vault användargränssnitt enligt nedan. (Du kan också ange dessa egenskaper när du skapar Key Vault). Läs mer om dessa Key Vault [här](../key-vault/general/soft-delete-overview.md).

![Aktivera mjuk borttagning eller rensningsskydd](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

Du kan också aktivera mjuk borttagning och rensningsskydd via PowerShell med hjälp av stegen nedan:

1. Logga in på ditt Azure-konto.

    ```azurepowershell
    Login-AzAccount
    ```

1. Välj den prenumeration som innehåller valvet.

    ```azurepowershell
    Set-AzContext -SubscriptionId SubscriptionId
    ```

1. Aktivera mjuk borttagning

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

1. Aktivera rensningsskydd

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="assign-encryption-key-to-the-rs-vault"></a>Tilldela krypteringsnyckel till RS-valvet

>[!NOTE]
> Kontrollera följande innan du fortsätter:
>
> - Alla steg som nämns ovan har slutförts:
>   - Recovery Services-valvets hanterade identitet har aktiverats och har tilldelats nödvändiga behörigheter
>   - Funktionen Azure Key Vault har aktiverat mjuk borttagning och rensningsskydd
> - Recovery Services-valvet som du vill aktivera CMK-kryptering **för har inga** objekt skyddade eller registrerade i det

När ovanstående är säkerställt fortsätter du med att välja krypteringsnyckeln för valvet.

### <a name="to-assign-the-key-in-the-portal"></a>Tilldela nyckeln i portalen

1. Gå till Recovery Services-valvet – > **egenskaper**

    ![Krypteringsinställningar](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. Välj **Uppdatera** under **Krypteringsinställningar.**

1. I fönstret Krypteringsinställningar väljer du **Använd din egen nyckel** och fortsätter att ange nyckeln på något av följande sätt. **Kontrollera att den nyckel som du vill använda är en RSA 2048-nyckel som är i ett aktiverat tillstånd.**

    1. Ange den **nyckel-URI** som du vill kryptera data med i det här Recovery Services-valvet. Du måste också ange den prenumeration där Azure Key Vault (som innehåller den här nyckeln) finns. Den här nyckel-URI:en kan hämtas från motsvarande nyckel i Azure Key Vault. Se till att nyckel-URI:en kopieras korrekt. Vi rekommenderar att du använder knappen **Kopiera till Urklipp som** medföljer nyckelidentifieraren.

        >[!NOTE]
        >När du anger krypteringsnyckeln med hjälp av nyckel-URI roteras inte nyckeln automatiskt. Så viktiga uppdateringar måste göras manuellt genom att den nya nyckeln anges när det behövs.

        ![Ange nyckel-URI](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. Bläddra och välj nyckeln från Key Vault i nyckelväljarfönstret.

        >[!NOTE]
        >När du anger krypteringsnyckeln med hjälp av nyckelväljarfönstret roteras nyckeln automatiskt när en ny version av nyckeln aktiveras. [Läs mer](#enabling-auto-rotation-of-encryption-keys) om hur du aktiverar automatisk rotation av krypteringsnycklar.

        ![Välj nyckel från nyckelvalvet](./media/encryption-at-rest-with-cmk/key-vault.png)

1. Välj **Spara**.

1. **Spåra förlopp och status för** uppdatering av krypteringsnyckel: Du kan  spåra förlopp och status för tilldelningen av krypteringsnyckel med hjälp av vyn Säkerhetskopieringsjobb i det vänstra navigeringsfältet. Statusen bör snart ändras till **Slutfört.** Valvet krypterar nu alla data med den angivna nyckeln som KEK.

    ![Statusen har slutförts](./media/encryption-at-rest-with-cmk/status-succeeded.png)

    Krypteringsnyckeluppdateringarna loggas också i valvets aktivitetslogg.

    ![Aktivitetslogg](./media/encryption-at-rest-with-cmk/activity-log.png)

### <a name="to-assign-the-key-with-powershell"></a>Tilldela nyckeln med PowerShell

Använd kommandot [Set-AzRecoveryServicesVaultProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultproperty) för att aktivera kryptering med kund hanterade nycklar och för att tilldela eller uppdatera krypteringsnyckeln som ska användas.

Exempel:

```azurepowershell
$keyVault = Get-AzKeyVault -VaultName "testkeyvault" -ResourceGroupName "testrg" 
$key = Get-AzKeyVaultKey -VaultName $keyVault -Name "testkey" 
Set-AzRecoveryServicesVaultProperty -EncryptionKeyId $key.ID -KeyVaultSubscriptionId "xxxx-yyyy-zzzz"  -VaultId $vault.ID


$enc=Get-AzRecoveryServicesVaultProperty -VaultId $vault.ID
$enc.encryptionProperties | fl
```

Utdata:

```output
EncryptionAtRestType          : CustomerManaged
KeyUri                        : testkey
SubscriptionId                : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 
LastUpdateStatus              : Succeeded
InfrastructureEncryptionState : Disabled
```

>[!NOTE]
> Den här processen är densamma när du vill uppdatera eller ändra krypteringsnyckeln. Om du vill uppdatera och använda en nyckel från en annan Key Vault (skiljer sig från den som används för närvarande) kontrollerar du att:
>
> - Nyckelvalvet finns i samma region som Recovery Services-valvet
>
> - Mjuk borttagning och rensning har aktiverats för nyckelvalvet
>
> - Recovery Services-valvet har de behörigheter som krävs för att komma åt nyckelvalvet.

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>Backa upp till ett valv som krypterats med kund hanterade nycklar

Innan du fortsätter med att konfigurera skydd rekommenderar vi starkt att du ser till att följande checklista följs. Detta är viktigt eftersom kryptering med kund hanterade nycklar inte kan aktiveras på ett icke-CMK-krypterat valv när ett objekt har konfigurerats för säkerhetskopiering (eller försök att konfigureras) och fortsätter att använda plattform hanterade nycklar.

>[!IMPORTANT]
> Innan du fortsätter med att konfigurera skyddet måste **du ha** slutfört följande steg:
>
>1. Skapat ditt Backup-valv
>1. Aktiverade Recovery Services-valvets system tilldelade hanterade identitet eller tilldelade en användar tilldelad hanterad identitet till valvet
>1. Tilldelade behörigheter till ditt Säkerhetskopieringsvalv (eller den användar tilldelade hanterade identiteten) för åtkomst till krypteringsnycklar från Key Vault
>1. Aktiverat skydd för mjuk borttagning och rensning för Key Vault
>1. Tilldelat en giltig krypteringsnyckel för säkerhetskopieringsvalvet
>
>Om alla ovanstående steg har bekräftats fortsätter du först med att konfigurera säkerhetskopieringen.

Processen för att konfigurera och utföra säkerhetskopieringar till ett Recovery Services-valv som är krypterat med kund hanterade nycklar är samma som för ett valv som använder plattformsbaserade nycklar, utan ändringar i **upplevelsen**. Detta gäller för [säkerhetskopiering](./quick-backup-vm-portal.md) av virtuella Azure-datorer samt säkerhetskopiering av arbetsbelastningar som körs på en virtuell dator (till exempel SAP HANA [,](./tutorial-backup-sap-hana-db.md) [SQL Server databaser).](./tutorial-sql-backup.md)

## <a name="restoring-data-from-backup"></a>Återställa data från en säkerhetskopia

### <a name="vm-backup"></a>Säkerhetskopiering av virtuell dator

Data som lagras i Recovery Services-valvet kan återställas enligt stegen som beskrivs [här.](./backup-azure-arm-restore-vms.md) När du återställer från ett Recovery Services-valv som krypterats med kund hanterade nycklar kan du välja att kryptera återställda data med en diskkrypteringsuppsättning (DES).

#### <a name="restoring-vm--disk"></a>Återställa virtuell dator/disk

1. När du återställer disk/virtuell dator från en återställningspunkt för ögonblicksbilder krypteras de återställda data med de DES som används för att kryptera den virtuella källdatorns diskar.

1. När du återställer disk/virtuell dator från en återställningspunkt med återställningstyp som "valv" kan du välja att de återställda data ska krypteras med hjälp av en DES som anges vid tidpunkten för återställningen. Du kan också välja att fortsätta med återställningen av data utan att ange en DES, i vilket fall den krypteras med Microsoft-hanterade nycklar.

Du kan kryptera den återställda disken/den virtuella datorn när återställningen är klar, oavsett vilket val som gjorts när återställningen initieras.

![Återställningspunkter](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>Välj en diskkrypteringsuppsättning vid återställning från valvåterställningspunkt

**I portalen:**

Diskkrypteringsuppsättningen anges under Krypteringsinställningar i återställningsfönstret, enligt nedan:

1. I Kryptera **diskar med din nyckel väljer** du **Ja.**

1. I listrutan väljer du de DES som du vill använda för de återställda diskarna. **Kontrollera att du har åtkomst till DES.**

>[!NOTE]
>Möjligheten att välja en DES under återställning är inte tillgänglig om du återställer en virtuell dator som använder Azure Disk Encryption.

![Kryptera disk med din nyckel](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

**Med PowerShell:**

Använd kommandot [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) med parametern [ ] för att ange de DES som ska användas för att kryptera den `-DiskEncryptionSetId <string>` återställda disken. [](/powershell/module/az.compute/get-azdiskencryptionset) Mer information om hur du återställer diskar från säkerhetskopiering av virtuella datorer finns i [den här artikeln.](./backup-azure-vms-automation.md#restore-an-azure-vm)

Exempel:

```azurepowershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $vault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $vault.ID
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $vault.ID
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -DiskEncryptionSetId “testdes1” -VaultId $vault.ID
```

#### <a name="restoring-files"></a>Återställa filer

När du utför en filåterställning krypteras de återställda data med nyckeln som används för att kryptera målplatsen.

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>Återställa SAP HANA/SQL-databaser i virtuella Azure-datorer

När du återställer från en säkerhetskopierad SAP HANA/SQL-databas som körs på en virtuell Azure-dator krypteras återställda data med hjälp av krypteringsnyckeln som används på mållagringsplatsen. Det kan vara en kund hanterad nyckel eller en plattforms hanterad nyckel som används för att kryptera diskarna på den virtuella datorn.

## <a name="additional-topics"></a>Ytterligare information

### <a name="enable-encryption-using-customer-managed-keys-at-vault-creation-in-preview"></a>Aktivera kryptering med kund hanterade nycklar när valvet skapas (i förhandsversion)

>[!NOTE]
>Aktivering av kryptering vid valvskapande med kund hanterade nycklar är en begränsad offentlig förhandsversion och kräver att prenumerationer tillåts. Om du vill registrera dig för förhandsversionen fyller [du i formuläret](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURDNTVVhGOUxXSVBZMEwxUU5FNDkyQkU4Ny4u) och skriver till oss på [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

När din prenumeration tillåts visas fliken **Kryptering för säkerhetskopiering.** På så sätt kan du aktivera kryptering på säkerhetskopian med kund hanterade nycklar när du skapar ett nytt Recovery Services-valv. Utför följande steg för att aktivera krypteringen:

1. Bredvid fliken Grundläggande inställningar går du till fliken Kryptering **för säkerhetskopiering** och anger **krypteringsnyckeln** och identiteten som ska användas för kryptering.

   ![Aktivera kryptering på valvnivå](media/encryption-at-rest-with-cmk/enable-encryption-using-cmk-at-vault.png)


   >[!NOTE]
   >Inställningarna gäller endast säkerhetskopiering och är valfria.

1. Välj **Använd kund hanterad nyckel** som krypteringstyp.

1. Om du vill ange vilken nyckel som ska användas för kryptering väljer du lämpligt alternativ.

   Du kan ange URI:en för krypteringsnyckeln eller bläddra och välja nyckeln. När du anger nyckeln med alternativet **Välj Key Vault** aktiveras automatisk rotation av krypteringsnyckeln automatiskt. [Läs mer om automatisk rotation.](#enabling-auto-rotation-of-encryption-keys) 

1. Ange den användartilldeerade hanterade identiteten för att hantera kryptering med kundhanterade nycklar. Klicka **på Välj** för att bläddra och välja den identitet som krävs.

1. När du är klar fortsätter du med att lägga till taggar (valfritt) och fortsätter att skapa valvet.

### <a name="enabling-auto-rotation-of-encryption-keys"></a>Aktivera automatisk rotation av krypteringsnycklar

När du anger den kund hanterade nyckeln som måste användas för att kryptera säkerhetskopior använder du följande metoder för att ange den:

- Ange nyckel-URI
- Välj från Key Vault

Med alternativet **Välj Key Vault** kan du aktivera automatisk rotation för den valda nyckeln. Detta eliminerar det manuella arbetet med att uppdatera till nästa version. Men med det här alternativet:
- Uppdatering av nyckelversion kan ta upp till en timme att gälla.
- När en ny version av nyckeln gäller ska den gamla versionen också vara tillgänglig (i aktiverat tillstånd) för minst ett efterföljande säkerhetskopieringsjobb efter att nyckeluppdateringen har aktiverats.

### <a name="using-azure-policies-for-auditing-and-enforcing-encryption-utilizing-customer-managed-keys-in-preview"></a>Använda Azure-principer för granskning och framtvingande av kryptering med kund hanterade nycklar (i förhandsversion)

Azure Backup kan du använda Azure-princip för att granska och framtvinga kryptering med kund hanterade nycklar för data i Recovery Services-valvet. Använda Azure-principer:

- Granskningsprincipen kan användas för granskningsvalv med kryptering med kund hanterade nycklar som aktiveras efter 2021-04-01. För valv med CMK-kryptering aktiverat före det här datumet kan principen misslyckas med att tillämpas eller kan visa falska negativa resultat (det vill säga dessa valv kan rapporteras som icke-kompatibla, trots att **CMK-kryptering** är aktiverat).
- Om du vill använda granskningsprincipen för granskningsvalv med **CMK-kryptering** aktiverat före 2021-04-01 använder du Azure Portal för att uppdatera en krypteringsnyckel. Detta hjälper till att uppgradera till den nya modellen. Om du inte vill ändra krypteringsnyckeln anger du samma nyckel igen via nyckel-URI eller alternativet för nyckelval. 

   >[!Warning]
    >Om du använder PowerShell för att hantera krypteringsnycklar för säkerhetskopiering rekommenderar vi inte att du uppdaterar nycklarna från portalen.<br>Om du uppdaterar nyckeln från portalen kan du inte använda PowerShell för att uppdatera krypteringsnyckeln ytterligare förrän en PowerShell-uppdatering som stöder den nya modellen är tillgänglig. Du kan dock fortsätta att uppdatera nyckeln från Azure Portal.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>Kan jag kryptera ett befintligt Backup-valv med kund hanterade nycklar?

Nej, CMK-kryptering kan endast aktiveras för nya valv. Valvet får därför aldrig ha några objekt som skyddas. I själva verket måste inga försök att skydda objekt till valvet göras innan kryptering med kund hanterade nycklar kan användas.

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>Jag försökte skydda ett objekt i mitt valv, men det misslyckades, och valvet innehåller fortfarande inte några objekt som skyddas till det. Kan jag aktivera CMK-kryptering för det här valvet?

Nej, valvet får inte ha gjort några försök att skydda objekt till det tidigare.

### <a name="i-have-a-vault-thats-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>Jag har ett valv som använder CMK-kryptering. Kan jag senare återgå till kryptering med plattforms hanterade nycklar även om jag har säkerhetskopierade objekt som skyddas i valvet?

Nej, när du har aktiverat CMK-kryptering kan den inte återställas till att använda plattform hanterade nycklar. Du kan ändra de nycklar som används enligt dina krav.

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>Gäller CMK-kryptering Azure Backup även för Azure Site Recovery?

Nej, den här artikeln beskriver endast kryptering av säkerhetskopierade data. För Azure Site Recovery måste du ange egenskapen separat som tillgänglig från tjänsten.

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>Jag missade ett av stegen i den här artikeln och gick vidare för att skydda min datakälla. Kan jag fortfarande använda CMK-kryptering?

Om du inte följer stegen i artikeln och fortsätter att skydda objekt kan det leda till att valvet inte kan använda kryptering med kund hanterade nycklar. Vi rekommenderar därför att du refererar till den [här checklistan innan](#backing-up-to-a-vault-encrypted-with-customer-managed-keys) du fortsätter för att skydda objekt.

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>Lägger användningen av CMK-kryptering till kostnaden för mina säkerhetskopior?

Om du använder CMK-kryptering för säkerhetskopiering medför det inga ytterligare kostnader för dig. Du kan dock fortsätta att medföra kostnader för att använda din Azure Key Vault där nyckeln lagras.

## <a name="next-steps"></a>Nästa steg

- [Översikt över säkerhetsfunktioner i Azure Backup](security-overview.md)
