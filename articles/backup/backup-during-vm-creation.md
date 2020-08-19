---
title: Aktivera säkerhetskopiering när du skapar en virtuell Azure-dator
description: Beskriver hur du aktiverar säkerhets kopiering när du skapar en virtuell Azure-dator med Azure Backup.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: c744f6aa2bef6d3d6800aa6b6dc077915fc5205b
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586706"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Aktivera säkerhetskopiering när du skapar en virtuell Azure-dator

Använd Azure Backup tjänsten för att säkerhetskopiera virtuella datorer i Azure. Virtuella datorer säkerhets kopie ras enligt ett schema som anges i en säkerhets kopierings princip och återställnings punkter skapas från säkerhets kopieringar. Återställnings punkter lagras i Recovery Services valv.

Den här artikeln beskriver hur du aktiverar säkerhets kopiering när du skapar en virtuell dator (VM) i Azure Portal.  

## <a name="before-you-start"></a>Innan du börjar

- [Kontrol lera](backup-support-matrix-iaas.md#supported-backup-actions) vilka operativ system som stöds om du aktiverar säkerhets kopiering när du skapar en virtuell dator.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Om du inte redan har loggat in på ditt konto loggar du in på [Azure Portal](https://portal.azure.com).

## <a name="create-a-vm-with-backup-configured"></a>Skapa en virtuell dator med säkerhets kopia konfigurerad

1. Klicka på **skapa en resurs**i Azure Portal.

2. I Azure Marketplace klickar du på **Compute**och väljer sedan en avbildning av en virtuell dator.

3. Konfigurera den virtuella datorn i enlighet med [Windows](../virtual-machines/windows/quick-create-portal.md) -eller [Linux](../virtual-machines/linux/quick-create-portal.md) -instruktionerna.

4. Klicka på **på**fliken **hantering** i **Aktivera säkerhets kopiering**.
5. Azure Backup säkerhets kopieringar till ett Recovery Services-valv. Klicka på **Skapa nytt** om du inte har ett befintligt valv.
6. Acceptera det föreslagna valv namnet eller ange ditt eget.
7. Ange eller skapa en resurs grupp där valvet ska placeras. Resurs grupps valvet kan skilja sig från resurs gruppen för den virtuella datorn.

    ![Aktivera säkerhets kopiering för en virtuell dator](./media/backup-during-vm-creation/enable-backup.png)

8. Godkänn standard principen för säkerhets kopiering eller ändra inställningarna.
    - En säkerhets kopierings princip anger hur ofta ögonblicks bilder av den virtuella datorn ska säkerhets kopie ras och hur länge säkerhets kopiorna ska bevaras.
    - Standard principen säkerhetskopierar den virtuella datorn en gång om dagen.
    - Du kan anpassa en egen säkerhets kopierings princip för en virtuell Azure-dator för att ta säkerhets kopior varje dag eller varje vecka.
    - [Läs mer](backup-azure-vms-introduction.md#backup-and-restore-considerations) om säkerhets kopierings överväganden för virtuella Azure-datorer.
    - [Läs mer](backup-instant-restore-capability.md) om funktionen för omedelbar återställning.

      ![Standard princip för säkerhets kopiering](./media/backup-during-vm-creation/daily-policy.png)

>[!NOTE]
>[SSE och PMK är standard krypterings metoder](backup-encryption.md) för virtuella Azure-datorer. Azure Backup stöder säkerhets kopiering och återställning av de här virtuella Azure-datorerna.

## <a name="azure-backup-resource-group-for-virtual-machines"></a>Azure Backup resurs grupp för Virtual Machines

Säkerhets kopierings tjänsten skapar en separat resurs grupp (RG), inte en annan resurs grupp än den virtuella datorns resurs grupp för att lagra återställnings punkt samlingen (RPC). RPC-anläggningarna återställnings punkter för hanterade virtuella datorer. Standard namngivnings formatet för resurs gruppen som skapas av säkerhets kopierings tjänsten är: `AzureBackupRG_<Geo>_<number>` . Exempel: *AzureBackupRG_northeurope_1*. Nu kan du anpassa resurs grupps namnet som skapats av Azure Backup.

Poäng till Anmärkning:

1. Du kan antingen använda standard namnet på RG eller redigera det enligt företagets krav.
2. Du anger RG namn-mönstret som ininformation när du skapar en princip för säkerhets kopiering av virtuella datorer. RG namn ska ha följande format: `<alpha-numeric string>* n <alpha-numeric string>` . ' n ' ersätts med ett heltal (från 1) och används för att skala ut om den första RG är full. En RG kan ha högst 600 RPC-anrop idag.
              ![Välj namn när du skapar en princip](./media/backup-during-vm-creation/create-policy.png)
3. Mönstret bör följa reglerna för namngivning av RG nedan och den totala längden får inte överskrida den maximalt tillåtna längden för RG namn.
    1. Resurs grupp namn får bara innehålla alfanumeriska tecken, punkter, under streck, bindestreck och parenteser. De kan inte sluta med en punkt.
    2. Resurs grupp namn får innehålla upp till 74 tecken, inklusive namnet på RG och suffixet.
4. Det första `<alpha-numeric-string>` är obligatoriskt medan den andra efter "n" är valfri. Detta gäller endast om du ger ett anpassat namn. Om du inte anger något i någon av text rutorna används standard namnet.
5. Du kan redigera namnet på RG genom att ändra principen om och när det behövs. Om namn mönstret ändras kommer nya RPs att skapas i den nya RG. Den gamla RPs kommer dock fortfarande att finnas i den gamla RG och flyttas inte, eftersom RP-samlingen inte stöder resurs flytt. Slutligen kommer RPs att få skräp insamlat när poängen går ut.
![Ändra namn vid ändring av princip](./media/backup-during-vm-creation/modify-policy.png)
6. Vi rekommenderar att du inte låser resurs gruppen som skapas för användning av säkerhets kopierings tjänsten.

Information om hur du konfigurerar Azure Backup resurs grupp för Virtual Machines med hjälp av PowerShell finns i [skapa Azure Backup resurs grupp under kvarhållning av ögonblicks bilder](backup-azure-vms-automation.md#creating-azure-backup-resource-group-during-snapshot-retention).

## <a name="start-a-backup-after-creating-the-vm"></a>Starta en säkerhets kopiering när du har skapat den virtuella datorn

Säkerhets kopian av den virtuella datorn kommer att köras enligt säkerhets kopierings principen. Vi rekommenderar dock att du kör en första säkerhets kopiering.

När den virtuella datorn har skapats gör du följande:

1. I egenskaperna för den virtuella datorn klickar du på **säkerhetskopiera**. VM-statusen är den första säkerhets kopieringen väntar tills den första säkerhets kopieringen körs
2. Klicka på **Säkerhetskopiera nu** för att köra en säkerhets kopiering på begäran.

    ![Köra en säkerhets kopiering på begäran](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Använd en Resource Manager-mall för att distribuera en skyddad virtuell dator

I föregående steg förklaras hur du använder Azure Portal för att skapa en virtuell dator och skydda den i ett Recovery Services-valv. Om du snabbt vill distribuera en eller flera virtuella datorer och skydda dem i ett Recovery Services valv, se mallen [distribuera en virtuell Windows-dator och aktivera säkerhets kopiering](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="next-steps"></a>Nästa steg

Nu när du har skyddat din virtuella dator kan du läsa om hur du hanterar och återställer dem.

- [Hantera och övervaka virtuella datorer](backup-azure-manage-vms.md)
- [Återställa virtuell dator](backup-azure-arm-restore-vms.md)

Om du stöter på några problem kan du [läsa](backup-azure-vms-troubleshoot.md) fel söknings guiden.
