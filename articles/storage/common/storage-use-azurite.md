---
title: Använd Azurite-emulatorn för lokal Azure Storage utveckling
description: Azurite-emulatorn med öppen källkod tillhandahåller en kostnads fri lokal miljö för att testa dina Azure Storage-program.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/15/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: b5051a5e29f9e34c43963a9a264ee47e2ad7b04a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92490789"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development"></a>Använd Azurite-emulatorn för lokal Azure Storage utveckling

Azurite-emulatorn med öppen källkod tillhandahåller en kostnads fri lokal miljö för att testa dina Azure blob-och Queue Storage-program. När du är nöjd med hur ditt program fungerar lokalt växlar du till att använda ett Azure Storage konto i molnet. Emulatorn ger plattforms oberoende stöd för Windows, Linux och macOS.

Azurite är den framtida Storage mula-plattformen. Azurite ersätter Azure Storage- [emulatorn](storage-use-emulator.md). Azurite kommer fortfarande att uppdateras för att stödja de senaste versionerna av Azure Storage-API: er.

Det finns flera olika sätt att installera och köra Azurite på det lokala systemet:

  1. [Installera och kör kod tillägget för Visual Studio-Azurite](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Installera och kör Azurite med NPM](#install-and-run-azurite-by-using-npm)
  1. [Installera och köra Azurite Docker-avbildningen](#install-and-run-the-azurite-docker-image)
  1. [Klona, skapa och köra Azurite från GitHub-lagringsplatsen](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Installera och kör kod tillägget för Visual Studio-Azurite

I Visual Studio Code väljer du rutan **tillägg** och söker efter *Azurite* i **tilläggen: Marketplace**.

![Visual Studio Code Extensions Marketplace](media/storage-use-azurite/azurite-vs-code-extension.png)

Du kan också gå till [Visual Studio Code Extension marknad](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) i webbläsaren. Välj knappen **Installera** för att öppna Visual Studio Code och gå direkt till Azurite-tilläggs sidan.

Tillägget har stöd för följande Visual Studio Code-kommandon. Öppna kommando paletten genom att trycka på F1 i Visual Studio Code. 

   - **Azurite: Rensa** -Återställ alla Azurite Services persistency-data
   - **Azurite: Rensa BLOB** service-rensad BLOB service
   - **Azurite: Rensa Queue Service** -rensa Queue Service
   - **Azurite: Stäng** alla Azurite-tjänster
   - **Azurite: Stäng BLOB service** – Stäng BLOB service
   - **Azurite: Stäng Queue Service** -Stäng Queue Service
   - **Azurite: starta** – starta alla Azurite-tjänster
   - **Azurite: starta BLOB service** – starta BLOB service
   - **Azurite: starta Queue Service** – starta Queue Service

Välj fönstret tillägg om du vill konfigurera Azurite i Visual Studio Code. Välj ikonen **Hantera** (kugg hjul) för **Azurite**. Välj **Inställningar för tillägg**.

![Azurites konfigurera tilläggs inställningar](media/storage-use-azurite/azurite-configure-extension-settings.png)

Följande inställningar stöds:

   - **Azurite: BLOB Host** – slut punkten för BLOB service lyssning. Standardvärdet är 127.0.0.1.
   - **Azurite: BLOB-port** – BLOB service lyssnings port. Standard porten är 10000.
   - **Azurite: cert** -Path till en lokalt betrodd PEM eller PFX-certifikat fil Sök väg för att aktivera https-läge.
   - **Azurite: Felsök** – mata ut fel söknings loggen till Azurite-kanalen. Standardvärdet är **falskt**.
   - **Azurite: nyckel** Sök väg till en lokalt betrodd PEM-nyckel fil som krävs när **Azurite: cert** pekar på en PEM-fil.
   - **Azurite: plats** – sökvägen till arbets ytans plats. Standardvärdet är Visual Studio Code-arbetsmappen.
   - **Azurite:** löst – aktivera löst läge, vilket ignorerar huvuden och parametrar som inte stöds.
   - **Azurite:** OAuth-valfri OAuth-nivå.
   - **Azurite: PWD** -Password för PFX-fil. Krävs när **Azurite: cert** pekar på en PFX-fil.
   - **Azurite: köa värd** -den kötjänst lyssnar slut punkten. Standardvärdet är 127.0.0.1.
   - **Azurite: Queue port** -den kötjänst lyssnings porten. Standard porten är 10001.
   - **Azurite: tyst** -tyst läge inaktiverar åtkomst loggen. Standardvärdet är **falskt**.
   - **Azurite: hoppa över kontroll av API-version** – hoppa över kontroll av begäran API-version. Standardvärdet är **falskt**.

## <a name="install-and-run-azurite-by-using-npm"></a>Installera och kör Azurite med NPM

Den här installations metoden kräver att du har [Node.js version 8,0 eller senare](https://nodejs.org) installerad. Node Package Manager (NPM) är paket hanterings verktyget som ingår i varje Node.js-installation. När du har installerat Node.js kör du följande `npm` kommando för att installera Azurite.

```console
npm install -g azurite
```

När du har installerat Azurite kan du läsa [Kör Azurite från en kommando rad](#run-azurite-from-a-command-line).

## <a name="install-and-run-the-azurite-docker-image"></a>Installera och köra Azurite Docker-avbildningen

Använd [DockerHub](https://hub.docker.com/) för att hämta den [senaste Azurite-avbildningen](https://hub.docker.com/_/microsoft-azure-storage-azurite) med hjälp av följande kommando:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Kör Azurite Docker-avbildningen**:

Följande kommando kör Azurite Docker-avbildningen. `-p 10000:10000`Parametern omdirigerar begär Anden från värd datorns port 10000 till Docker-instansen.

```console
docker run -p 10000:10000 -p 10001:10001 \
    mcr.microsoft.com/azure-storage/azurite
```

**Ange arbets ytans plats**:

I följande exempel `-v c:/azurite:/data` anger parametern *c:/Azurite* som den Azurite sparade data platsen. Katalogen, *c:/Azurite*, måste skapas innan du kör Docker-kommandot.

```console
docker run -p 10000:10000 -p 10001:10001 \
    -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Kör bara BLOB service**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite \
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

Mer information om hur du konfigurerar Azurite vid start finns i [kommando rads alternativ](#command-line-options).

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>Klona, skapa och köra Azurite från GitHub-lagringsplatsen

Den här installations metoden kräver att du har [git](https://git-scm.com/) installerat. Klona [GitHub-lagringsplatsen](https://github.com/azure/azurite) för Azurite-projektet med hjälp av följande konsol kommando.

```console
git clone https://github.com/Azure/Azurite.git
```

När du har klonat käll koden kör du följande kommandon från roten av den klonade lagrings platsen för att bygga och installera Azurite.

```console
npm install
npm run build
npm install -g
```

När du har installerat och skapat Azurite kan du läsa [Kör Azurite från en kommando rad](#run-azurite-from-a-command-line).

## <a name="run-azurite-from-a-command-line"></a>Köra Azurite från en kommando rad

> [!NOTE]
> Azurite kan inte köras från kommando raden om du bara har installerat Visual Studio Code-tillägget. Använd i stället kommando paletten Visual Studio Code. Mer information finns i [Installera och köra Azurite Visual Studio Code Extension](#install-and-run-the-azurite-visual-studio-code-extension).

Kom igång direkt med kommando raden genom att skapa en katalog med namnet *c:\azurite* och sedan starta Azurite genom att utfärda följande kommando:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Det här kommandot instruerar Azurite att lagra alla data i en viss katalog, *c:\azurite*. Om `--location` alternativet utelämnas används den aktuella arbets katalogen.

## <a name="command-line-options"></a>Kommandoradsalternativ

Det här avsnittet innehåller information om kommando rads växlar som är tillgängliga när du startar Azurite.

### <a name="help"></a>Help

**Valfritt** -Hämta kommando rads hjälp med `-h` `--help` växeln eller.

```console
azurite -h
azurite --help
```

### <a name="blob-listening-host"></a>BLOB-lyssnings värd

**Valfritt** – Azurite lyssnar som standard till 127.0.0.1 som den lokala servern. Använd `--blobHost` växeln för att ange adressen till dina krav.

Acceptera endast begär Anden på den lokala datorn:

```console
azurite --blobHost 127.0.0.1
```

Tillåt fjärrbegäranden:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> Att tillåta fjärrbegäranden kan göra systemet sårbart för externa attacker.

### <a name="blob-listening-port-configuration"></a>Konfiguration av BLOB-lyssnings port

 Som standard kommer Azurite att lyssna efter BLOB service på port 10000. Använd `--blobPort` växeln för att ange den lyssnings port som du behöver.

> [!NOTE]
> När du har använt en anpassad port måste du uppdatera anslutnings strängen eller motsvarande konfiguration i Azure Storage verktyg eller SDK: er.

Anpassa Blob Service lyssnings port:

```console
azurite --blobPort 8888
```

Låt systemet automatiskt välja en tillgänglig port:

```console
azurite --blobPort 0
```

Porten som används visas vid start av Azurite.

### <a name="queue-listening-host"></a>Köa avlyssnings värd

**Valfritt** – Azurite lyssnar som standard till 127.0.0.1 som den lokala servern. Använd `--queueHost` växeln för att ange adressen till dina krav.

Acceptera endast begär Anden på den lokala datorn:

```console
azurite --queueHost 127.0.0.1
```

Tillåt fjärrbegäranden:

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> Att tillåta fjärrbegäranden kan göra systemet sårbart för externa attacker.

### <a name="queue-listening-port-configuration"></a>Köa lyssnings port konfiguration

 Som standard kommer Azurite att lyssna efter kötjänst på port 10001. Använd `--queuePort` växeln för att ange den lyssnings port som du behöver.

> [!NOTE]
> När du har använt en anpassad port måste du uppdatera anslutnings strängen eller motsvarande konfiguration i Azure Storage verktyg eller SDK: er.

Anpassa Kötjänst lyssnings port:

```console
azurite --queuePort 8888
```

Låt systemet automatiskt välja en tillgänglig port:

```console
azurite --queuePort 0
```

Porten som används visas vid start av Azurite.

### <a name="workspace-path"></a>Sökväg till arbets ytan

**Valfri** -Azurite lagrar data till den lokala disken under körningen. Använd `-l` växeln eller `--location` för att ange en sökväg som arbets ytans plats. Som standard används den aktuella process arbets katalogen. Observera gemenen "l".

```console
azurite -l c:\azurite
azurite --location c:\azurite
```

### <a name="access-log"></a>Åtkomst logg

**Valfritt** – åtkomst loggen visas som standard i konsol fönstret. Inaktivera visningen av åtkomst loggen med hjälp av `-s` eller- `--silent` växeln.

```console
azurite -s
azurite --silent
```
### <a name="debug-log"></a>Fel söknings logg

**Valfri** – fel söknings loggen innehåller detaljerad information om varje begäran och stack spårning för undantag. Aktivera fel söknings loggen genom att ange en giltig lokal fil Sök väg till `-d` `--debug` växeln eller.

```console
azurite -d path/debug.log
azurite --debug path/debug.log
```

### <a name="loose-mode"></a>Löst läge

**Valfritt** – Azurite använder strikt läge för att blockera begärandehuvuden och parametrar som inte stöds. Inaktivera strikt läge genom att använda `-L` `--loose` växeln eller. Notera kapitalet "L".

```console
azurite -L
azurite --loose
```
### <a name="version"></a>Version

**Valfritt** – Visa det installerade Azurite-versions numret med `-v` `--version` växeln eller.

```console
azurite -v
azurite --version
```

### <a name="certificate-configuration-https"></a>Certifikat konfiguration (HTTPS)

**Valfritt** – Azurite använder http-protokollet. Aktivera HTTPS-läge genom att ange en sökväg till en Privacy Enhanced Mail (. pem) eller [personal information Exchange (. pfx)](/windows-hardware/drivers/install/personal-information-exchange---pfx--files) -certifikat fil till `--cert` växeln.

När `--cert` har angetts för en PEM-fil måste du ange en motsvarande `--key` växel.

```console
azurite --cert path/server.pem --key path/key.pem
```

När `--cert` har angetts för en PFX-fil måste du ange en motsvarande `--pwd` växel.

```console
azurite --cert path/server.pfx --pwd pfxpassword
```

Detaljerad information om hur du skapar PEM-och PFX-filer finns i [https-installation](https://github.com/Azure/Azurite/blob/master/README.md#https-setup).

### <a name="oauth-configuration"></a>OAuth-konfiguration

**Valfri** – Aktivera OAuth-autentisering för Azurite med hjälp av `--oauth` växeln.

```console
azurite --oauth basic --cert path/server.pem --key path/key.pem
```

> [!NOTE]
> OAuth kräver en HTTPS-slutpunkt. Kontrol lera att HTTPS har Aktiver ATS genom att tillhandahålla `--cert` en växel tillsammans med `--oauth` växeln.

Azurite stöder grundläggande autentisering genom att ange `basic` parametern till `--oauth` växeln. Azurite kommer att utföra grundläggande autentisering, t. ex. validera inkommande Bearer-token, kontrol lera utfärdare, mål grupp och förfallo datum. Azurite kontrollerar inte signaturen eller behörigheterna för token.

### <a name="skip-api-version-check"></a>Hoppa över kontroll av API-version

**Valfritt** – vid start, kontrollerar Azurite att den BEGÄRda API-versionen är giltig. Följande kommando hoppar över kontroll av API-version:

```console
azurite --skipApiVersionCheck
```


## <a name="authorization-for-tools-and-sdks"></a>Auktorisering för verktyg och SDK: er

Anslut till Azurite från Azure Storage SDK: er eller verktyg, t. ex. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), genom att använda valfri strategi för autentisering. Autentisering krävs. Azurite stöder auktorisering med OAuth, delad nyckel och signaturer för delad åtkomst (SAS). Azurite stöder även anonym åtkomst till offentliga behållare.

Om du använder Azure-SDK: er startar du Azurite med `--oauth basic and --cert --key/--pwd` alternativen.

### <a name="well-known-storage-account-and-key"></a>Välkänt lagrings konto och nyckel

Azurite accepterar samma välkända konto och nyckel som används av den äldre Azure Storage emulatorn.

- Konto namn: `devstoreaccount1`
- Konto nyckel: `Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

### <a name="custom-storage-accounts-and-keys"></a>Anpassade lagrings konton och nycklar

Azurite stöder anpassade lagrings konto namn och nycklar genom att ställa in `AZURITE_ACCOUNTS` miljövariabeln i följande format: `account1:key1[:key2];account2:key1[:key2];...` .

Använd till exempel ett anpassat lagrings konto som har en nyckel:

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

```bash
export AZURITE_ACCOUNTS="account1:key1"
```

Eller Använd flera lagrings konton med två nycklar vardera:

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

```bash
export AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

Azurite uppdaterar anpassade konto namn och nycklar från miljö variabeln varje minut som standard. Med den här funktionen kan du rotera konto nyckeln dynamiskt eller lägga till nya lagrings konton utan att starta om Azurite.

> [!NOTE]
> Standard `devstoreaccount1` lagrings kontot är inaktiverat när du anger anpassade lagrings konton.

### <a name="connection-strings"></a>Anslutningssträngar

Det enklaste sättet att ansluta till Azurite från ditt program är att konfigurera en anslutnings sträng i programmets konfigurations fil som refererar till genvägen *UseDevelopmentStorage = True*. Här är ett exempel på en anslutnings sträng i en *app.config* -fil:

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="http-connection-strings"></a>HTTP-anslutningssträngar

Du kan skicka följande anslutnings strängar till [Azure SDK](https://aka.ms/azsdk) : er eller verktyg, till exempel azure CLI 2,0 eller Storage Explorer.

Den fullständiga anslutnings strängen är:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

Om du bara vill ansluta till Blob-tjänsten är anslutnings strängen:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;`

Om du bara vill ansluta till Queue Service är anslutnings strängen:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

#### <a name="https-connection-strings"></a>HTTPS-anslutningssträngar

Den fullständiga HTTPS-anslutnings strängen är:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Om du bara vill använda Blob-tjänsten är HTTPS-anslutnings strängen:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;`

Om du bara vill använda Queue Service är HTTPS-anslutningssträngen:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

`dotnet dev-certs`Använd följande anslutnings sträng om du använde för att generera ett självsignerat certifikat.

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://localhost:10000/devstoreaccount1;QueueEndpoint=https://localhost:10001/devstoreaccount1;`

Uppdatera anslutnings strängen när du använder [anpassade lagrings konton och nycklar](#custom-storage-accounts-and-keys).

Mer information finns i [Konfigurera anslutnings strängar för Azure Storage](storage-configure-connection-string.md).

### <a name="azure-sdks"></a>Azure-SDK:er

Använd alternativen OAuth och HTTPS för att använda Azurite med [Azure SDK](https://aka.ms/azsdk): er:

```console
azurite --oauth basic --cert certname.pem --key certname-key.pem
```

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Du kan sedan instansiera en BlobContainerClient, BlobServiceClient eller BlobClient.

```csharp
// With container URL and DefaultAzureCredential
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new BlobContainerClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "container-name"
  );

// With account name and key
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

#### <a name="azure-queue-storage"></a>Azure Queue Storage

Du kan också skapa en instans av en QueueClient eller QueueServiceClient.

```csharp
// With queue URL and DefaultAzureCredential
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new QueueClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "queue-name"
  );

// With account name and key
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

Du kan använda Storage Explorer för att visa de data som lagras i Azurite.

#### <a name="connect-to-azurite-using-http"></a>Ansluta till Azurite med HTTP

I Storage Explorer ansluter du till Azurite genom att följa dessa steg:

 1. Välj ikonen **Hantera konton**
 1. Välj **Lägg till ett konto**
 1. Välj **Anslut till en lokal emulator**
 1. Välj **Nästa**
 1. Redigera fältet **visnings namn** till valfritt namn
 1. Välj **Nästa** igen
 1. Välj **Anslut**

#### <a name="connect-to-azurite-using-https"></a>Ansluta till Azurite med HTTPS

Som standard öppnas Storage Explorer inte en HTTPS-slutpunkt som använder ett självsignerat certifikat. Om du kör Azurite med HTTPS använder du förmodligen ett självsignerat certifikat. I Storage Explorer importerar du SSL-certifikat via   ->    ->  dialog rutan Redigera **import certifikat** för SSL-certifikat.

##### <a name="import-certificate-to-storage-explorer"></a>Importera certifikat till Storage Explorer

1. Hitta certifikatet på den lokala datorn.
1. I Storage Explorer går du till **Redigera**  ->  **SSL-certifikat**  ->  **Importera certifikat** och importerar ditt certifikat.

Om du inte importerar ett certifikat får du ett fel meddelande:

`unable to verify the first certificate` eller `self signed certificate in chain`

##### <a name="add-azurite-via-https-connection-string"></a>Lägg till Azurite via HTTPS-anslutningssträng

Följ dessa steg om du vill lägga till Azurite HTTPS i Storage Explorer:

1. Välj **Växla Utforskaren**
1. Välj **lokal & ansluten**
1. Högerklicka på **lagrings konton** och välj **Anslut till Azure Storage**.
1. Välj **Använd en anslutnings sträng**
1. Välj **Nästa**.
1. Ange ett värde i fältet **visnings namn** .
1. Ange [https-anslutningssträngen](#https-connection-strings) från föregående avsnitt i det här dokumentet
1. Välj **Nästa**
1. Välj **Anslut**

## <a name="workspace-structure"></a>Arbets ytans struktur

Följande filer och mappar kan skapas på arbets ytans plats vid initiering av Azurite.

- `__blobstorage__` – Katalog som innehåller Azurite BLOB service bestående binära data
- `__queuestorage__` – Katalog som innehåller binära data för Azurite Queue Service
- `__azurite_db_blob__.json` -Azurite BLOB service-metadatafil
- `__azurite_db_blob_extent__.json` -Azurite BLOB service allokeringsutrymmet metadata-fil
- `__azurite_db_queue__.json` -Azurite Queue Service-metadatafil
- `__azurite_db_queue_extent__.json` -Azurite Queue Service allokeringsutrymmet metadata-fil

Om du vill rensa Azurite tar du bort över filer och mappar och startar om emulatorn.

## <a name="differences-between-azurite-and-azure-storage"></a>Skillnader mellan Azurite och Azure Storage

Det finns funktions skillnader mellan en lokal instans av Azurite och ett Azure Storage konto i molnet.

### <a name="endpoint-and-connection-url"></a>Slut punkt och anslutnings-URL

Tjänstens slut punkter för Azurite skiljer sig från slut punkterna för ett Azure Storage konto. Den lokala datorn utför inte domän namns matchning, vilket kräver att Azurite-slutpunkter är lokala adresser.

När du adresserar en resurs i ett Azure Storage konto är konto namnet en del av URI-värdnamnet. Resursen som ska adresseras är en del av URI-sökvägen:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Följande URI är en giltig adress för en BLOB i ett Azure Storage-konto:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Eftersom den lokala datorn inte har domän namns matchning, är konto namnet en del av URI-sökvägen i stället för värd namnet. Använd följande URI-format för en resurs i Azurite:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Följande adress kan användas för att få åtkomst till en BLOB i Azurite:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Skalning och prestanda

Azurite har inte stöd för ett stort antal anslutna klienter. Det finns ingen garanti för prestanda. Azurite är avsett för utvecklings-och testnings ändamål.

### <a name="error-handling"></a>Felhantering

Azurite justeras med logik för Azure Storage fel hantering, men det finns skillnader. Fel meddelanden kan till exempel vara olika, medan fel status koderna justeras.

### <a name="ra-grs"></a>RA-GRS

Azurite stöder Geo-redundant replikering med Läs åtkomst (RA-GRS). För lagrings resurser går du till den sekundära platsen genom `-secondary` att lägga till i konto namnet. Följande adress kan till exempel användas för att få åtkomst till en blob med hjälp av den skrivskyddade sekundära Azurite:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

### <a name="table-support"></a>Tabell stöd

Stöd för tabeller i Azurite är för närvarande under utveckling och öppen till bidrag! För den senaste förloppet kontrollerar du [Azurite v3 Table](https://github.com/Azure/Azurite/wiki/Azurite-V3-Table) Project.

Stöd för varaktiga funktioner kräver tabeller.

## <a name="azurite-is-open-source"></a>Azurite är öppen källkod

Bidrag och förslag för Azurite är välkommen. Gå till Azurite [GitHub Project](https://github.com/Azure/Azurite/projects) Page eller [GitHub-problem](https://github.com/Azure/Azurite/issues) för mil stolpar och arbets objekt vi håller på att spåra för kommande funktioner och fel korrigeringar. Detaljerade arbets objekt spåras också i GitHub.

## <a name="next-steps"></a>Nästa steg

- [Använd Azure Storage emulatorn för utveckling och testning](storage-use-emulator.md) av dokument i den äldre Azure Storage emulatorn som ersätts av Azurite.
- [Konfigurera Azure Storage anslutnings strängar](storage-configure-connection-string.md) förklarar hur du sätter samman en giltig Azure Storage anslutnings sträng.
