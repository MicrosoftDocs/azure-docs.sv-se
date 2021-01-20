---
title: Återställa Azure-Managed Disks
description: Lär dig hur du återställer Azure-Managed Disks från Azure Portal.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 848a7476b1c5095d4e4d3156d4c7ce33da777090
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611142"
---
# <a name="restore-azure-managed-disks-in-preview"></a>Återställa Azure Managed Disks (för hands version)

>[!IMPORTANT]
>Azure disk Backup är i för hands version utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). För region tillgänglighet, se [support mat ris](disk-backup-support-matrix.md).
>
>[Fyll i det här formuläret](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) om du vill registrera dig för för hands versionen.

I den här artikeln förklaras hur du återställer [Azure-Managed disks](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview) från en återställnings punkt som skapats av Azure Backup.

För närvarande stöds inte alternativet Original-Location återställning (OLR) för återställning genom att ersätta den befintliga käll disken där säkerhets kopiorna gjordes. Du kan återställa från en återställnings punkt för att skapa en ny disk antingen i samma resurs grupp som käll disken från vilken säkerhets kopian gjordes eller i någon annan resurs grupp. Detta kallas Alternate-Location återställning (återställning till) och detta hjälper till att hålla både käll disken och den återställda (nya) disken.

I den här artikeln får du lära dig att:

- Återställ för att skapa en ny disk

- Spåra status för återställnings åtgärden

## <a name="restore-to-create-a-new-disk"></a>Återställ för att skapa en ny disk

Säkerhets kopierings valv använder hanterad identitet för att få åtkomst till andra Azure-resurser. För att återställa från säkerhets kopian kräver säkerhets kopierings valvets hanterade identitet en uppsättning behörigheter för resurs gruppen där disken ska återställas.

Säkerhets kopierings valvet använder en systemtilldelad hanterad identitet, som är begränsad till en per resurs och är kopplad till livs cykeln för den här resursen. Du kan bevilja behörighet till den hanterade identiteten med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC). Hanterad identitet är ett tjänst objekt av en särskild typ som bara kan användas med Azure-resurser. Läs mer om [hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Följande krav krävs för att utföra en återställnings åtgärd:

1. Tilldela rollen **disk återställnings operatör** till säkerhets kopierings valvets hanterade identitet i resurs gruppen där disken ska återställas av Azure Backup-tjänsten.

    >[!NOTE]
    > Du kan välja samma resurs grupp som käll disken där säkerhets kopiorna tas eller till någon annan resurs grupp i samma eller en annan prenumeration.

    1. Gå till resurs gruppen där disken ska återställas. Till exempel är resurs gruppen *TargetRG*.

    1. Gå till **åtkomst kontroll (IAM)** och välj **Lägg till roll tilldelningar**

    1. I rutan till höger kontext väljer du **disk återställnings operator** i list rutan **roll** . Välj den hanterade identiteten för säkerhets kopierings valvet och **Spara**.

        >[!TIP]
        >Ange namnet på säkerhets kopierings valvet för att välja valvets hanterade identitet.

        ![Välj rollen disk återställnings operatör](./media/restore-managed-disks/disk-restore-operator-role.png)

1. Kontrol lera att den hanterade identiteten för säkerhets kopierings valvet har rätt uppsättning roll tilldelningar i resurs gruppen där disken kommer att återställas.

    1. Gå till **säkerhets kopierings valv – > identitet** och välj **Azure Role-tilldelningar**

        ![Välj Azure Role-tilldelningar](./media/restore-managed-disks/azure-role-assignments.png)

    1. Kontrol lera att rollen, resurs namnet och resurs typen visas korrekt.

        ![Verifiera roll, resurs namn och resurs typ](./media/restore-managed-disks/verify-role.png)

    >[!NOTE]
    >När roll tilldelningarna visas korrekt på portalen kan det ta ungefär 15 minuter innan behörigheten tillämpas på säkerhets kopierings valvets hanterade identitet.
    >
    >Under schemalagda säkerhets kopieringar eller en säkerhets kopiering på begäran lagrar Azure Backup diskens stegvisa ögonblicks bilder i den ögonblicks bilds resurs grupp som angavs när säkerhets kopian av disken konfigurerades. Azure Backup använder dessa stegvisa ögonblicks bilder under återställnings åtgärden. Om ögonblicks bilderna tas bort eller flyttas från resurs gruppen för ögonblicks bilder eller om roll tilldelningarna för säkerhets kopierings valvet har återkallats på resurs gruppen för ögonblicks bilder, kommer återställningen att Miss förflyttning

1. Om disken som ska återställas är krypterad med [Kundhanterade nycklar (CMK)](https://docs.microsoft.com/azure/virtual-machines/disks-enable-customer-managed-keys-portal) eller med hjälp av [Double Encryption med hjälp av plattforms hanterade nycklar och Kundhanterade nycklar](https://docs.microsoft.com/azure/virtual-machines/disks-enable-double-encryption-at-rest-portal), tilldelar du sedan rollen **läsare** behörighet till säkerhets kopierings valvets hanterade identitet på **disk krypterings uppsättnings** resursen.

När förutsättningarna är uppfyllda följer du stegen nedan för att utföra återställnings åtgärden.

1. I [Azure Portal](https://portal.azure.com/)går du till **säkerhets kopierings Center**. Välj **säkerhets kopierings instanser** under avsnittet **Hantera** . I listan över säkerhets kopierings instanser väljer du den disk säkerhets kopierings instans som du vill utföra återställnings åtgärden för.

    ![Lista över säkerhets kopierings instanser](./media/restore-managed-disks/backup-instances.png)

    Alternativt kan du utföra den här åtgärden från det säkerhets kopierings valv som du använde för att konfigurera säkerhets kopiering för disken.

1. På sidan **säkerhets kopierings instans** väljer du den återställnings punkt som du vill använda för att utföra återställnings åtgärden och väljer **Återställ**.

    ![Välj återställnings punkt](./media/restore-managed-disks/select-restore-point.png)

1. I **återställnings** arbets flödet granskar du **grunderna** och **väljer fliken återställnings punkt** information och väljer **sedan Nästa: återställnings parametrar**.

    ![Granska grunderna och välj information om återställnings punkt](./media/restore-managed-disks/review-information.png)

1. På fliken **återställnings parametrar** väljer du den **mål prenumeration** och **mål resurs grupp** där du vill återställa säkerhets kopian. Ange namnet på disken som ska återställas. Välj **Nästa: granska + Återställ**.

    ![Återställ parametrar](./media/restore-managed-disks/restore-parameters.png)

    >[!TIP]
    >Diskar som säkerhets kopie ras av Azure Backup med hjälp av disk säkerhets kopierings lösningen kan också säkerhets kopie ras genom att Azure Backup hjälp av Azure VM backup-lösningen med Recovery Services-valvet. Om du har konfigurerat skyddet av den virtuella Azure-datorn som disken är ansluten till kan du även använda åtgärden för återställning av virtuella Azure-datorer. Du kan välja att återställa den virtuella datorn eller diskar och filer eller mappar från återställnings punkten för motsvarande instans av Azure VM-säkerhetskopiering. Mer information finns i [säkerhets kopiering av virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/about-azure-vm-restore).

1. När verifieringen är klar väljer du **Återställ** för att starta återställnings åtgärden.

    ![Initiera återställnings åtgärd](./media/restore-managed-disks/initiate-restore.png)

    >[!NOTE]
    > Det kan ta några minuter innan verifieringen har slutförts innan du kan utlösa återställnings åtgärden. Verifieringen kan Miss lyckas om:
    >
    > - Det finns redan en disk med samma namn som angavs i det **återställda disk namnet** i **mål resurs gruppen**
    > - den hanterade identiteten för säkerhets kopierings valvet har inte några giltiga roll tilldelningar i **mål resurs gruppen**
    > - Säkerhets kopierings valvets hanterade identitets roll tilldelningar återkallas på **resurs gruppen för ögonblicks bilder** där stegvisa ögonblicks bilder lagras
    > - Om stegvisa ögonblicks bilder tas bort eller flyttas från resurs gruppen för ögonblicks bilder

Restore kommer att skapa en ny disk från den valda återställnings punkten i mål resurs gruppen som angavs under återställningen. Om du vill använda den återställda disken på en befintlig virtuell dator måste du utföra fler steg:

- Om den återställda disken är en data disk kan du koppla en befintlig disk till en virtuell dator. Om den återställda disken är en operativ system disk kan du byta OS-disk för en virtuell dator från Azure Portal under fönstret **virtuell dator** -> **diskar** i avsnittet **Inställningar** .

    ![Växla OS-diskar](./media/restore-managed-disks/swap-os-disks.png)

- För virtuella Windows-datorer, om den återställda disken är en datadisk, följer du anvisningarna för att [Koppla från den ursprungliga data disken](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-the-portal) från den virtuella datorn. [Anslut sedan den återställda disken](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) till den virtuella datorn. Följ anvisningarna för att [Växla OS-disken](https://docs.microsoft.com/azure/virtual-machines/windows/os-disk-swap) för den virtuella datorn med den återställda disken.

- För virtuella Linux-datorer, om den återställda disken är en datadisk, följer du anvisningarna för att [Koppla från den ursprungliga data disken](https://docs.microsoft.com/azure/virtual-machines/linux/detach-disk#detach-a-data-disk-using-the-portal) från den virtuella datorn. [Anslut sedan den återställda disken](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#attach-an-existing-disk) till den virtuella datorn. Följ anvisningarna för att [Växla OS-disken](https://docs.microsoft.com/azure/virtual-machines/linux/os-disk-swap) för den virtuella datorn med den återställda disken.

Vi rekommenderar att du återkallar rollen för **disk återställnings operatören** från säkerhets kopierings valvets hanterade identitet i **mål resurs gruppen** när återställnings åtgärden har slutförts.

## <a name="track-a-restore-operation"></a>Spåra en återställnings åtgärd

När du har utlöst återställnings åtgärden skapar säkerhets kopierings tjänsten ett jobb för spårning. Azure Backup visar meddelanden om jobbet i portalen. Så här visar du återställnings jobbets förlopp:

1. Gå till sidan **säkerhets kopierings instans** . Den visar instrument panelen jobb med drift och status för de senaste sju dagarna.

    ![Instrument panel för jobb](./media/restore-managed-disks/jobs-dashboard.png)

1. Om du vill visa status för återställnings åtgärden väljer du **Visa alla** för att visa pågående och tidigare jobb för den här säkerhets kopierings instansen.

    ![Välj Visa alla](./media/restore-managed-disks/view-all.png)

1. Granska listan över säkerhets kopierings-och återställnings jobb och deras status. Välj ett jobb i listan med jobb för att visa jobb information.

    ![Lista över jobb](./media/restore-managed-disks/list-of-jobs.png)

## <a name="next-steps"></a>Nästa steg

- [Vanliga frågor och svar om Azure disk backup](disk-backup-faq.md)
