---
title: Regelbunden säkerhets kopiering/återställning i fristående Azure-Service Fabric
description: Använd en fristående Service Fabric regelbunden säkerhets kopierings-och återställnings funktion för att aktivera regelbunden säkerhets kopiering av program data.
ms.topic: conceptual
ms.date: 5/24/2019
ms.openlocfilehash: d78a627c0c50a3e2ec57138e40cb5bc97486d6f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103198696"
---
# <a name="periodic-backup-and-restore-in-a-standalone-service-fabric"></a>Periodisk säkerhets kopiering och återställning i en fristående Service Fabric
> [!div class="op_single_selector"]
> * [Kluster i Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Fristående kluster](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric är en distribuerad system plattform som gör det enkelt att utveckla och hantera pålitliga, distribuerade och mikrotjänster-baserade moln program. Det tillåter körning av både tillstånds lösa och tillstånds känsliga Micro-tjänster. Tillstånds känsliga tjänster kan upprätthålla föränderligt, auktoritativt tillstånd bortom begäran och svar eller en fullständig transaktion. Om en tillstånds känslig tjänst kraschar under en längre tid eller förlorar information på grund av en katastrof, kan det behöva återställas till en ny säkerhets kopia av sitt tillstånd för att fortsätta tillhandahålla tjänsten när den har säkerhetskopierats.

Service Fabric replikerar tillstånden över flera noder för att säkerställa att tjänsten är hög tillgänglig. Även om en nod i klustret Miss lyckas, fortsätter tjänsten att vara tillgänglig. I vissa fall är det dock önskvärt att tjänst data är tillförlitliga mot bredare haverier.
 
En tjänst kan till exempel behöva säkerhetskopiera sina data för att skydda från följande scenarier:
- Permanent förlust av ett helt Service Fabric-kluster.
- Permanent förlust av en majoritet av en tjänstepartitions repliker
- Administrativa fel där tillstånden oavsiktligt tas bort eller skadas. En administratör med tillräcklig behörighet tar till exempel bort tjänsten felaktigt.
- Buggar i tjänsten som orsakar datafel. Detta kan till exempel inträffa när en tjänst kods uppgradering börjar skriva felaktiga data till en tillförlitlig samling. I sådana fall kan både koden och data måste återställas till ett tidigare tillstånd.
- Data bearbetning offline. Det kan vara praktiskt att ha offline-bearbetning av data för Business Intelligence som sker separat från tjänsten som genererar data.

Service Fabric tillhandahåller en inbyggd API för [säkerhets kopiering och återställning av säkerhets](service-fabric-reliable-services-backup-restore.md)tidpunkt. Programutvecklare kan använda dessa API: er för att säkerhetskopiera tjänstens status regelbundet. Om tjänst administratörer vill utlösa en säkerhets kopia från utanför tjänsten vid en specifik tidpunkt (t. ex. innan du uppgraderar programmet), måste utvecklare Visa säkerhets kopieringen (och återställa) som ett API från tjänsten. Att underhålla säkerhets kopieringarna är ytterligare en kostnad ovanför detta. Till exempel kanske du vill ha fem stegvisa säkerhets kopior varje halvtimme, följt av en fullständig säkerhets kopiering. Efter den fullständiga säkerhets kopieringen kan du ta bort de tidigare stegvisa säkerhets kopiorna. Den här metoden kräver ytterligare kod som leder till ytterligare kostnad under program utvecklingen.

Säkerhets kopiering av program data på regelbunden basis är ett grundläggande behov av att hantera ett distribuerat program och skydda mot förlust av data eller långvarig förlust av tjänst tillgänglighet. Service Fabric tillhandahåller en valfri säkerhets kopierings-och återställnings tjänst som gör att du kan konfigurera regelbunden säkerhets kopiering av tillstånds känsliga Reliable Services (inklusive aktör tjänster) utan att behöva skriva någon ytterligare kod. Den gör det också lättare att återställa tidigare säkerhets kopior. 

Service Fabric innehåller en uppsättning API: er för att uppnå följande funktioner som rör regelbunden säkerhets kopiering och återställning:

- Schemalägg regelbunden säkerhets kopiering av pålitliga tillstånds känsliga tjänster och Reliable Actors med stöd för att överföra säkerhets kopior till (externa) lagrings platser. Lagrings platser som stöds
    - Azure Storage
    - Fil resurs (lokalt)
- Räkna upp säkerhets kopior
- Utlös en oplanerad säkerhets kopiering av en partition
- Återställa en partition med hjälp av tidigare säkerhets kopiering
- Tillfälligt inaktivera säkerhets kopieringar
- Bevarande hantering av säkerhets kopior (kommande)

## <a name="prerequisites"></a>Förutsättningar
* Service Fabric kluster med Fabric version 6,4 eller senare. Läs den här [artikeln](service-fabric-cluster-creation-for-windows-server.md) för steg för att ladda ned det nödvändiga paketet.
* X. 509-certifikat för kryptering av hemligheter som behövs för att ansluta till lagring för att lagra säkerhets kopior. Se [artikeln](service-fabric-windows-cluster-x509-security.md) för att lära dig hur du hämtar eller skapar ett självsignerat X. 509-certifikat.

* Service Fabric tillförlitligt tillstånds känsligt program som skapats med Service Fabric SDK version 3,0 eller senare. För program som är riktade till .NET Core 2,0 bör programmet skapas med hjälp av Service Fabric SDK version 3,1 eller senare.
* Installera Microsoft. ServiceFabric. PowerShell. http-modulen (för hands version) för att göra konfigurations anrop.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.PowerShell.Http -AllowPrerelease
```

> [!NOTE]
> Om din PowerShellGet-version är mindre än 1.6.0 måste du uppdatera för att lägga till stöd för flaggan *-AllowPrerelease* :
>
> `Install-Module -Name PowerShellGet -Force`

* Kontrol lera att klustret är anslutet med `Connect-SFCluster` kommandot innan du gör någon konfigurations förfrågan med hjälp av Microsoft. ServiceFabric. PowerShell. http-modulen.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Aktiverar säkerhets kopierings-och återställnings tjänsten
Först måste du aktivera _säkerhets kopierings-och återställnings tjänsten_ i klustret. Hämta mallen för det kluster som du vill distribuera. Du kan använda [exempel mallarna](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples). Aktivera _säkerhets kopierings-och återställnings tjänsten_ med följande steg:

1. Kontrol lera att `apiversion` är inställt på `10-2017` i kluster konfigurations filen och uppdatera den enligt följande kodfragment:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Aktivera _säkerhets kopierings-och återställnings tjänsten_ genom att lägga till följande `addonFeatures` avsnitt under `properties` avsnittet som visas i följande kodfragment: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Konfigurera X. 509-certifikat för kryptering av autentiseringsuppgifter. Detta är viktigt för att se till att de angivna autentiseringsuppgifterna för att ansluta till lagring krypteras innan de sparas. Konfigurera krypterings certifikat genom att lägga till följande `BackupRestoreService` avsnitt under `fabricSettings` avsnittet som visas i följande kodfragment: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            },
            {
                "name": "SecretEncryptionCertX509StoreName",
                "value": "My"
            }]
        }
        ...
    }
    ```

4. När du har uppdaterat kluster konfigurations filen med föregående ändringar, tillämpar du dem och låter distributionen/uppgraderingen slutföras. När du är klar börjar _säkerhets kopierings-och återställnings tjänsten_ att köras i klustret. URI: n för den här tjänsten är `fabric:/System/BackupRestoreService` och tjänsten kan finnas i avsnittet system service i Service Fabric Explorer. 



## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Aktivera regelbunden säkerhets kopiering för tillförlitliga tillstånds känsliga tjänster och Reliable Actors
Låt oss gå igenom stegen för att aktivera regelbunden säkerhets kopiering för tillförlitliga tillstånds känsliga tjänster och Reliable Actors. De här stegen förutsätter
- Klustret har kon figurer ATS med säkerhets kopierings-och återställnings service_.
- En tillförlitlig tillstånds känslig tjänst har distribuerats i klustret. I den här snabb starts guiden är program-URI `fabric:/SampleApp` och URI för tillförlitlig tillstånds känslig tjänst som tillhör det här programmet `fabric:/SampleApp/MyStatefulService` . Den här tjänsten distribueras med en enda partition och partitions-ID: t är `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7` .  

### <a name="create-backup-policy"></a>Skapa säkerhets kopierings princip

Det första steget är att skapa en säkerhets kopierings princip som beskriver säkerhets kopierings schema, mål lagring för säkerhets kopierings data, princip namn, maximala stegvisa säkerhets kopior som ska tillåtas innan fullständig säkerhets kopiering och bevarande princip för säkerhets kopierings lagringen utlöses. 

För säkerhets kopierings lagring skapar du fil resurs och ger ReadWrite åtkomst till den här fil resursen för alla Service Fabric Node-datorer. Det här exemplet förutsätter att resursen med namnet finns `BackupStore` på `StorageServer` .


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med hjälp av modulen Microsoft. ServiceFabric. PowerShell. http

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -FileShare -Path '\\StorageServer\BackupStore' -Basic -RetentionDuration '10.00:00:00'

```
#### <a name="rest-call-using-powershell"></a>Rest-anrop med PowerShell

Kör följande PowerShell-skript för att anropa nödvändiga REST API för att skapa en ny princip.

```powershell
$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}   

$StorageInfo = @{
    Path = '\\StorageServer\BackupStore'
    StorageKind = 'FileShare'
}

$RetentionPolicy = @{ 
    RetentionPolicyType = 'Basic'
    RetentionDuration =  'P10D'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
    RetentionPolicy = $RetentionPolicy
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "http://localhost:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
```

#### <a name="using-service-fabric-explorer"></a>Använda Service Fabric Explorer

1. I Service Fabric Explorer går du till fliken säkerhets kopior och väljer åtgärder > skapa säkerhets kopierings princip.

    ![Skapa säkerhets kopierings princip][6]

2. Fyll i informationen. För fristående kluster ska FileShare väljas.

    ![Skapa säkerhets kopierings principens FileShare][7]

### <a name="enable-periodic-backup"></a>Aktivera periodisk säkerhets kopiering
När du har definierat principen för att uppfylla data skydds kraven för programmet ska säkerhets kopierings principen associeras med programmet. Beroende på krav kan säkerhets kopierings policyn associeras med ett program, en tjänst eller en partition.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med hjälp av modulen Microsoft. ServiceFabric. PowerShell. http

```powershell
Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'
```

#### <a name="rest-call-using-powershell"></a>Rest-anrop med PowerShell
Kör följande PowerShell-skript för att anropa nödvändiga REST API för att associera en säkerhets kopierings princip med ett namn som `BackupPolicy1` skapats i ovanstående steg med programmet `SampleApp` .

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

#### <a name="using-service-fabric-explorer"></a>Använda Service Fabric Explorer

1. Välj ett program och gå till åtgärd. Klicka på Aktivera/uppdatera program säkerhets kopiering.

    ![Aktivera program säkerhets kopiering][3] 

2. Välj slutligen önskad princip och välj *Aktivera säkerhets kopiering*.

    ![Välj princip][4]

### <a name="verify-that-periodic-backups-are-working"></a>Verifiera att regelbundna säkerhets kopieringar fungerar

När du har aktiverat säkerhets kopiering för programmet börjar alla partitioner som tillhör pålitliga tillstånds känsliga tjänster och Reliable Actors under programmet att bli säkerhetskopierade regelbundet enligt den associerade säkerhets kopierings principen.

![Partitionera säkerhetskopierade hälso händelse][0]

### <a name="list-backups"></a>Lista säkerhets kopior

Säkerhets kopieringar som är kopplade till alla partitioner som tillhör pålitliga tillstånds känsliga tjänster och Reliable Actors av programmet kan räknas upp med _GetBackups_ -API. Beroende på kraven kan säkerhets kopiorna räknas upp för program, tjänster eller en partition.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med hjälp av modulen Microsoft. ServiceFabric. PowerShell. http

```powershell
    Get-SFApplicationBackupList -ApplicationId WordCount     
```

#### <a name="rest-call-using-powershell"></a>Rest-anrop med PowerShell

Kör följande PowerShell-skript för att anropa HTTP API för att räkna upp de säkerhets kopior som skapats för alla partitioner i `SampleApp` programmet.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Exempel på utdata för körningen ovan:

```
BackupId                : d7e4038e-2c46-47c6-9549-10698766e714
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.39.40.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2058
CreationTimeUtc         : 2018-04-01T19:39:40Z
FailureError            : 

BackupId                : 8c21398a-2141-4133-b4d7-e1a35f0d7aac
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.54.38.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2237
CreationTimeUtc         : 2018-04-01T19:54:38Z
FailureError            : 

BackupId                : fc75bd4c-798c-4c9a-beee-e725321f73b2
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 20.09.44.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2437
CreationTimeUtc         : 2018-04-01T20:09:44Z
FailureError            : 
```

#### <a name="using-service-fabric-explorer"></a>Använda Service Fabric Explorer

Om du vill visa säkerhets kopior i Service Fabric Explorer navigerar du till en partition och väljer fliken säkerhets kopior.

![Räkna upp säkerhets kopior][5]

## <a name="limitation-caveats"></a>Begränsning/varningar
- Service Fabric PowerShell-cmdletar är i förhands gransknings läge.
- Inget stöd för Service Fabric kluster i Linux.

## <a name="next-steps"></a>Nästa steg
- [Förstå konfiguration av regelbunden säkerhetskopiering](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [REST API referens för säkerhets kopierings återställning](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/create-bp-fileshare.png
