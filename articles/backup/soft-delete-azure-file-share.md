---
title: Oavsiktligt borttagnings skydd för Azure-filresurser
description: Lär dig hur du kan använda mjuk borttagning för att skydda dina Azure-filresurser från oavsiktlig borttagning.
ms.topic: conceptual
ms.date: 02/02/2020
ms.custom: references_regions
ms.openlocfilehash: 52a116320e07c25d4ee7f00b8063ca15faeb8560
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89179920"
---
# <a name="accidental-delete-protection-for-azure-file-shares-using-azure-backup"></a>Oavsiktligt borttagnings skydd för Azure-filresurser med hjälp av Azure Backup

För att skydda mot cyberattacker eller oavsiktlig borttagning aktive ras [mjuk borttagning](../storage/files/storage-files-prevent-file-share-deletion.md) för alla fil resurser i ett lagrings konto när du konfigurerar säkerhets kopiering för alla fil resurser i respektive lagrings konto. Med mjuk borttagning, även om en skadlig aktör tar bort fil resursen, behålls fil resursens innehåll och återställnings punkter (ögonblicks bilder) i minst 14 ytterligare dagar, vilket gör det möjligt att återställa fil resurser utan data förlust.  Mjuk borttagning stöds för standard-och Premium lagrings konton och inställningen aktive ras av Azure Backup för alla lagrings konton som är värdar för säkerhets kopiering av fil resurser.

I följande flödes diagram visas de olika stegen och tillstånden för ett säkerhets kopierings objekt när mjuk borttagning har Aktiver ATS för fil resurser i ett lagrings konto:

 ![Flödes schema för mjuk borttagning](./media/soft-delete-afs/soft-delete-flow-chart.png)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="when-will-soft-delete-be-enabled-for-file-shares-in-my-storage-account"></a>När kommer mjuk borttagning att aktive ras för fil resurser i mitt lagrings konto?

När du konfigurerar säkerhets kopiering för första gången för en fil resurs i ett lagrings konto aktiverar Azure Backup tjänsten mjuk borttagning för alla fil resurser i respektive lagrings konto.

### <a name="can-i-configure-the-number-of-days-for-which-my-snapshots-and-restore-points-will-be-retained-in-soft-deleted-state-after-i-delete-the-file-share"></a>Kan jag konfigurera antalet dagar för vilka mina ögonblicks bilder och återställnings punkter ska behållas i läget "tyst borttaget" när jag har tagit bort fil resursen?

Ja, du kan ställa in kvarhållningsperioden enligt dina krav. [Det här dokumentet](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal) förklarar hur du konfigurerar kvarhållningsperioden. För lagrings konton med säkerhetskopierade fil resurser bör inställningen för minsta kvarhållning vara 14 dagar.

### <a name="does-azure-backup-reset-my-retention-setting-because-i-configured-it-to-less-than-14-days"></a>Har Azure Backup Återställ inställningen för kvarhållning eftersom jag har konfigurerat den till mindre än 14 dagar?

Från ett säkerhets perspektiv rekommenderar vi att du har en minsta kvarhållning på 14 dagar för lagrings konton med säkerhetskopierade fil resurser. Så på varje säkerhets kopierings jobb körs, om Azure Backup identifierar inställningen till mindre än 14 dagar, återställs den till 14 dagar.

### <a name="what-is-the-cost-incurred-during-the-retention-period"></a>Vad kostar det att betala under kvarhållningsperioden?

Under den avbrotts bara borttagnings perioden förblir den skyddade instansen kostnad och lagrings kostnad för ögonblicks bilder.  Dessutom debiteras du för den använda kapaciteten med jämna mellanrum för standard fil resurser och lagrings takt för ögonblicks bilder för Premium-filresurser.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-deleted-state"></a>Kan jag utföra en återställnings åtgärd när mina data är i tyst borttaget läge?

Du måste först ta bort den mjuk borttagna fil resursen för att utföra återställnings åtgärder. Åtgärden ta bort tar fil resursen i det säkerhetskopierade tillstånd där du kan återställa till en viss tidpunkt. Om du vill veta hur du tar bort en fil resurs kan du gå till [den här länken](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) eller Visa [skriptet ta bort fil resurs](./scripts/backup-powershell-script-undelete-file-share.md).

### <a name="how-can-i-purge-the-data-of-a-file-share-in-a-storage-account-that-has-at-least-one-protected-file-share"></a>Hur kan jag rensa data i en fil resurs i ett lagrings konto som har minst en skyddad fil resurs?

Om du har minst en skyddad fil resurs i ett lagrings konto innebär det att mjuk borttagning är aktiverat för alla fil resurser i det kontot och att dina data bevaras i 14 dagar efter borttagnings åtgärden. Men om du vill rensa data omedelbart och inte vill att de ska behållas följer du dessa steg:

1. Om du redan har tagit bort fil resursen samtidigt som du har aktiverat mjuk borttagning, tar du först bort fil resursen från fil [portalen](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) eller genom att använda [skriptet ta bort fil resurs](./scripts/backup-powershell-script-undelete-file-share.md).
2. Inaktivera mjuk borttagning för fil resurser i ditt lagrings konto genom att följa de steg som beskrivs i [det här dokumentet](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#disable-soft-delete).
3. Ta nu bort fil resursen vars innehåll du vill rensa omedelbart.

>[!NOTE]
>Du bör utföra steg 2 innan nästa schemalagda säkerhets kopierings jobb körs mot den skyddade fil resursen i ditt lagrings konto. Eftersom när säkerhets kopierings jobbet körs återaktiverar den mjuk borttagning för alla fil resurser i lagrings kontot.

>[!WARNING]
>När du har inaktiverat mjuk borttagning i steg 2, är alla borttagnings åtgärder som utförts mot fil resurserna en permanent borttagnings åtgärd. Om du av misstag tar bort den säkerhetskopierade fil resursen när du har inaktiverat mjuk borttagning förlorar du alla ögonblicks bilder och kan inte återställa dina data.

### <a name="in-the-context-of-a-file-shares-soft-delete-setting-what-changes-does-azure-backup-do-when-i-unregister-a-storage-account"></a>Vilka ändringar Azure Backup när jag avregistrerar ett lagrings konto i kontexten för en fil resurss inställning för mjuk borttagning?

Vid avregistreringen kontrollerar Azure Backup kvarhållningsperioden för fil resurser och om den är större än 14 dagar eller mindre än 14 dagar, så lämnar den kvarhållning som den är. Men om kvarhållning är 14 dagar, anser vi att det har Aktiver ATS av Azure Backup och inaktiverar sedan den mjuka borttagningen under Avregistrerings processen. Om du vill avregistrera lagrings kontot samtidigt som du behåller inställningen kvarhållning aktiverar du det igen från fönstret lagrings konto när du har slutfört avregistreringen. Du kan referera till [den här länken](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) för konfigurations stegen.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [säkerhetskopierar Azure-filresurser från Azure Portal](backup-afs.md)
