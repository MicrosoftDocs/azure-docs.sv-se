---
title: Back up Azure file shares in the Azure Portal
description: Lär dig hur du använder Azure Portal för att a upp Azure-filresurser i Recovery Services-valvet
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: e7f44a71388468be432bdfcb0eb2bf67c0fcc8ef
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519943"
---
# <a name="back-up-azure-file-shares"></a>Säkerhetskopiera Azure-filresurser

I den här artikeln förklaras hur du [kommer tillbaka till Azure-filresurser](../storage/files/storage-files-introduction.md) från Azure Portal.

I den här artikeln får du lära dig att:

* Skapa ett Recovery Services-valv.
* Konfigurera säkerhetskopiering från Recovery Services-valvet
* Konfigurera säkerhetskopiering från filresursfönstret
* Köra en säkerhetskopiering på begäran för att skapa en återställningspunkt

## <a name="prerequisites"></a>Förutsättningar

* [Lär dig](azure-file-share-backup-overview.md) mer om azure-filresursens lösning för ögonblicksbildsbaserad säkerhetskopiering.
* Kontrollera att filresursen finns i någon av de [lagringskontotyper som stöds.](azure-file-share-support-matrix.md)
* Identifiera eller skapa ett [Recovery Services-valv](#create-a-recovery-services-vault) i samma region som lagringskontot som är värd för filresursen.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="configure-backup-from-the-recovery-services-vault"></a>Konfigurera säkerhetskopiering från Recovery Services-valvet

Följande steg beskriver hur du kan konfigurera säkerhetskopiering för flera filresurser från fönstret Recovery Services-valv:

1. I Azure Portal [du](https://portal.azure.com/)det Recovery Services-valv som du vill använda för att konfigurera säkerhetskopiering för filresursen.

1. I fönstret **Recovery Services-valv** väljer du **+Backup** på menyn längst upp.

   ![Recovery Services-valv](./media/backup-afs/recovery-services-vault.png)

    1. I fönstret **Säkerhetskopieringsmål** anger **du Var körs din arbetsbelastning?** till **Azure** genom att välja **alternativet Azure** i listrutan.

          ![Välj Azure som arbetsbelastning](./media/backup-afs/backup-goal.png)

    2. I **Vad vill du backa upp?** väljer du **Azure-filresurs** i listrutan.

          ![Välj Azure FileShare](./media/backup-afs/select-azure-file-share.png)

    3. Välj **Säkerhetskopiering** för att registrera tillägget för Azure-filresursen i valvet.

          ![Välj Säkerhetskopiering för att associera Azure-filresursen med valvet](./media/backup-afs/register-extension.png)

1. När du har **valt Säkerhetskopiera** **öppnas fönstret** Säkerhetskopiering. Välj det lagringskonto som är värd för den  filresurs som du vill skydda genom att välja länktexten Välj under **textrutan Lagringskonto.**

   ![Välj länken Välj](./media/backup-afs/choose-select-link.png)

1. Fönstret **Välj lagringskonto** öppnas till höger och visar en uppsättning identifierade lagringskonton som stöds. De är antingen associerade med det här valvet eller finns i samma region som valvet, men ännu inte associerade till något Recovery Services-valv.

1. I listan över identifierade lagringskonton väljer du ett konto och sedan **OK.**

   ![Välj bland de identifierade lagringskontona](./media/backup-afs/select-discovered-storage-account.png)

1. Nästa steg är att välja de filresurser som du vill backa upp. Välj knappen **Lägg** till i avsnittet **FileShares to Backup (Filresurs till säkerhetskopia).**

   ![Välj de filresurser som ska backa upp](./media/backup-afs/select-file-shares-to-back-up.png)

1. Kontextfönstret **Välj** filresurser öppnas till höger. Azure söker i lagringskontot efter filresurser som kan säkerhetskopieras. Om du nyligen har lagt till dina filresurser och inte ser dem i listan kan du tillåta att filresurser visas en stund.

1. I listan **Välj filresurser** väljer du en eller flera av de filresurser som du vill backa upp. Välj **OK**.

   ![Välj filresurser](./media/backup-afs/select-file-shares.png)

1. Om du vill välja en säkerhetskopieringsprincip för filresursen har du tre alternativ:

   * Välj standardprincipen.<br>
   Med det här alternativet kan du aktivera daglig säkerhetskopiering som ska bevaras i 30 dagar. Om du inte har en befintlig säkerhetskopieringspolicy i valvet öppnas fönstret Säkerhetskopiering med standardprincipinställningarna. Om du vill välja standardinställningarna kan du välja Aktivera **säkerhetskopiering direkt.**

   * Skapa en ny policy <br>

      1. Om du vill skapa en ny säkerhetskopieringspolicy för filresursen väljer du länktexten under listrutan i avsnittet **Säkerhetskopieringspolicy.**<br>

         ![Skapa ny princip](./media/backup-afs/create-new-policy.png)

      1. Kontextfönstret **Säkerhetskopieringspolicy** öppnas till höger. Ange ett principnamn i textrutan och välj kvarhållningsperioden enligt dina behov. Endast alternativet för daglig kvarhållning är aktiverat som standard. Om du vill ha kvarhållning varje vecka, månad eller år markerar du motsvarande kryssruta och anger önskat kvarhållningsvärde.

      1. När du har specificerat kvarhållningsvärdena och ett giltigt principnamn väljer du **OK.**<br>

         ![Ange principnamn och kvarhållningsvärden](./media/backup-afs/policy-name.png)

   * Välj en av de befintliga säkerhetskopieringsprinciperna <br>

      Om du vill välja en av de befintliga säkerhetskopieringsprinciperna för att konfigurera skydd väljer du önskad princip i **listrutan** Säkerhetskopieringspolicy.<br>

      ![Välj befintlig princip](./media/backup-afs/choose-existing-policy.png)

1. Välj **Aktivera säkerhetskopiering** för att börja skydda filresursen.

   ![Välj Aktivera säkerhetskopiering](./media/backup-afs/enable-backup.png)

När du har angett en säkerhetskopieringspolicy tas en ögonblicksbild av filresurser vid den schemalagda tidpunkten. Återställningspunkten behålls också under den valda perioden.

>[!NOTE]
>Azure Backup har nu stöd för principer med kvarhållning per dag/vecka/månad/år för säkerhetskopiering av Azure-filresurs.

## <a name="configure-backup-from-the-file-share-pane"></a>Konfigurera säkerhetskopiering från filresursfönstret

Följande steg beskriver hur du kan konfigurera säkerhetskopiering för enskilda filresurser från respektive filresursfönster:

1. I [Azure Portal](https://portal.azure.com/)du det lagringskonto som är värd för den filresurs som du vill a tillbaka.

1. När du är i lagringskontot väljer du panelen med etiketten **Filresurser**. Du kan också navigera **till Filresurser** via innehållsförteckningen för lagringskontot.

   ![Lagringskonto](./media/backup-afs/storage-account.png)

1. I listan över filresurser bör du se alla filresurser som finns på lagringskontot. Välj den filresurs som du vill backa upp.

   ![Lista över filresurser](./media/backup-afs/file-shares-list.png)

1. Välj **Säkerhetskopiering** under **avsnittet** Åtgärder i filresursfönstret. Fönstret **Konfigurera säkerhetskopiering** läses in till höger.

   ![Fönstret Konfigurera säkerhetskopiering](./media/backup-afs/configure-backup.png)

1. För val av Recovery Services-valv gör du något av följande:

    * Om du redan har ett valv väljer du alternativknappen **Välj** befintligt Recovery  Services-valv och väljer ett av de befintliga valvena i listrutan Valvnamn.

       ![Välj befintligt valv](./media/backup-afs/select-existing-vault.png)

    * Om du inte har något valv väljer du alternativknappen **Skapa nytt** Recovery Services-valv. Ange ett namn för valvet. Den skapas i samma region som filresursen. Som standard skapas valvet i samma resursgrupp som filresursen. Om du vill välja en annan resursgrupp väljer du **Skapa** ny länk under **listrutan Resurstyp** och anger ett namn för resursgruppen. Klicka på **OK** för att fortsätta.

       ![Skapa nytt valv](./media/backup-afs/create-new-vault.png)

      >[!IMPORTANT]
      >Om lagringskontot är registrerat med ett valv, eller om det finns några skyddade resurser i lagringskontot som är värd för filresursen som du försöker skydda, fylls Recovery Services-valvnamnet i förväg och du kan inte redigera det Läs mer [här.](backup-azure-files-faq.yml#why-can-t-i-change-the-vault-to-configure-backup-for-the-file-share-)

1. För att **välja säkerhetskopieringspolicy** gör du något av följande:

    * Lämna standardprincipen. Den schemalägger dagliga säkerhetskopieringar med en kvarhållning på 30 dagar.

    * Välj en befintlig säkerhetskopieringspolicy, om  du har en, från listrutan Säkerhetskopieringspolicy.

       ![Välj säkerhetskopieringspolicy](./media/backup-afs/choose-backup-policy.png)

    * Skapa en ny princip med kvarhållning per dag/vecka/månad/år enligt dina behov.  

         1. Välj **länktexten Skapa en** ny princip.

         2. Kontextfönstret **Säkerhetskopieringspolicy** öppnas till höger. Ange ett principnamn i textrutan och välj kvarhållningsperioden enligt dina behov. Endast alternativet för daglig kvarhållning är aktiverat som standard. Om du vill ha kvarhållning varje vecka, månad eller år markerar du motsvarande kryssruta och anger önskat kvarhållningsvärde.

         3. När du har specificerat kvarhållningsvärdena och ett giltigt principnamn väljer du **OK.**

            ![Skapa en ny säkerhetskopieringsprincip](./media/backup-afs/create-new-backup-policy.png)

1. Välj **Aktivera säkerhetskopiering** för att börja skydda filresursen.

   ![Välj Aktivera säkerhetskopiering](./media/backup-afs/select-enable-backup.png)

1. Du kan spåra konfigurationsförloppet i portalmeddelandena eller genom att övervaka säkerhetskopieringsjobben under valvet som du använder för att skydda filresursen.

   ![Portalmeddelanden](./media/backup-afs/portal-notifications.png)

1. När säkerhetskopieringen har slutförts väljer du **Säkerhetskopiering** under **avsnittet Åtgärder** i fönstret filresurs. Kontextfönstret med **Vault Essentials läses** in till höger. Därifrån kan du utlösa säkerhetskopiering och återställning på begäran.

   ![Valv essentials](./media/backup-afs/vault-essentials.png)

## <a name="run-an-on-demand-backup-job"></a>Köra ett säkerhetskopieringsjobb på begäran

Ibland kanske du vill generera en ögonblicksbild av säkerhetskopian, eller återställningspunkten, utanför de tider som schemalagts i säkerhetskopieringsprincipen. En vanlig orsak till att skapa en säkerhetskopiering på begäran är direkt efter att du har konfigurerat säkerhetskopieringspolicyn. Baserat på schemat i säkerhetskopieringsprincipen kan det ta flera timmar eller dagar innan en ögonblicksbild tas. För att skydda dina data fram tills säkerhetskopieringspolicyn aktiveras, bör du starta en säkerhetskopiering på begäran. Det krävs ofta att du skapar en säkerhetskopiering på begäran innan du gör planerade ändringar i filresurser.

### <a name="from-the-recovery-services-vault"></a>Från Recovery Services-valvet

1. Öppna Recovery Services-valvet som du använde för att valva upp filresursen. I fönstret **Översikt** väljer du **Säkerhetskopieringsobjekt** under **avsnittet Skyddade** objekt.

   ![Välj Säkerhetskopieringsobjekt](./media/backup-afs/backup-items.png)

1. När du har **valt Säkerhetskopieringsobjekt** visas ett nytt fönster **med** alla typer av säkerhetskopieringshantering bredvid **fönstret** Översikt.

   ![Lista över typer av säkerhetskopieringshantering](./media/backup-afs/backup-management-types.png)

1. I listan **Typ av säkerhetskopieringshantering** väljer **du Azure Storage (Azure Files)**. Du ser en lista över alla filresurser och motsvarande lagringskonton som säkerhetskopieras med hjälp av det här valvet.

   ![Azure Storage (Azure Files) säkerhetskopieringsobjekt](./media/backup-afs/azure-files-backup-items.png)

1. I listan över Azure-filresurser väljer du den filresurs som du vill använda. Information **om säkerhetskopieringsobjekt** visas. På menyn **Säkerhetskopieringsobjekt** väljer du **Säkerhetskopiera nu.** Eftersom det här säkerhetskopieringsjobbet är på begäran finns det ingen bevarandeprincip associerad med återställningspunkten.

   ![Välj Säkerhetskopiera nu](./media/backup-afs/backup-now.png)

1. Fönstret **Säkerhetskopiera** nu öppnas. Ange den senaste dag som du vill behålla återställningspunkten för. Du kan ha en maximal kvarhållning på 10 år för säkerhetskopiering på begäran.

   ![Välj kvarhållningsdatum](./media/backup-afs/retention-date.png)

1. Välj **OK** för att bekräfta säkerhetskopieringsjobbet på begäran som körs.

1. Övervaka portalmeddelanden för att hålla reda på körningen av säkerhetskopieringsjobb. Du kan övervaka jobbförloppet på valvets instrumentpanel. Välj **Säkerhetskopieringsjobb**  >  **pågår.**

### <a name="from-the-file-share-pane"></a>Från filresursfönstret

1. Öppna fönstret Översikt för **filresursen** där du vill göra en säkerhetskopiering på begäran.

1. Välj **Säkerhetskopiering** under **avsnittet** Åtgärd. Kontextfönstret med **Vault Essentials läses** in till höger. Välj **Säkerhetskopiera nu** för att göra en säkerhetskopiering på begäran.

   ![Välj Säkerhetskopiera nu](./media/backup-afs/select-backup-now.png)

1. Fönstret **Säkerhetskopiera** nu öppnas. Ange kvarhållningen för återställningspunkten. Du kan ha en maximal kvarhållning på 10 år för säkerhetskopiering på begäran.

   ![Kvarhåll säkerhetskopieringsdatum](./media/backup-afs/retain-backup-date.png)

1. Välj **Ja** för att bekräfta.

>[!NOTE]
>Azure Backup låser lagringskontot när du konfigurerar skydd för alla filresurs i motsvarande konto. Detta ger skydd mot oavsiktlig borttagning av ett lagringskonto med säkerhetskopierade filresurser.

## <a name="best-practices"></a>Bästa praxis

* Ta inte bort ögonblicksbilder som skapats av Azure Backup. Om du tar bort ögonblicksbilder kan du förlora återställningspunkter och/eller drabbas av återställningsfel.

* Ta inte bort låset för lagringskontot genom att Azure Backup. Om du tar bort låset är lagringskontot känsligt för oavsiktlig borttagning, och om det tas bort förlorar du dina ögonblicksbilder eller säkerhetskopior.

## <a name="next-steps"></a>Nästa steg

Lär dig att:

* [Återställa Azure-filresurser](restore-afs.md)
* [Hantera säkerhetskopior av Azure-filresurs](manage-afs-backup.md)
