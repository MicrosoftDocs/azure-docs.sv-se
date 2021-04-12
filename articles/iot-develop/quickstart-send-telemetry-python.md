---
title: Skicka telemetri för enheten till Azure IoT Central snabb start (python)
description: I den här snabb starten använder du Azure IoT Hub Device SDK för python för att skicka telemetri från en enhet till IoT Central.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: 5d872dd7c94a0b3ab23623bb246ff7ae81609779
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047175"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-central-python"></a>Snabb start: skicka telemetri från en enhet till Azure IoT Central (python)

**Gäller för**: [enhets program utveckling](about-iot-develop.md#device-application-development)

I den här snabb starten får du lära dig ett grundläggande program utvecklings arbets flöde för IoT-enheter. Först använder du Azure IoT Central för att skapa ett moln program. Sedan använder du Azure IoT python SDK för att bygga en simulerad enhet, ansluta till IoT Central och skicka enhet till moln-telemetri. 

## <a name="prerequisites"></a>Förutsättningar
- [Python 3.7 +](https://www.python.org/downloads/). Andra versioner av python som stöds finns i [funktioner i Azure IoT-enheter](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).
    
    För att se till att din python-version är uppdaterad, kör `python --version` . Om du har både python 2 och python 3 installerade, och använder en python 3-miljö, installerar du alla bibliotek med `pip3` . `pip3`Att köra garanterar att biblioteken installeras i din python 3-körning.
    > [!IMPORTANT]
    > I python-installationsprogrammet väljer du alternativet för att **lägga till python i sökvägen**. Om du redan har python 3,7 eller senare installerat kontrollerar du att du har lagt till python-installationsmappen i `PATH` miljövariabeln.

## <a name="create-an-application"></a>Skapa ett program
I det här avsnittet skapar du ett IoT Central-program. IoT Central är en portal baserad IoT-programplattform som hjälper till att minska komplexiteten och kostnaderna för att utveckla och hantera IoT-lösningar.

Så här skapar du ett Azure IoT Central-program:
1. Bläddra till [Azure IoT Central](https://apps.azureiotcentral.com/) och logga in med ett Microsoft personal-, arbets-eller skol konto.
1. Navigera till **build** och välj **anpassade appar**.
   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-build.png" alt-text="IoT Central start sida":::
1. I **program namn** anger du ett unikt namn eller använder det genererade namnet.
1. I **URL** anger du ett URL-prefix för minnes värt program eller använder det GENERERAde URL-prefixet.
1. Lämna **Programmall** inställd till *anpassat program*. List rutan kan visa andra alternativ, om det redan finns mallar i ditt konto.
1. Välj ett **pris Plans** alternativ. 
    - Välj **kostnads fritt** om du vill använda programmet kostnads fritt i sju dagar. Du kan konvertera ett kostnads fritt program till standard prissättning innan det går ut.
    - Alternativt kan du välja en standard pris plan. Om du väljer standard prissättning visas fler alternativ och du måste ange en **katalog**, en **Azure-prenumeration** och en **plats**. Mer information om priser finns i [priser för Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). 
        - **Katalog** är den Azure Active Directory som du skapar programmet i. En Azure Active Directory innehåller användar identiteter, autentiseringsuppgifter och annan organisations information. Om du inte har en Azure Active Directory skapas en när du skapar en Azure-prenumeration.
        - Med en **Azure-prenumeration** kan du skapa instanser av Azure-tjänster. IoT Central etablerar resurser i din prenumeration. Om du inte har någon Azure-prenumeration kan du [skapa en kostnads fri](https://azure.microsoft.com/free/). När du har skapat prenumerationen går du tillbaka till sidan IoT Central **nytt program** . Den nya prenumerationen visas i list rutan för **Azure-prenumerationen** .
        - **Location** är [Azure-geografin](https://azure.microsoft.com/global-infrastructure/geographies/) där du skapar ett program. Välj en plats som är fysiskt närmast dina enheter för att få optimala prestanda. När du har valt en plats kan du inte flytta programmet till en annan plats.

    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-pricing.png" alt-text="Dialog rutan IoT Central nytt program":::
1. Välj **Skapa**.
    
    När IoT Central skapar programmet omdirigeras du till program instrument panelen.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-created.png" alt-text="IoT Central ny instrument panel för program":::

## <a name="add-a-device"></a>Lägg till en enhet
I det här avsnittet lägger du till en ny enhet till ditt IoT Central-program. Enheten är en instans av en enhets mall som representerar en riktig eller simulerad enhet som du ska ansluta till programmet. 

Så här skapar du en ny enhet:
1. Välj **enheter** i den vänstra rutan och välj sedan **+ ny**. Dialog rutan ny enhet öppnas.
1. Lämna **enhets mal len** inställd på *otilldelade*.

    > [!NOTE]
    > I den här snabb starten för enkelhets skull ansluter du en simulerad enhet som använder en otilldelad mall. Om du fortsätter att använda IoT Central för att hantera enheter, lär du dig hur du använder enhetsspecifika mallar. En översikt över hur du arbetar med enhetsspecifika finns i [snabb start: Lägg till en simulerad enhet i IoT Central programmet](../iot-central/core/quick-create-simulated-device.md).
1. Ange ett eget **enhets namn** och **enhets-ID**. Du kan också använda de genererade värdena.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-create-device.png" alt-text="Dialog rutan IoT Central ny enhet":::
1. Välj **Skapa**.

    Enheten som skapas visas i listan **alla enheter** .
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-devices-list.png" alt-text="Lista med IoT Central alla enheter":::
    
Hämta anslutnings information för den nya enheten:
1. I listan **alla enheter** dubbelklickar du på den länkade enhetens namn för att visa information. 
1. I menyn högst upp väljer du **Anslut**.

    I dialog rutan **enhets anslutning** visas anslutnings information:  :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-device-connect.png" alt-text="IoT central information om enhets anslutning":::
1. Kopiera följande värden från dialog rutan **enhets anslutning** till en säker plats. Du kommer att använda dessa i nästa avsnitt för att ansluta enheten till IoT Central.
    * `ID scope`
    * `Device ID`
    * `Primary key`

## <a name="send-messages-and-monitor-telemetry"></a>Skicka meddelanden och övervaka telemetri
I det här avsnittet ska du använda python SDK för att bygga en simulerad enhet och skicka telemetri till ditt IoT Central-program. 

1. Öppna en Terminal med Windows CMD eller PowerShell eller bash (för Windows eller Linux). Du använder terminalen för att installera python SDK, uppdatera miljövariabler och köra python-kod exemplet.

1. Kopiera [enhets exemplen för Azure IoT python SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) till din lokala dator.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Gå till katalogen *Azure-IoT-SDK: python/Azure-IoT-Device/samples* .

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples
    ```
1. Installera Azure IoT python SDK.

    ```console
    pip install azure-iot-device
    ```

1. Ange var och en av följande miljövariabler för att aktivera den simulerade enheten för att ansluta till IoT Central. För `ID_SCOPE` , `DEVICE_ID` , och `DEVICE_KEY` använder du de värden som du sparade i dialog rutan IoT Central *enhets anslutning* .

    **Windows CMD**

    ```console
    set PROVISIONING_HOST=global.azure-devices-provisioning.net
    ```
    ```console
    set ID_SCOPE=<your ID scope>
    ```
    ```console
    set DEVICE_ID=<your device ID>
    ```
    ```console
    set DEVICE_KEY=<your device's primary key>
    ```

    > [!NOTE]
    > För Windows CMD finns det inga citat tecken runt anslutnings strängen eller andra variabel värden.

    **PowerShell**

    ```azurepowershell
    $env:PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```azurepowershell
    $env:ID_SCOPE='<your ID scope>'
    ```
    ```azurepowershell
    $env:DEVICE_ID='<your device ID>'
    ```
    ```azurepowershell
    $env:DEVICE_KEY='<your device's primary key>'
    ```

    **Bash (Linux eller Windows)**

    ```bash
    export PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```bash
    export ID_SCOPE='<your ID scope>'
    ```
    ```bash
    export DEVICE_ID='<your device ID>'
    ```
    ```bash
    export DEVICE_KEY='<your device's primary key>'
    ```

1. I din terminal kör du koden för exempel filen * simple_send_temperature. py. Den här koden använder den simulerade IoT-enheten och skickar ett meddelande till IoT Central.

    Så här kör du python-exemplet från terminalen:
    ```console
    python ./simple_send_temperature.py
    ```

    Du kan också köra python-koden från exemplet i python IDE:
    ```python
    import asyncio
    import os
    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import Message
    import uuid
    import json
    import random

    # ensure environment variables are set for your device and IoT Central application credentials
    provisioning_host = os.getenv("PROVISIONING_HOST")
    id_scope = os.getenv("ID_SCOPE")
    registration_id = os.getenv("DEVICE_ID")
    symmetric_key = os.getenv("DEVICE_KEY")

    # allows the user to quit the program from the terminal
    def stdin_listener():
        """
        Listener for quitting the sample
        """
        while True:
            selection = input("Press Q to quit\n")
            if selection == "Q" or selection == "q":
                print("Quitting...")
                break

    async def main():

        # provisions the device to IoT Central-- this uses the Device Provisioning Service behind the scenes
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
            provisioning_host=provisioning_host,
            registration_id=registration_id,
            id_scope=id_scope,
            symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print("The complete registration result is")
        print(registration_result.registration_state)

        if registration_result.status == "assigned":
            print("Your device has been provisioned. It will now begin sending telemetry.")
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
            )

            # Connect the client.
            await device_client.connect()

        # Send the current temperature as a telemetry message
        async def send_telemetry():
            print("Sending telemetry for temperature")

            while True:
                current_temp = random.randrange(10, 50)  # Current temperature in Celsius (randomly generated)
                # Send a single temperature report message
                temperature_msg = {"temperature": current_temp}

                msg = Message(json.dumps(temperature_msg))
                msg.content_encoding = "utf-8"
                msg.content_type = "application/json"
                print("Sent message")
                await device_client.send_message(msg)
                await asyncio.sleep(8)

        send_telemetry_task = asyncio.create_task(send_telemetry())

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)
        # Wait for user to indicate they are done listening for method calls
        await user_finished

        send_telemetry_task.cancel()
        # Finally, shut down the client
        await device_client.disconnect()

    if __name__ == "__main__":
        asyncio.run(main())

        # If using Python 3.6 or below, use the following code instead of asyncio.run(main()):
        # loop = asyncio.get_event_loop()
        # loop.run_until_complete(main())
        # loop.close()
    ```

När python-koden skickar ett meddelande från enheten till ditt IoT Central-program visas meddelandena på fliken **rå data** på enheten i IoT Central. Du kan behöva uppdatera sidan för att visa de senaste meddelandena.

   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-telemetry-output.png" alt-text="Skärm bild som visar IoT Central rå data utdata":::

Din enhet är nu säkert ansluten och skickar telemetri till Azure IoT.

## <a name="clean-up-resources"></a>Rensa resurser
Om du inte längre behöver IoT Central resurserna som skapas i den här självstudien kan du ta bort dem från IoT Central portalen. Om du planerar att fortsätta följa dokumentationen i den här hand boken kan du behålla programmet som du skapade och återanvända det för andra exempel.

Ta bort exempel programmet Azure IoT Central och alla dess enheter och resurser:
1. Välj **Administration**  >  **ditt program**.
1. Välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig ett grundläggande Azure IoT Application-arbetsflöde för att på ett säkert sätt ansluta en enhet till molnet och skicka enhet till moln-telemetri. Du använde Azure-IoT Central för att skapa ett program och en enhet. sedan använde du Azure IoT python SDK för att skapa en simulerad enhet och skicka telemetri. Du använde också IoT Central för att övervaka Telemetrin.

I nästa steg ska du utforska Azure IoT python SDK genom program exempel.

- [Asynkrona exempel](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios): den här katalogen innehåller asynkrona python-exempel för ytterligare IoT Hub scenarier.
- [Synkrona exempel](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples): den här katalogen innehåller python-exempel som ska användas med python 2,7 eller synkrona kompatibilitetsproblem för python 3.6 +
- [IoT Edge exempel](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios): den här katalogen innehåller python-exempel för att arbeta med Edge-moduler och underordnade enheter.
