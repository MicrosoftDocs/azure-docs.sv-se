---
title: Meddelanden från moln till enhet med Azure IoT Hub (.NET) | Microsoft Docs
description: 'Skicka meddelanden från moln till enhet till en enhet från en Azure IoT Hub med Azure IoT-SDK: er för .NET. Du ändrar en enhets app för att ta emot meddelanden från molnet till enheten och ändra en backend-app för att skicka meddelanden från molnet till enheten.'
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-csharp
ms.openlocfilehash: d8df9884c0104792240d85d9ebd4235ef2a18741
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92142363"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Skicka meddelanden från molnet till din enhet med IoT Hub (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub är en fullständigt hanterad tjänst som hjälper till att möjliggöra tillförlitlig och säker dubbelriktad kommunikation mellan miljon tals enheter och Server delen av lösningen. Snabb starten [Skicka telemetri från en enhet till en IoT Hub](quickstart-send-telemetry-dotnet.md) visar hur du skapar en IoT-hubb, etablerar en enhets identitet i den och kodar en enhets app som skickar enhet till molnet-meddelanden.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Den här självstudien bygger på [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md). Det visar hur du utför följande uppgifter:

* Skicka meddelanden från moln till enhet från Server delen av lösningen till en enda enhet via IoT Hub.

* Ta emot meddelanden från molnet till enheten på en enhet.

* Från Server delen av lösningen kan du begära leverans bekräftelse (*feedback*) för meddelanden som skickas till en enhet från IoT Hub.

Du hittar mer information om moln-till-enhet-meddelanden i [D2C-och C2D-meddelanden med IoT Hub](iot-hub-devguide-messaging.md).

I slutet av den här självstudien kör du två .NET-konsol program.

* **SimulatedDevice**. Den här appen ansluter till din IoT-hubb och tar emot meddelanden från molnet till enheten. Den här appen är en modifierad version av appen som skapats i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md).

* **SendCloudToDevice**. Den här appen skickar ett meddelande från moln till enhet till enhetens app via IoT Hub och får sedan leverans bekräftelse.

> [!NOTE]
> IoT Hub har SDK-stöd för många enhets plattformar och språk, inklusive C, Java, python och Java Script, via SDK: er för [Azure IoT-enheter](iot-hub-devguide-sdks.md). Stegvisa instruktioner för hur du ansluter din enhet till den här själv studie kursen och i allmänhet till Azure IoT Hub finns i [IoT Hub Developer Guide](iot-hub-devguide.md).
>

## <a name="prerequisites"></a>Förutsättningar

* Visual Studio

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

* Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här artikeln använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-device-app"></a>Ta emot meddelanden i enhets appen

I det här avsnittet ändrar du enhets appen som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md) för att ta emot meddelanden från molnet till enheten från IoT Hub.

1. I Visual Studio, i **SimulatedDevice** -projektet, lägger du till följande metod i **SimulatedDevice** -klassen.

   ```csharp
    private static async void ReceiveC2dAsync()
    {
        Console.WriteLine("\nReceiving cloud to device messages from service");
        while (true)
        {
            Message receivedMessage = await s_deviceClient.ReceiveAsync();
            if (receivedMessage == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received message: {0}", 
            Encoding.ASCII.GetString(receivedMessage.GetBytes()));
            Console.ResetColor();

            await s_deviceClient.CompleteAsync(receivedMessage);
        }
    }
   ```

1. Lägg till följande metod i **main** -metoden, precis före `Console.ReadLine()` raden:

   ```csharp
   ReceiveC2dAsync();
   ```

`ReceiveAsync`Metoden returnerar det mottagna meddelandet asynkront vid den tidpunkt då enheten tas emot av enheten. Den returnerar *Null* efter en specificerad timeout-period. I det här exemplet används standardvärdet på en minut. När appen får ett *Null*-värde ska den fortsätta att vänta på nya meddelanden. Det här kravet är orsaken till `if (receivedMessage == null) continue` raden.

Anropet till `CompleteAsync()` meddelar IoT Hub att meddelandet har bearbetats och att meddelandet kan tas bort från enhets kön på ett säkert sätt. Enheten ska anropa den här metoden när bearbetningen har slutförts oavsett vilket protokoll den använder.

Med AMQP och HTTPS, men inte MQTT, kan enheten också:

* Överge ett meddelande som resulterar i att IoT Hub behåller meddelandet i enhets kön för framtida konsumtion.
* Avvisa ett meddelande som permanent tar bort meddelandet från enhets kön.

Om något händer som hindrar enheten från att kunna slutföra, avbryta eller avvisa meddelandet, kommer IoT Hub efter en fast tids gräns, köa meddelandet för leverans igen. Därför måste meddelande bearbetnings logiken i enhets appen vara *idempotenta*, så att samma meddelande får flera gånger samma resultat.

Mer detaljerad information om hur IoT Hub bearbetar meddelanden från molnet till enheten, inklusive information om livs cykeln för moln-till-enhet-meddelanden, finns i [skicka meddelanden från moln till enhet från en IoT-hubb](iot-hub-devguide-messages-c2d.md).

> [!NOTE]
> När du använder HTTPS i stället för MQTT eller AMQP som transport, `ReceiveAsync` returnerar metoden omedelbart. Det mönster som stöds för meddelanden från moln till enhet med HTTPS är tillfälligt anslutna enheter som söker efter meddelanden sällan (minst var 25: e minut). Om du skickar mer HTTPS får du resultat i IoT Hub begränsning av begär Anden. Mer information om skillnaderna mellan MQTT, AMQP och HTTPS-stöd finns i avsnittet [om kommunikation mellan moln och enheter](iot-hub-devguide-c2d-guidance.md) och [Välj ett kommunikations protokoll](iot-hub-devguide-protocols.md).
>

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

I den här artikeln skapar du en server dels tjänst för att skicka meddelanden från molnet till enheten via IoT-hubben som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md). För att skicka meddelanden från molnet till enheten måste tjänsten ha behörighet för **tjänst anslutning** . Som standard skapas varje IoT Hub med en delad åtkomst princip med namnet **tjänst** som ger den här behörigheten.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Skicka ett meddelande från moln till enhet

I det här avsnittet skapar du en .NET-konsol app som skickar meddelanden från molnet till enheten till den simulerade Device-appen.

1. I den aktuella Visual Studio-lösningen väljer du **fil**  >  **nytt**  >  **projekt**. I **skapa ett nytt projekt** väljer du **konsol program (.NET Framework)** och väljer sedan **Nästa**.

1. Ge projektet namnet *SendCloudToDevice*. Under **lösning** väljer **du Lägg till i lösning** och godkänner den senaste versionen av .NET Framework. Välj **Skapa** för att skapa projektet.

   ![Konfigurera ett nytt projekt i Visual Studio](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. I Solution Explorer högerklickar du på det nya projektet och väljer sedan **Hantera NuGet-paket**.

1. I **Hantera NuGet-paket** väljer du **Bläddra** och söker efter och väljer **Microsoft. Azure. Devices**. Välj  **Installera**.

   I det här steget hämtas, installeras och läggs en referens till i [Azure IoT service SDK NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

1. Lägg till följande- `using` instruktion högst upp i filen **program. cs** .

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. Lägg till följande fält i klassen **Program**. Ersätt `{iot hub connection string}` placeholder-värdet med IoT Hub-anslutningssträngen som du noterade tidigare i [Hämta IoT Hub-anslutningssträngen](#get-the-iot-hub-connection-string). Ersätt `{device id}` placeholder-värdet med enhets-ID för enheten som du lade till i [Skicka telemetri från en enhet till en IoT Hub](quickstart-send-telemetry-dotnet.md) -snabb start.

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   static string targetDevice = "{device id}";
   ```

1. Lägg till följande metod i **program** -klassen för att skicka ett meddelande till enheten.

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync(targetDevice, commandMessage);
   }
   ```

1. Lägg slutligen till följande rader i **main** -metoden.

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. Högerklicka på din lösning i Solution Explorer och välj **Ange start projekt**.

1. I **vanliga egenskaper**  >  **Start projekt**, Välj **flera start projekt** och välj sedan **Start** åtgärden för **SimulatedDevice** och **SendCloudToDevice**. Klicka på **OK** för att spara ändringarna.

1. Tryck på **F5**. Båda programmen ska starta. Välj fönstret **SendCloudToDevice** och tryck på **RETUR**. Du bör se meddelandet som tas emot av Device-appen.

   ![Enhets appen tar emot meddelande](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Få feedback om leverans

Det går att begära leverans (eller förfallo datum) bekräftelser från IoT Hub för varje moln-till-enhet-meddelande. Med det här alternativet kan lösningens Server del enkelt informera om återförsök eller kompensations logik. Mer information om feedback från moln till enhet finns i [D2C och C2D Messaging med IoT Hub](iot-hub-devguide-messaging.md).

I det här avsnittet ändrar du **SendCloudToDevice** -appen för att begära feedback och tar emot den från IoT-hubben.

1. I Visual Studio, i **SendCloudToDevice** -projektet, lägger du till följande metod i **program** klassen.

   ```csharp
   private async static void ReceiveFeedbackAsync()
   {
        var feedbackReceiver = serviceClient.GetFeedbackReceiver();

        Console.WriteLine("\nReceiving c2d feedback from service");
        while (true)
        {
            var feedbackBatch = await feedbackReceiver.ReceiveAsync();
            if (feedbackBatch == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received feedback: {0}",
              string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
            Console.ResetColor();

            await feedbackReceiver.CompleteAsync(feedbackBatch);
        }
    }
    ```

    Observera att det här mottagnings mönstret är samma som används för att ta emot meddelanden från molnet till enheten från Device-appen.

1. Lägg till följande rad i **main** -metoden, direkt efter `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` .

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. Om du vill begära feedback för leverans av ditt meddelande från molnet till enheten måste du ange en egenskap i **SendCloudToDeviceMessageAsync** -metoden. Lägg till följande rad, direkt efter `var commandMessage = new Message(...);` raden.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. Kör apparna genom att trycka på **F5**. Du bör se att båda programmen startar. Välj fönstret **SendCloudToDevice** och tryck på **RETUR**. Du bör se meddelandet som tas emot av Device-appen och efter några sekunder får du ett feedback-meddelande som tas emot av ditt **SendCloudToDevice** -program.

   ![Enhets appen tar emot meddelande-och tjänst program som tar emot feedback](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> För enkelhetens skull implementerar den här självstudien inte någon princip för återförsök. I produktions koden bör du implementera principer för återförsök, till exempel exponentiell backoff, som föreslagen vid [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).
>

## <a name="next-steps"></a>Nästa steg

I den här instruktionen har du lärt dig hur du skickar och tar emot meddelanden från molnet till enheten.

Exempel på kompletta lösningar från slut punkt till slut punkt som använder IoT Hub finns i [Azure IoT Remote Monitoring Solution Accelerator](/azure/iot-suite/).

Mer information om hur du utvecklar lösningar med IoT Hub finns i [IoT Hub Developer Guide](iot-hub-devguide.md).