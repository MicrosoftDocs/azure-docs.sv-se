---
title: Självstudie – utveckla C#-modul för Windows med hjälp av Azure IoT Edge
description: Den här självstudien visar hur du skapar en IoT Edge-modul med C#-kod och distribuerar den till en Windows IoT Edge-enhet.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/03/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, amqp, devx-track-csharp
ms.openlocfilehash: 46d3a9e961be0717aba75c1f5845b97b52092510
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931970"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Självstudie: utveckla en C# IoT Edge-modul för Windows-enheter

Använd Visual Studio för att utveckla C#-kod och distribuera den till en Windows-enhet som kör Azure IoT Edge.

Du kan använda Azure IoT Edge-moduler till att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Använd Visual Studio för att skapa en IoT Edge-modul som baseras på C# SDK.
> * Använd Visual Studio och Docker för att skapa en Docker-avbildning och publicera den i registret.
> * distribuera modulen till din IoT Edge-enhet
> * visa genererade data.

IoT Edge-modulen du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys vid kanten är användbar när du vill minska mängden data som skickas till och lagras i molnet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Den här självstudien visar hur du utvecklar en modul i **C#** med hjälp av **Visual Studio 2019** och distribuerar den till en **Windows-enhet**. Om du utvecklar moduler för Linux-enheter går du till [utveckla en C# IoT Edge modul för Linux-enheter](tutorial-csharp-module.md) i stället.

Använd följande tabell för att förstå alternativen för att utveckla och distribuera C#-moduler till Windows-enheter:

| C# | Visuell Studio-kod | Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Windows AMD64-utveckling** | ![Utveckla C#-moduler för WinAMD64 i VS Code](./media/tutorial-c-module/green-check.png) | ![Utveckla C#-moduler för WinAMD64 i Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Windows AMD64-felsökning** |   | ![Felsöka C#-moduler för WinAMD64 i Visual Studio](./media/tutorial-c-module/green-check.png) |

Innan du påbörjar den här självstudien bör du ha gått igenom föregående självstudie för att konfigurera din utvecklings miljö, [utveckla en IoT Edge modul för en Windows-enhet](tutorial-develop-for-windows.md). När du har slutfört den här självstudien bör du redan ha följande krav:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En [Windows-enhet som kör Azure IoT Edge](quickstart.md).
* Ett behållar register som [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio 2019](/visualstudio/install/install-visual-studio) konfigurerat med [Azure IoT Edge tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) -tillägget.
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) konfigurerat för att köra Windows-behållare.

> [!TIP]
> Om du använder Visual Studio 2017 (version 15,7 eller senare) kan du ladda ned och installera [Azure IoT Edge verktyg](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) för vs 2017 från Visual Studio Marketplace

## <a name="create-a-module-project"></a>Skapa ett modul-projekt

Följande steg skapar ett IoT Edge module-projekt med hjälp av Visual Studio och tillägget Azure IoT Edge-verktyg. När du har skapat en Project-mall lägger du till ny kod så att modulen filtrerar ut meddelanden utifrån deras rapporterade egenskaper.

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Azure IoT Edge-verktygen tillhandahåller projektmallar för alla språk som stöds IoT Edge modul i Visual Studio. Dessa mallar har alla filer och all kod som du behöver för att distribuera en fungerande modul för att testa IoT Edge, eller ge dig en start punkt för att anpassa mallen med din egen affärs logik.

1. Starta Visual Studio 2019 och välj **Skapa nytt projekt**.

2. Sök efter **IoT Edge** och välj **Azure IoT Edge-projektet (Windows amd64)** . Klicka på **Nästa**.

   ![Skapa ett nytt Azure IoT Edge-projekt](./media/tutorial-csharp-module-windows/new-project.png)

3. Byt namn på projektet och lösningen till något beskrivande som **CSharpTutorialApp**. Skapa projektet genom att klicka på **skapa** .

   ![Konfigurera ett nytt Azure IoT Edge projekt](./media/tutorial-csharp-module-windows/configure-project.png)

4. Konfigurera ditt projekt med följande värden:

   | Fält | Värde |
   | ----- | ----- |
   | Välja en mall | Välj **C#-modul**. |
   | Modulnamn projekt namn | Ge modulen namnet **CSharpModule**. |
   | Docker-avbildningens lagrings plats | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Behållar avbildningen fylls i automatiskt från värdet för modulens projekt namn. Ersätt **localhost: 5000** med **inloggnings serverns** värde från Azure Container Registry. Du kan hämta inloggnings servern från sidan Översikt i behållar registret i Azure Portal. <br><br> Den slutliga avbildnings lagrings platsen ser ut som \<registry name\> . azurecr.io/csharpmodule. |

   ![Konfigurera ditt projekt för mål enheten, modultypen och behållar registret](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. Välj **Lägg till** för att skapa projektet.

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Distributions manifestet delar autentiseringsuppgifterna för behållar registret med IoT Edge Runtime. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten. Använd autentiseringsuppgifterna från avsnittet **åtkomst nycklar** i Azure Container Registry.

1. Öppna **deployment.template.jspå** filen i Visual Studio-Solution Explorer.

2. Hitta egenskapen **registryCredentials** i önskade egenskaper för $edgeAgent. Den bör ha en ifylld registrerings adress från den information som du angav när du skapade projektet, och fälten username och Password måste innehålla variabel namn. Exempel:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

3. Öppna **. kuvert** -filen i din modul-lösning. (Den är dold som standard i Solution Explorer, så du kan behöva välja knappen **Visa alla filer** för att visa den.) . Kuvert-filen ska innehålla samma användar namn och lösen ord som du såg i deployment.template.jsi filen.

4. Lägg till värdena för **användar namn** och **lösen ord** från Azure Container Registry.

5. Spara ändringarna i. kuvert-filen.

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

Koden för standardmodulen tar emot meddelanden i en indatakö och skickar dem vidare via en utgående kö. Nu ska vi lägga till ytterligare kod så att modulen bearbetar meddelandena på gränsen innan de vidarebefordrar dem till IoT Hub. Uppdatera modulen så att den analyserar temperatur data i varje meddelande, och skickar endast meddelandet till IoT Hub om temperaturen överskrider ett visst tröskelvärde.

1. Öppna **CSharpModule**  >  **program.cs** i Visual Studio.

2. Överst i **CSharpModule**-namnrymden läger du till tre **using**-instruktioner för typer som ska användas senare:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Lägg till variabeln **temperatureThreshold** i **program** klassen efter Counter-variabeln. Variabeln temperatureThreshold anger det värde som den uppmätta temperaturen måste överskrida för att data ska skickas till IoT-hubben.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Lägg till **MessageBody**-, **maskin**-och **omgivande** klasser i **program** klassen efter variabel deklarationerna. Dessa klasser definierar det förväntade schemat för brödtexten i inkommande meddelanden.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
        public double temperature {get; set;}
        public double pressure {get; set;}
    }
    class Ambient
    {
        public double temperature {get; set;}
        public int humidity {get; set;}
    }
    ```

5. Hitta **init** -metoden. Med den här metoden skapas och konfigureras ett **ModuleClient** -objekt, vilket gör att modulen kan ansluta till den lokala Azure IoT Edge runtime för att skicka och ta emot meddelanden. Koden registrerar också ett återanrop för att ta emot meddelanden från en IoT Edge hubb via **INPUT1** -slutpunkten.

   Ersätt hela init-metoden med följande kod:

   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```

   Den uppdaterade init-metoden konfigurerar fortfarande anslutningen till IoT Edge runtime med ModuleClient, men lägger också till nya funktioner. Den läser modulens dubbla önskade egenskaper för att hämta **temperatureThreshold** -värdet. Sedan skapar den ett återanrop som lyssnar efter eventuella framtida uppdateringar av modulens dubbla egenskaper. Med det här återanropet kan du uppdatera temperatur tröskeln i modulen dubbelt från en fjärrdator, och ändringarna kommer att ingå i modulen.

   Den uppdaterade init-metoden ändrar även den befintliga **SetInputMessageHandlerAsync** -metoden. I exempel koden bearbetas inkommande meddelanden på *INPUT1* med funktionen *PipeMessage* , men vi vill ändra det för att använda funktionen *FilterMessages* som vi skapar i följande steg.

6. Lägg till en ny **onDesiredPropertiesUpdate** -Metod i **program** klassen. Metoden tar emot uppdateringar av önskade egenskaper från modultvillingen och uppdaterar variabeln **temperatureThreshold** som ska matchas. Alla moduler har en egen modultvilling, vilket innebär att du kan konfigurera den kod som körs i en modul direkt från molnet.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

7. Ta bort metoden Sample **PipeMessage** och ersätt den med en ny **FilterMessages** -metod. Den här metoden anropas när modulen tar emot ett meddelande från IoT Edge-hubben. Den filtrerar ut meddelanden som rapporterar temperaturer under temperaturtröskelvärdet som angetts via modultvillingen. Den lägger också till egenskapen **MessageType** i meddelandet med värdet angivet som **Avisering**.

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                using(var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

8. Spara filen Program.cs.

9. Öppna filen **deployment.template.js** i IoT Edge-lösningen. Den här filen talar om för IoT Edge-agenten vilka moduler som ska distribueras, i det här fallet **SimulatedTemperatureSensor** och **CSharpModule** och talar om för IoT Edge Hub hur meddelanden ska dirigeras mellan dem.

10. Lägg till **CSharpModule**-modultvillingen till distributionsmanifestet. Infoga följande JSON-innehåll längst ned i avsnittet **modulesContent** efter **$edgeHub**-modultvillingen:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Lägga till modultvilling till distributionsmall](./media/tutorial-csharp-module-windows/module-twin.png)

11. Spara filen deployment.template.json.

## <a name="build-and-push-your-module"></a>Bygga och pusha din modul

I föregående avsnitt skapade du en IoT Edge-lösning och lade till kod i **CSharpModule** som filtrerar bort meddelanden där den rapporterade maskintemperaturen ligger under det godkända tröskelvärdet. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

1. Använd följande kommando för att logga in på Docker på din utvecklings dator. Använd användar namnet, lösen ordet och inloggnings servern från Azure Container Registry. Du kan hämta dessa värden från avsnittet **åtkomst nycklar** i registret i Azure Portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du kan få en säkerhets varning som rekommenderar att du använder `--password-stdin` . Det bästa tillvägagångs sättet rekommenderas för produktions scenarier, men det ligger utanför omfånget för den här självstudien. Mer information finns i [inloggnings referens för Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

2. Högerklicka på det projekt namn som du vill bygga i Visual Studio-Solution Explorer. Standard namnet är **AzureIotEdgeApp1** och eftersom du skapar en Windows-modul bör tillägget vara **Windows. amd64**.

3. Välj **build-och push-IoT Edge moduler**.

   Kommandot build och push startar tre åtgärder. Först skapar den en ny mapp i lösningen som heter **config** och som innehåller det fullständiga distributions manifestet, och bygger ut information i distributions mal len och andra filer i lösningen. Sedan körs den `docker build` för att bygga behållar avbildningen baserat på lämpliga Dockerfile för din mål arkitektur. Sedan körs den `docker push` för att skicka avbildnings lagrings platsen till behållar registret.

   Den här processen kan ta flera minuter första gången, men går snabbare nästa gång du kör kommandona.

## <a name="deploy-modules-to-device"></a>Distribuera moduler till enhet

Använd Visual Studio Cloud Explorer och tillägget Azure IoT Edge Tools för att distribuera modulfönstret till din IoT Edge-enhet. Du har redan ett distributions manifest som är för berett för ditt scenario, **deployment.windows-amd64.js** filen i mappen config. Allt du behöver göra nu är att välja en enhet som ska ta emot distributionen.

Kontrol lera att din IoT Edges enhet är igång.

1. Expandera resurserna i Visual Studio Cloud Explorer för att se din lista över IoT-enheter.

2. Högerklicka på namnet på den IoT Edge enhet som du vill ta emot distributionen av.

3. Välj **skapa distribution**.

4. I Utforskaren väljer du **deployment.windows-amd64.js** filen i mappen config i din lösning.

5. Uppdatera Cloud Explorer för att se de distribuerade modulerna som visas på din enhet.

## <a name="view-generated-data"></a>Visa genererade data

När du tillämpar distributionsmanifestet till din IoT Edge-enhet samlar IoT Edge-körningen in den nya distributionsinformationen och börjar köra på den. Alla moduler som körs på enheten som inte finns med i distributionsmanifestet har stoppats. Alla moduler som saknas från enheten startas.

Du kan använda tillägget IoT Edge-verktyg för att visa meddelanden när de kommer till din IoT Hub.

1. I Visual Studio Cloud Explorer väljer du namnet på din IoT Edge-enhet.

2. I listan **åtgärder** väljer du **starta övervakning inbyggd händelse slut punkt**.

3. Visa meddelanden som kommer till IoT Hub. Det kan ta en stund innan meddelandena tas emot, eftersom ändringarna vi gjorde i CSharpModule-koden väntar tills datorns temperatur når 25 grader innan meddelanden skickas. Den lägger också till meddelande typ **avisering** till alla meddelanden som når detta temperatur tröskelvärde.

   ![Visa meddelanden som anländer på IoT Hub](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Redigera modulens dubbla

Vi använde CSharpModule-modulen för att ange temperatur tröskeln vid 25 grader. Du kan använda modulen för att ändra funktionen utan att behöva uppdatera modulens kod.

1. Öppna filen **deployment.windows-amd64.js** i Visual Studio. (Inte filen Deployment. template. Om du inte ser distributions manifestet i konfigurations filen i Solution Explorer väljer du ikonen **Visa alla filer** i Explorer-verktygsfältet.)

2. Hitta CSharpModule och ändra värdet för parametern **temperatureThreshold** till en ny temperatur på 5 grader till 10 grader högre än den senaste rapporterade temperaturen.

3. Spara **deployment.windows-amd64.jspå** filen.

4. Följ distributions stegen igen för att tillämpa det uppdaterade distributions manifestet på enheten.

5. Övervaka inkommande meddelanden från enhet till molnet. Du bör se att meddelandena stannar tills det nya temperatur tröskelvärdet har uppnåtts.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du använde i den här artikeln för att undvika avgifter.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul med kod för att filtrera rådata som genereras av din IoT Edge-enhet.

Du kan fortsätta till nästa självstudier för att lära dig hur Azure IoT Edge kan hjälpa dig att distribuera Azure Cloud Services för att bearbeta och analysera data i gränsen.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Custom vision service](tutorial-deploy-custom-vision.md)
