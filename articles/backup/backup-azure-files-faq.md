---
title: Vanliga frågor och svar om säkerhetskopiering av Azure Files
description: I den här artikeln hittar du svar på vanliga frågor om hur du skyddar dina Azure-filresurser med Azure Backup-tjänsten.
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: e2b6afb25e189ee2848f25c0ba59d843baf37090
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91940843"
---
# <a name="questions-about-backing-up-azure-files"></a>Frågor om hur du säkerhetskopierar Azure Files

Den här artikeln innehåller vanliga frågor och svar om hur du säkerhetskopierar Azure Files. I vissa svar finns det länkar till artiklar som har omfattande information. Du kan också skicka frågor om tjänsten Azure Backup på [sidan Microsoft Q&en fråga för diskussion](/answers/topics/azure-backup.html).

Om du snabbt vill titta igenom avsnitten i denna artikel kan du använda länkarna till höger, under **Innehåll i artikeln**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Konfigurera säkerhetskopieringsjobbet för Azure Files

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>Varför kan jag inte se vissa av de lagrings konton som jag vill skydda, vilket innehåller giltiga Azure-filresurser?

Se [support mat ris för Azure-filresurser](azure-file-share-support-matrix.md) för att se till att lagrings kontot tillhör en av de typer av lagrings konton som stöds. Det är också möjligt att det lagrings konto som du letar efter redan är skyddat eller registrerat i ett annat valv. [Avregistrera lagrings kontot](manage-afs-backup.md#unregister-a-storage-account) från valvet för att identifiera lagrings kontot i andra valv för skydd.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>Varför visas inte vissa av mina Azure-filresurser i lagringskontot när jag försöker konfigurera säkerhetskopiering?

Kontrollera om Azure-filresursen redan skyddas i samma Recovery Services-valv eller om den nyligen har tagits bort.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Kan jag skydda filresurser som är anslutna till en synkroniseringsgrupp i Azure Files Sync?

Ja. Skydd av Azure-filresurser som är anslutna till Sync-grupper har Aktiver ATS.

### <a name="when-trying-to-back-up-file-shares-i-selected-a-storage-account-to-discover-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>När jag försöker säkerhetskopiera fil resurser har jag valt ett lagrings konto för att identifiera fil resurserna i det. Men jag skyddar dem inte. Hur gör jag för att skyddar du dessa fil resurser med andra valv?

När du försöker säkerhetskopiera och väljer ett lagrings konto för att identifiera fil resurser i det registreras lagrings kontot med valvet som detta görs från. Om du väljer att skydda fil resurserna med ett annat valv [avregistrerar](manage-afs-backup.md#unregister-a-storage-account) du det valda lagrings kontot från det här valvet.

### <a name="why-cant-i-change-the-vault-to-configure-backup-for-the-file-share"></a>Varför kan jag inte ändra valvet för att konfigurera säkerhets kopiering för fil resursen?

Om lagrings kontot redan har registrerats med ett valv eller andra fil resurser i lagrings kontot skyddas med ett valv kan du inte välja att ändra det. Alla fil resurser i ett lagrings konto kan endast skyddas av samma valv. Om du vill ändra valvet måste du [stoppa skyddet för alla fil resurser i lagrings kontot](manage-afs-backup.md#stop-protection-on-a-file-share) från det anslutna valvet, [avregistrera](manage-afs-backup.md#unregister-a-storage-account) lagrings kontot och sedan välja ett annat valv för skydd.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>Kan jag ändra valvet till vilket jag säkerhetskopierar mina fil resurser?

Ja. Du måste dock [stoppa skyddet av fil resursen](manage-afs-backup.md#stop-protection-on-a-file-share) från det anslutna valvet, [avregistrera](manage-afs-backup.md#unregister-a-storage-account) lagrings kontot och sedan skydda det från ett annat valv.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Kan jag skydda två olika filresurser från samma lagringskonto till olika valv?

Nej. Filresurserna i ett lagringskonto kan endast skyddas med ett och samma valv.

## <a name="backup"></a>Backup

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>Vad gör jag om mina säkerhets kopieringar inte startar på grund av den maximala gränsen nås?

Du kan ha upp till 200 ögonblicksbilder för en filresurs vid varje tidpunkt. Gränsen gäller ögonblicksbilderna som har tagits av Azure Backup enligt definitionen i din princip. Om säkerhets kopieringen inte startar när gränsen har nåtts tar du bort ögonblicks bilder på begäran för att genomföra framtida säkerhets kopieringar.

## <a name="restore"></a>Återställ

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>Kan jag återställa från en borttagen Azure-filresurs?

Om fil resursen är i läget tyst Borttagning måste du först ta bort fil resursen för att utföra återställnings åtgärden. Åtgärden ta bort tar fil resursen i det aktiva läge där du kan återställa till en viss tidpunkt. Om du vill veta hur du tar bort en fil resurs kan du gå till [den här länken](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) eller Visa [skriptet ta bort fil resurs](./scripts/backup-powershell-script-undelete-file-share.md). Om fil resursen tas bort permanent kan du inte återställa innehållet och ögonblicks bilderna.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Kan jag återställa från säkerhetskopior om jag har stoppat skyddet på en Azure-filresurs?

Ja. Om du valde **Behåll säkerhetskopieringsdata** när du stoppade skyddet kan du återställa från alla befintliga återställningspunkter.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Vad händer om jag avbryter ett pågående återställnings jobb?

Om ett pågående återställnings jobb avbryts stoppas återställnings processen och alla filer som återställs före annulleringen förblir i konfigurerat mål (ursprunglig eller alternativ plats) utan återställningar.

## <a name="manage-backup"></a>Hantera säkerhets kopiering

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>Kan jag använda PowerShell för att konfigurera/hantera/återställa säkerhetskopior av Azure-filresurser?

Ja. Se den detaljerade dokumentationen [här](backup-azure-afs-automation.md).

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>Kan jag få åtkomst till ögonblicks bilder som tas av Azure backups och montera dem?

Alla ögonblicks bilder som tas av Azure Backup kan nås genom att Visa ögonblicks bilder i portalen, PowerShell eller CLI. Mer information om hur du Azure Files resurs ögonblicks bilder finns i [Översikt över resurs ögonblicks bilder för Azure Files](../storage/files/storage-snapshots-files.md).

### <a name="what-happens-after-i-move-a-backed-up-file-share-to-a-different-subscription"></a>Vad händer när jag flyttar en säkerhetskopierad fil resurs till en annan prenumeration?

När en fil resurs flyttas till en annan prenumeration betraktas den som en ny fil resurs genom att Azure Backup. Detta är de rekommenderade stegen:
 
Scenario: anta att du har en fil resurs *FS1* i prenumeration *S1* och att den skyddas med *v1* -valvet. Nu vill du flytta din fil resurs till prenumeration *S2*.
 
1.  Flytta det önskade lagrings kontot och fil resursen (FS1) till en annan prenumeration (S2).
2.  I v1-valvet utlöses stopp skyddet med åtgärden ta bort data för FS1.
3.  Avregistrera lagrings kontot som är värd för FS1 från v1-valvet.
4.  Konfigurera om säkerhets kopieringen för FS1, som nu har flyttats till S2, med ett valv (v2) i S2-prenumerationen. 
 
Observera att när du har konfigurerat om säkerhets kopieringen med v2 kommer ögonblicks bilderna som togs med v1 inte längre att hanteras av Azure Backup. Därför måste du ta bort dessa ögonblicks bilder manuellt enligt dina krav.

### <a name="can-i-move-my-backed-up-file-share-to-a-different-resource-group"></a>Kan jag flytta min säkerhetskopierade fil resurs till en annan resurs grupp?
 
Ja, du kan flytta din säkerhetskopierade fil resurs till en annan resurs grupp. Du måste dock konfigurera om säkerhets kopieringen för fil resursen eftersom den kommer att behandlas som en ny resurs genom att Azure Backup. Ögonblicks bilderna som skapades innan resurs grupps flyttningen kommer inte längre att hanteras av Azure Backup. Därför måste du ta bort dessa ögonblicks bilder manuellt enligt dina krav.

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Vad är den maximala kvarhållning som jag kan konfigurera för säkerhets kopieringar?

Mer information om maximal kvarhållning finns i [support mat ris](azure-file-share-support-matrix.md) . Azure Backup utför beräkningar i real tid av antalet ögonblicks bilder när du anger Retentions värden när du konfigurerar säkerhets kopierings principen. Så snart antalet ögonblicks bilder som motsvarar dina definierade kvarhållning överskrider 200 visas en varning i portalen där du uppmanas att justera dina kvarhållning-värden. Detta är så att du inte överskrider gränsen för maximalt antal ögonblicks bilder som stöds av Azure Files för alla fil resurser vid en viss tidpunkt.

### <a name="what-is-the-impact-on-existing-recovery-points-and-snapshots-when-i-modify-the-backup-policy-for-an-azure-file-share-to-switch-from-daily-policy-to-gfs-policy"></a>Vad är påverkan på befintliga återställnings punkter och ögonblicks bilder när jag ändrar säkerhets kopierings principen för en Azure-filresurs för att växla från "daglig princip" till "GFS policy"?

När du ändrar en daglig säkerhets kopierings princip till GFS-principen (lägger till vecko Visa per månad/årlig kvarhållning) är beteendet följande:

- **Kvarhållning**: om du lägger till veckovis/månads Visa/årligen kvarhållning som en del av att ändra principen, kommer alla framtida återställnings punkter som skapats som en del av den schemalagda säkerhets kopian att märkas enligt den nya principen. Alla befintliga återställnings punkter betraktas fortfarande som dagliga återställnings punkter och kommer därför inte att märkas som varje vecka/månad/år.

- **Rensningar av ögonblicks bilder och återställnings punkter**:

  - Om den dagliga kvarhållning är utökad uppdateras utgångs datumet för de befintliga återställnings punkterna enligt det dagliga kvarhållningsintervallet som kon figurer ATS i den nya principen.
  - Om daglig kvarhållning minskas markeras befintliga återställnings punkter och ögonblicks bilder för borttagning i nästa rensnings körnings jobb enligt det dagliga kvarhållningsintervallet som kon figurer ATS i den nya principen och tas sedan bort.

Här är ett exempel på hur det fungerar:

#### <a name="existing-policy-p1"></a>Befintlig princip [P1]

|Bevarande typ |Schema |Kvarhållning  |
|---------|---------|---------|
|Varje dag    |    Varje dag med 8 PM    |  100 dagar       |

#### <a name="new-policy-modified-p1"></a>Ny princip [ändrad P1]

| Bevarande typ | Schema                       | Kvarhållning |
| -------------- | ------------------------------ | --------- |
| Varje dag          | Varje dag kl. 9 PM              | 50 dagar   |
| Varje vecka         | På söndag till 9 PM              | 3 veckor   |
| Varje månad        | Den senaste måndagen vid 9 PM         | 1 månad   |
| Varje år         | I Jan den tredje söndagen med 9 PM | 4 år   |

#### <a name="impact"></a>Påverkan

1. Utgångs datumet för befintliga återställnings punkter justeras enligt det dagliga kvarhållningsintervallet för den nya principen: det vill säga 50 dagar. Därför kommer alla återställnings punkter som är äldre än 50 dagar att markeras för borttagning.

2. De befintliga återställnings punkterna märks inte som varje vecka/månad/år baserat på ny princip.

3. Alla framtida säkerhets kopieringar kommer att utlösas enligt det nya schemat: det vill säga kl. 9 PM.

4. Utgångs datumet för alla framtida återställnings punkter kommer att justeras med den nya principen.

>[!NOTE]
>Princip ändringarna påverkar bara de återställnings punkter som skapats som en del av det schemalagda säkerhets kopierings jobbet. För säkerhets kopiering på begäran avgörs kvarhållning av värdet för **kvarhållning till** som anges vid tidpunkten för säkerhets kopieringen.

### <a name="what-is-the-impact-on-existing-recovery-points-when-i-modify-an-existing-gfs-policy"></a>Vad påverkar befintliga återställnings punkter när jag ändrar en befintlig GFS-princip?

När en ny princip tillämpas på fil resurser görs alla framtida schemalagda säkerhets kopieringar enligt schemat som kon figurer ATS i den ändrade principen.  Kvarhållning av alla befintliga återställnings punkter justeras enligt de nya kvarhållningsintervallet som kon figurer ATS. Så om kvarhållning har utökats markeras befintliga återställnings punkter som behålls enligt den nya principen. Om kvarhållning minskas markeras de för rensning i nästa rensnings jobb och tas sedan bort.

Här är ett exempel på hur det fungerar:

#### <a name="existing-policy-p2"></a>Befintlig princip [P2]

| Bevarande typ | Schema           | Kvarhållning |
| -------------- | ------------------ | --------- |
| Varje dag          | Varje dag med 8 PM | 50 dagar   |
| Varje vecka         | På måndag till 8 PM  | 3 veckor   |

#### <a name="new-policy-modified-p2"></a>Ny princip [ändrad P2]

| Bevarande typ | Schema               | Kvarhållning |
| -------------- | ---------------------- | --------- |
| Varje dag          | Varje dag kl. 9 PM     | 10 dagar   |
| Varje vecka         | På måndag till 9 PM      | 2 veckor   |
| Varje månad        | Den senaste måndagen vid 9 PM | 2 månader  |

#### <a name="impact-of-change"></a>Effekt av ändring

1. Utgångs datumet för befintliga dagliga återställnings punkter justeras enligt det nya värdet för daglig kvarhållning, vilket är 10 dagar. Det innebär att alla dagliga återställnings punkter som är äldre än 10 dagar tas bort.

2. Förfallo datumet för befintliga vecko återställnings punkter justeras enligt det nya vecko kvarhållning svärdet, som är två veckor. En veckovis återställnings punkt som är äldre än två veckor kommer att tas bort.

3. De månatliga återställnings punkterna skapas bara som en del av framtida säkerhets kopieringar baserat på den nya princip konfigurationen.

4. Utgångs datumet för alla framtida återställnings punkter kommer att justeras med den nya principen.

>[!NOTE]
>Princip ändringarna påverkar bara de återställnings punkter som skapats som en del av den schemalagda säkerhets kopieringen. För säkerhets kopiering på begäran bestäms kvarhållning av värdet för **kvarhållning till** som anges vid tidpunkten för säkerhets kopieringen.

## <a name="next-steps"></a>Nästa steg

- [Felsöka problem vid säkerhets kopiering av Azure-filresurser](troubleshoot-azure-files.md)
