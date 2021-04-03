---
title: Azure IoT Hub module identitet & modul, dubbla (portal och .NET)
description: Lär dig att skapa modulidentitet och uppdatera modultvillingar med portalen och .NET.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 0d16d7a6dbf903ae790015c12e3d34e4a2a553d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92139352"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Kom igång med IoT Hub-modulidentitet och modultvilling med portalen och .NET-enhet

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Modulidentiteter och modultvillingar](iot-hub-devguide-module-twins.md) liknar enhetsidentitet och enhetstvilling i Azure IoT Hub, men har en större detaljnivå. Även om Azure IoT Hub enhets identitet och enhet dubbla aktiverar Server dels programmet för att konfigurera en enhet och ger insyn på enhetens villkor, ger modulens identitet och modul dubbla dessa funktioner för enskilda komponenter i en enhet. På enheter med flera komponenter, t. ex. operativ systembaserade enheter eller inbyggd program vara, är modulens identiteter och modulerna dubbla för att isolera konfiguration och villkor för varje komponent.
>

I den här kursen lär du dig:

* Så här skapar du en modul identitet i portalen.

* Så här använder du en .NET-enhets-SDK för att uppdatera modulen från din enhet.

> [!NOTE]
> Information om Azure IoT SDK: er som du kan använda för att skapa båda programmen som ska köras på enheter och Server delen av lösningen finns i [Azure IoT SDK](iot-hub-devguide-sdks.md): er.
>

## <a name="prerequisites"></a>Förutsättningar

* Visual Studio.

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

## <a name="create-a-hub"></a>Skapa en hubb

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-hub"></a>Registrera en ny enhet i hubben

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Skapa en modulidentitet i portalen

Inom en enhetsidentitet kan du skapa upp till 20 modulidentiteter. Följ dessa steg om du vill lägga till en identitet:

1. För enheten som du skapade i föregående avsnitt väljer du **Lägg till modul identitet** för att skapa din första modul identitet.

1. Ange namnet *myFirstModule*. Spara din modul identitet.

    ![Lägg till modulens identitet](./media/iot-hub-portal-csharp-module-twin-getstarted/add-module-identity.png)

    Din nya modul-ID visas längst ned på skärmen. Välj den för att se information om modulens identitet.

    ![Se information om modulens identitet](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

Spara **anslutnings strängen – primär nyckel**. Du kan använda den i nästa avsnitt för att konfigurera din modul på enheten.

## <a name="update-the-module-twin-using-net-device-sdk"></a>Uppdatera modultvillingen med SDK för .NET-enheter

Du har skapat modulidentiteten i din IoT Hub. Försök kommunicera till molnet från den simulerade enheten. När en modulidentitet har skapats skapas en modultvilling implicit i IoT Hub. I det här avsnittet skapar du en .NET-konsolapp på din simulerade enhet som uppdaterar modultvillingens rapporterade egenskaper.

### <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

Följ dessa steg om du vill skapa en app som uppdaterar modulens dubbla rapporterade egenskaper:

1. I Visual Studio väljer du **skapa ett nytt projekt** och väljer sedan **konsol program (.NET Framework)** och väljer **Nästa**.

1. I **Konfigurera ditt nya projekt** anger du *UpdateModuleTwinReportedProperties* som **projekt namn**. Fortsätt genom att välja **skapa** .

    ![Konfigurera att du är ett Visual Studio-projekt](./media/iot-hub-portal-csharp-module-twin-getstarted/configure-twins-project.png)

### <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Installera den senaste Azure IoT Hub .NET-enhets-SDK: n

Modulens identitet och modul är i offentlig för hands version. Den är endast tillgänglig i IoT Hub för hands version av enhets-SDK: er. Följ dessa steg om du vill installera det:

1. I Visual Studio öppnar du **verktyg**  >  **NuGet Package Manager**  >  **Hantera NuGet-paket för lösningen**.

1. Välj **Bläddra** och välj sedan **Inkludera för hands version**. Sök efter *Microsoft. Azure. devices. client*. Välj den senaste versionen och installera.

    ![Installera Azure IoT Hub .NET service SDK Preview](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

    Nu har du åtkomst till alla modulfunktioner.

### <a name="get-your-module-connection-string"></a>Hämta din anslutnings sträng för modul

Du behöver modulens anslutnings sträng för konsol programmet. Gör så här:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Navigera till din IoT-hubb och välj **IoT-enheter**. Öppna **t myfirstdevice** så ser du att **myFirstModule** har skapats.

1. Välj **myFirstModule** under **modul identiteter**. I **modulens identitets information** kopierar du **anslutnings strängen (primär nyckel)**.

    ![Information om Azure-portalmodulen](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

### <a name="create-updatemoduletwinreportedproperties-console-app"></a>Skapa UpdateModuleTwinReportedProperties-konsol program

Följ dessa steg om du vill skapa din app:

1. Lägg till följande- `using` instruktioner överst i filen **program. cs** :

  ```csharp
  using Microsoft.Azure.Devices.Client;
  using Microsoft.Azure.Devices.Shared;
  using Newtonsoft.Json;
  ```

2. Lägg till följande fält i klassen **Program**. Ersätt platshållarens värde med modulens anslutningssträng.

  ```csharp
  private const string ModuleConnectionString = "<Your module connection string>";
  private static ModuleClient Client = null;
  ```

3. Lägg till följande metod **OnDesiredPropertyChanged** till klassen **Program**:

  ```csharp
  private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
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

4. Ersätt slutligen **huvud** metoden med följande kod:

  ```csharp
  static void Main(string[] args)
  {
      Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;
  
      try
      {
          Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
          Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
          Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();
  
          Console.WriteLine("Retrieving twin");
          var twinTask = Client.GetTwinAsync();
          twinTask.Wait();
          var twin = twinTask.Result;
          Console.WriteLine(JsonConvert.SerializeObject(twin));
  
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
      Console.ReadKey();
      Client.CloseAsync().Wait();
  }
  
  private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
  {
      Console.WriteLine($"Status {status} changed: {reason}");
  }
  ```
  
  Du kan skapa och köra den här appen med **F5**.

Det är kodexemplet visar hur du hämtar modultvillingen och uppdaterar rapporterade egenskaper med AMQP-protokollet. I offentlig förhandsversion stöder vi endast AMQP för modultvillingåtgärder.

## <a name="next-steps"></a>Nästa steg

För att fortsätta komma igång med IoT-hubb och utforska andra IoT-scenarier, se:

* [Kom igång med IoT Hub-modulidentitet och modultvilling med .NET-säkerhetskopiering och .NET-enhet](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [Komma igång med IoT Edge](../iot-edge/quickstart-linux.md)