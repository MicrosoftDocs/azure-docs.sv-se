---
title: Snabb start – Azure IoT Hub enhets strömmar Node.js snabb start för SSH och RDP
description: I den här snabb starten kör du ett exempel Node.js program som fungerar som en proxy för att aktivera SSH-och RDP-scenarier över IoT Hub enhets strömmar.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: references_regions
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: ef45a6277adeff09a34fe22b7abeb21d3e603167
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624362"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Snabb start: Aktivera SSH och RDP över en IoT Hub enhets ström med hjälp av ett Node.js proxy-program (för hands version)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

I den här snabb starten aktiverar du en säker gränssnitts trafik (SSH) och Remote Desktop Protocol (RDP) som ska skickas till enheten via en enhets ström. Azure IoT Hub enhets strömmar gör att tjänst-och enhets program kan kommunicera på ett säkert och användarvänligt sätt. Den här snabb starten beskriver körningen av ett Node.js proxy-program som körs på tjänst sidan. Under den offentliga för hands versionen stöder Node.js SDK endast enhets strömmar på tjänst sidan. Därför täcker den här snabb starten instruktioner för att bara köra det tjänst lokala proxy-programmet.

## <a name="prerequisites"></a>Förutsättningar

* Slut för ande av [aktiverar SSH och RDP över IoT Hub enhets strömmar genom att använda ett C-proxyprogram](./quickstart-device-streams-proxy-c.md) eller [Aktivera SSH och RDP över IoT Hub enhets strömmar med hjälp av ett C#-proxyprogram](./quickstart-device-streams-proxy-csharp.md).

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node.js 10 +](https://nodejs.org).

    Du kan kontrol lera den aktuella versionen av Node.js på din utvecklings dator med hjälp av följande kommando:

    ```cmd/sh
    node --version
    ```

* [Ett exempel på ett Node.js projekt](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Microsoft Azure IoT Hub stöder för närvarande enhets strömmar som en [förhands gransknings funktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!IMPORTANT]
> För hands versionen av enhets strömmar stöds för närvarande bara för IoT-hubbar som skapats i följande regioner:
>
> * Central US
> * Centrala USA-EUAP
> * Norra Europa
> * Sydostasien

### <a name="add-azure-iot-extension"></a>Lägg till Azure IoT-tillägg

Lägg till Azure IoT-tillägget för Azure CLI till din Cloud Shell instans genom att köra följande kommando. IoT-tillägget lägger till IoT Hub, IoT Edge och IoT-kommandon (Device Provisioning service) i Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Om du har slutfört [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md) kan du hoppa över det här steget.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

Om du har slutfört [snabb starten: skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md)kan du hoppa över det här steget.

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I det här avsnittet använder du Azure Cloud Shell för att registrera en simulerad enhet.

1. Skapa enhets identiteten genom att köra följande kommando i Cloud Shell:

   > [!NOTE]
   > * Ersätt plats hållaren *YourIoTHubName* med det namn du valt för din IoT Hub.
   > * För namnet på enheten som du registrerar rekommenderar vi att du använder min *enhet* som visas. Om du väljer ett annat namn på enheten använder du det namnet i den här artikeln och uppdaterar enhets namnet i exempel programmen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Om du vill aktivera backend-programmet för att ansluta till din IoT-hubb och hämta meddelandena måste du också ha en *anslutnings sträng för tjänsten*. Följande kommando hämtar strängen för din IoT-hubb:

   > [!NOTE]
   > Ersätt plats hållaren *YourIoTHubName* med det namn du valt för din IoT Hub.

    ```azurecli-interactive
    az iot hub connection-string show --policy-name service --hub-name {YourIoTHubName} --output table
    ```

   Observera den returnerade tjänst anslutnings strängen för senare användning i den här snabb starten. Det ser ut som i följande exempel:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH till en enhet via enhetsströmmar

I det här avsnittet upprättar du en slutpunkt-till-slutpunkt-ström för att dirigera SSH-trafik.

### <a name="run-the-device-local-proxy-application"></a>Kör det enhetslokala proxyprogrammet

Som tidigare nämnts stöder IoT Hub Node.js SDK endast enhets strömmar på tjänst sidan. Använd ett program för enhets proxy som är tillgängligt i någon av följande snabb starter för det enhets lokala programmet:

   * [Aktivera SSH och RDP över IoT Hub enhets strömmar med hjälp av ett C proxy-program](./quickstart-device-streams-proxy-c.md)
   * [Aktivera SSH och RDP över IoT Hub enhets strömmar med hjälp av ett C#-proxyprogram](./quickstart-device-streams-proxy-csharp.md) 

Innan du fortsätter till nästa steg måste du kontrol lera att programmet enhetens lokala proxy körs. En översikt över installationen finns i exempel på [lokal Proxy](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

### <a name="run-the-service-local-proxy-application"></a>Köra tjänstlokalt proxyprogram

I den här artikeln beskrivs inställningarna för SSH (med port 22) och hur du ändrar inställningarna för RDP (som använder port 3389). Eftersom enhets strömmar är program-och protokoll-oberoende kan du ändra samma exempel för att hantera andra typer av program trafik på klient servern, vanligt vis genom att ändra kommunikations porten.

Med det enhets lokala proxy-programmet igång kör du det tjänst lokala proxy-programmet som är skrivet i Node.js genom att göra följande i ett lokalt terminalfönster:

1. För miljövariabler anger du autentiseringsuppgifterna för tjänsten, mål enhets-ID: t där SSH-daemonen körs och port numret för den proxy som körs på enheten.

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

   Ändra plats hållaren ServiceConnectionString så att den matchar din tjänst anslutnings sträng och att **enheten** matchar ditt enhets-ID om du har gett ditt eget namn.

1. Navigera till `Quickstarts/device-streams-service` katalogen i den zippade projektmappen. Använd följande kod för att köra det service-lokala proxy-programmet:

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH till din enhet via enhetsströmmar

I Linux kör du SSH genom att använda `ssh $USER@localhost -p 2222` på en Terminal. I Windows använder du din favorit-SSH-klient (till exempel, SparaTillFil).

Konsol utdata på den lokala tjänsten efter att SSH-sessionen har upprättats (det service lokala proxy-programmet lyssnar på port 2222):

![Utdata från SSH-Terminal](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Konsolens utdata från SSH-klientprogrammet (SSH-klienten kommunicerar med SSH-daemon genom att ansluta till port 22, där tjänstens lokala proxy-program lyssnar):

![Utdata för SSH-klient](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>RDP till din enhet via enhetsströmmar

Använd nu RDP-klientprogrammet och Anslut till Tjänstproxyn på port 2222, en godtycklig port som du valde tidigare.

> [!NOTE]
> Kontrollera att din enhetsproxy är korrekt konfigurerad för RDP och konfigurerad med RDP-port 3389.

![RDP-klienten ansluter till det lokala proxy-programmet](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapar du en IoT-hubb, registrerat en enhet och distribuerat ett proxy-program för att aktivera RDP och SSH på en IoT-enhet. RDP-och SSH-trafiken kommer att tunnlas via en enhets ström via IoT Hub. Den här processen eliminerar behovet av direkt anslutning till enheten.

Mer information om enhets strömmar finns i:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
