---
title: Regelbunden säkerhetskopiering och återställning i Azure Service Fabric
description: Använd Service Fabric periodiska säkerhets kopierings-och återställnings funktionen för att aktivera regelbunden data säkerhets kopiering av program data.
ms.topic: conceptual
ms.date: 5/24/2019
ms.openlocfilehash: 42097b50277e78b3f0e8f5e61a2bf70cc08dbc02
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103198718"
---
# <a name="periodic-backup-and-restore-in-an-azure-service-fabric-cluster"></a>Periodisk säkerhets kopiering och återställning i ett Azure Service Fabric-kluster
> [!div class="op_single_selector"]
> * [Kluster i Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Fristående kluster](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric är en distribuerad system plattform som gör det enkelt att utveckla och hantera pålitliga, distribuerade och mikrotjänster baserade moln program. Det tillåter körning av både tillstånds lösa och tillstånds känsliga Micro-tjänster. Tillstånds känsliga tjänster kan upprätthålla föränderligt, auktoritativt tillstånd bortom begäran och svar eller en fullständig transaktion. Om en tillstånds känslig tjänst kraschar under en längre tid eller förlorar information på grund av en katastrof, kan det behöva återställas till en ny säkerhets kopia av sitt tillstånd för att fortsätta tillhandahålla tjänsten när den har säkerhetskopierats.

Service Fabric replikerar tillstånden över flera noder för att säkerställa att tjänsten är hög tillgänglig. Även om en nod i klustret Miss lyckas, fortsätter tjänsten att vara tillgänglig. I vissa fall är det dock önskvärt att tjänst data är tillförlitliga mot bredare haverier.
 
Till exempel kanske tjänsten vill säkerhetskopiera sina data för att skydda dig från följande scenarier:
- I händelse av permanent förlust av ett helt Service Fabric-kluster.
- Permanent förlust av en majoritet av en tjänstepartitions repliker
- Administrativa fel där tillstånden oavsiktligt tas bort eller skadas. En administratör med tillräcklig behörighet tar till exempel bort tjänsten felaktigt.
- Buggar i tjänsten som orsakar datafel. Detta kan till exempel inträffa när en tjänst kods uppgradering börjar skriva felaktiga data till en tillförlitlig samling. I sådana fall kan både koden och data måste återställas till ett tidigare tillstånd.
- Data bearbetning offline. Det kan vara praktiskt att ha offline-bearbetning av data för Business Intelligence som sker separat från tjänsten som genererar data.

Service Fabric tillhandahåller ett inbyggt API för att göra en [säkerhets kopierings-och återställnings](service-fabric-reliable-services-backup-restore.md)tidpunkt. Programutvecklare kan använda dessa API: er för att säkerhetskopiera tjänstens status regelbundet. Om tjänst administratörer vill utlösa en säkerhets kopia från utanför tjänsten vid en specifik tidpunkt, t. ex. innan du uppgraderar programmet, måste utvecklare exponera säkerhets kopiering (och återställning) som ett API från tjänsten. Att underhålla säkerhets kopieringarna är ytterligare en kostnad ovanför detta. Till exempel kanske du vill ha fem stegvisa säkerhets kopior varje halvtimme, följt av en fullständig säkerhets kopiering. Efter den fullständiga säkerhets kopieringen kan du ta bort de tidigare stegvisa säkerhets kopiorna. Den här metoden kräver ytterligare kod som leder till ytterligare kostnad under program utvecklingen.

Säkerhets kopierings-och återställnings tjänsten i Service Fabric möjliggör enkel och automatisk säkerhets kopiering av information som lagras i tillstånds känsliga tjänster. Att säkerhetskopiera program data på regelbunden basis är grundläggande för att skydda mot data förlust och tjänster som inte är tillgängliga. Service Fabric tillhandahåller en valfri säkerhets kopierings-och återställnings tjänst som gör att du kan konfigurera regelbunden säkerhets kopiering av tillstånds känsliga Reliable Services (inklusive aktör tjänster) utan att behöva skriva någon ytterligare kod. Den gör det också lättare att återställa tidigare säkerhets kopior. 


Service Fabric innehåller en uppsättning API: er för att uppnå följande funktioner som rör regelbunden säkerhets kopiering och återställning:

- Schemalägg regelbunden säkerhets kopiering av pålitliga tillstånds känsliga tjänster och Reliable Actors med stöd för att överföra säkerhets kopior till (externa) lagrings platser. Lagrings platser som stöds
    - Azure Storage
    - Fil resurs (lokalt)
- Räkna upp säkerhets kopior
- Utlösa en ad hoc-säkerhetskopiering av en partition
- Återställa en partition med hjälp av tidigare säkerhets kopiering
- Tillfälligt inaktivera säkerhets kopieringar
- Bevarande hantering av säkerhets kopior (kommande)

## <a name="prerequisites"></a>Förutsättningar
* Service Fabric kluster med Fabric version 6,4 eller senare. I den här [artikeln](service-fabric-cluster-creation-via-arm.md) beskrivs steg för steg hur du skapar Service Fabric kluster med hjälp av Azure-resurs mal len.
* X. 509-certifikat för kryptering av hemligheter som behövs för att ansluta till lagring för att lagra säkerhets kopior. Se [artikeln](service-fabric-cluster-creation-via-arm.md) för att lära dig hur du hämtar eller skapar ett X. 509-certifikat.
* Service Fabric tillförlitligt tillstånds känsligt program som skapats med Service Fabric SDK version 3,0 eller senare. För program som är riktade till .NET Core 2,0 ska programmet skapas med hjälp av Service Fabric SDK version 3,1 eller senare.
* Skapa Azure Storage-konto för att lagra säkerhets kopior av program.
* Installera Microsoft. ServiceFabric. PowerShell. http-modulen (för hands version) för att göra konfigurations anrop.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
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

### <a name="using-azure-portal"></a>Använda Azure Portal

Aktivera `Include backup restore service` kryss rutan under `+ Show optional settings` `Cluster Configuration` fliken.

![Aktivera tjänsten för återställning av säkerhets kopiering med portalen][1]


### <a name="using-azure-resource-manager-template"></a>Använda Azure Resource Manager mall
Först måste du aktivera _säkerhets kopierings-och återställnings tjänsten_ i klustret. Hämta mallen för det kluster som du vill distribuera. Du kan antingen använda [exempel mallarna](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) eller skapa en Resource Manager-mall. Aktivera _säkerhets kopierings-och återställnings tjänsten_ med följande steg:

1. Kontrol lera att `apiversion` är inställt på **`2018-02-01`** för `Microsoft.ServiceFabric/clusters` resursen och uppdatera den enligt följande kodfragment:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Aktivera _säkerhets kopierings-och återställnings tjänsten_ genom att lägga till följande `addonFeatures` avsnitt under `properties` avsnittet som visas i följande kodfragment: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Konfigurera X. 509-certifikat för kryptering av autentiseringsuppgifter. Detta är viktigt för att säkerställa att de autentiseringsuppgifter som angetts för att ansluta till lagrings platsen krypteras innan de sparas. Konfigurera krypterings certifikat genom att lägga till följande `BackupRestoreService` avsnitt under `fabricSettings` avsnittet som visas i följande kodfragment: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. När du har uppdaterat kluster mal len med föregående ändringar, tillämpar du dem och låter distributionen/uppgraderingen slutföras. När du är klar börjar _säkerhets kopierings-och återställnings tjänsten_ att köras i klustret. URI: n för den här tjänsten är `fabric:/System/BackupRestoreService` och tjänsten kan finnas i avsnittet system service i Service Fabric Explorer. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Aktivera regelbunden säkerhets kopiering för tillförlitliga tillstånds känsliga tjänster och Reliable Actors
Låt oss gå igenom stegen för att aktivera regelbunden säkerhets kopiering för tillförlitliga tillstånds känsliga tjänster och Reliable Actors. De här stegen förutsätter
- Att klustret har kon figurer ATS med X. 509-säkerhet med _säkerhets kopierings-och återställnings tjänsten_.
- En tillförlitlig tillstånds känslig tjänst har distribuerats i klustret. I den här snabb starts guiden är program-URI `fabric:/SampleApp` och URI för tillförlitlig tillstånds känslig tjänst som tillhör det här programmet `fabric:/SampleApp/MyStatefulService` . Den här tjänsten distribueras med en enda partition och partitions-ID: t är `974bd92a-b395-4631-8a7f-53bd4ae9cf22` .
- Klient certifikatet med administratörs rollen är installerat i _mitt_ (_personliga_) Arkiv namn för _CurrentUser_ på den dator där skripten ska startas. Det här exemplet använder `1b7ebe2174649c45474a4819dafae956712c31d3` som tumavtryck för det här certifikatet. Mer information om klient certifikat finns i [rollbaserad åtkomst kontroll för Service Fabric klienter](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Skapa säkerhets kopierings princip

Det första steget är att skapa en säkerhets kopierings princip som beskriver säkerhets kopierings schema, mål lagring för säkerhets kopierings data, princip namn, maximala stegvisa säkerhets kopior som ska tillåtas innan fullständig säkerhets kopiering och bevarande princip för säkerhets kopierings lagringen utlöses. 

Använd Azure Storage kontot som du skapade ovan för säkerhets kopierings lagring. Container `backup-container` har kon figurer ATS för att lagra säkerhets kopior. En behållare med det här namnet skapas, om den inte redan finns, under uppladdning av säkerhets kopiering. Fyll i `ConnectionString` med en giltig anslutnings sträng för Azure Storage kontot, Ersätt `account-name` med ditt lagrings konto namn och `account-key` med din lagrings konto nyckel.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med hjälp av modulen Microsoft. ServiceFabric. PowerShell. http

Kör följande PowerShell-cmdletar för att skapa en ny säkerhets kopierings princip. Ersätt `account-name` med namnet på ditt lagrings konto och `account-key` med din lagrings konto nyckel.

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container' -Basic -RetentionDuration '10.00:00:00'

```

#### <a name="rest-call-using-powershell"></a>Rest-anrop med PowerShell

Kör följande PowerShell-skript för att anropa nödvändiga REST API för att skapa en ny princip. Ersätt `account-name` med namnet på ditt lagrings konto och `account-key` med din lagrings konto nyckel.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
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
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

```

#### <a name="using-service-fabric-explorer"></a>Använda Service Fabric Explorer

1. I Service Fabric Explorer går du till fliken säkerhets kopior och väljer åtgärder > skapa säkerhets kopierings princip.

    ![Skapa säkerhets kopierings princip][6]

2. Fyll i informationen. För Azure-kluster ska AzureBlobStore väljas.

    ![Skapa säkerhets kopierings policy Azure-Blob Storage][7]

### <a name="enable-periodic-backup"></a>Aktivera periodisk säkerhets kopiering
När du har definierat säkerhets kopierings policy för att uppfylla data skydds kraven för programmet ska säkerhets kopierings principen associeras med programmet. Beroende på krav kan säkerhets kopierings policyn associeras med ett program, en tjänst eller en partition.

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
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

#### <a name="using-service-fabric-explorer"></a>Använda Service Fabric Explorer
Kontrol lera att [Avancerat läge](service-fabric-visualizing-your-cluster.md#backup-and-restore) för Service Fabric Explorer har Aktiver ATS 

1. Välj ett program och gå till åtgärd. Klicka på Aktivera/uppdatera program säkerhets kopiering.

    ![Aktivera program säkerhets kopiering][3]

2. Välj slutligen önskad princip och klicka på Aktivera säkerhets kopiering.

    ![Välj princip][4]


### <a name="verify-that-periodic-backups-are-working"></a>Verifiera att regelbundna säkerhets kopieringar fungerar

När du har aktiverat säkerhets kopiering på program nivå börjar alla partitioner som tillhör pålitliga tillstånds känsliga tjänster och Reliable Actors under programmet att bli säkerhetskopierade regelbundet enligt den associerade säkerhets kopierings principen. 

![Partitionera säkerhetskopierade hälso händelse][0]

### <a name="list-backups"></a>Lista säkerhets kopior

Säkerhets kopieringar som är kopplade till alla partitioner som tillhör pålitliga tillstånds känsliga tjänster och Reliable Actors av programmet kan räknas upp med _GetBackups_ -API. Säkerhets kopieringar kan räknas upp för ett program, en tjänst eller en partition.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med hjälp av modulen Microsoft. ServiceFabric. PowerShell. http

```powershell
    
Get-SFApplicationBackupList -ApplicationId WordCount
```

#### <a name="rest-call-using-powershell"></a>Rest-anrop med PowerShell

Kör följande PowerShell-skript för att anropa HTTP API för att räkna upp de säkerhets kopior som skapats för alla partitioner i `SampleApp` programmet.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

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

#### <a name="using-service-fabric-explorer"></a>Använda Service Fabric Explorer

Om du vill visa säkerhets kopior i Service Fabric Explorer navigerar du till en partition och väljer fliken säkerhets kopior.

![Räkna upp säkerhets kopior][5]

## <a name="limitation-caveats"></a>Begränsning/varningar
- Service Fabric PowerShell-cmdletar är i förhands gransknings läge.
- Inget stöd för Service Fabric kluster i Linux.

## <a name="next-steps"></a>Nästa steg
- [Förstå konfiguration av regelbunden säkerhetskopiering](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [REST API referens för säkerhets kopierings återställning](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event-azure.png
[1]: ./media/service-fabric-backuprestoreservice/enable-backup-restore-service-with-portal.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/creation-bp.png
