---
title: ta med fil
description: ta med fil
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 01/14/2021
ms.openlocfilehash: 4999bd93f338ca7b34b141b88e06e4a769a4aaa1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876445"
---
I följande avsnitt ska du konfigurera en terminal och använda Azure CLI för att skapa en IoT-hubb. Om du vill konfigurera en terminal som kör Azure CLI-kommandon kan du antingen använda den webbläsarbaserade Azure Cloud Shell eller använda en lokal terminal.
* Om du Cloud Shell du till nästa avsnitt: [Starta Cloud Shell](#launch-the-cloud-shell). 
* Om du vill använda en lokal terminal hoppar du över nästa avsnitt och går till [Öppna en lokal terminal.](#open-a-local-terminal)

## <a name="launch-the-cloud-shell"></a>Starta Cloud Shell
I det här avsnittet skapar du en Cloud Shell och konfigurerar terminalmiljön.

Logga in på Azure Portal på https://portal.azure.com.  

Så här startar du Cloud Shell:

1. Välj knappen **Cloud Shell** på menyraden längst upp till höger i Azure Portal. 

    ![knappen Azure Portal Cloud Shell](media/iot-hub-include-create-hub-cli/cloud-shell-button.png)

    > [!NOTE]
    > Om det är första gången du använder Cloud Shell uppmanas du att skapa lagring, vilket krävs för att använda Cloud Shell.  Välj en prenumeration för att skapa ett lagringskonto och Microsoft Azure Files-resurs. 

2. Välj önskad CLI-miljö i **listrutan Välj** miljö. Den här snabbstarten använder **Bash-miljön.** Alla följande CLI-kommandon fungerar även i PowerShell-miljön. 

    ![Välj CLI-miljö](media/iot-hub-include-create-hub-cli/cloud-shell-environment.png)

3. Hoppa över nästa avsnitt och gå till [Installera Azure IoT-tillägget](#install-the-azure-iot-extension). 

## <a name="open-a-local-terminal"></a>Öppna en lokal terminal
Om du väljer att använda en lokal terminal i stället för Cloud Shell slutför du det här avsnittet.  

1. Öppna en lokal terminal.
1. Kör [kommandot az login:](/cli/azure/reference-index#az_login)

   ```azurecli
   az login
   ```

    Om CLI kan öppna din standardwebbläsare gör den det och läser in en Inloggningssida för Azure.

    Annars öppnar du en webbläsarsida på och https://aka.ms/devicelogin anger auktoriseringskoden som visas i terminalen.

    Om ingen webbläsare är tillgänglig eller om webbläsaren inte kan öppnas använder du enhetskodflödet med `az login --use-device-code` .

1. Logga in med dina autentiseringsuppgifter för kontot i webbläsaren.

    Mer information om olika autentiseringsmetoder finns i [Logga in med Azure CLI]( /cli/azure/authenticate-azure-cli ).

1. Gå till nästa avsnitt: [Installera Azure IoT-tillägget](#install-the-azure-iot-extension). 

## <a name="install-the-azure-iot-extension"></a>Installera Azure IoT-tillägget
I det här avsnittet installerar du Microsoft Azure IoT-tillägget för Azure CLI till CLI-gränssnittet. IOT-tillägget lägger IoT Hub, IoT Edge och IoT Device Provisioning Service (DPS) specifika kommandon till Azure CLI.

> [!IMPORTANT]
> Terminalkommandona i resten av den här snabbstarten fungerar på samma sätt i Cloud Shell eller en lokal terminal. Om du vill köra ett kommando väljer **du Kopiera** för att kopiera ett kodblock i den här snabbstarten. Klistra sedan in det i CLI-gränssnittet och kör det.

Kör kommandot [az extension add.](/cli/azure/extension#az_extension_add) 

   ```azurecli
   az extension add --name azure-iot
   ```
[!INCLUDE [iot-hub-cli-version-info](iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub
I det här avsnittet använder du Azure CLI för att skapa en IoT-hubb och en resursgrupp.  En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. En IoT-hubb fungerar som en central meddelandehubb för dubbelriktad kommunikation mellan ditt IoT-program och enheterna. 

Så här skapar du en IoT-hubb och en resursgrupp:

1. Kör kommandot [az group create](/cli/azure/group#az_group_create) för att skapa en resursgrupp. Följande kommando skapar en resursgrupp med namnet *MyResourceGroup* på *platsen eastus.* 
    >[!NOTE]
    > Du kan också ange en alternativ plats. Om du vill se tillgängliga platser kör du `az account list-locations` . I den här *självstudien används eastus* enligt exempelkommandot. 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Kör kommandot [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) för att skapa en IoT-hubb. Det kan ta några minuter att skapa en IoT-hubb. 

    *YourIotHubName*. Ersätt platshållaren och de omgivande kparenteserna i följande kommando med det namn du valde för din IoT-hubb. Ett IoT-hubbnamn måste vara globalt unikt i Azure. Använd namnet på din IoT-hubb i resten av den här snabbstarten oavsett var du ser platshållaren.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-a-simulated-device"></a>Skapa en simulerad enhet
I det här avsnittet skapar du en simulerad IoT-enhet som är ansluten till din IoT-hubb. 

Så här skapar du en simulerad enhet:
1. Kör kommandot [az iot hub device-identity create](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_create) i CLI-gränssnittet. Detta skapar den simulerade enhetsidentiteten. 

    *YourIotHubName*. Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb. 

    *myDevice*. Du kan använda det här namnet direkt för det simulerade enhets-ID:t i resten av den här artikeln. Du kan också använda ett annat namn. 

    ```azurecli
    az iot hub device-identity create --device-id myDevice --hub-name {YourIoTHubName} 
    ```

1.  Kör kommandot [az iot hub device-identity connection-string show.](/cli/azure/iot/hub/device-identity/connection-string#az_iot_hub_device_identity_connection_string_show) 

    ```azurecli
    az iot hub device-identity connection-string show --device-id myDevice --hub-name {YourIoTHubName}
    ```

    Anslutningssträngens utdata har följande format:

    ```Output
    HostName=<your IoT Hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<some value>
    ```

1. Spara anslutningssträngen på en säker plats. 

> [!NOTE]
> Håll CLI-gränssnittet öppet. Du kommer att använda det senare.