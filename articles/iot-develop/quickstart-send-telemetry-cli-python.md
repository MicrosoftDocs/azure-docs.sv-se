---
title: Skicka enhettelemetri till Azure IoT Hub (Python)
description: I den här snabbstarten använder du Azure IoT Hub Device SDK för Python för att skicka telemetri från en enhet till en IoT-hubb.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 03/24/2021
ms.openlocfilehash: be44ecf6dd154a21fd024ee37d92f61504350664
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876446"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-azure-iot-hub-python"></a>Snabbstart: Skicka telemetri från en enhet till en Azure IoT Hub (Python)

**Gäller för:** [Utveckling av enhetsprogram](about-iot-develop.md#device-application-development)

I den här snabbstarten får du lära dig ett grundläggande arbetsflöde för utveckling av IoT-enhetsprogram. Du använder Azure CLI för att skapa en Azure IoT-hubb och en enhet. Sedan använder du Azure IoT Python SDK för att skapa en simulerad klientenhet och skicka telemetri till hubben. 

## <a name="prerequisites"></a>Förutsättningar
- Om du inte har en Azure-prenumeration kan [du skapa en utan kostnad](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Azure CLI. Du kan köra alla kommandon i den här snabbstarten med Azure Cloud Shell, ett interaktivt CLI-gränssnitt som körs i webbläsaren. Om du använder Cloud Shell behöver du inte installera något. Om du föredrar att använda CLI lokalt kräver den här snabbstarten Azure CLI version 2.0.76 eller senare. Kör az --version för att se versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).
- [Python 3.7+](https://www.python.org/downloads/). Andra versioner av Python som stöds finns i [Azure IoT-enhetsfunktioner.](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features)
    
    Kontrollera att Python-versionen är uppdaterad genom att köra `python --version` . Om du har både Python 2 och Python 3 installerat och använder en Python 3-miljö installerar du alla bibliotek med `pip3` . Det här kommandot säkerställer att biblioteken installeras i Python 3-körningen.
    > [!IMPORTANT]
    > I Python-installationsprogrammet väljer du alternativet för att **lägga till Python i PATH**. Om du redan har Python 3.7 eller senare installerat bekräftar du att du har lagt till Python-installationsmappen i `PATH` miljövariabeln.

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-python-sdk-to-send-messages"></a>Använda Python SDK för att skicka meddelanden
I det här avsnittet använder du Python SDK för att skicka meddelanden från din simulerade enhet till din IoT Hub.

1. Öppna ett nytt terminalfönster. Du använder den här terminalen för att installera Python SDK och arbeta med Python-exempelkod. Du bör nu ha två öppna terminaler: den som du precis öppnade för att arbeta med Python och CLI-gränssnittet som du använde i föregående avsnitt för att ange Azure CLI-kommandon.       

1. Kopiera [Azure IoT Python SDK-enhetsexempel](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) till den lokala datorn:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```
1. Gå till katalogen *azure-iot-sdk-python/azure-iot-device/samples/pnp:*

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples/pnp
    ```
1. Installera Azure IoT Python SDK:

    ```console
    pip install azure-iot-device
    ```
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

1. I Python-terminalen kör du koden för den installerade exempelfilen *simple_thermostat.py*. Den här koden kommer åt den simulerade IoT-enheten och skickar ett meddelande till IoT-hubben.

    Så här kör du Python-exemplet från terminalen:
    ```console
    python ./simple_thermostat.py
    ```
    > [!NOTE]
    > I det här kodexe exemplet används Azure IoT Plug and Play, vilket gör att du kan integrera smarta enheter i dina lösningar utan manuell konfiguration.  Som standard använder de flesta exempel i den här dokumentationen IoT-Plug and Play. Mer information om fördelarna med IoT PnP och användningsfall finns i Vad är [IoT-Plug and Play?](../iot-pnp/overview-iot-plug-and-play.md)

 När Python-koden skickar ett meddelande från enheten till IoT-hubben visas meddelandet i CLI-gränssnittet som övervakar händelser:

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: ''
  interface: dtmi:com:example:Thermostat;1
  module: ''
  origin: <your device name>
  payload:
    temperature: 35
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
I den här snabbstarten har du lärt dig ett grundläggande Arbetsflöde för Azure IoT-program för säker anslutning av en enhet till molnet och sändning av enhet-till-moln-telemetri. Du använde Azure CLI för att skapa en IoT-hubb och en enhet. Sedan använde du Azure IoT Python SDK för att skapa en simulerad enhet och skicka telemetri till hubben. 

Som ett nästa steg kan du utforska Azure IoT Python SDK med hjälp av programexempel.

- [Asynkrona exempel:](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios)Den här katalogen innehåller asynkrona Python-exempel för IoT Hub scenarier.
- [Synkrona exempel:](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples)Den här katalogen innehåller Python-exempel för användning med Python 2.7 eller synkrona kompatibilitetsscenarier för Python 3.6+
- [IoT Edge exempel: Den](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios)här katalogen innehåller Python-exempel för att arbeta med Edge-moduler och nedströmsenheter.
