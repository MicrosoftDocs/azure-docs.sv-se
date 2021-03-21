---
title: Självstudie – utveckla C#-modul för Linux med hjälp av Azure IoT Edge
description: Den här självstudien visar hur du skapar en IoT Edge-modul med C#-kod och distribuerar den till en Linux IoT Edge-enhet.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 01b30fed23b33719f08e93907075eee757343b1c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103461750"
---
# <a name="tutorial-develop-a-c-iot-edge-module-using-linux-containers"></a>Självstudie: utveckla en C# IoT Edge-modul som använder Linux-behållare

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Använd Visual Studio Code för att utveckla C#-kod och distribuera den till en enhet som kör Azure IoT Edge.

Du kan använda Azure IoT Edge-moduler till att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. Du använder den simulerade IoT Edge-enhet som du skapade i snabbstarterna. I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Använd Visual Studio Code för att skapa en IoT Edge-modul som baseras på .NET Core 2.1 SDK.
> * använda Visual Studio Code och Docker till att skapa en Docker-avbildning och publicera den till registret
> * distribuera modulen till din IoT Edge-enhet
> * visa genererade data.

IoT Edge-modulen du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys vid kanten är användbar när du vill minska mängden data som skickas till och lagras i molnet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien visar hur du utvecklar en modul i **C#** med hjälp av **Visual Studio Code** och distribuerar den till en IoT Edge enhet. Om du utvecklar moduler med Windows-behållare går du till [utveckla en C# IoT Edge-modul som använder Windows-behållare](tutorial-csharp-module-windows.md) i stället.

Använd följande tabell för att förstå alternativen för att utveckla och distribuera C#-moduler med hjälp av Linux-behållare:

| C# | Visual Studio-koden | Visual Studio |
| -- | ------------------ | ------------- |
| **Linux AMD64** | ![C#-moduler för LinuxAMD64 i VS Code](./media/tutorial-c-module/green-check.png) | ![C#-moduler för LinuxAMD64 i Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![C#-moduler för LinuxARM32 i VS Code](./media/tutorial-c-module/green-check.png) | ![C#-moduler för LinuxARM64 i Visual Studio](./media/tutorial-c-module/green-check.png) |

>[!NOTE]
>Stöd för Linux ARM64-enheter finns i [offentlig för hands version](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Mer information finns i [utveckla och FELSÖKA ARM64 IoT Edge moduler i Visual Studio Code (för hands version)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

Innan du påbörjar den här självstudien bör du ha gått igenom den föregående kursen för att konfigurera din utvecklings miljö, [utveckla en IoT Edge-modul med hjälp av Linux-behållare](tutorial-develop-for-linux.md). När du har slutfört den här självstudien bör du redan ha följande krav:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En enhet som kör Azure IoT Edge. Du kan använda snabb starterna för att konfigurera en [Linux-enhet](quickstart-linux.md) eller [Windows-enhet](quickstart.md).
* Ett behållar register som [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio-kod](https://code.visualstudio.com/) som kon figurer ATS med [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) konfigurerat för att köra Linux-behållare.

För att slutföra de här självstudierna förbereder du följande ytterligare krav på utvecklings datorn:

* [C# för Visual Studio Code-tillägg (drivs av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-module-project"></a>Skapa ett modul-projekt

Följande steg skapar ett IoT Edge module-projekt för C# med hjälp av Visual Studio Code och tillägget Azure IoT-verktyg. När du har skapat en Project-mall lägger du till ny kod så att modulen filtrerar ut meddelanden utifrån deras rapporterade egenskaper.

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Skapa en C#-lösningsmall som du kan anpassa med din egen kod.

1. I Visual Studio Code väljer du **Visa**  >  **kommando-palett** för att öppna kommando paletten vs Code.

2. Ange och kör kommandot **Azure: Logga in** i kommandopaletten och följ anvisningarna för att logga in med ditt Azure-konto. Om du redan är inloggad kan du hoppa över det här steget.

3. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) på kommandopaletten. Skapa lösningen genom att följ anvisningarna på kommandopaletten.

   | Fält | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen eller acceptera standardnamnet **EdgeSolution**. |
   | Välj modulmall | Välj **C#-modul**. |
   | Ange ett modulnamn | Ge modulen namnet **CSharpModule**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen fylls i baserat på det namn du angav i föregående steg. Ersätt **localhost: 5000** med **inloggnings serverns** värde från Azure Container Registry. Du kan hämta inloggnings servern från sidan Översikt i behållar registret i Azure Portal. <br><br>Den slutliga avbildnings lagrings platsen ser ut som \<registry name\> . azurecr.io/csharpmodule. |

   ![Ange lagringsplatsen för Docker-avbildningen](./media/tutorial-csharp-module/repository.png)

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Miljöfilen lagrar autentiseringsuppgifterna för containerregistret och delar dem med körningsmiljön för IoT Edge. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten. Använd autentiseringsuppgifterna från avsnittet **åtkomst nycklar** i Azure Container Registry.

IoT Edge-tillägget försöker hämta dina autentiseringsuppgifter för behållar registret från Azure och fylla dem i miljö filen. Kontrol lera om dina autentiseringsuppgifter redan ingår. Om inte, lägger du till dem nu:

1. Öppna **. kuvert** -filen i vs Code-Utforskaren.
2. Uppdatera fälten med värdena för **användar namn** och **lösen ord** från Azure Container Registry.
3. Spara den här filen.

### <a name="select-your-target-architecture"></a>Välj din mål arkitektur

För närvarande kan Visual Studio Code utveckla C#-moduler för Linux AMD64-och Linux ARM32v7-enheter. Du måste välja vilken arkitektur du vill använda för varje lösning, eftersom behållaren har skapats och körs på olika sätt för varje arkitektur typ. Standardvärdet är Linux AMD64.

1. Öppna paletten kommando och Sök efter **Azure IoT Edge: Ange standard plattform för Edge-lösning** eller Välj gen vägs ikonen i sido fältet längst ned i fönstret.

2. I paletten kommando väljer du mål arkitekturen i listan med alternativ. I den här självstudien använder vi en virtuell Ubuntu-dator som IoT Edge enhet, så behåller standard- **amd64**.

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

1. I vs Code-Utforskaren öppnar du **modules**  >  **CSharpModule**  >  **program. cs**.

2. Överst i **CSharpModule**-namnrymden läger du till tre **using**-instruktioner för typer som ska användas senare:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Lägg till variabeln **temperatureThreshold** till klassen **Program**. Variabeln anger det värde som den uppmätta temperaturen måste överstiga för att data ska skickas till IoT-hubben.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Lägg till klasserna **MessageBody**, **Machine** och **Ambient** till klassen **Program**. Dessa klasser definierar det förväntade schemat för brödtexten i inkommande meddelanden.

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

5. Hitta funktionen **init** . Den här funktionen skapar och konfigurerar ett **ModuleClient** -objekt, vilket gör att modulen kan ansluta till den lokala Azure IoT Edge runtime för att skicka och ta emot meddelanden. När du har skapat **ModuleClient** läser koden **temperatureThreshold**-värdet från modultvillingens önskade egenskaper. Koden registrerar ett återanrop för att ta emot meddelanden från IoT Edge-hubben via **input1**-slutpunkten. Ersätt metoden **SetInputMessageHandlerAsync** med en ny och lägg till en **SetDesiredPropertyUpdateCallbackAsync**-metod för uppdateringar av de önskade egenskaperna. Gör den här ändringen genom att ersätta den sista raden i **Init**-metoden med följande kod:

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

6. Lägg till metoden **onDesiredPropertiesUpdate** till klassen **Program**. Metoden tar emot uppdateringar av önskade egenskaper från modultvillingen och uppdaterar variabeln **temperatureThreshold** som ska matchas. Alla moduler har en egen modultvilling, vilket innebär att du kan konfigurera den kod som körs i en modul direkt från molnet.

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

7. Ersätt metoden **PipeMessage** med metoden **FilterMessages**. Den här metoden anropas när modulen tar emot ett meddelande från IoT Edge-hubben. Den filtrerar ut meddelanden som rapporterar temperaturer under temperaturtröskelvärdet som angetts via modultvillingen. Den lägger också till egenskapen **MessageType** i meddelandet med värdet angivet som **Avisering**.

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
                using (var filteredMessage = new Message(messageBytes))
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

9. I VS Code-Utforskaren öppnar du **deployment.template.js** filen i din IoT Edge lösnings arbets yta.

10. Lägg till **CSharpModule**-modultvillingen till distributionsmanifestet. Infoga följande JSON-innehåll längst ned i avsnittet **modulesContent** efter **$edgeHub**-modultvillingen:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Lägga till modultvilling till distributionsmall](./media/tutorial-csharp-module/module-twin.png)

11. Spara filen deployment.template.json.

## <a name="build-and-push-your-module"></a>Bygga och pusha din modul

I föregående avsnitt skapade du en IoT Edge-lösning och lagt till kod i CSharpModule. Den nya koden filtrerar bort meddelanden där den rapporterade datorns temperatur ligger inom de acceptabla gränserna. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

1. Öppna den vs Code-integrerade terminalen genom att välja **Visa**  >  **Terminal**.

1. Logga in på Docker genom att ange följande kommando i terminalen. Logga in med användar namnet, lösen ordet och inloggnings servern från Azure Container Registry. Du kan hämta dessa värden från avsnittet **åtkomst nycklar** i registret i Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du kan få en säkerhets varning som rekommenderar att du använder `--password-stdin` . Det bästa tillvägagångs sättet rekommenderas för produktions scenarier, men det ligger utanför omfånget för den här självstudien. Mer information finns i [inloggnings referens för Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

1. I VS Code-Utforskaren högerklickar du på **deployment.template.jspå** filen och väljer **Build och push IoT Edge-lösning**.

   Kommandot build och push startar tre åtgärder. Först skapar den en ny mapp i lösningen som heter **config** och som innehåller det fullständiga distributions manifestet, och bygger ut information i distributions mal len och andra filer i lösningen. Sedan körs den `docker build` för att bygga behållar avbildningen baserat på lämpliga Dockerfile för din mål arkitektur. Sedan körs den `docker push` för att skicka avbildnings lagrings platsen till behållar registret.

   Den här processen kan ta flera minuter första gången, men går snabbare nästa gång du kör kommandona.

## <a name="deploy-and-run-the-solution"></a>Distribuera och kör lösningen

Använd Visual Studio Code Explorer och tillägget Azure IoT Tools för att distribuera modulfönstret till din IoT Edge-enhet. Du har redan ett distributions manifest som är för berett för ditt scenario, **deployment.amd64.js** filen i mappen config. Allt du behöver göra nu är att välja en enhet som ska ta emot distributionen.

Kontrol lera att din IoT Edges enhet är igång.

1. I Visual Studio Code Explorer, under avsnittet **Azure IoT Hub** , expanderar du **enheter** för att se listan med IoT-enheter.

2. Högerklicka på namnet för din IoT Edge-enhet och välj sedan **Create Deployment for Single Device** (Skapa distribution för en enskild enhet).

3. Välj **deployment.amd64.js** filen i mappen **config** och klicka sedan på **Välj gräns distributions manifest**. Använd inte filen deployment.template.json.

4. Under din enhet expanderar du **moduler** för att se en lista över distribuerade och aktiva moduler. Klicka på uppdateringsknappen. Du bör se den nya **CSharpModule** som körs tillsammans med **SimulatedTemperatureSensor** -modulen och **$edgeAgent** och **$edgeHub**.

    Det kan ta några minuter innan modulerna startar. Den IoT Edge körnings miljön måste ta emot sitt nya distributions manifest, Hämta modulens avbildningar från container körningen och sedan starta varje ny modul.

## <a name="view-generated-data"></a>Visa genererade data

När du tillämpar distributionsmanifestet till din IoT Edge-enhet samlar IoT Edge-körningen in den nya distributionsinformationen och börjar köra på den. Alla moduler som körs på enheten som inte finns med i distributionsmanifestet har stoppats. Alla moduler som saknas från enheten startas.

1. I Visual Studio Code Explorer högerklickar du på namnet på din IoT Edge enhet och väljer **starta övervakning inbyggd händelse slut punkt**.

2. Visa meddelanden som kommer till IoT Hub. Det kan ta en stund innan meddelandena anländer, eftersom den IoT Edge enheten måste ta emot sin nya distribution och starta alla moduler. Ändringarna vi gjorde i CModule-koden väntar tills datorns temperatur når 25 grader innan meddelanden skickas. Den lägger också till meddelande typ **avisering** till alla meddelanden som når detta temperatur tröskelvärde.

   ![Visa meddelanden som anländer på IoT Hub](./media/tutorial-csharp-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Redigera modulens dubbla

Vi använde CSharpModule-modulen dubbla i distributions manifestet för att ange temperatur tröskel vid 25 grader. Du kan använda modulen för att ändra funktionen utan att behöva uppdatera modulens kod.

1. I Visual Studio Code, expanderar du informationen under IoT Edge enheten för att se de moduler som körs.

2. Högerklicka på **CSharpModule** och välj **Redigera modul dubbla**.

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