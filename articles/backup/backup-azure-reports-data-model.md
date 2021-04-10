---
title: Data modell för Azure Backup Diagnostics-händelser
description: Den här data modellen refererar till det resursbaserade läget för att skicka diagnostiska händelser till Log Analytics (LA).
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 0d75af6d2b41aad0b5f821dd1f6409b30f7ca531
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499602"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Data modell för Azure Backup Diagnostics-händelser

> [!NOTE]
>
> För att skapa anpassade rapportvyer, rekommenderar vi att du använder [system funktioner på Azure Monitor loggar](backup-reports-system-functions.md) i stället för att arbeta med de obearbetade tabellerna i listan nedan.

## <a name="coreazurebackup"></a>CoreAzureBackup

Den här tabellen innehåller information om kärn säkerhets kopiering av entiteter, till exempel valv och säkerhets kopierings objekt.

| **Fält**                         | **Datatyp** | **Beskrivning**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Text          | Resurs-ID för data som samlas in. Till exempel, Recovery Services valv resurs-ID. |
| OperationName                     | Text          | Det här fältet representerar namnet på den aktuella åtgärden-BackupItem, BackupItemAssociation eller ProtectedContainer. |
| Kategori                          | Text          | Det här fältet representerar den kategori av diagnostikdata som skickas till Azure Monitor loggar. Till exempel CoreAzureBackup. |
| Agent version                      | Text          | Versions nummer för agent säkerhets kopiering eller skydds agenten (i händelse av SC DPM och MABS) |
| AzureBackupAgentVersion           | Text          | Versionen av Azure Backup-agenten på säkerhets kopierings hanterings servern |
| AzureDataCenter                   | Text          | Data Center där valvet finns                       |
| BackupItemAppVersion              | Text          | Säkerhets kopierings objektets program version                       |
| BackupItemFriendlyName            | Text          | Eget namn på säkerhets kopierings objekt                             |
| BackupItemName                    | Text          | Namn på det säkerhetskopierade objektet                                      |
| BackupItemProtectionState         | Text          | Skydds status för säkerhets kopierings objekt                          |
| BackupItemFrontEndSize            | Text          | Klient delens storlek på säkerhets kopierings objekt                            |
| BackupItemType                    | Text          | Typ av säkerhets kopierings objekt. Till exempel: VM, FileFolder             |
| BackupItemUniqueId                | Text          | Unikt ID för det säkerhetskopierade objektet                         |
| BackupManagementServerType        | Text          | Typ av säkerhets kopierings hanterings Server, som i MABS, SC DPM     |
| BackupManagementServerUniqueId    | Text          | Fält för att unikt identifiera säkerhets kopierings hanterings servern      |
| BackupManagementType              | Text          | Providertyp för säkerhets kopierings jobbet för servern. Till exempel IaaSVM, FileFolder |
| BackupManagementServerName        | Text          | Namn på säkerhets kopierings hanterings servern                         |
| BackupManagementServerOSVersion   | Text          | OS-version av säkerhets kopierings hanterings servern                   |
| BackupManagementServerVersion     | Text          | Version av säkerhets kopierings hanterings servern                      |
| LatestRecoveryPointLocation       | Text          | Plats för den senaste återställnings punkten för säkerhets kopierings objekt    |
| LatestRecoveryPointTime           | DateTime      | Datum och tid för den senaste återställnings punkten för det säkerhetskopierade objektet   |
| OldestRecoveryPointLocation       | Text          | Plats för den äldsta återställnings punkten för säkerhets kopierings objekt    |
| OldestRecoveryPointTime           | DateTime      | Datum och tid för den senaste återställnings punkten för det säkerhetskopierade objektet   |
| PolicyUniqueId                    | Text          | Unikt ID för att identifiera principen                             |
| ProtectedContainerFriendlyName    | Text          | Eget namn på den skyddade servern                        |
| ProtectedContainerLocation        | Text          | Om den skyddade behållaren finns lokalt eller i Azure |
| ProtectedContainerName            | Text          | Namn på den skyddade behållaren                            |
| ProtectedContainerOSType          | Text          | OS-typ för den skyddade behållaren                          |
| ProtectedContainerOSVersion       | Text          | OS-version av den skyddade behållaren                        |
| ProtectedContainerProtectionState | Text          | Skydds status för den skyddade behållaren                  |
| ProtectedContainerType            | Text          | Om den skyddade behållaren är en server eller en behållare  |
| ProtectedContainerUniqueId        | Text          | Unikt ID som används för att identifiera den skyddade behållaren för allting förutom virtuella datorer som säkerhets kopie ras med DPM, MABS |
| ProtectedContainerWorkloadType    | Text          | Typ av skyddad behållare som säkerhets kopie ras. Till exempel IaaSVMContainer |
| SkyddsgruppNamn               | Text          | Namnet på den skydds grupp som säkerhets kopie posten skyddas i, för SC DPM och MABS, om tillämpligt |
| ResourceGroupName                 | Text          | Resurs grupp för resursen (till exempel Recovery Services valvet) för data som samlas in |
| Schema                     | Text          | Det här fältet anger den aktuella versionen av schemat. Det är **v2** |
| SecondaryBackupProtectionState    | Text          | Om sekundärt skydd har Aktiver ATS för säkerhets kopierings objekt  |
| Tillstånd                             | Text          | Status för objektet säkerhets objekt. Till exempel aktiv, borttagen |
| StorageReplicationType            | Text          | Typ av lagrings replikering för valvet. Till exempel, förredundant |
| SubscriptionId                    | Text          | Prenumerations-ID för resursen (till exempel Recovery Services valv) som data samlas in för |
| VaultName                         | Text          | Namn på valvet                                            |
| VaultTags                         | Text          | Taggar som är associerade med valv resursen                    |
| VaultUniqueId                     | Text          | Unikt ID för valvet                             |
| SourceSystem                      | Text          | Käll system för aktuella data – Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

Den här tabellen innehåller information om aviserings relaterade fält.

| **Fält**                      | **Datatyp** | **Beskrivning**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | Unik identifierare för den resurs som samlar in data. Till exempel ett Recovery Services valv resurs-ID |
| OperationName                  | Text          | Namnet på den aktuella åtgärden. Till exempel avisering            |
| Kategori                       | Text          | Kategori av diagnostikdata som skickas till Azure Monitor loggar – AddonAzureBackupAlerts |
| AlertCode                      | Text          | Kod som unikt identifierar en aviserings typ                     |
| AlertConsolidationStatus       | Text          | Identifiera om aviseringen är en konsol IDE rad avisering eller inte         |
| AlertOccurrenceDateTime        | DateTime      | Datum och tid då aviseringen skapades                     |
| AlertRaisedOn                  | Text          | Typ av entitet som aviseringen utlöses på                        |
| AlertSeverity                  | Text          | Aviseringens allvarlighets grad. Till exempel kritiskt                 |
| AlertStatus                    | Text          | Aviseringens status. Till exempel Active                     |
| AlertTimeToResolveInMinutes    | Antal        | Åtgången tid för att lösa en avisering. Tomt för aktiva aviseringar.     |
| AlertType                      | Text          | Typ av avisering. Till exempel säkerhets kopiering                           |
| AlertUniqueId                  | Text          | Unik identifierare för den genererade aviseringen                    |
| BackupItemUniqueId             | Text          | Unik identifierare för det säkerhets kopierings objekt som är associerat med aviseringen |
| BackupManagementServerUniqueId | Text          | Fält för att unikt identifiera säkerhets kopierings hanterings servern som objektet skyddas med, om tillämpligt |
| BackupManagementType           | Text          | Providertyp för server som utför säkerhets kopierings jobb, till exempel IaaSVM, FileFolder |
| CountOfAlertsConsolidated      | Antal        | Antal aviseringar som konsol IDE ras om det är en konsol IDE rad avisering  |
| ProtectedContainerUniqueId     | Text          | Unik identifierare för den skyddade servern som är associerad med aviseringen |
| RecommendedAction              | Text          | Åtgärd som rekommenderas för att lösa aviseringen                      |
| Schema                  | Text          | Aktuell version av schemat, till exempel **v2**            |
| Tillstånd                          | Text          | Aktuellt tillstånd för aviserings objekt, t. ex. aktiv, borttagen |
| StorageUniqueId                | Text          | Unikt ID som används för att identifiera lagrings enheten                |
| VaultUniqueId                  | Text          | Unikt ID som används för att identifiera valvet som hör till aviseringen    |
| SourceSystem                   | Text          | Käll system för aktuella data – Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

Den här tabellen innehåller grundläggande skyddade instans fält.

| **Fält**                      | **Datatyp** | **Beskrivning**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | Unik identifierare för den resurs som samlar in data. Till exempel ett Recovery Services valv resurs-ID |
| OperationName                  | Text          | Åtgärdens namn, till exempel ProtectedInstance         |
| Kategori                       | Text          | Kategori av diagnostikdata som skickas till Azure Monitor loggar – AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | Text          | Unikt ID för det säkerhetskopierade objektet                                 |
| BackupManagementServerUniqueId | Text          | Fält för att unikt identifiera säkerhets kopierings hanterings servern som objektet skyddas med, om tillämpligt |
| BackupManagementType           | Text          | Providertyp för server som utför säkerhets kopierings jobb, till exempel IaaSVM, FileFolder |
| ProtectedContainerUniqueId     | Text          | Unikt ID för att identifiera den skyddade behållare som jobbet körs på |
| ProtectedInstanceCount         | Text          | Antal skyddade instanser för det associerade säkerhets kopierings objektet eller skyddade behållaren den datum/tid |
| Schema                  | Text          | Aktuell version av schemat, till exempel **v2**            |
| Tillstånd                          | Text          | Status för objektet säkerhets kopiering, till exempel aktiv, borttagen |
| VaultUniqueId                  | Text          | Unikt ID för det skyddade valvet som är associerat med den skyddade instansen |
| SourceSystem                   | Text          | Käll system för aktuella data – Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

Den här tabellen innehåller information om projektrelaterade fält.

| **Fält**                      | **Datatyp** | **Beskrivning**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | Resurs-ID för data som samlas in. Till exempel, Recovery Services valv resurs-ID |
| OperationName                  | Text          | Det här fältet representerar namnet på den aktuella åtgärden – jobb    |
| Kategori                       | Text          | Det här fältet representerar kategori av diagnostikdata som skickas till Azure Monitor loggar – AddonAzureBackupJobs |
| AdhocOrScheduledJob            | Text          | Fält för att ange om jobbet är ad hoc eller schemalagt           |
| BackupItemUniqueId             | Text          | Unikt ID som används för att identifiera det säkerhets kopierings objekt som är relaterat till lagrings enheten |
| BackupManagementServerUniqueId | Text          | Unikt ID som används för att identifiera säkerhets kopierings hanterings servern som är relaterad till lagrings enheten |
| BackupManagementType           | Text          | Typ av Provider för säkerhets kopiering, till exempel IaaSVM, FileFolder som jobbet tillhör |
| DataTransferredInMB            | Antal        | Överförda data i MB för det här jobbet                          |
| JobDurationInSecs              | Antal        | Total jobb varaktighet i sekunder                                |
| JobFailureCode                 | Text          | Kod sträng för felkod på grund av vilket jobbfel som skett    |
| JobOperation                   | Text          | Åtgärd för vilket jobb som körs, till exempel säkerhets kopiering, återställning, konfigurera säkerhets kopiering |
| JobOperationSubType            | Text          | Undertyp för jobb åtgärden. Till exempel "log", i händelse av logg säkerhets kopierings jobb |
| JobStartDateTime               | DateTime      | Datum och tid då jobbet började köras                       |
| JobStatus                      | Text          | Status för det slutförda jobbet, till exempel slutförd, misslyckades   |
| JobUniqueId                    | Text          | Unikt ID för att identifiera jobbet                                |
| ProtectedContainerUniqueId     | Text          | Unik identifierare för den skyddade servern som är kopplad till jobbet |
| RecoveryJobDestination         | Text          | Målet för ett återställnings jobb, där data återställs   |
| RecoveryJobRPDateTime          | DateTime      | Datum, tid då återställnings punkten som återställdes skapades |
| RecoveryJobLocation            | Text          | Platsen där återställnings punkten som återställdes har lagrats |
| RecoveryLocationType           | Text          | Typ av återställnings plats                                |
| Schema                  | Text          | Aktuell version av schemat, till exempel **v2**            |
| Tillstånd                          | Text          | Aktuellt tillstånd för jobbobjektet, till exempel aktiv, borttagen |
| VaultUniqueId                  | Text          | Unikt ID för det skyddade valvet som är associerat med jobbet |
| SourceSystem                   | Text          | Käll system för aktuella data – Azure                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

Den här tabellen innehåller information om principbaserad fält.

| **Fält**                       | **Datatyp**  | **Beskrivning**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Text           | Unik identifierare för den resurs som samlar in data. Till exempel ett Recovery Services valv resurs-ID |
| OperationName                   | Text           | Namnet på åtgärden, till exempel princip eller PolicyAssociation |
| Kategori                        | Text           | Kategori av diagnostikdata som skickas till Azure Monitor loggar – AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | Text           | Vecko dagar när säkerhets kopieringar har schemalagts            |
| BackupFrequency                 | Text           | Den frekvens med vilken säkerhets kopieringar körs. Till exempel varje dag, varje vecka |
| BackupManagementType            | Text           | Providertyp för säkerhets kopierings jobbet för servern. Till exempel IaaSVM, FileFolder |
| BackupManagementServerUniqueId  | Text           | Fält för att unikt identifiera säkerhets kopierings hanterings servern som objektet skyddas med, om tillämpligt |
| BackupTimes                     | Text           | Datum och tid när säkerhets kopieringar schemaläggs                     |
| DailyRetentionDuration          | Heltal   | Total Retentions tid i dagar för konfigurerade säkerhets kopieringar      |
| DailyRetentionTimes             | Text           | Datum och tid när daglig kvarhållning hade kon figurer ATS            |
| DiffBackupDaysOfTheWeek         | Text           | Vecko dagar för differentiella säkerhets kopieringar för SQL i Azure VM-säkerhetskopiering |
| DiffBackupFormat                | Text           | Format för differentiella säkerhets kopieringar för SQL i VM-säkerhetskopiering i Azure   |
| DiffBackupRetentionDuration     | Decimaltal | Retentions tid för differentiella säkerhets kopieringar för SQL i virtuell Azure-säkerhetskopiering |
| DiffBackupTime                  | Tid           | Tid för differentiella säkerhets kopieringar för SQL i VM-säkerhetskopiering i Azure     |
| LogBackupFrequency              | Decimaltal | Frekvens för säkerhets kopiering av loggar för SQL                            |
| LogBackupRetentionDuration      | Decimaltal | Retentions tid för logg säkerhets kopior för SQL i Azure VM-säkerhetskopiering |
| MonthlyRetentionDaysOfTheMonth  | Text           | Veckor i månaden då månatlig kvarhållning har kon figurer ATS.  Till exempel första, sista |
| MonthlyRetentionDaysOfTheWeek   | Text           | Vecko dagar som valts för månatlig kvarhållning              |
| MonthlyRetentionDuration        | Text           | Total Retentions tid i månader för konfigurerade säkerhets kopieringar    |
| MonthlyRetentionFormat          | Text           | Typ av konfiguration för månatlig kvarhållning. Till exempel varje dag för dag baserat, varje vecka i veckan baserat |
| MonthlyRetentionTimes           | Text           | Datum och tid när månatlig kvarhållning har kon figurer ATS           |
| MonthlyRetentionWeeksOfTheMonth | Text           | Veckor i månaden då månatlig kvarhållning har kon figurer ATS.   Till exempel första, sista |
| PolicyName                      | Text           | Namn på den princip som definierats                                   |
| PolicyUniqueId                  | Text           | Unikt ID för att identifiera principen                             |
| PolicyTimeZone                  | Text           | Tidszon där fälten för princip tid anges i loggarna |
| RetentionDuration               | Text           | Retentions tid för konfigurerade säkerhets kopior                    |
| RetentionType                   | Text           | Typ av kvarhållning                                            |
| Schema                   | Text           | Det här fältet anger den aktuella versionen av schemat och är **v2** |
| Tillstånd                           | Text           | Princip objektets aktuella tillstånd. Till exempel aktiv, borttagen |
| SynchronisationFrequencyPerDay  | Heltal   | Antal gånger under en dag som en fil säkerhets kopia synkroniseras för SC DPM och MABS |
| VaultUniqueId                   | Text           | Unikt ID för valvet som den här principen tillhör          |
| WeeklyRetentionDaysOfTheWeek    | Text           | Vecko dagar som valts för veckovis kvarhållning               |
| WeeklyRetentionDuration         | Decimaltal | Total varaktighet för veckovis kvarhållning i veckor för konfigurerade säkerhets kopieringar |
| WeeklyRetentionTimes            | Text           | Datum och tid när veckovis kvarhållning har kon figurer ATS            |
| YearlyRetentionDaysOfTheMonth   | Text           | Datum för den månad som valts för årlig kvarhållning             |
| YearlyRetentionDaysOfTheWeek    | Text           | Vecko dagar som valts för årlig kvarhållning               |
| YearlyRetentionDuration         | Decimaltal | Total Retentions tid i år för konfigurerade säkerhets kopieringar     |
| YearlyRetentionFormat           | Text           | Typ av konfiguration för årlig kvarhållning, till exempel varje dag för dag baserat, veckovis för vecka, baserat |
| YearlyRetentionMonthsOfTheYear  | Text           | Månader under året som valts för årlig kvarhållning             |
| YearlyRetentionTimes            | Text           | Datum och tid när årlig kvarhållning har kon figurer ATS            |
| YearlyRetentionWeeksOfTheMonth  | Text           | Veckor i månaden som valts för årlig kvarhållning             |
| SourceSystem                    | Text           | Käll system för aktuella data – Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

Den här tabellen innehåller information om Storage-relaterade fält.

| **Fält**                      | **Datatyp** | **Beskrivning**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | Resurs-ID för data som samlas in. Till exempel, Recovery Services valv resurs-ID |
| OperationName                  | Text          | Det här fältet representerar namnet på den aktuella åtgärden lagrings-eller StorageAssociation |
| Kategori                       | Text          | Det här fältet representerar kategori av diagnostikdata som skickas till Azure Monitor loggar – AddonAzureBackupStorage |
| BackupItemUniqueId             | Text          | Unikt ID som används för att identifiera säkerhets kopierings objekt för virtuella datorer som säkerhets kopie ras med DPM, MABS |
| BackupManagementServerUniqueId | Text          | Fält för att unikt identifiera säkerhets kopierings hanterings servern som objektet skyddas med, om tillämpligt |
| BackupManagementType           | Text          | Providertyp för säkerhets kopierings jobbet för servern. Till exempel IaaSVM, FileFolder |
| PreferredWorkloadOnVolume      | Text          | Arbets belastning för vilken den här volymen är den prioriterade lagringen      |
| ProtectedContainerUniqueId     | Text          | Unik identifierare för den skyddade behållare som är associerad med säkerhets kopierings objekt |
| Schema                  | Text          | Schemats version. Till exempel **v2**                   |
| Tillstånd                          | Text          | Status för objektet säkerhets objekt. Till exempel aktiv, borttagen |
| StorageAllocatedInMBs          | Antal        | Storleken på det lagrings utrymme som allokerats av motsvarande säkerhets kopierings objekt i motsvarande lagring av typ disk |
| StorageConsumedInMBs           | Antal        | Storlek på lagring som förbrukas av motsvarande säkerhets kopierings objekt i motsvarande lagrings utrymme |
| StorageName                    | Text          | Namn på lagrings enhet. Till exempel E:\                      |
| StorageTotalSizeInGBs          | Text          | Total lagrings utrymme, i GB, som används av lagrings enheten     |
| StorageType                    | Text          | Typ av lagring, till exempel moln, volym, disk             |
| StorageUniqueId                | Text          | Unikt ID som används för att identifiera lagrings enheten                |
| VaultUniqueId                  | Text          | Unikt ID som används för att identifiera valvet som hör till lagrings enheten |
| VolumeFriendlyName             | Text          | Eget namn på lagrings volymen                          |
| SourceSystem                   | Text          | Käll system för aktuella data – Azure                    |

## <a name="valid-operation-names-for-each-table"></a>Giltiga åtgärds namn för varje tabell

Varje post i tabellen ovan har ett associerat **Åtgärds namn**. Ett åtgärds namn beskriver typen av post (och anger även vilka fält i tabellen som är ifyllda för posten). Varje tabell (kategori) stöder ett eller flera distinkta åtgärds namn. Nedan visas en sammanfattning av de åtgärds namn som stöds för var och en av ovanstående tabeller.

| **Tabell namn/kategori**                   | **Åtgärds namn som stöds** | **Beskrivning**              |
| ------------------------------------------- | ------------------------------|----------------------------- |
| CoreAzureBackup | BackupItem | Representerar en post som innehåller all information om ett angivet säkerhets kopierings objekt, till exempel ID, namn, typ osv. |
| CoreAzureBackup | BackupItemAssociation | Representerar en mappning mellan ett säkerhets kopierings objekt och dess associerade skyddade behållare (om tillämpligt). |
| CoreAzureBackup | BackupItemFrontEndSizeConsumption | Representerar en mappning mellan ett säkerhets kopierings objekt och dess frontend-storlek. |
| CoreAzureBackup | ProtectedContainer | Representerar en post som innehåller all information om en specifik skyddad behållare, till exempel ID, namn, typ osv. |
| CoreAzureBackup | ProtectedContainerAssociation | Representerar en mappning mellan en skyddad behållare och valvet som används för säkerhets kopieringen. |
| CoreAzureBackup | Valv | Representerar en post som innehåller all information om ett angivet valv tex. ID, namn, taggar, plats osv. |
| CoreAzureBackup | RecoveryPoint | Representerar en post som innehåller den äldsta och senaste återställnings punkten för ett angivet säkerhets kopierings objekt. |
| AddonAzureBackupJobs | Jobb |  Representerar en post som innehåller all information om ett specifikt jobb. Till exempel jobb åtgärd, start tid, status osv. |
| AddonAzureBackupAlerts | Varning | Representerar en post som innehåller all information om en specifik avisering. Till exempel tid för att skapa aviseringar, allvarlighets grad, status osv.  |
| AddonAzureBackupStorage | Storage | Representerar en post som innehåller all information om en specifik lagrings enhet. Till exempel lagrings namn, typ osv. |
| AddonAzureBackupStorage | StorageAssociation | Representerar en mappning mellan ett säkerhets kopierings objekt och den totala moln lagring som förbrukas av säkerhets kopierings objekt. |
| AddonAzureBackupProtectedInstance | ProtectedInstance | Representerar en post som innehåller antalet skyddade instanser för varje behållare eller säkerhets kopierings objekt. För säkerhets kopiering av virtuella Azure-datorer är antalet skyddade instanser tillgängligt på säkerhets kopierings objekt nivå för andra arbets belastningar på den skyddade container nivån. |
| AddonAzureBackupPolicy | Policy |  Representerar en post som innehåller all information om en säkerhets kopierings-och bevarande princip. Till exempel ID, namn, inställningar för kvarhållning osv. |
| AddonAzureBackupPolicy | PolicyAssociation | Representerar en mappning mellan ett säkerhets kopierings objekt och den säkerhets kopierings princip som tillämpas på den. |   

Ofta måste du utföra kopplingar mellan olika tabeller och olika uppsättningar med poster som ingår i samma tabell (differentierade med åtgärds namn) för att hämta alla fält som krävs för din analys. Kom igång genom att läsa [exempel frågorna](./backup-azure-monitoring-use-azuremonitor.md#sample-kusto-queries) . 

## <a name="next-steps"></a>Nästa steg

- [Lär dig hur du skickar diagnostikdata till Log Analytics](./backup-azure-diagnostic-events.md)
- [Lär dig hur du skriver frågor för resursbaserade tabeller](./backup-azure-monitoring-use-azuremonitor.md#sample-kusto-queries)