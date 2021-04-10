---
title: Självstudie – utveckla C#-moduler för Windows med hjälp av Azure IoT Edge
description: Den här självstudien visar hur du skapar IoT Edge moduler med C#-kod och distribuerar dem till Windows-enheter som kör IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/03/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, amqp, devx-track-csharp
ms.openlocfilehash: 4e01b1ca9a3858ff31ad9b5da1d1159209c44330
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103464077"
---
# <a name="tutorial-develop-c-iot-edge-modules-using-windows-containers"></a>Självstudie: utveckla C# IoT Edge moduler med Windows-behållare

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Den här artikeln visar hur du använder Visual Studio för att utveckla C#-kod och distribuera den till en Windows-enhet som kör Azure IoT Edge.

>[!NOTE]
>IoT Edge 1,1 LTS är den sista versions kanal som stöder Windows-behållare. Från och med version 1,2 stöds inte Windows-behållare. Överväg att använda eller flytta till [IoT Edge för Linux i Windows](iot-edge-for-linux-on-windows.md) för att köra IoT Edge på Windows-enheter.

Du kan använda Azure IoT Edge moduler för att distribuera kod som implementerar affärs logiken direkt i dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Använd Visual Studio för att skapa en IoT Edge-modul som baseras på C# SDK.
> * Använd Visual Studio och Docker för att skapa en Docker-avbildning och publicera den i registret.
> * distribuera modulen till din IoT Edge-enhet
> * visa genererade data.

IoT Edge-modulen du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Modulen skickar meddelande överström endast om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys vid kanten är användbar när du vill minska mängden data som skickas till och lagras i molnet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien visar hur du utvecklar en modul i C# med hjälp av Visual Studio 2019 och sedan distribuerar den till en Windows-enhet. Om du utvecklar moduler med hjälp av Linux-behållare går du till [utveckla C# IoT Edge moduler med hjälp av Linux-behållare](tutorial-csharp-module.md) i stället.

För att förstå alternativen för att utveckla och distribuera C#-moduler med hjälp av Windows-behållare, se följande tabell:

| C# | Visual &nbsp; Studio- &nbsp; kod | Visual Studio 2017 &nbsp; och &nbsp; 2019 |
| -- | :------------------: | :------------------: |
| Windows AMD64-utveckling | ![Utveckla C#-moduler för WinAMD64 i Visual Studio Code](./media/tutorial-c-module/green-check.png) | ![Utveckla C#-moduler för WinAMD64 i Visual Studio](./media/tutorial-c-module/green-check.png) |
| Windows AMD64-felsökning |   | ![Felsöka C#-moduler för WinAMD64 i Visual Studio](./media/tutorial-c-module/green-check.png) |

Innan du påbörjar den här självstudien ställer du in utvecklings miljön genom att följa anvisningarna i självstudien [utveckla IoT Edge moduler med hjälp av Windows-behållare](tutorial-develop-for-windows.md) . När du har slutfört den kommer din miljö att innehålla följande krav:

* En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) (kostnads fri) eller standard nivå i Azure.
* En [Windows-enhet som kör Azure IoT Edge](how-to-install-iot-edge-windows-on-windows.md).
* Ett behållar register, till exempel [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio 2019](/visualstudio/install/install-visual-studio), konfigurerat med [Azure IoT Edge tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) -tillägget.
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/), konfigurerat för att köra Windows-behållare.

> [!TIP]
> Om du använder Visual Studio 2017 (version 15,7 eller senare) kan du hämta och installera Azure IoT Edge verktyg för Visual Studio 2017 från [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

## <a name="create-a-module-project"></a>Skapa ett modul-projekt

I det här avsnittet skapar du ett IoT Edge module-projekt med hjälp av Visual Studio och tillägget Azure IoT Edge-verktyg. När du har skapat en projektmall lägger du till ny kod så att modulen filtrerar ut meddelanden baserat på deras rapporterade egenskaper.

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Azure IoT Edge-verktyg innehåller projektmallar för alla språk som stöds IoT Edge modul i Visual Studio. Dessa mallar har alla filer och all kod som du behöver för att distribuera en fungerande modul för testning IoT Edge. De kan också ge dig en utgångs punkt för att anpassa dem till din egen affärs logik.

1. Öppna Visual Studio 2019 och välj sedan **Skapa nytt projekt**.

1. I fönstret **skapa ett nytt projekt** söker du efter **IoT Edge** och i resultat listan väljer du **Azure IoT Edge (Windows amd64)** -projektet.

   ![Skärm bild av fönstret IoT Edge skapa ett nytt projekt.](./media/tutorial-csharp-module-windows/new-project.png)

1. Välj **Nästa**.

    Fönstret **Konfigurera ditt nya projekt** öppnas.

   ![Skärm bild av fönstret "Konfigurera ditt nya projekt".](./media/tutorial-csharp-module-windows/configure-project.png)

1. I fönstret **Konfigurera ditt nya projekt** byter du namn på projektet och lösningen till något mer beskrivande, till exempel **CSharpTutorialApp**. 

1. Välj **Skapa** för att skapa projektet.

   Fönstret **Lägg till modul** öppnas.

   ![Skärm bild av fönstret Lägg till modul för att konfigurera ditt projekt.](./media/tutorial-csharp-module-windows/add-application-and-module.png)

1. Gör följande på sidan **Konfigurera ditt nya projekt** :

   a. I den vänstra rutan väljer du mallen **C#-modul** .  
   b. I rutan **Modulnamn** anger du **CSharpModule**.  
   c. I rutan **lagrings webb adress** ersätter du **localhost: 5000** med **inloggnings serverns** värde från Azure Container Registry i följande format: `<registry name>.azurecr.io/csharpmodule`

    > [!NOTE]
    > En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Behållar avbildningen är förifylld från modulens projekt namn värde.  Du kan hämta inloggnings servern från sidan Översikt i behållar registret i Azure Portal.

1. Välj **Lägg till** för att skapa projektet.

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Distributions manifestet delar autentiseringsuppgifterna för behållar registret med IoT Edge Runtime. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten. Använd autentiseringsuppgifterna från avsnittet **åtkomst nycklar** i Azure Container Registry.

1. I Visual Studio Solution Explorer öppnar du *deployment.template.jspå* filen.

1. Leta efter egenskapen **registryCredentials** i önskade egenskaper för $edgeAgent. Register adressen för egenskapen ska fyllas i med den information som du angav när du skapade projektet. Fälten username och Password måste innehålla variabel namn. Exempel:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Öppna miljö filen (miljö) i modulen lösning. Som standard är filen dold i Solution Explorer, så du kan behöva välja knappen **Visa alla filer** för att visa den. KUVERT filen ska innehålla samma användar namn och lösen ord som du såg i *deployment.template.jsi* filen.

1. Lägg till värdena för **användar namn** och **lösen ord** från Azure Container Registry.

1. Spara ändringarna i kuvert filen.

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

Koden för standardmodulen tar emot meddelanden i en indatakö och skickar dem vidare via en utgående kö. Nu ska vi lägga till ytterligare kod så att modulen bearbetar meddelandena på gränsen innan de vidarebefordrar dem till din IoT-hubb. Uppdatera modulen så att den analyserar temperatur data i varje meddelande och skickar meddelandet till IoT Hub endast om temperaturen överskrider ett visst tröskelvärde.

1. I Visual Studio väljer du **CSharpModule**  >  **program. cs**.

1. Överst i **CSharpModule**-namnrymden läger du till tre **using**-instruktioner för typer som ska användas senare:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

1. Lägg till variabeln **temperatureThreshold** i **program** klassen efter Counter-variabeln. Variabeln temperatureThreshold anger det värde som den uppmätta temperaturen måste överskrida för att data ska skickas till IoT-hubben.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

1. Lägg till **MessageBody**-, **maskin**-och **omgivande** klasser i **program** klassen efter variabel deklarationerna. Dessa klasser definierar det förväntade schemat för brödtexten i inkommande meddelanden.

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

1. Sök efter metoden **init** . Med den här metoden skapas och konfigureras ett **ModuleClient** -objekt, vilket gör att modulen kan ansluta till den lokala Azure IoT Edge runtime för att skicka och ta emot meddelanden. Koden registrerar också ett återanrop för att ta emot meddelanden från en IoT Edge hubb via **INPUT1** -slutpunkten.

   Ersätt hela init-metoden med följande kod:

   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime.
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties.
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```

   Den uppdaterade init-metoden konfigurerar fortfarande anslutningen till IoT Edge runtime med ModuleClient, men den lägger också till nya funktioner. Den läser modulens dubbla önskade egenskaper för att hämta **temperatureThreshold** -värdet. Det skapar sedan ett återanrop som lyssnar efter eventuella framtida uppdateringar av modulens egenskaper med dubbla. Med det här återanropet kan du uppdatera temperatur tröskeln i modulen dubbelt från en fjärrdator, och ändringarna kommer att ingå i modulen.

   Den uppdaterade init-metoden ändrar även den befintliga **SetInputMessageHandlerAsync** -metoden. I exempel koden bearbetas inkommande meddelanden på *INPUT1* med funktionen *PipeMessage* , men vi vill ändra det för att använda funktionen *FilterMessages* som vi skapar i följande steg.

1. Lägg till en ny **onDesiredPropertiesUpdate** -Metod i **program** klassen. Den här metoden tar emot uppdateringar av de önskade egenskaperna från modulen, och uppdaterar **temperatureThreshold** -variabeln så att den matchar. Alla moduler har en egen modultvilling, vilket innebär att du kan konfigurera den kod som körs i en modul direkt från molnet.

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

1. Ta bort metoden Sample **PipeMessage** och ersätt den med en ny **FilterMessages** -metod. Den här metoden anropas när modulen tar emot ett meddelande från IoT Edge-hubben. Den filtrerar bort meddelanden som rapporterar temperaturer under temperatur tröskeln som har ställts in via modul två. Metoden lägger också till egenskapen **MessageType** i meddelandet med värdet inställt på **Varning**.

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

1. Spara *Program.cs*-filen.

1. Öppna filen *deployment.template.js* i IoT Edge-lösningen. Den här filen talar om för IoT Edge-agenten vilka moduler som ska distribueras och talar om för IoT Edge Hub hur meddelanden ska skickas mellan dem. De moduler som ska distribueras är **SimulatedTemperatureSensor** och **CSharpModule**.

1. Lägg till **CSharpModule**-modultvillingen till distributionsmanifestet. Infoga följande JSON-innehåll längst ned i `modulesContent` avsnittet efter **$edgeHub** modul:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Skärm bild som visar den modul som har lagts till i distributions mal len.](./media/tutorial-csharp-module-windows/module-twin.png)

1. Spara filen *deployment.template.json*.

## <a name="build-and-push-your-module"></a>Bygga och pusha din modul

I föregående avsnitt skapade du en IoT Edge-lösning och lagt till kod i **CSharpModule** för att filtrera bort meddelanden där den rapporterade dator temperaturen är under det acceptabla tröskelvärdet. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

### <a name="sign-in-to-docker"></a>Logga in på Docker

1. Använd följande kommando för att logga in på Docker på din utvecklings dator. Använd användar namnet, lösen ordet och inloggnings servern från Azure Container Registry. Du kan hämta dessa värden från avsnittet **åtkomst nycklar** i registret i Azure Portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du kan få en säkerhets varning som rekommenderar användning av `--password-stdin` . Även om vi rekommenderar detta som bästa praxis för produktions scenarier, är det utanför den här självstudien. Mer information finns i [inloggnings referens för Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Bygg och skicka

1. I Visual Studio Solution Explorer högerklickar du på namnet på det projekt som du vill bygga. Standard namnet är **AzureIotEdgeApp1** och eftersom du skapar en Windows-modul bör tillägget vara **Windows. amd64**.

1. Välj **build-och push-IoT Edge moduler**.

   Kommandot build och push startar tre åtgärder:
   * Först skapas en ny mapp i lösningen med namnet *config*, som innehåller det fullständiga distributions manifestet. Den bygger på information i distributions mal len och andra lösningsfiler. 
   * Sedan körs den `docker build` för att bygga behållar avbildningen baserat på lämpliga Dockerfile för din mål arkitektur. 
   * Slutligen körs den `docker push` för att skicka avbildnings lagrings platsen till behållar registret.

   Den här processen kan ta flera minuter första gången, men den går snabbare nästa gång du kör kommandona.

## <a name="deploy-modules-to-the-device"></a>Distribuera moduler till enheten

Använd Visual Studio Cloud Explorer och tillägget Azure IoT Edge Tools för att distribuera modulfönstret till din IoT Edge-enhet. Du har redan förberett ett distributions manifest för ditt scenario, *deployment.windows-amd64.js* filen i mappen *config* . Allt du behöver göra nu är att välja en enhet som ska ta emot distributionen.

Kontrol lera att din IoT Edges enhet är igång.

1. Expandera resurserna i Visual Studio Cloud Explorer för att visa din lista över IoT-enheter.

1. Högerklicka på namnet på den IoT Edge enhet som du vill ta emot distributionen av.

1. Välj **skapa distribution**.

1. I Visual Studio Utforskaren väljer du *deployment.windows-amd64.js* filen i mappen *config* i din lösning.

1. Uppdatera Cloud Explorer för att visa de distribuerade modulerna som visas under enheten.

## <a name="view-generated-data"></a>Visa genererade data

När du har tillämpat distributions manifestet på IoT Edge-enheten samlar IoT Edge runtime på enheten den nya distributions informationen och börjar köras på den. Alla moduler som körs på enheten men som inte ingår i distributions manifestet stoppas. Alla moduler som saknas från enheten har startats.

Du kan använda tillägget IoT Edge-verktyg för att visa meddelanden när de anländer till din IoT-hubb.

1. I Visual Studio Cloud Explorer väljer du namnet på din IoT Edge-enhet.

1. I listan **åtgärder** väljer du **starta övervakning inbyggd händelse slut punkt**.

1. Visa meddelanden som kommer till din IoT-hubb. Det kan ta en stund innan meddelandena tas emot, eftersom den IoT Edge enheten måste ta emot sin nya distribution och starta alla moduler. Ändringarna i CSharpModule-koden måste vänta tills datorns temperatur når 25 grader innan meddelandena kan skickas. Koden lägger också till meddelande typ **avisering** till alla meddelanden som når detta temperatur tröskelvärde.

   ![Skärm bild av fönstret utdata som visar meddelanden som kommer från IoT Hub.](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Redigera modulens dubbla

Du använde CSharpModule-modulen för att ange temperatur tröskeln vid 25 grader. Du kan använda modulen för att ändra funktionen utan att behöva uppdatera modulens kod.

1. Öppna filen *deployment.windows-amd64.js* i Visual Studio. 

   Öppna *inte* filen *Deployment. template* . Om du inte ser distributions manifestet i *konfigurations* filen i Solution Explorer väljer du ikonen **Visa alla filer** i Solution Explorer verktygsfältet.

1. Leta efter CSharpModule och ändra värdet för parametern **temperatureThreshold** till en ny temperatur som är 5 till 10 grader högre än den senaste rapporterade temperaturen.

1. Spara *deployment.windows-amd64.jspå* filen.

1. Följ distributions anvisningarna igen för att tillämpa det uppdaterade distributions manifestet på enheten.

1. Övervaka inkommande meddelanden från enhet till molnet. Meddelandena ska stoppas tills det nya temperatur tröskelvärdet har uppnåtts.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta till nästa rekommenderade artikel kan du behålla och återanvända de resurser och konfigurationer som du skapade i den här självstudien. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du undvika att debiteras genom att ta bort de lokala konfigurationerna och de Azure-resurser som du använde här.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul med kod för att filtrera rådata som genereras av din IoT Edge-enhet.

Om du vill veta hur Azure IoT Edge kan hjälpa dig att distribuera Azure Cloud Services för att bearbeta och analysera data på gränsen fortsätter du till nästa självstudier.

> [!div class="nextstepaction"]
> [Azure Functions](tutorial-deploy-function.md) 
>  [Azure Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Azure Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Custom vision tjänst](tutorial-deploy-custom-vision.md)
