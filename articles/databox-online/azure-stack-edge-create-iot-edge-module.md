---
title: C# IoT Edge-modul för Azure Stack Edge Pro | Microsoft Docs
description: Lär dig hur du utvecklar en C# IoT Edge-modul som kan distribueras på din Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/06/2019
ms.author: alkohli
ms.custom: devx-track-csharp
ms.openlocfilehash: 4519bc187c4ec53294e5eef15c4ad1954b691224
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870849"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-with-azure-stack-edge-pro"></a>Utveckla en C#-IoT Edge modul för att flytta filer med Azure Stack Edge Pro

Den här artikeln beskriver hur du skapar en IoT Edge-modul för distribution med din Azure Stack Edge Pro enhet. Azure Stack Edge Pro är en lagringslösning som gör att du kan bearbeta data och skicka dem via nätverket till Azure.

Du kan använda Azure IoT Edge-moduler med Azure Stack Edge Pro för att transformera data när de flyttas till Azure. Modulen som används i den här artikeln implementerar logiken för att kopiera en fil från en lokal resurs till en molnresurs på din Azure Stack Edge Pro enhet.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> * Skapa ett containerregister för att lagra och hantera dina moduler (Docker-avbildningar).
> * Skapa en IoT Edge-modul som ska distribueras på Azure Stack Edge Pro enhet. 


## <a name="about-the-iot-edge-module"></a>Om IoT Edge modulen

Din Azure Stack Edge Pro enhet kan distribuera och köra IoT Edge moduler. Edge-moduler är i stort sett Docker-containrar som utför en viss uppgift, till exempel mata in ett meddelande från en enhet, transformera ett meddelande eller skicka ett meddelande till en IoT Hub. I den här artikeln skapar du en modul som kopierar filer från en lokal resurs till en molnresurs på din Azure Stack Edge Pro enhet.

1. Filer skrivs till den lokala resursen på din Azure Stack Edge Pro enhet.
2. Filhändelsegeneratorn skapar en filhändelse för varje fil som skrivs till den lokala resursen. Filhändelser genereras också när en fil ändras. Filhändelserna skickas sedan till IoT Edge Hub (i IoT Edge runtime).
3. Den IoT Edge modulen bearbetar filhändelsen för att skapa ett filhändelseobjekt som även innehåller en relativ sökväg för filen. Modulen genererar en absolut sökväg med hjälp av den relativa filsökvägen och kopierar filen från den lokala resursen till molnresursen. Modulen tar sedan bort filen från den lokala resursen.

![Så Azure IoT Edge modulen fungerar på Azure Stack Edge Pro](./media/azure-stack-edge-create-iot-edge-module/how-module-works-1.png)

När filen finns i molnresursen laddas den automatiskt upp till ditt Azure Storage konto.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrollera att du har:

- En Azure Stack Edge Pro enhet som körs.

    - Enheten har också en associerad IoT Hub resurs.
    - Enheten har edge-beräkningsrollen konfigurerad.
    Mer information finns i Konfigurera [beräkning för](azure-stack-edge-deploy-configure-compute.md#configure-compute) din Azure Stack Edge Pro.

- Följande utvecklingsresurser:

    - [Visual Studio Code](https://code.visualstudio.com/).
    - [C# för Visual Studio Code-tillägg (drivs av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
    - [Azure IoT Edge-tillägg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).
    - [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1).
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Du kan behöva skapa ett konto för att ladda ned och installera programvaran.

## <a name="create-a-container-registry"></a>Skapa ett containerregister

Ett Azure-containerregister är ett privat Docker-register i Azure där du kan lagra och hantera dina privata Docker-containeravbildningar. De två populära Docker-registertjänsterna som är tillgängliga i molnet Azure Container Registry och Docker Hub. Den här artikeln använder Container Registry.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Välj **Skapa en resurs > Containers > Container Registry**. Klicka på **Skapa**.
3. Ge:

   1. Ett unikt **registernamn** i Azure som innehåller mellan 5 och 50 alfanumeriska tecken.
   2. Välj en **prenumeration.**
   3. Skapa en ny eller välj en **befintlig resursgrupp.**
   4. Välj en **Plats.** Vi rekommenderar att den här platsen är samma som den som är associerad med Azure Stack Edge resurs.
   5. Växla till **Administratörsanvändare** för att **Aktivera**.
   6. Ställ in SKU:n på **Basic**.

      ![Skapa containerregister](./media/azure-stack-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. Välj **Skapa**.
5. När du har skapat ditt containerregister går du till det och väljer **Åtkomstnycklar**.

    ![Hämta åtkomstnycklar](./media/azure-stack-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. Kopiera värdena för **Inloggningsserver**, **Användarnamn** och **Lösenord**. Du använder dessa värden senare för att publicera Docker-avbildningen till registret och lägga till autentiseringsuppgifterna för registret Azure IoT Edge körningen.


## <a name="create-an-iot-edge-module-project"></a>Skapa ett projekt för IoT Edge-modulen

Följande steg skapar ett IoT Edge-modulprojekt baserat på .NET Core 2.1 SDK. Projektet använder Visual Studio Code och Azure IoT Edge tillägget.

### <a name="create-a-new-solution"></a>Skapa en ny lösning

Skapa en C#-lösningsmall som du kan anpassa med din egen kod.

1. I Visual Studio Code väljer du Visa **> kommandopalett för** att öppna kommandopaletten i VS Code.
2. Ange och kör kommandot **Azure: Logga in** i kommandopaletten och följ anvisningarna för att logga in med ditt Azure-konto. Om du redan är inloggad kan du hoppa över det här steget.
3. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) på kommandopaletten. Ange följande information i kommandopaletten för att skapa din lösning:

    1. Välj den mapp där du vill skapa lösningen.
    2. Ange ett namn för din lösning eller välj standardnamnet **EdgeSolution**.
    
        ![Skapa ny lösning 1](./media/azure-stack-edge-create-iot-edge-module/create-new-solution-1.png)

    3. Välj **C#-modul** som modulmall.
    4. Ersätt standardmodulnamnet med det namn som du vill tilldela. I det här fallet är det **FileCopyModule**.
    
        ![Skapa ny lösning 2](./media/azure-stack-edge-create-iot-edge-module/create-new-solution-2.png)

    5. Ange det containerregister som du skapade i föregående avsnitt som avbildningsdatabas för din första modul. Ersätt **localhost:5000** med det serverinloggningsvärde som du kopierade.

        Den slutliga strängen ser ut så `<Login server name>/<Module name>` här: . I det här exemplet är strängen: `mycontreg2.azurecr.io/filecopymodule` .

        ![Skapa ny lösning 3](./media/azure-stack-edge-create-iot-edge-module/create-new-solution-3.png)

4. Gå till **Arkiv > Öppna mapp**.

    ![Skapa ny lösning 4](./media/azure-stack-edge-create-iot-edge-module/create-new-solution-4.png)

5. Bläddra och peka på mappen **EdgeSolution** som du skapade tidigare. VS Code-fönstret läser in IoT Edge lösningsarbetsyta med dess fem komponenter på den översta nivån. Du kommer inte att redigera **mappen .vscode,** **.gitignore-filen,** **.env-filen** ochdeployment.template.js **på** i den här artikeln.
    
    Den enda komponent som du ändrar är mappen modules. Den här mappen har C#-koden för din modul och Docker-filer för att skapa modulen som en containeravbildning.

    ![Skapa ny lösning 5](./media/azure-stack-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

1. I VS Code-utforskaren öppnar **du moduler > FileCopyModule > Program.cs**.
2. Längst upp i **namnområdet FileCopyModule lägger** du till följande using-instruktioner för typer som används senare. **Microsoft.Azure.Devices.Client.Transport.Mqtt** är ett protokoll för att skicka meddelanden till IoT Edge Hub.

    ```
    namespace FileCopyModule
    {
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using Newtonsoft.Json;
    ```
3. Lägg till **variabeln InputFolderPath** **och OutputFolderPath** i klassen Program.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Direkt efter föregående steg lägger du till **klassen FileEvent** för att definiera meddelandetexten.

    ```
    /// <summary>
    /// The FileEvent class defines the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. I **metoden Init** skapar och konfigurerar koden ett **ModuleClient-objekt.** Med det här objektet kan modulen ansluta till den lokala Azure IoT Edge med hjälp av MQTT-protokollet för att skicka och ta emot meddelanden. Anslutningssträngen som används i Init-metoden skickas till modulen av IoT Edge-körningen. Koden registrerar ett FileCopy-återanrop för att ta emot meddelanden från en IoT Edge-hubb via **slutpunkten input1.** Ersätt **init-metoden** med följande kod.

    ```
    /// <summary>
    /// Initializes the ModuleClient and sets up the callback to receive
    /// messages containing file event information
    /// </summary>
    static async Task Init()
    {
        MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
        ITransportSettings[] settings = { mqttSetting };

        // Open a connection to the IoT Edge runtime
        ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
        await ioTHubModuleClient.OpenAsync();
        Console.WriteLine("IoT Hub module client initialized.");

        // Register callback to be called when a message is received by the module
        await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FileCopy, ioTHubModuleClient);
    }
    ```

6. Ta bort koden för **metoden PipeMessage** och infoga i stället koden för **FileCopy**.

    ```
        /// <summary>
        /// This method is called whenever the module is sent a message from the IoT Edge Hub.
        /// This method deserializes the file event, extracts the corresponding relative file path, and creates the absolute input file path using the relative file path and the InputFolderPath.
        /// This method also forms the absolute output file path using the relative file path and the OutputFolderPath. It then copies the input file to output file and deletes the input file after the copy is complete.
        /// </summary>
        static async Task<MessageResponse> FileCopy(Message message, object userContext)
        {
            int counterValue = Interlocked.Increment(ref counter);

            try
            {
                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                if (!string.IsNullOrEmpty(messageString))
                {
                    var fileEvent = JsonConvert.DeserializeObject<FileEvent>(messageString);

                    string relativeFileName = fileEvent.ShareRelativeFilePath.Replace("\\", "/");
                    string inputFilePath = InputFolderPath + relativeFileName;
                    string outputFilePath = OutputFolderPath + relativeFileName;

                    if (File.Exists(inputFilePath))                
                    {
                        Console.WriteLine($"Moving input file: {inputFilePath} to output file: {outputFilePath}");
                        var outputDir = Path.GetDirectoryName(outputFilePath);
                        if (!Directory.Exists(outputDir))
                        {
                            Directory.CreateDirectory(outputDir);
                        }

                        File.Copy(inputFilePath, outputFilePath, true);
                        Console.WriteLine($"Copied input file: {inputFilePath} to output file: {outputFilePath}");
                        File.Delete(inputFilePath);
                        Console.WriteLine($"Deleted input file: {inputFilePath}");
                    } 
                    else
                    {
                        Console.WriteLine($"Skipping this event as input file doesn't exist: {inputFilePath}");   
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Caught exception: {0}", ex.Message);
                Console.WriteLine(ex.StackTrace);
            }

            Console.WriteLine($"Processed event.");
            return MessageResponse.Completed;
        }
    ```

7. Spara den här filen.
8. Du kan också [ladda ned ett befintligt kodexempel](https://azure.microsoft.com/resources/samples/data-box-edge-csharp-modules/?cdn=disable) för det här projektet. Du kan sedan verifiera filen som du sparade mot filen **program.cs** i det här exemplet.

## <a name="build-your-iot-edge-solution"></a>Skapa din IoT Edge-lösning

I föregående avsnitt skapade du en IoT Edge-lösning och lade till kod i FileCopyModule för att kopiera filer från en lokal resurs till molnresursen. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

1. I VSCode går du till Terminal > Ny terminal för att öppna en ny Visual Studio Code-integrerad terminal.
2. Logga in på Docker genom att ange följande kommando i den integrerade terminalen.

    `docker login <ACR login server> -u <ACR username>`

    Använd inloggningsservern och användarnamnet som du kopierade från containerregistret.

    ![Skapa och push-IoT Edge lösning](./media/azure-stack-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Ange lösenordet när du uppmanas att ange lösenordet. Du kan också hämta värdena för inloggningsserver,  användarnamn och lösenord från åtkomstnycklarna i containerregistret i Azure Portal.
 
3. När autentiseringsuppgifterna har angetts kan du skicka modulavbildningen till Azure-containerregistret. I VS Code Explorer högerklickar du på filen **module.jsoch** väljer Skapa och **push-IoT Edge lösningen**.

    ![Skapa och push-IoT Edge lösning 2](./media/azure-stack-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    När du Visual Studio Code för att skapa din lösning körs två kommandon i den integrerade terminalen: docker build och docker push. Dessa två kommandon skapar din kod, bäddar in CSharpModule.dll i en container och skickar sedan koden till det containerregister som du angav när du initierade lösningen.

    Du uppmanas att välja modulplattform. Välj *amd64* som motsvarar Linux.

    ![Välj plattform](./media/azure-stack-edge-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Endast Linux-moduler stöds.

    Du kan se följande varning som du kan ignorera:

    *Program.cs(77,44): varning CS1998: Den här asynkrona metoden saknar "await"-operatorer och körs synkront. Överväg att använda await-operatorn för att vänta på icke-blockerande API-anrop eller "await Task.Run(...)" för att utföra cpu-bundet arbete på en bakgrundstråd.*

4. Den fullständiga adressen med tagg för containeravbildningen finns i den integrerade VS Code-terminalen. Avbildningsadressen bygger på information som finns i module.jspå filen med formatet `<repository>:<version>-<platform>` . I den här artikeln bör den se ut så `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64` här: .

## <a name="next-steps"></a>Nästa steg

Om du vill distribuera och köra den här Azure Stack Edge Pro finns stegen i Lägga [till en modul](azure-stack-edge-deploy-configure-compute.md#add-a-module).
