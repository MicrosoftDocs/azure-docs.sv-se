---
title: Kom igång med Azure IoT Hub module Identity & modul, dubbla (.NET)
description: Lär dig att skapa modulidentitet och uppdatera modultvillingar med IoT SDK:er för .NET.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: menchi
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: daba400b9daadf464c4c125ad266745237e71367
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92142511"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>Kom igång med IoT Hub modulens identitet och modul, dubbla (.NET)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Modulidentiteter och modultvillingar](iot-hub-devguide-module-twins.md) liknar enhetsidentitet och enhetstvilling i Azure IoT Hub, men har en större detaljnivå. Även om Azure IoT Hub enhets identitet och enhet dubbla aktiverar Server dels programmet för att konfigurera en enhet och ger insyn på enhetens villkor, ger modulens identitet och modul dubbla dessa funktioner för enskilda komponenter i en enhet. På enheter med flera komponenter, t. ex. operativ systembaserade enheter eller inbyggd program vara, är modulens identiteter och modulerna dubbla för att isolera konfiguration och villkor för varje komponent.

I slutet av den här självstudiekursen har du två .NET-konsolappar:

* **CreateIdentities**. Den här appen skapar en enhets identitet, en modul identitet och en associerad säkerhets nyckel för att ansluta dina enhets-och modul klienter.

* **UpdateModuleTwinReportedProperties**. Den här appen skickar den uppdaterade modulen dubbla rapporterade egenskaper till din IoT-hubb.

> [!NOTE]
> Artikeln om [Azure IoT SDK:er](iot-hub-devguide-sdks.md) innehåller information om Azure IoT SDK:er som du kan använda för att skapa båda programmen så att de kan köras på enheter och på lösningens backend-server.

## <a name="prerequisites"></a>Förutsättningar

* Visual Studio.

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

## <a name="create-a-hub"></a>Skapa en hubb

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Uppdatera modultvillingen med SDK för .NET-enheter

I det här avsnittet skapar du en .NET-konsolapp på din simulerade enhet som uppdaterar modultvillingens rapporterade egenskaper.

Innan du börjar ska du hämta anslutnings strängen för din modul. Logga in på [Azure-portalen](https://portal.azure.com/). Navigera till din hubb och välj **IoT-enheter**. Hitta **t myfirstdevice**. Välj **t myfirstdevice** för att öppna den och välj sedan **myFirstModule** för att öppna den. I **modulens identitets information** kopierar du **anslutnings strängen (primär nyckel)** vid behov i följande procedur.

   ![Information om Azure-portalmodulen](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. I Visual Studio lägger du till ett nytt projekt i lösningen genom att välja **Arkiv**  >  **nytt**  >  **projekt**. I skapa ett nytt projekt väljer du **konsol program (.NET Framework)** och väljer **Nästa**.

1. Ge projektet namnet *UpdateModuleTwinReportedProperties*. För **lösning** väljer **du Lägg till i lösning**. Kontrollera att .NET Framework-versionen är 4.6.1 eller senare.

    ![Skapa ett Visual Studio-projekt](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. Skapa projektet genom att välja **Skapa**.

1. I Visual Studio öppnar du **verktyg**  >  **NuGet Package Manager**  >  **Hantera NuGet-paket för lösningen**. Välj fliken **Bläddra**.

1. Sök efter och välj **Microsoft. Azure. devices. client** och välj sedan **Installera**.

    ![Skärm bild som visar "Microsoft. Azure. devices. Client" och knappen "installera" markerad.](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

1. Lägg till följande- `using` instruktioner överst i filen **program. cs** :

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

1. Lägg till följande fält i klassen **Program**. Ersätt platshållarens värde med modulens anslutningssträng.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

1. Lägg till följande metod **OnDesiredPropertyChanged** till klassen **Program**:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

1. Lägg till följande rader i **main** -metoden:

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    Det är kodexemplet visar hur du hämtar modultvillingen och uppdaterar rapporterade egenskaper med AMQP-protokollet. I offentlig förhandsversion stöder vi endast AMQP för modultvillingåtgärder.

1. Alternativt kan du lägga till dessa instruktioner i **huvud** metoden för att skicka en händelse till IoT Hub från modulen. Placera raderna under `try catch` blocket.

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Kör apparna

Nu kan du köra apparna.

1. Högerklicka på din lösning i **Solution Explorer** i Visual Studio och välj sedan **Ange start projekt**.

1. Under **gemensamma egenskaper** väljer du **Start projekt.**

1. Välj **flera start projekt** och välj sedan **Starta** som åtgärd för apparna och **OK** för att acceptera ändringarna.

1. Tryck på **F5** för att starta apparna.

## <a name="next-steps"></a>Nästa steg

För att fortsätta komma igång med IoT-hubb och utforska andra IoT-scenarier, se:

* [Connecting your device](iot-hub-node-node-device-management-get-started.md) (Komma igång med enhetshantering)

* [Komma igång med IoT Edge](../iot-edge/quickstart-linux.md)