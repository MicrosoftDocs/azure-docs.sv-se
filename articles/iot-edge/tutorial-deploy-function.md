---
title: 'Självstudie: Distribuera Azure Functions som moduler – Azure IoT Edge'
description: I den här självstudien utvecklar du en Azure-funktion IoT Edge en modul och distribuerar den sedan till en gränsenhet.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 30abc4a5a1431800cef2bcbda6f5eeedf9a216a3
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874647"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Självstudie: Distribuera Azure Functions som IoT Edge moduler

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Du kan använda Azure Functions till att distribuera kod som implementerar din affärslogik direkt till dina Azure IoT Edge-enheter. Den här självstudien visar hur du skapar och distribuerar en Azure-funktion som filtrerar sensordata på den simulerade IoT Edge enhet. Du använder den simulerade IoT Edge som du skapade i snabbstarterna. I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Använd Visual Studio Code för att skapa en Azure-funktion.
> * använda VS Code och Docker till att skapa en Docker-avbildning och publicera den till ett containerregister
> * distribuera modulen från containerregistret till din IoT Edge-enhet
> * visa filtrerade data.

<center>

![Diagram – Självstudiearkitektur: stega och distribuera funktionsmodul](./media/tutorial-deploy-function/functions-architecture.png)
</center>

Den Azure-funktion som du skapar i den här självstudien filtrerar temperaturdata som genereras av din enhet. Funktionen skickar endast meddelanden uppströms till Azure IoT Hub när temperaturen är över ett angivet tröskelvärde.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här självstudien bör du ha gått igenom föregående självstudie för att konfigurera utvecklingsmiljön för Linux-containerutveckling: [Utveckla IoT Edge-moduler med Linux-containrar](tutorial-develop-for-linux.md). När du har slutfört den här självstudien bör du ha följande förutsättningar på plats:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En enhet som kör Azure IoT Edge Med Linux-containrar. Du kan använda snabbstarterna för att konfigurera en [Linux-enhet eller](quickstart-linux.md) [Windows-enhet.](quickstart.md)
* Ett containerregister, till [exempel Azure Container Registry](../container-registry/index.yml).
* [Visual Studio kod](https://code.visualstudio.com/) som konfigurerats med [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE har konfigurerats](https://docs.docker.com/install/) för att köra Linux-containrar.

Om du vill IoT Edge modulen i Azure Functions installerar du följande ytterligare förutsättningar på utvecklingsdatorn:

* [C# för Visual Studio Code-tillägg (drivs av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [The .NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1).

## <a name="create-a-function-project"></a>Skapa ett funktionsprojekt

Azure IoT-verktyg för Visual Studio Code som du installerade i avsnittet med systemkrav och förutsättningar innehåller hanteringsfunktioner och några kodmallar. I det här avsnittet använder du Visual Studio Code för att skapa en IoT Edge som innehåller en Azure-funktion.

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Skapa en C#-funktionslösningsmall som du kan anpassa med din egen kod.

1. Öppna Visual Studio Code på utvecklingsdatorn.

2. Öppna kommandopaletten i VS Code genom att **välja Visa**  >  **kommandopalett.**

3. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) på kommandopaletten. Skapa lösningen genom att följ anvisningarna på kommandopaletten.

   | Fält | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen, till exempel **FunctionSolution**, eller acceptera standardnamnet. |
   | Välj modulmall | Välj **Azure Functions - C#**. |
   | Ange ett modulnamn | Ge modulen namnet **CSharpFunction**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen har fyllts i från föregående steg. Ersätt **localhost:5000 med** värdet **för inloggningsservern** från ditt Azure-containerregister. Du kan hämta inloggningsservern från översiktssidan för ditt containerregister i Azure Portal. Den slutliga strängen ser ut \<registry name\> som .azurecr.io/CSharpFunction. |

   ![Ange lagringsplatsen för Docker-avbildningen](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials&quot;></a>Lägg till autentiseringsuppgifter för registret

Miljöfilen lagrar autentiseringsuppgifterna för containerregistret och delar dem med körningsmiljön för IoT Edge. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten.

Tillägget IoT Edge försöker hämta dina autentiseringsuppgifter för containerregistret från Azure och fylla i dem i miljöfilen. Kontrollera om dina autentiseringsuppgifter redan ingår. Annars lägger du till dem nu:

1. Öppna .env-filen i VS Code-utforskaren.
2. Uppdatera fälten med det **användarnamn** och **lösenord** som du kopierade från Azure Container-registret.
3. Spara den här filen.

### <a name=&quot;select-your-target-architecture&quot;></a>Välj din målarkitektur

För närvarande Visual Studio Code C-moduler för Linux AMD64- och Linux ARM32v7-enheter. Du måste välja vilken arkitektur du riktar in dig på med varje lösning, eftersom containern byggs och körs på olika sätt för varje arkitekturtyp. Standardvärdet är Linux AMD64.

1. Öppna kommandopaletten och **sök efter Azure IoT Edge:** Ange standardmålplattform för Edge-lösning, eller välj genvägsikonen i sidofältet längst ned i fönstret.

2. I kommandopaletten väljer du målarkitekturen i listan med alternativ. I den här självstudien använder vi en virtuell Ubuntu-dator som IoT Edge enhet, så vi behåller standardinställningen **amd64**.

### <a name=&quot;update-the-module-with-custom-code&quot;></a>Uppdatera modulen med anpassad kod

Nu ska vi lägga till ytterligare kod så att modulen bearbetar meddelandena vid gränsen innan de vidarebefordras till IoT Hub.

1. I Visual Studio Code öppnar du  >  **modulerna CSharpFunction**  >  **CSharpFunction.cs**.

1. Ersätt innehållet i filen **CSharpFunction.cs** med följande kod. Den här koden tar emot telemetri om omgivande temperatur och datortemperatur och vidarebefordrar enbart meddelandet till IoT Hub om datortemperaturen överstiger ett angivet tröskelvärde.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName(&quot;CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threshold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
                            // Copy the properties of the original message into the new Message object.
                            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                            {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                            // Add a new property to the message to indicate it is an alert.
                            filteredMessage.Properties.Add("MessageType", "Alert");
                            // Send the message.
                            await output.AddAsync(filteredMessage);
                            logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                       }
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
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
   }
   ```

1. Spara filen.

## <a name="build-and-push-your-iot-edge-solution"></a>Skapa och push-IoT Edge lösning

I föregående avsnitt skapade du en IoT Edge-lösning och ändrade **CSharpFunction** för att filtrera ut meddelanden med rapporterade datortemperaturer under det godkända tröskelvärdet. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

1. Öppna den INTEGRERADE VS Code-terminalen genom att **välja Visa**  >  **terminal.**

2. Logga in på Docker genom att ange följande kommando i terminalen. Logga in med användarnamn, lösenord och inloggningsserver från ditt Azure-containerregister. Du kan hämta dessa värden från **avsnittet Åtkomstnycklar** i registret i Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du kan få en säkerhetsvarning som rekommenderar att du använder `--password-stdin` . Även om bästa praxis rekommenderas för produktionsscenarier ligger det utanför omfånget för den här självstudien. Mer information finns i [docker-inloggningsreferensen.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

3. I VS Code-utforskaren högerklickar du på **deployment.template.jspå** filen och väljer Build and Push IoT Edge Solution (Skapa och **push-IoT Edge lösning).**

   Build- och push-kommandot startar tre åtgärder. Först skapas en ny mapp i lösningen med namnet **config** som innehåller det fullständiga distributionsmanifestet, som är byggt på information i distributionsmallen och andra lösningsfiler. För det andra körs den `docker build` för att skapa containeravbildningen baserat på lämplig dockerfile för målarkitekturen. Sedan körs den för `docker push` att push-skicka avbildningsdatabasen till containerregistret.

   Den här processen kan ta flera minuter första gången, men går snabbare nästa gång du kör kommandona.

## <a name="view-your-container-image"></a>Visa din containeravbildning

Visual Studio Code matar ut ett meddelande om lyckad överföring när containeravbildningen har push-överförts till containerregistret. Om du själv vill kontrollera att åtgärden lyckades kan du visa avbildningen i registret.

1. Bläddra till ditt Azure containerregister i Azure Portal.
2. Välj **Lagringsplatser**.
3. Du bör se lagringsplatsen **csharpfunction** i listan. Välj den här lagringsplatsen för att se mer information.
4. I avsnittet **Taggar** bör du se taggen **0.0.1-amd64**. Den här taggen anger version och plattform för avbildningen du skapade. Dessa värden anges i filen module.json i mappen CSharpFunction.

## <a name="deploy-and-run-the-solution"></a>Distribuera och kör lösningen

Du kan använda Azure Portal för att distribuera funktionsmodulen till en IoT Edge enhet som du gjorde i snabbstarterna. Du kan också distribuera och övervaka moduler från Visual Studio Code. I följande avsnitt använder du Azure IoT-verktygen för VS Code som angavs i förhandskraven. Installera tillägget nu om du inte redan har gjort det.

1. I Visual Studio Code-utforskaren under **Azure IoT Hub** expanderar du **Enheter** för att se din lista över IoT-enheter.

2. Högerklicka på namnet på din IoT Edge och välj sedan **Skapa distribution för enskild enhet.**

3. Bläddra till lösningsmappen som innehåller **CSharpFunction**. Öppna mappen config, välj filen **deployment.amd64.jsoch** välj sedan **Välj Distributionsmanifest för Edge.**

4. Under din enhet expanderar du **Moduler** för att se en lista över distribuerade och körande moduler. Klicka på uppdateringsknappen. Du bör se att den **nya CSharpFunction** körs tillsammans med **modulen SimulatedTemperatureSensor** **och $edgeAgent** och **$edgeHub**.

    Det kan ta en stund för de nya modulerna att visas. IoT Edge-enheten måste hämta sin nya distributionsinformation från IoT Hub, starta de nya containrarna och rapportera statusen tillbaka till IoT Hub.

   ![Visa distribuerade moduler i VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-the-generated-data"></a>Visa genererade data

Du kan se alla meddelanden som kommer till din IoT-hubb genom **att köra Azure IoT Hub: Starta** övervakning av inbyggd händelseslutpunkt i kommandopaletten.

Du kan också filtrera vyn för att visa alla meddelanden som kommer till IoT-hubben från en viss enhet. Högerklicka på enheten i avsnittet Azure IoT Hub **Enheter** och välj **Starta övervakning av inbyggd händelseslutpunkt.**

Om du vill stoppa övervakningen av meddelanden kör **du kommandot Azure IoT Hub: Stop Monitoring Built-in Event Endpoint (Stoppa** övervakning av inbyggd händelseslutpunkt) i kommandopaletten.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du har skapat i den här artikeln för att därigenom undvika kostnader.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en Azure Function-modul med kod för att filtrera rådata som genereras av din IoT Edge enhet.

Fortsätt med någon av följande självstudier om du vill lära dig mer om hur Azure IoT Edge kan hjälpa dig att omvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Hitta medelvärden med hjälp av ett flytande fönster i Azure Stream Analytics](tutorial-deploy-stream-analytics.md)
