---
title: Skapa en Azure IoT Hub med hjälp av resurs leverantören REST API | Microsoft Docs
description: Lär dig hur du använder Resource Provider C# REST API för att skapa och hantera en IoT Hub program mässigt.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 2f393701b97be76acab49a627a195b337018fa29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92144434"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Skapa en IoT-hubb med hjälp av Resource Provider REST API (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Du kan använda [IoT Hub Resource provider REST API](/rest/api/iothub/iothubresource) för att skapa och hantera Azure IoT Hub program mässigt. Den här självstudien visar hur du använder IoT Hub Resource Provider REST API för att skapa en IoT-hubb från ett C#-program.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

För att göra den här självstudien behöver du följande:

* Visual Studio.

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

* [Azure PowerShell 1,0](/powershell/azure/install-Az-ps) eller senare.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Förbered ditt Visual Studio-projekt

1. I Visual Studio skapar du ett Visual C#-klassiskt Windows klassiska Skriv bords projekt med hjälp av projekt mal len **konsol program (.NET Framework)** . Ge projektet namnet **CreateIoTHubREST**.

2. I Solution Explorer högerklickar du på projektet och klickar sedan på **Hantera NuGet-paket**.

3. I NuGet Package Manager markerar du **Inkludera för hands version** och på sidan **Bläddra** i Sök efter **Microsoft. Azure. Management. ResourceManager**. Välj paketet, klicka på **Installera**, klicka på **OK** i **Granska ändringar** och klicka sedan på **Jag accepterar** för att acceptera licenserna.

4. Sök efter **Microsoft. IdentityModel. clients. ActiveDirectory** i NuGet Package Manager.  Klicka på **Installera**, klicka på **OK** i **Granska ändringar** och klicka sedan på **Jag accepterar** för att acceptera licensen.

5. Ersätt den befintliga **using** -satserna med följande kod i program. CS:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```

6. I program. cs lägger du till följande statiska variabler som ersätter plats hållar värden. Du har gjort en anteckning om **ApplicationId**, **SubscriptionId**, **TenantId** och **lösen ord** tidigare i den här självstudien. **Resurs gruppens namn** är namnet på den resurs grupp som du använder när du skapar IoT-hubben. Du kan använda en befintlig eller en ny resurs grupp. **IoT Hub namn** är namnet på det IoT Hub som du skapar, till exempel **MyIoTHub**. Namnet på din IoT Hub måste vara globalt unikt. **Distributions namnet** är ett namn för distributionen, t. ex. **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";

    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```
   
    [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Använd resurs leverantörs REST API för att skapa en IoT-hubb

Använd [IoT Hub Resource provider REST API](/rest/api/iothub/iothubresource) för att skapa en IoT-hubb i din resurs grupp. Du kan också använda resurs leverantörs REST API för att göra ändringar i en befintlig IoT-hubb.

1. Lägg till följande metod i program. CS:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Lägg till följande kod i **CreateIoTHub** -metoden. Den här koden skapar ett **httpclient** -objekt med autentiseringstoken i huvudena:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Lägg till följande kod i **CreateIoTHub** -metoden. Den här koden beskriver IoT Hub för att skapa och generera en JSON-representation. För den aktuella listan över platser som stöder IoT Hub se [Azure-status](https://azure.microsoft.com/status/):

    ```csharp
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };

    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Lägg till följande kod i **CreateIoTHub** -metoden. Den här koden skickar REST-begäran till Azure. Koden kontrollerar sedan svaret och hämtar URL: en som du kan använda för att övervaka distributions aktivitetens status:

    ```csharp
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;

    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }

    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Lägg till följande kod i slutet av **CreateIoTHub** -metoden. I den här koden används **asyncStatusUri** -adressen som hämtades i föregående steg för att vänta tills distributionen har slutförts:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Lägg till följande kod i slutet av **CreateIoTHub** -metoden. Den här koden hämtar nycklarna i IoT-hubben som du skapade och skriver ut dem i-konsolen:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Slutför och kör programmet

Du kan nu slutföra programmet genom att anropa metoden **CreateIoTHub** innan du skapar och kör det.

1. Lägg till följande kod i slutet av **main** -metoden:

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Klicka på **build** och **bygg sedan lösning**. Korrigera eventuella fel.

3. Klicka på **Felsök** och **starta sedan fel sökning** för att köra programmet. Det kan ta flera minuter innan distributionen körs.

4. Du kan kontrol lera att programmet har lagt till den nya IoT-hubben genom att gå till [Azure Portal](https://portal.azure.com/) och visa din lista över resurser. Du kan också använda PowerShell-cmdleten **Get-AzResource** .

> [!NOTE]
> Det här exempel programmet lägger till en S1-standardIoT Hub som du faktureras för. När du är klar kan du ta bort IoT-hubben via [Azure Portal](https://portal.azure.com/) eller genom att använda PowerShell-cmdleten **Remove-AzResource** när du är klar.

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat en IoT-hubb med hjälp av Resource Provider REST API kanske du vill utforska ytterligare:

* Läs om funktionerna i [IoT Hub Resource provider REST API](/rest/api/iothub/iothubresource).

* Läs [Azure Resource Manager översikt](../azure-resource-manager/management/overview.md) för att lära dig mer om funktionerna i Azure Resource Manager.

Mer information om hur du utvecklar för IoT Hub finns i följande artiklar:

* [Introduktion till C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK:er för Azure IoT](iot-hub-devguide-sdks.md)

För att ytterligare utforska funktionerna i IoT Hub, se:

* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/quickstart-linux.md)