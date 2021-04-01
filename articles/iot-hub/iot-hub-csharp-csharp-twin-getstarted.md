---
title: Kom igång med Azure IoT Hub Device Ungarns (.NET/.NET) | Microsoft Docs
description: Så här använder du Azure IoT Hub-enheten för att lägga till taggar och sedan använda en IoT Hub fråga. Du använder Azure IoT-enhetens SDK för .NET för att implementera den simulerade Device-appen och Azure IoT service SDK för .NET för att implementera en service app som lägger till taggarna och kör IoT Hubs frågan.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 267a69486dc91ef95c0de704346eeb1d1780ef48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89013766"
---
# <a name="get-started-with-device-twins-net"></a>Kom igång med enhets dubbla (.NET)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

I den här självstudien skapar du följande .NET-konsol program:

* **AddTagsAndQuery**. Den här backend-appen lägger till taggar och frågar enheten med dubbla.

* **ReportConnectivity**. Den här enhets appen simulerar en enhet som ansluter till din IoT Hub med enhets identiteten som skapades tidigare och rapporterar dess anslutnings tillstånd.

> [!NOTE]
> Artikeln [Azure IoT SDK](iot-hub-devguide-sdks.md) : er innehåller information om Azure IoT SDK: er som du kan använda för att bygga både enhets-och backend-appar.
>

## <a name="prerequisites"></a>Förutsättningar

* Visual Studio.

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

* Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här artikeln använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Skapa tjänst appen

I det här avsnittet skapar du en .NET-konsol app med C#, som lägger till platsens metadata till enheten som är kopplad till **myDeviceId**. Den frågar sedan enheten efter varandra i IoT Hub och väljer de enheter som finns i USA och sedan de som rapporterade en mobil anslutning.

1. I Visual Studio väljer du **skapa ett nytt projekt**. I **Skapa nytt projekt** väljer du **konsol program (.NET Framework)** och väljer sedan **Nästa**.

1. I **Konfigurera ditt nya projekt** namnger du projektet **AddTagsAndQuery**.

    ![Konfigurera ditt AddTagsAndQuery-projekt](./media/iot-hub-csharp-csharp-twin-getstarted/config-addtagsandquery-app.png)

1. I Solution Explorer högerklickar du på projektet **AddTagsAndQuery** och väljer sedan **Hantera NuGet-paket**.

1. Välj **Bläddra** och Sök efter och välj **Microsoft. Azure. Devices**. Välj **installera**.

    ![Fönstret för NuGet-pakethanteraren](./media/iot-hub-csharp-csharp-twin-getstarted/nuget-package-addtagsandquery-app.png)

   I det här steget hämtas, installeras och läggs en referens till i [Azure IoT service SDK NuGet-](https://www.nuget.org/packages/Microsoft.Azure.Devices/) paketet och dess beroenden.

1. Lägg till följande- `using` instruktioner överst i filen **program. cs** :

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

1. Lägg till följande fält i klassen **Program**. Ersätt `{iot hub connection string}` med IoT Hub anslutnings strängen som du kopierade i [Hämta IoT Hub-anslutningssträngen](#get-the-iot-hub-connection-string).

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. Lägg till följande metod i **program** -klassen:

    ```csharp  
    public static async Task AddTagsAndQuery()
    {
        var twin = await registryManager.GetTwinAsync("myDeviceId");
        var patch =
            @"{
                tags: {
                    location: {
                        region: 'US',
                        plant: 'Redmond43'
                    }
                }
            }";
        await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);

        var query = registryManager.CreateQuery(
          "SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
        var twinsInRedmond43 = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43: {0}", 
          string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

        query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
        var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43 using cellular network: {0}", 
          string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
    }
    ```

    **RegistryManager** -klassen visar alla de metoder som krävs för att interagera med enheten tillsammans från tjänsten. Föregående kod initierar först **registryManager** -objektet och hämtar sedan enheten för **myDeviceId** och uppdaterar slutligen dess Taggar med önskad plats information.

    Efter uppdateringen körs två frågor: det första alternativet väljer att endast enhetarna är dubbla enheter som finns i **Redmond43** -anläggningen och den andra refinar frågan så att endast de enheter som också är anslutna via mobil nät verket återställs.

    Föregående kod **när objektet skapas** , anger ett maximalt antal returnerade dokument. **Frågespråket** innehåller en **HasMoreResults** Boolean-egenskap som du kan använda för att anropa **GetNextAsTwinAsync** -metoderna flera gånger för att hämta alla resultat. En metod som heter **GetNextAsJson** är tillgänglig för resultat som inte är enhets dubbla, till exempel resultat av agg regerings frågor.

1. Slutligen lägger du till följande rader till **Main**-metoden:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. Kör det här programmet genom att högerklicka på projektet **AddTagsAndQuery** och välja **Felsök**, följt av **Starta ny instans**. Du bör se en enhet i resultatet för frågan som frågar efter alla enheter som finns i **Redmond43** och ingen för den fråga som begränsar resultatet till enheter som använder ett mobil nät verk.

    ![Fråga resultat i fönster](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

I nästa avsnitt skapar du en enhets app som rapporterar anslutnings informationen och ändrar resultatet för frågan i föregående avsnitt.

## <a name="create-the-device-app"></a>Skapa enhetsappen

I det här avsnittet skapar du en .NET-konsol app som ansluter till hubben som **myDeviceId** och uppdaterar sedan dess rapporterade egenskaper så att den innehåller den information som den är ansluten till med ett mobilt nätverk.

1. I Visual Studio väljer du **fil**  >  **nytt**  >  **projekt**. I **Skapa nytt projekt** väljer du **konsol program (.NET Framework)** och väljer sedan **Nästa**.

1. I **Konfigurera ditt nya projekt** namnger du projektet **ReportConnectivity**. För **lösning** väljer **du Lägg till i lösning** och väljer sedan **skapa**.

1. I Solution Explorer högerklickar du på projektet **ReportConnectivity** och väljer sedan **Hantera NuGet-paket**.

1. Välj **Bläddra** och Sök efter och välj **Microsoft. Azure. devices. client**. Välj **installera**.

   I det här steget hämtas, installeras och läggs en referens till i [Azure IoT Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet-paketet och dess beroenden.

1. Lägg till följande- `using` instruktioner överst i filen **program. cs** :

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Lägg till följande fält i klassen **Program**. Ersätt `{device connection string}` med den enhets anslutnings sträng som du antecknade i [Registrera en ny enhet i IoT Hub](#register-a-new-device-in-the-iot-hub).

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

1. Lägg till följande metod i **program** -klassen:

    ```csharp
    public static async void InitClient()
    {
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
              TransportType.Mqtt);
            Console.WriteLine("Retrieving twin");
            await Client.GetTwinAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

    **Klient** objekt visar alla metoder som du behöver för att interagera med enheten tillsammans från enheten. Den kod som visas ovan initierar **klient** objekt och hämtar sedan enheten för **myDeviceId**.

1. Lägg till följande metod i **program** -klassen:

    ```csharp  
    public static async void ReportConnectivity()
    {
        try
        {
            Console.WriteLine("Sending connectivity data as reported property");

            TwinCollection reportedProperties, connectivity;
            reportedProperties = new TwinCollection();
            connectivity = new TwinCollection();
            connectivity["type"] = "cellular";
            reportedProperties["connectivity"] = connectivity;
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

   Koden ovan uppdaterar den rapporterade egenskapen för  **myDeviceId** med anslutnings informationen.

1. Slutligen lägger du till följande rader till **Main**-metoden:

    ```csharp
    try
    {
        InitClient();
        ReportConnectivity();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. I Solution Explorer högerklickar du på din lösning och väljer **Ange start projekt**.

1. I   >  **Start projekt** för vanliga egenskaper väljer du **flera start projekt**. För **ReportConnectivity** väljer du **Starta** som **åtgärd**. Klicka på **OK** för att spara ändringarna.  

1. Kör den här appen genom att högerklicka på projektet **ReportConnectivity** och välja **Felsök** och sedan **Starta ny instans**. Du bör se appen som hämtar den dubbla informationen och sedan skicka anslutningen som en **_rapporterad egenskap_**.

    ![Kör Device app för att rapportera anslutningen](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)

   När enheten har rapporterat anslutnings informationen bör den visas i båda frågorna.

1. Högerklicka på projektet **AddTagsAndQuery** och välj **Felsök**  >  **Starta ny instans** för att köra frågorna igen. Den här gången ska **myDeviceId** visas i båda frågeresultaten.

    ![Enhets anslutningen har rapporter ATS](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du har lagt till metadata för enheten som taggar från en backend-app och skrev en simulerad Device-app för att rapportera enhetens anslutnings information på enheten. Du har också lärt dig hur du frågar den här informationen med hjälp av SQL-liknande IoT Hub frågespråk.

Du kan läsa mer från följande resurser:

* Information om hur du skickar telemetri från enheter finns i själv studie kursen [Skicka telemetri från en enhet till en IoT Hub](quickstart-send-telemetry-dotnet.md) .

* Information om hur du konfigurerar enheter med enhetens dubbla egenskaper finns i själv studie kursen [använda önskade egenskaper för att konfigurera enheter](tutorial-device-twins.md) .

* Information om hur du styr enheter interaktivt, t. ex. genom att aktivera en fläkt från en användardefinierad app, finns i själv studie kursen [använda direkta metoder](quickstart-control-device-dotnet.md) .
