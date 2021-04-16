---
title: Återställa Azure-Managed Disks
description: Lär dig hur du återställer Azure Managed Disks från Azure Portal.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: c57d60047a5bcef58c721ee25bd8a0b3ed523aa4
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517206"
---
# <a name="restore-azure-managed-disks"></a>Återställa Azure-Managed Disks

Den här artikeln beskriver hur du [återställer Azure Managed Disks](../virtual-machines/managed-disks-overview.md) från en återställningspunkt som skapats av Azure Backup.

För närvarande stöds Original-Location Recovery (OLR) att återställa genom att ersätta den befintliga källdisken som säkerhetskopiorna gjordes från inte. Du kan återställa från en återställningspunkt för att skapa en ny disk antingen i samma resursgrupp som den källdisk som säkerhetskopiorna gjordes från eller i någon annan resursgrupp. Detta kallas för Alternate-Location Recovery (ALR) och hjälper till att behålla både källdisken och den återställda (nya) disken.

I den här artikeln får du lära dig att:

- Återställa för att skapa en ny disk

- Spåra status för återställningsåtgärden

## <a name="restore-to-create-a-new-disk"></a>Återställa för att skapa en ny disk

Backup Vault använder hanterad identitet för att få åtkomst till andra Azure-resurser. För att återställa från en säkerhetskopia kräver Backup-valvets hanterade identitet en uppsättning behörigheter för den resursgrupp där disken ska återställas.

Säkerhetskopieringsvalvet använder en system tilldelad hanterad identitet, som är begränsad till en per resurs och är kopplad till den här resursens livscykel. Du kan bevilja behörigheter till den hanterade identiteten med hjälp av rollbaserad åtkomstkontroll i Azure (Azure RBAC). Hanterad identitet är ett huvudnamn för tjänsten av en särskild typ som endast kan användas med Azure-resurser. Läs mer om [hanterade identiteter.](../active-directory/managed-identities-azure-resources/overview.md)

Följande förutsättningar krävs för att utföra en återställningsåtgärd:

1. Tilldela rollen **Diskåterställningsoperatör** till backup-valvets hanterade identitet i resursgruppen där disken kommer att återställas av Azure Backup tjänsten.

    >[!NOTE]
    > Du kan välja samma resursgrupp som källdisken där säkerhetskopiorna ska tas eller till en annan resursgrupp inom samma eller en annan prenumeration.

    1. Gå till resursgruppen där disken ska återställas. Resursgruppen är till exempel *TargetRG*.

    1. Gå till **Åtkomstkontroll (IAM) och** välj Lägg **till rolltilldelningar**

    1. I det högra kontextfönstret väljer du **Diskåterställningsoperatör** i **listrutan** Roll. Välj den hanterade identiteten för säkerhetskopieringsvalvet och **spara**.

        >[!TIP]
        >Skriv namnet på säkerhetskopieringsvalvet för att välja valvets hanterade identitet.

        ![Välj operatörsroll för diskåterställning](./media/restore-managed-disks/disk-restore-operator-role.png)

1. Kontrollera att den hanterade identiteten för säkerhetskopieringsvalvet har rätt uppsättning rolltilldelningar för resursgruppen där disken ska återställas.

    1. Gå till **Säkerhetskopieringsvalv – > identitet** och välj **Azure-rolltilldelningar**

        ![Välj Azure-rolltilldelningar](./media/restore-managed-disks/azure-role-assignments.png)

    1. Kontrollera att rollen, resursnamnet och resurstypen visas korrekt.

        ![Verifiera roll, resursnamn och resurstyp](./media/restore-managed-disks/verify-role.png)

    >[!NOTE]
    >Även om rolltilldelningarna visas korrekt på portalen kan det ta cirka 15 minuter innan behörigheten tillämpas på säkerhetskopieringsvalvet hanterade identitet.
    >
    >Under schemalagda säkerhetskopieringar eller en säkerhetskopiering på begäran, Azure Backup disk inkrementella ögonblicksbilder i resursgruppen för ögonblicksbilder som tillhandahålls när du konfigurerar säkerhetskopiering av disken. Azure Backup använder dessa inkrementella ögonblicksbilder under återställningen. Om ögonblicksbilderna tas bort eller flyttas från resursgruppen för ögonblicksbilder eller om rolltilldelningarna för säkerhetskopieringsvalvet återkallas i resursgruppen för ögonblicksbilder misslyckas återställningen.

1. Om disken som ska återställas krypteras med kund-hanterade nycklar [(CMK)](../virtual-machines/disks-enable-customer-managed-keys-portal.md) eller med dubbel kryptering med  plattformsbaserade nycklar och kundhanteringsnycklar, [](../virtual-machines/disks-enable-double-encryption-at-rest-portal.md)tilldelar du behörigheten Läsare till Backup Vault:s hanterade identitet på resursen för diskkrypteringsuppsättning. 

När kraven är uppfyllda följer du dessa steg för att utföra återställningen.

1. I [Azure Portal](https://portal.azure.com/)du till **Backup Center**. Välj **Säkerhetskopieringsinstanser** under **avsnittet** Hantera. I listan över säkerhetskopieringsinstanser väljer du den disksäkerhetskopieringsinstans som du vill utföra återställningen för.

    ![Lista över säkerhetskopieringsinstanser](./media/restore-managed-disks/backup-instances.png)

    Alternativt kan du utföra den här åtgärden från säkerhetskopieringsvalvet som du använde för att konfigurera säkerhetskopiering för disken.

1. På skärmen **Säkerhetskopieringsinstans** väljer du den återställningspunkt som du vill använda för att utföra återställningsåtgärden och väljer **Återställ**.

    ![Välj återställningspunkt](./media/restore-managed-disks/select-restore-point.png)

1. I **arbetsflödet Återställ** granskar du fliken Grundläggande inställningar och Välj **återställningspunkt** och väljer **Nästa: Återställ parametrar.** 

    ![Granska grundläggande information och välj information om återställningspunkt](./media/restore-managed-disks/review-information.png)

1. På fliken **Återställ parametrar** väljer du den **målprenumeration** **och målresursgrupp** där du vill återställa säkerhetskopian. Ange namnet på disken som ska återställas. Välj **Nästa: Granska + återställ**.

    ![Återställningsparametrar](./media/restore-managed-disks/restore-parameters.png)

    >[!TIP]
    >Diskar som säkerhetskopieras av Azure Backup med disksäkerhetskopieringslösningen kan också säkerhetskopieras av Azure Backup med hjälp av Azure VM-säkerhetskopieringslösningen med Recovery Services-valvet. Om du har konfigurerat skydd för den virtuella Azure-datorn som den här disken är ansluten till kan du också använda återställningsåtgärden för virtuella Azure-datorer. Du kan välja att återställa den virtuella datorn eller diskar och filer eller mappar från återställningspunkten för motsvarande azure VM-säkerhetskopieringsinstans. Mer information finns i Säkerhetskopiering [av virtuella Azure-datorer.](./about-azure-vm-restore.md)

1. När verifieringen har lyckats väljer du **Återställ** för att starta återställningsåtgärden.

    ![Starta återställningsåtgärden](./media/restore-managed-disks/initiate-restore.png)

    >[!NOTE]
    > Verifieringen kan ta några minuter att slutföra innan du kan utlösa återställningsåtgärden. Verifieringen kan misslyckas om:
    >
    > - Det finns redan en disk med samma **namn som det återställda disknamnet** i **målresursgruppen**
    > - Backup-valvets hanterade identitet har inte giltiga rolltilldelningar i **målresursgruppen**
    > - Rolltilldelningarna för säkerhetskopieringsvalvet för hanterad identitet återkallas i resursgruppen **Ögonblicksbild där** inkrementella ögonblicksbilder lagras
    > - Om inkrementella ögonblicksbilder tas bort eller flyttas från resursgruppen för ögonblicksbilder

Återställningen skapar en ny disk från den valda återställningspunkten i målresursgruppen som angavs under återställningen. Om du vill använda den återställda disken på en befintlig virtuell dator måste du utföra fler steg:

- Om den återställda disken är en datadisk kan du ansluta en befintlig disk till en virtuell dator. Om den återställda disken är en OS-disk kan du växla OS-disken  för en virtuell dator från Azure Portal under fönstret Virtuell dator – > **diskmenyn** i **avsnittet** Inställningar.

    ![Växla OS-diskar](./media/restore-managed-disks/swap-os-disks.png)

- Om den återställda disken är en datadisk för virtuella Windows-datorer följer du anvisningarna för att koppla från den ursprungliga [datadisken](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-the-portal) från den virtuella datorn. Anslut [sedan den återställda disken](../virtual-machines/windows/attach-managed-disk-portal.md) till den virtuella datorn. Följ instruktionerna för [att växla OS-disken](../virtual-machines/windows/os-disk-swap.md) på den virtuella datorn med den återställda disken.

- Om den återställda disken är en datadisk för virtuella Linux-datorer följer du anvisningarna för att koppla från den ursprungliga [datadisken](../virtual-machines/linux/detach-disk.md#detach-a-data-disk-using-the-portal) från den virtuella datorn. Anslut [sedan den återställda disken](../virtual-machines/linux/attach-disk-portal.md#attach-an-existing-disk) till den virtuella datorn. Följ instruktionerna för [att växla OS-disken](../virtual-machines/linux/os-disk-swap.md) på den virtuella datorn med den återställda disken.

Vi rekommenderar att du återkallar rolltilldelningen **Diskåterställningsoperatör** från Backup-valvets hanterade identitet i målresursgruppen när återställningen har slutförts. 

## <a name="track-a-restore-operation"></a>Spåra en återställningsåtgärd

När du har utlöst återställningsåtgärden skapar säkerhetskopieringstjänsten ett jobb för spårning. Azure Backup visar meddelanden om jobbet i portalen. Så här visar du förloppet för återställningsjobbet:

1. Gå till skärmen **Säkerhetskopieringsinstans.** Den visar jobbinstrumentpanelen med åtgärd och status för de senaste sju dagarna.

    ![Instrumentpanel för jobb](./media/restore-managed-disks/jobs-dashboard.png)

1. Om du vill visa status för återställningsåtgärden väljer du **Visa alla** för att visa pågående och tidigare jobb för den här säkerhetskopieringsinstansen.

    ![Välj Visa alla](./media/restore-managed-disks/view-all.png)

1. Granska listan över säkerhetskopierings- och återställningsjobb och deras status. Välj ett jobb i listan över jobb för att visa jobbinformation.

    ![Lista över jobb](./media/restore-managed-disks/list-of-jobs.png)

## <a name="next-steps"></a>Nästa steg

- [Vanliga frågor och svar om Azure Disk Backup](disk-backup-faq.yml)