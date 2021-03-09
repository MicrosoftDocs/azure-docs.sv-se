---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: c939c1d061f4e01a00bc2b886056e42f0b059ad5
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510931"
---
## <a name="download-and-install"></a>Hämta och installera

#### <a name="windows-install"></a>[Windows-installation](#tab/windowsinstall)

Följ de här stegen för att installera tal-CLI i Windows:

1. I Windows behöver du [Microsoft Visual C++ Redistributable för Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) för din plattform. Det kan krävas en omstart för att installera det första gången.
1. Installera [.net Core 3,1 SDK](/dotnet/core/install/windows).
2. Installera tal-CLI med NuGet genom att ange följande kommando:

   ```console
   dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI --version 1.15.0
   ```
Skriv `spx` för att se hjälp för tal-cli.

> [!NOTE]
> Som ett alternativ till NuGet kan du ladda ned och extrahera [zip-arkivet](https://aka.ms/speech/spx-zips.zip)för tal-CLI, hitta och extrahera din plattform från `spx-zips` katalogen och lägga till `spx` sökvägen till din system **Sök vägs** variabel.


### <a name="font-limitations"></a>Tecken begränsningar

I Windows kan tal-CLI bara visa teckensnitt som är tillgängliga för kommando tolken på den lokala datorn.
[Windows Terminal](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) stöder alla teckensnitt som genereras interaktivt av tal-cli.

Om du skriver ut till en fil kan en text redigerare som anteckningar eller en webbläsare som Microsoft Edge även Visa alla teckensnitt.

#### <a name="linux-install"></a>[Linux-installation](#tab/linuxinstall)

Följande Linux-distributioner stöds för x64-arkitekturer med hjälp av tal CLI:

* CentOS 7/8
* Debian 9/10 
* Red Hat Enterprise Linux (RHEL) 7/8
* Ubuntu 16.04/18.04/20.04

> [!NOTE]
> Fler arkitekturer stöds av talet SDK (inte tal-CLI). Mer information finns i [om tal-SDK](../speech-sdk.md).

Följ de här stegen för att installera Speech CLI på Linux på en x64-processor:

1. Installera [.net Core 3,1 SDK](/dotnet/core/install/linux).
2. Installera tal-CLI med NuGet genom att ange följande kommando:

    `dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI --version 1.15.0`

Skriv `spx` för att se hjälp för tal-cli.

> [!NOTE]
> Som ett alternativ till NuGet kan du hämta binärfilerna i zip- [arkivet](https://aka.ms/speech/spx-zips.zip), extrahera `spx-netcore-30-linux-x64.zip` till en ny `~/spx` katalog, skriva `sudo chmod +r+x spx` på den binära och lägga till `~/spx` sökvägen till din PATH-systemvariabel.


#### <a name="docker-install-windows-linux-macos"></a>[Docker-installation (Windows, Linux, macOS)](#tab/dockerinstall)

Följ de här stegen för att installera tal-CLI i en Docker-behållare:

1. <a href="https://www.docker.com/get-started" target="_blank">Installera Docker Desktop</a> för din plattform om den inte redan är installerad.
2. Skriv följande kommando i en ny kommando tolk eller Terminal:
   ```console   
   docker pull msftspeech/spx
   ```
3. Skriv det här kommandot. Du bör se hjälp information för tal-CLI:
   ```console 
   docker run -it --rm msftspeech/spx help
   ```

### <a name="mount-a-directory-in-the-container"></a>Montera en katalog i behållaren

Tal CLI-verktyget sparar konfigurations inställningar som filer och läser in dessa filer när du utför något av kommandona (förutom hjälp kommandon).
När du använder tal-CLI i en Docker-behållare måste du montera en lokal katalog från behållaren, så att verktyget kan lagra eller hitta konfigurations inställningarna och även så att verktyget kan läsa eller skriva filer som krävs av kommandot, till exempel ljudfiler av tal.

I Windows skriver du det här kommandot för att skapa en lokal katalog tal CLI kan använda från behållaren:

`mkdir c:\spx-data`

Eller på Linux eller macOS skriver du det här kommandot i en Terminal för att skapa en katalog och se dess absoluta sökväg:

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

Du kommer att använda den absoluta sökvägen när du anropar tal-CLI.

### <a name="run-speech-cli-in-the-container"></a>Kör tal-CLI i behållaren

Den här dokumentationen visar det tal CLI- `spx` kommando som används i installationer som inte är Docker.
När du anropar `spx` kommandot i en Docker-behållare måste du montera en katalog i behållaren till ditt fil system där tal-CLI kan lagra och hitta konfigurations värden och läsa och skriva filer.

I Windows kommer dina kommandon att starta så här:

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx
```

På Linux eller macOS ser dina kommandon ut som exemplet nedan. Ersätt `ABSOLUTE_PATH` med den absoluta sökvägen för den monterade katalogen. Den här sökvägen returnerades av `pwd` kommandot i föregående avsnitt. 

Om du kör det här kommandot innan du ställer in din nyckel och region får du ett fel meddelande som anger att du ställer in din nyckel och region:
```console   
sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
```

Om du vill använda `spx` kommandot som är installerat i en behållare, anger du alltid det fullständiga kommandot som visas ovan, följt av parametrarna för din begäran.
I Windows anger det här kommandot till exempel din nyckel:

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx config --set @key SUBSCRIPTION-KEY
```

För mer utökad interaktion med kommando rads verktyget kan du starta en behållare med ett interaktivt bash-gränssnitt genom att lägga till en EntryPoint-parameter.
I Windows anger du det här kommandot för att starta en behållare som visar ett interaktivt kommando rads gränssnitt där du kan ange flera `spx` kommandon:
```console
docker run -it --entrypoint=/bin/bash -v c:\spx-data:/data --rm msftspeech/spx
```

> [!WARNING]
> Du kan inte använda datorns mikrofon när du kör tal-CLI i en Docker-behållare. Du kan dock läsa från och spara ljudfiler i din lokala monterade katalog. 

<!-- Need to troubleshoot issues with docker pull image

### Optional: Create a command line shortcut

If you're running the the Speech CLI from a Docker container on Linux or macOS you can create a shortcut. 

Follow these instructions to create a shortcut:
1. Open `.bash_profile` with your favorite text editor. For example:
   ```shell
   nano ~/.bash_profile
   ```
2. Next, add this function to your `.bash_profile`. Make sure you update this function with the correct path to your mounted directory:
   ```shell   
   spx(){
       sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
   }
   ```
3. Source your profile:
   ```shell
   source ~/.bash_profile
   ```
4. Now instead of running `sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx`, you can just type `spx` followed by arguments. For example: 
   ```shell
   // Get some help
   spx help recognize

   // Recognize speech from an audio file 
   spx recognize --file /mounted/directory/file.wav
   ```

> [!WARNING]
> If you change the mounted directory that Docker is referencing, you need to update the function in `.bash_profile`.
--->
***

## <a name="create-subscription-config"></a>Skapa prenumerations konfiguration

Om du vill börja använda tal-CLI måste du ange din röst prenumerations nyckel och regions-ID. Hämta dessa autentiseringsuppgifter genom att följa stegen i [testa tal tjänsten kostnads fritt](../overview.md#try-the-speech-service-for-free).
När du har en prenumerations nyckel och region-ID (t. ex. `eastus`, `westus` ), kör följande kommandon.

```console
spx config --set @key SUBSCRIPTION-KEY
spx config --set @region REGION
```

Din prenumerations-autentisering lagras nu för framtida SPX-begäranden. Om du behöver ta bort något av dessa lagrade värden kör `spx config @region --clear` eller `spx config @key --clear` .
