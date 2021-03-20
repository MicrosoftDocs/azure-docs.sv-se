---
title: Kom igång med Azure IoT Hub Device Management (.NET/.NET) | Microsoft Docs
description: Använda Azure-IoT Hub enhets hantering för att starta en fjärrstyrd enhet. Du använder Azure IoT-enhetens SDK för .NET för att implementera en simulerad enhets app som innehåller en direkt metod och Azure IoT service SDK för .NET för att implementera en tjänst-app som anropar den direkta metoden.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 5760c574e64a3b3b4a1df12092cff44374790a90
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89018407"
---
# <a name="get-started-with-device-management-net"></a>Kom igång med enhets hantering (.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

I den här självstudiekursen lär du dig att:

* Använd Azure Portal för att skapa en IoT-hubb och skapa en enhets identitet i din IoT-hubb.

* Skapa en simulerad enhets app som innehåller en direkt metod som startar om enheten. Direkta metoder anropas från molnet.

* Skapa en .NET-konsol app som anropar metoden starta om direkt i den simulerade appen via din IoT Hub.

I slutet av den här självstudiekursen har du två .NET-konsolappar:

* **SimulateManagedDevice**. Den här appen ansluter till din IoT Hub med enhets identiteten som skapades tidigare, tar emot en direkt metod för omstart, simulerar en fysisk omstart och rapporterar tiden för den senaste omstarten.

* **TriggerReboot**. Den här appen anropar en direkt metod i den simulerade Device-appen, visar svaret och visar uppdaterade egenskaper som rapporteras.

## <a name="prerequisites"></a>Förutsättningar

* Visual Studio.

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

* Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här artikeln använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Utlösa en fjärran sluten omstart på enheten med en direkt metod

I det här avsnittet skapar du en .NET-konsol app med C# som initierar en fjärromstart på en enhet med hjälp av en direkt metod. Appen använder enhets dubbla frågor för att identifiera den senaste omstarts tiden för enheten.

1. I Visual Studio väljer du **skapa ett nytt projekt**.

1. I **skapa ett nytt projekt** söker du efter och väljer projekt mal len **konsol program (.NET Framework)** och väljer sedan **Nästa**.

1. I **Konfigurera ditt nya projekt** namnger du projektet *TriggerReboot* och väljer .NET Framework version 4.5.1 eller senare. Välj **Skapa**.

    ![Nytt Visual C# Windows Classic Desktop-projekt](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. I **Solution Explorer** högerklickar du på projektet **TriggerReboot** och väljer sedan **Hantera NuGet-paket**.

1. Välj **Bläddra** och Sök sedan efter och välj **Microsoft. Azure. Devices**. Välj **Installera** för att installera **Microsoft. Azure. Devices** -paketet.

    ![Fönstret för NuGet-pakethanteraren](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   I det här steget hämtas, installeras och läggs en referens till i [Azure IoT service SDK NuGet-](https://www.nuget.org/packages/Microsoft.Azure.Devices/) paketet och dess beroenden.

1. Lägg till följande- `using` instruktioner överst i filen **program. cs** :

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. Lägg till följande fält i klassen **Program**. Ersätt `{iot hub connection string}` placeholder-värdet med IoT Hub anslutnings strängen som du kopierade tidigare i [Hämta IoT Hub-anslutningssträngen](#get-the-iot-hub-connection-string).

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. Lägg till följande metod i klassen **Program**.  Den här koden hämtar enheten så att den omstartar enheten och matar ut de rapporterade egenskaperna.

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. Lägg till följande metod i klassen **Program**.  Den här koden startar omstarten på enheten med en direkt metod.

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

1. Slutligen lägger du till följande rader till **Main**-metoden:

   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

1. Välj **bygge**  >  **build-lösning**.

> [!NOTE]
> Den här självstudien utför bara en fråga för enhetens rapporterade egenskaper. I produktions kod rekommenderar vi att du avsöker för att identifiera ändringar i de rapporterade egenskaperna.

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet får du:

* Skapa en .NET-konsol app som svarar på en direkt metod som anropas av molnet.

* Utlös en simulerad omstart av enheten.

* Använd de rapporterade egenskaperna för att aktivera enhets dubbla frågor för att identifiera enheter och när de startades om senast.

Följ dessa steg om du vill skapa en simulerad enhets app:

1. I Visual Studio, i TriggerReboot-lösningen som du redan har skapat, väljer du **fil**  >  **nytt**  >  **projekt**. I **skapa ett nytt projekt** söker du efter och väljer projekt mal len **konsol program (.NET Framework)** och väljer sedan **Nästa**.

1. I **Konfigurera ditt nya projekt** namnger du projektet *SimulateManagedDevice* och väljer **Lägg till i lösning** för **lösning**. Välj **Skapa**.

    ![Namnge och Lägg till ditt projekt i lösningen](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. I Solution Explorer högerklickar du på det nya **SimulateManagedDevice** -projektet och väljer sedan **Hantera NuGet-paket**.

1. Välj **Bläddra** och Sök efter och välj **Microsoft. Azure. devices. client**. Välj **installera**.

    ![NuGet paket hanterarens fönster klient program](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   I det här steget hämtas, installeras och läggs en referens till i [Azure IoT Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet-paketet och dess beroenden.

1. Lägg till följande- `using` instruktioner överst i filen **program. cs** :

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Lägg till följande fält i klassen **Program**. Ersätt `{device connection string}` placeholder-värdet med enhets anslutnings strängen som du noterade tidigare i [Registrera en ny enhet i IoT Hub](#register-a-new-device-in-the-iot-hub).

    ```csharp
    static string DeviceConnectionString = "{device connection string}";
    static DeviceClient Client = null;
    ```

1. Lägg till följande för att implementera den direkta metoden på enheten:

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
       try
       {
           Console.WriteLine("Rebooting!");

           // Update device twin with reboot time. 
           TwinCollection reportedProperties, reboot, lastReboot;
           lastReboot = new TwinCollection();
           reboot = new TwinCollection();
           reportedProperties = new TwinCollection();
           lastReboot["lastReboot"] = DateTime.Now;
           reboot["reboot"] = lastReboot;
           reportedProperties["iothubDM"] = reboot;
           Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
       }
       catch (Exception ex)
       {
           Console.WriteLine();
           Console.WriteLine("Error in sample: {0}", ex.Message);
       }

       string result = @"{""result"":""Reboot started.""}";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

1. Slutligen lägger du till följande kod i **main** -metoden för att öppna anslutningen till IoT-hubben och initiera metoden Listener:

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

       // setup callback for "reboot" method
       Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
       Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
       Console.ReadLine();

       Console.WriteLine("Exiting...");

       // as a good practice, remove the "reboot" handler
       Client.SetMethodHandlerAsync("reboot", null, null).Wait();
       Client.CloseAsync().Wait();
   }
   catch (Exception ex)
   {
       Console.WriteLine();
       Console.WriteLine("Error in sample: {0}", ex.Message);
   }
   ```

1. Högerklicka på din lösning i Solution Explorer och välj **Ange start projekt**.

1. För **vanliga egenskaper**  >  **Start projekt** väljer du **enstaka start projekt** och väljer sedan **SimulateManagedDevice** -projektet. Klicka på **OK** för att spara ändringarna.

1. Välj **bygge**  >  **build-lösning**.

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktions koden bör du implementera principer för omförsök (till exempel en exponentiell backoff), vilket rekommenderas vid [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).

## <a name="run-the-apps"></a>Kör apparna

Du är nu redo att köra apparna.

1. Om du vill köra .NET-enhets appens **SimulateManagedDevice** högerklickar du på projektet **SimulateManagedDevice** i Solution Explorer, väljer **Felsök** och väljer sedan **Starta ny instans**. Appen ska börja lyssna efter metod anrop från IoT Hub.

1. När enheten är ansluten och väntar på metod anrop högerklickar du på projektet **TriggerReboot** , väljer **Felsök** och väljer sedan **Starta ny instans**.

   Du bör se "starta om!" Skrivet i **SimulatedManagedDevice** -konsolen och de rapporterade egenskaperna för enheten, som innehåller den senaste omstarts tiden, skrivet i **TriggerReboot** -konsolen.

    ![Körning av tjänst-och enhets app](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
