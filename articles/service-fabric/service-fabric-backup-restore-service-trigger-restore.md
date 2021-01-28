---
title: Återställa säkerhets kopia i Azure Service Fabric
description: Använd den periodiska säkerhets kopierings-och återställnings funktionen i Service Fabric för att återställa data från en säkerhets kopia av dina program data.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 8566d82ef0d91caff47ff17a9cb12fcdc8241884
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928022"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Återställa säkerhets kopia i Azure Service Fabric

I Azure Service Fabric kan pålitliga tillstånds känsliga tjänster och Reliable Actors upprätthålla ett föränderligt, auktoritativt tillstånd efter att en begäran och svars transaktion har slutförts. En tillstånds känslig tjänst kan gå ned under en längre tid eller förlora information på grund av en katastrof. Om detta händer måste tjänsten återställas från den senaste acceptabla säkerhets kopian så att den kan fortsätta att fungera.

Du kan till exempel konfigurera en tjänst för att säkerhetskopiera data för att skydda dig mot följande scenarier:

- **Händelse av haveri beredskap**: permanent förlust av ett helt Service Fabric-kluster.
- **Händelse av data förlust**: permanent förlust av en majoritet av replikerna av en tjänstmall.
- **Händelse av data förlust**: oavsiktlig borttagning eller skada på tjänsten. En administratör tar till exempel bort tjänsten felaktigt.
- **Händelse av data skada**: fel i tjänsten orsakar skadade data. Data skada kan till exempel inträffa när en tjänst kods uppgradering skriver felaktiga data till en tillförlitlig samling. I sådana fall måste du kanske återställa både koden och data till ett tidigare tillstånd.

## <a name="prerequisites"></a>Förutsättningar

- För att utlösa en återställning måste _fel analys tjänsten (fas)_ vara aktive rad för klustret.
- Säkerhets _kopieringen av BRS (Backup Restore service)_ skapade säkerhets kopian.
- Återställningen kan bara utlösas på en partition.
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


## <a name="triggered-restore"></a>Utlöst återställning

En återställning kan utlösas i följande scenarier:

- Data återställning för _haveri beredskap_.
- Data återställning för _skadade data/data förlust_.

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Data återställning vid haveri beredskap

Om ett helt Service Fabric-kluster förloras kan du återställa data för partitionerna för den tillförlitliga tillstånds känsliga tjänsten och Reliable Actors. Du kan välja önskad säkerhets kopia i listan när du använder [GetBackupAPI med information om lagrings utrymme för säkerhets kopior](/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). Uppräkningen av säkerhets kopiering kan vara för ett program, en tjänst eller en partition.

I följande exempel förutsätter vi att det förlorade klustret är samma kluster som det hänvisas till i att [Aktivera regelbunden säkerhets kopiering för tillförlitliga tillstånds känsliga tjänster och Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). I det här fallet `SampleApp` distribueras med säkerhets kopierings principen aktive rad och säkerhets kopiorna konfigureras för att Azure Storage.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med hjälp av modulen Microsoft. ServiceFabric. PowerShell. http

```powershell
Get-SFBackupsFromBackupLocation -Application -ApplicationName 'fabric:/SampleApp' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Rest-anrop med PowerShell

Kör ett PowerShell-skript för att använda REST API för att returnera en lista över de säkerhets kopior som har skapats för alla partitioner i `SampleApp` programmet. API: t kräver säkerhets kopierings lagrings informationen för att visa en lista över tillgängliga säkerhets kopior.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$BackupEntity = @{
    EntityKind = 'Application'
    ApplicationName='fabric:/SampleApp'
}

$BackupLocationAndEntityInfo = @{
    Storage = $StorageInfo
    BackupEntity = $BackupEntity
}

$body = (ConvertTo-Json $BackupLocationAndEntityInfo)
$url = "https://myalternatesfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Exempel på utdata för körningen ovan:

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 
*
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
*
BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            :
```

Välj en av säkerhets kopieringarna för att utlösa återställningen. Den aktuella säkerhets kopian för haveri beredskap kan till exempel vara följande säkerhets kopia:

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

För återställnings-API: et måste du ange information om _BackupId_ och _BackupLocation_ .

Du måste också välja en måldisk i det alternativa klustret som beskrivs i [partitionsfunktionen](service-fabric-concepts-partitioning.md#get-started-with-partitioning). Den alternativa kluster säkerhets kopian återställs till partitionen som anges i partitionsfunktionen från det ursprungliga förlorade klustret.

Om partitions-ID: t i det alternativa klustret är `1c42c47f-439e-4e09-98b9-88b8f60800c6` , kan du mappa det till det ursprungliga partitions-ID: t `974bd92a-b395-4631-8a7f-53bd4ae9cf22` genom att jämföra hög nyckel och låg nyckel för _intervall partitionering (UniformInt64Partition)_.

För _namngiven partitionering_ jämförs name-värdet med att identifiera partitionen i ett alternativt kluster.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med hjälp av modulen Microsoft. ServiceFabric. PowerShell. http

```powershell

Restore-SFPartition  -PartitionId '1c42c47f-439e-4e09-98b9-88b8f60800c6' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Rest-anrop med PowerShell

Du begär återställningen mot säkerhets kopierings klustrets partition genom att använda följande [återställnings-API](/rest/api/servicefabric/sfclient-api-restorepartition):

```powershell

$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
    BackupStorage  = $StorageInfo
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Du kan följa förloppet för en återställning med TrackRestoreProgress.

> [!NOTE]
> När du använder PowerShell för att återställa partitionen, om BackupLocation har $, Escape-filen med ~
>

### <a name="using-service-fabric-explorer"></a>Använda Service Fabric Explorer
Du kan utlösa en återställning från Service Fabric Explorer. Kontrol lera att Avancerat läge har Aktiver ATS i Service Fabric Explorer inställningar.
1. Välj önskade partitioner och klicka på åtgärder. 
2. Välj Utlös partition återställning och fyll i information för Azure:

    ![Utlös partition återställning][2]

    eller FileShare:

    ![Utlös partition för återställning av partition][3]

### <a name="data-restore-for-_data-corruption__data-loss_"></a>Data återställning för data / _förlust_ av data skada

För _data förlust_ eller _skadade data_ kan säkerhetskopierade partitioner för tillförlitliga tillstånds känsliga tjänster och Reliable Actors partitioner återställas till någon av de valda säkerhets kopiorna.

Följande exempel är en fortsättning av [aktivering av regelbunden säkerhets kopiering för tillförlitliga tillstånds känsliga tjänster och Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). I det här exemplet är en säkerhets kopierings princip aktive rad för partitionen och tjänsten gör säkerhets kopior enligt önskad frekvens i Azure Storage.

Välj en säkerhets kopia från utdata från  [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). I det här scenariot genereras säkerhets kopieringen från samma kluster som tidigare.

Om du vill utlösa återställningen väljer du en säkerhets kopia i listan.  / Välj följande säkerhets kopia för skadade data förlust _data_:

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

Ange information om _BackupId_ och _BackupLocation_ för ÅTERställnings-API: et. Klustret har säkerhets kopiering aktive rad så Service Fabric _BRS (Backup Restore service)_ identifierar rätt lagrings plats från den tillhör ande säkerhets kopierings principen.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med hjälp av modulen Microsoft. ServiceFabric. PowerShell. http

```powershell
Restore-SFPartition  -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'

```

#### <a name="rest-call-using-powershell"></a>Rest-anrop med PowerShell

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Du kan spåra återställnings förloppet med hjälp av TrackRestoreProgress.

> [!NOTE]
> När du använder PowerShell för att återställa partitionen, om BackupLocation har $, Escape-filen med ~
>

## <a name="track-restore-progress"></a>Spåra återställnings förlopp

En partition av en tillförlitlig tillstånds känslig tjänst eller tillförlitlig aktör accepterar bara en Restore-begäran i taget. En partition accepterar bara en annan begäran när den aktuella återställnings förfrågan har slutförts. Flera återställnings begär Anden kan utlösas på olika partitioner samtidigt.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med hjälp av modulen Microsoft. ServiceFabric. PowerShell. http

```powershell
    Get-SFPartitionRestoreProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'
```

#### <a name="rest-call-using-powershell"></a>Rest-anrop med PowerShell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

Återställnings förfrågan fortskrider i följande ordning:

1. **Godkänd**: ett _godkänt_ återställnings tillstånd indikerar att den begärda partitionen har utlösts av korrekta parametrar för begäran.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **Pågår**: ett status återställnings tillstånd indikerar att _en återställning sker_ i partitionen med den säkerhets kopia som nämns i begäran. Partitionen rapporterar _DataLoss_ -tillstånd.
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Lyckad**, **misslyckad** eller **tids gräns**: en begärd återställning kan utföras i något av följande tillstånd. Varje tillstånd har följande betydelse-och svars information:
    - **Lyckades**: ett återställnings läge med återställnings tillstånd indikerar att ett reaktivt partitionsschema har _gjorts_ . Partitionen rapporterar _RestoredEpoch_ -och _RestoredLSN_ -tillstånd tillsammans med tiden i UTC.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Failure**: status för återställnings _försök_ indikerar att återställnings förfrågan Miss lyckas. Orsaken till felet rapporteras.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Timeout**: ett tillstånd _för timeout_ -återställning indikerar att begäran har en tids gräns. Skapa en ny Restore-begäran med fler [RestoreTimeout](/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout). Standardvärdet för timeout är 10 minuter. Se till att partitionen inte är i ett data förlust tillstånd innan du begär återställning igen.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>Automatisk återställning

Du kan konfigurera pålitliga tillstånds känsliga tjänster och Reliable Actors partitioner i Service Fabric-klustret för _Automatisk återställning_. I säkerhets kopierings principen har angetts `AutoRestore` till _True_. Aktivering av _Automatisk återställning_ återställer automatiskt data från den senaste säkerhets kopian när data går förlorade. Mer information finns i:

- [Automatisk återställnings aktivering i säkerhets kopierings princip](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [RestorePartition API-referens](/rest/api/servicefabric/sfclient-api-restorepartition)
- [GetPartitionRestoreProgress API-referens](/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Nästa steg
- [Förstå konfiguration av regelbunden säkerhetskopiering](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [REST API referens för säkerhets kopierings återställning](/rest/api/servicefabric/sfclient-index-backuprestore)

[2]: ./media/service-fabric-backuprestoreservice/restore-partition-backup.png
[3]: ./media/service-fabric-backuprestoreservice/restore-partition-fileshare.png
