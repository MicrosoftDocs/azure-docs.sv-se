---
title: Felsöka säkerhets kopiering av Azure-filresurs
description: Den här artikeln kan användas som felsökningsinformation om det skulle uppstå problem när du skyddar dina Azure (filresurser).
ms.date: 02/10/2020
ms.topic: troubleshooting
ms.openlocfilehash: 4908b8ed97bad43d9d24427660a8691ee43d7eaf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89376986"
---
# <a name="troubleshoot-problems-while-backing-up-azure-file-shares"></a>Felsöka problem vid säkerhets kopiering av Azure-filresurser

Den här artikeln innehåller felsöknings information för att åtgärda eventuella problem som du följer när du konfigurerar säkerhets kopiering eller återställning av Azure-filresurser med hjälp av tjänsten Azure Backup.

## <a name="common-configuration-issues"></a>Vanliga konfigurations problem

### <a name="could-not-find-my-storage-account-to-configure-backup-for-the-azure-file-share"></a>Det gick inte att hitta mitt lagrings konto för att konfigurera säkerhets kopiering för Azure-filresursen

- Vänta tills identifieringen har slutförts.
- Kontrol lera om någon fil resurs under lagrings kontot redan är skyddad med ett annat Recovery Services-valv.

  >[!NOTE]
  >Alla filresurser i ett lagringskonto kan endast skyddas med ett Recovery Services-valv. Du kan använda [det här skriptet](scripts/backup-powershell-script-find-recovery-services-vault.md) för att hitta Recovery Services valv där ditt lagrings konto är registrerat.

- Se till att fil resursen inte finns i något av de lagrings konton som inte stöds. Du kan läsa [support mat ris för Azure-filresursen](azure-file-share-support-matrix.md) för att hitta lagrings konton som stöds.
- Se till att den kombinerade längden på lagrings kontots namn och resurs gruppens namn inte överskrider 84 tecken i fallet med nya lagrings konton och 77 tecken om det är klassiska lagrings konton.
- Kontrol lera brand Väggs inställningarna för lagrings kontot för att säkerställa att alternativet att tillåta att betrodda Microsoft-tjänster har åtkomst till lagrings kontot har Aktiver ATS.

### <a name="error-in-portal-states-discovery-of-storage-accounts-failed"></a>Fel i portalen anger att identifieringen av lagringskonton misslyckades

Ignorera felet om du har en partner prenumeration (CSP-aktiverad). Kontakta supporten om din prenumeration inte är CSP-aktiverad och dina lagrings konton inte kan identifieras.

### <a name="selected-storage-account-validation-or-registration-failed"></a>Det gick inte att verifiera eller registrera den valda lagrings kontot

Försök att registrera igen. Kontakta supporten om problemet kvarstår.

### <a name="could-not-list-or-find-file-shares-in-the-selected-storage-account"></a>Det gick inte att lista eller hitta fil resurser i det valda lagrings kontot

- Kontrol lera att lagrings kontot finns i resurs gruppen och att det inte har tagits bort eller flyttats efter den senaste verifieringen eller registreringen i valvet.
- Se till att fil resursen du vill skydda inte har tagits bort.
- Se till att lagrings kontot är ett lagrings konto som stöds för fil resurs säkerhets kopiering. Du kan läsa [support mat ris för Azure-filresursen](azure-file-share-support-matrix.md) för att hitta lagrings konton som stöds.
- Kontrol lera om fil resursen redan är skyddad i samma Recovery Services-valv.

### <a name="backup-file-share-configuration-or-the-protection-policy-configuration-is-failing"></a>Konfiguration av säkerhets kopierings fil resurs (eller konfiguration av skydds princip) fungerar inte

- Gör om konfigurationen för att se om problemet kvarstår.
- Se till att den fil resurs som du vill skydda inte har tagits bort.
- Om du försöker skydda flera fil resurser samtidigt och några av fil resurserna Miss lyckas, kan du prova med att konfigurera säkerhets kopieringen för de misslyckade fil resurserna igen.

### <a name="unable-to-delete-the-recovery-services-vault-after-unprotecting-a-file-share"></a>Det gick inte att ta bort Recovery Services valvet efter borttagning av skydd för en fil resurs

I Azure Portal öppnar du ditt **valv**  >    >  **lagrings konton** för säkerhets kopierings infrastruktur. Välj **avregistrera** för att ta bort lagrings kontona från Recovery Servicess valvet.

>[!NOTE]
>Ett Recovery Services-valv kan bara tas bort när alla lagrings konton som registrerats med valvet har avregistrerats.

## <a name="common-backup-or-restore-errors"></a>Vanliga säkerhets kopierings-eller återställnings fel

>[!NOTE]
>Se [det här dokumentet](./backup-rbac-rs-vault.md#minimum-role-requirements-for-the-azure-file-share-backup) för att se till att du har behörighet att utföra säkerhets kopierings-eller återställnings åtgärder.

### <a name="filesharenotfound--operation-failed-as-the-file-share-is-not-found"></a>FileShareNotFound-åtgärden misslyckades eftersom det inte gick att hitta fil resursen

Felkod: FileShareNotFound

Fel meddelande: åtgärden misslyckades eftersom det inte gick att hitta fil resursen

Se till att fil resursen som du försöker skydda inte har tagits bort.

### <a name="usererrorfileshareendpointunreachable--storage-account-not-found-or-not-supported"></a>UserErrorFileShareEndpointUnreachable-lagrings kontot hittades inte eller stöds inte

Felkod: UserErrorFileShareEndpointUnreachable

Fel meddelande: lagrings kontot hittades inte eller stöds inte

- Kontrol lera att lagrings kontot finns i resurs gruppen och inte har tagits bort eller tagits bort från resurs gruppen efter den senaste verifieringen.

- Se till att lagrings kontot är ett lagrings konto som stöds för fil resurs säkerhets kopiering.

### <a name="afsmaxsnapshotreached--you-have-reached-the-max-limit-of-snapshots-for-this-file-share-you-will-be-able-to-take-more-once-the-older-ones-expire"></a>AFSMaxSnapshotReached-du har nått Max gränsen för ögonblicks bilder för den här fil resursen. du kommer att kunna ta längre tid än den äldre tiden

Felkod: AFSMaxSnapshotReached

Fel meddelande: du har nått Max gränsen för ögonblicks bilder för den här fil resursen. du kommer att kunna ta längre tid än tidigare.

- Det här felet kan inträffa när du skapar flera säkerhets kopieringar på begäran för en fil resurs.
- Det finns en gräns på 200 ögonblicks bilder per fil resurs, inklusive de som tas av Azure Backup. Äldre schemalagda säkerhetskopieringar (eller ögonblicksbilder) rensas automatiskt. Säkerhetskopieringar på begäran (eller ögonblicksbilder) måste tas bort om den maximala gränsen har nåtts.

Ta bort säkerhetskopieringar på begäran (ögonblicksbilder av Azure-filresurser) från Azure Files-portalen.

>[!NOTE]
> Återställningspunkterna går förlorade om du tar bort ögonblicksbilder som skapats av Azure Backup.

### <a name="usererrorstorageaccountnotfound--operation-failed-as-the-specified-storage-account-does-not-exist-anymore"></a>UserErrorStorageAccountNotFound-åtgärden misslyckades eftersom det angivna lagrings kontot inte finns längre

Felkod: UserErrorStorageAccountNotFound

Fel meddelande: det gick inte att utföra åtgärden eftersom det angivna lagrings kontot inte finns längre.

Kontrol lera att lagrings kontot fortfarande finns och inte har tagits bort.

### <a name="usererrordtsstorageaccountnotfound--the-storage-account-details-provided-are-incorrect"></a>UserErrorDTSStorageAccountNotFound-informationen om lagrings kontot är felaktig

Felkod: UserErrorDTSStorageAccountNotFound

Fel meddelande: informationen om lagrings kontot är felaktig.

Kontrol lera att lagrings kontot fortfarande finns och inte har tagits bort.

### <a name="usererrorresourcegroupnotfound--resource-group-doesnt-exist"></a>UserErrorResourceGroupNotFound-resurs gruppen finns inte

Felkod: UserErrorResourceGroupNotFound

Fel meddelande: resurs gruppen finns inte

Välj en befintlig resurs grupp eller skapa en ny resurs grupp.

### <a name="parallelsnapshotrequest--a-backup-job-is-already-in-progress-for-this-file-share"></a>ParallelSnapshotRequest-ett säkerhets kopierings jobb pågår redan för den här fil resursen

Felkod: ParallelSnapshotRequest

Fel meddelande: ett säkerhets kopierings jobb pågår redan för den här fil resursen.

- Fil resurs säkerhets kopiering stöder inte parallella ögonblicks bild begär Anden mot samma fil resurs.

- Vänta tills det befintliga säkerhets kopierings jobbet har slutförts och försök sedan igen. Om du inte kan hitta ett säkerhets kopierings jobb i Recovery Services-valvet, kontrollerar du andra Recovery Services-valv i samma prenumeration.

### <a name="filesharebackupfailedwithazurerprequestthrottling-filesharerestorefailedwithazurerprequestthrottling--file-share-backup-or-restore-failed-due-to-storage-service-throttling-this-may-be-because-the-storage-service-is-busy-processing-other-requests-for-the-given-storage-account"></a>FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling-säkerhets kopiering eller återställning av fil resurs misslyckades på grund av begränsningar i lagrings tjänsten. Detta kan bero på att lagrings tjänsten är upptagen med att bearbeta andra begär Anden för det aktuella lagrings kontot

Felkod: FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling

Fel meddelande: fil resurs säkerhets kopiering eller återställning misslyckades på grund av begränsningar i lagrings tjänsten. Detta kan bero på att lagringstjänsten är upptagen med andra begäranden för det angivna lagringskontot.

Försök utföra säkerhets kopiering/återställning vid ett senare tillfälle.

### <a name="targetfilesharenotfound--target-file-share-not-found"></a>TargetFileShareNotFound-mål fil resurs hittades inte

Felkod: TargetFileShareNotFound

Fel meddelande: det gick inte att hitta mål fil resursen.

- Kontrol lera att det valda lagrings kontot finns och att mål fil resursen inte har tagits bort.

- Se till att lagrings kontot är ett lagrings konto som stöds för fil resurs säkerhets kopiering.

### <a name="usererrorstorageaccountislocked--backup-or-restore-jobs-failed-due-to-storage-account-being-in-locked-state"></a>UserErrorStorageAccountIsLocked-säkerhets kopierings-eller återställnings jobb misslyckades på grund av att lagrings kontot är i låst läge

Felkod: UserErrorStorageAccountIsLocked

Fel meddelande: säkerhets kopierings-eller återställnings jobb misslyckades på grund av att lagrings kontot är i låst läge.

Ta bort låset på lagrings kontot eller Använd **ta bort lås** i stället för **läslås** och försök att säkerhetskopiera eller återställa igen.

### <a name="datatransferservicecoflimitreached--recovery-failed-because-number-of-failed-files-are-more-than-the-threshold"></a>DataTransferServiceCoFLimitReached-återställningen misslyckades eftersom antalet misslyckade filer överstiger tröskelvärdet

Felkod: DataTransferServiceCoFLimitReached

Fel meddelande: återställningen misslyckades eftersom antalet misslyckade filer är större än tröskelvärdet.

- Orsaken till återställnings felen finns i en fil (sökvägen finns i jobb informationen). Åtgärda felen och försök att utföra återställnings åtgärden för de filer som misslyckades.

- Vanliga orsaker till fil återställnings problem:

  - filer som misslyckats används för närvarande
  - Det finns en katalog med samma namn som den felaktiga filen i den överordnade katalogen.

### <a name="datatransferserviceallfilesfailedtorecover--recovery-failed-as-no-file-could-be-recovered"></a>DataTransferServiceAllFilesFailedToRecover-återställningen misslyckades eftersom ingen fil kunde återställas

Felkod: DataTransferServiceAllFilesFailedToRecover

Fel meddelande: återställningen misslyckades eftersom ingen fil kunde återställas.

- Orsaken till återställnings felen finns i en fil (sökvägen finns i jobb informationen). Åtgärda felen och försök att köra återställningsåtgärden på nytt för bara de filer som misslyckades.

- Vanliga orsaker till fil återställnings problem:

  - filer som misslyckats används för närvarande
  - Det finns en katalog med samma namn som den felaktiga filen i den överordnade katalogen.

### <a name="usererrordtssourceurinotvalid---restore-fails-because-one-of-the-files-in-the-source-does-not-exist"></a>UserErrorDTSSourceUriNotValid – återställningen Miss lyckas eftersom en av filerna i källan inte finns

Felkod: DataTransferServiceSourceUriNotValid

Fel meddelande: det går inte att återställa eftersom en av filerna i källan inte finns.

- De markerade objekten finns inte i informationen för återställningspunkten. Ange rätt fillista för att återställa filerna.
- Ögonblicksbilden av filresursen som motsvarar återställningspunkten tas bort manuellt. Välj en annan återställningspunkt och försök att utföra återställningsåtgärden på nytt.

### <a name="usererrordtsdestlocked--a-recovery-job-is-in-process-to-the-same-destination"></a>UserErrorDTSDestLocked-ett återställnings jobb håller på att bearbetas till samma mål

Felkod: UserErrorDTSDestLocked

Fel meddelande: ett återställnings jobb pågår på samma mål.

- Fil resurs säkerhets kopiering stöder inte parallell återställning till samma mål fil resurs.

- Vänta tills den pågående återställningen har slutförts och prova sedan igen. Om du inte hittar ett återställnings jobb i Recovery Services-valvet kan du kontrol lera andra Recovery Services valv i samma prenumeration.

### <a name="usererrortargetfilesharefull--restore-operation-failed-as-target-file-share-is-full"></a>UserErrorTargetFileShareFull-det gick inte att återställa eftersom mål fil resursen är full

Felkod: UserErrorTargetFileShareFull

Fel meddelande: återställnings åtgärden misslyckades eftersom mål fil resursen är full.

Öka storleks kvoten för mål fil resursen för att hantera återställnings data och försök utföra återställnings åtgärden igen.

### <a name="usererrortargetfilesharequotanotsufficient--target-file-share-does-not-have-sufficient-storage-size-quota-for-restore"></a>UserErrorTargetFileShareQuotaNotSufficient-mål fil resursen har inte tillräckligt med lagrings storleks kvot för återställning

Felkod: UserErrorTargetFileShareQuotaNotSufficient

Fel meddelande: mål fil resursen har inte tillräckligt med lagrings storleks kvot för återställning

Öka storleks kvoten för mål fil resursen för att kunna hantera återställnings data och försök sedan igen

### <a name="file-sync-prerestorefailed--restore-operation-failed-as-an-error-occurred-while-performing-pre-restore-operations-on-file-sync-service-resources-associated-with-the-target-file-share"></a>File Sync PreRestoreFailed-Restore-åtgärden misslyckades eftersom det uppstod ett fel under återställnings åtgärder på File Sync tjänst resurser som är kopplade till mål fil resursen

Felkod: File Sync PreRestoreFailed

Fel meddelande: återställnings åtgärden misslyckades eftersom ett fel inträffade under återställnings åtgärder på File Sync tjänst resurser som är kopplade till mål fil resursen.

Försök att återställa data vid ett senare tillfälle. Kontakta Microsofts supportavdelning om problemet kvarstår.

### <a name="azurefilesyncchangedetectioninprogress--azure-file-sync-service-change-detection-is-in-progress-for-the-target-file-share-the-change-detection-was-triggered-by-a-previous-restore-to-the-target-file-share"></a>AzureFileSyncChangeDetectionInProgress-Azure File Sync tjänstens ändrings identifiering pågår för mål fil resursen. Ändrings identifieringen utlöstes av en tidigare återställning till mål fil resursen

Felkod: AzureFileSyncChangeDetectionInProgress

Fel meddelande: Azure File Sync tjänst ändrings identifiering pågår för mål fil resursen. Ändrings identifieringen utlöstes av en tidigare återställning till mål fil resursen.

Använd en annan mål fil resurs. Du kan också vänta på att Azure File Sync tjänst ändrings identifieringen ska slutföras för mål fil resursen innan du försöker återställa igen.

### <a name="usererrorafsrecoverysomefilesnotrestored--one-or-more-files-could-not-be-recovered-successfully-for-more-information-check-the-failed-file-list-in-the-path-given-above"></a>UserErrorAFSRecoverySomeFilesNotRestored-en eller flera filer kunde inte återställas. Mer information finns i listan över misslyckade filer i sökvägen ovan

Felkod: UserErrorAFSRecoverySomeFilesNotRestored

Fel meddelande: en eller flera filer kunde inte återställas. Mer information finns i listan med filer som misslyckats på den sökväg som anges ovan.

- Orsaken till återställnings felen finns i filen (sökvägen finns i jobb informationen). Åtgärda orsakerna och försök att utföra återställnings åtgärden för de filer som misslyckades.
- Vanliga orsaker till fil återställnings problem:

  - filer som misslyckats används för närvarande
  - Det finns en katalog med samma namn som den felaktiga filen i den överordnade katalogen.

### <a name="usererrorafssourcesnapshotnotfound--azure-file-share-snapshot-corresponding-to-recovery-point-cannot-be-found"></a>UserErrorAFSSourceSnapshotNotFound-det går inte att hitta ögonblicks bilden för Azure-filresursen

Felkod: UserErrorAFSSourceSnapshotNotFound

Fel meddelande: det går inte att hitta ögonblicks bilden av Azure-filresursen för återställnings punkten

- Se till att fil resursens ögonblicks bild, som motsvarar den återställnings punkt som du försöker använda för återställning, fortfarande finns.

  >[!NOTE]
  >Om du tar bort en ögonblicks bild av en fil resurs som har skapats av Azure Backup, blir motsvarande återställnings punkter oanvändbara. Vi rekommenderar att du inte tar bort ögonblicks bilder för att garantera garanterad återställning.

- Försök att välja en annan återställnings punkt för att återställa dina data.

### <a name="usererroranotherrestoreinprogressonsametarget--another-restore-job-is-in-progress-on-the-same-target-file-share"></a>UserErrorAnotherRestoreInProgressOnSameTarget-ett annat återställnings jobb pågår på samma mål fil resurs

Felkod: UserErrorAnotherRestoreInProgressOnSameTarget

Fel meddelande: ett annat återställnings jobb pågår på samma mål fil resurs

Använd en annan mål fil resurs. Alternativt avbryter du åtgärden eller väntar tills den andra återställningen är klar.

## <a name="common-modify-policy-errors"></a>Vanliga ändringar av princip fel

### <a name="bmsusererrorconflictingprotectionoperation--another-configure-protection-operation-is-in-progress-for-this-item"></a>BMSUserErrorConflictingProtectionOperation – en annan konfigurations skydds åtgärd pågår för det här objektet

Felkod: BMSUserErrorConflictingProtectionOperation

Fel meddelande: en annan konfigurations skydds åtgärd pågår för det här objektet.

Vänta tills den tidigare ändrings princip åtgärden har slutförts och försök igen vid ett senare tillfälle.

### <a name="bmsusererrorobjectlocked--another-operation-is-in-progress-on-the-selected-item"></a>BMSUserErrorObjectLocked-en annan åtgärd pågår för det valda objektet

Felkod: BMSUserErrorObjectLocked

Fel meddelande: en annan åtgärd pågår för det valda objektet.

Vänta tills den andra pågående åtgärden har slutförts och försök igen vid ett senare tillfälle.

## <a name="common-soft-delete-related-errors"></a>Vanliga fel som rör mjuk borttagning

### <a name="usererrorrestoreafsinsoftdeletestate--this-restore-point-is-not-available-as-the-snapshot-associated-with-this-point-is-in-a-file-share-that-is-in-soft-deleted-state"></a>UserErrorRestoreAFSInSoftDeleteState – den här återställnings punkten är inte tillgänglig eftersom ögonblicks bilden som är kopplad till den här punkten finns i en fil resurs som är i läget Soft-Deleted

Felkod: UserErrorRestoreAFSInSoftDeleteState

Fel meddelande: den här återställnings punkten är inte tillgänglig eftersom ögonblicks bilden som är kopplad till den här punkten finns i en fil resurs som är i läget Soft-Deleted.

Du kan inte utföra en återställnings åtgärd när fil resursen är i läget Soft Deleted. Ångra borttagningen av fil resursen från fil portalen eller Använd [skriptet ta bort](scripts/backup-powershell-script-undelete-file-share.md) och försök sedan återställa.

### <a name="usererrorrestoreafsindeletestate--listed-restore-points-are-not-available-as-the-associated-file-share-containing-the-restore-point-snapshots-has-been-deleted-permanently"></a>UserErrorRestoreAFSInDeleteState-listade återställnings punkter är inte tillgängliga eftersom den tillhör ande fil resurs som innehåller återställnings punkt ögonblicks bilderna har tagits bort permanent

Felkod: UserErrorRestoreAFSInDeleteState

Fel meddelande: listade återställnings punkter är inte tillgängliga eftersom den tillhör ande fil resurs som innehåller återställnings punkt ögonblicks bilderna har tagits bort permanent.

Kontrol lera att den säkerhetskopierade fil resursen har tagits bort. Om den var i läget tyst Borttagning kontrollerar du om den mjuka borttagnings perioden är över och återställdes inte tillbaka. I något av dessa fall förlorar du alla ögonblicks bilder permanent och kommer inte att kunna återställa data.

>[!NOTE]
> Vi rekommenderar att du inte tar bort den säkerhetskopierade fil resursen, eller om den är i tyst borttagen tillstånd, ångrar borttagningen innan perioden för mjuk borttagning upphör, för att undvika att alla återställnings punkter går förlorade.

### <a name="usererrorbackupafsinsoftdeletestate---backup-failed-as-the-azure-file-share-is-in-soft-deleted-state"></a>UserErrorBackupAFSInSoftDeleteState – det gick inte att säkerhetskopiera eftersom Azure-filresursen är i läget Soft-Deleted

Felkod: UserErrorBackupAFSInSoftDeleteState

Fel meddelande: säkerhets kopieringen misslyckades eftersom Azure-filresursen är i läget Soft-Deleted

Ångra borttagningen av fil resursen från fil **portalen** eller genom att använda [borttagnings skriptet](scripts/backup-powershell-script-undelete-file-share.md) för att fortsätta med säkerhets kopieringen och förhindra permanent borttagning av data.

### <a name="usererrorbackupafsindeletestate--backup-failed-as-the-associated-azure-file-share-is-permanently-deleted"></a>UserErrorBackupAFSInDeleteState – det gick inte att säkerhetskopiera eftersom den associerade Azure-filresursen tas bort permanent

Felkod: UserErrorBackupAFSInDeleteState

Fel meddelande: säkerhets kopieringen misslyckades eftersom den associerade Azure-filresursen tas bort permanent

Kontrol lera att den säkerhetskopierade fil resursen har tagits bort permanent. Om ja, stoppa säkerhets kopieringen för fil resursen för att undvika upprepade säkerhets kopierings fel. Information om hur du stoppar skyddet finns i [stoppa skyddet för Azure-filresurs](./manage-afs-backup.md#stop-protection-on-a-file-share)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du säkerhetskopierar Azure-filresurser finns i:

- [Säkerhetskopiera Azure-filresurser](backup-afs.md)
- [Säkerhetskopiera Azure-filresurser – Vanliga frågor och svar](backup-azure-files-faq.md)
