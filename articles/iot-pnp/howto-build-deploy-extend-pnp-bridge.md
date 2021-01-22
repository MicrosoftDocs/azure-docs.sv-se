---
title: Så här skapar, distribuerar och utökar du IoT Plug and Play Bridge | Microsoft Docs
description: Identifiera IoT Plug and Play Bridge-komponenter. Lär dig hur du utökar bryggan och hur du kör den på IoT-enheter, gatewayar och som en IoT Edge modul.
author: usivagna
ms.author: ugans
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 43c89b0fac08bf9f2c72f885fbf4788371876b17
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678584"
---
# <a name="build-deploy-and-extend-the-iot-plug-and-play-bridge"></a>Skapa, distribuera och utöka IoT Plug and Play-bryggan

IoT Plug and Play Bridge låter dig ansluta de befintliga enheterna som är anslutna till en gateway till IoT Hub. Du kan använda Bridge för att mappa IoT Plug and Play-gränssnitt till anslutna enheter. Ett IoT Plug and Play-gränssnitt definierar den telemetri som en enhet skickar, de egenskaper som synkroniseras mellan enheten och molnet och de kommandon som enheten svarar på. Du kan installera och konfigurera brygga applikationen med öppen källkod på Windows-eller Linux-gatewayer.

Den här artikeln beskriver i detalj hur du:

- Konfigurera en brygga.
- Utöka en brygga genom att skapa nya kort.
- Så här skapar och kör du en bro i olika miljöer.

Ett enkelt exempel som visar hur du använder Bridge finns i [så här ansluter du IoT plug and Play Bridge-exemplet som körs på Linux eller Windows till IoT Hub](howto-use-iot-pnp-bridge.md).

Vägledningen och exemplen i den här artikeln förutsätter grundläggande välbekanthet med [Azure Digital](../digital-twins/overview.md) -och [IoT-plug and Play](overview-iot-plug-and-play.md).

## <a name="general-prerequisites"></a>Allmänna krav

### <a name="supported-os-platforms"></a>OS-plattformar som stöds

Följande OS-plattformar och versioner stöds:

|Plattform  |Versioner som stöds  |
|---------|---------|
|Windows 10 |     Alla Windows SKU: er stöds. Exempel: IoT Enterprise, Server, Desktop, IoT Core. *För övervakning av kamera hälsa rekommenderas 20H1 eller senare build. Alla andra funktioner är tillgängliga på alla Windows 10-versioner.*  |
|Linux     |Testat och stöds på Ubuntu 18,04, funktioner på andra distributioner har inte testats.         |
||

### <a name="hardware"></a>Maskinvara

- Alla maskin varu plattformar som stöder ovanstående OS-SKU: er och versioner.
- Serie-, USB-, Bluetooth-och kamera-kring utrustning och sensorer stöds internt. IoT Plug and Play Bridge kan utökas för att stödja anpassad kring utrustning eller sensor.

### <a name="azure-iot-products-and-tools"></a>Azure IoT-produkter och-verktyg

- **Azure-IoT Hub** – du behöver en [Azure IoT-hubb](../iot-hub/index.yml) i din Azure-prenumeration för att ansluta enheten till. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar. Om du inte har en IoT-hubb [följer du de här anvisningarna för att skapa en](../iot-hub/iot-hub-create-using-cli.md). IoT Plug and Play-support ingår inte i IoT-hubbar på Basic-nivå.
- **Azure IoT Explorer** – för att interagera med din IoT plug and Play-enhet kan du använda Azure IoT Explorer-verktyget. [Hämta och installera den senaste versionen av Azure IoT Explorer](./howto-use-iot-explorer.md) för ditt operativ system. Konfigurera Azure IoT Explorer-verktyget för att ansluta till din IoT-hubb och för att söka efter modell definitioner i mappen *pnpbridge\docs\schemas* i **IoT-plug-and-Play-Bridge** som du har klonat.

## <a name="configure-a-bridge"></a>Konfigurera en brygga

Det finns två sätt att konfigurera bryggan:

- Om bryggan körs internt på en IoT-enhet eller Gateway använder du konfigurations filen. Det här scenariot kan använda en Linux-eller Windows-dator.
- Om bryggan körs som en IoT Edge modul i IoT Edge runtime, använder du modulens dubbla önskade egenskap. Det här scenariot förutsätter att IoT Edge runtime finns i en Linux-miljö.

### <a name="configuration-file"></a>Konfigurationsfil

När Plug and Play Bridge körs internt på en IoT-enhet eller gateway, använder den en konfigurations fil för att:

- Hämta information om IoT Central-eller IoT Hub-anslutningen.
- Konfigurera enheter för vilka IoT Plug and Play-gränssnitt publiceras.

Använd något av följande alternativ för att ange konfigurations filen till bryggan:

- Skicka sökvägen till konfigurations filen som en kommando rads parameter till den körbara filen i Bridge.
- Använd den befintliga *config.jspå* filen i mappen *pnpbridge\cmake\ pnpbridge_x86 \src\pnpbridge\samples\console* .

[Konfigurations filens schema](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/pnpbridge/src/pnpbridge_config_schema.json) är tillgängligt i GitHub-lagringsplatsen.

> [!TIP]
> Om du redigerar en konfigurations fil för en brygga i VS Code kan du använda den här schema filen för att verifiera filen.

### <a name="iot-edge-module-configuration"></a>Konfiguration av IoT Edge-modul

När bryggan körs som en IoT Edge modul i en IoT Edge körning, skickas konfigurations filen från molnet som en uppdatering till `PnpBridgeConfig` önskad egenskap. Bryggan väntar tills den här egenskapen uppdateras innan nätverkskorten och komponenterna konfigureras.

## <a name="extend-the-bridge"></a>Utöka bryggan

Om du vill utöka brons funktioner kan du redigera dina egna brygga kort.

Bryggan använder kort för att:

- Upprätta en anslutning mellan en enhet och molnet.
- Aktivera data flödet mellan en enhet och molnet.
- Aktivera enhets hantering från molnet.

Varje bro kort måste:

- Skapa ett digitalt garn gränssnitt.
- Använd gränssnittet för att binda enhets sidans funktioner till molnbaserade funktioner som telemetri, egenskaper och kommandon.
- Upprätta kontroll-och data kommunikation med enhets maskin varan eller den inbyggda program varan.

Varje bro kort samverkar med en speciell typ av enhet baserat på hur adaptern ansluter till och interagerar med enheten. Även om kommunikationen med en enhet använder ett hand skaknings protokoll kan ett bro kort ha flera sätt att tolka data från enheten. I det här scenariot använder Bridge-kortet information om kortet i konfigurations filen för att avgöra vilken *gränssnitts konfiguration* som kortet ska använda för att parsa data.

För att interagera med enheten använder ett bro kort ett kommunikations protokoll som stöds av den enhet och de API: er som tillhandahålls av det underliggande operativ systemet, eller enhets leverantören.

För att interagera med molnet används API: er som tillhandahålls av Azure IoT Device C SDK för att skicka telemetri, skapa digitala dubbla gränssnitt, skicka egenskaps uppdateringar och skapa callback-funktioner för egenskaps uppdateringar och kommandon.

### <a name="create-a-bridge-adapter"></a>Skapa ett bro kort

Bryggan förväntar sig ett bro kort som implementerar de API: er som definierats i [_PNP_ADAPTER](https://github.com/Azure/iot-plug-and-play-bridge/blob/9964f7f9f77ecbf4db3b60960b69af57fd83a871/pnpbridge/src/pnpbridge/inc/pnpadapter_api.h#L296) gränssnittet:

```c
typedef struct _PNP_ADAPTER {
  // Identity of the IoT Plug and Play adapter that is retrieved from the config
  const char* identity;

  PNPBRIDGE_ADAPTER_CREATE createAdapter;
  PNPBRIDGE_COMPONENT_CREATE createPnpComponent;
  PNPBRIDGE_COMPONENT_START startPnpComponent;
  PNPBRIDGE_COMPONENT_STOP stopPnpComponent;
  PNPBRIDGE_COMPONENT_DESTROY destroyPnpComponent;
  PNPBRIDGE_ADAPTER_DESTOY destroyAdapter;
} PNP_ADAPTER, * PPNP_ADAPTER;
```

I det här gränssnittet:

- `PNPBRIDGE_ADAPTER_CREATE` skapar kortet och konfigurerar gränssnitts hanterings resurserna. Ett kort kan också vara beroende av parametrar för globala kort för skapande av kort. Den här funktionen anropas en gång för ett enda nätverkskort.
- `PNPBRIDGE_COMPONENT_CREATE` skapar de digitala dubbla klient gränssnitten och binder återanrops funktionerna. Kortet initierar kommunikations kanalen till enheten. Kortet kan konfigurera resurserna så att telemetri-flödet aktive ras, men det går inte att starta rapportering av telemetri förrän `PNPBRIDGE_COMPONENT_START` anropas. Den här funktionen anropas en gång för varje gränssnitts komponent i konfigurations filen.
- `PNPBRIDGE_COMPONENT_START` kallas att låta bryggan börja vidarebefordra telemetri från enheten till den digitala dubbla klienten. Den här funktionen anropas en gång för varje gränssnitts komponent i konfigurations filen.
- `PNPBRIDGE_COMPONENT_STOP` stoppar telemetri-flödet.
- `PNPBRIDGE_COMPONENT_DESTROY` förstör den digitala dubbla klienten och de associerade gränssnitts resurserna. Den här funktionen anropas en gång för varje gränssnitts komponent i konfigurations filen när bryggan är felaktig eller när ett allvarligt fel uppstår.
- `PNPBRIDGE_ADAPTER_DESTROY` rensar resurserna för bro nätverkskort.

### <a name="bridge-core-interaction-with-bridge-adapters"></a>Brygga Core-interaktion med Bridge-kort

I följande lista beskrivs vad som händer när Bridge startar:

1. När bryggan startar söker Bridge hanteraren igenom varje gränssnitts komponent som definierats i konfigurations filen och anropar `PNPBRIDGE_ADAPTER_CREATE` på lämpligt kort. Kortet kan använda globala kort konfigurations parametrar för att konfigurera resurser för att stödja de olika *gränssnitts konfigurationerna*.
1. För varje enhet i konfigurations filen initierar Bridge Manager att gränssnittet skapas genom att anropa `PNPBRIDGE_COMPONENT_CREATE` i lämpligt bro kort.
1. Kortet tar emot valfria kort konfigurations inställningar för gränssnitts komponenten och använder den här informationen för att konfigurera anslutningar till enheten.
1. Kortet skapar de digitala dubbla klient gränssnitten och binder återanrops funktionerna för egenskaps uppdateringar och-kommandon. Om du skapar enhets anslutningar blockeras inte återanropen när digitala dubbla gränssnitt har skapats. Den aktiva enhets anslutningen är oberoende av den aktiva gränssnitts klient som bryggan skapar. Om anslutningen Miss lyckas förutsätter kortet att enheten är inaktiv. Bridge-kortet kan välja att göra om anslutningen.
1. När bryggan för korts hanteraren skapar alla gränssnitts komponenter som anges i konfigurations filen, registreras alla gränssnitt med Azure IoT Hub. Registreringen är ett block som är ett asynkront anrop. När anropet har slutförts utlöser den ett återanrop i Bridge-kortet som sedan kan börja hantera egenskaper och kommando återanrop från molnet.
1. Bridge adapter Manager anropar sedan `PNPBRIDGE_INTERFACE_START` varje komponent och Bridge-kortet börjar rapportera telemetri till den digitala dubbla klienten.

### <a name="design-guidelines"></a>Designriktlinjer

Följ dessa rikt linjer när du utvecklar ett nytt bro kort:

- Bestäm vilka enhets funktioner som stöds och vilka gränssnitts definitioner för komponenterna som använder det här kortet.
- Ta reda på vilka gränssnitt och globala parametrar ditt nätverkskort behöver ha definierat i konfigurations filen.
- Identifiera den lågnivå enhets kommunikation som krävs för att stödja komponent egenskaper och-kommandon.
- Ta reda på hur kortet ska parsa rå data från enheten och konvertera dem till de typer av telemetri som IoT Plug and Play-gränssnittets definition anger.
- Implementera det brygga nätverkskorts gränssnitt som beskrivs ovan.
- Lägg till det nya kortet till kort manifestet och skapa bryggan.

### <a name="enable-a-new-bridge-adapter"></a>Aktivera ett nytt bro kort

Du aktiverar kort i bryggan genom att lägga till en referens i [adapter_manifest. c](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/shared/adapter_manifest.c):

```c
  extern PNP_ADAPTER MyPnpAdapter;
  PPNP_ADAPTER PNP_ADAPTER_MANIFEST[] = {
    .
    .
    &MyPnpAdapter
  }
```

> [!IMPORTANT]
> Återanrop i Bridge-kort anropas sekventiellt. Ett nätverkskort ska inte blockera ett återanrop eftersom det förhindrar att bryggan skapas.

### <a name="sample-camera-adapter"></a>Exempel på kamera kort

I [README för kamera kortet](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/Camera/readme.md) beskrivs ett exempel på ett kamera kort som du kan aktivera.

## <a name="build-and-run-the-bridge-on-an-iot-device-or-gateway"></a>Skapa och köra bryggan på en IoT-enhet eller gateway

| Plattform | Stöds |
| :-----------: | :-----------: |
| Windows |  Yes |
| Linux | Yes |

### <a name="prerequisites"></a>Förutsättningar

För att slutföra det här avsnittet måste du installera följande program vara på den lokala datorn:

- En utvecklings miljö som stöder kompilering av C++, till exempel [Visual Studio (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/) – se till att du inkluderar NuGet Package Manager-komponenten och **Skriv bords utveckling med C++** -arbetsbelastning när du installerar Visual Studio.
- [Cmake](https://cmake.org/download/) – när du installerar cmake väljer du alternativet **Lägg till CMAKE i System Sök vägen**.
- Om du bygger på Windows måste du också hämta [windows 17763 SDK](https://developer.microsoft.com/windows/downloads/windows-10-sdk).

### <a name="source-code"></a>Källkod

Klona [IoT plug and Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) -lagringsplatsen till den lokala datorn:

```console
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

Vi tror att föregående kommando tar flera minuter att köra.

> [!NOTE]
> Om du stöter på problem med att logga in i `git submodule update` Windows kan du försöka lösa problemet genom att köra följande kommando: `git config --system core.longpaths true` .

### <a name="build-the-bridge-on-windows"></a>Skapa en brygga på Windows

Öppna **kommando tolken för utvecklare för VS 2019** och navigera till den mapp som innehåller den lagrings plats du har klonat och kör följande kommandon:

```console
cd pnpbridge\scripts\windows

build.cmd
```

Vi tror att föregående kommando tar flera minuter att köra.

Du kan också öppna den genererade *pnpbridge\cmake\ pnpbridge_x86 \ azure_iot_pnp_bridge. SLN* -lösnings filen i Visual Studio för att redigera, återskapa och felsöka koden.

> [!TIP]
> I det här projektet används CMAKE för att generera projektfiler. Eventuella ändringar som du gör i projektet i Visual Studio kan gå förlorade om rätt CMAKE-filer inte uppdateras.

### <a name="build-the-bridge-on-linux"></a>Skapa en brygga på Linux

Använd bash-gränssnittet och navigera till den mapp som innehåller den lagrings plats du har klonat och kör följande kommandon:

```bash
cd scripts/linux

./setup.sh

./build.sh
```

### <a name="configure-the-generic-sensors"></a>Konfigurera allmänna sensorer

Ändra följande parametrar under `pnp_bridge_connection_parameters` noden i *config.jspå* filen i mappen *IoT-plug-and-Play-bridge\pnpbridge\cmake\ pnpbridge_x86 \src\pnpbridge\samples\console* i Windows eller i mappen *IoT-plug-and-Play-bridge/pnpbridge/cmake/pnpbridge_linux \ src/pnpbridge/samples/Console* i Windows:

Om du använder en anslutnings sträng för att ansluta till din IoT-hubb:

```JSON
{
  "connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "root_interface_model_id": "[To fill in]",
    "auth_parameters": {
      "auth_type": "symmetric_key",
      "symmetric_key": "[To fill in]"
    }
  }
}
```

> [!TIP]
> `symmetric_key`Värdet måste matcha SAS-nyckeln i anslutnings strängen.

Om du använder DPS för att ansluta till din IoT Hub-eller IoT Central-program:

```JSON
{
  "connection_parameters": {
    "connection_type" : "dps",
    "root_interface_model_id": "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "auth_parameters" : {
      "auth_type" : "symmetric_key",
      "symmetric_key" : "[DEVICE KEY]"
    },
    "dps_parameters" : {
      "global_prov_uri" : "[GLOBAL PROVISIONING URI] - typically it is global.azure-devices-provisioning.net",
      "id_scope": "[IoT Central / IoT Hub ID SCOPE]",
      "device_id": "[DEVICE ID]"
    }
  }
}
```

Granska resten av konfigurations filen för att se vilka gränssnitts komponenter och globala parametrar som konfigureras i det här exemplet.

### <a name="start-the-bridge-in-windows"></a>Starta Bridge i Windows

Starta Bridge genom att köra den i kommando tolken:

```console
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

Debug\pnpbridge_bin.exe
```

> [!TIP]
> Om du vill använda en konfigurations fil från en annan plats skickar du sökvägen som en kommando rads parameter när du kör bryggan.
  
> [!TIP]
> Om du antingen har en inbyggd kamera eller USB-kamera som är ansluten till din dator kan du starta ett program som använder Camera, till exempel den inbyggda appen **kamera** . När **kamera** appen körs visar brygga konsolen utdata övervaknings statistik och bild Rute frekvensen som rapporteras via det digitala dubbla gränssnittet till din IoT-hubb.

## <a name="build-and-run-the-bridge-as-an-iot-edge-module"></a>Skapa och kör bryggan som en IoT Edge modul

| Plattform | Stöds |
| :-----------: | :-----------: |
| Windows |  Nej |
| Linux | Yes |

### <a name="prerequisites"></a>Förutsättningar

För att slutföra det här avsnittet behöver du en kostnads fri Azure IoT-hubb på nivån standard eller standard. Information om hur du skapar en IoT Hub finns i [skapa en IoT-hubb](../iot-hub/iot-hub-create-through-portal.md).

Stegen i det här avsnittet förutsätter att du har följande utvecklings miljö på en Windows 10-dator. Med dessa verktyg kan du bygga och distribuera en IoT Edge-modul till din IoT Edge-enhet:

- Windows-undersystem för Linux (WSL) 2 som kör Ubuntu 18,04 LTS. Mer information finns i [installations guide för Windows-undersystemet för Linux för Windows 10](https://docs.microsoft.com/windows/wsl/install-win10).
- Docker Desktop för Windows konfigurerat för att använda WSL 2. Mer information finns i [Docker Desktop Wsl 2 Server](https://docs.docker.com/docker-for-windows/wsl/)del.
- [Visual Studio Code som är installerad i Windows-miljön](https://code.visualstudio.com/docs/setup/windows) med följande tre tillägg installerade:

  - [Tilläggs paket för fjärrutveckling](https://aka.ms/vscode-remote/download/extension) – med det här tillägget kan du skapa och köra kod i Wsl 2.
  - [Docker för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) – det här tillägget måste vara aktiverat i vs Codes **Wsl: Ubuntu-18,04-** miljö.
  - [Azure IoT-verktyg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) – det här tillägget måste vara aktiverat i vs Codes **Wsl: Ubuntu-18,04-** miljö.

- Kör följande kommandon i din WSL 2-miljö för att installera de nödvändiga build-verktygen:

  ```bash
  sudo apt-get update
  sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
  ```

- [Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest&preserve-view=true) installerat i din Wsl 2-miljö för att hantera dina Azure-resurser.

  > [!TIP]
  > Om du vill kan du köra `az` kommandona i [Azure Cloud Shell](https://shell.azure.com/) där CLI är förinstallerat.

### <a name="create-an-iot-edge-device"></a>Skapa en IoT Edge-enhet

Kommandona skapar en IoT Edge enhet som körs på en virtuell Azure-dator. Att köra en IoT Edge-enhet på en virtuell dator är användbart för att testa distributionen om du inte har åtkomst till en riktig enhet.

Om du vill skapa en IoT Edge enhets registrering i din IoT-hubb kör du följande kommandon i din WSL 2-miljö. Använd `az login` kommandot för att logga in på din Azure-prenumeration:

```azurecli
az iot hub device-identity create --device-id bridge-edge-device --edge-enabled true --hub-name {your IoT hub name}
```

Kör följande kommandon för att skapa en virtuell Azure-dator med IoT Edge runtime installerad. Uppdatera plats hållarna med lämpliga värden:

```azurecli
az group create --name bridge-edge-resources --location eastus
az deployment group create \
--resource-group bridge-edge-resources \
--template-uri "https://aka.ms/iotedge-vm-deploy" \
--parameters dnsLabelPrefix='{unique DNS name for virtual machine}' \
--parameters adminUsername='{admin user name}' \
--parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name} -o tsv) \
--parameters authenticationType='password' \
--parameters adminPasswordOrKey="{admin password for virtual machine}"
```

Nu har du IoT Edge runtime som körs på en virtuell dator. Du kan använda följande kommando för att kontrol lera att **$edgeAgent** och **$edgeHub** körs på enheten:

```azurecli
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

> [!CAUTION]
> Du faktureras när den här virtuella datorn körs. Glöm inte att stänga av den när du inte använder den och ta bort den när du är klar.

### <a name="download-the-source-code"></a>Ladda ned käll koden

I följande steg skapar du Docker-avbildningen i din WSL 2-miljö. Om du vill klona **IoT-plug-and-Play-Bridge-** lagringsplatsen kör du följande kommandon i ett Wsl 2 bash-gränssnitt i en lämplig mapp:

```bash
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

Vi tror att föregående kommando tar flera minuter att köra.

### <a name="update-the-dockerfile"></a>Uppdatera *Dockerfile*

Starta VS Code, öppna kommando-paletten, ange *fjärran Wsl: öppna mappen i Wsl* och öppna sedan mappen *IoT-plug-and-Play-Bridge* som innehåller den klonade lagrings platsen.

Öppna filen *pnpbridge\Dockerfile.amd64* . Redigera miljö variabel definitionerna enligt följande:

```dockerfile
ENV IOTHUB_DEVICE_CONNECTION_STRING="{Add your device connection string here}"
ENV PNP_BRIDGE_ROOT_MODEL_ID="dtmi:com:example:RootPnpBridgeSampleDevice;1"
ENV PNP_BRIDGE_HUB_TRACING_ENABLED="false"
ENV IOTEDGE_WORKLOADURI="something"
```

> [!TIP]
> Du kan använda följande kommando för att hämta anslutnings strängen för enheten: `az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name}`

Det finns exempel på *Dockerfiles* för andra arkitekturer.

### <a name="build-the-iot-plug-and-play-bridge-module-image"></a>Skapa IoT Plug and Play Bridge-modulen avbildning

I VS Code öppnar du kommando-paletten, anger *Docker-register: Logga in på Docker CLI* och välj sedan din Azure-prenumeration och behållar registret. Det här kommandot gör att Docker kan ansluta till behållar registret och ladda upp modul avbildningen.

Öppna filen *pnpbridge/module.js* . Lägg till `{your container registry name}.azurecr.io/iotpnpbridge` som behållar avbildnings lagring och `v1` som version.

I VS Code högerklickar du på filen *pnpbridge/module.jspå* filen **i trädvyn,** väljer **Bygg och push IoT Edge modul avbildning** och väljer sedan **amd64** som plattform.

I VS Code, i **Docker** -vyn, kan du bläddra i innehållet i behållar registret som nu innehåller avbildningen **iotpnpbridge: v1-amd64-** modul.

### <a name="create-a-container-registry"></a>Skapa ett containerregister

En IoT Edge enhet laddar ned sina modulblad från ett behållar register. I det här exemplet används ett Azure Container Registry.

Skapa ett Azure Container Registry i resurs gruppen **resurser-Edge-Resources** . Aktivera sedan administratörs åtkomst till ditt behållar register och hämta de autentiseringsuppgifter som din IoT Edge enhet behöver för att ladda ned modulerna:

```azurecli
az acr create -g bridge-edge-resources --sku Basic -n {your container registry name}
az acr update --admin-enabled true -n {your container registry name}
az acr credential show -n {your container registry name}
```

### <a name="create-the-deployment-manifest"></a>Skapa distributions manifestet

Ett IoT Edge distributions manifest anger moduler och konfigurations värden som ska distribueras till en IoT Edge enhet.

I VS Code öppnar du filen *pnpbridge/deployment.template.jspå* filen:

- Uppdatera `registryCredentials` med värdena från föregående kommando. `address`Värdet ser ut så här `{your container registry name}.azurecr.io` .
- Uppdatera `image` värdet för `ModulePnpBridge` . Modul bilden ser ut så här: `{your container registry}.azurecr.io/iotpnpbridge:v1-amd64` .
- Ersätt `PnPBridgeConfig` med följande JSON för att konfigurera koldioxid identifierings kortet:

  ```json
  "PnpBridgeConfig": {
    "pnp_bridge_interface_components": [
      {
        "_comment": "Component 1 - Modbus Device",
        "pnp_bridge_component_name": "Co2Detector1",
        "pnp_bridge_adapter_id": "modbus-pnp-interface",
        "pnp_bridge_adapter_config": {
          "unit_id": 1,
          "tcp": {
            "host": "10.159.29.2",
            "port": 502
          },
          "modbus_identity": "DL679"
        }
      }
    ],
    "pnp_bridge_adapter_global_configs": {
      "modbus-pnp-interface": {
        "DL679": {
          "telemetry": {
            "co2": {
              "startAddress": "40001",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 5000,
              "conversionCoefficient": 1
            },
            "temperature": {
              "startAddress": "40003",
              "length": 1,
              "dataType": "decimal",
              "defaultFrequency": 5000,
              "conversionCoefficient": 0.01
            }
          },
          "properties": {
            "firmwareVersion": {
              "startAddress": "40482",
              "length": 1,
              "dataType": "hexstring",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "modelName": {
              "startAddress": "40483",
              "length": 2,
              "dataType": "string",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmStatus_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "boolean",
              "defaultFrequency": 1000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmThreshold_co2": {
              "startAddress": "40225",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 30000,
              "conversionCoefficient": 1,
              "access": 2
            }
          },
          "commands": {
            "clearAlarm_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "flag",
              "conversionCoefficient": 1
            }
          }
        }
      }
    }
  }
  ```

  Spara ändringarna.

I VS Code högerklickar du på filen *pnpbridge/deployment.template.jspå* filen **i trädvyn och** väljer **skapa IoT Edge distributions manifest**. Det här kommandot genererar *pnpbridge/config/deployment.amd64.jspå* distributions manifestet.

### <a name="deploy-the-bridge-to-your-iot-edge-device"></a>Distribuera bryggan till din IoT Edge-enhet

I VS Code öppnar du kommando-paletten, anger *Azure IoT Hub: välj IoT Hub* och välj sedan din prenumeration och IoT Hub.

I VS Code högerklickar du på filen *pnpbridge/config/deployment.amd64.jspå* filen **i trädvyn,** väljer **skapa distribution för en enskild enhet** och väljer den **brygga-Edge-enhet**.

Kör följande kommando för att visa status för modulerna på enheten:

```azurecli
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

I listan över moduler som körs finns nu **ModulePnpBridge** -modulen som är konfigurerad för att använda koldioxid identifierings kortet.

### <a name="tidy-up"></a>Städa upp

Om du vill ta bort den virtuella datorn och behållar registret från din Azure-prenumeration kör du följande kommando:

```azurecli
az group delete -n bridge-edge-resources
```

## <a name="folder-structure"></a>Mappstruktur

*pnpbridge\deps\azure-IoT-SDK-c-PnP*: git-undermoduler som innehåller Azure IoT-enhetens SDK för c SDK.

*pnpbridge\scripts*: Bygg skript.

*pnpbridge\src*: käll kod för IoT plug and Play Bridge Core.

*pnpbridge\src\adapters*: käll kod för olika IoT plug and Play Bridge-kort.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om IoT Plug and Play-bryggan går du till [iot plug and Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) GitHub-lagringsplatsen.
