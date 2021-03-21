---
title: Självstudie – självstudie för anpassad python-modul med Azure IoT Edge
description: Den här självstudien visar hur du skapar en IoT Edge-modul med Python-kod och distribuerar den till en gränsenhet.
services: iot-edge
author: kgremban
manager: philmea
ms.reviewer: kgremban
ms.author: kgremban
ms.date: 08/04/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d6a95dae91ef3e6aa7d39cf8af51c355a87ea73a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462736"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-using-linux-containers"></a>Självstudie: utveckla och distribuera en python IoT Edge-modul med Linux-behållare

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Använd Visual Studio Code för att utveckla python-kod och distribuera den till en enhet som kör Azure IoT Edge.

Du kan använda Azure IoT Edge-moduler till att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensor data på den IoT Edge enhet som du har konfigurerat i snabb starten. I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * använda Visual Studio Code till att skapa en IoT Edge Python-modul
> * använda Visual Studio Code och Docker till att skapa en Docker-avbildning och publicera den till registret
> * distribuera modulen till din IoT Edge-enhet
> * visa genererade data.

IoT Edge-modulen du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys vid kanten är användbar när du vill minska mängden data som skickas till och lagras i molnet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien visar hur du utvecklar en modul i **python** med **Visual Studio Code** och hur du distribuerar den till en IoT Edge enhet.

IoT Edge stöder inte python-moduler med Windows-behållare.

Använd följande tabell för att förstå alternativen för att utveckla och distribuera python-moduler med hjälp av Linux-behållare:

| Python | Visuell Studio-kod | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Använda VS Code för python-moduler på Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Använda VS Code för python-moduler på Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

Innan du påbörjar den här självstudien bör du ha gått igenom den föregående själv studie kursen för att konfigurera utvecklings miljön för utveckling av Linux-behållare: [utveckla IoT Edge moduler med hjälp av Linux-behållare](tutorial-develop-for-linux.md). När du har slutfört den här självstudien bör du ha följande krav på plats:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En enhet som kör Azure IoT Edge. Du kan använda snabb starterna för att konfigurera en [Linux-enhet](quickstart-linux.md) eller [Windows-enhet](quickstart.md).
* Ett behållar register som [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio-kod](https://code.visualstudio.com/) som kon figurer ATS med [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) konfigurerat för att köra Linux-behållare.

Om du vill utveckla en IoT Edge-modul i python installerar du följande ytterligare krav på utvecklings datorn:

* [Python-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-python.python) för Visual Studio Code.
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) för att installera Python-paket (ingår vanligtvis i Python-installationen).

>[!Note]
>Se till att din `bin`-mapp finns på sökvägen för din plattform. Vanligtvis `~/.local/` för UNIX och macOS, eller `%APPDATA%\Python` i Windows.

## <a name="create-a-module-project"></a>Skapa ett modul-projekt

Följande steg skapar en IoT Edge Python-modul med Visual Studio Code och Azure IoT-verktygen.

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Skapa en python-mall för lösningar som du kan anpassa med din egen kod.

1. I Visual Studio Code väljer du **Visa**  >  **kommando-palett** för att öppna kommando paletten vs Code.

2. Ange och kör kommandot **Azure: Logga in** i kommandopaletten och följ anvisningarna för att logga in med ditt Azure-konto. Om du redan är inloggad kan du hoppa över det här steget.

3. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) på kommandopaletten. Följ anvisningarna och ange följande information för att skapa din lösning:

   | Fält | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen eller acceptera standardnamnet **EdgeSolution**. |
   | Välj modulmall | Välj **Python-modul**. |
   | Ange ett modulnamn | Ge modulen namnet **PythonModule**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen fylls i baserat på det namn du angav i föregående steg. Ersätt **localhost: 5000** med **inloggnings serverns** värde från Azure Container Registry. Du kan hämta inloggnings servern från sidan Översikt i behållar registret i Azure Portal. <br><br>Den slutliga avbildnings lagrings platsen ser ut som \<registry name\> . azurecr.io/pythonmodule. |

   ![Ange lagringsplatsen för Docker-avbildningen](./media/tutorial-python-module/repository.png)

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Miljöfilen lagrar autentiseringsuppgifterna för containerlagringsplatsen och delar dem med körningsmiljön för IoT Edge. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten.

IoT Edge-tillägget försöker hämta dina autentiseringsuppgifter för behållar registret från Azure och fylla dem i miljö filen. Kontrol lera om dina autentiseringsuppgifter redan ingår. Om inte, lägger du till dem nu:

1. Öppna **. kuvert** -filen i vs Code-Utforskaren.
2. Uppdatera fälten med det **användarnamn** och **lösenord** som du kopierade från Azure Container-registret.
3. Spara .env-filen.

### <a name="select-your-target-architecture"></a>Välj din mål arkitektur

För närvarande kan Visual Studio Code utveckla python-moduler för Linux AMD64-och Linux ARM32v7-enheter. Du måste välja vilken arkitektur du vill använda för varje lösning, eftersom behållaren har skapats och körs på olika sätt för varje arkitektur typ. Standardvärdet är Linux AMD64.

1. Öppna paletten kommando och Sök efter **Azure IoT Edge: Ange standard plattform för Edge-lösning** eller Välj gen vägs ikonen i sido fältet längst ned i fönstret.

2. I paletten kommando väljer du mål arkitekturen i listan med alternativ. I den här självstudien använder vi en virtuell Ubuntu-dator som IoT Edge enhet, så behåller standard- **amd64**.

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

Varje mall innehåller exempel kod, som tar simulerade sensor data från **SimulatedTemperatureSensor** -modulen och dirigerar den till IoT Hub. I det här avsnittet lägger du till kod som expanderar **PythonModule** för att analysera meddelanden innan de skickas.

1. I vs Code-Utforskaren öppnar du **moduler**  >  **PythonModule**  >  **main.py**.

2. Längst upp i filen **main.py** importerar du **json**-biblioteket:

    ```python
    import json
    ```

3. Lägg till variablerna **TEMPERATURE_THRESHOLD** och **TWIN_CALLBACKS** under de globala räknarna. Tröskelvärdet för temperatur är det värde som den uppmätta datortemperaturen måste överstiga för att data ska skickas till IoT-hubben.

    ```python
    # global counters
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    RECEIVED_MESSAGES = 0
    ```

4. Ersätt **input1_listener** -funktionen med följande kod:

    ```python
        # Define behavior for receiving an input message on input1
        # Because this is a filter module, we forward this message to the "output1" queue.
        async def input1_listener(module_client):
            global RECEIVED_MESSAGES
            global TEMPERATURE_THRESHOLD
            while True:
                try:
                    input_message = await module_client.receive_message_on_input("input1")  # blocking call
                    message = input_message.data
                    size = len(message)
                    message_text = message.decode('utf-8')
                    print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
                    custom_properties = input_message.custom_properties
                    print ( "    Properties: %s" % custom_properties )
                    RECEIVED_MESSAGES += 1
                    print ( "    Total messages received: %d" % RECEIVED_MESSAGES )
                    data = json.loads(message_text)
                    if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
                        custom_properties["MessageType"] = "Alert"
                        print ( "Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
                        await module_client.send_message_to_output(input_message, "output1")
                except Exception as ex:
                    print ( "Unexpected error in input1_listener: %s" % ex )

        # twin_patch_listener is invoked when the module twin's desired properties are updated.
        async def twin_patch_listener(module_client):
            global TWIN_CALLBACKS
            global TEMPERATURE_THRESHOLD
            while True:
                try:
                    data = await module_client.receive_twin_desired_properties_patch()  # blocking call
                    print( "The data in the desired properties patch was: %s" % data)
                    if "TemperatureThreshold" in data:
                        TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
                    TWIN_CALLBACKS += 1
                    print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
                except Exception as ex:
                    print ( "Unexpected error in twin_patch_listener: %s" % ex )
    ```

5. Uppdatera **lyssnarna** så att de även lyssnar på dubbla uppdateringar.

    ```python
        # Schedule task for C2D Listener
        listeners = asyncio.gather(input1_listener(module_client), twin_patch_listener(module_client))

        print ( "The sample is now waiting for messages. ")
    ```

6. Spara filen main.py.

7. I VS Code-Utforskaren öppnar du **deployment.template.js** filen i din IoT Edge lösnings arbets yta.

8. Lägg till **PythonModule**-modultvillingen i distributionsmanifestet. Infoga följande JSON-innehåll längst ned i avsnittet **moduleContent** efter **$edgeHub**-modultvillingen:

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Lägga till modultvilling till distributionsmall](./media/tutorial-python-module/module-twin.png)

9. Spara filen deployment.template.json.

## <a name="build-and-push-your-module"></a>Bygga och pusha din modul

I föregående avsnitt skapade du en IoT Edge-lösning och lagt till kod i PythonModule som filtrerar bort meddelanden där den rapporterade datorns temperatur ligger inom de acceptabla gränserna. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

1. Öppna den vs Code-integrerade terminalen genom att välja **Visa**  >  **Terminal**.

2. Logga in på Docker genom att ange följande kommando i terminalen. Logga in med användar namnet, lösen ordet och inloggnings servern från Azure Container Registry. Du kan hämta dessa värden från avsnittet **åtkomst nycklar** i registret i Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du kan få en säkerhets varning som rekommenderar att du använder `--password-stdin` . Det bästa tillvägagångs sättet rekommenderas för produktions scenarier, men det ligger utanför omfånget för den här självstudien. Mer information finns i [inloggnings referens för Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

3. I VS Code-Utforskaren högerklickar du på **deployment.template.jspå** filen och väljer **Build och push IoT Edge-lösning**.

   Kommandot build och push startar tre åtgärder. Först skapar den en ny mapp i lösningen som heter **config** och som innehåller det fullständiga distributions manifestet, och bygger ut information i distributions mal len och andra filer i lösningen. Sedan körs den `docker build` för att bygga behållar avbildningen baserat på lämpliga Dockerfile för din mål arkitektur. Sedan körs den `docker push` för att skicka avbildnings lagrings platsen till behållar registret.

   Den här processen kan ta flera minuter första gången, men går snabbare nästa gång du kör kommandona.

## <a name="deploy-modules-to-device"></a>Distribuera moduler till enhet

Använd Visual Studio Code Explorer och tillägget Azure IoT Tools för att distribuera modulfönstret till din IoT Edge-enhet. Du har redan ett distributions manifest som är för berett för ditt scenario, **deployment.amd64.js** filen i mappen config. Allt du behöver göra nu är att välja en enhet som ska ta emot distributionen.

Kontrol lera att din IoT Edges enhet är igång.

1. I Visual Studio Code Explorer, under avsnittet **Azure IoT Hub** , expanderar du **enheter** för att se listan med IoT-enheter.

2. Högerklicka på namnet för din IoT Edge-enhet och välj sedan **Create Deployment for Single Device** (Skapa distribution för en enskild enhet).

3. Välj **deployment.amd64.js** filen i mappen **config** och klicka sedan på **Välj gräns distributions manifest**. Använd inte filen deployment.template.json.

4. Under din enhet expanderar du **moduler** för att se en lista över distribuerade och aktiva moduler. Klicka på uppdateringsknappen. Du bör se den nya **PythonModule** som körs tillsammans med **SimulatedTemperatureSensor** -modulen och **$edgeAgent** och **$edgeHub**.

    Det kan ta några minuter innan modulerna startar. Den IoT Edge körnings miljön måste ta emot sitt nya distributions manifest, Hämta modulens avbildningar från container körningen och sedan starta varje ny modul.

## <a name="view-the-generated-data"></a>Visa genererade data

När du tillämpar distributionsmanifestet till din IoT Edge-enhet samlar IoT Edge-körningen in den nya distributionsinformationen och börjar köra på den. Alla moduler som körs på enheten som inte finns med i distributionsmanifestet har stoppats. Alla moduler som saknas från enheten startas.

Du kan visa statusen för din IoT Edge-enhet i avsnittet om **Azure IoT Hub-enheter** i Visual Studio Code-utforskaren. Expandera enhetsinformationen så ser du en lista med moduler som distribueras och körs.

1. I Visual Studio Code Explorer högerklickar du på namnet på din IoT Edge enhet och väljer **starta övervakning inbyggd händelse slut punkt**.

2. Visa meddelanden som kommer till IoT Hub. Det kan ta en stund innan meddelandena tas emot. Den IoT Edge enheten måste ta emot den nya distributionen och starta alla moduler. Ändringarna vi gjorde i PythonModule-koden väntar tills datorns temperatur når 25 grader innan meddelanden skickas. Den lägger också till meddelande typ **avisering** till alla meddelanden som når detta temperatur tröskelvärde.

## <a name="edit-the-module-twin"></a>Redigera modulens dubbla

Vi använde PythonModule-modulen dubbla i distributions manifestet för att ange temperatur tröskel vid 25 grader. Du kan använda modulen för att ändra funktionen utan att behöva uppdatera modulens kod.

1. I Visual Studio Code, expanderar du informationen under IoT Edge enheten för att se de moduler som körs.

2. Högerklicka på **PythonModule** och välj **Redigera modul dubbla**.

3. Hitta **TemperatureThreshold** i önskade egenskaper. Ändra värdet till en ny temperatur 5 grader till 10 grader högre än den senaste rapporterade temperaturen.

4. Spara modulens dubbla fil.

5. Högerklicka någonstans i modulens dubbla redigerings fönster och välj **Uppdatera modul dubbla**.

6. Övervaka inkommande meddelanden från enhet till molnet. Du bör se att meddelandena stannar tills det nya temperatur tröskelvärdet har uppnåtts.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du använde i den här artikeln för att undvika avgifter.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul som innehåller kod för att filtrera rådata som genereras av din IoT Edge-enhet.

Du kan fortsätta till nästa självstudier för att lära dig hur Azure IoT Edge kan hjälpa dig att distribuera Azure Cloud Services för att bearbeta och analysera data i gränsen.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Custom vision service](tutorial-deploy-custom-vision.md)