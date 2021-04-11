---
title: Skicka telemetri för enheten till Azure IoT Hub snabb start (Node.js)
description: I den här snabb starten använder du Azure IoT Hub Device SDK för Node.js för att skicka telemetri från en enhet till en IoT-hubb.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: 047700be674dfab997b5c87f7446c19fdea9e0eb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605968"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>Snabb start: skicka telemetri från en enhet till en IoT Hub (Node.js)

**Gäller för**: [enhets program utveckling](about-iot-develop.md#device-application-development)

I den här snabb starten får du lära dig ett grundläggande program utvecklings arbets flöde för IoT-enheter. Du använder Azure CLI för att skapa en Azure IoT-hubb och en simulerad enhet. sedan använder du Azure IoT Node.js SDK för att få åtkomst till enheten och skicka telemetri till hubben.

## <a name="prerequisites"></a>Förutsättningar
- Om du inte har en Azure-prenumeration kan du [skapa en kostnads fri](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Azure CLI. Du kan köra alla kommandon i den här snabb starten med hjälp av Azure Cloud Shell, ett interaktivt CLI-gränssnitt som körs i din webbläsare. Om du använder Cloud Shell behöver du inte installera något. Om du föredrar att använda CLI lokalt kräver den här snabb starten Azure CLI version 2.0.76 eller senare. Kör az --version för att se versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).
- [Node.js 10 +](https://nodejs.org). Om du använder Azure Cloud Shell ska du inte uppdatera den installerade versionen av Node.js. Den Azure Cloud Shell har redan den senaste Node.js versionen.

    Verifiera den aktuella versionen av Node.js på din utvecklings dator med hjälp av följande kommando:

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>Använd Node.js SDK för att skicka meddelanden
I det här avsnittet ska du använda Node.js SDK för att skicka meddelanden från din simulerade enhet till IoT Hub. 

1. Öppna ett nytt terminalfönster. Du kommer att använda den här terminalen för att installera Node.js SDK och arbeta med Node.js exempel kod. Du bör nu ha två terminaler öppna: den som du nyss öppnade för att arbeta med Node.js och CLI-gränssnittet som du använde i föregående avsnitt för att ange Azure CLI-kommandon.

1. Kopiera [Azure IoT Node.js SDK Device-exempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) till den lokala datorn:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Navigera till katalogen *Azure-IoT-SDK-Node/Device/samples/PnP* :

    ```console
    cd azure-iot-sdk-node/device/samples/pnp
    ```

1. Installera Azure IoT Node.js SDK och nödvändiga beroenden:

    ```console
    npm install
    ```

    Det här kommandot installerar de nödvändiga beroendena som anges i *package.js* filen i katalogen enhets exempel.

1. Ange båda följande miljövariabler för att aktivera den simulerade enheten för att ansluta till Azure IoT.
    * Ange en miljö variabel som kallas `IOTHUB_DEVICE_CONNECTION_STRING` . För variabelvärdet använder du den enhets anslutnings sträng som du sparade i föregående avsnitt.
    * Ange en miljö variabel som kallas `IOTHUB_DEVICE_SECURITY_TYPE` . Använd litteralt sträng värde för variabeln `connectionString` .

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```
    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```

    > [!NOTE]
    > För Windows CMD finns det inga citat tecken runt sträng värden för varje variabel.

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
1. I ditt öppna CLI-gränssnitt kör du kommandot [AZ IoT Hub Monitor-Events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) för att börja övervaka händelser på din simulerade IoT-enhet.  Händelse meddelanden skrivs ut i terminalen när de tas emot.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. I din Node.js Terminal kör du koden för den installerade exempel filen *simple_thermostat.js* . Den här koden ansluter till den simulerade IoT-enheten och skickar ett meddelande till IoT Hub.

    Köra Node.js-exemplet från terminalen:
    ```console
    node ./simple_thermostat.js
    ```
    > [!NOTE]
    > I det här kod exemplet används Azure IoT-Plug and Play som gör att du kan integrera smarta enheter i dina lösningar utan någon manuell konfiguration.  Som standard använder de flesta exempel i den här dokumentationen IoT Plug and Play. Om du vill veta mer om fördelarna med IoT PnP och om du använder eller inte använder den, kan du läsa mer i [IoT plug and Play?](../iot-pnp/overview-iot-plug-and-play.md)

När Node.js koden skickar ett simulerat telemetri från enheten till IoT Hub visas meddelandet i CLI-gränssnittet som övervakar händelser:

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

Enheten är nu säker ansluten och skickar telemetri till Azure IoT Hub.

## <a name="clean-up-resources"></a>Rensa resurser
Om du inte längre behöver de Azure-resurser som skapats i den här snabb starten kan du ta bort dem med hjälp av Azure CLI.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. 

Ta bort en resursgrupp med namnet:
1. Kör kommandot [AZ Group Delete](/cli/azure/group#az-group-delete) . Det här kommandot tar bort resurs gruppen, IoT Hub och den enhets registrering som du skapade.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Kör kommandot [AZ Group List](/cli/azure/group#az-group-list) för att bekräfta att resurs gruppen har tagits bort.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig ett grundläggande Azure IoT Application-arbetsflöde för att på ett säkert sätt ansluta en enhet till molnet och skicka enhet till moln-telemetri. Du använde Azure CLI för att skapa en IoT-hubb och en simulerad enhet. sedan använde du Azure IoT Node.js SDK för att få åtkomst till enheten och skicka telemetri till hubben. 

I nästa steg ska du utforska Azure IoT Node.js SDK genom program exempel.

- [Fler Node.js exempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples): den här katalogen innehåller fler exempel från Node.js SDK-lagringsplatsen för att demonstrera IoT Hub scenarier.