---
title: Lagra block-blobar på enheter – Azure IoT Edge | Microsoft Docs
description: Förstå nivåer och tids till Live-funktioner finns i Blob Storage-åtgärder som stöds och Anslut till ditt Blob Storage-konto.
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5954c3083afc73fb25c796086f8fb8809af03ec1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200672"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>Lagra data på gränsen med Azure Blob Storage på IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure Blob Storage på IoT Edge tillhandahåller en [Block-Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) och [lägger till en BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) Storage-lösning på gränsen. En Blob Storage-modul på din IoT Edge-enhet fungerar som en Azure blob-tjänst, förutom att blobbar lagras lokalt på IoT Edge enheten. Du kan komma åt dina blobar med samma Azure Storage SDK-metoder eller BLOB-API-anrop som du redan använder. I den här artikeln beskrivs begreppen för Azure Blob Storage på IoT Edge behållare som kör en blob-tjänst på din IoT Edge enhet.

Den här modulen är användbar i scenarier:

* var data måste lagras lokalt tills den kan bearbetas eller överföras till molnet. Dessa data kan vara videor, bilder, ekonomi data, sjukhus data eller andra ostrukturerade data.
* När enheterna finns på en plats med begränsad anslutning.
* När du effektivt vill bearbeta data lokalt för att få låg latens åtkomst till data, så att du kan reagera på nödfall så snabbt som möjligt.
* När du vill minska bandbredds kostnaderna och undvika att överföra terabyte data till molnet. Du kan bearbeta data lokalt och bara skicka bearbetade data till molnet.

Titta på videon för snabb introduktion
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

Den här modulen innehåller funktioner för **deviceToCloudUpload** och **deviceAutoDelete** .

**deviceToCloudUpload** är en konfigurerbar funktion. Den här funktionen överför automatiskt data från den lokala blob-lagringen till Azure med stöd för Internet anslutning utan avbrott. Det gör att du kan:

* Aktivera/inaktivera deviceToCloudUpload-funktionen.
* Välj i vilken ordning data ska kopieras till Azure som NewestFirst eller OldestFirst.
* Ange det Azure Storage konto som du vill att dina data ska överföras till.
* Ange de behållare som du vill överföra till Azure. Med den här modulen kan du ange namn på både käll-och mål behållare.
* Välj möjligheten att ta bort Blobbarna direkt efter att överföringen till moln lagringen är färdig
* Utför fullständig BLOB-uppladdning (med `Put Blob` drift) och överföring på Block nivå (med `Put Block` , `Put Block List` och `Append Block` åtgärder).

I den här modulen används överföring på Block nivå, när blobben består av block. Här följer några vanliga scenarier:

* Ditt program uppdaterar vissa block av en tidigare överförd Block-Blob eller lägger till nya block i en tilläggs-blob. den här modulen laddar bara upp de uppdaterade blocken och inte hela bloben.
* Modulen laddar upp blob och Internet-anslutningen stängs av när anslutningen är tillbaka och laddar bara om de återstående blocken och inte hela bloben.

Om en oväntad process terminering (t. ex. strömavbrott) sker under en BLOB-uppladdning, laddas alla block som har förfallit till uppladdningen igen när modulen är online igen.

**deviceAutoDelete** är en konfigurerbar funktion. Den här funktionen tar automatiskt bort blobbar från den lokala lagrings platsen när den angivna varaktigheten (mätt i minuter) går ut. Det gör att du kan:

* Aktivera/inaktivera deviceAutoDelete-funktionen.
* Ange tiden i minuter (deleteAfterMinutes) efter vilken Blobbarna ska tas bort automatiskt.
* Välj möjligheten att behålla blobben när den laddas upp om deleteAfterMinutes-värdet upphör att gälla.

## <a name="prerequisites"></a>Förutsättningar

En Azure IoT Edge-enhet:

* Du kan använda din utvecklings dator eller en virtuell dator som en IoT Edge enhet genom att följa stegen i snabb starten för [Linux](quickstart-linux.md) -eller [Windows-enheter](quickstart.md).

* Se [Azure IoT Edge system som stöds](support.md#operating-systems) för en lista över operativ system och arkitekturer som stöds. Azure-Blob Storage i IoT Edge-modulen stöder följande arkitekturer:
  * Windows AMD64
  * Linux AMD64
  * Linux ARM32
  * Linux-ARM64 (för hands version)

Molnresurser:

En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på standardnivå i Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>egenskaper för deviceToCloudUpload och deviceAutoDelete

Använd modulens önskade egenskaper för att ange **deviceToCloudUploadProperties** och **deviceAutoDeleteProperties**. Du kan ställa in önskade egenskaper under distributionen eller ändra dem senare genom att redigera modulen dubbla utan att behöva distribuera om. Vi rekommenderar att du kontrollerar "Modulin" för `reported configuration` och `configurationValidation` för att se till att värdena är korrekt spridda.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

Namnet på den här inställningen är `deviceToCloudUploadProperties` . Om du använder IoT Edge simulatorn ställer du in värdena på de relaterade miljövariablerna för dessa egenskaper, som du hittar i förklarings avsnittet.

| Egenskap | Möjliga värden | Förklaring |
| ----- | ----- | ---- |
| uploadOn | SANT, FALSKT | Ange som `false` standard. Om du vill aktivera funktionen väljer du det här fältet till `true` . <br><br> Miljö variabel: `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Gör att du kan välja i vilken ordning data ska kopieras till Azure. Ange som `OldestFirst` standard. Ordningen bestäms efter tidpunkten för den senaste ändringen av blobben. <br><br> Miljö variabel: `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` är en anslutnings sträng som gör att du kan ange det lagrings konto som du vill att dina data ska överföras till. Ange `Azure Storage Account Name` , `Azure Storage Account Key` , `End point suffix` . Lägg till lämpliga EndpointSuffix av Azure där data ska överföras, det varierar för Global Azure, Azure och Microsoft Azure Stack. <br><br> Du kan välja att ange Azure Storage SAS-anslutningssträng här. Men du måste uppdatera den här egenskapen när den upphör att gälla. SAS-behörigheter kan innehålla skapa åtkomst för behållare och skapa, skriva och lägga till åtkomst för blobbar.  <br><br> Miljö variabel: `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Gör att du kan ange de behållar namn som du vill överföra till Azure. Med den här modulen kan du ange namn på både käll-och mål behållare. Om du inte anger namnet på mål behållaren tilldelas behållar namnet automatiskt som `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>` . Du kan skapa mall strängar för mål behållar namn, se kolumnen möjliga värden. <br>*% h – > IoT Hub namn (3-50 tecken). <br>*% d-> IoT Edge enhets-ID (1 till 129 tecken). <br>*% m – > modulens namn (1 till 64 tecken). <br>*% c – > käll behållar namn (3 till 63 tecken). <br><br>Den maximala storleken på behållar namnet är 63 tecken och tilldelar mål behållar namnet automatiskt om storleken på containern överskrider 63 tecken så trimmas varje avsnitt (IoTHubName, IotEdgeDeviceID, Modulnamn, SourceContainerName) till 15 tecken. <br><br> Miljö variabel: `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| deleteAfterUpload | SANT, FALSKT | Ange som `false` standard. När det är inställt på tas `true` data bort automatiskt när uppladdning till moln lagring är klart. <br><br> **Varning**! om du använder tillägg för att lägga till blobar, kommer den här inställningen att ta bort tillägg till blobar från lokal lagring efter en lyckad uppladdning, och eventuella framtida tillägg till block-åtgärder till dessa blobbar kommer att Miss lyckas. Använd den här inställningen med försiktighet, aktivera inte detta om ditt program inte har några frekventa tillägg eller inte stöder kontinuerliga tillägg-åtgärder<br><br> Miljö variabel: `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` . |

### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

Namnet på den här inställningen är `deviceAutoDeleteProperties` . Om du använder IoT Edge simulatorn ställer du in värdena på de relaterade miljövariablerna för dessa egenskaper, som du hittar i förklarings avsnittet.

| Egenskap | Möjliga värden | Förklaring |
| ----- | ----- | ---- |
| deleteOn | SANT, FALSKT | Ange som `false` standard. Om du vill aktivera funktionen väljer du det här fältet till `true` . <br><br> Miljö variabel: `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Ange tiden i minuter. Dina blobar tas bort automatiskt från den lokala lagrings platsen när det här värdet upphör att gälla. Aktuellt maximalt antal minuter är 35791. <br><br> Miljö variabel: `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | SANT, FALSKT | Som standard är den inställd på `true` och den behåller blobben medan den laddas upp till moln lagring om deleteAfterMinutes upphör att gälla. Du kan ställa in det på så att `false` det tar bort data så snart deleteAfterMinutes går ut. Obs: för att den här egenskapen ska fungera måste uploadOn anges till sant.  <br><br> **Varning!** om du använder tillägg för att lägga till blobar kommer den här inställningen att ta bort tillägg till blobar från lokal lagring när värdet upphör att gälla, och eventuella framtida tilläggs block åtgärder till dessa blobar Miss kommer. Du kanske vill kontrol lera att värdet för förfallo datum är tillräckligt stort för den förväntade frekvensen för att lägga till åtgärder som utförs av ditt program.<br><br> Miljö variabel: `deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>Använda SMB-resurs som lokal lagring

Du kan ange SMB-resurs som din lokala lagrings Sök väg när du distribuerar Windows-behållare för den här modulen på Windows-värden.

Kontrol lera att SMB-resursen och IoT-enheten finns i ömsesidigt betrodda domäner.

Du kan köra `New-SmbGlobalMapping` PowerShell-kommandot för att MAPPA SMB-resursen lokalt på IoT-enheten som kör Windows.

Nedan visas konfigurations stegen:

```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```

Exempel:

```powershell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G:
```

Det här kommandot använder autentiseringsuppgifterna för att autentisera med fjärr-SMB-servern. Mappa sedan sökvägen till fjär resursen till G: enhets beteckningen (kan vara en annan tillgänglig enhets beteckning). IoT-enheten har nu data volymen mappad till en sökväg på enheten G:.

Se till att användaren i IoT-enheten kan läsa och skriva till fjärr-SMB-resursen.

För distributionen `<storage mount>` kan värdet vara **G:/ContainerData: C:/BlobRoot**.

## <a name="granting-directory-access-to-container-user-on-linux"></a>Bevilja katalog åtkomst till behållar användare i Linux

Om du har använt [volym montering](https://docs.docker.com/storage/volumes/) för lagring i dina alternativ för att skapa en Linux-behållare behöver du inte göra några ytterligare steg, men om du använde [BIND Mount](https://docs.docker.com/storage/bind-mounts/) krävs dessa steg för att köra tjänsten på rätt sätt.

Enligt principen om minsta behörighet för att begränsa åtkomst behörigheterna för användare till minimalt minimal behörighet som de behöver för att utföra sitt arbete, innehåller den här modulen en användare (namn: absie, ID: 11000) och en användar grupp (namn: absie, ID: 11000). Om behållaren har startats som **rot** (standard användare är **rot**), startas tjänsten som **absie** användare med låg behörighet.

Det här beteendet gör att konfigurationen av behörigheterna för värdsökväg binder är avgörande för att tjänsten ska fungera korrekt, annars kraschar tjänsten med åtkomst nekade fel. Den sökväg som används i katalog bindningen måste vara tillgänglig för behållar användaren (exempel: absie 11000). Du kan bevilja behållar användaren åtkomst till katalogen genom att köra kommandona nedan på värden:

```terminal
sudo chown -R 11000:11000 <blob-dir>
sudo chmod -R 700 <blob-dir>
```

Exempel:

```terminal
sudo chown -R 11000:11000 /srv/containerdata
sudo chmod -R 700 /srv/containerdata
```

Om du behöver köra tjänsten som en annan användare än **absie** kan du ange ditt anpassade användar-ID i createOptions under egenskapen User i distributions manifestet. I så fall måste du använda standard-eller rot grupps-ID: t `0` .

```json
"createOptions": {
  "User": "<custom user ID>:0"
}
```

Bevilja nu behållar användaren åtkomst till katalogen

```terminal
sudo chown -R <user ID>:<group ID> <blob-dir>
sudo chmod -R 700 <blob-dir>
```

## <a name="configure-log-files"></a>Konfigurera loggfiler

Information om hur du konfigurerar loggfiler för modulen finns i [metod tips för produktion](./production-checklist.md#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Ansluta till din Blob Storage-modul

Du kan använda konto namnet och konto nyckeln som du konfigurerade för din modul för att få åtkomst till Blob Storage på din IoT Edge-enhet.

Ange IoT Edge enhet som BLOB-slutpunkt för alla lagrings begär Anden som du gör till den. Du kan [skapa en anslutnings sträng för en explicit lagrings slut punkt](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) med hjälp av IoT Edge enhets information och konto namnet som du har konfigurerat.

* För moduler som har distribuerats på samma enhet som Azure-Blob Storage på IoT Edge modul körs, är BLOB-slutpunkten: `http://<module name>:11002/<account name>` .
* För moduler eller program som körs på en annan enhet måste du välja rätt slut punkt för nätverket. Beroende på din nätverks konfiguration väljer du ett slut punkts format så att data trafiken från den externa modulen eller programmet kan komma åt enheten som kör Azure-Blob Storage i IoT Edge-modulen. BLOB-slutpunkten för det här scenariot är en av:
  * `http://<device IP >:11002/<account name>`
  * `http://<IoT Edge device hostname>:11002/<account name>`
  * `http://<fully qualified domain name>:11002/<account name>`
 
 > [!IMPORTANT]
 > Azure IoT Edge är Skift läges känslig när du anropar moduler, och Storage SDK: n är också i gemener. Även om namnet på modulen på [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) är **AzureBlobStorageonIoTEdge** kan du med hjälp av att ändra namnet till gemener se till att dina anslutningar till Azure-Blob Storage i IoT Edge-modulen inte avbryts.
 
## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob Storage snabb starts exempel

Azure Blob Storage-dokumentationen innehåller exempel kod för snabb start på flera språk. Du kan köra dessa exempel för att testa Azure Blob Storage på IoT Edge genom att ändra BLOB-slutpunkten för att ansluta till din lokala Blob Storage-modul.

Följande snabb starts exempel använder språk som också stöds av IoT Edge, så du kan distribuera dem som IoT Edge moduler tillsammans med Blob Storage-modulen:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
  * Azure Blob Storage på IoT Edge-modul v-1.4.0 och tidigare är kompatibla med WindowsAzure. Storage 9.3.3 SDK och v 1.4.1 stöder också Azure. Storage. blobs 12.8.0 SDK.
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * Versioner före V 2.1 i python SDK har ett känt problem där modulen inte returnerar skapande tid för BLOB. På grund av det här problemet fungerar inte vissa metoder som List blobbar. Som en lösning anger du uttryckligen API-versionen på BLOB-klienten till "2017-04-17". Exempel  `block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [Lägg till BLOB-exempel](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-legacy.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-legacy.md)
* [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [Kör](../storage/blobs/storage-quickstart-blobs-go.md)
* [PHP](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Ansluta till din lokala lagrings plats med Azure Storage Explorer

Du kan använda [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att ansluta till ditt lokala lagrings konto.

1. Ladda ned och installera Azure Storage Explorer

1. Anslut till Azure Storage med hjälp av en anslutnings sträng

1. Ange anslutnings sträng: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Gå igenom stegen för att ansluta.

1. Skapa behållare inuti ditt lokala lagrings konto

1. Starta överföring av filer som block-blobbar eller bifoga blobbar.
   > [!NOTE]
   > Den här modulen stöder inte sid-blobar.

1. Du kan också välja att ansluta dina Azure Storage-konton i Storage Explorer. Med den här konfigurationen får du en enda vy för både ditt lokala lagrings konto och ditt Azure Storage-konto

## <a name="supported-storage-operations"></a>Lagrings åtgärder som stöds

Blob Storage-moduler på IoT Edge använda Azure Storage SDK: er och är konsekvent med 2017-04-17-versionen av Azure Storage API för Block-Blob-slutpunkter.

Eftersom inte alla Azure Blob Storage-åtgärder stöds av Azure Blob Storage på IoT Edge, visar det här avsnittet status för var och en.

### <a name="account"></a>Konto

Tillåtna

* Visa en lista med containrar

Som inte stöds

* Hämta och ange egenskaper för BLOB service
* Begäran om preflight-BLOB
* Hämta BLOB service-statistik
* Hämta konto information

### <a name="containers"></a>Containers

Tillåtna

* Skapa och ta bort behållare
* Hämta egenskaper för behållare och metadata
* Lista blobar
* Hämta och ange behållar-ACL
* Ange metadata för behållare

Som inte stöds

* Lease container

### <a name="blobs"></a>Blobar

Tillåtna

* Placera, hämta och ta bort BLOB
* Hämta och ange BLOB-egenskaper
* Hämta och ange BLOB-metadata

Som inte stöds

* Låna BLOB
* Ögonblicks bilds-BLOB
* Kopiera och Avbryt kopiering av BLOB
* Ångra borttagning av BLOB
* Ange BLOB-nivå

### <a name="block-blobs"></a>Blockblobar

Tillåtna

* Spärra block
* Placera och hämta blockeringslistan

Som inte stöds

* Skicka block från URL

### <a name="append-blobs"></a>Tilläggsblobar

Tillåtna

* Lägg till block

Som inte stöds

* Lägg till block från URL

## <a name="event-grid-on-iot-edge-integration"></a>Event Grid på IoT Edge-integrering

> [!CAUTION]
> Integrationen med Event Grid på IoT Edge är i för hands version

Den här Azure-Blob Storage i IoT Edge-modulen tillhandahåller nu integrering med Event Grid på IoT Edge. Detaljerad information om den här integrationen finns i [självstudien distribuera moduler, publicera händelser och verifiera händelse leverans](../event-grid/edge/react-blob-storage-events-locally.md).

## <a name="release-notes"></a>Viktig information

Här är [viktig information i Docker Hub](https://hub.docker.com/_/microsoft-azure-blob-storage) för den här modulen. Du kanske kan hitta mer information om fel korrigeringar och reparationer i versions anteckningarna för en speciell version.

## <a name="suggestions"></a>Förslag

Din feedback är viktig för oss att göra den här modulen och dess funktioner användbara och lätta att använda. Dela din feedback och berätta för oss hur vi kan förbättra.

Du kan kontakta oss på absiotfeedback@microsoft.com

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuerar Azure Blob Storage på IoT Edge](how-to-deploy-blob.md)

Håll dig uppdaterad med senaste uppdateringar och meddelande i [Azure Blob Storage på IoT Edge blogg](https://aka.ms/abs-iot-blogpost)
