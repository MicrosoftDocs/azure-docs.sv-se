---
title: Säkerhetskopiera Windows-datorer med hjälp av MARS-agenten
description: Använd MARS-agenten (Microsoft Azure Recovery Services) för att säkerhetskopiera Windows-datorer.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 54932192d61633da55657e2ba57adf4e30c4fbc7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98702776"
---
# <a name="back-up-windows-server-files-and-folders-to-azure"></a>Säkerhetskopiera Windows Server-filer och-mappar till Azure

Den här artikeln beskriver hur du säkerhetskopierar Windows-datorer med hjälp av [Azure Backup](backup-overview.md) -tjänsten och Microsoft Azure Recovery Services mars-agenten. MARS kallas även Azure Backup agenten.

I den här artikeln får du lära dig att:

> [!div class="checklist"]
>
> * Verifiera kraven
> * Skapa en säkerhets kopierings princip och ett schema.
> * Utföra en säkerhets kopiering på begäran.

## <a name="before-you-start"></a>Innan du börjar

* Lär dig hur [Azure Backup använder mars-agenten för att säkerhetskopiera Windows-datorer](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
* Lär dig mer om den [säkerhets kopierings arkitektur](backup-architecture.md#architecture-back-up-to-dpmmabs) som kör mars-agenten på en sekundär MABS eller Data Protection Manager Server.
* Granska [vad som stöds och vad du kan säkerhetskopiera](backup-support-matrix-mars-agent.md) av mars-agenten.
* [Kontrol lera Internet åtkomst](install-mars-agent.md#verify-internet-access) på de datorer som du vill säkerhetskopiera.
* Om MARS-agenten inte är installerad, lär du dig hur du installerar den [här](install-mars-agent.md).

## <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy

Säkerhets kopierings principen anger när ögonblicks bilder av data ska tas för att skapa återställnings punkter. Det anger också hur länge återställnings punkter ska behållas. Du kan använda MARS-agenten för att konfigurera en säkerhets kopierings princip.

Azure Backup tar inte automatiskt sommar tid (DST) i kontot. Standardvärdet kan orsaka en avvikelse mellan den faktiska tiden och den schemalagda säkerhets kopierings tiden.

Så här skapar du en säkerhetskopieringspolicy:

1. När du har hämtat och registrerat MARS-agenten öppnar du agent konsolen. Du hittar den genom att söka efter **Microsoft Azure Backup** på datorn.  

1. Under **åtgärder** väljer du **Schemalägg säkerhets kopiering**.

    ![Schemalägga en Windows Server-säkerhetskopiering](./media/backup-configure-vault/schedule-first-backup.png)
1. I guiden Schemalägg säkerhets kopiering väljer du **komma igång**  >  **härnäst**.
1. Under **Välj objekt som ska säkerhets kopie ras** väljer du **Lägg till objekt**.

    ![Lägg till objekt som ska säkerhets kopie ras](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. I rutan **Välj objekt** väljer du objekt som ska säkerhets kopie ras och väljer sedan **OK**.

    ![Välj objekt som ska säkerhets kopie ras](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. På sidan **Välj objekt som ska säkerhets kopie ras** väljer du **Nästa**.
1. På sidan **Ange schema för säkerhets kopiering** anger du när du vill göra en daglig eller veckovis säkerhets kopiering. Välj sedan **Nästa**.

    * En återställnings punkt skapas när en säkerhets kopia görs.
    * Antalet återställnings punkter som har skapats i din miljö beror på ditt schema för säkerhets kopiering.
    * Du kan schemalägga upp till tre dagliga säkerhets kopieringar per dag. I följande exempel sker två dagliga säkerhets kopieringar, en vid midnatt och en på 6:00 PM.

        ![Konfigurera ett schema för daglig säkerhets kopiering](./media/backup-configure-vault/day-schedule.png)

    * Du kan också köra vecko Visa säkerhets kopieringar. I följande exempel tas säkerhets kopiorna varje alternativ söndag och onsdag vid 9:30 AM och 1:00.

        ![Konfigurera ett schema för veckovis säkerhets kopiering](./media/backup-configure-vault/week-schedule.png)

1. På sidan **Välj bevarande princip** anger du hur du vill lagra historiska kopior av dina data. Välj sedan **Nästa**.

    * Inställningarna för kvarhållning anger vilka återställnings punkter som ska lagras och hur länge de ska lagras.
    * För en inställning för daglig kvarhållning anger du att vid den tid som anges för daglig kvarhållning, kommer den senaste återställnings punkten att behållas för det angivna antalet dagar. Du kan också ange en bevarande princip per månad för att ange att återställnings punkten som skapades den 30 i varje månad ska lagras i 12 månader.
    * Kvarhållning för dagliga och vecko Visa återställnings punkter sammanfaller vanligt vis med schemat för säkerhets kopiering. Så när schemat utlöser en säkerhets kopia lagras återställnings punkten som säkerhets kopian skapar under den tid som en daglig eller veckovis bevarande princip anger.
    * Se följande exempel:

        * Dagliga säkerhets kopieringar vid midnatt och 6:00 är sparade i sju dagar.
        * Säkerhets kopieringar som gjorts på en lördag vid midnatt och 6:00 PM behålls i fyra veckor.
        * Säkerhets kopieringar som gjorts den sista lördagen i månaden vid midnatt och 6:00 PM behålls i 12 månader.
        * Säkerhets kopieringar som gjorts den senaste lördagen i mars behålls i 10 år.

        ![Exempel på en bevarande princip](./media/backup-configure-vault/retention-example.png)

1. På sidan **Välj typ av första säkerhets kopiering** bestämmer du om du vill ta den första säkerhets kopian över nätverket eller använda säkerhets kopiering offline. För att ta den första säkerhets kopieringen över nätverket väljer du **automatiskt över nätverket**  >  **Nästa**.

    Mer information om säkerhets kopiering offline finns i [använda Azure Data box för säkerhets kopiering offline](offline-backup-azure-data-box.md).

    ![Välj en första säkerhets kopierings typ](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. På sidan **bekräftelse** granskar du informationen och väljer sedan **Slutför**.

    ![Bekräfta säkerhets kopierings typen](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. När du har skapat säkerhets kopierings schemat väljer du **Stäng**.

    ![Visa schema förlopp för säkerhets kopiering](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Skapa en princip på varje dator där agenten är installerad.

### <a name="do-the-initial-backup-offline"></a>Gör den första säkerhets kopieringen offline

Du kan köra en första säkerhets kopiering automatiskt via nätverket eller så kan du säkerhetskopiera offline. Dirigerings-seeding för en första säkerhets kopiering är användbart om du har stora mängder data som kräver mycket bandbredd i nätverket som ska överföras.

För att utföra en offline-överföring:

1. Skriv säkerhets kopierings data till en mellanlagringsplats.
1. Använd AzureOfflineBackupDiskPrep-verktyget för att kopiera data från mellanlagringsplatsen till en eller flera SATA-diskar.

    Verktyget skapar ett Azure-importerat jobb. Mer information finns i [Vad är Azure import/export-tjänsten](../import-export/storage-import-export-service.md).
1. Skicka SATA-diskarna till ett Azure-datacenter.

    På data centret kopieras disk data till ett Azure Storage-konto. Azure Backup kopierar data från lagrings kontot till valvet och stegvisa säkerhets kopieringar schemaläggs.

Mer information om seeding offline finns i [använda Azure Data box för säkerhets kopiering offline](offline-backup-azure-data-box.md).

### <a name="enable-network-throttling"></a>Aktivera nätverks begränsning

Du kan styra hur MARS-agenten använder nätverks bandbredd genom att aktivera nätverks begränsning. Begränsning är användbart om du behöver säkerhetskopiera data under arbets tid, men du vill kontrol lera hur mycket bandbredd som används av säkerhets kopierings-och återställnings aktiviteten.

Nätverks begränsning i Azure Backup använder [tjänst kvalitet (QoS)](/windows-server/networking/technologies/qos/qos-policy-top) på det lokala operativ systemet.

Nätverks begränsning för säkerhets kopieringar är tillgängligt på Windows Server 2012 och senare och på Windows 8 och senare. Operativ system ska köra de senaste Service Pack-versionerna.

Så här aktiverar du nätverks begränsning:

1. I MARS-agenten väljer du **ändra egenskaper**.
1. På fliken **begränsning** väljer du **Aktivera användnings begränsning för Internet bandbredd för säkerhets kopierings åtgärder**.

    ![Konfigurera nätverks begränsning för säkerhets kopierings åtgärder](./media/backup-configure-vault/throttling-dialog.png)
1. Ange tillåten bandbredd under arbets tid och arbets tid. Bandbredds värden börjar på 512 kbit/s och går upp till 1 023 Mbit/s. Välj sedan **OK**.

## <a name="run-an-on-demand-backup"></a>Köra en säkerhetskopiering på begäran

1. I MARS-agenten väljer du **Säkerhetskopiera nu**.

    ![Säkerhetskopiera nu i Windows Server](./media/backup-configure-vault/backup-now.png)

1. Om MARS-agentens version är 2.0.9169.0 eller senare kan du ange ett anpassat kvarhållningsdatum. I avsnittet **Kvarhåll säkerhets kopiering till** väljer du ett datum i kalendern.

   ![Använd kalendern för att anpassa ett kvarhållningsdatum](./media/backup-configure-vault/mars-ondemand.png)

1. På sidan **bekräftelse** granskar du inställningarna och väljer **sedan säkerhetskopiera**.
1. Stäng guiden genom att välja **Stäng** . Om du stänger guiden innan säkerhets kopieringen är klar fortsätter guiden att köras i bakgrunden.

När den första säkerhets kopieringen är klar visas statusen **slutfört jobb** i säkerhets kopierings konsolen.

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>Konfigurera princip för kvarhållning av säkerhets kopiering på begäran

> [!NOTE]
> Den här informationen gäller endast MARS agent-versioner som är äldre än 2.0.9169.0.
>

| Säkerhets kopiering – schema alternativ | Varaktighet för datakvarhållning
| -- | --
| Dag | **Standard kvarhållning**: motsvarar "kvarhållning i dagar för dagliga säkerhets kopieringar." <br/><br/> **Undantag**: om en daglig schemalagd säkerhets kopia som anges för långsiktig kvarhållning (veckor, månader eller år) Miss lyckas, kan en säkerhets kopiering på begäran som aktive ras direkt efter felet anses vara för långsiktig kvarhållning. Annars anses nästa schemalagda säkerhets kopiering för långsiktig kvarhållning.<br/><br/> **Exempel scenario**: den schemalagda säkerhets kopieringen på torsdag klockan 8:00 misslyckades. Den här säkerhets kopian var tänkt för varje vecka, månads vis eller årlig kvarhållning. Den första säkerhets kopieringen på begäran som utlöses innan nästa schemalagda säkerhets kopiering på fredag på 8:00 är automatiskt märkt för varje vecka, månad eller årlig kvarhållning. Den här säkerhets kopieringen ersätter torsdag 8:00 AM-säkerhetskopiering.
| Vecka | **Standard kvarhållning**: en dag. Säkerhets kopieringar på begäran som görs för en data källa med en veckovis säkerhets kopierings policy tas bort nästa dag. De tas bort även om de är de senaste säkerhets kopiorna för data källan. <br/><br/> **Undantag**: om en schemalagd säkerhets kopia som har angetts för långsiktig kvarhållning (veckor, månader eller år) Miss lyckas, Miss lyckas en säkerhets kopiering på begäran som aktive ras direkt efter felet, för långsiktig kvarhållning. Annars anses nästa schemalagda säkerhets kopiering för långsiktig kvarhållning. <br/><br/> **Exempel scenario**: den schemalagda säkerhets kopieringen på torsdag klockan 8:00 misslyckades. Den här säkerhets kopian var tänkt för månatlig eller årlig kvarhållning. Den första säkerhets kopieringen på begäran som utlöses innan nästa schemalagda säkerhets kopiering på torsdag klockan 8:00 är automatiskt märkt för månatlig eller årlig kvarhållning. Den här säkerhets kopieringen ersätter torsdag 8:00 AM-säkerhetskopiering.

Mer information finns i [skapa en säkerhets kopierings princip](#create-a-backup-policy).

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [återställer filer i Azure](backup-azure-restore-windows-server.md).
* Hitta [vanliga frågor om säkerhets kopiering av filer och mappar](backup-azure-file-folder-backup-faq.md)
