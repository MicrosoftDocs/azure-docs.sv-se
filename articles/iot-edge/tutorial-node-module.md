---
title: Självstudie utveckla Node.js modul för Linux – Azure IoT Edge | Microsoft Docs
description: Den här självstudien visar hur du skapar en IoT Edge-modul med Node.js-kod och distribuerar den till en gränsenhet
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 77e7edfba94c7b428e59dadfd9f0824608941d7a
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223259"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-using-linux-containers"></a>Självstudie: utveckla och distribuera en Node.js IoT Edge-modul med Linux-behållare

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Använd Visual Studio Code för att utveckla Node.js kod och distribuera den till en enhet som kör Azure IoT Edge.

Du kan använda IoT Edge-moduler för att distribuera kod som implementerar din affärslogik direkt till dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. Du använder den IoT Edge enheten som du skapade i snabb starterna. I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Använda Visual Studio Code för att skapa en IoT Edge Node.js-modul
> * Använda Visual Studio Code och Docker för att skapa en Docker-avbildning och publicera den till ditt register
> * Distribuera modulen till din IoT Edge-enhet
> * Visa genererade data

IoT Edge-modulen som du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys är användbar för att minska mängden data som kommuniceras till och lagras i molnet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien visar hur du utvecklar en modul i **Node.js** att använda **Visual Studio Code** och hur du distribuerar den till en IoT Edge enhet.

IoT Edge stöder inte Node.js moduler som använder Windows-behållare.

Använd följande tabell för att förstå alternativen för att utveckla och distribuera Node.js moduler:

| Node.js | Visuell Studio-kod | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Använda VS Code för Node.js moduler på Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Använda VS Code för Node.js moduler på Linux-ARM32](./media/tutorial-c-module/green-check.png) |  |

Innan du påbörjar den här självstudien bör du ha gått igenom den föregående själv studie kursen för att konfigurera utvecklings miljön för utveckling av Linux-behållare: [utveckla IoT Edge moduler med hjälp av Linux-behållare](tutorial-develop-for-linux.md). När du har slutfört den här självstudien bör du ha följande krav på plats:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En enhet som kör Azure IoT Edge. Du kan använda snabb starterna för att konfigurera en [Linux-enhet](quickstart-linux.md) eller [Windows-enhet](quickstart.md).
* Ett behållar register som [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio-kod](https://code.visualstudio.com/) som kon figurer ATS med [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) konfigurerat för att köra Linux-behållare.

Om du vill utveckla en IoT Edge-modul i Node.js installerar du följande ytterligare krav på utvecklings datorn:

* [Node.js och NPM](https://nodejs.org). npm-paketet distribueras med Node.js, vilket innebär att npm installeras automatiskt på din dator när du laddar ned Node.js.

## <a name="create-a-module-project"></a>Skapa ett modul-projekt

Följande steg visar hur du skapar en IoT Edge Node.js-modul med Visual Studio Code och Azure IoT-verktyg.

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Använd **npm** för att skapa en Node.js-lösningsmall som du kan utgå ifrån.

1. I Visual Studio Code väljer du **Visa**  >  **integrerad Terminal** för att öppna den integrerade vs Code-terminalen.

2. Installera **yeoman** och generatorn för Node.js Azure IoT Edge-modulen genom att köra följande kommando i den integrerade terminalen:

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. Välj **Visa**  >  **kommando palett** för att öppna kommando paletten vs Code.

4. Ange och kör kommandot **Azure: Logga in** i kommandopaletten och följ anvisningarna för att logga in med ditt Azure-konto. Om du redan har loggat in kan du hoppa över det här steget.

5. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) i kommandopaletten. Skapa lösningen genom att följ anvisningarna på kommandopaletten.

   | Fält | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen eller acceptera standardnamnet **EdgeSolution**. |
   | Välj modulmall | Välj **Node.js-modulen**. |
   | Ange ett modulnamn | Ge modulen namnet **NodeModule**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen fylls i baserat på det namn du angav i föregående steg. Ersätt **localhost: 5000** med **inloggnings serverns** värde från Azure Container Registry. Du kan hämta inloggnings servern från sidan Översikt i behållar registret i Azure Portal. <br><br>Den slutliga avbildnings lagrings platsen ser ut som \<registry name\> . azurecr.io/nodemodule. |

   ![Ange lagringsplatsen för Docker-avbildningen](./media/tutorial-node-module/repository.png)

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Miljöfilen lagrar autentiseringsuppgifterna för containerlagringsplatsen och delar dem med IoT Edge-körningsmiljön. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten.

IoT Edge-tillägget försöker hämta dina autentiseringsuppgifter för behållar registret från Azure och fylla dem i miljö filen. Kontrol lera om dina autentiseringsuppgifter redan ingår. Om inte, lägger du till dem nu:

1. Öppna **. kuvert** -filen i vs Code-Utforskaren.
2. Uppdatera fälten med det **användarnamn** och **lösenord** som du kopierade från Azure Container-registret.
3. Spara den här filen.

### <a name="select-your-target-architecture"></a>Välj din mål arkitektur

För närvarande kan Visual Studio Code utveckla Node.js moduler för Linux AMD64-och Linux ARM32v7-enheter. Du måste välja vilken arkitektur du vill använda för varje lösning, eftersom behållaren har skapats och körs på olika sätt för varje arkitektur typ. Standardvärdet är Linux AMD64.

1. Öppna paletten kommando och Sök efter **Azure IoT Edge: Ange standard plattform för Edge-lösning** eller Välj gen vägs ikonen i sido fältet längst ned i fönstret.

2. I paletten kommando väljer du mål arkitekturen i listan med alternativ. I den här självstudien använder vi en virtuell Ubuntu-dator som IoT Edge enhet, så behåller standard- **amd64**.

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

Varje mall levereras med exempel kod inkluderad, som tar simulerade sensor data från **SimulatedTemperatureSensor** -modulen och dirigerar den till IoT Hub. I det här avsnittet lägger du till kod för att göra så att NodeModule analyserar meddelandena innan de skickas.

1. I vs Code-Utforskaren öppnar du **moduler**  >  **NodeModule**  >  **app.js**.

2. Lägg till en tröskelvariabel för temperatur under nodmoduler. Tröskelvärdet för temperatur är det värde som den uppmätta temperaturen måste överstiga för att data ska skickas till IoT Hub.

    ```javascript
    var temperatureThreshold = 25;
    ```

3. Ersätt hela funktionen `PipeMessage` med funktionen `FilterMessage`.

    ```javascript
    // This function filters out messages that report temperatures below the temperature threshold.
    // It also adds the MessageType property to the message with the value set to Alert.
    function filterMessage(client, inputName, msg) {
        client.complete(msg, printResultFor('Receiving message'));
        if (inputName === 'input1') {
            var message = msg.getBytes().toString('utf8');
            var messageBody = JSON.parse(message);
            if (messageBody && messageBody.machine && messageBody.machine.temperature && messageBody.machine.temperature > temperatureThreshold) {
                console.log(`Machine temperature ${messageBody.machine.temperature} exceeds threshold ${temperatureThreshold}`);
                var outputMsg = new Message(message);
                outputMsg.properties.add('MessageType', 'Alert');
                client.sendOutputEvent('output1', outputMsg, printResultFor('Sending received message'));
            }
        }
    }

    ```

4. Ersätt funktionsnamnet `pipeMessage` med `filterMessage` i funktionen `client.on()`.

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

5. Kopiera följande kodfragment i motringningen för funktionen `client.open()` efter `client.on()` i instruktionen `else`. Funktionen anropas när önskade egenskaper uppdateras.

    ```javascript
    client.getTwin(function (err, twin) {
        if (err) {
            console.error('Error getting twin: ' + err.message);
        } else {
            twin.on('properties.desired', function(delta) {
                if (delta.TemperatureThreshold) {
                    temperatureThreshold = delta.TemperatureThreshold;
                }
            });
        }
    });
    ```

6. Spara filen app.js.

7. I VS Code-Utforskaren öppnar du **deployment.template.js** filen i din IoT Edge lösnings arbets yta.

8. Lägg till NodeModule-modultvillingen till distributionsmanifestet. Infoga följande JSON-innehåll längst ned i avsnittet `moduleContent` efter `$edgeHub`-modultvillingen:

   ```json
     "NodeModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Lägga till modultvilling till distributionsmall](./media/tutorial-node-module/module-twin.png)

9. Spara filen deployment.template.json.

## <a name="build-and-push-your-module"></a>Bygga och pusha din modul

I föregående avsnitt skapade du en IoT Edge-lösning och lagt till kod i NodeModule som filtrerar bort meddelanden där den rapporterade datorns temperatur ligger inom de acceptabla gränserna. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

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

4. Under din enhet expanderar du **moduler** för att se en lista över distribuerade och aktiva moduler. Klicka på uppdateringsknappen. Du bör se den nya **NodeModule** som körs tillsammans med **SimulatedTemperatureSensor** -modulen och **$edgeAgent** och **$edgeHub**.

    Det kan ta några minuter innan modulerna startar. Den IoT Edge körnings miljön måste ta emot sitt nya distributions manifest, Hämta modulens avbildningar från container körningen och sedan starta varje ny modul.

## <a name="view-the-generated-data"></a>Visa genererade data

När du tillämpar distributionsmanifestet till din IoT Edge-enhet samlar IoT Edge-körningen in den nya distributionsinformationen och börjar köra på den. Alla moduler som körs på enheten som inte finns med i distributionsmanifestet har stoppats. Alla moduler som saknas från enheten startas.

Du kan visa statusen för din IoT Edge-enhet i avsnittet om **Azure IoT Hub-enheter** i Visual Studio Code-utforskaren. Expandera enhetsinformationen så ser du en lista med moduler som distribueras och körs.

1. I Visual Studio Code Explorer högerklickar du på namnet på din IoT Edge enhet och väljer **starta övervakning inbyggd händelse slut punkt**.

2. Visa meddelanden som kommer till IoT Hub. Det kan ta en stund innan meddelandena tas emot. Den IoT Edge enheten måste ta emot den nya distributionen och starta alla moduler. Ändringarna vi gjorde i NodeModule-koden väntar tills datorns temperatur når 25 grader innan meddelanden skickas. Den lägger också till meddelande typ **avisering** till alla meddelanden som når detta temperatur tröskelvärde.

## <a name="edit-the-module-twin"></a>Redigera modulens dubbla

Vi använde NodeModule-modulen dubbla i distributions manifestet för att ange temperatur tröskel vid 25 grader. Du kan använda modulen för att ändra funktionen utan att behöva uppdatera modulens kod.

1. I Visual Studio Code, expanderar du informationen under IoT Edge enheten för att se de moduler som körs.

2. Högerklicka på **NodeModule** och välj **Redigera modul dubbla**.

3. Hitta **TemperatureThreshold** i önskade egenskaper. Ändra värdet till en ny temperatur 5 grader till 10 grader högre än den senaste rapporterade temperaturen.

4. Spara modulens dubbla fil.

5. Högerklicka någonstans i modulens dubbla redigerings fönster och välj **Uppdatera modul dubbla**.

6. Övervaka inkommande meddelanden från enhet till molnet. Du bör se att meddelandena stannar tills det nya temperatur tröskelvärdet har uppnåtts.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du har skapat i den här artikeln för att därigenom undvika kostnader.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul som innehåller kod för att filtrera rådata som genereras av din IoT Edge-enhet.

Du kan fortsätta till nästa självstudier för att lära dig hur Azure IoT Edge kan hjälpa dig att distribuera Azure Cloud Services för att bearbeta och analysera data i gränsen.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Custom vision service](tutorial-deploy-custom-vision.md)