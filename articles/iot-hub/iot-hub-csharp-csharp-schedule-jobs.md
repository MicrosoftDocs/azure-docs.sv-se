---
title: Schemalägg jobb med Azure IoT Hub (.NET/.NET) | Microsoft Docs
description: Schemalägga ett Azure IoT Hub-jobb för att anropa en direkt metod på flera enheter. Du använder Azure IoT-enhetens SDK för .NET för att implementera de simulerade enhetens appar och en tjänst-app för att köra jobbet.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 0391afe5562238f488fde7e50d914c46a5c32944
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92142461"
---
# <a name="schedule-and-broadcast-jobs-net"></a>Schema-och sändnings jobb (.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Använd Azure IoT Hub för att schemalägga och spåra jobb som uppdaterar miljon tals enheter. Använd jobb för att:

* Uppdatera önskade egenskaper

* Uppdatera Taggar

* Anropa direkt metoder

Ett jobb radbryter en av dessa åtgärder och spårar körningen mot en uppsättning enheter som definieras av en enhets dubbla frågor. En backend-app kan till exempel använda ett jobb för att anropa en direkt metod på 10 000 enheter som startar om enheterna. Du kan ange en uppsättning enheter med en enhet med dubbla frågor och schemalägga jobbet så att det körs vid ett senare tillfälle. Jobbet spårar förloppet när var och en av enheterna tar emot och kör metoden starta om Direct.

Mer information om var och en av dessa funktioner finns i:

* Enhetens dubbla och egenskaper: [Kom igång med enhets dubbla](iot-hub-csharp-csharp-twin-getstarted.md) och [Självstudier: hur du använder enhetens dubbla egenskaper](tutorial-device-twins.md)

* Direkta metoder: [IoT Hub Developer Guide – direkta metoder](iot-hub-devguide-direct-methods.md) och [Självstudier: Använd direkta metoder](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en app för enheten som implementerar en direkt metod som kallas **LockDoor**, som kan anropas av backend-appen.

* Skapa en backend-app som skapar ett jobb för att anropa metoden **LockDoor** Direct på flera enheter. Ett annat jobb skickar önskade egenskaps uppdateringar till flera enheter.

I slutet av den här självstudien har du två .NET-konsol program (C#):

* **SimulateDeviceMethods**. Den här appen ansluter till din IoT-hubb och implementerar **LockDoor** Direct-metoden.

* **ScheduleJob**. Den här appen använder jobb för att anropa metoden **LockDoor** Direct och uppdatera enhetens dubbla önskade egenskaper på flera enheter.

## <a name="prerequisites"></a>Förutsättningar

* Visual Studio.

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

* Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här artikeln använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet skapar du en .NET-konsol app som svarar på en direkt metod som anropas av lösningens Server del.

1. I Visual Studio väljer du **skapa ett nytt projekt** och klickar sedan på projekt mal len **konsol program (.NET Framework)** . Fortsätt genom att välja **Nästa**.

1. I **Konfigurera ditt nya projekt** namnger du projektet *SimulateDeviceMethods* och väljer sedan **skapa**.

    ![Konfigurera ditt SimulateDeviceMethods-projekt](./media/iot-hub-csharp-csharp-schedule-jobs/configure-device-app.png)

1. I Solution Explorer högerklickar du på projektet **SimulateDeviceMethods** och väljer sedan **Hantera NuGet-paket**.

1. I **NuGet Package Manager** väljer du **Bläddra** och söker efter och väljer **Microsoft. Azure. devices. client**. Välj **installera**.

    ![NuGet paket hanterarens fönster klient program](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

    I det här steget hämtas, installeras och läggs en referens till i [Azure IoT Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet-paketet och dess beroenden.

1. Lägg till följande- `using` instruktioner överst i filen **program. cs** :

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Lägg till följande fält i klassen **Program**. Ersätt placeholder-värdet med enhets anslutnings strängen som du antecknade i föregående avsnitt:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

1. Lägg till följande kod för att implementera den direkta metoden på enheten:

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);

        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```

1. Lägg till följande metod för att implementera enhetens dubbla lyssnare på enheten:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. Slutligen lägger du till följande kod i **main** -metoden för att öppna anslutningen till IoT-hubben och initiera metoden Listener:

    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
          TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. Spara ditt arbete och bygg din lösning.

> [!NOTE]
> För att det ska vara enkelt implementerar den här självstudien inte några principer för återförsök. I produktions koden bör du implementera principer för omförsök (till exempel anslutnings försök), enligt rekommendationer för [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Schemalägg jobb för att anropa en direkt metod och skicka enhets dubbla uppdateringar

I det här avsnittet skapar du en .NET-konsol app (med C#) som använder jobb för att anropa metoden **LockDoor** Direct och skicka önskade egenskaps uppdateringar till flera enheter.

1. I Visual Studio väljer du **fil**  >  **nytt**  >  **projekt**. I **skapa ett nytt projekt** väljer du **konsol program (.NET Framework)** och väljer sedan **Nästa**.

1. I **Konfigurera ditt nya projekt** namnger du projektet *ScheduleJob*. För **lösning** väljer **du Lägg till i lösning** och väljer sedan **skapa**.

    ![Namnge och konfigurera ScheduleJob-projekt](./media/iot-hub-csharp-csharp-schedule-jobs/config-schedule-job-app.png)

1. I Solution Explorer högerklickar du på projektet **ScheduleJob** och väljer sedan **Hantera NuGet-paket**.

1. I **NuGet Package Manager** väljer du **Bläddra**, söker efter och väljer **Microsoft. Azure. Devices** och väljer sedan **Installera**.

   I det här steget hämtas, installeras och läggs en referens till i [Azure IoT service SDK NuGet-](https://www.nuget.org/packages/Microsoft.Azure.Devices/) paketet och dess beroenden.

1. Lägg till följande- `using` instruktioner överst i filen **program. cs** :

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Lägg till följande `using` instruktion om den inte redan finns i standard instruktionerna.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. Lägg till följande fält i klassen **Program**. Ersätt plats hållarna med IoT Hub anslutnings strängen som du kopierade tidigare i [Hämta IoT Hub-anslutningssträngen](#get-the-iot-hub-connection-string) och namnet på enheten.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. Lägg till följande metod i **program** -klassen:

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && 
          (result.Status != JobStatus.Failed));
    }
    ```

1. Lägg till följande metod i **program** -klassen:

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = 
          new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), 
          TimeSpan.FromSeconds(5));

        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. Lägg till en annan metod i **program** klassen:

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > Mer information om frågesyntax finns i [IoT Hub frågespråk](./iot-hub-devguide-query-language.md).
    >

1. Slutligen lägger du till följande rader till **Main**-metoden:

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. Spara ditt arbete och bygg din lösning.

## <a name="run-the-apps"></a>Kör apparna

Nu är det dags att köra apparna.

1. Högerklicka på din lösning i Visual Studio-Solution Explorer och välj **Ange start projekt**.

1. Välj **vanliga egenskaper**  >  **Start projekt** och välj sedan **flera start projekt**.

1. Se till att `SimulateDeviceMethods` visas överst i listan följt av `ScheduleJob` . Ange båda åtgärderna för att **Starta** och välj **OK**.

1. Kör projekten genom att klicka på **Start** eller gå till **Felsök** -menyn och klicka på **Starta fel sökning**.

   Du ser utdata från både enhets-och backend-appar.

    ![Kör apparna för att schemalägga jobb](./media/iot-hub-csharp-csharp-schedule-jobs/schedule-jobs-console-results.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du ett jobb för att schemalägga en direkt metod till en enhet och uppdateringen av enhetens egenskaper.

* Om du vill fortsätta att komma igång med IoT Hub-och enhets hanterings mönster, t. ex. fjärran sluten av den inbyggda Air-uppdateringen, kan du läsa mer [i självstudier](tutorial-firmware-update.md)

* Information om hur du distribuerar AI till Edge-enheter med Azure IoT Edge finns i [komma igång med IoT Edge](../iot-edge/quickstart-linux.md).