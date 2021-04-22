---
title: Självstudie – Utveckla C#-modulen för Linux med hjälp av Azure IoT Edge
description: Den här självstudien visar hur du skapar en IoT Edge-modul med C#-kod och distribuerar den till en Linux IoT Edge enhet.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: e24c04152911f976907e6bdc433c33e035ade639
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874755"
---
# <a name="tutorial-develop-a-c-iot-edge-module-using-linux-containers"></a>Självstudie: Utveckla en C# IoT Edge modul med Linux-containrar

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

Den här självstudien visar hur du utvecklar en modul **i C#** **med Visual Studio Code** och distribuerar den till en IoT Edge enhet. Om du utvecklar moduler med Hjälp av Windows-containrar går du till [Utveckla en C#-IoT Edge-modul med Hjälp av Windows-containrar i](tutorial-csharp-module-windows.md) stället.

Använd följande tabell för att förstå alternativen för att utveckla och distribuera C#-moduler med Hjälp av Linux-containrar:

| C# | Visual Studio-koden | Visual Studio |
| -- | ------------------ | ------------- |
| **Linux AMD64** | ![C#-moduler för LinuxAMD64 i VS Code](./media/tutorial-c-module/green-check.png) | ![C#-moduler för LinuxAMD64 i Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![C#-moduler för LinuxARM32 i VS Code](./media/tutorial-c-module/green-check.png) | ![C#-moduler för LinuxARM64 i Visual Studio](./media/tutorial-c-module/green-check.png) |

>[!NOTE]
>Stöd för Linux ARM64-enheter är tillgängligt i [offentlig förhandsversion.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Mer information finns i [Utveckla och felsöka ARM64 IoT Edge moduler i Visual Studio Code (förhandsversion)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

Innan du påbörjar den här självstudien bör du ha gått igenom föregående självstudie för att konfigurera utvecklingsmiljön, utveckla [en IoT Edge-modul med Linux-containrar](tutorial-develop-for-linux.md). När du har slutfört självstudien bör du redan ha följande förutsättningar:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En enhet som kör Azure IoT Edge Med Linux-containrar. Du kan använda snabbstarterna för att konfigurera en [Linux-enhet eller](quickstart-linux.md) [Windows-enhet.](quickstart.md)
* Ett containerregister, till [exempel Azure Container Registry](../container-registry/index.yml).
* [Visual Studio kod](https://code.visualstudio.com/) som konfigurerats med [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE har konfigurerats](https://docs.docker.com/install/) för att köra Linux-containrar.

För att slutföra de här självstudierna förbereder du följande ytterligare förutsättningar på utvecklingsdatorn:

* [C# för Visual Studio Code-tillägg (drivs av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1).

## <a name="create-a-module-project"></a>Skapa ett modulprojekt

Följande steg skapar ett IoT Edge-modulprojekt för C# med hjälp av Visual Studio Code och Azure IoT Tools tillägget. När du har skapat en projektmall lägger du till ny kod så att modulen filtrerar ut meddelanden baserat på deras rapporterade egenskaper.

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Skapa en C#-lösningsmall som du kan anpassa med din egen kod.

1. I Visual Studio Code väljer du Visa  >  **kommandopalett för** att öppna kommandopaletten i VS Code.

2. Ange och kör kommandot **Azure: Logga in** i kommandopaletten och följ anvisningarna för att logga in med ditt Azure-konto. Om du redan är inloggad kan du hoppa över det här steget.

3. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) på kommandopaletten. Skapa lösningen genom att följ anvisningarna på kommandopaletten.

   | Fält | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen eller acceptera standardnamnet **EdgeSolution**. |
   | Välj modulmall | Välj **C#-modul**. |
   | Ange ett modulnamn | Ge modulen namnet **CSharpModule**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen fylls i baserat på det namn du angav i föregående steg. Ersätt **localhost:5000 med** värdet **för inloggningsservern** från ditt Azure-containerregister. Du kan hämta inloggningsservern från översiktssidan för ditt containerregister i Azure Portal. <br><br>Den slutliga avbildningsdatabasen ser \<registry name\> ut som .azurecr.io/csharpmodule. |

   ![Ange lagringsplatsen för Docker-avbildningen](./media/tutorial-csharp-module/repository.png)

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Miljöfilen lagrar autentiseringsuppgifterna för containerregistret och delar dem med körningsmiljön för IoT Edge. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten. Använd autentiseringsuppgifterna från **avsnittet Åtkomstnycklar** i ditt Azure-containerregister.

Tillägget IoT Edge försöker hämta dina autentiseringsuppgifter för containerregistret från Azure och fylla i dem i miljöfilen. Kontrollera om dina autentiseringsuppgifter redan ingår. Annars lägger du till dem nu:

1. Öppna **.env-filen i VS Code-utforskaren.**
2. Uppdatera fälten med värdena för **användarnamn** **och lösenord** från Ditt Azure-containerregister.
3. Spara den här filen.

### <a name="select-your-target-architecture"></a>Välj din målarkitektur

För närvarande Visual Studio Code utveckla C#-moduler för Linux AMD64- och Linux ARM32v7-enheter. Du måste välja vilken arkitektur du riktar in dig på med varje lösning, eftersom containern byggs och körs på olika sätt för varje arkitekturtyp. Standardvärdet är Linux AMD64.

1. Öppna kommandopaletten och **sök efter Azure IoT Edge:** Ange standardmålplattform för Edge-lösning, eller välj genvägsikonen i sidofältet längst ned i fönstret.

2. I kommandopaletten väljer du målarkitekturen i listan med alternativ. I den här självstudien använder vi en virtuell Ubuntu-dator som IoT Edge enhet, så vi behåller standardinställningen **amd64**.

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

1. Öppna modulerna   >  **CSharpModule** Program.cs i VS Code-utforskaren.  >  

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

5. Hitta **funktionen Init.** Den här funktionen skapar och konfigurerar **ett ModuleClient-objekt** som gör att modulen kan ansluta till den lokala Azure IoT Edge för att skicka och ta emot meddelanden. När du har skapat **ModuleClient** läser koden **temperatureThreshold**-värdet från modultvillingens önskade egenskaper. Koden registrerar ett återanrop för att ta emot meddelanden från IoT Edge-hubben via **input1**-slutpunkten. Ersätt metoden **SetInputMessageHandlerAsync** med en ny och lägg till en **SetDesiredPropertyUpdateCallbackAsync**-metod för uppdateringar av de önskade egenskaperna. Gör den här ändringen genom att ersätta den sista raden i **Init**-metoden med följande kod:

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

9. I VS Code-utforskaren öppnar du **deployment.template.jsfil i** arbetsytan IoT Edge lösning.

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

## <a name="build-and-push-your-module"></a>Skapa och push-skicka modulen

I föregående avsnitt skapade du en IoT Edge och lade till kod i CSharpModule. Den nya koden filtrerar bort meddelanden där temperaturen för den rapporterade datorn ligger inom de godkända gränserna. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

1. Öppna den VS Code-integrerade terminalen genom att **välja**  >  **Visa terminal.**

1. Logga in på Docker genom att ange följande kommando i terminalen. Logga in med användarnamn, lösenord och inloggningsserver från ditt Azure-containerregister. Du kan hämta dessa värden från **avsnittet Åtkomstnycklar** i registret i Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du kan få en säkerhetsvarning som rekommenderar att du använder `--password-stdin` . Även om bästa praxis rekommenderas för produktionsscenarier ligger det utanför omfånget för den här självstudien. Mer information finns i [docker-inloggningsreferensen.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

1. I VS Code-utforskaren högerklickar du på **deployment.template.jspå** filen och väljer Build and Push IoT Edge Solution (Skapa och **push-IoT Edge lösning).**

   Build- och push-kommandot startar tre åtgärder. Först skapas en ny mapp i lösningen med namnet **config** som innehåller det fullständiga distributionsmanifestet, som är byggt på information i distributionsmallen och andra lösningsfiler. För det andra körs den `docker build` för att skapa containeravbildningen baserat på lämplig dockerfile för målarkitekturen. Sedan körs den för `docker push` att push-skicka avbildningsdatabasen till containerregistret.

   Den här processen kan ta flera minuter första gången, men går snabbare nästa gång du kör kommandona.

## <a name="deploy-and-run-the-solution"></a>Distribuera och kör lösningen

Använd Visual Studio Code Explorer och Azure IoT Tools för att distribuera modulprojektet till din IoT Edge enhet. Du har redan ett distributionsmanifest som är förberett **för ditt scenario,deployment.amd64.jspå** filen i config-mappen. Allt du behöver göra nu är att välja en enhet som ska ta emot distributionen.

Kontrollera att din IoT Edge är igång.

1. I Visual Studio Code-utforskaren under **Azure IoT Hub** expanderar du **Enheter** för att se din lista över IoT-enheter.

2. Högerklicka på namnet för din IoT Edge-enhet och välj sedan **Create Deployment for Single Device** (Skapa distribution för en enskild enhet).

3. Välj filen **deployment.amd64.jsi** **konfigurationsmappen och** klicka sedan på **Välj Distributionsmanifest för Edge.** Använd inte filen deployment.template.json.

4. Under din enhet expanderar du **Moduler** för att se en lista över distribuerade och körande moduler. Klicka på uppdateringsknappen. Du bör se att den **nya CSharpModule** körs tillsammans med **modulen SimulatedTemperatureSensor** och **$edgeAgent** och **$edgeHub**.

    Det kan ta några minuter för modulerna att starta. Den IoT Edge körningen måste ta emot sitt nya distributionsmanifest, hämta modulavbildningarna från containerkörningen och sedan starta varje ny modul.

## <a name="view-generated-data"></a>Visa genererade data

När du tillämpar distributionsmanifestet till din IoT Edge-enhet samlar IoT Edge-körningen in den nya distributionsinformationen och börjar köra på den. Alla moduler som körs på enheten som inte finns med i distributionsmanifestet har stoppats. Alla moduler som saknas från enheten startas.

1. I Visual Studio Code Explorer högerklickar du på namnet på din IoT Edge och väljer Starta övervakning **av inbyggd händelseslutpunkt.**

2. Visa meddelanden som kommer till IoT Hub. Det kan ta en stund innan meddelandena tas emot eftersom IoT Edge måste ta emot sin nya distribution och starta alla moduler. Sedan väntar de ändringar vi gjorde i CModule-koden tills datorns temperatur når 25 grader innan meddelanden skickas. Den lägger också till meddelandetypen **Avisering** till alla meddelanden som når det temperaturtröskelvärdet.

   ![Visa meddelanden som kommer IoT Hub](./media/tutorial-csharp-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Redigera modultvillingen

Vi använde CSharpModule-modultvillingen i distributionsmanifestet för att ange tröskelvärdet för temperatur till 25 grader. Du kan använda modultvillingen för att ändra funktionerna utan att behöva uppdatera modulkoden.

1. I Visual Studio Code expanderar du informationen under din IoT Edge för att se de moduler som körs.

2. Högerklicka på **CSharpModule och välj** Redigera **modultvilling**.

3. Hitta **TemperatureThreshold** i önskade egenskaper. Ändra dess värde till en ny temperatur 5 grader till 10 grader högre än den senaste rapporterade temperaturen.

4. Spara modultvillingens fil.

5. Högerklicka någonstans i redigeringsfönstret för modultvillingen och välj **Uppdatera modultvilling.**

6. Övervaka inkommande enhet-till-moln-meddelanden. Du bör se meddelandena stoppas tills det nya tröskelvärdet för temperatur har nåtts.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du ta bort de lokala konfigurationerna och De Azure-resurser som du använde i den här artikeln för att undvika kostnader.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul som innehåller kod för att filtrera rådata som genereras av din IoT Edge-enhet.

Du kan fortsätta med nästa självstudier för att lära dig hur Azure IoT Edge kan hjälpa dig att distribuera Azure-molntjänster för att bearbeta och analysera data vid gränsen.

> [!div class="nextstepaction"]
> [Funktioner](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Custom Vision Service](tutorial-deploy-custom-vision.md)