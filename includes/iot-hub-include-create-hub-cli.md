---
title: ta med fil
description: ta med fil
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 01/14/2021
ms.openlocfilehash: 10bd2c4902157b9e01b1cb0ff10b3ebdf448568c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102244877"
---
I följande avsnitt ställer du in en Terminal och använder Azure CLI för att skapa en IoT-hubb. Om du vill konfigurera en Terminal som kör Azure CLI-kommandon kan du antingen använda den webbläsarbaserade Azure Cloud Shell eller använda en lokal Terminal.
* Om du vill använda Cloud Shell går du till nästa avsnitt: [starta Cloud Shell](#launch-the-cloud-shell). 
* Om du vill använda en lokal Terminal, hoppar du över nästa avsnitt och går till [öppna en lokal Terminal](#open-a-local-terminal).

## <a name="launch-the-cloud-shell"></a>Starta Cloud Shell
I det här avsnittet skapar du en Cloud Shell-session och konfigurerar din terminal-miljö.

Logga in på Azure Portal på https://portal.azure.com.  

Så här startar du Cloud Shell:

1. Välj knappen **Cloud Shell** i den övre högra meny raden i Azure Portal. 

    ![Knappen Azure Portal Cloud Shell](media/iot-hub-include-create-hub-cli/cloud-shell-button.png)

    > [!NOTE]
    > Om det här är första gången du använder Cloud Shell, så blir du ombedd att skapa lagring, vilket krävs för att använda Cloud Shell.  Välj en prenumeration för att skapa ett lagrings konto och Microsoft Azure fil resurs. 

2. Välj önskad CLI-miljö i list rutan **Välj miljö** . I den här snabb starten används **bash** -miljön. Alla följande CLI-kommandon fungerar i PowerShell-miljön. 

    ![Välj CLI-miljö](media/iot-hub-include-create-hub-cli/cloud-shell-environment.png)

3. Hoppa över nästa avsnitt och gå till [Installera Azure IoT-tillägget](#install-the-azure-iot-extension). 

## <a name="open-a-local-terminal"></a>Öppna en lokal Terminal
Om du väljer att använda en lokal Terminal i stället för att Cloud Shell, fyller du i det här avsnittet.  

1. Öppna en lokal Terminal.
1. Kör kommandot [AZ login](/cli/azure/reference-index#az_login) :

   ```azurecli
   az login
   ```

    Om CLI kan öppna din standard webbläsare så gör den det och läser in en Azure-inloggnings sida.

    Annars öppnar du en webb sida på https://aka.ms/devicelogin och anger den auktoriseringskod som visas i din terminal.

    Om ingen webbläsare är tillgänglig eller om det inte går att öppna webbläsaren använder du enhets kod flödet med `az login --use-device-code` .

1. Logga in med dina autentiseringsuppgifter för kontot i webbläsaren.

    Mer information om olika autentiseringsmetoder finns i [Logga in med Azure CLI]( /cli/azure/authenticate-azure-cli ).

1. Gå till nästa avsnitt: [Installera Azure IoT-tillägget](#install-the-azure-iot-extension). 

## <a name="install-the-azure-iot-extension"></a>Installera Azure IoT-tillägget
I det här avsnittet installerar du Microsoft Azure IoT-tillägget för Azure CLI till CLI-gränssnittet. IOT-tillägget lägger till IoT Hub-, IoT Edge-och IoT Device Provisioning-tjänst (DPS)-kommandon i Azure CLI.

> [!IMPORTANT]
> Terminal-kommandona i resten av den här snabb starten fungerar på samma plats i Cloud Shell eller i en lokal Terminal. Om du vill köra ett kommando väljer du **Kopiera** för att kopiera ett kodblock i den här snabb starten. Klistra sedan in den i CLI-gränssnittet och kör den.

Kör kommandot [AZ Extension Add](/cli/azure/extension#az-extension-add) . 

   ```azurecli
   az extension add --name azure-iot
   ```
[!INCLUDE [iot-hub-cli-version-info](iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub
I det här avsnittet använder du Azure CLI för att skapa en IoT-hubb och en resurs grupp.  En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. En IoT-hubb fungerar som en central meddelande hubb för dubbelriktad kommunikation mellan IoT-programmet och enheterna. 

Så här skapar du en IoT-hubb och en resurs grupp:

1. Kör kommandot [AZ Group Create](/cli/azure/group#az-group-create) för att skapa en resurs grupp. Följande kommando skapar en resurs grupp med namnet *MyResourceGroup* på den *östra* platsen. 
    >[!NOTE]
    > Du kan också ange en alternativ plats. Kör om du vill se tillgängliga platser `az account list-locations` . I den här självstudien används *öster* som du ser i exempel kommandot. 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Kör kommandot [AZ IoT Hub Create](/cli/azure/iot/hub#az-iot-hub-create) för att skapa en IoT-hubb. Det kan ta några minuter att skapa en IoT-hubb. 

    *YourIotHubName*. Ersätt den här plats hållaren och de omgivande klamrarna i följande kommando med det namn som du valde för din IoT-hubb. Ett IoT Hub-namn måste vara globalt unikt i Azure. Använd ditt IoT Hub-namn i resten av den här snabb starten där du ser plats hållaren.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-a-simulated-device"></a>Skapa en simulerad enhet
I det här avsnittet skapar du en simulerad IoT-enhet som är ansluten till din IoT-hubb. 

Så här skapar du en simulerad enhet:
1. Kör kommandot [AZ IoT Hub Device-Identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) i CLI-gränssnittet. Detta skapar den simulerade enhets identiteten. 

    *YourIotHubName*. Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb. 

    min *enhet*. Du kan använda det här namnet direkt för det simulerade enhets-ID: t i resten av den här artikeln. Du kan också använda ett annat namn. 

    ```azurecli
    az iot hub device-identity create --device-id myDevice --hub-name {YourIoTHubName} 
    ```

1.  Kör kommandot [AZ IoT Hub Device-Identity Connection-sträng show](/cli/azure/ext/azure-iot/iot/hub/device-identity/connection-string#ext_azure_iot_az_iot_hub_device_identity_connection_string_show) . 

    ```azurecli
    az iot hub device-identity connection-string show --device-id myDevice --hub-name {YourIoTHubName}
    ```

    Anslutnings strängens utdata har följande format:

    ```Output
    HostName=<your IoT Hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<some value>
    ```

1. Spara anslutnings strängen på en säker plats. 

> [!NOTE]
> Håll CLI-gränssnittet öppet. Du kommer att använda det senare.