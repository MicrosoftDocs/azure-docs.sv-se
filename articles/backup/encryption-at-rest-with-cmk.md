---
title: Kryptering av säkerhets kopierings data med Kundhanterade nycklar
description: Lär dig hur Azure Backup kan kryptera dina säkerhetskopierade data med Kundhanterade nycklar (CMK).
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: dfed3f983867568befc77d7dbc81cdde70eef9ed
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589613"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Kryptering av säkerhets kopierings data med Kundhanterade nycklar

Med Azure Backup kan du kryptera dina säkerhets kopierings data med Kundhanterade nycklar (CMK) i stället för att använda plattforms hanterade nycklar, som är aktiverade som standard. Dina nycklar som används för att kryptera säkerhetskopierade data måste lagras i [Azure Key Vault](../key-vault/index.yml).

Krypterings nyckeln som används för kryptering av säkerhets kopior kan skilja sig från den som används för källan. Data skyddas med hjälp av en AES 256-baserad data krypterings nyckel (DEK), som i sin tur skyddas med hjälp av dina nycklar (KEK). Detta ger dig fullständig kontroll över data och nycklar. För att tillåta kryptering krävs det att Recovery Services valvet beviljas åtkomst till krypterings nyckeln i Azure Key Vault. Du kan ändra nyckeln som och när det behövs.

I den här artikeln beskrivs följande:

- Skapa ett Recovery Services-valv
- Konfigurera Recovery Services-valvet för att kryptera säkerhetskopierade data med Kundhanterade nycklar
- Utföra säkerhets kopiering till valv som krypterats med Kundhanterade nycklar
- Återställa data från säkerhets kopior

## <a name="before-you-start"></a>Innan du börjar

- Med den här funktionen kan du **bara kryptera nya Recovery Services-valv**. Det finns inte stöd för alla valv som innehåller befintliga objekt som är registrerade eller försökte registreras.

- När den har Aktiver ATS för ett Recovery Services valv kan inte kryptering med Kundhanterade nycklar återställas till med hjälp av plattforms hanterade nycklar (standard). Du kan ändra krypterings nycklarna enligt dina krav.

- Den här funktionen **stöder för närvarande inte säkerhets kopiering med mars-agenten**och du kanske inte kan använda ett CMK-krypterat valv för samma. MARS-agenten använder en kryptering baserad på användar lösen fras. Den här funktionen stöder inte heller säkerhets kopiering av klassiska virtuella datorer.

- Den här funktionen är inte relaterad till [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md), som använder gästkod kryptering av en virtuell dators diskar med BitLocker (för Windows) och dm-crypt (för Linux)

- Recovery Services-valvet kan bara krypteras med nycklar som lagras i en Azure Key Vault, som finns i **samma region**. Nycklar måste också vara **RSA 2048-nycklar** enbart och ska vara i **aktiverat** läge.

- Det finns för närvarande inte stöd för att flytta CMK-krypterade Recovery Services valv över resurs grupper och prenumerationer.

- Den här funktionen kan för närvarande bara konfigureras från Azure Portal.

Om du inte har skapat och konfigurerat Recovery Services-valvet kan du [läsa hur du gör det här](backup-create-rs-vault.md).

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Konfigurera ett valv för kryptering med Kundhanterade nycklar

Det här avsnittet omfattar följande steg:

1. Aktivera hanterad identitet för Recovery Services valvet

1. Tilldela behörighet till valvet för att få åtkomst till krypterings nyckeln i Azure Key Vault

1. Aktivera mjuk borttagning och tömning av skydd på Azure Key Vault

1. Tilldela krypterings nyckeln till Recovery Services-valvet

Alla dessa steg måste följas i den ordning som anges ovan för att uppnå avsedda resultat. Varje steg beskrivs i detalj nedan.

### <a name="enable-managed-identity-for-your-recovery-services-vault"></a>Aktivera hanterad identitet för Recovery Services valvet

Azure Backup använder systemtilldelad hanterad identitet för att autentisera Recovery Services-valvet för att få åtkomst till krypterings nycklar som lagras i Azure Key Vault. Om du vill aktivera hanterad identitet för Recovery Services valvet följer du stegen som beskrivs nedan.

>[!NOTE]
>Den hanterade identiteten får inte inaktive ras (även tillfälligt). Inaktive ring av den hanterade identiteten kan leda till inkonsekvent beteende.

1. Gå till Recovery Services valv – > **identitet**

    ![Identitets inställningar](./media/encryption-at-rest-with-cmk/managed-identity.png)

1. Ändra **statusen** till **på** och klicka på **Spara**.

1. Ett objekt-ID genereras, vilket är den systemtilldelade hanterade identiteten för valvet.

### <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>Tilldela behörighet till Recovery Servicess valvet för att få åtkomst till krypterings nyckeln i Azure Key Vault

Du måste nu tillåta Recovery Services-valvet att få åtkomst till Azure Key Vault som innehåller krypterings nyckeln. Detta görs genom att tillåta att Recovery Services valvets hanterade identitet får åtkomst till Key Vault.

1. Gå till dina Azure Key Vault-> **åtkomst principer**. Fortsätt till **+ Lägg till åtkomst principer**.

    ![Lägg till åtkomst principer](./media/encryption-at-rest-with-cmk/access-policies.png)

1. Under **nyckel behörigheter**väljer du **Hämta**, **lista**, **packa upp nyckel** och **packa upp nyckel** åtgärder. Detta anger de åtgärder på nyckeln som ska tillåtas.

    ![Tilldela nyckel behörigheter](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. Gå till **Välj huvud konto** och Sök efter ditt valv i sökrutan med hjälp av dess namn eller hanterad identitet. När den visas väljer du valvet och klickar på **Välj** längst ned i fönstret.

    ![Välj huvud konto](./media/encryption-at-rest-with-cmk/select-principal.png)

1. När du är färdig klickar du på **Lägg** till för att lägga till den nya åtkomst principen.

1. Klicka på **Spara** för att spara ändringar som gjorts i åtkomst principen för Azure Key Vault.

### <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>Aktivera mjuk borttagning och tömning av skydd på Azure Key Vault

Du måste **Aktivera mjuk borttagning och tömning av skydd** på Azure Key Vault som lagrar din krypterings nyckel. Du kan göra detta från Azure Key Vault användar gränssnittet som visas nedan. (Du kan också ange dessa egenskaper när du skapar Key Vault). Läs mer om dessa Key Vaults egenskaper [här](../key-vault/general/soft-delete-overview.md).

![Aktivera mjuk borttagning och rensnings skydd](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

Du kan också aktivera mjuk borttagning och rensning av skydd via PowerShell med hjälp av stegen nedan:

1. Logga in på ditt Azure-konto.

    ```azurepowershell
    Login-AzAccount
    ```

1. Välj den prenumeration som innehåller ditt valv.

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

1. Aktivera rensnings skydd

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

### <a name="assign-encryption-key-to-the-rs-vault"></a>Tilldela en krypterings nyckel till RS-valvet

>[!NOTE]
> Innan du fortsätter bör du kontrol lera följande:
>
> - Alla steg som nämns ovan har slutförts:
>   - Det Recovery Services valvets hanterade identitet har Aktiver ATS och har tilldelats nödvändiga behörigheter
>   - Azure Key Vault har mjuk borttagning och rensning-skydd aktiverat
> - Recovery Services-valvet som du vill aktivera CMK-kryptering för har inga objekt som är skyddade eller registrerade för det

När ovanstående är säkerställd fortsätter du med att välja krypterings nyckel för ditt valv.

Så här tilldelar du nyckeln:

1. Gå till Recovery Services valv – > **Egenskaper**

    ![Krypteringsinställningar](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. Klicka på **Uppdatera** under **krypterings inställningar**.

1. I fönstret krypterings inställningar väljer du **Använd din egen nyckel** och fortsätter att ange nyckeln på något av följande sätt. **Kontrol lera att den nyckel som du vill använda är en RSA 2048-nyckel, som är i ett aktiverat läge.**

    1. Ange den **nyckel-URI** som du vill kryptera data i Recovery Services valvet med. Du måste också ange den prenumeration där Azure Key Vault (som innehåller den här nyckeln) finns. Den här nyckel-URI: n kan hämtas från motsvarande nyckel i Azure Key Vault. Se till att nyckel-URI: n har kopierats korrekt. Vi rekommenderar att du använder knappen **Kopiera till Urklipp** som medföljer nyckel identifieraren.

        ![Ange nyckel-URI](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. Bläddra och välj nyckeln från Key Vault i fönstret nyckel väljare.

        ![Välj nyckel från Key Vault](./media/encryption-at-rest-with-cmk/key-vault.png)

1. Klicka på **Spara**.

1. **Spårnings förlopp för uppdatering av krypterings nyckel:** Du kan följa förloppet för nyckel tilldelningen med hjälp av **aktivitets loggen** i Recovery Services valvet. Statusen bör snart ändras till **lyckades**. Ditt valv kommer nu att kryptera alla data med den angivna nyckeln som KEK.

    ![Spåra förloppet med aktivitets loggen](./media/encryption-at-rest-with-cmk/activity-log.png)

    ![Statusen lyckades](./media/encryption-at-rest-with-cmk/status-succeeded.png)

>[!NOTE]
> Den här processen är densamma när du vill uppdatera/ändra krypterings nyckeln. Om du vill uppdatera och använda en nyckel från en annan Key Vault (skiljer sig från den som används för närvarande) kontrollerar du att:
>
> - Key Vault finns i samma region som Recovery Services-valvet
>
> - Nyckel valvet har mjuk borttagnings-och rensnings skydd aktiverat
>
> - Recovery Services-valvet har de behörigheter som krävs för att komma åt Key Vault.

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>Säkerhetskopiera till ett valv som är krypterat med Kundhanterade nycklar

Innan du fortsätter att konfigurera skyddet rekommenderar vi starkt att du ser till att följande check lista följer. Detta är viktigt sedan när ett objekt har kon figurer ATS för säkerhets kopiering (eller försökte konfigureras) till ett icke-CMK krypterat valv, det går inte att aktivera kryptering med Kundhanterade nycklar på den och fortsätter att använda plattforms hanterade nycklar.

>[!IMPORTANT]
> Innan du fortsätter att konfigurera skydd måste **du ha** slutfört följande steg:
>
>1. Aktiverade din prenumeration för att använda Kundhanterade nycklar för ditt säkerhets kopierings valv.
>1. Säkerhets kopierings valvet har skapats
>1. Aktiverade den systemtilldelade säkerhets kopierings valvets tilldelade hanterade identitet
>1. Tilldelade behörigheter till säkerhets kopierings valvet för att få åtkomst till krypterings nycklar från din Key Vault
>1. Aktiverat mjuk borttagning och tömning av skydd för din Key Vault
>1. Tilldelat en giltig krypterings nyckel för säkerhets kopierings valvet
>
>Om alla ovanstående steg har bekräftats fortsätter du med att konfigurera säkerhets kopieringen.

Processen för att konfigurera och utföra säkerhets kopieringar till ett Recovery Services valv som är krypterade med Kundhanterade nycklar är samma som för ett valv som använder plattforms hanterade nycklar, utan **ändringar i upplevelsen**. Detta gäller även för [säkerhets kopiering av virtuella Azure-datorer](./quick-backup-vm-portal.md) samt säkerhets kopiering av arbets belastningar som körs i en virtuell dator (till exempel [SAP HANA](./tutorial-backup-sap-hana-db.md) [SQL Server](./tutorial-sql-backup.md) databaser).

## <a name="restoring-data-from-backup"></a>Återställa data från säkerhets kopia

### <a name="vm-backup"></a>VM-säkerhetskopiering

Data som lagras i Recovery Services-valvet kan återställas enligt de steg som beskrivs [här](./backup-azure-arm-restore-vms.md). När du återställer från ett Recovery Services valv som är krypterat med Kundhanterade nycklar kan du välja att kryptera återställda data med en disk krypterings uppsättning (DES).

#### <a name="restoring-vm--disk"></a>Återställa VM/disk

1. När du återställer disk/VM från en "ögonblicks bild" återställnings punkt krypteras de återställda data med det DES som används för att kryptera den virtuella käll datorns diskar.

1. När du återställer disk/virtuell dator från en återställnings punkt med återställnings typ som "valv" kan du välja att återställa återställda data med hjälp av ett DES som anges vid återställnings tillfället. Alternativt kan du välja att fortsätta med att återställa data utan att ange ett DES, då det kommer att krypteras med hjälp av Microsoft-hanterade nycklar.

Du kan kryptera den återställda disken/virtuella datorn när återställningen har slutförts, oavsett vad som gjorts när du påbörjar återställningen.

![Återställnings punkter](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>Välj en disk krypterings uppsättning vid återställning från valv återställnings punkt

Disk krypterings uppsättningen anges under krypterings inställningar i återställnings fönstret, som du ser nedan:

1. I **kryptera disk (er) med din nyckel**väljer du **Ja**.

1. Välj den DES som du vill använda för de återställda diskarna i list rutan. **Se till att du har åtkomst till DES.**

>[!NOTE]
>Möjligheten att välja ett DES när du återställer är inte tillgänglig om du återställer en virtuell dator som använder Azure Disk Encryption.

![Kryptera disk med din nyckel](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

#### <a name="restoring-files"></a>Filer återställs

När du utför en fil återställning krypteras de återställda data med den nyckel som används för att kryptera mål platsen.

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>Återställa SAP HANA/SQL-databaser i virtuella Azure-datorer

När du återställer från en säkerhets kopie rad SAP HANA/SQL-databas som körs i en virtuell Azure-dator krypteras de återställda data med hjälp av krypterings nyckeln som används på mål lagrings platsen. Det kan vara en kundhanterad nyckel eller en plattforms-hanterad nyckel som används för att kryptera diskarna på den virtuella datorn.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>Kan jag kryptera ett befintligt säkerhets kopierings valv med Kundhanterade nycklar?

Nej, CMK-kryptering kan bara aktive ras för nya valv. Därför måste valvet aldrig ha haft några objekt skyddade. I själva verket måste inga försök att skydda några objekt till valvet göras innan du aktiverar kryptering med Kundhanterade nycklar.

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>Jag försökte skydda ett objekt till mitt valv, men det misslyckades och valvet innehåller fortfarande inga objekt som är skyddade för det. Kan jag aktivera CMK-kryptering för det här valvet?

Nej, valvet får inte ha haft några försök att skydda några objekt tidigare.

### <a name="i-have-a-vault-that-is-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>Jag har ett valv som använder CMK-kryptering. Kan jag senare återställa till kryptering med hjälp av plattforms hanterade nycklar även om jag har säkerhetskopierade objekt skyddade till valvet?

Nej, när du har aktiverat CMK-kryptering kan den inte återställas för att använda plattforms hanterade nycklar. Du kan ändra de nycklar som används enligt dina krav.

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>Gäller CMK-kryptering för Azure Backup även för Azure Site Recovery?

Nej, den här artikeln handlar bara om kryptering av säkerhets kopierings data. För Azure Site Recovery måste du ange egenskapen separat som tillgänglig från tjänsten.

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>Jag missade något av stegen i den här artikeln och gick vidare för att skydda min data källa. Kan jag fortfarande använda CMK-kryptering?

Inte följer stegen i artikeln och fortsätter att skydda objekt kan leda till att valvet inte kan använda kryptering med Kundhanterade nycklar. Vi rekommenderar därför att du läser [den här check listan](#backing-up-to-a-vault-encrypted-with-customer-managed-keys) innan du fortsätter att skydda objekt.

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>Lägger du till kostnaden för mina säkerhets kopieringar med CMK-kryptering?

Om du använder CMK-kryptering för säkerhets kopiering debiteras du inte ytterligare kostnader. Du kan dock fortsätta att betala kostnader för att använda Azure Key Vault där din nyckel lagras.

## <a name="next-steps"></a>Nästa steg

- [Översikt över säkerhetsfunktioner i Azure Backup](security-overview.md)
