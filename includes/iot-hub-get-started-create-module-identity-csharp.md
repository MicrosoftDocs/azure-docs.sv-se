---
title: ta med fil
description: ta med fil
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: a5c1ddd085ae65b9920d73f50f993f4646785a69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "68883727"
---
## <a name="create-a-module-identity"></a>Skapa en modulidentitet

I det här avsnittet skapar du en .NET-konsol app som skapar en enhets identitet och en modul identitet i identitets registret i din hubb. En enhet eller modul kan inte ansluta till hubben om den inte har en post i identitets registret. Mer information finns i avsnittet om [identitetsregistret i utvecklarhandboken för IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

När du kör den här konsolappen, genereras ett unikt ID och en unik nyckel för både enheten och modulen. Enheten och modulen använder dessa värden för att identifiera sig själva när de skickar enhets-till-moln-meddelanden till IoT Hub. ID:n är skiftlägeskänsliga.

1. Öppna Visual Studio och välj **skapa ett nytt projekt**.

1. I **skapa ett nytt projekt** väljer du **konsol program (.NET Framework)**.

1. Välj **Nästa** för att öppna **Konfigurera det nya projektet**. Ge projektet namnet *CreateIdentities* och lösningen namnet *IoTHubGetStarted*. Kontrollera att .NET Framework-versionen är 4.6.1 eller senare.

    ![Ange namn och ramverk för din Visual Studio-lösning](./media/iot-hub-get-started-create-module-identity-csharp/configure-createidentities-project.png)

1. I Visual Studio öppnar du **verktyg**  >  **NuGet Package Manager**  >  **Hantera NuGet-paket för lösningen**. Välj fliken **Bläddra**.

1. Sök efter **Microsoft. Azure. Devices**. Markera den och välj sedan **Installera**.

    ![Installera den aktuella versionen av Azure IoT Hub .NET service SDK](./media/iot-hub-get-started-create-module-identity-csharp/install-service-sdk.png)

1. Lägg till följande- `using` instruktioner överst i filen **program. cs** :

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

1. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdet med IoT Hub-anslutningssträngen som du skapade i föregående avsnitt.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

1. Lägg till följande kod i **huvud** klassen.

   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

1. Lägg till följande metoder i klassen **Program**:

    ```csharp
    private static async Task AddDeviceAsync()
    {
       RegistryManager registryManager = 
         RegistryManager.CreateFromConnectionString(connectionString);
       Device device;

       try
       {
           device = await registryManager.AddDeviceAsync(new Device(deviceID));
       }
       catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
        }

        Console.WriteLine("Generated device key: {0}", 
          device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = 
          RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = 
              await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    `AddDeviceAsync`Metoden skapar en enhets identitet med ID **t myfirstdevice**. Om enhets-ID: t redan finns i identitets registret hämtar koden bara den befintliga enhets informationen. Appen visar sedan den primära nyckeln för den identiteten. Du använder den här nyckeln i den simulerade Device-appen för att ansluta till din hubb.

    `AddModuleAsync`Metoden skapar en modul identitet med ID **myFirstModule** under enhets **t myfirstdevice**. Om detta modul-ID redan finns i identitets registret hämtar koden bara den befintliga informationen om modulen. Appen visar sedan den primära nyckeln för den identiteten. Du använder den här nyckeln i appen för simulerad modul för att ansluta till din hubb.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Kör den här appen och anteckna enhets nyckeln och modulens nyckel.

> [!NOTE]
> Registret IoT Hub identitet lagrar endast enhets-och modulens identiteter för att möjliggöra säker åtkomst till hubben. Enhets-ID:n och nycklar lagras i identitetsregistret och används som autentiseringsuppgifter. I identitetsregistret lagras också en aktiverad/inaktiverad-flagga för varje enhet som du kan använda till att inaktivera enhetens åtkomst. Om appen behöver lagra andra enhetsspecifika metadata bör den använda ett programspecifikt arkiv. Det finns ingen aktiverad/inaktiverad flagga för modulidentiteter. Mer information finns i [utvecklarhandboken för IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
