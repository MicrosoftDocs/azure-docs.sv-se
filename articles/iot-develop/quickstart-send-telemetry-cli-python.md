---
title: Skicka telemetri för enheten till Azure IoT Hub snabb start (python)
description: I den här snabb starten använder du Azure IoT Hub Device SDK för python för att skicka telemetri från en enhet till en IoT-hubb.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: 0d791ebedd1b03bd50150170ad1b36a08371c9ae
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654986"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-azure-iot-hub-python"></a>Snabb start: skicka telemetri från en enhet till en Azure IoT Hub (python)

**Gäller för**: [enhets program utveckling](about-iot-develop.md#device-application-development)

I den här snabb starten får du lära dig ett grundläggande program utvecklings arbets flöde för IoT-enheter. Du använder Azure CLI för att skapa en Azure IoT-hubb och en enhet. sedan använder du Azure IoT python SDK för att skapa en simulerad klient enhet och skicka telemetri till hubben. 

## <a name="prerequisites"></a>Förutsättningar
- Om du inte har en Azure-prenumeration kan du [skapa en kostnads fri](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Azure CLI. Du kan köra alla kommandon i den här snabb starten med hjälp av Azure Cloud Shell, ett interaktivt CLI-gränssnitt som körs i din webbläsare. Om du använder Cloud Shell behöver du inte installera något. Om du föredrar att använda CLI lokalt kräver den här snabb starten Azure CLI version 2.0.76 eller senare. Kör az --version för att se versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).
- [Python 3.7 +](https://www.python.org/downloads/). Andra versioner av python som stöds finns i [funktioner i Azure IoT-enheter](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).
    
    För att se till att din python-version är uppdaterad, kör `python --version` . Om du har både python 2 och python 3 installerade, och använder en python 3-miljö, installerar du alla bibliotek med `pip3` . Detta säkerställer att biblioteken installeras i din python 3-körning.
    > [!IMPORTANT]
    > I python-installationsprogrammet väljer du alternativet för att **lägga till python i sökvägen**. Om du redan har python 3,7 eller senare installerat kontrollerar du att du har lagt till python-installationsmappen i `PATH` miljövariabeln.

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-python-sdk-to-send-messages"></a>Använda python SDK för att skicka meddelanden
I det här avsnittet ska du använda python SDK för att skicka meddelanden från din simulerade enhet till IoT Hub.

1. Öppna ett nytt terminalfönster. Du kommer att använda den här terminalen för att installera python SDK och arbeta med python-exempel kod. Du bör nu ha två terminaler öppna: den som du nyss öppnade för att arbeta med python och CLI-gränssnittet som du använde i föregående avsnitt för att ange Azure CLI-kommandon.       

1. Kopiera [enhets exemplen för Azure IoT python SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) till din lokala dator:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

    och navigera till katalogen *Azure-IoT-SDK: python/Azure-IoT-Device/samples* :

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples
    ```
1. Installera Azure IoT python SDK:

    ```console
    pip install azure-iot-device
    ```
1. Ange enhets anslutnings strängen som en miljö variabel som kallas `IOTHUB_DEVICE_CONNECTION_STRING` . Det här är den sträng som du fick i föregående avsnitt när du har skapat din simulerade python-enhet.

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```

    > [!NOTE]
    > För Windows CMD finns det inga citat tecken runt anslutnings strängen.

    **Linux (bash)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    ```

1. I ditt öppna CLI-gränssnitt kör du kommandot [AZ IoT Hub Monitor-Events](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub?view=azure-cli-latest#ext-azure-iot-az-iot-hub-monitor-events&preserve-view=true) för att börja övervaka händelser på din simulerade IoT-enhet.  Händelse meddelanden skrivs ut i terminalen när de tas emot.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. I din python-Terminal kör du koden för den installerade exempel filen *simple_send_message. py* . Den här koden ansluter till den simulerade IoT-enheten och skickar ett meddelande till IoT Hub.

    Så här kör du python-exemplet från terminalen:
    ```console
    python ./simple_send_message.py
    ```

    Du kan också köra python-koden från exemplet i python IDE:
    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient


    async def main():
        # Fetch the connection string from an environment variable
        conn_str = os.getenv("IOTHUB_DEVICE_CONNECTION_STRING")

        # Create instance of the device client using the authentication provider
        device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

        # Connect the device client.
        await device_client.connect()

        # Send a single message
        print("Sending message...")
        await device_client.send_message("This is a message that is being sent")
        print("Message successfully sent!")

        # finally, disconnect
        await device_client.disconnect()


    if __name__ == "__main__":
        asyncio.run(main())
    ```

När python-koden skickar ett meddelande från enheten till IoT Hub visas meddelandet i CLI-gränssnittet som övervakar händelser:

```output
Starting event monitor, use ctrl-c to stop...
event:
origin: <your Device name>
payload: This is a message that is being sent
```

Enheten är nu säker ansluten och skickar telemetri till Azure IoT Hub.

## <a name="clean-up-resources"></a>Rensa resurser
Om du inte längre behöver de Azure-resurser som skapats i den här snabb starten kan du ta bort dem med hjälp av Azure CLI.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser.

Ta bort en resursgrupp med namnet:
1. Kör kommandot [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete&preserve-view=true) . Detta tar bort resurs gruppen, IoT Hub och den enhets registrering som du skapade.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Kör kommandot [AZ Group List](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-list&preserve-view=true) för att bekräfta att resurs gruppen har tagits bort.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Nästa steg
I den här snabb starten har du lärt dig ett grundläggande Azure IoT Application-arbetsflöde för att på ett säkert sätt ansluta en enhet till molnet och skicka enhet till moln-telemetri. Du använde Azure CLI för att skapa en IoT-hubb och en enhet. sedan använde du Azure IoT python SDK för att skapa en simulerad enhet och skicka telemetri till hubben. 

I nästa steg ska du utforska Azure IoT python SDK genom program exempel.

- [Asynkrona exempel](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios): den här katalogen innehåller asynkrona python-exempel för ytterligare IoT Hub scenarier.
- [Synkrona exempel](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples): den här katalogen innehåller python-exempel som ska användas med python 2,7 eller synkrona kompatibilitetsproblem för python 3.5 +
- [IoT Edge exempel](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios): den här katalogen innehåller python-exempel för att arbeta med Edge-moduler och underordnade enheter.