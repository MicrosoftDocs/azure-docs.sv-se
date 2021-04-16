---
title: Back up Windows machines by using the MARS agent ( Back up Windows machines by using the MARS agent)
description: Använd MARS Microsoft Azure agenten (Recovery Services) för att operativsystemet ska äldsta till att äldsta till ena Windows-datorerna.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 54628c15ffb51c7157132c9a91f41c16873df340
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519178"
---
# <a name="back-up-windows-server-files-and-folders-to-azure"></a>Back up Windows Server files and folders to Azure

I den här artikeln förklaras hur du kommer [åt Windows-Azure Backup](backup-overview.md) med hjälp av Azure Backup och MARS-agenten (Microsoft Azure Recovery Services). MARS kallas även för Azure Backup agent.

I den här artikeln får du lära dig att:

> [!div class="checklist"]
>
> * Verifiera kraven
> * Skapa en säkerhetskopieringspolicy och ett schema.
> * Utför en säkerhetskopiering på begäran.

## <a name="before-you-start"></a>Innan du börjar

* Lär dig [Azure Backup använder MARS-agenten för att backa Windows-datorer.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)
* Lär dig mer [om säkerhetskopieringsarkitekturen](backup-architecture.md#architecture-back-up-to-dpmmabs) som kör MARS-agenten på en sekundär MABS Data Protection Manager server.
* Gå [igenom vad som stöds och vad du kan backa upp](backup-support-matrix-mars-agent.md) av MARS-agenten.
* [Kontrollera Internetåtkomsten](install-mars-agent.md#verify-internet-access) på de datorer som du vill backa upp.
* Om MARS-agenten inte är installerad kan du läsa om hur du installerar [den här](install-mars-agent.md).

## <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy

Säkerhetskopieringsprincipen anger när du ska ta ögonblicksbilder av data för att skapa återställningspunkter. Den anger också hur länge återställningspunkter ska behållas. Du använder MARS-agenten för att konfigurera en säkerhetskopieringspolicy.

Azure Backup tar inte automatiskt hänsyn till sommartid (DST). Det här standardvärdet kan orsaka viss avvikelse mellan den faktiska tiden och den schemalagda säkerhetskopieringstiden.

Så här skapar du en säkerhetskopieringspolicy:

1. När du har hämtat och registrerat MARS-agenten öppnar du agentkonsolen. Du hittar den genom att söka efter **Microsoft Azure Backup** på datorn.  

1. Under **Åtgärder väljer** du **Schemalägg säkerhetskopiering.**

    ![Schemalägga en Windows Server-säkerhetskopiering](./media/backup-configure-vault/schedule-first-backup.png)
1. I guiden Schemalägg säkerhetskopiering väljer du **Komma igång**  >  **nästa.**
1. Välj **Lägg till objekt under Välj objekt** som ska **backa upp.**

    ![Lägga till objekt som ska backa upp](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. I rutan **Välj objekt** väljer du objekt som ska as tillbaka och väljer sedan **OK.**

    ![Välj objekt som ska backa upp](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. På sidan **Select Items to Back Up (Välj** objekt att backa upp) väljer du **Nästa.**
1. På sidan **Ange schema för säkerhetskopiering** anger du när du ska göra dagliga eller veckovisa säkerhetskopieringar. Välj sedan **Nästa**.

    * En återställningspunkt skapas när en säkerhetskopia tas.
    * Antalet återställningspunkter som skapas i din miljö beror på ditt säkerhetskopieringsschema.
    * Du kan schemalägga upp till tre dagliga säkerhetskopieringar per dag. I följande exempel sker två dagliga säkerhetskopieringar, en vid midnatt och en kl. 18:00.

        ![Konfigurera ett schema för daglig säkerhetskopiering](./media/backup-configure-vault/day-schedule.png)

    * Du kan även köra veckovisa säkerhetskopieringar. I följande exempel tas säkerhetskopior varje annan söndag och onsdag kl. 09:30 och 01:00.

        ![Konfigurera ett schema för veckovis säkerhetskopiering](./media/backup-configure-vault/week-schedule.png)

1. På sidan **Välj kvarhållningsprincip** anger du hur historiska kopior av dina data ska lagras. Välj sedan **Nästa**.

    * Kvarhållningsinställningarna anger vilka återställningspunkter som ska lagras och hur länge de ska lagras.
    * För en inställning för daglig kvarhållning anger du att den senaste återställningspunkten behålls under det angivna antalet dagar vid den tidpunkt som anges för den dagliga kvarhållningen. Eller så kan du ange en princip för månatlig kvarhållning som anger att återställningspunkten som skapades den 30:e i varje månad ska lagras i 12 månader.
    * Kvarhållning för dagliga och veckovisa återställningspunkter överensstämmer vanligtvis med säkerhetskopieringsschemat. Så när schemat utlöser en säkerhetskopiering lagras återställningspunkten som säkerhetskopieringen skapar under den tid som den dagliga eller veckovisa bevarandeprincipen anger.
    * Se följande exempel:

        * Dagliga säkerhetskopior vid midnatt och 18:00 sparas i sju dagar.
        * Säkerhetskopior som tas på en lördag vid midnatt och 18:00 sparas i fyra veckor.
        * Säkerhetskopior som tas den sista lördagen i månaden vid midnatt och 18:00 sparas i 12 månader.
        * Säkerhetskopior som tas den senaste lördagen i mars sparas i 10 år.

        ![Exempel på en bevarandeprincip](./media/backup-configure-vault/retention-example.png)

1. På sidan **Välj typ av första säkerhetskopiering** bestämmer du om du vill göra den första säkerhetskopieringen över nätverket eller använda offlinesäkerhetskopiering. Om du vill göra den första säkerhetskopieringen över nätverket väljer **du Automatiskt över nätverket**  >  **Nästa.**

    Mer information om offlinesäkerhetskopiering finns i [Använda Azure Data Box för säkerhetskopiering offline.](offline-backup-azure-data-box.md)

    ![Välj en första säkerhetskopieringstyp](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. Granska **informationen** på sidan Bekräftelse och välj sedan **Slutför.**

    ![Bekräfta säkerhetskopieringstypen](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. När guiden har skapat säkerhetskopieringsschemat väljer du **Stäng**.

    ![Visa förloppet för säkerhetskopieringsschemat](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Skapa en princip på varje dator där agenten är installerad.

### <a name="do-the-initial-backup-offline"></a>Gör den första säkerhetskopieringen offline

Du kan köra en första säkerhetskopiering automatiskt via nätverket eller säkerhetskopiera offline. Offline-seeding för en första säkerhetskopiering är användbart om du har stora mängder data som kräver mycket nätverksbandbredd för att överföras.

Så här gör du en offlineöverföring:

1. Skriv säkerhetskopierade data till en mellanlagringsplats.
1. Använd verktyget AzureOfflineBackupDiskPrep för att kopiera data från mellanlagringsplatsen till en eller flera SATA-diskar.

    Verktyget skapar ett Azure Import-jobb. Mer information finns i [Vad är Azure Import/Export-tjänsten.](../import-export/storage-import-export-service.md)
1. Skicka SATA-diskarna till ett Azure-datacenter.

    I datacentret kopieras diskdata till ett Azure Storage-konto. Azure Backup kopierar data från lagringskontot till valvet och inkrementella säkerhetskopieringar schemaläggs.

Mer information om offline-seeding finns i [Använda Azure Data Box för offlinesäkerhetskopiering.](offline-backup-azure-data-box.md)

### <a name="enable-network-throttling"></a>Aktivera nätverksbegränsning

Du kan styra hur MARS-agenten använder nätverksbandbredd genom att aktivera nätverksbegränsning. Begränsning är användbart om du behöver säkerhetskopiera data under arbetstid, men vill styra hur mycket bandbredd som säkerhetskopierings- och återställningsaktiviteten använder.

Nätverksbegränsning i Azure Backup använder [QoS (Quality of Service)](/windows-server/networking/technologies/qos/qos-policy-top) på det lokala operativsystemet.

Nätverksbegränsning för säkerhetskopieringar är tillgängligt i Windows Server 2012 och senare, och på Windows 8 och senare. Operativsystemen bör köra de senaste service pack-versionerna.

Så här aktiverar du nätverksbegränsning:

1. I MARS-agenten väljer du **Ändra egenskaper.**
1. På fliken **Begränsning väljer du** Aktivera begränsning av **Internetbandbredd för säkerhetskopieringsåtgärder.**

    ![Konfigurera nätverksbegränsning för säkerhetskopieringsåtgärder](./media/backup-configure-vault/throttling-dialog.png)
1. Ange tillåten bandbredd under arbetstid och icke-arbetstid. Bandbreddsvärden börjar vid 512 kbit/s och går upp till 1 023 Mbit/s. Välj sedan **OK**.

## <a name="run-an-on-demand-backup"></a>Köra en säkerhetskopiering på begäran

1. I MARS-agenten väljer **du Back Up Now ( Back Up Now).**

    ![Back up now in Windows Server](./media/backup-configure-vault/backup-now.png)

1. Om MARS-agentversionen är 2.0.9169.0 eller senare kan du ange ett anpassat kvarhållningsdatum. I avsnittet **Behåll säkerhetskopiering till** väljer du ett datum i kalendern.

   ![Använda kalendern för att anpassa ett kvarhållningsdatum](./media/backup-configure-vault/mars-ondemand.png)

1. På sidan **Bekräftelse** granskar du inställningarna och väljer **Back Up ( Back Up).**
1. Stäng **guiden** genom att välja Stäng. Om du stänger guiden innan säkerhetskopieringen är klar fortsätter guiden att köras i bakgrunden.

När den första säkerhetskopieringen är klar visas **statusen Jobbet har** slutförts i konsolen Säkerhetskopiering.

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>Konfigurera kvarhållningsbeteende för säkerhetskopieringspolicy på begäran

> [!NOTE]
> Den här informationen gäller endast för MARS-agentversioner som är äldre än 2.0.9169.0.
>

| Alternativ för säkerhetskopieringsschema | Varaktighet för databevarande
| -- | --
| Dag | **Standardbevarande:** Motsvarar "kvarhållning i dagar för dagliga säkerhetskopieringar". <br/><br/> **Undantag:** Om en daglig schemalagd säkerhetskopiering som har ställts in för långsiktig kvarhållning (veckor, månader eller år) misslyckas, kommer en säkerhetskopiering på begäran som utlöses direkt efter felet att övervägas för långsiktig kvarhållning. Annars övervägs nästa schemalagda säkerhetskopiering för långsiktig kvarhållning.<br/><br/> **Exempelscenario:** Den schemalagda säkerhetskopieringen på torsdag kl. 08:00 misslyckades. Den här säkerhetskopian togs i beaktande för veckovis, månatlig eller årsvis kvarhållning. Så den första säkerhetskopieringen på begäran som utlöses före nästa schemalagda säkerhetskopiering på fredag kl. 08:00 taggas automatiskt för veckovis, månatlig eller årsvis kvarhållning. Den här säkerhetskopieringen ersätter säkerhetskopieringen torsdag 8:00.
| Vecka | **Standardbevarande:** En dag. Säkerhetskopior på begäran som tas för en datakälla som har en veckovis säkerhetskopieringspolicy tas bort nästa dag. De tas bort även om de är de senaste säkerhetskopiorna för datakällan. <br/><br/> **Undantag:** Om en veckovis schemalagd säkerhetskopiering som har ställts in för långsiktig kvarhållning (veckor, månader eller år) misslyckas, övervägs en säkerhetskopiering på begäran som utlöses direkt efter felet för långsiktig kvarhållning. Annars övervägs nästa schemalagda säkerhetskopiering för långsiktig kvarhållning. <br/><br/> **Exempelscenario:** Den schemalagda säkerhetskopieringen på torsdag kl. 08:00 misslyckades. Den här säkerhetskopian övervägdes för månatlig eller årsbasis för kvarhållning. Så den första säkerhetskopieringen på begäran som utlöses före nästa schemalagda säkerhetskopiering på torsdag kl. 08:00 taggas automatiskt för månatlig eller årsvis kvarhållning. Den här säkerhetskopian ersätter säkerhetskopieringen torsdag 8:00.

Mer information finns i Skapa [en säkerhetskopieringspolicy.](#create-a-backup-policy)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur [du återställer filer i Azure](backup-azure-restore-windows-server.md).
* Hitta [vanliga frågor om att filer och mappar](backup-azure-file-folder-backup-faq.yml)
