---
title: System funktioner på Azure Monitor loggar
description: Skriv anpassade frågor på Azure Monitor loggar med hjälp av system funktioner
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: acb45e6ad0250a1f8d10377fdd509e40051f25b9
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564916"
---
# <a name="system-functions-on-azure-monitor-logs"></a>System funktioner på Azure Monitor loggar

Azure Backup innehåller en uppsättning funktioner som kallas system funktioner eller lösnings funktioner som är tillgängliga som standard i dina Log Analytics (LA)-arbets ytor.
 
Dessa funktioner fungerar på data i [rå Azure Backup tabeller](./backup-azure-reports-data-model.md) i La och returnerar formaterade data som hjälper dig att enkelt hämta information om alla säkerhetskopierade entiteter med hjälp av enkla frågor. Användare kan skicka parametrar till dessa funktioner för att filtrera data som returneras av de här funktionerna. 

Vi rekommenderar att du använder system funktioner för att skicka frågor till dina säkerhets kopierings data i LA arbets ytor för att skapa anpassade rapporter, eftersom de ger ett antal fördelar, enligt beskrivningen i avsnittet nedan.

## <a name="benefits-of-using-system-functions"></a>Fördelar med att använda system funktioner

* **Enklare frågor**: med hjälp av funktioner kan du minska antalet kopplingar som behövs i dina frågor. Som standard returnerar funktionerna "utplattade" scheman som inkluderar all information som hör till entiteten (säkerhets kopierings instans, jobb, valv och så vidare) efter frågas. Om du till exempel behöver hämta en lista över slutförda säkerhets kopierings jobb genom att säkerhetskopiera objekt namn och dess tillhör ande behållare, kommer ett enkelt anrop till funktionen **_AzureBackup_getJobs ()** att ge dig all den här informationen för varje jobb. Å andra sidan kräver det att du utför flera kopplingar mellan [AddonAzureBackupJobs](./backup-azure-reports-data-model.md#addonazurebackupjobs) -och [CoreAzureBackup](./backup-azure-reports-data-model.md#coreazurebackup) -tabeller när du skickar frågor till RAW-tabellerna direkt.

* **Mjukare över gång från den äldre Diagnostics-händelsen**: med hjälp av system funktioner kan du övergå smidigt från den [bakåtkompatibla](./backup-azure-diagnostic-events.md#legacy-event) AzureBackupReport (i AzureDiagnostics-läge) till de [resursbaserade händelserna](./backup-azure-diagnostic-events.md#diagnostics-events-available-for-azure-backup-users). Med alla systemfunktioner som tillhandahålls av Azure Backup kan du ange en parameter som låter dig välja om funktionen endast ska fråga efter data från de resursbaserade tabellerna eller fråga efter data från både den äldre tabellen och de resursbaserade tabellerna (med deduplicering av poster).
    * Om du har migrerat till resurs-/regionsspecifika tabeller kan du välja att undanta den äldre tabellen från att frågas av funktionen.
    * Om du för närvarande håller på att migrera och har vissa data i de äldre tabeller som du behöver för analys, kan du välja att inkludera den äldre tabellen. När över gången är klar och du inte längre behöver data från den äldre tabellen kan du bara uppdatera värdet för den parameter som skickades till funktionen i dina frågor, för att utesluta den äldre tabellen.
    * Om du fortfarande använder den äldre tabellen fungerar funktionerna fortfarande om du väljer att inkludera den äldre tabellen via samma parameter. Vi rekommenderar dock att du [växlar till de Resource-/regionsspecifika tabellerna](./backup-azure-diagnostic-events.md#steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace) tidigast.

* **Minskar risken för anpassade frågor som bryts**: om Azure Backup introducerar förbättringar av schemat för de underliggande La tabellerna för att hantera framtida rapporterings scenarier, kommer definitionen av funktionerna också att uppdateras för att ta hänsyn till schema ändringarna. Om du använder system funktioner för att skapa anpassade frågor, kommer dina frågor inte att brytas, även om det finns ändringar i tabellens underliggande schema.

> [!NOTE]
> System funktionerna underhålls av Microsoft och deras definitioner kan inte redige ras av användare. Om du behöver redigerbara funktioner kan du skapa [sparade funktioner](../azure-monitor/logs/functions.md) i La.

## <a name="types-of-system-functions-offered-by-azure-backup"></a>Typer av system funktioner som erbjuds av Azure Backup

* **Kärn funktioner**: dessa är funktioner som hjälper dig att fråga någon av nyckel Azure Backup entiteter, t. ex. säkerhets kopierings instanser, valv, principer, jobb och fakturerings enheter. Funktionen **_AzureBackup_getBackupInstances** returnerar till exempel en lista över alla säkerhets kopierings instanser som finns i din miljö från den senaste slutförda dagen (i UTC-tid). Parametrarna och det returnerade schemat för var och en av dessa huvud funktioner sammanfattas nedan i den här artikeln.

* **Trend funktioner**: dessa är funktioner som returnerar historiska poster för säkerhetskopierade entiteter (t. ex. säkerhets kopierings instanser, fakturerings grupper) och gör att du kan hämta information om varje dag, veckovis och månatlig trend på nyckel mått (till exempel antal, förbrukad lagring) som hör till dessa entiteter. Parametrarna och det returnerade schemat för var och en av dessa trend funktioner sammanfattas nedan i den här artikeln.

> [!NOTE]
> För närvarande returnerar system Functions data för upp till den senaste slutförda dagen (i UTC-tid). Data för den aktuella del dagen returneras inte. Så om du vill hämta poster för den aktuella dagen måste du använda de obearbetade LA-tabellerna.


## <a name="list-of-system-functions"></a>Lista över system funktioner

### <a name="core-functions"></a>Kärn funktioner

#### <a name="_azurebackup_getvaults"></a>_AzureBackup_GetVaults ()

Den här funktionen returnerar listan över alla Recovery Services-valv i Azure-miljön som är kopplade till arbets ytan LA.

**Parametrar**

| **Parameter namn** | **Beskrivning** | **Krävs?** | **Exempel värde** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Använd bara den här parametern tillsammans med parametern RangeEnd om du behöver hämta alla valvbaserade poster under tids perioden från rang Est art till RangeEnd. Som standard är värdet för rang Est art och RangeEnd null, vilket gör att funktionen bara hämtar den senaste posten för varje valv. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Använd den här parametern tillsammans med en rang Est parameter endast om du behöver hämta alla valvbaserade poster under tids perioden från rang Est art till RangeEnd. Som standard är värdet för rang Est art och RangeEnd null, vilket gör att funktionen bara hämtar den senaste posten för varje valv. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Använd den här parametern för att filtrera resultatet av funktionen för en viss uppsättning prenumerationer där säkerhets kopierings data finns. Om du anger en kommaavgränsad lista med prenumerations-ID: n som en parameter för den här funktionen kan du bara hämta de valv som finns i de angivna prenumerationerna. Som standard är värdet för den här parametern *, vilket gör att funktionen söker efter poster i alla prenumerationer. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Använd den här parametern för att filtrera resultatet av funktionen för en viss uppsättning regioner där det finns säkerhets kopierings data. Genom att ange en kommaavgränsad lista över regioner som en parameter för den här funktionen kan du bara hämta de valv som finns i de angivna regionerna. Som standard är värdet för den här parametern *, vilket gör att funktionen söker efter poster i alla regioner. | N | "öster, väst"|
| VaultList    |Använd den här parametern för att filtrera utdata från funktionen för en viss uppsättning valv. Om du anger en kommaavgränsad lista med valv namn som en parameter för den här funktionen kan du bara hämta poster som avser de angivna valvena. Som standard är värdet för den här parametern *, vilket gör att funktionen söker efter poster i alla valv. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Använd den här parametern för att filtrera utdata från funktionen till poster som hör till en viss valv typ. För närvarande är den enda typen av valv som stöds "Microsoft. RecoveryServices/valv", vilket är standardvärdet för den här parametern | N | "Microsoft. RecoveryServices/valv"|
| ExcludeLegacyEvent  | Använd den här parametern för att välja om du vill fråga efter data i den äldre AzureDiagnostics-tabellen eller inte. Om värdet för den här parametern är falskt, frågar funktionen data från både AzureDiagnostics-tabellen och de resursbaserade tabellerna. Om värdet för den här parametern är sant, frågar funktionen data från de resursbaserade tabellerna. Standardvärdet är True. | N | true |

**Returnerade fält**

| **Fält namn** | **Beskrivning** |
| -------------- | --------------- |
| UniqueId | Primär nyckel som anger ett unikt ID för valvet |
| Id | Azure Resource Manager (ARM) ID för valvet |
| Name | Namn på valvet |
| SubscriptionId | ID för den prenumeration där valvet finns |
| Location | Plats där valvet finns |
| VaultStore_StorageReplicationType | Typ av lagrings replik som är associerad med valvet |
| Taggar | Taggar för valvet |
| TimeGenerated | Tidsstämpel för posten |
| Typ |  Typ av valv, som är "Microsoft. RecoveryServices/valv"|

#### <a name="_azurebackup_getpolicies"></a>_AzureBackup_GetPolicies ()

Den här funktionen returnerar listan över säkerhets kopierings principer som används i din Azure-miljö tillsammans med detaljerad information om varje princip, till exempel typ av data källa, typ av lagrings replikering och så vidare.

**Parametrar**

| **Parameter namn** | **Beskrivning** | **Krävs?** | **Exempel värde** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Använd den här parametern tillsammans med rang parametern endast om du behöver hämta alla principbaserad poster under tids perioden från rang Est art till RangeEnd. Som standard är värdet för rang Est art och RangeEnd null, vilket gör att funktionen bara hämtar den senaste posten för varje princip. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Använd den här parametern tillsammans med en rang Est parameter endast om du behöver hämta alla principbaserad poster under tids perioden från rang Est art till RangeEnd. Som standard är värdet för rang Est art och RangeEnd null, vilket gör att funktionen bara hämtar den senaste posten för varje princip. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Använd den här parametern för att filtrera resultatet av funktionen för en viss uppsättning prenumerationer där säkerhets kopierings data finns. Om du anger en kommaavgränsad lista med prenumerations-ID: n som en parameter för den här funktionen kan du bara hämta de principer som finns i de angivna prenumerationerna. Som standard är värdet för den här parametern *, vilket gör att funktionen söker efter poster i alla prenumerationer. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Använd den här parametern för att filtrera resultatet av funktionen för en viss uppsättning regioner där det finns säkerhets kopierings data. Genom att ange en kommaavgränsad lista över regioner som en parameter för den här funktionen kan du bara hämta de principer som finns i de angivna regionerna. Som standard är värdet för den här parametern *, vilket gör att funktionen söker efter poster i alla regioner. | N | "öster, väst"|
| VaultList    |Använd den här parametern för att filtrera utdata från funktionen för en viss uppsättning valv. Om du anger en kommaavgränsad lista med valv namn som en parameter för den här funktionen kan du hämta poster med principer som endast gäller för de angivna valvena. Som standard är värdet för den här parametern *, vilket gör att funktionen söker efter poster i principer i alla valv. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Använd den här parametern för att filtrera utdata från funktionen till poster som hör till en viss valv typ. För närvarande är den enda typen av valv som stöds "Microsoft. RecoveryServices/valv", vilket är standardvärdet för den här parametern. | N | "Microsoft. RecoveryServices/valv"|
| ExcludeLegacyEvent  | Använd den här parametern för att välja om du vill fråga efter data i den äldre AzureDiagnostics-tabellen eller inte. Om värdet för den här parametern är falskt, frågar funktionen data från både AzureDiagnostics-tabellen och de resursbaserade tabellerna. Om värdet för den här parametern är sant, frågar funktionen data från de resursbaserade tabellerna. Standardvärdet är True. | N | true |
| BackupSolutionList | Använd den här parametern för att filtrera resultatet av funktionen för en viss uppsättning säkerhets kopierings lösningar som används i din Azure-miljö. Om du till exempel anger "säkerhets kopiering av virtuella Azure-datorer, SQL i Azure VM backup, DPM" som värde för den här parametern, returnerar funktionen bara poster som är relaterade till objekt som säkerhets kopie ras med säkerhets kopiering av virtuella Azure-datorer, SQL i Azure VM Backup eller DPM till Azure Backup. Som standard är värdet för den här parametern "*", som gör att funktionen returnerar poster som hör till alla säkerhets kopierings lösningar som stöds av säkerhets kopierings rapporter (de värden som stöds är "säkerhets kopiering av virtuella Azure-datorer", "SQL i Azure VM Backup", "SAP HANA i Azure VM Backup", "Azure Storage (Azure Files) säkerhets kopiering", "Azure Backup Agent", "DPM", "Azure Backup Server" eller en kommaavgränsad kombination av något av dessa värden). | N | "Säkerhets kopiering av virtuella Azure-datorer, SQL i Azure VM backup, DPM, Azure Backup Agent" |

**Returnerade fält**

| **Fält namn** | **Beskrivning** |
| -------------- | --------------- |
| UniqueId | Primär nyckel som anger ett unikt ID för principen |
| Id | Azure Resource Manager (ARM) ID för principen |
| Name | Namnet på principen |
| Lösning för säkerhets kopiering | Säkerhets kopierings lösning som principen är associerad med. Till exempel Azure VM backup, SQL i Azure VM backup och så vidare. |
| TimeGenerated | Tidsstämpel för posten |
| VaultUniqueId | Sekundär nyckel som refererar till valvet som är associerat med principen |
| VaultResourceId | Azure Resource Manager (ARM) ID för valvet som är associerat med principen |
| VaultName | Namnet på valvet som är associerat med principen |
| VaultTags | Taggar för valvet som är associerat med principen |
| VaultLocation | Plats för valvet som är associerat med principen |
| VaultSubscriptionId | Prenumerations-ID för valvet som är associerat med principen |
| VaultStore_StorageReplicationType | Typ av lagrings replikering för valvet som är associerat med principen |
| VaultType | Typ av valv, som är "Microsoft. RecoveryServices/valv" |
| ExtendedProperties | Ytterligare egenskaper för principen |

#### <a name="_azurebackup_getjobs"></a>_AzureBackup_GetJobs ()

Den här funktionen returnerar en lista över alla säkerhets kopierings-och återställnings jobb som har utlösts inom ett angivet tidsintervall, tillsammans med detaljerad information om varje jobb, till exempel jobb status, jobb varaktighet, överförda data och så vidare.

**Parametrar**

| **Parameter namn** | **Beskrivning** | **Krävs?** | **Exempel värde** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Använd den här parametern tillsammans med parametern RangeEnd för att hämta en lista över alla jobb som startades under tids perioden från rang Est art till RangeEnd. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | Använd den här parametern tillsammans med en rang parameter för att hämta en lista över alla jobb som startades under tids perioden från rang Est art till RangeEnd. | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Använd den här parametern för att filtrera resultatet av funktionen för en viss uppsättning prenumerationer där säkerhets kopierings data finns. Om du anger en kommaavgränsad lista med prenumerations-ID: n som en parameter för den här funktionen kan du bara hämta de jobb som är associerade med valv i de angivna prenumerationerna. Som standard är värdet för den här parametern *, vilket gör att funktionen söker efter poster i alla prenumerationer. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Använd den här parametern för att filtrera resultatet av funktionen för en viss uppsättning regioner där det finns säkerhets kopierings data. Genom att ange en kommaavgränsad lista över regioner som en parameter för den här funktionen kan du bara hämta de jobb som är associerade med valv i de angivna regionerna. Som standard är värdet för den här parametern *, vilket gör att funktionen söker efter poster i alla regioner. | N | "öster, väst"|
| VaultList    | Använd den här parametern för att filtrera utdata från funktionen för en viss uppsättning valv. Om du anger en kommaavgränsad lista med valv namn som en parameter för den här funktionen kan du bara hämta jobb som rör de angivna valvena. Som standard är värdet för den här parametern *, vilket gör att funktionen söker efter jobb i alla valv. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Använd den här parametern för att filtrera utdata från funktionen till poster som hör till en viss valv typ. För närvarande är den enda typen av valv som stöds "Microsoft. RecoveryServices/valv", vilket är standardvärdet för den här parametern. | N | "Microsoft. RecoveryServices/valv"|
| ExcludeLegacyEvent  | Använd den här parametern för att välja om du vill fråga efter data i den äldre AzureDiagnostics-tabellen eller inte. Om värdet för den här parametern är falskt, frågar funktionen data från både AzureDiagnostics-tabellen och de resursbaserade tabellerna. Om värdet för den här parametern är sant, frågar funktionen data från de resursbaserade tabellerna. Standardvärdet är True. | N | true |
| BackupSolutionList | Använd den här parametern för att filtrera resultatet av funktionen för en viss uppsättning säkerhets kopierings lösningar som används i din Azure-miljö. Om du till exempel anger "säkerhets kopiering av virtuella Azure-datorer, SQL i Azure VM backup, DPM" som värde för den här parametern, returnerar funktionen bara poster som är relaterade till objekt som säkerhets kopie ras med säkerhets kopiering av virtuella Azure-datorer, SQL i Azure VM Backup eller DPM till Azure Backup. Som standard är värdet för den här parametern "*", som gör att funktionen returnerar poster som hör till alla säkerhets kopierings lösningar som stöds av säkerhets kopierings rapporter (de värden som stöds är "säkerhets kopiering av virtuella Azure-datorer", "SQL i Azure VM Backup", "SAP HANA i Azure VM Backup", "Azure Storage (Azure Files) säkerhets kopiering", "Azure Backup Agent", "DPM", "Azure Backup Server" eller en kommaavgränsad kombination av något av dessa värden). | N | "Säkerhets kopiering av virtuella Azure-datorer, SQL i Azure VM backup, DPM, Azure Backup Agent" |
| JobOperationList | Använd den här parametern för att filtrera utdata från funktionen för en speciell typ av jobb. Till exempel säkerhets kopiering eller återställning. Som standard är värdet för den här parametern "*", vilket gör att funktionen söker efter både säkerhets kopierings-och återställnings jobb. | N | Band | 
| JobStatusList | Använd den här parametern för att filtrera utdata från funktionen för en angiven jobb status. Till exempel slutförd, misslyckad och så vidare. Som standard är värdet för den här parametern "*", vilket gör att funktionen söker efter alla jobb oavsett status. | N | "Misslyckades, CompletedWithWarnings" |
| JobFailureCodeList | Använd den här parametern för att filtrera utdata från funktionen för en speciell felkod. Som standard är värdet för den här parametern "*", vilket gör att funktionen söker efter alla jobb, oavsett felkod. | N | Resultatet
| DatasourceSetName | Använd den här parametern för att filtrera utdata från funktionen till en viss överordnad resurs. Om du till exempel vill returnera SQL i Azure VM backup-instanser som tillhör den virtuella datorn "testvm" anger du _testvm_ som värde för den här parametern. Som standard är värdet "*", vilket gör att funktionen söker efter poster i alla säkerhets kopierings instanser. | N | testvm |
| BackupInstanceName | Använd den här parametern om du vill söka efter jobb på en viss säkerhets kopierings instans efter namn. Som standard är värdet "*", vilket gör att funktionen söker efter poster i alla säkerhets kopierings instanser. | N | testvm |
| ExcludeLog | Använd den här parametern om du vill undanta logg jobb från att returneras av funktionen (hjälp i fråga om prestanda). Som standard är värdet för den här parametern sant, vilket gör att funktionen exkluderar logg jobb. | N | true


**Returnerade fält**

| **Fält namn** | **Beskrivning** |
| -------------- | --------------- |
| UniqueId | Primär nyckel som anger jobbets unika ID |
| OperationCategory | Kategori för den åtgärd som utförs. Till exempel säkerhets kopiering, återställning |
| Åtgärd | Information om den åtgärd som utförs. Till exempel log (för logg säkerhets kopiering)|
| Status | Status för jobbet. Till exempel slutförd, misslyckad, CompletedWithWarnings |
| ErrorTitle | Felkod för jobbet |
| StartTime | Datum och tid då jobbet startades |
| DurationInSecs | Jobbets varaktighet i sekunder |
| DataTransferredInMBs | Data som överförs av jobbet i MB |
| RestoreJobRPDateTime | Datum och tid när återställnings punkten som återställdes skapades |
| RestoreJobRPLocation | Platsen där återställnings punkten som återställdes har lagrats |
| BackupInstanceUniqueId | Sekundär nyckel som refererar till den säkerhets kopierings instans som är kopplad till jobbet |
| BackupInstanceId | Azure Resource Manager (ARM) ID för den säkerhets kopierings instans som är associerad med jobbet |
| BackupInstanceFriendlyName | Namnet på den säkerhets kopierings instans som är associerad med jobbet |
| DatasourceResourceId | Azure Resource Manager (ARM) ID för den underliggande data källan som är associerad med jobbet. Till exempel Azure Resource Manager (ARM) ID för den virtuella datorn |
| DatasourceFriendlyName | Eget namn på den underliggande data källan som är kopplad till jobbet |
| DatasourceType | Typ av data källa som är kopplad till jobbet. Till exempel "Microsoft. Compute/virtualMachines" |
| BackupSolution | Säkerhets kopierings lösning som jobbet är associerat med. Till exempel Azure VM backup, SQL i Azure VM backup och så vidare. |
| DatasourceSetResourceId | Azure Resource Manager (ARM) ID för den överordnade resursens överordnade resurs (där det är tillämpligt). För till exempel en SQL i en Azure VM-datakälla innehåller det här fältet Azure Resource Manager (ARM) ID för den virtuella dator där SQL Database finns |
| DatasourceSetType | Typ av data källans överordnade resurs (där det är tillämpligt). Till exempel för en SAP HANA i Azure VM DataSource, är det här fältet Microsoft. Compute/virtualMachines eftersom den överordnade resursen är en virtuell Azure-dator |
| VaultResourceId | Azure Resource Manager (ARM) ID för valvet som är associerat med jobbet |
| VaultUniqueId | Sekundär nyckel som refererar till valvet som är associerat med jobbet |
| VaultName | Namnet på valvet som är associerat med jobbet |
| VaultTags | Taggar för valvet som är associerat med jobbet |
| VaultSubscriptionId | Prenumerations-ID för valvet som är associerat med jobbet |
| VaultLocation | Plats för valvet som är associerat med jobbet |
| VaultStore_StorageReplicationType | Typ av lagrings replik för det valv som är associerat med jobbet |
| VaultType | Typ av valv, som är "Microsoft. RecoveryServices/valv" |
| TimeGenerated | Tidsstämpel för posten |

#### <a name="_azurebackup_getbackupinstances"></a>_AzureBackup_GetBackupInstances ()

Den här funktionen returnerar listan över säkerhets kopierings instanser som är associerade med dina Recovery Services-valv, tillsammans med detaljerad information om varje säkerhets kopierings instans, till exempel användning av moln lagring, associerad princip och så vidare.

**Parametrar**

| **Parameter namn** | **Beskrivning** | **Krävs?** | **Exempel värde** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Använd bara den här parametern tillsammans med RangeEnd-parametern om du behöver hämta alla säkerhets kopie instansen-relaterade poster under tids perioden från rang Est art till RangeEnd. Som standard är värdet för rang Est art och RangeEnd null, vilket gör att funktionen bara hämtar den senaste posten för varje säkerhets kopierings instans. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Använd den här parametern tillsammans med en rang Est parameter endast om du behöver hämta alla säkerhets kopie instansen-relaterade poster under tids perioden från rang Est till RangeEnd. Som standard är värdet för rang Est art och RangeEnd null, vilket gör att funktionen bara hämtar den senaste posten för varje säkerhets kopierings instans. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Använd den här parametern för att filtrera resultatet av funktionen för en viss uppsättning prenumerationer där säkerhets kopierings data finns. Om du anger en kommaavgränsad lista med prenumerations-ID: n som en parameter för den här funktionen kan du bara hämta de säkerhets kopierings instanser som finns i de angivna prenumerationerna. Som standard är värdet för den här parametern *, vilket gör att funktionen söker efter poster i alla prenumerationer. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Använd den här parametern för att filtrera resultatet av funktionen för en viss uppsättning regioner där det finns säkerhets kopierings data. Genom att ange en kommaavgränsad lista över regioner som en parameter för den här funktionen kan du bara hämta de säkerhets kopierings instanser som finns i de angivna regionerna. Som standard är värdet för den här parametern *, vilket gör att funktionen söker efter poster i alla regioner. | N | "öster, väst"|
| VaultList    |Använd den här parametern för att filtrera utdata från funktionen för en viss uppsättning valv. Om du anger en kommaavgränsad lista med valv namn som en parameter för den här funktionen kan du hämta poster för säkerhets kopierings instanser som bara rör de angivna valvena. Som standard är värdet för den här parametern *, vilket gör att funktionen söker efter poster i säkerhets kopierings instanser i alla valv. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Använd den här parametern för att filtrera utdata från funktionen till poster som hör till en viss valv typ. För närvarande är den enda typen av valv som stöds "Microsoft. RecoveryServices/valv", vilket är standardvärdet för den här parametern. | N | "Microsoft. RecoveryServices/valv"|
| ExcludeLegacyEvent  | Använd den här parametern för att välja om du vill fråga efter data i den äldre AzureDiagnostics-tabellen eller inte. Om värdet för den här parametern är falskt, frågar funktionen data från både AzureDiagnostics-tabellen och de resursbaserade tabellerna. Om värdet för den här parametern är sant, frågar funktionen data från de resursbaserade tabellerna. Standardvärdet är True. | N | true |
| BackupSolutionList | Använd den här parametern för att filtrera resultatet av funktionen för en viss uppsättning säkerhets kopierings lösningar som används i din Azure-miljö. Om du till exempel anger "säkerhets kopiering av virtuella Azure-datorer, SQL i Azure VM backup, DPM" som värde för den här parametern, returnerar funktionen bara poster som är relaterade till objekt som säkerhets kopie ras med säkerhets kopiering av virtuella Azure-datorer, SQL i Azure VM Backup eller DPM till Azure Backup. Som standard är värdet för den här parametern "*", som gör att funktionen returnerar poster som hör till alla säkerhets kopierings lösningar som stöds av säkerhets kopierings rapporter (de värden som stöds är "säkerhets kopiering av virtuella Azure-datorer", "SQL i Azure VM Backup", "SAP HANA i Azure VM Backup", "Azure Storage (Azure Files) säkerhets kopiering", "Azure Backup Agent", "DPM", "Azure Backup Server" eller en kommaavgränsad kombination av något av dessa värden). | N | "Säkerhets kopiering av virtuella Azure-datorer, SQL i Azure VM backup, DPM, Azure Backup Agent" |
| ProtectionInfoList | Använd den här parametern för att välja om du bara vill ta med de säkerhets kopierings instanser som är aktivt skyddade eller om du även vill inkludera de instanser vars skydd har stoppats och instanser där den första säkerhets kopieringen väntar. De värden som stöds är "skyddade", "ProtectionStopped", "InitialBackupPending" eller en kommaavgränsad kombination av dessa värden. Som standard är värdet "*", vilket gör att funktionen söker efter alla säkerhets kopierings instanser oberoende av skydds information. | N | Känslig |
| DatasourceSetName | Använd den här parametern för att filtrera utdata från funktionen till en viss överordnad resurs. Om du till exempel vill returnera SQL i Azure VM backup-instanser som tillhör den virtuella datorn "testvm" anger du _testvm_ som värde för den här parametern. Som standard är värdet "*", vilket gör att funktionen söker efter poster i alla säkerhets kopierings instanser. | N | testvm |
| BackupInstanceName | Använd den här parametern om du vill söka efter en viss säkerhets kopierings instans efter namn. Som standard är värdet "*", vilket gör att funktionen söker efter alla säkerhets kopierings instanser. | N | testvm |
| DisplayAllFields | Använd den här parametern för att välja om du bara vill hämta en delmängd av fälten som returnerades av funktionen. Om värdet för den här parametern är false, eliminerar funktionen lagrings-och kvarhållning relaterad information från utdata från funktionen. Detta är användbart om du använder den här funktionen som ett mellanliggande steg i en större fråga och behöver optimera prestandan för frågan genom att ta bort kolumner som du inte behöver för analys. Som standard är värdet för den här parametern sant, vilket gör att funktionen returnerar alla fält som hör till säkerhets kopierings instansen. | N | true |

**Returnerade fält**

| **Fält namn** | **Beskrivning** |
| -------------- | --------------- |
| UniqueId | Primär nyckel som anger unikt ID för säkerhets kopierings instansen |
| Id | Azure Resource Manager-ID (ARM) för säkerhets kopierings instansen |
| FriendlyName | Eget namn på säkerhets kopierings instansen |
| ProtectionInfo | Information om skydds inställningarna för säkerhets kopierings instansen. Till exempel är det skydd som har kon figurer ATS, skyddet stoppat, första säkerhets kopiering väntar |
| LatestRecoveryPoint | Datum och tid för den senaste återställnings punkten som är kopplad till säkerhets kopierings instansen |
| OldestRecoveryPoint | Datum och tid för den äldsta återställnings punkten som är associerad med säkerhets kopierings instansen |
| SourceSizeInMBs | Klient delens storlek för säkerhets kopierings instansen i MB |
| VaultStore_StorageConsumptionInMBs | Total moln lagring som förbrukas av säkerhets kopierings instansen i valvet – standard nivå |
| DataSourceFriendlyName | Eget namn på den data källa som motsvarar säkerhets kopierings instansen |
| BackupSolution | Säkerhets kopierings lösning som säkerhets kopierings instansen är associerad med. Till exempel Azure VM backup, SQL i Azure VM backup och så vidare. |
| DatasourceType | Typ av data källa som motsvarar säkerhets kopierings instansen. Till exempel "Microsoft. Compute/virtualMachines" |
| DatasourceResourceId | Azure Resource Manager (ARM) ID för den underliggande data källan som motsvarar säkerhets kopierings instansen. Till exempel Azure Resource Manager (ARM) ID för den virtuella datorn |
| DatasourceSetFriendlyName | Eget namn på data källans överordnade resurs (där det är tillämpligt). För till exempel en SQL i en Azure VM-datakälla innehåller det här fältet namnet på den virtuella dator där SQL Database finns |
| DatasourceSetResourceId | Azure Resource Manager (ARM) ID för den överordnade resursens överordnade resurs (där det är tillämpligt). För till exempel en SQL i en Azure VM-datakälla innehåller det här fältet Azure Resource Manager (ARM) ID för den virtuella dator där SQL Database finns |
| DatasourceSetType | Typ av data källans överordnade resurs (där det är tillämpligt). Till exempel för en SAP HANA i Azure VM DataSource, är det här fältet Microsoft. Compute/virtualMachines eftersom den överordnade resursen är en virtuell Azure-dator |
| PolicyName | Namnet på principen som är associerad med säkerhets kopierings instansen |
| PolicyUniqueId | Sekundär nyckel som refererar till principen som är kopplad till säkerhets kopierings instansen  |
| PolicyId | Azure Resource Manager (ARM) ID för principen som är associerad med säkerhets kopierings instansen |
| VaultResourceId | Azure Resource Manager (ARM) ID för valvet som är associerat med säkerhets kopierings instansen |
| VaultUniqueId | Sekundär nyckel som refererar till valvet som är associerat med säkerhets kopierings instansen |
| VaultName | Namnet på valvet som är associerat med säkerhets kopierings instansen |
| VaultTags | Taggar för valvet som är associerat med säkerhets kopierings instansen |
| VaultSubscriptionId | Prenumerations-ID för valvet som är kopplat till säkerhets kopierings instansen |
| VaultLocation | Plats för valvet som är associerat med säkerhets kopierings instansen |
| VaultStore_StorageReplicationType | Typ av lagrings replikering för valvet som är kopplat till säkerhets kopierings instansen |
| VaultType | Typ av valv, som är "Microsoft. RecoveryServices/valv" |
| TimeGenerated | Tidsstämpel för posten |

#### <a name="_azurebackup_getbillinggroups"></a>_AzureBackup_GetBillingGroups ()

Den här funktionen returnerar en lista över alla säkerhetskopierade fakturerings enheter (fakturerings grupper) tillsammans med information om viktiga fakturerings komponenter som till exempel storlek på klient del och total moln lagring.

**Parametrar**

| **Parameter namn** | **Beskrivning** | **Krävs?** | **Exempel värde** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Använd bara den här parametern tillsammans med parametern RangeEnd om du behöver hämta alla relaterade poster för fakturerings gruppen under tids perioden från rang Est art till RangeEnd. Som standard är värdet för rang Est art och RangeEnd null, vilket gör att funktionen bara hämtar den senaste posten för varje fakturerings grupp. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Använd den här parametern tillsammans med en rang Est parameter endast om du behöver hämta alla relaterade poster för fakturerings gruppen under tids perioden från rang Est art till RangeEnd. Som standard är värdet för rang Est art och RangeEnd null, vilket gör att funktionen bara hämtar den senaste posten för varje fakturerings grupp. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Använd den här parametern för att filtrera resultatet av funktionen för en viss uppsättning prenumerationer där säkerhets kopierings data finns. Om du anger en kommaavgränsad lista med prenumerations-ID: n som en parameter för den här funktionen kan du bara hämta de fakturerings grupper som finns i de angivna prenumerationerna. Som standard är värdet för den här parametern *, vilket gör att funktionen söker efter poster i alla prenumerationer. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Använd den här parametern för att filtrera resultatet av funktionen för en viss uppsättning regioner där det finns säkerhets kopierings data. Genom att ange en kommaavgränsad lista över regioner som en parameter för den här funktionen kan du bara hämta de fakturerings grupper som finns i de angivna regionerna. Som standard är värdet för den här parametern *, vilket gör att funktionen söker efter poster i alla regioner. | N | "öster, väst"|
| VaultList    |Använd den här parametern för att filtrera utdata från funktionen för en viss uppsättning valv. Om du anger en kommaavgränsad lista med valv namn som en parameter för den här funktionen kan du hämta poster för säkerhets kopierings instanser som bara rör de angivna valvena. Som standard är värdet för den här parametern *, vilket gör att funktionen söker efter poster i fakturerings grupper i alla valv. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Använd den här parametern för att filtrera utdata från funktionen till poster som hör till en viss valv typ. För närvarande är den enda typen av valv som stöds "Microsoft. RecoveryServices/valv", vilket är standardvärdet för den här parametern. | N | "Microsoft. RecoveryServices/valv"|
| ExcludeLegacyEvent  | Använd den här parametern för att välja om du vill fråga efter data i den äldre AzureDiagnostics-tabellen eller inte. Om värdet för den här parametern är falskt, frågar funktionen data från både AzureDiagnostics-tabellen och de resursbaserade tabellerna. Om värdet för den här parametern är sant, frågar funktionen data från de resursbaserade tabellerna. Standardvärdet är True. | N | true |
| BackupSolutionList | Använd den här parametern för att filtrera resultatet av funktionen för en viss uppsättning säkerhets kopierings lösningar som används i din Azure-miljö. Om du till exempel anger "säkerhets kopiering av virtuella Azure-datorer, SQL i Azure VM backup, DPM" som värde för den här parametern, returnerar funktionen bara poster som är relaterade till objekt som säkerhets kopie ras med säkerhets kopiering av virtuella Azure-datorer, SQL i Azure VM Backup eller DPM till Azure Backup. Som standard är värdet för den här parametern "*", som gör att funktionen returnerar poster som hör till alla säkerhets kopierings lösningar som stöds av säkerhets kopierings rapporter (de värden som stöds är "säkerhets kopiering av virtuella Azure-datorer", "SQL i Azure VM Backup", "SAP HANA i Azure VM Backup", "Azure Storage (Azure Files) säkerhets kopiering", "Azure Backup Agent", "DPM", "Azure Backup Server" eller en kommaavgränsad kombination av något av dessa värden). | N | "Säkerhets kopiering av virtuella Azure-datorer, SQL i Azure VM backup, DPM, Azure Backup Agent" |
| BillingGroupName | Använd den här parametern om du vill söka efter en viss fakturerings grupp efter namn. Som standard är värdet "*", vilket gör att funktionen söker efter alla fakturerings grupper. | N | testvm |

**Returnerade fält**

| **Fält namn** | **Beskrivning** |
| -------------- | --------------- |
| UniqueId | Primär nyckel som anger unikt ID för fakturerings gruppen |
| FriendlyName | Eget namn på fakturerings gruppen |
| Name | Namnet på fakturerings gruppen |
| Typ | Typ av fakturerings grupp. Till exempel ProtectedContainer eller BackupItem |
| SourceSizeInMBs | Klient delens storlek för fakturerings gruppen i MB |
| VaultStore_StorageConsumptionInMBs | Total moln lagring som förbrukas av fakturerings gruppen i valvet – standard nivå |
| BackupSolution | Säkerhets kopierings lösning som fakturerings gruppen är associerad med. Till exempel Azure VM backup, SQL i Azure VM backup och så vidare. |
| VaultResourceId | Azure Resource Manager (ARM) ID för valvet som är associerat med fakturerings gruppen |
| VaultUniqueId | Sekundär nyckel som refererar till valvet som är kopplat till fakturerings gruppen |
| VaultName | Namn på valvet som är associerat med fakturerings gruppen |
| VaultTags | Taggar för valvet som associeras med fakturerings gruppen |
| VaultSubscriptionId | Prenumerations-ID för valvet som är associerat med fakturerings gruppen |
| VaultLocation | Plats för valvet som är associerat med fakturerings gruppen |
| VaultStore_StorageReplicationType | Typ av lagrings replikering för valvet som är associerat med fakturerings gruppen |
| VaultType | Typ av valv, som är "Microsoft. RecoveryServices/valv" |
| TimeGenerated | Tidsstämpel för posten |
| ExtendedProperties | Ytterligare egenskaper för fakturerings gruppen |

### <a name="trend-functions"></a>Trend funktioner

#### <a name="_azurebackup_getbackupinstancestrends"></a>_AzureBackup_GetBackupInstancesTrends ()

Den här funktionen returnerar historiska poster för varje säkerhets kopierings instans, så att du kan visa viktiga trender, veckovis och månads trender för antalet säkerhets kopierings instanser och lagrings förbrukningen på flera nivåer.

**Parametrar**

| **Parameter namn** | **Beskrivning** | **Krävs?** | **Exempel värde** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Använd den här parametern tillsammans med parametern RangeEnd för att hämta alla säkerhets kopie instansen relaterade poster i tids perioden från rang Est art till RangeEnd. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | Använd den här parametern tillsammans med en rang parameter för att hämta alla säkerhets kopierings instanser relaterade poster under tids perioden från rang Est art till RangeEnd. | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Använd den här parametern för att filtrera resultatet av funktionen för en viss uppsättning prenumerationer där säkerhets kopierings data finns. Om du anger en kommaavgränsad lista med prenumerations-ID: n som en parameter för den här funktionen kan du bara hämta de säkerhets kopierings instanser som finns i de angivna prenumerationerna. Som standard är värdet för den här parametern *, vilket gör att funktionen söker efter poster i alla prenumerationer. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Använd den här parametern för att filtrera resultatet av funktionen för en viss uppsättning regioner där det finns säkerhets kopierings data. Genom att ange en kommaavgränsad lista över regioner som en parameter för den här funktionen kan du bara hämta de säkerhets kopierings instanser som finns i de angivna regionerna. Som standard är värdet för den här parametern *, vilket gör att funktionen söker efter poster i alla regioner. | N | "öster, väst"|
| VaultList    |Använd den här parametern för att filtrera utdata från funktionen för en viss uppsättning valv. Om du anger en kommaavgränsad lista med valv namn som en parameter för den här funktionen kan du hämta poster för säkerhets kopierings instanser som bara rör de angivna valvena. Som standard är värdet för den här parametern *, vilket gör att funktionen söker efter poster i säkerhets kopierings instanser i alla valv. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Använd den här parametern för att filtrera utdata från funktionen till poster som hör till en viss valv typ. För närvarande är den enda typen av valv som stöds "Microsoft. RecoveryServices/valv", vilket är standardvärdet för den här parametern. | N | "Microsoft. RecoveryServices/valv"|
| ExcludeLegacyEvent  | Använd den här parametern för att välja om du vill fråga efter data i den äldre AzureDiagnostics-tabellen eller inte. Om värdet för den här parametern är falskt, frågar funktionen data från både AzureDiagnostics-tabellen och de resursbaserade tabellerna. Om värdet för den här parametern är sant, frågar funktionen data från de resursbaserade tabellerna. Standardvärdet är True. | N | true |
| BackupSolutionList | Använd den här parametern för att filtrera resultatet av funktionen för en viss uppsättning säkerhets kopierings lösningar som används i din Azure-miljö. Om du till exempel anger "säkerhets kopiering av virtuella Azure-datorer, SQL i Azure VM backup, DPM" som värde för den här parametern, returnerar funktionen bara poster som är relaterade till objekt som säkerhets kopie ras med säkerhets kopiering av virtuella Azure-datorer, SQL i Azure VM Backup eller DPM till Azure Backup. Som standard är värdet för den här parametern "*", som gör att funktionen returnerar poster som hör till alla säkerhets kopierings lösningar som stöds av säkerhets kopierings rapporter (de värden som stöds är "säkerhets kopiering av virtuella Azure-datorer", "SQL i Azure VM Backup", "SAP HANA i Azure VM Backup", "Azure Storage (Azure Files) säkerhets kopiering", "Azure Backup Agent", "DPM", "Azure Backup Server" eller en kommaavgränsad kombination av något av dessa värden). | N | "Säkerhets kopiering av virtuella Azure-datorer, SQL i Azure VM backup, DPM, Azure Backup Agent" |
| ProtectionInfoList | Använd den här parametern för att välja om du bara vill ta med de säkerhets kopierings instanser som är aktivt skyddade eller om du även vill inkludera de instanser vars skydd har stoppats och instanser där den första säkerhets kopieringen väntar. De värden som stöds är "skyddade", "ProtectionStopped", "InitialBackupPending" eller en kommaavgränsad kombination av dessa värden. Som standard är värdet "*", vilket gör att funktionen söker efter alla säkerhets kopierings instanser oberoende av skydds information. | N | Känslig |
| DatasourceSetName | Använd den här parametern för att filtrera utdata från funktionen till en viss överordnad resurs. Om du till exempel vill returnera SQL i Azure VM backup-instanser som tillhör den virtuella datorn "testvm" anger du _testvm_ som värde för den här parametern. Som standard är värdet "*", vilket gör att funktionen söker efter poster i alla säkerhets kopierings instanser. | N | testvm |
| BackupInstanceName | Använd den här parametern om du vill söka efter en viss säkerhets kopierings instans efter namn. Som standard är värdet "*", vilket gör att funktionen söker efter alla säkerhets kopierings instanser. | N | testvm |
| DisplayAllFields | Använd den här parametern för att välja om du bara vill hämta en delmängd av fälten som returnerades av funktionen. Om värdet för den här parametern är false, eliminerar funktionen lagrings-och kvarhållning relaterad information från utdata från funktionen. Detta är användbart om du använder den här funktionen som ett mellanliggande steg i en större fråga och behöver optimera prestandan för frågan genom att ta bort kolumner som du inte behöver för analys. Som standard är värdet för den här parametern sant, vilket gör att funktionen returnerar alla fält som hör till säkerhets kopierings instansen. | N | true |
| AggregationType | Använd den här parametern för att ange tids kornig het för data som ska hämtas. Om värdet för den här parametern är "daglig", returnerar funktionen en post per säkerhets kopierings instans per dag, så att du kan analysera dagliga trender för lagrings användning och antal säkerhets kopierings instanser. Om värdet för den här parametern är "veckovis", returnerar funktionen en post per säkerhets kopierings instans per vecka, så att du kan analysera vecko trender. På samma sätt kan du ange "Månatlig" för att analysera månads trender. Standardvärdet är "Daily". Om du visar data över större tidsintervall rekommenderar vi att du använder "veckovis" eller "Månatlig" för bättre prestanda och underlättar trend analyser. | N | Veckorna |

**Returnerade fält**

| **Fält namn** | **Beskrivning** |
| -------------- | --------------- |
| UniqueId | Primär nyckel som anger unikt ID för säkerhets kopierings instansen |
| Id | Azure Resource Manager-ID (ARM) för säkerhets kopierings instansen |
| FriendlyName | Eget namn på säkerhets kopierings instansen |
| ProtectionInfo | Information om skydds inställningarna för säkerhets kopierings instansen. Till exempel är det skydd som har kon figurer ATS, skyddet stoppat, första säkerhets kopiering väntar |
| LatestRecoveryPoint | Datum och tid för den senaste återställnings punkten som är kopplad till säkerhets kopierings instansen |
| OldestRecoveryPoint | Datum och tid för den äldsta återställnings punkten som är associerad med säkerhets kopierings instansen |
| SourceSizeInMBs | Klient delens storlek för säkerhets kopierings instansen i MB |
| VaultStore_StorageConsumptionInMBs | Total moln lagring som förbrukas av säkerhets kopierings instansen i valvet – standard nivå |
| DataSourceFriendlyName | Eget namn på den data källa som motsvarar säkerhets kopierings instansen |
| BackupSolution | Säkerhets kopierings lösning som säkerhets kopierings instansen är associerad med. Till exempel Azure VM backup, SQL i Azure VM backup och så vidare. |
| DatasourceType | Typ av data källa som motsvarar säkerhets kopierings instansen. Till exempel "Microsoft. Compute/virtualMachines" |
| DatasourceResourceId | Azure Resource Manager (ARM) ID för den underliggande data källan som motsvarar säkerhets kopierings instansen. Till exempel Azure Resource Manager (ARM) ID för den virtuella datorn |
| DatasourceSetFriendlyName | Eget namn på data källans överordnade resurs (där det är tillämpligt). För till exempel en SQL i en Azure VM-datakälla innehåller det här fältet namnet på den virtuella dator där SQL Database finns |
| DatasourceSetResourceId | Azure Resource Manager (ARM) ID för den överordnade resursens överordnade resurs (där det är tillämpligt). För till exempel en SQL i en Azure VM-datakälla innehåller det här fältet Azure Resource Manager (ARM) ID för den virtuella dator där SQL Database finns |
| DatasourceSetType | Typ av data källans överordnade resurs (där det är tillämpligt). Till exempel för en SAP HANA i Azure VM DataSource, är det här fältet Microsoft. Compute/virtualMachines eftersom den överordnade resursen är en virtuell Azure-dator |
| PolicyName | Namnet på principen som är associerad med säkerhets kopierings instansen |
| PolicyUniqueId | Sekundär nyckel som refererar till principen som är kopplad till säkerhets kopierings instansen  |
| PolicyId | Azure Resource Manager (ARM) ID för principen som är associerad med säkerhets kopierings instansen |
| VaultResourceId | Azure Resource Manager (ARM) ID för valvet som är associerat med säkerhets kopierings instansen |
| VaultUniqueId | Sekundär nyckel som refererar till valvet som är associerat med säkerhets kopierings instansen |
| VaultName | Namnet på valvet som är associerat med säkerhets kopierings instansen |
| VaultTags | Taggar för valvet som är associerat med säkerhets kopierings instansen |
| VaultSubscriptionId | Prenumerations-ID för valvet som är kopplat till säkerhets kopierings instansen |
| VaultLocation | Plats för valvet som är associerat med säkerhets kopierings instansen |
| VaultStore_StorageReplicationType | Typ av lagrings replikering för valvet som är kopplat till säkerhets kopierings instansen |
| VaultType | Typ av valv, som är "Microsoft. RecoveryServices/valv" |
| TimeGenerated | Tidsstämpel för posten |

#### <a name="_azurebackup_getbillinggroupstrends"></a>_AzureBackup_GetBillingGroupsTrends ()

Den här funktionen returnerar historiska poster för varje fakturerings enhet, så att du kan visa viktiga trender, veckovis och månads trender för klient delens storlek och lagrings förbrukning på flera olika nivåer.

**Parametrar**

| **Parameter namn** | **Beskrivning** | **Krävs?** | **Exempel värde** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Använd den här parametern tillsammans med parametern RangeEnd för att hämta alla relaterade poster i fakturerings gruppen under tids perioden från rang Est art till RangeEnd. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | Använd den här parametern tillsammans med en rang-parameter för att hämta alla relaterade poster i fakturerings gruppen under tids perioden från rang Est art till RangeEnd. | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList  | Använd den här parametern för att filtrera resultatet av funktionen för en viss uppsättning prenumerationer där säkerhets kopierings data finns. Om du anger en kommaavgränsad lista med prenumerations-ID: n som en parameter för den här funktionen kan du bara hämta de fakturerings grupper som finns i de angivna prenumerationerna. Som standard är värdet för den här parametern *, vilket gör att funktionen söker efter poster i alla prenumerationer. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Använd den här parametern för att filtrera resultatet av funktionen för en viss uppsättning regioner där det finns säkerhets kopierings data. Genom att ange en kommaavgränsad lista över regioner som en parameter för den här funktionen kan du bara hämta de fakturerings grupper som finns i de angivna regionerna. Som standard är värdet för den här parametern *, vilket gör att funktionen söker efter poster i alla regioner. | N | "öster, väst"|
| VaultList    |Använd den här parametern för att filtrera utdata från funktionen för en viss uppsättning valv. Om du anger en kommaavgränsad lista med valv namn som en parameter för den här funktionen kan du hämta poster för säkerhets kopierings instanser som bara rör de angivna valvena. Som standard är värdet för den här parametern *, vilket gör att funktionen söker efter poster i fakturerings grupper i alla valv. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Använd den här parametern för att filtrera utdata från funktionen till poster som hör till en viss valv typ. För närvarande är den enda typen av valv som stöds "Microsoft. RecoveryServices/valv", vilket är standardvärdet för den här parametern. | N | "Microsoft. RecoveryServices/valv"|
| ExcludeLegacyEvent  | Använd den här parametern för att välja om du vill fråga efter data i den äldre AzureDiagnostics-tabellen eller inte. Om värdet för den här parametern är falskt, frågar funktionen data från både AzureDiagnostics-tabellen och de resursbaserade tabellerna. Om värdet för den här parametern är sant, frågar funktionen data från de resursbaserade tabellerna. Standardvärdet är True. | N | true |
| BackupSolutionList | Använd den här parametern för att filtrera resultatet av funktionen för en viss uppsättning säkerhets kopierings lösningar som används i din Azure-miljö. Om du till exempel anger "säkerhets kopiering av virtuella Azure-datorer, SQL i Azure VM backup, DPM" som värde för den här parametern, returnerar funktionen bara poster som är relaterade till objekt som säkerhets kopie ras med säkerhets kopiering av virtuella Azure-datorer, SQL i Azure VM Backup eller DPM till Azure Backup. Som standard är värdet för den här parametern "*", som gör att funktionen returnerar poster som hör till alla säkerhets kopierings lösningar som stöds av säkerhets kopierings rapporter (de värden som stöds är "säkerhets kopiering av virtuella Azure-datorer", "SQL i Azure VM Backup", "SAP HANA i Azure VM Backup", "Azure Storage (Azure Files) säkerhets kopiering", "Azure Backup Agent", "DPM", "Azure Backup Server" eller en kommaavgränsad kombination av något av dessa värden). | N | "Säkerhets kopiering av virtuella Azure-datorer, SQL i Azure VM backup, DPM, Azure Backup Agent" |
| BillingGroupName | Använd den här parametern om du vill söka efter en viss fakturerings grupp efter namn. Som standard är värdet "*", vilket gör att funktionen söker efter alla fakturerings grupper. | N | testvm |
| AggregationType | Använd den här parametern för att ange tids kornig het för data som ska hämtas. Om värdet för den här parametern är "daglig", returnerar funktionen en post per fakturerings grupp per dag, så att du kan analysera dagliga trender för lagrings förbrukning och storlek på klient delen. Om värdet för den här parametern är "veckovis", returnerar funktionen en post per säkerhets kopierings instans per vecka, så att du kan analysera vecko trender. På samma sätt kan du ange "Månatlig" för att analysera månads trender. Standardvärdet är "Daily". Om du visar data över större tidsintervall rekommenderar vi att du använder "veckovis" eller "Månatlig" för bättre prestanda och underlättar trend analyser. | N | Veckorna |

**Returnerade fält**

| **Fält namn** | **Beskrivning** |
| -------------- | --------------- |
| UniqueId | Primär nyckel som anger unikt ID för fakturerings gruppen |
| FriendlyName | Eget namn på fakturerings gruppen |
| Name | Namnet på fakturerings gruppen |
| Typ | Typ av fakturerings grupp. Till exempel ProtectedContainer eller BackupItem |
| SourceSizeInMBs | Klient delens storlek för fakturerings gruppen i MB |
| VaultStore_StorageConsumptionInMBs | Total moln lagring som förbrukas av fakturerings gruppen i valvet – standard nivå |
| BackupSolution | Säkerhets kopierings lösning som fakturerings gruppen är associerad med. Till exempel Azure VM backup, SQL i Azure VM backup och så vidare. |
| VaultResourceId | Azure Resource Manager (ARM) ID för valvet som är associerat med fakturerings gruppen |
| VaultUniqueId | Sekundär nyckel som refererar till valvet som är kopplat till fakturerings gruppen |
| VaultName | Namn på valvet som är associerat med fakturerings gruppen |
| VaultTags | Taggar för valvet som associeras med fakturerings gruppen |
| VaultSubscriptionId | Prenumerations-ID för valvet som är associerat med fakturerings gruppen |
| VaultLocation | Plats för valvet som är associerat med fakturerings gruppen |
| VaultStore_StorageReplicationType | Typ av lagrings replikering för valvet som är associerat med fakturerings gruppen |
| VaultType | Typ av valv, som är "Microsoft. RecoveryServices/valv" |
| TimeGenerated | Tidsstämpel för posten |
| ExtendedProperties | Ytterligare egenskaper för fakturerings gruppen |

## <a name="sample-queries"></a>Exempel frågor

Nedan visas några exempel frågor som hjälper dig att komma igång med att använda system funktioner.

- Alla misslyckade virtuella Azure VM-jobb inom ett angivet tidsintervall

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06") //call function with RangeStart and RangeEnd parameters set, and other parameters with default value
    | where BackupSolution=="Azure Virtual Machine Backup" and Status=="Failed"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- Alla säkerhets kopierings jobb för SQL-logg inom ett angivet tidsintervall

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06","*","*","*","*",true,"*","*","*","*","*","*",false) //call function with RangeStart and RangeEnd parameters set, ExcludeLog parameter as false, and other parameters with default value
    | where BackupSolution=="SQL in Azure VM Backup" and Operation=="Log"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- Veckovis trend för säkerhets kopierings lagring som förbrukas för den virtuella datorn "testvm"

    ````Kusto
    _AzureBackup_GetBackupInstancesTrends("2021-01-01", "2021-03-06","*","*","*","*",false,"*","*","*","*",true, "Weekly") //call function with RangeStart and RangeEnd parameters set, AggregationType parameter as Weekly, and other parameters with default value
    | where BackupSolution == "Azure Virtual Machine Backup"
    | where FriendlyName == "testvm"
    | project TimeGenerated, VaultStore_StorageConsumptionInMBs
    | render timechart 
    ````

## <a name="next-steps"></a>Nästa steg
[Läs mer om Backup-rapporter](./configure-reports.md)