---
title: Säkerhetskopiera Azure-filresurser i Azure Portal
description: Lär dig hur du använder Azure Portal för att säkerhetskopiera Azure-filresurser i Recovery Services-valvet
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: ca49f1ad48ab0534b27b91ad6a5a50b393cda782
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88890356"
---
# <a name="back-up-azure-file-shares"></a>Säkerhetskopiera Azure-filresurser

Den här artikeln beskriver hur du säkerhetskopierar [Azure-filresurser](../storage/files/storage-files-introduction.md) från Azure Portal.

I den här artikeln får du lära dig att:

* Skapa ett Recovery Services-valv.
* Konfigurera säkerhets kopiering från Recovery Services-valvet
* Konfigurera säkerhets kopiering från fönstret fil resurs
* Köra en säkerhetskopiering på begäran för att skapa en återställningspunkt

## <a name="prerequisites"></a>Förutsättningar

* [Lär dig mer](azure-file-share-backup-overview.md) om den ögonblicks bilds lösning för Azure-filresursen.
* Se till att fil resursen finns i någon av de [typer av lagrings konton som stöds](azure-file-share-support-matrix.md).
* Identifiera eller skapa ett [Recovery Services valv](#create-a-recovery-services-vault) i samma region som det lagrings konto som är värd för fil resursen.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="configure-backup-from-the-recovery-services-vault"></a>Konfigurera säkerhets kopiering från Recovery Services-valvet

Följande steg beskriver hur du kan konfigurera säkerhets kopiering för flera fil resurser från fönstret Recovery Services valv:

1. I [Azure Portal](https://portal.azure.com/)öppnar du Recovery Services-valvet som du vill använda för att konfigurera säkerhets kopiering för fil resursen.

1. I fönstret **Recovery Services valv** väljer du **+ säkerhetskopiera** på menyn högst upp.

   ![Recovery Services-valv](./media/backup-afs/recovery-services-vault.png)

    1. I fönstret **säkerhets kopierings mål** anger **du var arbets belastningen körs?** till **Azure** genom att välja alternativet **Azure** i list rutan.

          ![Välj Azure som arbets belastning](./media/backup-afs/backup-goal.png)

    2. I **vad vill du säkerhetskopiera? väljer du** **Azure-filresurs** i list rutan.

          ![Välj Azure-FileShare](./media/backup-afs/select-azure-file-share.png)

    3. Välj **säkerhets kopiering** för att registrera tillägget Azure File Share i valvet.

          ![Välj säkerhets kopiering för att associera Azure-filresursen med valvet](./media/backup-afs/register-extension.png)

1. När du har valt **säkerhets kopiering** öppnas fönstret **säkerhets kopiering** . Välj det lagrings konto som är värd för den fil resurs som du vill skydda genom att välja länken **Välj** länk under text rutan **lagrings konto** .

   ![Välj länken Välj](./media/backup-afs/choose-select-link.png)

1. **Rutan Välj lagrings konto** öppnas till höger och listar en uppsättning identifierade lagrings konton som stöds. De är antingen associerade med det här valvet eller finns i samma region som valvet, men har ännu inte kopplats till något Recovery Services-valv.

1. I listan över identifierade lagrings konton väljer du ett konto och väljer **OK**.

   ![Välj bland de identifierade lagrings kontona](./media/backup-afs/select-discovered-storage-account.png)

1. Nästa steg är att välja de fil resurser som du vill säkerhetskopiera. Välj knappen **Lägg till** i avsnittet **fil resurser to backup** .

   ![Välj de fil resurser som ska säkerhets kopie ras](./media/backup-afs/select-file-shares-to-back-up.png)

1. Fönstret **Välj fildelnings** kontext öppnas till höger. Azure söker i lagrings kontot efter fil resurser som kan säkerhets kopie ras. Om du nyligen har lagt till dina fil resurser och inte ser dem i listan, kan det vara en tid för fil resurserna att visas.

1. I listan **Välj fil resurser** väljer du en eller flera av de fil resurser som du vill säkerhetskopiera. Välj **OK**.

   ![Välj fil resurser](./media/backup-afs/select-file-shares.png)

1. Du kan välja en säkerhets kopierings princip för fil resursen med tre alternativ:

   * Välj standard principen.<br>
   Med det här alternativet kan du aktivera daglig säkerhets kopiering som ska behållas i 30 dagar. Om du inte har en befintlig säkerhets kopierings princip i valvet öppnas säkerhets kopierings fönstret med standard princip inställningarna. Om du vill välja standardinställningar kan du välja **Aktivera säkerhets kopiering** direkt.

   * Skapa en ny policy <br>

      1. Om du vill skapa en ny säkerhets kopierings princip för fil resursen väljer du länk texten under List rutan i avsnittet **säkerhets kopierings princip** .<br>

         ![Skapa ny princip](./media/backup-afs/create-new-policy.png)

      1. Kontext fönstret för **säkerhets kopierings principen** öppnas till höger. Ange ett princip namn i text rutan och välj kvarhållningsperioden enligt ditt krav. Endast alternativet för daglig kvarhållning är aktiverat som standard. Om du vill ha veckovis, månatlig eller årlig kvarhållning markerar du motsvarande kryss ruta och anger önskat kvarhållningsintervall.

      1. När du har angett värdena för kvarhållning och ett giltigt princip namn väljer du **OK**.<br>

         ![Ange princip namn och bevarande värden](./media/backup-afs/policy-name.png)

   * Välj en av de befintliga säkerhets kopierings principerna <br>

      Om du vill välja en av de befintliga säkerhets kopierings principerna för att konfigurera skydd väljer du önskad princip i list rutan **säkerhets kopierings princip** .<br>

      ![Välj en befintlig princip](./media/backup-afs/choose-existing-policy.png)

1. Välj **Aktivera säkerhets kopiering** för att börja skydda fil resursen.

   ![Välj Aktivera säkerhets kopiering](./media/backup-afs/enable-backup.png)

När du har angett en säkerhets kopierings princip tas en ögonblicks bild av fil resurserna vid den schemalagda tiden. Återställnings punkten behålls även för den valda perioden.

>[!NOTE]
>Azure Backup stöder nu principer med dagliga/veckovis/månads Visa/årliga kvarhållning för säkerhets kopiering av Azure-filresurser.

## <a name="configure-backup-from-the-file-share-pane"></a>Konfigurera säkerhets kopiering från fönstret fil resurs

I följande steg förklaras hur du kan konfigurera säkerhets kopiering för enskilda fil resurser från respektive fil resurs fönster:

1. I [Azure Portal](https://portal.azure.com/)öppnar du det lagrings konto som är värd för den fil resurs som du vill säkerhetskopiera.

1. När du är i lagrings kontot väljer du panelen med namnet **fil resurser**. Du kan också navigera till **fil resurser** via innehålls förteckningen för lagrings kontot.

   ![Lagringskonto](./media/backup-afs/storage-account.png)

1. I listan fil resurs bör du se alla fil resurser som finns i lagrings kontot. Välj den fil resurs som du vill säkerhetskopiera.

   ![Fil resurs lista](./media/backup-afs/file-shares-list.png)

1. Välj **säkerhets kopiering** under avsnittet **åtgärder** i rutan fil resurs. Fönstret **Konfigurera säkerhets kopiering** kommer att läsas in till höger.

   ![Konfigurera säkerhets kopierings fönstret](./media/backup-afs/configure-backup.png)

1. Gör något av följande för val av Recovery Services valv:

    * Om du redan har ett valv väljer du alternativ knappen **Välj befintlig** Recovery Services valv och väljer något av de befintliga valvena från den nedrullningsbara menyn **valv namn** .

       ![Välj befintligt valv](./media/backup-afs/select-existing-vault.png)

    * Om du inte har något valv väljer du alternativ knappen **Skapa nytt** Recovery Services valv. Ange ett namn för valvet. Den skapas i samma region som fil resursen. Valvet skapas som standard i samma resurs grupp som fil resursen. Om du vill välja en annan resurs grupp väljer du **Skapa ny** länk under List rutan **resurs typ** och anger ett namn för resurs gruppen. Klicka på **OK** för att fortsätta.

       ![Skapa nytt valv](./media/backup-afs/create-new-vault.png)

      >[!IMPORTANT]
      >Om lagrings kontot har registrerats med ett valv, eller om det finns några skyddade resurser inom lagrings kontot som är värd för den fil resurs som du försöker skydda, kommer namnet på Recovery Services valvet att fyllas i och du får inte redigera det [mer här](backup-azure-files-faq.md#why-cant-i-change-the-vault-to-configure-backup-for-the-file-share).

1. Gör något av följande för val av **säkerhets kopierings princip** :

    * Lämna standard principen. Den kommer att schemalägga dagliga säkerhets kopieringar med en kvarhållning på 30 dagar.

    * Välj en befintlig säkerhets kopierings policy, om du har en, på den nedrullningsbara menyn **säkerhets kopierings policy** .

       ![Välj säkerhets kopierings princip](./media/backup-afs/choose-backup-policy.png)

    * Skapa en ny princip med dagliga/veckovis/månads Visa/årliga kvarhållning enligt ditt krav.  

         1. Välj länk texten **skapa en ny princip** .

         2. Kontext fönstret för **säkerhets kopierings principen** öppnas till höger. Ange ett princip namn i text rutan och välj kvarhållningsperioden enligt ditt krav. Endast alternativet för daglig kvarhållning är aktiverat som standard. Om du vill ha veckovis, månatlig eller årlig kvarhållning markerar du motsvarande kryss ruta och anger önskat kvarhållningsintervall.

         3. När du har angett värdena för kvarhållning och ett giltigt princip namn väljer du **OK**.

            ![Skapa ny säkerhets kopierings princip](./media/backup-afs/create-new-backup-policy.png)

1. Välj **Aktivera säkerhets kopiering** för att börja skydda fil resursen.

   ![Välj Aktivera säkerhets kopiering](./media/backup-afs/select-enable-backup.png)

1. Du kan följa konfigurations förloppet i Portal meddelanden eller genom att övervaka säkerhets kopierings jobben under valvet som du använder för att skydda fil resursen.

   ![Portal meddelanden](./media/backup-afs/portal-notifications.png)

1. När du har slutfört åtgärden för att konfigurera säkerhets kopiering väljer du **säkerhets kopiering** under avsnittet **åtgärder** i rutan fil resurs. Sammanhangs fönstret visar att **valv Essentials** är till höger. Därifrån kan du utlösa säkerhets kopierings-och återställnings åtgärder på begäran.

   ![Valv Essentials](./media/backup-afs/vault-essentials.png)

## <a name="run-an-on-demand-backup-job"></a>Köra ett säkerhets kopierings jobb på begäran

Ibland kanske du vill skapa en ögonblicks bild av en säkerhets kopia eller återställnings punkt utanför de tider som schemalagts i säkerhets kopierings principen. En vanlig orsak till att generera en säkerhets kopiering på begäran är direkt efter att du har konfigurerat säkerhets kopierings principen. Baserat på schemat i säkerhets kopierings policyn kan det vara timmar eller dagar tills en ögonblicks bild tas. För att skydda dina data fram tills säkerhetskopieringspolicyn aktiveras, bör du starta en säkerhetskopiering på begäran. Det krävs ofta att du skapar en säkerhets kopiering på begäran innan du gör några planerade ändringar i fil resurserna.

### <a name="from-the-recovery-services-vault"></a>Från Recovery Services-valvet

1. Öppna det Recovery Services valv som du använde för att säkerhetskopiera fil resursen. I **översikts** fönstret väljer du **säkerhets kopierings objekt** under avsnittet **skyddade objekt** .

   ![Välj säkerhets kopierings objekt](./media/backup-afs/backup-items.png)

1. När du har valt **säkerhets kopierings objekt** visas ett nytt fönster med en lista med alla **typer av säkerhets kopierings hantering** bredvid **översikts** fönstret.

   ![Lista över säkerhets kopierings hanterings typer](./media/backup-afs/backup-management-types.png)

1. Välj **Azure Storage (Azure Files)** i listan **typ av säkerhets kopierings hantering** . Du ser en lista över alla fil resurser och motsvarande lagrings konton som har säkerhetskopierats med hjälp av det här valvet.

   ![Azure Storage (Azure Files) säkerhetskopiera objekt](./media/backup-afs/azure-files-backup-items.png)

1. I listan över Azure-filresurser väljer du den fil resurs som du vill använda. Information om **säkerhets kopierings objekt** visas. På menyn **säkerhets kopierings objekt** väljer du **Säkerhetskopiera nu**. Eftersom det här säkerhets kopierings jobbet är på begäran finns det ingen bevarande princip som är associerad med återställnings punkten.

   ![Välj Säkerhetskopiera nu](./media/backup-afs/backup-now.png)

1. Fönstret **Säkerhetskopiera nu** öppnas. Ange den senaste dag som du vill behålla återställningspunkten för. Du kan ha en maximal kvarhållning på 10 år för en säkerhets kopiering på begäran.

   ![Välj kvarhållningsdatum](./media/backup-afs/retention-date.png)

1. Välj **OK** för att bekräfta säkerhets kopierings jobbet på begäran som körs.

1. Övervaka Portal meddelanden för att hålla reda på hur du slutför körningen av säkerhets kopierings jobb. Du kan övervaka jobb förloppet i valv-instrumentpanelen. Välj **pågående säkerhets kopierings jobb**  >  .

### <a name="from-the-file-share-pane"></a>Från rutan fil resurs

1. Öppna fil resursens **översikts** fönster där du vill göra en säkerhets kopiering på begäran.

1. Välj **säkerhets kopiering** under avsnittet **åtgärd** . Sammanhangs fönstret visar att **valv Essentials** är till höger. Välj **säkerhets kopiering nu** för att utföra en säkerhets kopiering på begäran.

   ![Välj Säkerhetskopiera nu](./media/backup-afs/select-backup-now.png)

1. Fönstret **Säkerhetskopiera nu** öppnas. Ange kvarhållning för återställnings punkten. Du kan ha en maximal kvarhållning på 10 år för en säkerhets kopiering på begäran.

   ![Behåll säkerhets kopierings datum](./media/backup-afs/retain-backup-date.png)

1. Välj **Ja** för att bekräfta.

>[!NOTE]
>Azure Backup låser lagrings kontot när du konfigurerar skydd för alla fil resurser i motsvarande konto. Detta ger skydd mot oavsiktlig borttagning av ett lagrings konto med säkerhetskopierade fil resurser.

## <a name="best-practices"></a>Bästa praxis

* Ta inte bort ögonblicks bilder som skapats av Azure Backup. Om du tar bort ögonblicksbilder kan du förlora återställningspunkter och/eller drabbas av återställningsfel.

* Ta inte bort låset som gjorts på lagrings kontot genom att Azure Backup. Om du tar bort låset är ditt lagrings konto känsligt för oavsiktlig borttagning och om det tas bort förlorar du ögonblicks bilderna eller säkerhets kopiorna.

## <a name="next-steps"></a>Nästa steg

Lär dig att:

* [Återställa Azure-filresurser](restore-afs.md)
* [Hantera säkerhets kopior av Azure-filresurser](manage-afs-backup.md)
