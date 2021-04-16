---
title: Snabbstart – Azure IoT Hub för enhetsströmmar Node.js snabbstart för SSH och RDP
description: I den här snabbstarten kör du ett exempelprogram Node.js som fungerar som proxy för att aktivera SSH- och RDP-scenarier över IoT Hub enhetsströmmar.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: references_regions, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 16daee2855b8e493521c1b40468e7068dac9e759
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477952"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Snabbstart: Aktivera SSH och RDP via en IoT Hub med hjälp av ett Node.js -proxyprogram (förhandsversion)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

I den här snabbstarten aktiverar du SSH- och RDP-trafik (Secure Shell) och Remote Desktop Protocol (RDP) som ska skickas till enheten via en enhetsström. Azure IoT Hub enhetsströmmar gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. I den här snabbstarten beskrivs körningen Node.js ett proxyprogram som körs på tjänstsidan. Under den offentliga förhandsversionen Node.js SDK endast stöd för enhetsströmmar på tjänstsidan. Därför omfattar den här snabbstarten instruktioner för att endast köra det tjänst lokala proxyprogrammet.

## <a name="prerequisites"></a>Förutsättningar

* Slutförande av Aktivera SSH och RDP över IoT Hub enhetsströmmar med hjälp av ett [C-proxyprogram](./quickstart-device-streams-proxy-c.md) eller Aktivera SSH och RDP över IoT Hub enhetsströmmar med hjälp av ett [C#-proxyprogram](./quickstart-device-streams-proxy-csharp.md).

* Ett Azure-konto med en aktiv prenumeration. [Skapa en utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Node.js 10+](https://nodejs.org).

    Du kan verifiera den aktuella versionen Node.js på utvecklingsdatorn med hjälp av följande kommando:

    ```cmd/sh
    node --version
    ```

* [Ett exempel Node.js projektet](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Microsoft Azure IoT Hub stöder för närvarande enhetsströmmar som en [förhandsgranskningsfunktion.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!IMPORTANT]
> Förhandsversionen av enhetsströmmar stöds för närvarande endast för IoT Hubs som skapats i följande regioner:
>
> * Central US
> * USA, centrala (EUAP)
> * Norra Europa
> * Sydostasien

### <a name="add-azure-iot-extension"></a>Lägga till Azure IoT-tillägg

Lägg till Azure IoT-tillägget för Azure CLI till din Cloud Shell instans genom att köra följande kommando. IoT-tillägget lägger IoT Hub-, IoT Edge- och IoT Device Provisioning Service-specifika kommandon (DPS) till Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Om du har slutfört [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md) kan du hoppa över det här steget.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

Om du har [slutfört Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md)kan du hoppa över det här steget.

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I det här avsnittet använder du Azure Cloud Shell för att registrera en simulerad enhet.

1. Skapa enhetsidentiteten genom att köra följande kommando i Cloud Shell:

   > [!NOTE]
   > * Ersätt *platshållaren YourIoTHubName* med det namn som du valde för din IoT-hubb.
   > * För namnet på den enhet som du registrerar rekommenderar vi att du använder *MyDevice* enligt bilden. Om du väljer ett annat namn för din enhet använder du det namnet i hela den här artikeln och uppdaterar enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Om du vill att backend-programmet ska kunna ansluta till din IoT-hubb och hämta meddelandena behöver du även en *tjänstanslutningssträng*. Följande kommando hämtar strängen för din IoT-hubb:

   > [!NOTE]
   > Ersätt *platshållaren YourIoTHubName* med det namn som du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub connection-string show --policy-name service --hub-name {YourIoTHubName} --output table
    ```

   Observera den returnerade tjänstanslutningssträngen för senare användning i den här snabbstarten. Det ser ut som i följande exempel:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH till en enhet via enhetsströmmar

I det här avsnittet upprättar du en ström från början till slut för att tunnel-SSH-trafik.

### <a name="run-the-device-local-proxy-application"></a>Kör det enhetslokala proxyprogrammet

Som tidigare nämnts stöder IoT Hub Node.js SDK endast enhetsströmmar på tjänstsidan. För det enhets lokala programmet använder du ett enhetsproxyprogram som är tillgängligt i någon av följande snabbstarter:

   * [Aktivera SSH och RDP över IoT Hub enhetsströmmar med hjälp av ett C-proxyprogram](./quickstart-device-streams-proxy-c.md)
   * [Aktivera SSH och RDP via IoT Hub enhetsströmmar med hjälp av ett C#-proxyprogram](./quickstart-device-streams-proxy-csharp.md) 

Innan du fortsätter till nästa steg ska du kontrollera att det enhets lokala proxyprogrammet körs. En översikt över konfigurationen finns i [Exempel på lokal proxy.](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)

### <a name="run-the-service-local-proxy-application"></a>Köra tjänstlokalt proxyprogram

Den här artikeln beskriver konfigurationen för SSH (med port 22) och beskriver sedan hur du ändrar konfigurationen för RDP (som använder port 3389). Eftersom enhetsströmmar är program- och protokolloberoende kan du ändra samma exempel för att hantera andra typer av klient-server-programtrafik, vanligtvis genom att ändra kommunikationsporten.

När det enhets lokala proxyprogrammet körs kör du det tjänst-lokala proxyprogram som skrivits i Node.js genom att göra följande i ett lokalt terminalfönster:

1. För miljövariabler anger du dina autentiseringsuppgifter för tjänsten, målenhets-ID:t där SSH-daemonen körs och portnumret för den proxy som körs på enheten.

   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"
   export PROXY_PORT=2222

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   SET PROXY_PORT=2222
   ```

   Ändra platshållaren ServiceConnectionString så att den matchar din tjänstanslutningssträng och **MyDevice** så att den matchar ditt enhets-ID om du gav ditt ett annat namn.

1. Navigera till `Quickstarts/device-streams-service` katalogen i den uppackade projektmappen. Använd följande kod för att köra det tjänst-lokala proxyprogrammet:

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH till din enhet via enhetsströmmar

I Linux kör du SSH med hjälp av `ssh $USER@localhost -p 2222` på en terminal. I Windows använder du din favorit-SSH-klient (till exempel PuTTY).

Konsolens utdata på tjänst-lokal efter att SSH-sessionen har upprättats (det tjänst-lokala proxyprogrammet lyssnar på port 2222):

![SSH-terminalutdata](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Konsolens utdata från SSH-klientprogrammet (SSH-klienten kommunicerar med SSH-daemon genom att ansluta till port 22, där det tjänst lokala proxyprogrammet lyssnar):

![SSH-klientutdata](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>RDP till din enhet via enhetsströmmar

Använd RDP-klientprogrammet och anslut till tjänstproxyn på port 2222, en godtycklig port som du valde tidigare.

> [!NOTE]
> Kontrollera att din enhetsproxy är korrekt konfigurerad för RDP och konfigurerad med RDP-port 3389.

![RDP-klienten ansluter till det tjänst-lokala proxyprogrammet](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten konfigurerade du en IoT-hubb, registrerade en enhet och distribuerade ett tjänstproxyprogram för att aktivera RDP och SSH på en IoT-enhet. RDP- och SSH-trafiken kommer att skickas via en enhetsström via IoT-hubben. Den här processen eliminerar behovet av direkt anslutning till enheten.

Mer information om enhetsströmmar finns i:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
