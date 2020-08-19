---
title: Använd Azure Storage emulatorn för utveckling och testning
description: Azure Storage-emulatorn tillhandahåller en kostnads fri lokal utvecklings miljö för att utveckla och testa dina Azure Storage-program.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/16/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.openlocfilehash: 959a58a38861075c6509fe57136d8991eeb98ce6
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588203"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Använd Azure Storage emulatorn för utveckling och testning

Microsoft Azure Storage-emulator är ett verktyg som emulerar Azure Blob-, Queue-och table-tjänsterna för lokala utvecklings behov. Du kan testa ditt program mot lagrings tjänsterna lokalt utan att skapa en Azure-prenumeration eller debitera några kostnader. När du är nöjd med hur ditt program fungerar i emulatorn växlar du till att använda ett Azure Storage-konto i molnet.

> [!IMPORTANT]
> Azure Storage emulatorn är inte längre aktivt utvecklad. [**Azurite**](storage-use-azurite.md) är Storage emulator-plattformen som går framåt. Azurite ersätter Azure Storage-emulatorn. Azurite kommer fortfarande att uppdateras för att stödja de senaste versionerna av Azure Storage-API: er. Mer information finns i [**använda Azurite-emulatorn för lokal Azure Storage utveckling**](storage-use-azurite.md).

## <a name="get-the-storage-emulator"></a>Hämta Storage-emulatorn

Storage-emulatorn är tillgänglig som en del av [Microsoft Azure SDK](https://azure.microsoft.com/downloads/). Du kan också installera Storage-emulatorn med hjälp av det [fristående installations programmet](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (direkt hämtning). Du måste ha administratörs behörighet på datorn för att kunna installera Storage-emulatorn.

Storage-emulatorn körs för närvarande endast på Windows. Om du behöver en Storage-emulator för Linux är det ett alternativ som communityn har kvar, [Azurite](https://github.com/azure/azurite)med öppen källkod.

> [!NOTE]
> Data som skapats i en version av Storage-emulatorn kan inte nås när du använder en annan version. Om du behöver spara dina data på lång sikt rekommenderar vi att du lagrar dessa data i ett Azure Storage-konto i stället för i Storage-emulatorn.
> 
> Storage-emulatorn är beroende av vissa versioner av OData-biblioteken. Det finns inte stöd för att ersätta de OData-dll: er som används av Storage-emulatorn med andra versioner, och det kan leda till oväntat beteende. En version av OData som stöds av lagrings tjänsten kan dock användas för att skicka begär anden till emulatorn.

## <a name="how-the-storage-emulator-works"></a>Så här fungerar Storage-emulatorn

Storage-emulatorn använder en lokal Microsoft SQL Server 2012 Express LocalDB-instans för att emulera Azure Storage-tjänster. Du kan välja att konfigurera Storage-emulatorn för att få åtkomst till en lokal instans av SQL Server i stället för LocalDB-instansen. Mer information finns i avsnittet [starta och initiera Storage-emulatorn](#start-and-initialize-the-storage-emulator) längre fram i den här artikeln.

Lagringsprovidern ansluter till SQL Server eller LocalDB med Windows-autentisering.

Det finns vissa skillnader i funktionalitet mellan Storage-emulatorn och Azure Storage-tjänsterna. Mer information om dessa skillnader finns i [skillnaderna mellan Storage-emulatorn och Azure Storage](#differences-between-the-storage-emulator-and-azure-storage) avsnittet längre fram i den här artikeln.

## <a name="start-and-initialize-the-storage-emulator"></a>Starta och initiera Storage-emulatorn

Starta Azure Storage-emulatorn:

1. Välj **Start** -knappen eller tryck på **Windows** -tangenten.
2. Börja skriva `Azure Storage Emulator` .
3. Välj emulatorn i listan med program som visas.

När Storage-emulatorn startar visas ett kommando tolks fönster. Du kan använda det här konsol fönstret för att starta och stoppa Storage-emulatorn. Du kan också rensa data, Hämta status och initiera emulatorn från kommando tolken. Mer information finns i referens avsnittet om [kommando rads verktyget för Storage-emulatorn](#storage-emulator-command-line-tool-reference) längre fram i den här artikeln.

> [!NOTE]
> Azure Storage emulatorn kanske inte startar korrekt om en annan Storage-emulator, till exempel Azurite, körs på systemet.

När emulatorn körs visas en ikon i aktivitetsfältets meddelandefält i Windows.

När du stänger kommando tolken i Storage-emulatorn fortsätter Storage-emulatorn att köras. Om du vill öppna konsolen för Storage-emulatorn igen följer du föregående steg som om du startar Storage-emulatorn.

Första gången du kör Storage-emulatorn initieras den lokala lagrings miljön. Initierings processen skapar en databas i LocalDB och reserverar HTTP-portar för varje lokal lagrings tjänst.

Storage-emulatorn installeras som standard på `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator` .

> [!TIP]
> Du kan använda [Microsoft Azure Storage Explorer](https://storageexplorer.com) för att arbeta med resurser för lokal lagrings emulator. Sök efter "(emulator-standard portar) (nyckel)" under "lokala & anslutna" i trädet för Storage Explorer resurser när du har installerat och startat Storage-emulatorn.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Initiera Storage-emulatorn om du vill använda en annan SQL-databas

Du kan använda kommando rads verktyget Storage-emulator för att initiera Storage-emulatorn så att den pekar på en annan SQL Database-instans än standard instansen av LocalDB:

1. Öppna fönstret Storage emulator-konsol enligt beskrivningen i avsnittet [starta och initiera modulen lagrings-emulator](#start-and-initialize-the-storage-emulator) .
1. I konsol fönstret skriver du följande kommando, där `<SQLServerInstance>` är namnet på SQL Server-instansen. Om du vill använda LocalDB anger `(localdb)\MSSQLLocalDb` du som SQL Server-instansen.

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   Du kan också använda följande kommando, som dirigerar emulatorn till att använda standard SQL Server-instansen:

   `AzureStorageEmulator.exe init /server .`

   Du kan också använda följande kommando, som initierar databasen till standard LocalDB-instansen:

   `AzureStorageEmulator.exe init /forceCreate`

Mer information om dessa kommandon finns i [kommando rads verktyget Storage mula Reference](#storage-emulator-command-line-tool-reference).

> [!TIP]
> Du kan använda [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) för att hantera dina SQL Server instanser, inklusive LocalDB-installationen. I dialog rutan SMSS **Anslut till Server** anger du `(localdb)\MSSQLLocalDb` i fältet **Server Namn:** för att ansluta till LocalDB-instansen.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Autentisera begär Anden mot Storage-emulatorn

När du har installerat och startat Storage-emulatorn kan du testa koden mot den. Varje begäran du gör mot lagringsprovidern måste vara auktoriserad, såvida det inte är en anonym begäran. Du kan auktorisera begär Anden mot Storage-emulatorn med hjälp av autentisering med delad nyckel eller med en signatur för delad åtkomst (SAS).

### <a name="authorize-with-shared-key-credentials"></a>Auktorisera med autentiseringsuppgifter för delad nyckel

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Mer information om anslutnings strängar finns i [Konfigurera anslutnings strängar för Azure Storage](../storage-configure-connection-string.md).

### <a name="authorize-with-a-shared-access-signature"></a>Auktorisera med en signatur för delad åtkomst

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Vissa klient bibliotek för Azure Storage, till exempel Xamarin-biblioteket, stöder endast autentisering med en SAS-token (signatur för delad åtkomst). Du kan skapa SAS-token med [Storage Explorer](https://storageexplorer.com/) eller ett annat program som stöder autentisering med delad nyckel.

Du kan också skapa en SAS-token med hjälp av Azure PowerShell. I följande exempel skapas en SAS-token med fullständig behörighet till en BLOB-behållare:

1. Installera Azure PowerShell om du inte redan har gjort det (med den senaste versionen av Azure PowerShell-cmdletar rekommenderas). Installations anvisningar finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-Az-ps).
2. Öppna Azure PowerShell och kör följande kommandon och Ersätt `CONTAINER_NAME` med ett namn som du väljer:

```powershell
$context = New-AzStorageContext -Local

New-AzStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

Den resulterande URI: n för signaturen för delad åtkomst för den nya behållaren bör likna följande:

```
http://127.0.0.1:10000/devstoreaccount1/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

Signaturen för delad åtkomst som skapas i det här exemplet är giltig i en dag. Signaturen ger fullständig åtkomst (läsa, skriva, ta bort, lista) till blobar i behållaren.

Mer information om signaturer för delad åtkomst finns i [bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av signaturer för delad åtkomst (SAS)](storage-sas-overview.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Adressera resurser i Storage-emulatorn

Tjänstens slut punkter för lagrings-emulatorn skiljer sig från slut punkterna för ett Azure Storage-konto. Den lokala datorn utför inte domän namns matchning, vilket kräver att Storage-emulatorns slut punkter är lokala adresser.

När du adresserar en resurs i ett Azure Storage-konto använder du följande schema. Konto namnet är en del av URI-värdnamnet och resursen som ska adresseras är en del av URI-sökvägen:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Följande URI är till exempel en giltig adress för en BLOB i ett Azure Storage-konto:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Eftersom den lokala datorn inte har domän namns matchning, är konto namnet en del av URI-sökvägen i stället för värd namnet. Använd följande URI-format för en resurs i Storage-emulatorn:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Följande adress kan till exempel användas för att komma åt en BLOB i Storage-emulatorn:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Tjänstens slut punkter för Storage-emulatorn är:

* Blob Service: `http://127.0.0.1:10000/<account-name>/<resource-path>`
* Kötjänst: `http://127.0.0.1:10001/<account-name>/<resource-path>`
* Table service: `http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Adressera det konto som är sekundärt med RA-GRS

Från och med version 3,1 stöder Storage-emulatorn Geo-redundant replikering av Läs åtkomst (RA-GRS). Du kan komma åt den sekundära platsen genom att lägga till-sekundär till konto namnet. Följande adress kan till exempel användas för att få åtkomst till en blob med hjälp av den skrivskyddade sekundären i Storage-emulatorn:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Använd lagrings klient biblioteket för .NET version 3,2 eller senare för program mässig åtkomst till den sekundära med Storage-emulatorn. Mer information finns i [Microsoft Azure Storage klient biblioteket för .net](https://msdn.microsoft.com/library/azure/dn261237.aspx) .
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Kommando rads verktyg för Storage-emulator

Från och med version 3,0 visas ett konsol fönster när du startar Storage-emulatorn. Använd kommando raden i konsol fönstret för att starta och stoppa emulatorn. Du kan också fråga efter status och utföra andra åtgärder från kommando raden.

> [!NOTE]
> Om du har installerat Microsoft Azure Compute-emulatorn visas en ikon i system fältet när du startar Storage-emulatorn. Högerklicka på ikonen för att visa en meny som ger ett grafiskt sätt att starta och stoppa Storage-emulatorn.
>
>

### <a name="command-line-syntax"></a>Syntax för kommandorad

`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Alternativ

Om du vill visa en lista över alternativ skriver du `/help` i kommandotolken.

| Alternativ | Beskrivning | Kommando | Argument |
| --- | --- | --- | --- |
| **Start** |Startar Storage-emulatorn. |`AzureStorageEmulator.exe start [-inprocess]` |*-Reprocess*: starta emulatorn i den aktuella processen i stället för att skapa en ny process. |
| **Stopp** |Stoppar Storage-emulatorn. |`AzureStorageEmulator.exe stop` | |
| **Status** |Skriver ut statusen för Storage-emulatorn. |`AzureStorageEmulator.exe status` | |
| **Rensa** |Rensar data i alla tjänster som anges på kommando raden. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*BLOB*: rensar BLOB-data. <br/>*Queue*: tar bort köa data. <br/>*tabell*: raderar tabell data. <br/>*alla*: alla data i alla tjänster raderas. |
| **Initiering** |Konfigurerar emulatorn vid ett engångs initiering. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-Server serverName\instanceName*: anger den server som är värd för SQL-instansen. <br/>*-Sqlinstance instans*namn: anger namnet på den SQL-instans som ska användas i standard Server instansen. <br/>*-forcecreate*: tvingar fram skapandet av SQL-databasen, även om den redan finns. <br/>*-skipcreate*: hoppar över skapandet av SQL-databasen. Detta prioriteras över-forcecreate.<br/>*-reserveports*: försöker reservera de http-portar som är kopplade till tjänsterna.<br/>*-unreserveports*: försöker ta bort reservationer för de http-portar som är kopplade till tjänsterna. Detta prioriteras över-reserveports.<br/>*-InProcess*: utför initiering i den aktuella processen i stället för att skapa en ny process. Den aktuella processen måste startas med utökade behörigheter om du ändrar port reservationer. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Skillnader mellan Storage-emulatorn och Azure Storage

Eftersom Storage-emulatorn är en lokal emulerad miljö finns det skillnader mellan att använda emulatorn och ett Azure Storage-konto i molnet:

* Storage-emulatorn stöder endast ett fast konto och en välkänd autentiseringsnyckel.
* Lagringsprovidern är inte en skalbar lagrings tjänst och har inte stöd för ett stort antal samtidiga klienter.
* Som det beskrivs i [adresserings resurser i Storage-emulatorn](#addressing-resources-in-the-storage-emulator), adresseras resurser på ett annat sätt i Storage-emulatorn jämfört med ett Azure Storage-konto. Skillnaden är att domän namns matchning är tillgängligt i molnet men inte på den lokala datorn.
* Från och med version 3,1 stöder Storage emulator-kontot Read-Access Geo-redundant replikering (RA-GRS). I emulatorn har alla konton RA-GRS aktiverat och det finns aldrig någon fördröjning mellan de primära och sekundära replikerna. Åtgärderna Hämta BLOB service stats statistik, Hämta Queue Service-statistik och hämta Table service-statistik stöds på det sekundära kontot och returnerar alltid värdet för `LastSyncTime` Response-elementet som den aktuella tiden enligt den underliggande SQL-databasen.
* Slut punkterna för fil tjänsten och SMB-protokollen stöds för närvarande inte i Storage-emulatorn.
* Om du använder en version av lagrings tjänster som inte stöds av emulatorn returnerar emulatorn ett VersionNotSupportedByEmulator-fel (HTTP-status kod 400-Felaktig begäran).

### <a name="differences-for-blob-storage"></a>Skillnader för Blob Storage

Följande skillnader gäller Blob Storage i emulatorn:

* Storage-emulatorn stöder bara BLOB-storlekar upp till 2 GB.
* Den maximala längden för ett BLOB-namn i Storage-emulatorn är 256 tecken, medan den maximala längden för ett BLOB-namn i Azure Storage är 1024 tecken.
* Med en stegvis kopia kan ögonblicks bilder från skrivna blobbar kopieras, vilket returnerar ett problem med tjänsten.
* Hämtning av sid intervall fungerar inte mellan ögonblicks bilder som kopierats med en stegvis kopierings-blob.
* En placerings-BLOB-åtgärd kan lyckas mot en blob som finns i Storage-emulatorn med ett aktivt lån även om låne-ID: t inte har angetts i begäran.
* Det går inte att lägga till BLOB-åtgärder av emulatorn. Försök att utföra en åtgärd på en append-BLOB returnerar ett FeatureNotSupportedByEmulator-fel (HTTP-status kod 400-Felaktig begäran).

### <a name="differences-for-table-storage"></a>Skillnader i tabell lagring

Följande skillnader gäller för Table Storage i emulatorn:

* Datum egenskaper i Table service i Storage-emulatorn stöder bara det intervall som stöds av SQL Server 2005 (de måste vara senare än 1 januari 1753). Alla datum före den 1 januari 1753 ändras till det här värdet. Precisionen för datum är begränsad till precisionen för SQL Server 2005, vilket innebär att datumen är exakta till 1/300th av en sekund.
* Storage-emulatorn stöder partitionsnyckel och rad nyckel egenskaps värden som är mindre än 512 byte. Den totala storleken på konto namn, tabell namn och nyckel egenskaps namn får inte överstiga 900 byte.
* Den totala storleken på en rad i en tabell i Storage-emulatorn är begränsad till mindre än 1 MB.
* I Storage-emulatorn, egenskaper för data typ `Edm.Guid` eller `Edm.Binary` endast stöd `Equal (eq)` `NotEqual (ne)` operatorer och jämförelse operatorer i filter strängar för frågor.

### <a name="differences-for-queue-storage"></a>Skillnader för Queue Storage

Det finns inga skillnader vad gäller Queue Storage i emulatorn.

## <a name="storage-emulator-release-notes"></a>Viktig information om Storage mula torn

### <a name="version-510"></a>Version 5,10

* Storage-emulatorn avvisar inte version 2019-07-07 av lagrings tjänsterna på BLOB-, Queue-och Table service-slutpunkter.

### <a name="version-59"></a>Version 5,9

* Storage-emulatorn avvisar inte version 2019-02-02 av lagrings tjänsterna på BLOB-, Queue-och Table service-slutpunkter.

### <a name="version-58"></a>Version 5,8

* Storage-emulatorn avvisar inte version 2018-11-09 av lagrings tjänsterna på BLOB-, Queue-och Table service-slutpunkter.

### <a name="version-57"></a>Version 5,7

* En bugg har åtgärd ATS som orsakar en krasch om loggning har Aktiver ATS.

### <a name="version-56"></a>Version 5,6

* Storage-emulatorn stöder nu version 2018-03-28 av lagrings tjänsterna på BLOB-, Queue-och Table service-slutpunkter.

### <a name="version-55"></a>Version 5,5

* Storage-emulatorn stöder nu version 2017-11-09 av lagrings tjänsterna på BLOB-, Queue-och Table service-slutpunkter.
* Stöd har lagts till för den BLOB- **skapade** egenskapen som returnerar blobens skapande tid.

### <a name="version-54"></a>Version 5,4

* För att förbättra installations stabiliteten försöker emulatorn inte längre att reservera portar vid installations tiden. Om du vill ha port reservationer använder du alternativet *-reserveports* för kommandot **init** för att ange dem.

### <a name="version-53"></a>Version 5,3

* Storage-emulatorn stöder nu version 2017-07-29 av lagrings tjänsterna på BLOB-, Queue-och Table service-slutpunkter.

### <a name="version-52"></a>Version 5,2

* Storage-emulatorn stöder nu version 2017-04-17 av lagrings tjänsterna på BLOB-, Queue-och Table service-slutpunkter.
* Ett fel har åtgärd ATS där tabell egenskaps värden inte kunde kodas korrekt.

### <a name="version-51"></a>Version 5,1

* Ett fel har åtgärd ATS där Storage-emulatorn returnerade `DataServiceVersion` rubriken i vissa svar där tjänsten inte var det.

### <a name="version-50"></a>Version 5,0

* Installations programmet för Storage-emulatorn söker inte längre efter befintliga MSSQL-och .NET Framework-installationer.
* Installations programmet för Storage-emulatorn skapar inte längre databasen som en del av installationen. Databasen kommer fortfarande att skapas om det behövs som en del av starten.
* Databas skapandet kräver inte längre utökade privilegier.
* Port reservationer behövs inte längre för start.
* Lägger till följande alternativ i `init` : `-reserveports` (kräver höjning), `-unreserveports` (kräver höjning) `-skipcreate` .
* Alternativet Storage emulator UI på ikonen i system fältet startar nu kommando rads gränssnittet. Det gamla användar gränssnittet är inte längre tillgängligt.
* Vissa DLL-filer har tagits bort eller bytt namn.

### <a name="version-46"></a>Version 4,6

* Storage-emulatorn stöder nu version 2016-05-31 av lagrings tjänsterna på BLOB-, Queue-och Table service-slutpunkter.

### <a name="version-45"></a>Version 4,5

* En bugg har åtgärd ATS som gjorde att installationen och initieringen Miss fungerar när den säkerhetskopierade databasen har bytt namn.

### <a name="version-44"></a>Version 4,4

* Storage-emulatorn stöder nu version 2015-12-11 av lagrings tjänsterna på BLOB-, Queue-och Table service-slutpunkter.
* Lagrings emulatorns skräp insamling av BLOB-data är nu mer effektiv när du hanterar ett stort antal blobbar.
* En bugg har åtgärd ATS som gjorde att XML för behållar-ACL verifierades något annorlunda än i lagrings tjänsten.
* En bugg har åtgärd ATS som ibland orsakade att max-och minsta DateTime-värden rapporteras i fel tidszon.

### <a name="version-43"></a>Version 4,3

* Storage-emulatorn stöder nu version 2015-07-08 av lagrings tjänsterna på BLOB-, Queue-och Table service-slutpunkter.

### <a name="version-42"></a>Version 4,2

* Storage-emulatorn stöder nu version 2015-04-05 av lagrings tjänsterna på BLOB-, Queue-och Table service-slutpunkter.

### <a name="version-41"></a>Version 4,1

* Storage-emulatorn stöder nu version 2015-02-21 av lagrings tjänsterna på BLOB-, Queue-och Table service-slutpunkter. Den har inte stöd för de nya tilläggs-BLOB-funktionerna.
* Emulatorn returnerar nu ett meningsfullt fel meddelande för versioner av lagrings tjänster som inte stöds. Vi rekommenderar att du använder den senaste versionen av emulatorn. Om du får ett VersionNotSupportedByEmulator-fel (HTTP-status kod 400-Felaktig begäran) laddar du ned den senaste versionen av emulatorn.
* Ett fel har åtgärd ATS där ett tävlings villkor orsakade att tabell enhets data var felaktiga under samtidiga sammanslagnings åtgärder.

### <a name="version-40"></a>Version 4,0

* Den körbara filen för lagrings emulatorn har döpts om till *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Version 3,2

* Storage-emulatorn stöder nu version 2014-02-14 av lagrings tjänsterna på BLOB-, Queue-och Table service-slutpunkter. Fil tjänstens slut punkter stöds inte för närvarande i Storage-emulatorn. Mer information om version 2014-02-14 finns i [versions hantering för Azure Storage-tjänsterna](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) .

### <a name="version-31"></a>Version 3,1

* Read-Access Geo-redundant lagring (RA-GRS) stöds nu i Storage-emulatorn. `Get Blob Service Stats`API: `Get Queue Service Stats` erna, och `Get Table Service Stats` har stöd för det sekundära kontot och returnerar alltid värdet för LastSyncTime-svars elementet som den aktuella tiden enligt den underliggande SQL-databasen. Använd lagrings klient biblioteket för .NET version 3,2 eller senare för program mässig åtkomst till den sekundära med Storage-emulatorn. Mer information finns i Microsoft Azure Storage klient bibliotek för .NET-referens.

### <a name="version-30"></a>Version 3,0

* Azure Storage emulatorn levereras inte längre i samma paket som Compute-emulatorn.
* Grafiskt användar gränssnitt i Storage-emulatorn är inaktuellt. Den har ersatts av ett skript bara kommando rads gränssnitt. Mer information om kommando rads gränssnittet finns i kommando rads verktyget Storage mula reference. Det grafiska gränssnittet fortsätter att finnas i version 3,0, men det går bara att komma åt den när du har installerat beräknings-emulatorn genom att högerklicka på ikonen system fält och välja Visa användar gränssnitt för Storage-emulatorn.
* Version 2013-08-15 av Azure Storage-tjänster stöds nu fullt ut. (Tidigare har den här versionen endast stöd för för hands versionen av Storage mula version 2.2.1.)

## <a name="next-steps"></a>Nästa steg

* Utvärdera den plattforms oberoende [Azurite](https://github.com/azure/azurite)för lagring med öppen källkod. 
* [Azure Storage exempel som använder .net](../storage-samples-dotnet.md) innehåller länkar till flera kod exempel som du kan använda när du utvecklar ditt program.
* Du kan använda [Microsoft Azure Storage Explorer](https://storageexplorer.com) för att arbeta med resurser i ditt moln lagrings konto och i Storage-emulatorn.

## <a name="see-also"></a>Se även

* [Lokal Azure Storage utveckling med Azurite, Azure SDK: er och Azure Storage Explorer](https://blog.jongallant.com/2020/04/local-azure-storage-development-with-azurite-azuresdks-storage-explorer/)
