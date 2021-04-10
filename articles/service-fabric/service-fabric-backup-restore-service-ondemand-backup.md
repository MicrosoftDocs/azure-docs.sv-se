---
title: Säkerhets kopiering på begäran i Azure Service Fabric
description: Använd säkerhets kopierings-och återställnings funktionen i Service Fabric för att säkerhetskopiera dina program data efter behov.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: d7986c8cd8d0714215c7b4dc57170be346e627ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98928037"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Säkerhets kopiering på begäran i Azure Service Fabric

Du kan säkerhetskopiera data för pålitliga tillstånds känsliga tjänster och Reliable Actors för att lösa haveri-eller data förlust scenarier.

Azure Service Fabric har funktioner för [regelbunden säkerhets kopiering av data](service-fabric-backuprestoreservice-quickstart-azurecluster.md) och säkerhets kopiering av data baserat på behov. Säkerhets kopiering på begäran är användbart eftersom det skyddar mot _data förlust av data_ på /  grund av planerade ändringar i den underliggande tjänsten eller dess miljö.

Funktionerna för säkerhets kopiering på begäran är användbara för att fånga tjänsternas status innan du aktiverar en tjänst-eller service miljö-åtgärd manuellt. Om du till exempel gör en ändring i binärfilerna för tjänsten när du uppgraderar eller nedgraderar tjänsten. I sådana fall kan säkerhets kopiering på begäran hjälpa till att skydda data mot skador genom program kod fel.
## <a name="prerequisites"></a>Förutsättningar

- Installera Microsoft. ServiceFabric. PowerShell. http-modulen (för hands version) för att göra konfigurations anrop.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

> [!NOTE]
> Om din PowerShellGet-version är mindre än 1.6.0 måste du uppdatera för att lägga till stöd för flaggan *-AllowPrerelease* :
>
> `Install-Module -Name PowerShellGet -Force`

- Kontrol lera att klustret är anslutet med `Connect-SFCluster` kommandot innan du gör någon konfigurations förfrågan med hjälp av Microsoft. ServiceFabric. PowerShell. http-modulen.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggering-on-demand-backup"></a>Utlösa säkerhets kopiering på begäran

Säkerhets kopiering på begäran kräver lagrings information för att ladda upp säkerhetskopierade filer. Du anger platsen för säkerhets kopiering på begäran, antingen i den periodiska säkerhets kopierings principen eller i en begäran om säkerhets kopiering på begäran.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>Säkerhets kopiering på begäran till lagring som anges av en princip för periodisk säkerhets kopiering

Du kan konfigurera den periodiska säkerhets kopierings policyn så att den använder en partition av en tillförlitlig tillstånds känslig tjänst eller tillförlitlig aktör för ytterligare säkerhets kopiering på begäran till lagringen.

I följande fall är det en fortsättning av scenariot för att [Aktivera regelbunden säkerhets kopiering för tillförlitliga tillstånds känsliga tjänster och Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). I det här fallet aktiverar du en säkerhets kopierings princip för att använda en partition och en säkerhets kopiering sker med en angiven frekvens i Azure Storage.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med hjälp av modulen Microsoft. ServiceFabric. PowerShell. http

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' 

```

#### <a name="rest-call-using-powershell"></a>Rest-anrop med PowerShell

Använd [BackupPartition](/rest/api/servicefabric/sfclient-api-backuppartition) -API: et för att konfigurera utlösare för säkerhets kopiering på begäran för PARTITIONS-ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22` .

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Använd [GetBackupProgress](/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) -API: et för att aktivera spårning för [säkerhets kopiering på begäran](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="on-demand-backup-to-specified-storage"></a>Säkerhets kopiering på begäran till den angivna lagrings platsen

Du kan begära säkerhets kopiering på begäran för en partition av en tillförlitlig tillstånds känslig tjänst eller tillförlitlig aktör. Ange lagrings informationen som en del av begäran om säkerhets kopiering på begäran.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med hjälp av modulen Microsoft. ServiceFabric. PowerShell. http

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -AzureBlobStore -ConnectionString  'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Rest-anrop med PowerShell

Använd [BackupPartition](/rest/api/servicefabric/sfclient-api-backuppartition) -API: et för att konfigurera utlösare för säkerhets kopiering på begäran för PARTITIONS-ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22` . Ta med följande Azure Storage information:

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$OnDemandBackupRequest = @{
    BackupStorage = $StorageInfo
}

$body = (ConvertTo-Json $OnDemandBackupRequest)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Du kan använda [GetBackupProgress](/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) -API: et för att ställa in spårning för [säkerhets kopiering på begäran](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="using-service-fabric-explorer"></a>Använda Service Fabric Explorer
Kontrol lera att Avancerat läge har Aktiver ATS i Service Fabric Explorer inställningar.
1. Välj önskade partitioner och klicka på åtgärder. 
2. Välj Utlös säkerhets kopiering av partition och fyll i information för Azure:

    ![Utlös säkerhets kopiering av partition][0]

    eller FileShare:

    ![Utlös partition för säkerhets kopiering av partition][1]

## <a name="tracking-on-demand-backup-progress"></a>Spåra säkerhets kopierings förlopp på begäran

En partition av en tillförlitlig tillstånds känslig tjänst eller tillförlitlig aktör accepterar bara en begäran om säkerhets kopiering på begäran i taget. En annan begäran kan bara godkännas när den aktuella begäran om säkerhets kopiering på begäran har slutförts.

Olika partitioner kan utlösa begär Anden om säkerhets kopiering på begäran på samma tidpunkt.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med hjälp av modulen Microsoft. ServiceFabric. PowerShell. http

```powershell

Get-SFPartitionBackupProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'

```
#### <a name="rest-call-using-powershell"></a>Rest-anrop med PowerShell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Säkerhets kopierings begär Anden på begäran kan ha följande tillstånd:

- **Accepterad**: säkerhets kopieringen har startats på partitionen och pågår.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Lyckad**, **misslyckad** eller **tids gräns**: en begärd säkerhets kopiering på begäran kan utföras i något av följande tillstånd:
  - **Lyckades**: ett _lyckat_ säkerhets kopierings tillstånd indikerar att partitionens tillstånd har säkerhetskopierats. Svaret innehåller _BackupEpoch_ och _BackupLSN_ för partitionen tillsammans med tiden i UTC.
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Fel**: ett tillstånd för _fel_ säkerhets kopiering indikerar att ett fel uppstod under säkerhets kopieringen av partitionens tillstånd. Orsaken till felet anges i svar.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Timeout**: en _timeout_ för säkerhets kopiering indikerar att det inte gick att skapa en säkerhets kopia av partitionens tillstånd inom en specifik tids period. Standardvärdet för timeout är 10 minuter. Initiera en ny begäran om säkerhets kopiering på begäran med större [BackupTimeout](/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) i det här scenariot.
    ```
    BackupState             : Timeout
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
    ```

## <a name="next-steps"></a>Nästa steg

- [Förstå regelbunden konfiguration av säkerhets kopiering](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [BackupRestore REST API referens](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/trigger-partition-backup.png
[1]: ./media/service-fabric-backuprestoreservice/trigger-backup-fileshare.png
