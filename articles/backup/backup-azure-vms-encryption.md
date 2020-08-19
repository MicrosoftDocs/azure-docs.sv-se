---
title: Säkerhetskopiera och återställa krypterade virtuella Azure-datorer
description: Beskriver hur du säkerhetskopierar och återställer krypterade virtuella Azure-datorer med tjänsten Azure Backup.
ms.topic: conceptual
ms.date: 08/18/2020
ms.openlocfilehash: 304196f6b517c353cb4fc142129fa4d3007a1d9c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585344"
---
# <a name="back-up-and-restore-encrypted-azure-virtual-machines"></a>Säkerhetskopiera och återställa krypterade virtuella Azure-datorer

Den här artikeln beskriver hur du säkerhetskopierar och återställer virtuella Windows-eller Linux Azure-datorer (VM: ar) med krypterade diskar med hjälp av tjänsten [Azure Backup](backup-overview.md) . Mer information finns i [kryptering av virtuella Azure-säkerhetskopieringar](backup-azure-vms-introduction.md#encryption-of-azure-vm-backups).

## <a name="encryption-using-platform-managed-keys"></a>Kryptering med hjälp av plattforms hanterade nycklar

Som standard krypteras alla diskar i de virtuella datorerna automatiskt i vila med hjälp av PMK (Platform-Managed Keys) som använder [kryptering av lagrings tjänst](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). Du kan säkerhetskopiera de här virtuella datorerna med Azure Backup utan några särskilda åtgärder som krävs för att stödja krypteringen i slutet. Mer information om kryptering med plattforms hanterade nycklar [finns i den här artikeln](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#platform-managed-keys).

![Krypterade diskar](./media/backup-encryption/encrypted-disks.png)

## <a name="encryption-using-customer-managed-keys"></a>Kryptering med kundhanterade nycklar

När du krypterar diskar med anpassade hanterade nycklar (CMK) lagras nyckeln som används för att kryptera diskarna i Azure Key Vault och hanteras av dig. Kryptering för lagringstjänst (SSE) med CMK skiljer sig från Azure Disk Encryption-kryptering (ADE). ADE använder krypterings verktygen i operativ systemet. SSE krypterar data i lagrings tjänsten, så att du kan använda alla operativ system eller avbildningar för dina virtuella datorer. Mer information om kryptering av hanterade diskar med Kundhanterade nycklar finns i [den här artikeln](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys).

## <a name="encryption-support-using-ade"></a>Krypterings stöd med ADE

Azure Backup stöder säkerhets kopiering av virtuella Azure-datorer som har sina operativ system/data diskar krypterade med Azure Disk Encryption (ADE). ADE använder BitLocker för kryptering av virtuella Windows-datorer och dm-crypt-funktionen för virtuella Linux-datorer. ADE integreras med Azure Key Vault för att hantera nycklar och hemligheter för disk kryptering. Key Vault nyckel krypterings nycklar (KeyExchange) kan användas för att lägga till ett extra säkerhets lager, kryptera krypterings hemligheter innan de skrivs till Key Vault.

Azure Backup kan säkerhetskopiera och återställa virtuella Azure-datorer med hjälp av ADE med och utan Azure AD-appen, som sammanfattas i följande tabell.

**Typ av virtuell dator disk** | **ADE (BEK/dm-crypt)** | **ADE och KEK**
--- | --- | ---
**Ohanterade** | Ja | Ja
**Hanterade**  | Ja | Ja

- Lär dig mer om [ade](../security/fundamentals/azure-disk-encryption-vms-vmss.md), [Key Vault](../key-vault/general/overview.md)och [KeyExchange](../virtual-machine-scale-sets/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek).
- Läs [vanliga frågor och svar](../security/fundamentals/azure-disk-encryption-vms-vmss.md) om Azure VM Disk Encryption.

### <a name="limitations"></a>Begränsningar

- Du kan säkerhetskopiera och återställa krypterade virtuella datorer inom samma prenumeration och region.
- Azure Backup stöder virtuella datorer som har krypterats med fristående nycklar. Alla nycklar som ingår i ett certifikat som används för att kryptera en virtuell dator stöds inte för närvarande.
- Du kan säkerhetskopiera och återställa krypterade virtuella datorer i samma prenumeration och region som Recovery Services säkerhets kopierings valv.
- Det går inte att återställa krypterade virtuella datorer på nivån fil/mapp. Du måste återställa hela den virtuella datorn för att återställa filer och mappar.
- När du återställer en virtuell dator kan du inte använda alternativet [Ersätt befintlig virtuell dator](backup-azure-arm-restore-vms.md#restore-options) för krypterade virtuella datorer. Det här alternativet stöds bara för okrypterade hanterade diskar.

## <a name="before-you-start"></a>Innan du börjar

Innan du börjar gör du följande:

1. Se till att du har en eller flera virtuella [Windows](../virtual-machines/linux/disk-encryption-overview.md) -eller [Linux](../virtual-machines/linux/disk-encryption-overview.md) -datorer med ade aktiverat.
2. [Granska support matrisen](backup-support-matrix-iaas.md) för säkerhets kopiering av virtuella Azure-datorer
3. [Skapa](backup-create-rs-vault.md) ett Recovery Services säkerhets kopierings valv om du inte har något.
4. Om du aktiverar kryptering för virtuella datorer som redan har Aktiver ATS för säkerhets kopiering behöver du bara ange säkerhets kopiering med behörigheter för att få åtkomst till Key Vault så att säkerhets kopior kan fortsätta utan avbrott. [Läs mer](#provide-permissions) om att tilldela dessa behörigheter.

Dessutom finns det några saker som du kan behöva göra i vissa fall:

- **Installera VM-agenten på den virtuella datorn**: Azure Backup säkerhetskopierar virtuella Azure-datorer genom att installera ett tillägg till Azure VM-agenten som körs på datorn. Om den virtuella datorn skapades från en Azure Marketplace-avbildning installeras och körs agenten. Om du skapar en anpassad virtuell dator, eller om du migrerar en lokal dator, kan du behöva [Installera agenten manuellt](backup-azure-arm-vms-prepare.md#install-the-vm-agent).

## <a name="configure-a-backup-policy"></a>Konfigurera en säkerhetskopieringspolicy

1. Om du ännu inte har skapat ett Recovery Services säkerhets kopierings valv följer du [dessa anvisningar](backup-create-rs-vault.md).
1. Öppna valvet i portalen och välj **+ säkerhetskopiera** i **översikts** avsnittet.

    ![Säkerhets kopierings fönstret](./media/backup-azure-vms-encryption/select-backup.png)

1. I **säkerhets kopierings mål**  >  **var din arbets belastning körs? väljer du** **Azure**.
1. I **vad vill du säkerhetskopiera?** Välj **virtuell dator**. Välj sedan **säkerhets kopiering**.

      ![Bladet scenario](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

1. Välj säkerhets kopierings princip i **säkerhets kopierings princip**  >  **Choose backup policy**och välj den princip som du vill associera med valvet. Välj sedan **OK**.
    - En säkerhets kopierings princip anger när säkerhets kopieringar görs och hur länge de lagras.
    - Information om standardprincipen visas under den nedrullningsbara menyn.

    ![Välj säkerhets kopierings princip](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

1. Om du inte vill använda standard principen väljer du **Skapa ny**och [skapar en anpassad princip](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

1. Under **Virtual Machines**väljer du **Lägg till**.

    ![Lägg till virtuella datorer](./media/backup-azure-vms-encryption/add-virtual-machines.png)

1. Välj de krypterade virtuella datorer som du vill säkerhetskopiera med hjälp av Välj princip och välj **OK**.

      ![Välj krypterade virtuella datorer](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

1. Om du använder Azure Key Vault visas ett meddelande på sidan valv som Azure Backup behöver skrivskyddad åtkomst till nycklarna och hemligheterna i Key Vault.

    - Om du får det här meddelandet krävs ingen åtgärd.

        ![Åtkomst OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Om du får det här meddelandet måste du ange behörigheter enligt beskrivningen i [proceduren nedan](#provide-permissions).

        ![Åtkomst varning](./media/backup-azure-vms-encryption/access-warning.png)

1. Välj **Aktivera säkerhets kopiering** för att distribuera säkerhets kopierings principen i valvet och aktivera säkerhets kopiering för de valda virtuella datorerna.

## <a name="trigger-a-backup-job"></a>Utlösa ett säkerhets kopierings jobb

Den första säkerhets kopieringen kommer att köras enligt schemat, men du kan köra den direkt på följande sätt:

1. I menyn valv väljer du **säkerhets kopierings objekt**.
2. I **säkerhets kopierings objekt**väljer du **virtuell Azure-dator**.
3. I listan **säkerhets kopierings objekt** väljer du ellipserna (...).
4. Välj **Säkerhetskopiera nu**.
5. I **Säkerhetskopiera nu**använder du kalender kontrollen för att välja den sista dagen som återställnings punkten ska behållas. Välj sedan **OK**.
6. Övervaka Portal meddelanden. Du kan övervaka jobb förloppet i valv instrument panelen > **säkerhets kopierings jobb**  >  **pågår**. Beroende på den virtuella datorns storlek kan det ta en stund att skapa den första säkerhetskopian.

## <a name="provide-permissions"></a>Ange behörigheter

Azure Backup behöver skrivskyddad åtkomst för att säkerhetskopiera nycklar och hemligheter, tillsammans med de associerade virtuella datorerna.

- Din Key Vault är kopplad till Azure AD-klienten för Azure-prenumerationen. Om du är **medlems användare**får Azure Backup få åtkomst till Key Vault utan ytterligare åtgärder.
- Om du är **gäst användare**måste du ange behörigheter för Azure Backup för att få åtkomst till nyckel valvet.

Ange behörigheter:

1. I Azure Portal väljer du **alla tjänster**och söker efter **nyckel valv**.
1. Välj nyckel valvet som är associerat med den krypterade virtuella datorn som du säkerhetskopierar.
1. Välj **åtkomst principer**  >  **Lägg till åtkomst princip**.

    ![Lägg till åtkomst princip](./media/backup-azure-vms-encryption/add-access-policy.png)

1. I **Lägg till åtkomst princip**  >  **Konfigurera från mall (valfritt)** väljer du **Azure Backup**.
    - De behörigheter som krävs är förifyllda för **nyckel behörigheter** och **hemliga behörigheter**.
    - Om den virtuella datorn är krypterad med **endast Bek**tar du bort valet för **nyckel behörigheter** eftersom du bara behöver behörighet för hemligheter.

    ![Val av Azure Backup](./media/backup-azure-vms-encryption/select-backup-template.png)

1. Välj **Lägg till**. **Säkerhets kopierings hanterings tjänsten** har lagts till i **åtkomst principer**.

    ![Åtkomstprinciper](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

1. Välj **Spara** för att ge Azure Backup med behörigheterna.

## <a name="restore-an-encrypted-vm"></a>Återställa en krypterad virtuell dator

Krypterade virtuella datorer kan bara återställas genom att återställa den virtuella dator disken enligt beskrivningen nedan. Det finns inte stöd för att **ersätta befintliga** och **återställa virtuella datorer** .

Återställa krypterade virtuella datorer enligt följande:

1. [Återställa den virtuella dator disken](backup-azure-arm-restore-vms.md#restore-disks).
2. Återskapa den virtuella dator instansen genom att göra något av följande:
    1. Använd mallen som genereras under återställnings åtgärden för att anpassa VM-inställningar och utlösa VM-distribution. [Läs mer](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    2. Skapa en ny virtuell dator från de återställda diskarna med PowerShell. [Läs mer](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).
3. För virtuella Linux-datorer installerar du om ADE-tillägget så att data diskarna är öppna och monterade.

## <a name="next-steps"></a>Nästa steg

Om du stöter på problem kan du läsa följande artiklar:

- [Vanliga fel](backup-azure-vms-troubleshoot.md) vid säkerhets kopiering och återställning av krypterade virtuella Azure-datorer.
- Problem med [Azure VM-agent/säkerhets kopierings tillägg](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) .
