---
title: Kommando rads-och PowerShell-referens för Azure Cosmos DB emulator
description: Lär dig mer om kommando rads parametrarna för Azure Cosmos DB emulator, hur du styr emulatorn med PowerShell och hur du ändrar antalet behållare som du kan skapa i emulatorn.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: e0c795484bf860402d05c1dc5779633962a44ec2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99979512"
---
# <a name="command-line-and-powershell-reference-for-azure-cosmos-db-emulator"></a>Kommando rads-och PowerShell-referens för Azure Cosmos DB emulator
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB-emulatorn tillhandahåller en lokal miljö som emulerar tjänsten Azure Cosmos DB för lokala utvecklings behov. När du har [installerat emulatorn](local-emulator.md)kan du kontrol lera emulatorn med kommando rads-och PowerShell-kommandon. Den här artikeln beskriver hur du använder kommando rads-och PowerShell-kommandon för att starta och stoppa emulatorn, konfigurera alternativ och utföra andra åtgärder. Du måste köra kommandona från installations platsen.

##  <a name="manage-the-emulator-with-command-line-syntax"></a><a id="command-line"></a>Hantera emulatorn med kommandoradssyntax

```cmd
Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]
```

Om du vill visa en lista över alternativ skriver du `Microsoft.Azure.Cosmos.Emulator.exe /?` i kommandotolken.

|**Alternativ** | **Beskrivning** | **Kommando**| **Argument**|
|---|---|---|---|
|[Inga argument] | Startar Azure Cosmos DB-emulatorn med standardinställningar. |Microsoft.Azure.Cosmos.Emulator.exe| |
|[Hjälp] |Visar en lista över kommandoradsargument som stöds.|Microsoft.Azure.Cosmos.Emulator.exe/? | |
| GetStatus |Laddar ned status för Azure Cosmos DB-emulatorn. Statusen visas med slutkoden: 1 = Startar, 2 = Körs, 3 = Stoppad. En negativ slutkod anger att ett fel har uppstått. Inga andra utdata produceras. | Microsoft.Azure.Cosmos.Emulator.exe/GetStatus| |
| Avstängning| Stänger Azure Cosmos DB-emulatorn.| Microsoft.Azure.Cosmos.Emulator.exe/shutdown | |
|DataPath | Anger den sökväg där du kan lagra filer. Standardvärdet är%LocalAppdata%\CosmosDBEmulator. | Microsoft.Azure.Cosmos.Emulator.exe/DataPath =\<datapath\> | \<datapath\>: En tillgänglig sökväg |
|Port | Anger det portnummer som ska användas för emulatorn. Standardvärdet är 8081. |Microsoft.Azure.Cosmos.Emulator.exe/port =\<port\> | \<port\>: Enstaka port nummer |
| ComputePort | Angett det port nummer som ska användas för tjänsten Compute interop Gateway. Gatewayens HTTP-slutpunkt avsöknings port beräknas som ComputePort + 79. Därför måste ComputePort och ComputePort + 79 vara öppna och tillgängliga. Standardvärdet är 8900. | Microsoft.Azure.Cosmos.Emulator.exe/ComputePort =\<computeport\> | \<computeport\>: Enstaka port nummer |
| EnableMongoDbEndpoint = 3,2 | Aktiverar MongoDB API 3,2 | Microsoft.Azure.Cosmos.Emulator.exe/EnableMongoDbEndpoint = 3.2 | |
| EnableMongoDbEndpoint = 3.6 | Aktiverar MongoDB API 3,6 | Microsoft.Azure.Cosmos.Emulator.exe/EnableMongoDbEndpoint = 3.6 | |
| MongoPort | Anger det portnummer som ska användas för API för MongoDB-kompatibilitet. Standardvärdet är 10255. |Microsoft.Azure.Cosmos.Emulator.exe/MongoPort =\<mongoport\>|\<mongoport\>: Enstaka port nummer|
| EnableCassandraEndpoint | Aktiverar API för Cassandra | Microsoft.Azure.Cosmos.Emulator.exe/EnableCassandraEndpoint | |
| CassandraPort | Anger det port nummer som ska användas för Cassandra-slutpunkten. Standardvärdet är 10350. | Microsoft.Azure.Cosmos.Emulator.exe/CassandraPort =\<cassandraport\> | \<cassandraport\>: Enstaka port nummer |
| EnableGremlinEndpoint | Aktiverar Gremlin-API | Microsoft.Azure.Cosmos.Emulator.exe/EnableGremlinEndpoint | |
| GremlinPort | Port nummer som ska användas för Gremlin-slutpunkten. Standardvärdet är 8901. | Microsoft.Azure.Cosmos.Emulator.exe/GremlinPort =\<port\> | \<port\>: Enstaka port nummer |
|EnableTableEndpoint | Aktiverar Azure Tabell-API | Microsoft.Azure.Cosmos.Emulator.exe/EnableTableEndpoint | |
|TablePort | Port nummer som ska användas för Azure Table-slutpunkten. Standardvärdet är 8902. | Microsoft.Azure.Cosmos.Emulator.exe/TablePort =\<port\> | \<port\>: Enstaka port nummer|
| KeyFile | Läs verifierings nyckel från den angivna filen. Använd alternativet/GenKeyFile för att generera en KeyFile | Microsoft.Azure.Cosmos.Emulator.exe/KeyFile =\<file_name\> | \<file_name\>: Sökväg till filen |
| ResetDataPath | Tar rekursivt bort alla filer på den angivna sökvägen. Om du inte anger en sökväg, används%LOCALAPPDATA%\CosmosDbEmulator som standard | Microsoft.Azure.Cosmos.Emulator.exe/ResetDataPath =\<path> | \<path\>: Fil Sök väg  |
| StartTraces  |  Börja samla in fel söknings spårnings loggar med LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe/StartTraces | |
| StopTraces     | Stoppa insamling av fel söknings spårnings loggar med LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe/StopTraces  | |
| StartWprTraces  |  Börja samla in fel söknings spårnings loggar med Windows Performance inspelnings verktyg. | Microsoft.Azure.Cosmos.Emulator.exe/StartWprTraces | |
| StopWprTraces     | Avbryt insamlingen av fel söknings spårnings loggar med Windows Performance inspelnings verktyg. | Microsoft.Azure.Cosmos.Emulator.exe/StopWprTraces  | |
|FailOnSslCertificateNameMismatch | Som standard återskapar emulatorn sitt självsignerade TLS/SSL-certifikat om certifikatets SAN inte innehåller emulatorns domän namn, lokal IPv4-adress, localhost och 127.0.0.1. Med det här alternativet går det inte att starta emulatorn vid start i stället. Du bör sedan använda alternativet/GenCert för att skapa och installera ett nytt självsignerat TLS/SSL-certifikat. | Microsoft.Azure.Cosmos.Emulator.exe/FailOnSslCertificateNameMismatch  | |
| GenCert | Skapa och installera ett nytt självsignerat TLS/SSL-certifikat. Du kan också inkludera en kommaavgränsad lista över ytterligare DNS-namn för att få åtkomst till emulatorn över nätverket. | Microsoft.Azure.Cosmos.Emulator.exe/GenCert =\<dns-names\> |\<dns-names\>: Valfri kommaavgränsad lista över ytterligare DNS-namn  |
| DirectPorts |Anger portarna som ska användas för direktanslutning. Standardvärdena är 10251, 10252, 10253, 10254. | Microsoft.Azure.Cosmos.Emulator.exe/DirectPorts:\<directports\> | \<directports\>: Kommaavgränsad lista över 4 portar |
| Nyckel |Auktoriseringsnyckel för emulatorn. Nyckeln måste vara en base-64-kodning av en 64 bytes vektor. | Microsoft.Azure.Cosmos.Emulator.exe/Key:\<key\> | \<key\>: Nyckeln måste vara bas-64-kodningen för en 64-byte-Vector|
| EnableRateLimiting | Anger att begränsande beteende för förfrågningsfrekvens är aktiverat. |Microsoft.Azure.Cosmos.Emulator.exe/EnableRateLimiting | |
| DisableRateLimiting |Anger att begränsande beteende för förfrågningsfrekvens är inaktiverat. |Microsoft.Azure.Cosmos.Emulator.exe/DisableRateLimiting | |
| NoUI | Visa inte emulatorns användargränssnitt. | Microsoft.Azure.Cosmos.Emulator.exe/NoUI | |
| NoExplorer | Visa inte datautforskaren vid start. |Microsoft.Azure.Cosmos.Emulator.exe/NoExplorer | | 
| PartitionCount | Anger det högsta antalet partitionerade behållare. Mer information finns i [ändra antalet behållare](#set-partitioncount) . | Microsoft.Azure.Cosmos.Emulator.exe/PartitionCount =\<partitioncount\> | \<partitioncount\>: Maximalt antal tillåtna behållare för enskilda partitioner. Standardvärdet är 25. Maxvärdet är 250 GB.|
| DefaultPartitionCount| Anger standardvärdet för antalet partitioner för en partitionerad behållare. | Microsoft.Azure.Cosmos.Emulator.exe/DefaultPartitionCount =\<defaultpartitioncount\> | \<defaultpartitioncount\> Standardvärdet är 25.|
| AllowNetworkAccess | Ger åtkomst till emulatorn över ett nätverk. Du måste också skicka/Key = \<key_string\> eller/KeyFile = \<file_name\> för att aktivera nätverks åtkomst. | Microsoft.Azure.Cosmos.Emulator.exe/AllowNetworkAccess/Key = \<key_string\> eller Microsoft.Azure.Cosmos.Emulator.exe/AllowNetworkAccess/KeyFile =\<file_name\>| |
| NoFirewall | Ändra inte brand Väggs regler när alternativet/AllowNetworkAccess används. |Microsoft.Azure.Cosmos.Emulator.exe/NoFirewall | |
| GenKeyFile | Generera en ny auktoriseringsnyckel och spara den i den angivna filen. Den genererade nyckeln kan användas med alternativen /Key eller /KeyFile. | Microsoft.Azure.Cosmos.Emulator.exe/GenKeyFile =\<path to key file\> | |
| Konsekvens | Ställ in konsekvensnivå för kontot. | Microsoft.Azure.Cosmos.Emulator.exe/Consistency =\<consistency\> | \<consistency\>: Värdet måste vara någon av följande [konsekvens nivåer](consistency-levels.md): session, Strong, eventuell eller BoundedStaleness. Standardvärdet är Session. |
| ? | Visa hjälpmeddelandet.| | |

## <a name="manage-the-emulator-with-powershell"></a>Hantera emulatorn med PowerShell

Emulatorn levereras med en PowerShell-modul för att starta, stoppa, avinstallera och hämta tjänstens status. Kör följande cmdlet för att använda PowerShell-modulen:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

Du kan också placera `PSModules` katalogen på `PSModulePath` och importera den på det sätt som visas i följande kommando:

```powershell
$env:PSModulePath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Här följer en sammanfattning av kommandon för att styra emulatorn från PowerShell:

### `Get-CosmosDbEmulatorStatus`

**Syntax**

`Get-CosmosDbEmulatorStatus`

**Kommentarer**

Returnerar någon av dessa ServiceControllerStatus-värden: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running eller ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

**Syntax**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Kommentarer**

Startar emulatorn. Som standard väntar kommandot till emulatorn är redo att ta emot begäranden. Använd alternativet -NoWait om du vill att cmdleten ska returneras så fort den startar emulatorn.

### `Stop-CosmosDbEmulator`

**Syntax**

 `Stop-CosmosDbEmulator [-NoWait]`

**Kommentarer**

Stoppar emulatorn. Som standard väntar kommandot tills emulatorn är helt avstängd. Använd alternativet -NoWait om du vill att cmdleten ska returneras så fort emulatorn börjar stängas av.

### `Uninstall-CosmosDbEmulator`

**Syntax**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Kommentarer**

Avinstallerar emulatorn och tar eventuellt bort allt innehåll i $env:LOCALAPPDATA\CosmosDbEmulator.
Cmdleten garanterar att emulatorn stoppas innan den avinstalleras.

## <a name="change-the-number-of-default-containers"></a><a id="set-partitioncount"></a>Ändra antalet standard behållare

Som standard kan du skapa upp till 25 behållare med fast storlek (stöds endast med Azure Cosmos DB SDK: er) eller 5 obegränsade behållare med Azure Cosmos DB-emulatorn. Genom att ändra **PartitionCount** -värdet kan du skapa upp till 250 fast storleks behållare eller 50 obegränsade behållare, eller någon kombination av de två som inte överstiger 250 fasta storleks behållare (där en obegränsad container = 5 fast storleks behållare). Det rekommenderas dock inte att ställa in emulatorn så att den körs med fler än 200 behållare med fast storlek. På grund av den omkostnader som läggs till i diskens IO-åtgärder, vilket leder till oförutsägbara tids gränser vid användning av slut punkts-API: er.

Om du försöker skapa en behållare när det aktuella antalet partitioner har överskridits, genererar emulatorn ett ServiceUnavailable-undantag med följande meddelande.

> Vi har tyvärr för närvarande hög efter frågan i den här regionen och kan inte slutföra din begäran för tillfället. Vi arbetar kontinuerligt för att få mer och mer kapacitet online och uppmana dig att försöka igen.
> ActivityId: 12345678-1234-1234-1234-123456789abc

Kör följande steg för att ändra antalet behållare som är tillgängliga i Azure Cosmos DB-emulatorn:

1. Ta bort alla lokala Azure Cosmos DB-emulatordata genom att högerklicka på ikonen för **Azure Cosmos DB-emulatorn** i meddelandefältet och sedan klicka på **Återställ data...**

1. Ta bort alla emulator-data i den här mappen `%LOCALAPPDATA%\CosmosDBEmulator` .

1. Avsluta alla öppna instanser genom att högerklicka på ikonen för **Azure Cosmos DB-emulator** i meddelandefältet och klicka sedan på **Avsluta**. Det kan ta någon minut för alla instanser att avslutas.

1. Installera den senaste versionen av [Azure Cosmos DB-emulatorn](https://aka.ms/cosmosdb-emulator).

1. Starta emulatorn med PartitionCount-flaggan genom att ställa in ett värde <= 250. Exempel: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`.
 
## <a name="next-steps"></a>Nästa steg

* [Exportera Azure Cosmos DB emulator-certifikat för användning med Java-, python-och Node.js-appar](local-emulator-export-ssl-certificates.md)
* [Felsöka problem med emulatorn](troubleshoot-local-emulator.md)
