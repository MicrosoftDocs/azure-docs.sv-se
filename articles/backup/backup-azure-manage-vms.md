---
title: Hantera och övervaka virtuella Azure-säkerhetskopieringar
description: Lär dig att hantera och övervaka virtuella Azure-säkerhetskopieringar med hjälp av tjänsten Azure Backup.
ms.topic: conceptual
ms.date: 08/02/2020
ms.openlocfilehash: 51ce88bb67d64ce129a3479d38db9a66dfe65d0a
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635085"
---
# <a name="manage-azure-vm-backups-with-azure-backup-service"></a>Hantera virtuella Azure-säkerhetskopieringar med Azure Backup tjänsten

Den här artikeln beskriver hur du hanterar virtuella datorer i Azure som säkerhets kopie ras med [Azure Backup-tjänsten](backup-overview.md). Artikeln sammanfattar också den säkerhets kopierings information som du hittar på instrument panelen för valvet.

I Azure Portal ger instrument panelen Recovery Services valv till gång till valv information, inklusive:

* Den senaste säkerhets kopieringen, som också är den senaste återställnings punkten.
* Säkerhets kopierings principen.
* Den totala storleken på alla ögonblicks bilder av säkerhets kopior.
* Antalet virtuella datorer som har Aktiver ATS för säkerhets kopiering.

Du kan hantera säkerhets kopior med hjälp av instrument panelen och genom att gå djupare till enskilda virtuella datorer. Öppna valvet på instrument panelen för att starta dator säkerhets kopieringar.

![Fullständig Instrumentpanels vy med skjutreglage](./media/backup-azure-manage-vms/bottom-slider.png)

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="view-vms-on-the-dashboard"></a>Visa virtuella datorer på instrument panelen

Visa virtuella datorer på valv-instrument panelen:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. På menyn till vänster väljer du **Alla tjänster**.

    ![Välj Alla tjänster](./media/backup-azure-manage-vms/select-all-services.png)

1. I dialogrutan **Alla tjänster** anger du *Recovery Services*. Listan med resurser filtreras enligt din inmatning. I resurslistan väljer du **Recovery Services-valv**.

    ![Ange och välja Recovery Services-valv](./media/backup-azure-manage-vms/all-services.png)

    Listan över Recovery Services-valv i prenumerationen visas.

1. För enkel användning väljer du PIN-ikonen bredvid ditt valv namn och väljer **Fäst på instrument panelen**.
1. Öppna instrument panelen för valvet.

    ![Öppna panelen för valv-instrumentpanel och inställningar](./media/backup-azure-manage-vms/full-view-rs-vault.png)

1. På panelen **säkerhets kopierings objekt** väljer du **virtuell Azure-dator**.

    ![Öppna panelen säkerhets kopierings objekt](./media/backup-azure-manage-vms/azure-virtual-machine.png)

1. I rutan **säkerhets kopierings objekt** kan du Visa listan över skyddade virtuella datorer. I det här exemplet skyddar valvet en virtuell dator: *myVMR1*.  

    ![Visa fönstret säkerhets kopierings objekt](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

1. Från valv objektets instrument panel kan du ändra säkerhets kopierings principer, köra en säkerhets kopiering på begäran, stoppa eller återuppta skyddet av virtuella datorer, ta bort säkerhetskopierade data, Visa återställnings punkter och köra en återställning.

    ![Instrument panelen för säkerhets kopierings objekt och fönstret inställningar](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Hantera säkerhets kopierings princip för en virtuell dator

### <a name="modify-backup-policy"></a>Ändra säkerhets kopierings princip

Så här ändrar du en befintlig säkerhets kopierings princip:

1. Logga in på [Azure-portalen](https://portal.azure.com/). Öppna instrument panelen för valvet.
2. Välj säkerhets kopierings policyn för den **virtuella Azure-datorns** typ i **hantera principer för > säkerhets kopiering**.
3. Välj **ändra** och ändra inställningarna.

### <a name="switch-backup-policy"></a>Växla princip för säkerhets kopiering

Så här hanterar du en säkerhets kopierings princip:

1. Logga in på [Azure-portalen](https://portal.azure.com/). Öppna instrument panelen för valvet.
2. På panelen **säkerhets kopierings objekt** väljer du **virtuell Azure-dator**.

    ![Öppna panelen säkerhets kopierings objekt](./media/backup-azure-manage-vms/azure-virtual-machine.png)

3. I rutan **säkerhets kopierings objekt** kan du Visa listan över skyddade virtuella datorer och senaste säkerhets kopierings status med tiden för senaste återställnings punkter.

    ![Visa fönstret säkerhets kopierings objekt](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. Från valv objektets instrument panel kan du välja en säkerhets kopierings princip.

   * Om du vill växla principer väljer du en annan princip och väljer sedan **Spara**. Den nya principen tillämpas omedelbart på valvet.

     ![Välj en säkerhetskopieringsprincip](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Köra en säkerhetskopiering på begäran

Du kan köra en säkerhets kopiering på begäran av en virtuell dator efter att du har konfigurerat skyddet. Tänk på följande:

* Om den första säkerhets kopieringen väntar, skapar säkerhets kopiering på begäran en fullständig kopia av den virtuella datorn i Recovery Services valvet.
* Om den första säkerhets kopieringen är klar kommer en säkerhets kopiering på begäran endast att skicka ändringar från den tidigare ögonblicks bilden till Recovery Services valvet. Det innebär att senare säkerhets kopieringar alltid är stegvisa.
* Kvarhållningsintervallet för en säkerhets kopiering på begäran är det kvarhållningsintervall som du anger när du utlöser säkerhets kopieringen.

> [!NOTE]
> Tjänsten Azure Backup stöder upp till tre säkerhets kopieringar på begäran per dag och en ytterligare schemalagd säkerhets kopiering.

Så här utlöser du en säkerhets kopiering på begäran:

1. Välj **säkerhets kopierings objekt** under **skyddat objekt** på [instrument panelen för valv objekt](#view-vms-on-the-dashboard).

    ![Alternativet Säkerhetskopiera nu](./media/backup-azure-manage-vms/backup-now-button.png)

2. Välj **virtuell Azure-dator** från **typ av säkerhets kopierings hantering**. Fönstret **säkerhets kopierings objekt (virtuell Azure-dator)** visas.
3. Välj en virtuell dator och välj **Säkerhetskopiera nu** för att skapa en säkerhets kopiering på begäran. Fönstret **Säkerhetskopiera nu** visas.
4. I fältet **Behåll säkerhets kopian** till anger du ett datum då säkerhets kopian ska behållas.

    ![Kalendern Säkerhetskopiera nu](./media/backup-azure-manage-vms/backup-now-check.png)

5. Välj **OK** för att köra säkerhets kopierings jobbet.

Om du vill spåra jobbets förlopp väljer du panelen **säkerhets kopierings jobb** på instrument panelen för valvet.

## <a name="stop-protecting-a-vm"></a>Sluta skydda en virtuell dator

Det finns två sätt att sluta skydda en virtuell dator:

* **Stoppa skyddet och behåll säkerhets kopierings data**. Med det här alternativet stoppas alla framtida säkerhets kopierings jobb från att skydda den virtuella datorn. Azure Backups tjänsten behåller dock de återställnings punkter som har säkerhetskopierats.  Du måste betala för att behålla återställnings punkterna i valvet (se [Azure Backup priser](https://azure.microsoft.com/pricing/details/backup/) för mer information). Du kommer att kunna återställa den virtuella datorn om det behövs. Om du väljer att återuppta skyddet för virtuella datorer kan du använda alternativet *återuppta säkerhets kopiering* .
* **Stoppa skyddet och ta bort säkerhetskopierade data**. Med det här alternativet stoppas alla framtida säkerhets kopierings jobb från att skydda din virtuella dator och ta bort alla återställnings punkter. Du kommer inte att kunna återställa den virtuella datorn eller använda alternativet *återuppta säkerhets kopiering* .

>[!NOTE]
>Om du tar bort en data källa utan att stoppa säkerhets kopieringen kommer nya säkerhets kopieringar att Miss lyckas. De gamla återställnings punkterna upphör att gälla enligt principen, men den senaste återställnings punkten kommer alltid att behållas tills du stoppar säkerhets kopieringarna och tar bort data.
>

### <a name="stop-protection-and-retain-backup-data"></a>Stoppa skyddet och behåll säkerhets kopierings data

Stoppa skyddet och behåll data för en virtuell dator:

1. På [valv objektets instrument panel](#view-vms-on-the-dashboard)väljer du **stoppa säkerhets kopiering**.
2. Välj **Behåll säkerhets kopierings data** och bekräfta valet efter behov. Lägg till en kommentar om du vill. Om du inte är säker på objektets namn, hovrar du över utrops tecknet för att visa namnet.

    ![Behåll säkerhets kopierings data](./media/backup-azure-manage-vms/retain-backup-data.png)

Ett meddelande gör att du vet att säkerhets kopierings jobben har stoppats.

### <a name="stop-protection-and-delete-backup-data"></a>Stoppa skyddet och ta bort säkerhets kopierings data

Stoppa skyddet och ta bort data för en virtuell dator:

1. På [valv objektets instrument panel](#view-vms-on-the-dashboard)väljer du **stoppa säkerhets kopiering**.
2. Välj **ta bort säkerhets kopierings data** och bekräfta valet efter behov. Ange namnet på det säkerhetskopierade objektet och Lägg till en kommentar om du vill.

    ![Ta bort säkerhetskopieringsdata](./media/backup-azure-manage-vms/delete-backup-data.png)

> [!NOTE]
> När du har slutfört borttagnings åtgärden bevaras de säkerhetskopierade data i 14 dagar i [läget Soft Deleted](./soft-delete-virtual-machines.md). <br>Dessutom kan du också [Aktivera eller inaktivera mjuk borttagning](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete).

## <a name="resume-protection-of-a-vm"></a>Återuppta skyddet av en virtuell dator

Om du väljer alternativet [stoppa skydd och behåll säkerhets kopierings data](#stop-protection-and-retain-backup-data) under stoppa VM-skyddet kan du använda **återuppta säkerhets kopiering**. Det här alternativet är inte tillgängligt om du väljer alternativet [stoppa skydd och ta bort säkerhets kopierings](#stop-protection-and-delete-backup-data) data eller [tar bort säkerhetskopierade data](#delete-backup-data).

Återuppta skyddet för en virtuell dator:

1. På [valv objektets instrument panel](#view-vms-on-the-dashboard)väljer du **återuppta säkerhets kopiering**.

2. Följ stegen i [hantera säkerhets kopierings principer](#manage-backup-policy-for-a-vm) för att tilldela principen för den virtuella datorn. Du behöver inte välja den virtuella datorns första skydds princip.
3. När du har tillämpat säkerhets kopierings principen på den virtuella datorn visas följande meddelande:

    ![Meddelande som anger att den virtuella datorn har skyddats](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Ta bort säkerhetskopieringsdata

Det finns två sätt att ta bort en virtuell dators säkerhets kopierings data:

* På instrument panelen för valv väljer du stoppa säkerhets kopiering och följer anvisningarna för alternativet [stoppa skydd och ta bort säkerhets kopierings data](#stop-protection-and-delete-backup-data) .

  ![Välja Avbryt säkerhetskopiering](./media/backup-azure-manage-vms/stop-backup-button.png)

* Välj Ta bort säkerhetskopierade data på instrument panelen för valv objekt. Det här alternativet är aktiverat om du har valt att [stoppa skyddet och behålla alternativet Säkerhetskopiera data](#stop-protection-and-retain-backup-data) under stoppa VM-skyddet.

  ![Välj Ta bort säkerhets kopia](./media/backup-azure-manage-vms/delete-backup-button.png)

  * Välj **ta bort säkerhetskopierade data** på [instrument panelen för valv objekt](#view-vms-on-the-dashboard).
  * Ange namnet på det säkerhetskopierade objektet för att bekräfta att du vill ta bort återställnings punkterna.

    ![Ta bort säkerhetskopieringsdata](./media/backup-azure-manage-vms/delete-backup-data.png)

  * Om du vill ta bort säkerhetskopierade data för objektet väljer du **ta bort**. Ett meddelande visas där du vet att säkerhets kopierings data har tagits bort.

För att skydda dina data innehåller Azure Backup funktionen för mjuk borttagning. Med mjuk borttagning, även efter att säkerhets kopieringen (alla återställnings punkter) för en virtuell dator har tagits bort, behålls säkerhets kopierings data i ytterligare 14 dagar. Mer information finns i [dokumentationen om mjuk borttagning](./backup-azure-security-feature-cloud.md).

  > [!NOTE]
  > När du tar bort säkerhetskopierade data tar du bort alla tillhör ande återställnings punkter. Du kan inte välja vissa återställnings punkter att ta bort.

### <a name="backup-item-where-primary-data-source-no-longer-exists"></a>Säkerhets kopierings objekt där den primära data källan inte längre finns

* Om de virtuella Azure-datorerna som kon figurer ATS för Azure Backup antingen tas bort eller flyttas utan att skyddet stoppas, kommer de båda schemalagda säkerhets kopierings jobben och säkerhets kopierings jobben på begäran (ad hoc) att Miss sen Säkerhets kopierings kontrollen visas som kritisk endast för misslyckade säkerhets kopierings jobb på begäran (misslyckade schemalagda jobb visas inte).
* Dessa säkerhets kopierings objekt förblir aktiva i systemet som följer säkerhets kopierings-och bevarande principen som angetts av användaren. Säkerhetskopierade data för de här virtuella Azure-datorerna kommer att behållas enligt bevarande principen. De utgångna återställnings punkterna (förutom den senaste återställnings punkten) rensas enligt det kvarhållningsintervall som anges i säkerhets kopierings principen.
* För att undvika ytterligare kostnader rekommenderar vi att du tar bort de säkerhets kopierings objekt där den primära data källan inte längre finns. Detta är i ett scenario där säkerhets kopierings objekt/data för de borttagna resurserna inte längre behövs, eftersom den senaste återställnings punkten behålls kontinuerligt och du debiteras enligt gällande pris för säkerhets kopiering.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [säkerhetskopierar virtuella Azure-datorer från inställningarna för den virtuella datorn](backup-azure-vms-first-look-arm.md).
* Lär dig hur du [återställer virtuella datorer](backup-azure-arm-restore-vms.md).
* Lär dig hur du [övervakar säkerhets kopior av virtuella Azure-datorer](./backup-azure-monitoring-built-in-monitor.md).
