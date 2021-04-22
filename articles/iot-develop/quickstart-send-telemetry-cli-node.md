---
title: Skicka enhettelemetri till Azure IoT Hub (Node.js)
description: I den här snabbstarten använder du Azure IoT Hub Device SDK för att Node.js skicka telemetri från en enhet till en IoT-hubb.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: 0e1c99124228da9490abaa17ecc41b931631d9fb
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876984"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>Snabbstart: Skicka telemetri från en enhet till en IoT-hubb (Node.js)

**Gäller för:** [Utveckling av enhetsprogram](about-iot-develop.md#device-application-development)

I den här snabbstarten får du lära dig ett grundläggande arbetsflöde för utveckling av IoT-enhetsprogram. Du använder Azure CLI för att skapa en Azure IoT-hubb och en simulerad enhet. Sedan använder du Azure IoT Node.js SDK för att komma åt enheten och skicka telemetri till hubben.

## <a name="prerequisites"></a>Förutsättningar
- Om du inte har en Azure-prenumeration kan [du skapa en utan kostnad](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Azure CLI. Du kan köra alla kommandon i den här snabbstarten med Azure Cloud Shell, ett interaktivt CLI-gränssnitt som körs i webbläsaren. Om du använder Cloud Shell behöver du inte installera något. Om du föredrar att använda CLI lokalt kräver den här snabbstarten Azure CLI version 2.0.76 eller senare. Kör az --version för att se versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).
- [Node.js 10+](https://nodejs.org). Om du använder Azure Cloud Shell bör du inte uppdatera den installerade versionen av Node.js. Den Azure Cloud Shell har redan den senaste Node.js versionen.

    Kontrollera den aktuella versionen Node.js på utvecklingsdatorn med hjälp av följande kommando:

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>Skicka meddelanden med Node.js SDK
I det här avsnittet använder du SDKNode.js för att skicka meddelanden från din simulerade enhet till din IoT Hub. 

1. Öppna ett nytt terminalfönster. Du använder den här terminalen för att installera Node.js SDK och arbeta med Node.js exempelkod. Du bör nu ha två terminaler öppna: den som du precis öppnade för att arbeta med Node.js och CLI-gränssnittet som du använde i föregående avsnitt för att ange Azure CLI-kommandon.

1. Kopiera Azure [IoT Node.js SDK-enhetsexempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) till din lokala dator:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Gå till katalogen *azure-iot-sdk-node/device/samples/pnp:*

    ```console
    cd azure-iot-sdk-node/device/samples/pnp
    ```

1. Installera Azure IoT Node.js SDK och nödvändiga beroenden:

    ```console
    npm install
    ```

    Det här kommandot installerar rätt beroenden som anges ipackage.js *på filen* i enhetens exempelkatalog.

1. Ange båda av följande miljövariabler så att den simulerade enheten kan ansluta till Azure IoT.
    * Ange en miljövariabel med namnet `IOTHUB_DEVICE_CONNECTION_STRING` . För variabelvärdet använder du enhetsanslutningssträngen som du sparade i föregående avsnitt.
    * Ange en miljövariabel med namnet `IOTHUB_DEVICE_SECURITY_TYPE` . För variabeln använder du stränglitteralvärdet `connectionString` .

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```
    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```

    > [!NOTE]
    > För Windows CMD finns det inga citattecken runt strängvärdena för varje variabel.

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_CONNECTION_STRING='<your connection string here>'
    ```
    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='connectionString'
    ```

    **Bash (Linux eller Windows)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    ```
    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```
1. I det öppna CLI-gränssnittet kör du [kommandot az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) för att börja övervaka händelser på din simulerade IoT-enhet.  Händelsemeddelanden skrivs ut i terminalen när de tas emot.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. I Node.js terminalen kör du koden för den installerade exempelfilen *simple_thermostat.js* . Den här koden kommer åt den simulerade IoT-enheten och skickar ett meddelande till IoT-hubben.

    Så här kör Node.js exempel från terminalen:
    ```console
    node ./simple_thermostat.js
    ```
    > [!NOTE]
    > I det här kodexe exemplet används Azure IoT Plug and Play, vilket gör att du kan integrera smarta enheter i dina lösningar utan manuell konfiguration.  Som standard använder de flesta exempel i den här dokumentationen IoT-Plug and Play. Mer information om fördelarna med IoT PnP och användningsfall finns i Vad är [IoT-Plug and Play?](../iot-pnp/overview-iot-plug-and-play.md)

När koden Node.js ett simulerat telemetrimeddelande från enheten till IoT-hubben visas meddelandet i CLI-gränssnittet som övervakar händelser:

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: ''
  interface: dtmi:com:example:Thermostat;1
  module: ''
  origin: <your device ID>
  payload:
    temperature: 36.87027777131555
```

Enheten är nu säkert ansluten och skickar telemetri till Azure IoT Hub.

## <a name="clean-up-resources"></a>Rensa resurser
Om du inte längre behöver De Azure-resurser som skapades i den här snabbstarten kan du använda Azure CLI för att ta bort dem.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. 

Ta bort en resursgrupp med namnet:
1. Kör kommandot [az group delete.](/cli/azure/group#az_group_delete) Det här kommandot tar bort resursgruppen, IoT Hub och enhetsregistreringen som du skapade.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Kör kommandot [az group list](/cli/azure/group#az_group_list) för att bekräfta att resursgruppen har tagits bort.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig ett grundläggande Arbetsflöde för Azure IoT-program för säker anslutning av en enhet till molnet och sändning av enhet-till-moln-telemetri. Du använde Azure CLI för att skapa en IoT-hubb och en simulerad enhet. Sedan använde du Azure IoT Node.js SDK för att komma åt enheten och skicka telemetri till hubben. 

Som ett nästa steg kan du utforska Azure IoT Node.js SDK med hjälp av programexempel.

- [Fler Node.js exempel: Den](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)här katalogen innehåller fler exempel från Node.js SDK-lagringsplatsen för att visa IoT Hub scenarier.