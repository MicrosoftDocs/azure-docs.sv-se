---
title: Förstå konfiguration av regelbunden säkerhetskopiering
description: Använd Service Fabric periodiska säkerhets kopierings-och återställnings funktionen för att konfigurera regelbunden säkerhets kopiering av dina pålitliga tillstånds känsliga tjänster eller Reliable Actors.
ms.topic: article
ms.date: 2/01/2019
ms.openlocfilehash: 2607502af44b178131820d78f23bcdf4e32454a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018893"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Förstå regelbunden konfiguration av säkerhets kopiering i Azure Service Fabric

Att konfigurera regelbunden säkerhets kopiering av dina pålitliga tillstånds känsliga tjänster eller Reliable Actors består av följande steg:

1. **Skapa säkerhets kopierings principer**: i det här steget skapas en eller flera säkerhets kopierings principer beroende på kraven.

2. **Aktiverar säkerhets kopiering**: i det här steget kopplar du säkerhets kopierings principer som skapats i **steg 1** till obligatoriska entiteter, _program_, _tjänster_ eller en _partition_.

## <a name="create-backup-policy"></a>Skapa säkerhets kopierings princip

En säkerhets kopierings princip består av följande konfigurationer:

* **Automatisk återställning vid data förlust**: anger om återställning ska aktive ras automatiskt med den senaste tillgängliga säkerhets kopian, om partitionen upplever en data förlust händelse.
> [!NOTE]
> Vi rekommenderar att inte ställa in automatisk återställning i produktions kluster
>

* **Högsta antal säkerhets kopior**: definierar det maximala antalet stegvisa säkerhets kopior som ska tas mellan två fullständiga säkerhets kopieringar. Högsta antal stegvisa säkerhets kopieringar anger den övre gränsen. En fullständig säkerhets kopiering kan utföras innan det angivna antalet stegvisa säkerhets kopieringar har slutförts på något av följande villkor

    1. Repliken har aldrig tagit en fullständig säkerhets kopia eftersom den har blivit primär.

    2. Några av logg posterna sedan den senaste säkerhets kopieringen har trunkerats.

    3. Repliken godkände MaxAccumulatedBackupLogSizeInMB-gränsen.

* **Schema för säkerhets kopiering**: den tid eller frekvens med vilken regelbundna säkerhets kopieringar ska utföras. En kan schemalägga säkerhets kopieringar så att de blir återkommande vid angivet intervall eller vid en viss tidpunkt varje dag/vecka.

    1. **Frekvens-baserat säkerhets kopierings schema**: den här schema typen ska användas om behovet är att säkerhetskopiera data med fasta intervall. Önskat tidsintervall mellan två på varandra följande säkerhets kopior definieras med ISO8601-format. Frekvensbaserade säkerhets kopierings scheman stöder intervall matchning till minuten.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **Tidsbaserat säkerhets kopierings schema**: den här schema typen ska användas om behovet är att säkerhetskopiera data vid vissa tidpunkter på dagen eller i veckan. Typ av schema frekvens kan vara varje dag eller varje vecka.
        1. **_Dagligt_ tidsbaserat säkerhets kopierings schema**: den här schema typen ska användas om behovet är att säkerhetskopiera data vid vissa tidpunkter på dagen. Om du vill ange detta anger `ScheduleFrequencyType` du till _dagligt_ och anger en `RunTimes` lista över önskad tid under dagen i iso8601-format, det datum som anges tillsammans med tiden kommer att ignoreras. Till exempel `0001-01-01T18:00:00` representerar _6:00_ varje dag, vilket ignorerar datum del _0001-01-01_. I exemplet nedan visas konfigurationen för att utlösa en daglig säkerhets kopia vid _9:00 am_ och _6:00_ varje dag.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Daily",
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

        2. **Tidsbaserad säkerhets kopiering _per vecka_**: den här schema typen ska användas om behovet är att säkerhetskopiera data vid vissa tidpunkter på dagen. Om du vill ange detta ställer du in `ScheduleFrequencyType` på _veckovis_, anger `RunDays` en lista med dagar i veckan när säkerhets kopieringen måste utlösas och anges `RunTimes` till lista över önskad tid under dagen i iso8601-format, det datum som anges tillsammans med tiden ignoreras. Lista över dagar i veckan när den periodiska säkerhets kopieringen ska aktive ras. I exemplet nedan visas konfigurationen för att utlösa en daglig säkerhets kopia vid _9:00 am_ och _6:00 PM_ under måndag till fredag.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Weekly",
                "RunDays": [
                   "Monday",
                   "Tuesday",
                   "Wednesday",
                   "Thursday",
                   "Friday"
                ],
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

* **Lagring av säkerhets kopior**: anger den plats där säkerhets kopiorna ska överföras. Lagrings utrymmet kan vara antingen Azure Blob Store eller fil resursen.
    1. **Azure Blob Store**: den här lagrings typen ska väljas när behovet är att lagra genererade säkerhets kopior i Azure. Både _fristående_ och _Azure-baserade_ kluster kan använda den här lagrings typen. Beskrivningen för den här lagrings typen kräver en anslutnings sträng och namnet på den behållare där säkerhets kopiorna måste överföras. Om behållaren med det angivna namnet inte är tillgänglig skapas den när du överför en säkerhets kopia.

        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

        > [!NOTE]
        > Tjänsten för återställning av säkerhets kopiering fungerar inte med v1 Azure Storage
        >

    2. **Fil resurs**: den här lagrings typen ska väljas för _fristående_ kluster när behovet är att lagra data säkerhets kopia lokalt. Beskrivningen för den här lagrings typen kräver en fil resurs Sök väg där säkerhets kopior måste överföras. Åtkomst till fil resursen kan konfigureras med hjälp av något av följande alternativ
        1. _Integrerad Windows-autentisering_, där åtkomst till fil resursen tillhandahålls till alla datorer som hör till Service Fabric klustret. I det här fallet anger du följande fält för att konfigurera _fildelning_ baserad lagring av säkerhets kopior.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. _Skydda fil resursen med hjälp av användar namn och lösen ord_, där åtkomsten till fil resursen tillhandahålls till vissa användare. Fil resurs lagrings specifikationen ger också möjlighet att ange sekundärt användar namn och sekundärt lösen ord för att ge autentiseringsuppgifter för säkerhets kopiering i fall autentiseringen Miss lyckas med det primära användar namnet och det primära lösen ordet. I det här fallet anger du följande fält för att konfigurera _fildelning_ baserad lagring av säkerhets kopior.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore",
                "PrimaryUserName": "backupaccount",
                "PrimaryPassword": "<Password for backupaccount>",
                "SecondaryUserName": "backupaccount2",
                "SecondaryPassword": "<Password for backupaccount2>"
            }
            ```

> [!NOTE]
> Se till att lagrings tillförlitligheten uppfyller eller överskrider Tillförlitlighets kraven för säkerhetskopierade data.
>

* **Bevarande princip**: anger principen för att behålla säkerhets kopior i den konfigurerade lagringen. Det finns bara stöd för grundläggande bevarande principer.
    1. **Princip för grundläggande bevarande**: den här bevarande principen gör det möjligt att säkerställa optimal lagrings användning genom att ta bort säkerhetskopierade filer som inte behövs längre. `RetentionDuration` kan anges för att ställa in tidsintervallet för vilka säkerhets kopieringar måste behållas i lagringen. `MinimumNumberOfBackups` är en valfri parameter som kan anges för att se till att det angivna antalet säkerhets kopior alltid behålls oberoende av `RetentionDuration` . I exemplet nedan visas konfigurationen för att bevara säkerhets kopior i _10_ dagar och antalet säkerhets kopior kan inte gå under _20_.

        ```json
        {
            "RetentionPolicyType": "Basic",
            "RetentionDuration" : "P10D",
            "MinimumNumberOfBackups": 20
        }
        ```

## <a name="enable-periodic-backup"></a>Aktivera periodisk säkerhets kopiering
När du har definierat säkerhets kopierings policyn för att uppfylla kraven på säkerhets kopiering måste säkerhets kopierings principen vara lämplig för antingen ett _program_, en _tjänst_ eller en _partition_.

> [!NOTE]
> Se till att inga program uppgraderingar pågår innan du aktiverar säkerhets kopiering
>

### <a name="hierarchical-propagation-of-backup-policy"></a>Hierarkisk spridning av säkerhets kopierings princip
I Service Fabric är relationen mellan program, tjänst och partitioner hierarkisk, som förklaras i [program modellen](./service-fabric-application-model.md). Säkerhets kopierings policyn kan kopplas antingen till ett _program_, en _tjänst_ eller en _partition_ i hierarkin. Säkerhets kopierings policyn sprids hierarkiskt till nästa nivå. Förutsatt att det bara finns en säkerhets kopierings princip som skapats och associerats med ett _program_, säkerhets kopie ras alla tillstånds känsliga partitioner som tillhör alla _pålitliga tillstånds känsliga tjänster_ och _Reliable Actors_ av _programmet_ med säkerhets kopierings principen. Eller om säkerhets kopierings policyn är associerad med en _tillförlitlig tillstånds känslig tjänst_ säkerhets kopie ras alla dess partitioner med säkerhets kopierings principen.

### <a name="overriding-backup-policy"></a>Åsidosätt säkerhets kopierings princip
Det kan finnas ett scenario där data säkerhets kopiering med samma säkerhets kopierings schema krävs för alla tjänster i programmet, med undantag för specifika tjänster där behovet är att säkerhetskopiera data med högre frekvens schema eller att säkerhetskopiera till ett annat lagrings konto eller en annan resurs. För att lösa sådana scenarier tillhandahåller Backup Restore service-tjänsten en funktion för att åsidosätta spridd princip i tjänst-och partitions omfång När säkerhets kopierings principen är associerad med en _tjänst_ eller _partition_ åsidosätts den distribuerade säkerhets kopierings principen, om sådan finns.

### <a name="example"></a>Exempel

I det här exemplet används installationen med två program, _MyApp_A_ och _MyApp_B_. Program _MyApp_A_ innehåller två pålitliga tillstånds känsliga tjänster, _SvcA1_  &  _SvcA3_ och en pålitlig aktörs tjänst, _ActorA2_. _SvcA1_ innehåller tre partitioner medan _ActorA2_ och _SvcA3_ innehåller två partitioner.  Program _MyApp_B_ innehåller tre pålitliga tillstånds känsliga tjänster, _SvcB1_, _SvcB2_ och _SvcB3_. _SvcB1_ och _SvcB2_ innehåller två partitioner, medan _SvcB3_ innehåller tre partitioner.

Anta att dessa programs krav för säkerhets kopiering av data är följande

1. MyApp_A
    1. Skapa daglig säkerhets kopiering av data för alla partitioner i alla _pålitliga tillstånds känsliga tjänster_ och _Reliable Actors_ som hör till programmet. Ladda upp säkerhets kopierings data till platsen _BackupStore1_.

    2. En av tjänsterna, _SvcA3_, kräver data säkerhets kopiering varje timme.

    3. Data storleken i partitionen _SvcA1_P2_ är mer än förväntad och dess säkerhets kopierings data ska lagras på olika lagrings platser _BackupStore2_.

2. MyApp_B
    1. Skapa en säkerhets kopia av data varje söndag kl. 8:00 för alla partitioner i _SvcB1_ -tjänsten. Ladda upp säkerhets kopierings data till platsen _BackupStore1_.

    2. Skapa säkerhets kopia av data varje dag vid 8:00 för partition _SvcB2_P1_. Ladda upp säkerhets kopierings data till platsen _BackupStore1_.

För att åtgärda dessa krav för säkerhets kopiering, skapas säkerhets kopierings principer BP_1 BP_5 skapas och säkerhets kopiering aktive ras enligt följande.
1. MyApp_A
    1. Skapa säkerhets kopierings princip, _BP_1_, med frekvens-baserat säkerhets kopierings schema där frekvensen är inställd på 24 timmar. och lagring av säkerhets kopior som kon figurer ATS för att använda lagrings platsen _BackupStore1_. Aktivera den här principen för  program MyApp_A [att använda Aktivera API för program säkerhets kopiering](/rest/api/servicefabric/sfclient-api-enableapplicationbackup) . Den här åtgärden möjliggör säkerhets kopiering av data med hjälp av säkerhets kopierings princip _BP_1_ för alla partitioner med _pålitliga tillstånds känsliga tjänster_ och _Reliable Actors_ som hör till program _MyApp_A_.

    2. Skapa säkerhets kopierings princip, _BP_2_, med frekvens-baserat säkerhets kopierings schema där frekvensen anges till 1 timme. och lagring av säkerhets kopior som kon figurer ATS för att använda lagrings platsen _BackupStore1_. Aktivera den här principen för tjänst- _SvcA3_ med hjälp av Aktivera API för [säkerhets kopiering av tjänst](/rest/api/servicefabric/sfclient-api-enableservicebackup) . Den här åtgärden åsidosätter den spridda princip _BP_1_ genom uttryckligen aktiverade säkerhets kopierings principer _BP_2_ för alla _SvcA3_ som leder till data säkerhets kopiering med säkerhets kopierings princip _BP_2_ för dessa partitioner.

    3. Skapa säkerhets kopierings princip, _BP_3_, med frekvens-baserat säkerhets kopierings schema där frekvensen är inställd på 24 timmar. och lagring av säkerhets kopior som kon figurer ATS för att använda lagrings platsen _BackupStore2_. Aktivera den här principen för partition _SvcA1_P2_ att använda API för [att aktivera partitions säkerhets kopiering](/rest/api/servicefabric/sfclient-api-enablepartitionbackup) . Den här åtgärden åsidosätter den spridda princip _BP_1_ genom uttryckligen aktiverade säkerhets kopierings principer _BP_3_ för partition _SvcA1_P2_.

2. MyApp_B
    1. Skapa säkerhets kopierings princip, _BP_4_, med tidsbaserat säkerhets kopierings schema där schema frekvens typ är inställd på veckovis, körnings dagar är inställt på söndag och körnings tiderna är inställt på 8:00. Säkerhets kopierings lagring som kon figurer ATS för att använda lagrings platsen _BackupStore1_. Aktivera den här principen för tjänst- _SvcB1_ med hjälp av Aktivera API för [säkerhets kopiering av tjänst](/rest/api/servicefabric/sfclient-api-enableservicebackup) . Den här åtgärden aktiverar data säkerhets kopiering med säkerhets kopierings princip _BP_4_ för alla partitioner för service _SvcB1_.

    2. Skapa säkerhets kopierings princip, _BP_5_, med tidsbaserat säkerhets kopierings schema där schema frekvens typ är inställd på daglig och kör tid är inställd på 8:00. Säkerhets kopierings lagring som kon figurer ATS för att använda lagrings platsen _BackupStore1_. Aktivera den här principen för partition _SvcB2_P1_ att använda API för [att aktivera partitions säkerhets kopiering](/rest/api/servicefabric/sfclient-api-enablepartitionbackup) . Den här åtgärden aktiverar data säkerhets kopiering med säkerhets kopierings princip _BP_5_ för partition _SvcB2_P1_.

Följande diagram illustrerar uttryckligen aktiverade säkerhets kopierings principer och distribuerade säkerhets kopierings principer.

![Service Fabric programhierarki][0]

## <a name="disable-backup"></a>Inaktivera säkerhetskopiering
Säkerhets kopierings principer kan inaktive ras när det inte finns några behov av att säkerhetskopiera data. Säkerhets kopierings principen som är aktive rad i ett _program_ kan bara inaktive ras i samma _program_ med hjälp av inaktivera API för [program säkerhets](/rest/api/servicefabric/sfclient-api-disableapplicationbackup) kopiering. säkerhets kopierings principen som är aktive rad på en _tjänst_ kan inaktive ras _på samma_ _tjänst_ som använder inaktivera [API för](/rest/api/servicefabric/sfclient-api-disablepartitionbackup) [tjänst säkerhets kopiering](/rest/api/servicefabric/sfclient-api-disableservicebackup) och säkerhets kopierings principen som är aktive rad på en _partition_ kan

* Om du inaktiverar säkerhets kopierings principen för ett _program_ stoppas alla periodiska data säkerhets kopieringar på grund av spridningen av säkerhets kopierings principen till pålitliga tillstånds känsliga diskpartitioner eller tillförlitliga aktörs partitioner.

* Om säkerhets kopierings principen inaktive ras för en _tjänst_ stoppas alla regelbundna säkerhets kopieringar på grund av spridningen av den här säkerhets kopierings principen till partitionerna för _tjänsten_.

* Om du inaktiverar säkerhets kopierings principen för en _partition_ stoppas alla regelbundna data säkerhets kopieringar på grund av säkerhets kopierings principen på partitionen.

* När du inaktiverar säkerhets kopiering för en entitet (program/tjänst/partition) `CleanBackup` kan anges till _Sant_ för att ta bort alla säkerhets kopior i den konfigurerade lagringen.
    ```json
    {
        "CleanBackup": true 
    }
    ```
> [!NOTE]
> Se till att inga program uppgraderingar pågår innan du inaktiverar säkerhets kopieringen
>

## <a name="suspend--resume-backup"></a>Pausa & återuppta säkerhets kopieringen
En viss situation kan kräva tillfällig fjädring av regelbunden säkerhets kopiering av data. I sådana fall, beroende på vad som krävs, kan du inaktivera säkerhets kopierings-API: et för ett _program_, en _tjänst_ eller en _partition_. Regelbunden SUS Pension är transitiv över under trädet för programmets hierarki från den punkt som används. 

* När SUS Pension används i ett _program_ med hjälp av inaktivera API för [program säkerhets kopiering](/rest/api/servicefabric/sfclient-api-suspendapplicationbackup) pausas alla tjänster och partitioner under det här programmet för regelbunden säkerhets kopiering av data.

* När SUS Pension används på en _tjänst_ med hjälp av [suspend-tjänsten för säkerhets kopiering](/rest/api/servicefabric/sfclient-api-suspendservicebackup) inaktive ras alla partitioner under den här tjänsten för regelbunden säkerhets kopiering av data.

* När SUS Pension används på en _partition_ med hjälp av [pausa partitions säkerhets kopierings](/rest/api/servicefabric/sfclient-api-suspendpartitionbackup) -API, pausas partitioner under den här tjänsten för regelbunden säkerhets kopiering av data.

När behovet av SUS Pension är över kan den periodiska säkerhets kopian återställas med respektive återuppta säkerhets kopierings-API. Regelbunden säkerhets kopiering måste återupptas på samma _program_, _tjänst_ eller _partition_ där det pausades.

* Om SUS Pension har tillämpats på ett _program_ ska den återupptas med hjälp av [återuppta program säkerhets kopierings](/rest/api/servicefabric/sfclient-api-resumeapplicationbackup) -API. 

* Om SUS Pension har tillämpats på en _tjänst_ ska den återupptas med hjälp av [återuppta service backup-](/rest/api/servicefabric/sfclient-api-resumeservicebackup) API.

* Om SUS Pension har tillämpats på en _partition_, ska den återupptas med hjälp av [återuppta partitions säkerhets kopierings](/rest/api/servicefabric/sfclient-api-resumepartitionbackup) -API.

### <a name="difference-between-suspend-and-disable-backups"></a>Skillnaden mellan pausa och inaktivera säkerhets kopieringar
Inaktivera säkerhets kopiering ska användas när säkerhets kopiering inte längre krävs för ett visst program, en tjänst eller en partition. En kan anropa inaktivera begäran om säkerhets kopiering tillsammans med parametern för rensade säkerhets kopior så att alla befintliga säkerhets kopior också tas bort. Pausa är dock att användas i scenarier där en vill inaktivera säkerhets kopieringar tillfälligt, till exempel när den lokala disken blir full eller om det inte går att ladda upp säkerhets kopieringen på grund av kända nätverks problem osv. 

Inaktive ring kan bara anropas på en nivå som tidigare har Aktiver ATS för säkerhets kopiering, men som uttryckligen upphör att gälla, kan du tillämpa på valfri nivå som för närvarande är aktive rad för säkerhets kopiering antingen direkt eller via arv/hierarki Om säkerhets kopiering till exempel har Aktiver ATS på en program nivå kan ett anrop endast aktive ras på program nivån, men en paus kan anropas i programmet, alla tjänster eller partitioner i det programmet. 

## <a name="auto-restore-on-data-loss"></a>Automatisk återställning vid data förlust
-Tjänstepartitionen kan förlora data på grund av oväntade fel. Till exempel kan disken för två av tre repliker för en partition (inklusive den primära repliken) skadas eller rensas.

När Service Fabric upptäcker att partitionen har data förlust, anropar den `OnDataLossAsync` gränssnitts metod på partitionen och förväntar sig att partitionen ska vidta den nödvändiga åtgärden för att ta bort data förlust. I det här fallet `AutoRestoreOnDataLoss` `true` utlöses återställningen automatiskt med den senaste tillgängliga säkerhets kopian för den här partitionen, om den gällande säkerhets kopierings principen vid partitionen har angetts till.

> [!NOTE]
> Vi rekommenderar att inte ställa in automatisk återställning i produktions kluster
>

## <a name="get-backup-configuration"></a>Hämta säkerhets kopierings konfiguration
Separata API: er görs tillgängliga för att hämta konfigurations information för säkerhets kopiering i en _program_-, _tjänst_-och _partition_ omfattning. [Hämta konfigurations information för program säkerhets kopiering](/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo), [Hämta konfigurations information för tjänst säkerhets kopiering](/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo)och [Hämta konfigurations information för säkerhets kopiering av partitioner](/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo) är dessa API: er Huvudsakligen returnerar dessa API: er den tillämpliga säkerhets kopierings principen, omfattning som säkerhets kopierings principen tillämpas på och information om SUS pension. Följande är en kort beskrivning av returnerade resultat från dessa API: er.

- Konfigurations information för program säkerhets kopiering: innehåller information om säkerhets kopierings policyn som tillämpas på programmet och alla åsidosättande principer på tjänster och partitioner som hör till programmet. Den innehåller också information om SUS pension för programmet och IT-tjänster och partitioner.

- Konfigurations information för tjänst säkerhets kopiering: innehåller information om en effektiv säkerhets kopierings princip för tjänsten och omfattningen där den här principen tillämpades och alla åsidosättande principer på dess partitioner. Den innehåller också information om SUS pension för tjänsten och dess partitioner.

- Konfigurations information för partitions säkerhets kopiering: innehåller information om en effektiv säkerhets kopierings policy vid partitionen och omfattningen där den här principen tillämpades. Den innehåller också information om SUS pension för partitionerna.

## <a name="list-available-backups"></a>Lista tillgängliga säkerhets kopior

Tillgängliga säkerhets kopior kan listas med Get backup List API. Resultatet av API-anropet inkluderar säkerhets kopierings information som är relaterad till alla säkerhets kopior som är tillgängliga på säkerhets kopierings lagringen, som konfigureras i den aktuella säkerhets kopierings principen Olika varianter av detta API tillhandahålls för att visa en lista över tillgängliga säkerhets kopior som hör till ett program, en tjänst eller en partition. Dessa API: er har stöd för att hämta den _senaste_ tillgängliga säkerhets kopian av alla tillämpliga partitioner eller filtrering av säkerhets kopior baserat på _start datum_ och _slutdatum_.

Dessa API: er stöder också sid brytning av resultaten, när _MaxResults_ -parametern har angetts till ett positivt heltal som inte är noll returnerar API: n det högsta antalet _MaxResults_ för säkerhets kopiering av information. Om det finns fler tillgängliga säkerhets kopierings uppgifter än _MaxResults_ -värdet returneras en fortsättnings-token. Giltig parametern för att fortsätta token kan användas för att hämta nästa uppsättning resultat. När ett giltigt värde för över gångs-token skickas till nästa anrop till API: et returnerar API nästa uppsättning resultat. Ingen fortsättnings-token ingår i svaret när alla tillgängliga resultat returneras.

Nedan följer en kort information om vilka varianter som stöds.

- [Hämta program säkerhets kopierings lista](/rest/api/servicefabric/sfclient-api-getapplicationbackuplist): returnerar en lista med tillgängliga säkerhets kopior för varje partition som hör till Service Fabric program.

- [Hämta säkerhets kopie lista för tjänsten](/rest/api/servicefabric/sfclient-api-getservicebackuplist): returnerar en lista med tillgängliga säkerhets kopior för varje partition som hör till den Service Fabric tjänsten.
 
- [Hämta säkerhets kopie lista för partitioner](/rest/api/servicefabric/sfclient-api-getpartitionbackuplist): returnerar en lista med tillgängliga säkerhets kopior för den angivna partitionen.

## <a name="next-steps"></a>Nästa steg
- [REST API referens för säkerhets kopierings återställning](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/backup-policy-association-example.png
