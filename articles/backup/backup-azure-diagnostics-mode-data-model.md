---
title: Azure Monitor loggar data modell
description: I den här artikeln lär du dig mer om Azure Monitor Log Analytics data modell information för Azure Backup data.
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 2d2146612763bac39eb636bb7da522586a0daee2
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586774"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Log Analytics data modell för Azure Backup data

Använd Log Analytics data modell för att skapa anpassade aviseringar från Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> Den här data modellen refererar till Azure-diagnostik läge för att skicka diagnostiska händelser till Log Analytics (LA). Om du vill lära dig data modellen för det nya resurs läget kan du läsa följande artikel: [data modell för Azure Backup diagnostiska händelser](./backup-azure-reports-data-model.md)

## <a name="using-azure-backup-data-model"></a>Använda Azure Backup data modell

Du kan använda följande fält som ingår i data modellen för att skapa visuella objekt, anpassade frågor och instrument paneler enligt dina krav.

### <a name="alert"></a>Varning

Den här tabellen innehåller information om aviserings relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| AlertUniqueId_s |Text |Unik identifierare för den genererade aviseringen |
| AlertType_s |Text |Typ av avisering, till exempel säkerhets kopiering |
| AlertStatus_s |Text |Status för aviseringen, till exempel aktiv |
| AlertOccurrenceDateTime_s |Datum/tid |Datum och tid då aviseringen skapades |
| AlertSeverity_s |Text |Allvarlighets grad för aviseringen, till exempel kritisk |
|AlertTimeToResolveInMinutes_s    | Antal        |Åtgången tid för att lösa en avisering. Tomt för aktiva aviseringar.         |
|AlertConsolidationStatus_s   |Text         |Identifiera om aviseringen är en konsol IDE rad avisering eller inte         |
|CountOfAlertsConsolidated_s     |Antal         |Antal aviseringar som konsol IDE ras om det är en konsol IDE rad avisering          |
|AlertRaisedOn_s     |Text         |Typ av entitet som aviseringen utlöses på         |
|AlertCode_s     |Text         |Kod som unikt identifierar en aviserings typ         |
|RecommendedAction_s   |Text         |Åtgärd som rekommenderas för att lösa aviseringen         |
| EventName_s |Text |Händelsens namn. Alltid AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Unik identifierare för det säkerhets kopierings objekt som är associerat med aviseringen |
| SchemaVersion_s |Text |Aktuell version av schemat, till exempel **v2** |
| State_s |Text |Aktuellt tillstånd för aviserings objekt, t. ex. aktiv, borttagen |
| BackupManagementType_s |Text |Providertyp för säkerhets kopiering, till exempel IaaSVM, FileFolder som aviseringen tillhör |
| OperationName |Text |Namnet på den aktuella åtgärden, till exempel avisering |
| Kategori |Text |Kategori av diagnostikdata som skickas till Azure Monitor loggar. Alltid AzureBackupReport |
| Resurs |Text |Detta är den resurs för vilken data samlas in, och det visar Recovery Services valv namn |
| ProtectedContainerUniqueId_s |Text |Unik identifierare för den skyddade servern som är associerad med aviseringen (was ProtectedServerUniqueId_s i v1)|
| VaultUniqueId_s |Text |Unikt ID för det skyddade valvet som är associerat med aviseringen |
| SourceSystem |Text |Käll system för aktuella data – Azure |
| ResourceId |Text |Unik identifierare för den resurs som samlar in data. Till exempel ett Recovery Services valv resurs-ID |
| SubscriptionId |Text |Prenumerations-ID för resursen (t. ex. Recovery Services valv) som data samlas in för |
| ResourceGroup |Text |Resurs grupp för resursen (t. ex. Recovery Services valv) som data samlas in för |
| ResourceProvider |Text |Resurs leverantör för vilken data samlas in. Till exempel Microsoft. RecoveryServices |
| ResourceType |Text |Resurs typ som data samlas in för. Till exempel valv |

### <a name="backupitem"></a>BackupItem

Den här tabellen innehåller information om säkerhets kopiering av objekt-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |Text |Händelsens namn. Alltid AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Unikt ID för det säkerhetskopierade objektet |
| BackupItemId_s |Text |Identifierare för säkerhets kopierings objekt (det här fältet är endast för v1-schema) |
| BackupItemName_s |Text |Namn på säkerhets kopierings objekt |
| BackupItemFriendlyName_s |Text |Eget namn på säkerhets kopierings objekt |
| BackupItemType_s |Text |Typ av säkerhets kopierings objekt, till exempel virtuell dator, FileFolder |
| BackupItemProtectionState_s |Text |Skydds status för säkerhets kopierings objekt |
| BackupItemAppVersion_s |Text |Säkerhets kopierings objektets program version |
| ProtectionState_s |Text |Aktuellt skydds tillstånd för säkerhets kopierings objekt, till exempel skyddat, ProtectionStopped |
| ProtectionGroupName_s |Text | Namnet på den skydds grupp som säkerhets kopie posten skyddas i, för SC DPM och MABS, om tillämpligt|
| SecondaryBackupProtectionState_s |Text |Om sekundärt skydd har Aktiver ATS för säkerhets kopierings objekt|
| SchemaVersion_s |Text |Version av schemat, till exempel **v2** |
| State_s |Text |Status för objektet säkerhets kopiering, till exempel aktiv, borttagen |
| BackupManagementType_s |Text |Providertyp för säkerhets kopiering, till exempel IaaSVM, FileFolder som det här säkerhetskopierade objektet tillhör |
| OperationName |Text |Namnet på åtgärden, till exempel BackupItem |
| Kategori |Text |Kategori av diagnostikdata som skickas till Azure Monitor loggar. Alltid AzureBackupReport |
| Resurs |Text |Resurs för vilken data samlas in, till exempel Recovery Services valv namn |
| SourceSystem |Text |Käll system för aktuella data – Azure |
| ResourceId |Text |Resurs-ID för data som samlas in, till exempel Recovery Services valv resurs-ID |
| SubscriptionId |Text |Prenumerations-ID för resursen (t. ex. Recovery Services valv) för data som samlas in |
| ResourceGroup |Text |Resurs grupp för resursen (t. ex. Recovery Services valv) för data som samlas in |
| ResourceProvider |Text |Resurs leverantör för data som samlas in, till exempel Microsoft. RecoveryServices |
| ResourceType |Text |Typ av resurs för data som samlas in, till exempel valv |

### <a name="backupitemassociation"></a>BackupItemAssociation

Den här tabellen innehåller information om säkerhets kopierings objekts associationer med olika entiteter.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |Text |Det här fältet representerar namnet på den här händelsen. Det är alltid AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Unikt ID för det säkerhetskopierade objektet |
| SchemaVersion_s |Text |Det här fältet anger den aktuella versionen av schemat. Det är **v2** |
| State_s |Text |Aktuellt tillstånd för objektet för säkerhets kopierings objekt, till exempel aktivt, borttaget |
| BackupManagementType_s |Text |Providertyp för server som utför säkerhets kopierings jobb, till exempel IaaSVM, FileFolder |
| BackupItemSourceSize_s |Text | Klient delens storlek på säkerhets kopierings objekt |
| BackupManagementServerUniqueId_s |Text | Fält för att unikt identifiera säkerhets kopierings hanterings servern som objektet skyddas med, om tillämpligt |
| Kategori |Text |Det här fältet representerar en kategori av diagnostikdata som skickas till Log Analytics. Det är AzureBackupReport |
| OperationName |Text |Det här fältet representerar namnet på den aktuella åtgärden – BackupItemAssociation |
| Resurs |Text |Detta är den resurs för vilken data samlas in, och det visar Recovery Services valv namn |
| ProtectedContainerUniqueId_s |Text |Unik identifierare för den skyddade server som är associerad med säkerhets kopierings objekt (was ProtectedServerUniqueId_s i v1) |
| VaultUniqueId_s |Text |Unikt ID för valvet som innehåller det säkerhetskopierade objektet |
| SourceSystem |Text |Käll system för aktuella data – Azure |
| ResourceId |Text |Resurs-ID för data som samlas in. Till exempel, Recovery Services valv resurs-ID |
| SubscriptionId |Text |Prenumerations-ID för resursen (t. ex. Recovery Services valv) som data samlas in för |
| ResourceGroup |Text |Resurs grupp för resursen (t. ex. Recovery Services valv) som data samlas in för |
| ResourceProvider |Text |Resurs leverantör för data som samlas in, till exempel Microsoft. RecoveryServices |
| ResourceType |Text |Typ av resurs för data samlas in, till exempel valv |

### <a name="backupmanagementserver"></a>BackupManagementServer

Den här tabellen innehåller information om säkerhets kopierings objekts associationer med olika entiteter.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |Namn på säkerhets kopierings hanterings servern        |
|AzureBackupAgentVersion_s     |Text         |Versionen av Azure Backup-agenten på säkerhets kopierings hanterings servern          |
|BackupManagementServerVersion_s     |Text         |Version av säkerhets kopierings hanterings servern|
|BackupManagementServerOSVersion_s    |Text            |OS-version av säkerhets kopierings hanterings servern|
|BackupManagementServerType_s     |Text         |Typ av säkerhets kopierings hanterings Server, som MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Text         |Fält för att unikt identifiera säkerhets kopierings hanterings servern       |
| SourceSystem |Text |Käll system för aktuella data – Azure |
| ResourceId |Text |Resurs-ID för data som samlas in. Till exempel, Recovery Services valv resurs-ID |
| SubscriptionId |Text |Prenumerations-ID för resursen (t. ex. Recovery Services valv) som data samlas in för |
| ResourceGroup |Text |Resurs grupp för resursen (t. ex. Recovery Services valv) som data samlas in för |
| ResourceProvider |Text |Resurs leverantör för data som samlas in, till exempel Microsoft. RecoveryServices |
| ResourceType |Text |Typ av resurs för data samlas in, till exempel valv |

### <a name="job"></a>Jobb

Den här tabellen innehåller information om projektrelaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |Text |Händelsens namn. Alltid AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Unikt ID för det säkerhetskopierade objektet |
| SchemaVersion_s |Text |Version av schemat, till exempel **v2** |
| State_s |Text |Aktuellt tillstånd för jobbobjektet, till exempel aktiv, borttagen |
| BackupManagementType_s |Text |Providertyp för server som utför säkerhets kopierings jobb, till exempel IaaSVM, FileFolder |
| OperationName |Text |Det här fältet representerar namnet på den aktuella åtgärden – jobb |
| Kategori |Text |Det här fältet representerar en kategori av diagnostikdata som skickas till Azure Monitor loggar. Det är AzureBackupReport |
| Resurs |Text |Detta är den resurs för vilken data samlas in, och det visar Recovery Services valv namn |
| ProtectedServerUniqueId_s |Text |Unik identifierare för den skyddade servern som är kopplad till jobbet |
| ProtectedContainerUniqueId_s |Text | Unikt ID för att identifiera den skyddade behållare som jobbet körs på |
| VaultUniqueId_s |Text |Unikt ID för det skyddade valvet |
| JobOperation_s |Text |Åtgärd för vilket jobb som körs, till exempel säkerhets kopiering, återställning, konfigurera säkerhets kopiering |
| JobStatus_s |Text |Status för det slutförda jobbet, till exempel slutförd, misslyckades |
| JobFailureCode_s |Text |Kod sträng för felkod på grund av vilket jobbfel som skett |
| JobStartDateTime_s |Datum/tid |Datum och tid då jobbet började köras |
| BackupStorageDestination_s |Text |Mål för lagring av säkerhets kopior, till exempel moln, disk  |
| AdHocOrScheduledJob_s |Text | Fält för att ange om jobbet är ad hoc eller schemalagt |
| JobDurationInSecs_s | Antal |Total jobb varaktighet i sekunder |
| DataTransferredInMB_s | Antal |Överförda data i MB för det här jobbet|
| JobUniqueId_g |Text |Unikt ID för att identifiera jobbet |
| RecoveryJobDestination_s |Text | Målet för ett återställnings jobb, där data återställs |
| RecoveryJobRPDateTime_s |DateTime | Datum, tid då återställnings punkten som återställdes skapades |
| RecoveryJobRPLocation_s |Text | Platsen där återställnings punkten som återställdes har lagrats|
| SourceSystem |Text |Käll system för aktuella data – Azure |
| ResourceId |Text |Resurs-ID för data som samlas in. Till exempel, Recovery Services valv resurs-ID|
| SubscriptionId |Text |Prenumerations-ID för resursen (t. ex. Recovery Services valv) som data samlas in för |
| ResourceGroup |Text |Resurs grupp för resursen (t. ex. Recovery Services valv) som data samlas in för |
| ResourceProvider |Text |Resurs leverantör för vilken data samlas in. Till exempel Microsoft. RecoveryServices |
| ResourceType |Text |Resurs typ som data samlas in för. Till exempel valv |

### <a name="policy"></a>Policy

Den här tabellen innehåller information om principbaserad fält.

| Fält | Datatyp | Versioner som gäller | Description |
| --- | --- | --- | --- |
| EventName_s |Text ||Det här fältet representerar namnet på den här händelsen. Det är alltid AzureBackupCentralReport |
| SchemaVersion_s |Text ||Det här fältet anger den aktuella versionen av schemat. Det är **v2** |
| State_s |Text ||Aktuellt tillstånd för principobjektet, till exempel aktiv, borttagen |
| BackupManagementType_s |Text ||Providertyp för server som utför säkerhets kopierings jobb, till exempel IaaSVM, FileFolder |
| OperationName |Text ||Det här fältet representerar namnet på den aktuella åtgärden – princip |
| Kategori |Text ||Det här fältet representerar en kategori av diagnostikdata som skickas till Azure Monitor loggar. Det är AzureBackupReport |
| Resurs |Text ||Detta är den resurs för vilken data samlas in, och det visar Recovery Services valv namn |
| PolicyUniqueId_g |Text ||Unikt ID för att identifiera principen |
| PolicyName_s |Text ||Namn på den princip som definierats |
| BackupFrequency_s |Text ||Den frekvens med vilken säkerhets kopieringar körs, till exempel varje dag, varje vecka |
| BackupTimes_s |Text ||Datum och tid när säkerhets kopieringar schemaläggs |
| BackupDaysOfTheWeek_s |Text ||Vecko dagar när säkerhets kopieringar har schemalagts |
| RetentionDuration_s |Heltal ||Retentions tid för konfigurerade säkerhets kopior |
| DailyRetentionDuration_s |Heltal ||Total Retentions tid i dagar för konfigurerade säkerhets kopieringar |
| DailyRetentionTimes_s |Text ||Datum och tid när daglig kvarhållning hade kon figurer ATS |
| WeeklyRetentionDuration_s |Decimaltal ||Total varaktighet för veckovis kvarhållning i veckor för konfigurerade säkerhets kopieringar |
| WeeklyRetentionTimes_s |Text ||Datum och tid när veckovis kvarhållning har kon figurer ATS |
| WeeklyRetentionDaysOfTheWeek_s |Text ||Vecko dagar som valts för veckovis kvarhållning |
| MonthlyRetentionDuration_s |Decimaltal ||Total Retentions tid i månader för konfigurerade säkerhets kopieringar |
| MonthlyRetentionTimes_s |Text ||Datum och tid när månatlig kvarhållning har kon figurer ATS |
| MonthlyRetentionFormat_s |Text ||Typ av konfiguration för månatlig kvarhållning, till exempel varje dag för dag baserat, veckovis för vecka, baserat |
| MonthlyRetentionDaysOfTheWeek_s |Text ||Vecko dagar som valts för månatlig kvarhållning |
| MonthlyRetentionWeeksOfTheMonth_s |Text ||Veckor i månaden då månatlig kvarhållning konfigureras, till exempel första, senaste |
| YearlyRetentionDuration_s |Decimaltal ||Total Retentions tid i år för konfigurerade säkerhets kopieringar |
| YearlyRetentionTimes_s |Text ||Datum och tid när årlig kvarhållning har kon figurer ATS |
| YearlyRetentionMonthsOfTheYear_s |Text ||Månader under året som valts för årlig kvarhållning |
| YearlyRetentionFormat_s |Text ||Typ av konfiguration för årlig kvarhållning, till exempel varje dag för dag baserat, veckovis för vecka, baserat | |
| YearlyRetentionDaysOfTheMonth_s |Text ||Datum för den månad som valts för årlig kvarhållning |
| SynchronisationFrequencyPerDay_s |Heltal |v2|Antal gånger under en dag som en fil säkerhets kopia synkroniseras för SC DPM och MABS |
| DiffBackupFormat_s |Text |v2|Format för differentiella säkerhets kopieringar för SQL i VM-säkerhetskopiering i Azure |
| DiffBackupTime_s |Tid |v2|Tid för differentiella säkerhets kopieringar för SQL i VM-säkerhetskopiering i Azure|
| DiffBackupRetentionDuration_s |Decimaltal |v2|Retentions tid för differentiella säkerhets kopieringar för SQL i virtuell Azure-säkerhetskopiering|
| LogBackupFrequency_s |Decimaltal |v2|Frekvens för säkerhets kopiering av loggar för SQL|
| LogBackupRetentionDuration_s |Decimaltal |v2|Retentions tid för logg säkerhets kopior för SQL i Azure VM-säkerhetskopiering|
| DiffBackupDaysofTheWeek_s |Text |v2|Vecko dagar för differentiella säkerhets kopieringar för SQL i Azure VM-säkerhetskopiering|
| SourceSystem |Text ||Käll system för aktuella data – Azure |
| ResourceId |Text ||Resurs-ID för data som samlas in. Till exempel, Recovery Services valv resurs-ID |
| SubscriptionId |Text ||Prenumerations-ID för resursen (t. ex. Recovery Services valv) som data samlas in för |
| ResourceGroup |Text ||Resurs grupp för resursen (t. ex. Recovery Services valv) som data samlas in för |
| ResourceProvider |Text ||Resurs leverantör för vilken data samlas in. Till exempel Microsoft. RecoveryServices |
| ResourceType |Text ||Resurs typ som data samlas in för. Till exempel valv |

### <a name="policyassociation"></a>PolicyAssociation

Den här tabellen innehåller information om princip kopplingar med olika entiteter.

| Fält | Datatyp | Versioner som gäller | Description |
| --- | --- | --- | --- |
| EventName_s |Text ||Det här fältet representerar namnet på den här händelsen. Det är alltid AzureBackupCentralReport |
| SchemaVersion_s |Text ||Det här fältet anger den aktuella versionen av schemat. Det är **v2** |
| State_s |Text ||Aktuellt tillstånd för principobjektet, till exempel aktiv, borttagen |
| BackupManagementType_s |Text ||Providertyp för server som utför säkerhets kopierings jobb, till exempel IaaSVM, FileFolder |
| OperationName |Text ||Det här fältet representerar namnet på den aktuella åtgärden – PolicyAssociation |
| Kategori |Text ||Det här fältet representerar en kategori av diagnostikdata som skickas till Azure Monitor loggar. Det är AzureBackupReport |
| Resurs |Text ||Detta är den resurs för vilken data samlas in, och det visar Recovery Services valv namn |
| PolicyUniqueId_g |Text ||Unikt ID för att identifiera principen |
| VaultUniqueId_s |Text ||Unikt ID för valvet som den här principen tillhör |
| BackupManagementServerUniqueId_s |Text |v2 |Fält för att unikt identifiera säkerhets kopierings hanterings servern som objektet skyddas med, om tillämpligt        |
| SourceSystem |Text ||Käll system för aktuella data – Azure |
| ResourceId |Text ||Resurs-ID för data som samlas in. Till exempel, Recovery Services valv resurs-ID |
| SubscriptionId |Text ||Prenumerations-ID för resursen (t. ex. Recovery Services valv) som data samlas in för |
| ResourceGroup |Text ||Resurs grupp för resursen (t. ex. Recovery Services valv) som data samlas in för |
| ResourceProvider |Text ||Resurs leverantör för vilken data samlas in. Till exempel Microsoft. RecoveryServices |
| ResourceType |Text ||Resurs typ som data samlas in för. Till exempel valv |

### <a name="protected-container"></a>Skyddad behållare

Den här tabellen innehåller grundläggande fält om skyddade behållare. (Was ProtectedServer i v1)

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | Fält som unikt identifierar en skyddad behållare |
| ProtectedContainerOSType_s |Text |OS-typ för den skyddade behållaren |
| ProtectedContainerOSVersion_s |Text |OS-version av den skyddade behållaren |
| AgentVersion_s |Text |Versions nummer för agent säkerhets kopiering eller skydds agenten (i händelse av SC DPM och MABS) |
| BackupManagementType_s |Text |Providertyp för säkerhets kopiering. Till exempel IaaSVM, FileFolder |
| EntityState_s |Text |Det skyddade Server objektets aktuella tillstånd. Till exempel aktiv, borttagen |
| ProtectedContainerFriendlyName_s |Text |Eget namn på skyddad Server |
| ProtectedContainerName_s |Text |Namn på den skyddade behållaren |
| ProtectedContainerWorkloadType_s |Text |Typ av skyddad behållare som säkerhets kopie ras. Till exempel IaaSVMContainer |
| ProtectedContainerLocation_s |Text |Om den skyddade behållaren finns lokalt eller i Azure |
| ProtectedContainerType_s |Text |Om den skyddade behållaren är en server eller en behållare |
| ProtectedContainerProtectionState_s  |Text |Skydds status för den skyddade behållaren |

### <a name="storage"></a>Storage

Den här tabellen innehåller information om Storage-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| CloudStorageInBytes_s |Decimaltal |Moln lagrings lagring som används av säkerhets kopieringar, beräknat utifrån det senaste värdet (det här fältet är endast för v1-schema)|
| ProtectedInstances_s |Decimaltal |Antal skyddade instanser som används för att beräkna frontend-lagring vid fakturering, beräknat utifrån det senaste värdet |
| EventName_s |Text |Det här fältet representerar namnet på den här händelsen. Det är alltid AzureBackupCentralReport |
| SchemaVersion_s |Text |Det här fältet anger den aktuella versionen av schemat. Det är **v2** |
| State_s |Text |Aktuellt tillstånd för lagrings objekt, t. ex. aktiv, borttagen |
| BackupManagementType_s |Text |Providertyp för server som utför säkerhets kopierings jobb, till exempel IaaSVM, FileFolder |
| OperationName |Text |Det här fältet representerar namnet på den aktuella åtgärds lagringen |
| Kategori |Text |Det här fältet representerar en kategori av diagnostikdata som skickas till Azure Monitor loggar. Det är AzureBackupReport |
| Resurs |Text |Detta är den resurs för vilken data samlas in, och det visar Recovery Services valv namn |
| ProtectedServerUniqueId_s |Text |Unikt ID för den skyddade Server för vilken lagring beräknas |
| VaultUniqueId_s |Text |Unikt ID för valvet för lagring beräknas |
| SourceSystem |Text |Käll system för aktuella data – Azure |
| ResourceId |Text |Resurs-ID för data som samlas in. Till exempel, Recovery Services valv resurs-ID |
| SubscriptionId |Text |Prenumerations-ID för resursen (t. ex. Recovery Services valv) som data samlas in för |
| ResourceGroup |Text |Resurs grupp för resursen (t. ex. Recovery Services valv) som data samlas in för |
| ResourceProvider |Text |Resurs leverantör för vilken data samlas in. Till exempel Microsoft. RecoveryServices |
| ResourceType |Text |Resurs typ som data samlas in för. Till exempel valv |
| StorageUniqueId_s |Text |Unikt ID som används för att identifiera lagrings enheten |
| StorageType_s |Text |Typ av lagring, till exempel moln, volym, disk |
| StorageName_s |Text |Namn på lagrings enhet, till exempel E:\ |
| StorageTotalSizeInGBs_s |Text |Total lagrings utrymme, i GB, som används av lagrings enheten|

### <a name="storageassociation"></a>StorageAssociation

Den här tabellen innehåller grundläggande Storage-relaterade fält som ansluter lagring till andra entiteter.

| Fält | Datatyp | Beskrivning |
| --- | --- |  --- |
| StorageUniqueId_s |Text |Unikt ID som används för att identifiera lagrings enheten |
| SchemaVersion_s |Text |Det här fältet anger den aktuella versionen av schemat. Det är **v2** |
| BackupItemUniqueId_s |Text |Unikt ID som används för att identifiera det säkerhets kopierings objekt som är relaterat till lagrings enheten |
| BackupManagementServerUniqueId_s |Text |Unikt ID som används för att identifiera säkerhets kopierings hanterings servern som är relaterad till lagrings enheten|
| VaultUniqueId_s |Text |Unikt ID som används för att identifiera valvet som hör till lagrings enheten|
| StorageConsumedInMBs_s |Antal|Storlek på lagring som förbrukas av motsvarande säkerhets kopierings objekt i motsvarande lagrings utrymme |
| StorageAllocatedInMBs_s |Antal |Storleken på det lagrings utrymme som allokerats av motsvarande säkerhets kopierings objekt i motsvarande lagring av typ disk|

### <a name="vault"></a>Valv

Den här tabellen innehåller information om valvbaserade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |Text |Det här fältet representerar namnet på den här händelsen. Det är alltid AzureBackupCentralReport |
| SchemaVersion_s |Text |Det här fältet anger den aktuella versionen av schemat. Det är **v2** |
| State_s |Text |Aktuellt tillstånd för Valve-objektet, till exempel aktivt, borttaget |
| OperationName |Text |Det här fältet representerar namnet på den aktuella åtgärden – valvet |
| Kategori |Text |Det här fältet representerar en kategori av diagnostikdata som skickas till Azure Monitor loggar. Det är AzureBackupReport |
| Resurs |Text |Detta är den resurs för vilken data samlas in, och det visar Recovery Services valv namn |
| VaultUniqueId_s |Text |Unikt ID för valvet |
| VaultName_s |Text |Namn på valvet |
| AzureDataCenter_s |Text |Data Center där valvet finns |
| StorageReplicationType_s |Text |Typ av lagrings replikering för valvet, t. ex. |
| SourceSystem |Text |Käll system för aktuella data – Azure |
| ResourceId |Text |Resurs-ID för data som samlas in. Till exempel, Recovery Services valv resurs-ID |
| SubscriptionId |Text |Prenumerations-ID för resursen (t. ex. Recovery Services valv) som data samlas in för |
| ResourceGroup |Text |Resurs grupp för resursen (t. ex. Recovery Services valv) som data samlas in för |
| ResourceProvider |Text |Resurs leverantör för vilken data samlas in. Till exempel Microsoft. RecoveryServices |
| ResourceType |Text |Resurs typ som data samlas in för. Till exempel valv |

### <a name="backup-management-server"></a>Säkerhets kopierings hanterings Server

Den här tabellen innehåller grundläggande fält om säkerhets kopierings hanterings servrar.

|Fält  |Datatyp  | Beskrivning  |
|---------|---------|----------|
|BackupManagementServerName_s     |Text         |Namn på säkerhets kopierings hanterings servern        |
|AzureBackupAgentVersion_s     |Text         |Versionen av Azure Backup-agenten på säkerhets kopierings hanterings servern          |
|BackupManagementServerVersion_s     |Text         |Version av säkerhets kopierings hanterings servern|
|BackupManagementServerOSVersion_s     |Text            |OS-version av säkerhets kopierings hanterings servern|
|BackupManagementServerType_s     |Text         |Typ av säkerhets kopierings hanterings Server, som MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Text         |Fält för att unikt identifiera säkerhets kopierings hanterings servern       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Den här tabellen anger de arbets belastningar som en volym är kopplad till.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| StorageUniqueId_s |Text |Unikt ID som används för att identifiera lagrings enheten |
| BackupItemType_s |Text |De arbets belastningar som den här volymen är den prioriterade lagringen för|

### <a name="protectedinstance"></a>ProtectedInstance

Den här tabellen innehåller grundläggande skyddade instans fält.

| Fält | Datatyp |Versioner som gäller | Description |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |v2|Unikt ID som används för att identifiera säkerhets kopierings objekt för virtuella datorer som säkerhets kopie ras med DPM, MABS|
| ProtectedContainerUniqueId_s |Text |v2|Unikt ID som används för att identifiera den skyddade behållaren för allting förutom virtuella datorer som säkerhets kopie ras med DPM, MABS|
| ProtectedInstanceCount_s |Text |v2|Antal skyddade instanser för det associerade säkerhets kopierings objektet eller skyddade behållaren den datum/tid|

### <a name="recoverypoint"></a>RecoveryPoint

Den här tabellen innehåller grundläggande fält för återställnings punkt.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |Unikt ID som används för att identifiera säkerhets kopierings objekt för virtuella datorer som säkerhets kopie ras med DPM, MABS|
| OldestRecoveryPointTime_s |Text |Datum och tid för den äldsta återställnings punkten för det säkerhetskopierade objektet|
| OldestRecoveryPointLocation_s |Text |Plats för den äldsta återställnings punkten för säkerhets kopierings objekt|
| LatestRecoveryPointTime_s |Text |Datum och tid för den senaste återställnings punkten för det säkerhetskopierade objektet|
| LatestRecoveryPointLocation_s |Text |Plats för den senaste återställnings punkten för säkerhets kopierings objekt|

## <a name="sample-kusto-queries"></a>Exempel på Kusto-frågor

Nedan visas några exempel som hjälper dig att skriva frågor om Azure Backup data som finns i Azure-diagnostiks tabellen:

- Alla slutförda säkerhets kopierings jobb

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- Alla misslyckade säkerhets kopierings jobb

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- Alla lyckade säkerhets kopierings jobb för virtuella Azure-datorer

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Alla lyckade säkerhets kopierings jobb för SQL-logg

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Alla lyckade Azure Backup Agent jobb

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

## <a name="v1-schema-vs-v2-schema"></a>Schemat v1 schema vs v2

Tidigare skickades diagnostikdata för Azure Backup Agent och säkerhets kopiering av virtuella Azure-datorer till Azure-diagnostiks tabellen i ett schema som kallas ***v1-schema** _. Sedan lades nya kolumner till för att stödja andra scenarier och arbets belastningar, och diagnostikdata skickades i ett nytt schema som kallas _ *_v2 schema_* *.  

Av sekretesskäl för bakåtkompatibilitet skickas diagnostikdata för Azure Backup Agent och säkerhets kopiering av virtuella Azure-datorer till Azure-diagnostik tabell i både v1-och v2-schemat (med v1-schemat nu på en utfasnings väg). Du kan identifiera vilka poster i Log Analytics som är v1-schemat genom att filtrera poster för SchemaVersion_s = = "v1" i dina logg frågor.

Referera till den tredje kolumnen Beskrivning i den [data modell](#using-azure-backup-data-model) som beskrivs ovan för att identifiera vilka kolumner som endast tillhör v1-schema.

### <a name="modifying-your-queries-to-use-the-v2-schema"></a>Ändra dina frågor till att använda v2-schemat

Eftersom v1-schemat finns på en föråldrad sökväg rekommenderar vi att du bara använder v2-schemat i alla dina anpassade frågor på Azure Backup diagnostikdata. Nedan visas ett exempel på hur du uppdaterar dina frågor för att ta bort beroendet av v1-schemat:

1. Identifiera om frågan använder ett fält som endast gäller för v1-schemat. Anta att du har en fråga om du vill visa en lista över alla säkerhets kopierings objekt och deras associerade skyddade servrar på följande sätt:

    ````Kusto
    AzureDiagnostics
    | where Category=="AzureBackupReport"
    | where OperationName=="BackupItemAssociation"
    | distinct BackupItemUniqueId_s, ProtectedServerUniqueId_s
    ````

    Frågan ovan använder fältet ProtectedServerUniqueId_s, som endast gäller för v1-schemat. Schema motsvarigheten v2 för det här fältet är ProtectedContainerUniqueId_s (se tabellerna ovan). Fältet BackupItemUniqueId_s gäller även v2-schemat och samma fält kan användas i den här frågan.

2. Uppdatera frågan så att den använder schema fält namnen v2. Vi rekommenderar att du använder filtret **där SchemaVersion_s = = "v2"** i alla dina frågor, så att endast poster som motsvarar v2-schemat tolkas av frågan:

    ````Kusto
    AzureDiagnostics
    | where Category=="AzureBackupReport"
    | where OperationName=="BackupItemAssociation"
    | where SchemaVersion_s=="V2"
    | distinct BackupItemUniqueId_s, ProtectedContainerUniqueId_s
    ````

## <a name="next-steps"></a>Nästa steg

När du har granskat data modellen kan du börja [skapa anpassade frågor](../azure-monitor/visualize/tutorial-logs-dashboards.md) i Azure Monitor loggar för att skapa en egen instrument panel.
