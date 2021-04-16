---
title: Felsöka säkerhetskopiering av Azure-filresurs
description: Den här artikeln kan användas som felsökningsinformation om det skulle uppstå problem när du skyddar dina Azure (filresurser).
ms.date: 02/10/2020
ms.topic: troubleshooting
ms.openlocfilehash: 4c934d2295fa702425e8df0a03636b9f9208cfa4
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515081"
---
# <a name="troubleshoot-problems-while-backing-up-azure-file-shares"></a>Felsöka problem vid backning av Azure-filresurser

Den här artikeln innehåller felsökningsinformation för att åtgärda eventuella problem som du stöta på när du konfigurerar säkerhetskopiering eller återställning av Azure-filresurser med Azure Backup Service.

## <a name="common-configuration-issues"></a>Vanliga konfigurationsproblem

### <a name="could-not-find-my-storage-account-to-configure-backup-for-the-azure-file-share"></a>Det gick inte att hitta mitt lagringskonto för att konfigurera säkerhetskopiering för Azure-filresursen

- Vänta tills identifieringen har slutförts.
- Kontrollera om någon filresurs under lagringskontot redan är skyddad med ett annat Recovery Services-valv.

  >[!NOTE]
  >Alla filresurser i ett lagringskonto kan endast skyddas med ett Recovery Services-valv. Du kan använda det [här skriptet](scripts/backup-powershell-script-find-recovery-services-vault.md) för att hitta Recovery Services-valvet där ditt lagringskonto är registrerat.

- Kontrollera att filresursen inte finns i något av de lagringskonton som inte stöds. Du kan gå till [supportmatrisen för säkerhetskopiering av Azure-filresurs för](azure-file-share-support-matrix.md) att hitta lagringskonton som stöds.
- Se till att den kombinerade längden på lagringskontots namn och resursgruppens namn inte överstiger 84 tecken för nya lagringskonton och 77 tecken när det gäller klassiska lagringskonton.
- Kontrollera brandväggsinställningarna för lagringskontot för att se till att alternativet att ge betrodda Microsoft-tjänster åtkomst till lagringskontot är aktiverat.

### <a name="error-in-portal-states-discovery-of-storage-accounts-failed"></a>Fel i portalen anger att identifieringen av lagringskonton misslyckades

Om du har en partnerprenumeration (CSP-aktiverad) ignorerar du felet. Om din prenumeration inte är CSP-aktiverad och dina lagringskonton inte kan identifieras kontaktar du supporten.

### <a name="selected-storage-account-validation-or-registration-failed"></a>Verifieringen eller registreringen av det valda lagringskontot misslyckades

Försök att registrera igen. Kontakta supporten om problemet kvarstår.

### <a name="could-not-list-or-find-file-shares-in-the-selected-storage-account"></a>Det gick inte att visa eller hitta filresurser i det valda lagringskontot

- Kontrollera att lagringskontot finns i resursgruppen och inte har tagits bort eller flyttats efter den senaste verifieringen eller registreringen i valvet.
- Kontrollera att filresursen som du vill skydda inte har tagits bort.
- Kontrollera att lagringskontot är ett lagringskonto som stöds för säkerhetskopiering av filresurs. Du kan gå till [säkerhetskopieringsmatrisen för Azure-filresurs för](azure-file-share-support-matrix.md) att hitta lagringskonton som stöds.
- Kontrollera om filresursen redan är skyddad i samma Recovery Services-valv.

### <a name="backup-file-share-configuration-or-the-protection-policy-configuration-is-failing"></a>Säkerhetskopiering av filresurskonfigurationen (eller skyddsprincipkonfigurationen) misslyckas

- Försök igen med konfigurationen för att se om problemet kvarstår.
- Kontrollera att filresursen som du vill skydda inte har tagits bort.
- Om du försöker skydda flera filresurser samtidigt och vissa av filresurser misslyckas kan du försöka konfigurera säkerhetskopiering för de filresurser som misslyckades igen.

### <a name="unable-to-delete-the-recovery-services-vault-after-unprotecting-a-file-share"></a>Det går inte att ta bort Recovery Services-valvet när en filresurs har tagits bort från skyddet

I Azure Portal du lagringskontona **för Vault**  >  **Backup**  >  **Infrastructure**. Välj **Avregistrera för** att ta bort lagringskontona från Recovery Services-valvet.

>[!NOTE]
>Ett Recovery Services-valv kan bara tas bort när du har avregistrerat alla lagringskonton som registrerats med valvet.

## <a name="common-backup-or-restore-errors"></a>Vanliga säkerhetskopierings- eller återställningsfel

>[!NOTE]
>Läs det [här dokumentet för](./backup-rbac-rs-vault.md#minimum-role-requirements-for-the-azure-file-share-backup) att se till att du har tillräcklig behörighet för att utföra säkerhetskopierings- eller återställningsåtgärder.

### <a name="filesharenotfound--operation-failed-as-the-file-share-is-not-found"></a>FileShareNotFound – Åtgärden misslyckades eftersom filresursen inte hittades

Felkod: FileShareNotFound

Felmeddelande: Åtgärden misslyckades eftersom filresursen inte hittades

Kontrollera att filresursen som du försöker skydda inte har tagits bort.

### <a name="usererrorfileshareendpointunreachable--storage-account-not-found-or-not-supported"></a>UserErrorFileShareEndpointUnreachable – Lagringskontot hittades inte eller stöds inte

Felkod: UserErrorFileShareEndpointUnreachable

Felmeddelande: Lagringskontot hittades inte eller stöds inte

- Kontrollera att lagringskontot finns i resursgruppen och inte har tagits bort eller tagits bort från resursgruppen efter den senaste verifieringen.

- Kontrollera att lagringskontot är ett lagringskonto som stöds för säkerhetskopiering av filresurs.

### <a name="afsmaxsnapshotreached--you-have-reached-the-max-limit-of-snapshots-for-this-file-share-you-will-be-able-to-take-more-once-the-older-ones-expire"></a>AFSMaxSnapshotReached – Du har nått maxgränsen för ögonblicksbilder för den här filresursen; du kommer att kunna ta mer när de äldre upphör att gälla

Felkod: AFSMaxSnapshotReached

Felmeddelande: Du har nått maxgränsen för ögonblicksbilder för den här filresursen. du kommer att kunna ta fler när de äldre upphör att gälla.

- Det här felet kan inträffa när du skapar flera säkerhetskopieringar på begäran för en filresurs.
- Det finns en gräns på 200 ögonblicksbilder per filresurs, inklusive de som tas av Azure Backup. Äldre schemalagda säkerhetskopieringar (eller ögonblicksbilder) rensas automatiskt. Säkerhetskopieringar på begäran (eller ögonblicksbilder) måste tas bort om den maximala gränsen har nåtts.

Ta bort säkerhetskopieringar på begäran (ögonblicksbilder av Azure-filresurser) från Azure Files-portalen.

>[!NOTE]
> Återställningspunkterna går förlorade om du tar bort ögonblicksbilder som skapats av Azure Backup.

### <a name="usererrorstorageaccountnotfound--operation-failed-as-the-specified-storage-account-does-not-exist-anymore"></a>UserErrorStorageAccountNotFound – Åtgärden misslyckades eftersom det angivna lagringskontot inte längre finns

Felkod: UserErrorStorageAccountNotFound

Felmeddelande: Åtgärden misslyckades eftersom det angivna lagringskontot inte längre finns.

Kontrollera att lagringskontot fortfarande finns kvar och inte tas bort.

### <a name="usererrordtsstorageaccountnotfound--the-storage-account-details-provided-are-incorrect"></a>UserErrorDTSStorageAccountNotFound – Den angivna lagringskontoinformationen är felaktig

Felkod: UserErrorDTSStorageAccountNotFound

Felmeddelande: Den angivna lagringskontoinformationen är felaktig.

Kontrollera att lagringskontot fortfarande finns kvar och inte tas bort.

### <a name="usererrorresourcegroupnotfound--resource-group-doesnt-exist"></a>UserErrorResourceGroupNotFound – Resursgruppen finns inte

Felkod: UserErrorResourceGroupNotFound

Felmeddelande: Resursgruppen finns inte

Välj en befintlig resursgrupp eller skapa en ny resursgrupp.

### <a name="parallelsnapshotrequest--a-backup-job-is-already-in-progress-for-this-file-share"></a>ParallelSnapshotRequest – Ett säkerhetskopieringsjobb pågår redan för den här filresursen

Felkod: ParallelSnapshotRequest

Felmeddelande: Ett säkerhetskopieringsjobb pågår redan för den här filresursen.

- Säkerhetskopiering av filresurs stöder inte parallella begäranden om ögonblicksbilder mot samma filresurs.

- Vänta tills det befintliga säkerhetskopieringsjobbet har avslutats och försök sedan igen. Om du inte hittar något säkerhetskopieringsjobb i Recovery Services-valvet kontrollerar du andra Recovery Services-valv i samma prenumeration.

### <a name="filesharebackupfailedwithazurerprequestthrottling-filesharerestorefailedwithazurerprequestthrottling--file-share-backup-or-restore-failed-due-to-storage-service-throttling-this-may-be-because-the-storage-service-is-busy-processing-other-requests-for-the-given-storage-account"></a>FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling – Säkerhetskopieringen eller återställningen av filresursen misslyckades på grund av begränsning av lagringstjänsten. Detta kan beror på att lagringstjänsten är upptagen med att bearbeta andra begäranden för det angivna lagringskontot

Felkod: FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling

Felmeddelande: Säkerhetskopieringen eller återställningen av filresursen misslyckades på grund av begränsning av lagringstjänsten. Detta kan bero på att lagringstjänsten är upptagen med andra begäranden för det angivna lagringskontot.

Prova att säkerhetskopiera/återställa vid ett senare tillfälle.

### <a name="targetfilesharenotfound--target-file-share-not-found"></a>TargetFileShareNotFound – Det går inte att hitta målfilresursen

Felkod: TargetFileShareNotFound

Felmeddelande: Målfilresursen hittades inte.

- Kontrollera att det valda lagringskontot finns och att målfilresursen inte tas bort.

- Kontrollera att lagringskontot är ett lagringskonto som stöds för säkerhetskopiering av filresurs.

### <a name="usererrorstorageaccountislocked--backup-or-restore-jobs-failed-due-to-storage-account-being-in-locked-state"></a>UserErrorStorageAccountIsLocked – Säkerhetskopierings- eller återställningsjobb misslyckades på grund av att lagringskontot är i låst tillstånd

Felkod: UserErrorStorageAccountIsLocked

Felmeddelande: Säkerhetskopierings- eller återställningsjobb misslyckades på grund av att lagringskontot är i låst tillstånd.

Ta bort låset på lagringskontot eller använd **borttagningslåset i** stället **för läslås och** försök säkerhetskopiera eller återställ igen.

### <a name="datatransferservicecoflimitreached--recovery-failed-because-number-of-failed-files-are-more-than-the-threshold"></a>DataTransferServiceCoFLimitReached – Återställningen misslyckades eftersom antalet misslyckade filer är större än tröskelvärdet

Felkod: DataTransferServiceCofLimitReached

Felmeddelande: Återställningen misslyckades eftersom antalet misslyckade filer är större än tröskelvärdet.

- Orsaker till återställningsfel visas i en fil (sökvägen finns i jobbinformationen). Åtgärda felen och försök att återställa endast för de filer som misslyckades.

- Vanliga orsaker till filåterställningsfel:

  - filer som misslyckades används för närvarande
  - en katalog med samma namn som den misslyckade filen finns i den överordnade katalogen.

### <a name="datatransferserviceallfilesfailedtorecover--recovery-failed-as-no-file-could-be-recovered"></a>DataTransferServiceAllFilesFailedToRecover – Återställningen misslyckades eftersom ingen fil kunde återställas

Felkod: DataTransferServiceAllFilesFailedToRecover

Felmeddelande: Återställningen misslyckades eftersom ingen fil kunde återställas.

- Orsaker till återställningsfel visas i en fil (sökvägen finns i jobbinformationen). Åtgärda felen och försök att köra återställningsåtgärden på nytt för bara de filer som misslyckades.

- Vanliga orsaker till filåterställningsfel:

  - filer som misslyckades används för närvarande
  - en katalog med samma namn som den misslyckade filen finns i den överordnade katalogen.

### <a name="usererrordtssourceurinotvalid---restore-fails-because-one-of-the-files-in-the-source-does-not-exist"></a>UserErrorDTSSourceUriNotValid – Återställningen misslyckas eftersom en av filerna i källan inte finns

Felkod: DataTransferServiceSourceUriNotValid

Felmeddelande: Återställningen misslyckas eftersom en av filerna i källan inte finns.

- De markerade objekten finns inte i informationen för återställningspunkten. Ange rätt fillista för att återställa filerna.
- Ögonblicksbilden av filresursen som motsvarar återställningspunkten tas bort manuellt. Välj en annan återställningspunkt och försök att utföra återställningsåtgärden på nytt.

### <a name="usererrordtsdestlocked--a-recovery-job-is-in-process-to-the-same-destination"></a>UserErrorDTSDestLocked – ett återställningsjobb pågår till samma mål

Felkod: UserErrorDTSDestLocked

Felmeddelande: Ett återställningsjobb pågår till samma mål.

- Säkerhetskopiering av filresurs stöder inte parallell återställning till samma målfilresurs.

- Vänta tills den pågående återställningen har slutförts och prova sedan igen. Om du inte hittar något återställningsjobb i Recovery Services-valvet kontrollerar du andra Recovery Services-valv i samma prenumeration.

### <a name="usererrortargetfilesharefull--restore-operation-failed-as-target-file-share-is-full"></a>UserErrorTargetFileShareFull – Återställningsåtgärden misslyckades eftersom målfilresursen är full

Felkod: UserErrorTargetFileShareFull

Felmeddelande: Återställningsåtgärden misslyckades eftersom målfilresursen är full.

Öka storlekskvoten för målfilresursen för att hantera återställningsdata och försök att utföra återställningsåtgärden igen.

### <a name="usererrortargetfilesharequotanotsufficient--target-file-share-does-not-have-sufficient-storage-size-quota-for-restore"></a>UserErrorTargetFileShareQuotaNotSufficient – Målfilresursen har inte tillräcklig kvot för lagringsstorlek för återställning

Felkod: UserErrorTargetFileShareQuotaNotSufficient

Felmeddelande: Målfilresursen har inte tillräckligt med lagringsstorlek för återställning

Öka storlekskvoten för målfilresursen för att hantera återställningsdata och försök igen

### <a name="file-sync-prerestorefailed--restore-operation-failed-as-an-error-occurred-while-performing-pre-restore-operations-on-file-sync-service-resources-associated-with-the-target-file-share"></a>File Sync PreRestoreFailed – Återställningsåtgärden misslyckades eftersom ett fel uppstod vid utförande av föråterställningsåtgärder på File Sync Service-resurser som är associerade med målfilresursen

Felkod: File Sync PreRestoreFailed

Felmeddelande: Återställningsåtgärden misslyckades eftersom ett fel uppstod när en föråterställning utfördes på File Sync Service-resurser som är associerade med målfilresursen.

Försök att återställa data vid ett senare tillfälle. Kontakta Microsofts supportavdelning om problemet kvarstår.

### <a name="azurefilesyncchangedetectioninprogress--azure-file-sync-service-change-detection-is-in-progress-for-the-target-file-share-the-change-detection-was-triggered-by-a-previous-restore-to-the-target-file-share"></a>AzureFileSyncChangeDetectionInProgress- Azure File Sync Tjänständringsidentifiering pågår för målfilresursen. Ändringsidentifiering utlöstes av en tidigare återställning till målfilresursen

Felkod: AzureFileSyncChangeDetectionInProgress

Felmeddelande: Azure File Sync tjänständringsidentifiering pågår för målfilresursen. Ändringsidentifiering utlöstes av en tidigare återställning till målfilresursen.

Använd en annan målfilresurs. Du kan också vänta tills Azure File Sync tjänständringsidentifiering har slutförts för målfilresursen innan du försöker återställa igen.

### <a name="usererrorafsrecoverysomefilesnotrestored--one-or-more-files-could-not-be-recovered-successfully-for-more-information-check-the-failed-file-list-in-the-path-given-above"></a>UserErrorAFSRecoverySomeFilesNotRestored – Det gick inte att återställa en eller flera filer. Mer information finns i listan över misslyckade filer i sökvägen ovan

Felkod: UserErrorAFSRecoverySomeFilesNotRestored

Felmeddelande: Det gick inte att återställa en eller flera filer. Mer information finns i listan med filer som misslyckats på den sökväg som anges ovan.

- Orsaker till återställningsfel visas i filen (sökvägen finns i jobbinformationen). Åtgärda orsakerna och försök att återställa endast för de filer som misslyckades.
- Vanliga orsaker till filåterställningsfel:

  - filer som misslyckades används för närvarande
  - en katalog med samma namn som den misslyckade filen finns i den överordnade katalogen.

### <a name="usererrorafssourcesnapshotnotfound--azure-file-share-snapshot-corresponding-to-recovery-point-cannot-be-found"></a>UserErrorAFSSourceSnapshotNotFound – Det går inte att hitta ögonblicksbilden av Azure-filresursen som motsvarar återställningspunkten

Felkod: UserErrorAFSSourceSnapshotNotFound

Felmeddelande: Det går inte att hitta ögonblicksbilden av Azure-filresursen som motsvarar återställningspunkten

- Kontrollera att ögonblicksbilden av filresursen, som motsvarar den återställningspunkt som du försöker använda för återställning, fortfarande finns.

  >[!NOTE]
  >Om du tar bort en ögonblicksbild av en filresurs som har skapats Azure Backup kan motsvarande återställningspunkter bli oanvändbara. Vi rekommenderar att du inte tar bort ögonblicksbilder för att garantera garanterad återställning.

- Försök att välja en annan återställningspunkt för att återställa dina data.

### <a name="usererroranotherrestoreinprogressonsametarget--another-restore-job-is-in-progress-on-the-same-target-file-share"></a>UserErrorAnotherRestoreInProgressOnSameTarget – Ett annat återställningsjobb pågår på samma målfilresurs

Felkod: UserErrorAnotherRestoreInProgressOnSameTarget

Felmeddelande: Ett annat återställningsjobb pågår på samma målfilresurs

Använd en annan målfilresurs. Alternativt avbryter du åtgärden eller väntar tills den andra återställningen är klar.

## <a name="common-modify-policy-errors"></a>Vanliga ändringsprincipfel

### <a name="bmsusererrorconflictingprotectionoperation--another-configure-protection-operation-is-in-progress-for-this-item"></a>BMSUserErrorConflictingProtectionOperation – En annan åtgärd för att konfigurera skydd pågår för det här objektet

Felkod: BMSUserErrorConflictingProtectionOperation

Felmeddelande: En annan åtgärd för att konfigurera skydd pågår för det här objektet.

Vänta tills den tidigare ändringsprincipåtgärden har avslutats och försök igen vid ett senare tillfälle.

### <a name="bmsusererrorobjectlocked--another-operation-is-in-progress-on-the-selected-item"></a>BMSUserErrorObjectLocked – En annan åtgärd pågår för det valda objektet

Felkod: BMSUserErrorObjectLocked

Felmeddelande: En annan åtgärd pågår för det valda objektet.

Vänta tills den andra pågående åtgärden har slutförts och försök igen vid ett senare tillfälle.

## <a name="common-soft-delete-related-errors"></a>Vanliga fel relaterade till mjuk borttagning

### <a name="usererrorrestoreafsinsoftdeletestate--this-restore-point-is-not-available-as-the-snapshot-associated-with-this-point-is-in-a-file-share-that-is-in-soft-deleted-state"></a>UserErrorRestoreAFSInSoftDeleteState – Den här återställningspunkten är inte tillgänglig eftersom ögonblicksbilden som är associerad med den här punkten är i en filresurs som är i läget för mjuk borttagning

Felkod: UserErrorRestoreAFSInSoftDeleteState

Felmeddelande: Den här återställningspunkten är inte tillgänglig eftersom ögonblicksbilden som är associerad med den här punkten är i en filresurs som är i mjuk borttagningstillstånd.

Du kan inte utföra en återställningsåtgärd när filresursen är i läget för mjuk borttagning. Undelete the file share from Files portal or using the [Undelete script](scripts/backup-powershell-script-undelete-file-share.md) and then try to restore.

### <a name="usererrorrestoreafsindeletestate--listed-restore-points-are-not-available-as-the-associated-file-share-containing-the-restore-point-snapshots-has-been-deleted-permanently"></a>UserErrorRestoreAFSInDeleteState – Listade återställningspunkter är inte tillgängliga eftersom den associerade filresursen som innehåller ögonblicksbilderna av återställningspunkten har tagits bort permanent

Felkod: UserErrorRestoreAFSInDeleteState

Felmeddelande: Listade återställningspunkter är inte tillgängliga eftersom den associerade filresursen som innehåller ögonblicksbilderna av återställningspunkten har tagits bort permanent.

Kontrollera om den säkerhetskopierade filresursen har tagits bort. Om den var i läget för mjuk borttagning kontrollerar du om kvarhållningsperioden för mjuk borttagning är över och inte har återställts. I något av dessa fall förlorar du alla dina ögonblicksbilder permanent och kan inte återställa data.

>[!NOTE]
> Vi rekommenderar att du inte tar bort den säkerhetskopierade filresursen, eller om den är i läget för mjuk borttagning, tar bort den innan kvarhållningsperioden för mjuk borttagning upphör för att undvika att alla återställningspunkter går förlorade.

### <a name="usererrorbackupafsinsoftdeletestate---backup-failed-as-the-azure-file-share-is-in-soft-deleted-state"></a>UserErrorBackupAFSInSoftDeleteState – Säkerhetskopieringen misslyckades eftersom Azure-filresursen är i läget för mjuk borttagning

Felkod: UserErrorBackupAFSInSoftDeleteState

Felmeddelande: Säkerhetskopieringen misslyckades eftersom Azure-filresursen är i läget för mjuk borttagning

Ta bort filresursen från **Files-portalen** eller genom att använda skriptet [Undelete](scripts/backup-powershell-script-undelete-file-share.md) för att fortsätta säkerhetskopieringen och förhindra permanent borttagning av data.

### <a name="usererrorbackupafsindeletestate--backup-failed-as-the-associated-azure-file-share-is-permanently-deleted"></a>UserErrorBackupAFSInDeleteState – Säkerhetskopieringen misslyckades eftersom den associerade Azure-filresursen tas bort permanent

Felkod: UserErrorBackupAFSInDeleteState

Felmeddelande: Säkerhetskopieringen misslyckades eftersom den associerade Azure-filresursen tas bort permanent

Kontrollera om den säkerhetskopierade filresursen tas bort permanent. Om ja, stoppa säkerhetskopieringen för filresursen för att undvika upprepade säkerhetskopieringsfel. Information om hur du stoppar skyddet finns [i Stop Protection for Azure file share (Stoppa skydd för Azure-filresurs)](./manage-afs-backup.md#stop-protection-on-a-file-share)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan backa Upp Azure-filresurser finns i:

- [Säkerhetskopiera Azure-filresurser](backup-afs.md)
- [Säkerhetskopiera Azure-filresurser – Vanliga frågor och svar](backup-azure-files-faq.yml)
