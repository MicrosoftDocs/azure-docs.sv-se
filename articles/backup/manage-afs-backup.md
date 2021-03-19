---
title: Hantera säkerhetskopior av Azure-filresurser
description: I den här artikeln beskrivs vanliga uppgifter för att hantera och övervaka Azure-filresurser som säkerhets kopie ras av Azure Backup.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 784b22f11b57fb025f6d9401e10d527c83751898
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88757633"
---
# <a name="manage-azure-file-share-backups"></a>Hantera säkerhetskopior av Azure-filresurser

I den här artikeln beskrivs vanliga uppgifter för att hantera och övervaka Azure-filresurser som säkerhets kopie ras av [Azure Backup](./backup-overview.md). Du lär dig hur du utför hanterings uppgifter i Recovery Services valvet.

## <a name="monitor-jobs"></a>Övervaka jobb

När du utlöser en säkerhets kopierings-eller återställnings åtgärd skapar säkerhets kopierings tjänsten ett jobb för spårning. Du kan övervaka förloppet för alla jobb på sidan **Säkerhetskopieringsjobb**.

Öppna sidan **Säkerhetskopieringsjobb**:

1. Öppna det Recovery Services-valv som du använde för att konfigurera säkerhets kopiering för dina fil resurser. I **översikts** fönstret väljer du **säkerhets kopierings jobb** under avsnittet **övervakning** .

   ![Säkerhets kopierings jobb i avsnittet övervakning](./media/manage-afs-backup/backup-jobs.png)

1. När du har valt **OK** visar fönstret **säkerhets kopierings jobb** status för alla jobb. Välj det arbets belastnings namn som motsvarar den fil resurs som du vill övervaka.

   ![Arbets belastnings namn](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Skapa en ny policy

Du kan skapa en ny princip för att säkerhetskopiera Azure-filresurser från avsnittet **säkerhets kopierings principer** i Recovery Services valvet. Alla principer som skapas när du konfigurerade säkerhets kopiering för fil resurser visas med **princip typen** **Azure-filresurs**.

Så här visar du befintliga säkerhets kopierings principer:

1. Öppna det Recovery Services-valv som du använde för att konfigurera säkerhets kopieringen för fil resursen. På menyn Recovery Services valv väljer du **säkerhets kopierings principer** under avsnittet **Hantera** . Alla säkerhets kopierings principer som kon figurer ATS i valvet visas.

   ![Alla säkerhets kopierings principer](./media/manage-afs-backup/all-backup-policies.png)

1. Om du vill visa principer som är speciella för **Azure-filresursen** väljer du **Azure-filresurs** i list rutan längst upp till höger.

   ![Välj Azure-filresurs](./media/manage-afs-backup/azure-file-share.png)

Så här skapar du en ny säkerhets kopierings princip:

1. I fönstret **säkerhets kopierings principer** väljer du **+ Lägg till**.

   ![Ny säkerhets kopierings princip](./media/manage-afs-backup/new-backup-policy.png)

1. I fönstret **Lägg till** väljer du **Azure-filresurs** som **princip typ**. Rutan **säkerhets kopierings policy** för **Azure-filresursen** öppnas. Ange princip namn, säkerhets kopierings frekvens och kvarhållningsintervall för återställnings punkterna. När du har definierat principen väljer du **OK**.

   ![Definiera säkerhets kopierings policyn](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Ändra princip

Du kan ändra en säkerhets kopierings princip om du vill ändra säkerhets kopierings frekvens eller kvarhållningsintervall.

Så här ändrar du en princip:

1. Öppna det Recovery Services-valv som du använde för att konfigurera säkerhets kopieringen för fil resursen. På menyn Recovery Services valv väljer du **säkerhets kopierings principer** under avsnittet **Hantera** . Alla säkerhets kopierings principer som kon figurer ATS i valvet visas.

   ![Alla säkerhets kopierings principer i valvet](./media/manage-afs-backup/all-backup-policies-modify.png)

1. Om du vill visa principer som är speciella för en Azure-filresurs väljer du **Azure-filresurs** i list rutan längst upp till höger. Välj den säkerhets kopierings princip som du vill ändra.

   ![Azure-filresurs att ändra](./media/manage-afs-backup/azure-file-share-modify.png)

1. **Schema** fönstret öppnas. Redigera **schema för säkerhets kopiering** och **kvarhållningsintervall** efter behov och välj **Spara**. Du ser meddelandet "uppdatering pågår" i fönstret. När princip ändringarna har uppdaterats visas meddelandet "uppdateringen av säkerhets kopierings principen har uppdaterats".

   ![Spara den ändrade principen](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Avbryta skyddet av en filresurs

Det finns två sätt att sluta skydda Azure-filresurser:

* Stoppa alla framtida säkerhets kopierings jobb och *ta bort alla återställnings punkter*.
* Stoppa alla framtida säkerhets kopierings jobb, men *lämna återställnings punkterna*.

Det kan finnas en kostnad som är kopplad till att lämna återställnings punkterna i lagringen, eftersom de underliggande ögonblicks bilderna som skapats av Azure Backup kommer att behållas. Fördelen med att lämna återställnings punkterna är att du kan återställa fil resursen senare. Information om kostnaden för att lämna återställnings punkterna finns i [pris informationen](https://azure.microsoft.com/pricing/details/backup/). Om du väljer att ta bort alla återställnings punkter kan du inte återställa fil resursen.

Sluta skydda en Azure-filresurs:

1. Öppna Recovery Services-valvet som innehåller fil resurs återställnings punkterna. Välj **säkerhets kopierings objekt** under avsnittet **skyddade objekt** . Listan över typer av säkerhets kopierings objekt visas.

   ![Säkerhetskopieringsobjekt](./media/manage-afs-backup/backup-items.png)

1. I listan **Typ av säkerhetskopieringshantering** väljer du **Azure Storage (Azure Files)**. Listan **säkerhets kopierings objekt (Azure Storage Azure Files)** visas.

   ![Välj Azure Storage (Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. I listan **säkerhets kopierings objekt (Azure Storage (Azure Files))** väljer du det säkerhets kopierings objekt som du vill stoppa skyddet för.

1. Välj alternativet **stoppa säkerhets kopiering** .

   ![Välja Avbryt säkerhetskopiering](./media/manage-afs-backup/stop-backup.png)

1. I fönstret **stoppa säkerhets kopiering** väljer du **Behåll säkerhets kopierings data** eller **ta bort säkerhetskopierade data**. Välj sedan **stoppa säkerhets kopiering**.

    ![Välj Behåll säkerhets kopierings data eller ta bort säkerhets kopierings data](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Återuppta skyddet av en filresurs

Om alternativet **Behåll säkerhets kopierings data** valdes när skyddet för fil resursen stoppades, är det möjligt att återuppta skyddet. Om alternativet **ta bort säkerhets kopierings data** valdes, kan inte skyddet för fil resursen återupptas.

Återuppta skyddet för Azure-fil resursen:

1. Öppna Recovery Services-valvet som innehåller fil resurs återställnings punkterna. Välj **säkerhets kopierings objekt** under avsnittet **skyddade objekt** . Listan över typer av säkerhets kopierings objekt visas.

   ![Säkerhetskopiera objekt för återuppta](./media/manage-afs-backup/backup-items-resume.png)

1. I listan **Typ av säkerhetskopieringshantering** väljer du **Azure Storage (Azure Files)**. Listan **säkerhets kopierings objekt (Azure Storage Azure Files)** visas.

   ![Lista över Azure Storage (Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. I listan **säkerhets kopierings objekt (Azure Storage (Azure Files))** väljer du det säkerhets kopierings objekt som du vill återuppta skyddet för.

1. Välj alternativet **återuppta säkerhets kopiering** .

   ![Välj återuppta säkerhets kopiering](./media/manage-afs-backup/resume-backup.png)

1. Fönstret **säkerhets kopierings princip** öppnas. Välj en princip som du väljer för att återuppta säkerhets kopieringen.

1. När du har valt en princip för säkerhets kopiering väljer du **Spara**. Du ser meddelandet "uppdatering pågår" i portalen. När säkerhets kopieringen har återupptagits visas meddelandet "en uppdaterad säkerhets kopierings princip för den skyddade Azure-filresursen".

   ![Säkerhets kopierings principen har uppdaterats](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Ta bort säkerhetskopieringsdata

Du kan ta bort säkerhets kopian av en fil resurs under **säkerhets kopierings** jobbet, eller när som helst efter att du har stoppat skyddet. Det kan vara fördelaktigt att vänta i dagar eller till och med veckor innan du tar bort återställnings punkterna. När du tar bort säkerhetskopierade data kan du inte välja vissa återställnings punkter att ta bort. Om du bestämmer dig för att ta bort dina säkerhetskopierade data tar du bort alla återställnings punkter som är associerade med fil resursen.

Följande procedur förutsätter att skyddet har stoppats för fil resursen.

Ta bort säkerhetskopierade data för Azure-filresursen:

1. När säkerhets kopierings jobbet har stoppats är alternativen **Fortsätt säkerhetskopiera** och **ta bort säkerhets kopierings data** tillgängliga på instrument panelen för säkerhets kopierings **objekt** . Välj alternativet för att **ta bort säkerhets kopierings data** .

   ![Ta bort säkerhetskopieringsdata](./media/manage-afs-backup/delete-backup-data.png)

1. Fönstret **ta bort säkerhets kopierings data** öppnas. Ange namnet på fil resursen för att bekräfta borttagningen. Du kan också ange mer information i rutorna **orsak** eller **kommentarer** . När du är säker på att du vill ta bort säkerhetskopierade data väljer du **ta bort**.

   ![Bekräfta borttagning av data](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>Avregistrera ett lagrings konto

Om du vill skydda dina fil resurser i ett visst lagrings konto med hjälp av ett annat Recovery Services-valv måste du först [sluta skydda alla fil resurser](#stop-protection-on-a-file-share) i lagrings kontot. Avregistrera sedan kontot från det aktuella Recovery Services-valvet som används för skydd.

Följande procedur förutsätter att skyddet har stoppats för alla fil resurser i lagrings kontot som du vill avregistrera.

Så här avregistrerar du lagrings kontot:

1. Öppna Recovery Servicess valvet där ditt lagrings konto är registrerat.
1. I **översikts** fönstret väljer du alternativet **infrastruktur för säkerhets kopiering** under avsnittet **Hantera** .

   ![Välja infrastruktur för säkerhetskopiering](./media/manage-afs-backup/backup-infrastructure.png)

1. Fönstret **infrastruktur för säkerhets kopiering** öppnas. Välj **lagrings konton** under avsnittet **Azure Storage-konton** .

   ![Välj lagrings konton](./media/manage-afs-backup/storage-accounts.png)

1. När du har valt **lagrings konton** visas en lista över lagrings konton som registrerats med valvet.
1. Högerklicka på det lagrings konto som du vill avregistrera och välj **avregistrera**.

   ![Välj avregistrera](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Felsöka säkerhets kopiering av Azure-filresurser](./troubleshoot-azure-files.md).
