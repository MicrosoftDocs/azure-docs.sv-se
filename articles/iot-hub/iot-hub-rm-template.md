---
title: Skapa en Azure-IoT Hub med hjälp av en mall (.NET) | Microsoft Docs
description: Hur du använder en Azure Resource Manager mall för att skapa ett IoT Hub med ett C#-program.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: db4b676e65d36a9476fd72b66cc8ccfa38af4d85
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92144494"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Skapa en IoT-hubb med Azure Resource Manager mall (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Du kan använda Azure Resource Manager för att skapa och hantera Azure IoT Hub program mässigt. Den här självstudien visar hur du använder en Azure Resource Manager mall för att skapa en IoT-hubb från ett C#-program.

> [!NOTE]
> Azure har två olika distributions modeller för att skapa och arbeta med resurser:  [Azure Resource Manager och klassisk](../azure-resource-manager/management/deployment-models.md).  Den här artikeln beskriver hur du använder Azure Resource Manager distributions modell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

För att göra den här självstudien behöver du följande:

* Visual Studio.
* Ett aktivt Azure-konto. <br/>Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.
* Ett [Azure Storage konto][lnk-storage-account] där du kan lagra dina Azure Resource Manager mallfiler.
* [Azure PowerShell 1,0][lnk-powershell-install] eller senare.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Förbered ditt Visual Studio-projekt

1. I Visual Studio skapar du ett Visual C#-klassiskt Windows klassiska Skriv bords projekt med hjälp av projekt mal len **konsol program (.NET Framework)** . Ge projektet namnet **CreateIoTHub**.

2. I Solution Explorer högerklickar du på projektet och klickar sedan på **Hantera NuGet-paket**.

3. I NuGet Package Manager markerar du **Inkludera för hands version** och på sidan **Bläddra** i Sök efter **Microsoft. Azure. Management. ResourceManager**. Välj paketet, klicka på **Installera**, klicka på **OK** i **Granska ändringar** och klicka sedan på **Jag accepterar** för att acceptera licenserna.

4. Sök efter **Microsoft. IdentityModel. clients. ActiveDirectory** i NuGet Package Manager.  Klicka på **Installera**, klicka på **OK** i **Granska ändringar** och klicka sedan på **Jag accepterar** för att acceptera licensen.

5. Ersätt den befintliga **using** -satserna med följande kod i program. CS:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. I program. cs lägger du till följande statiska variabler som ersätter plats hållar värden. Du har gjort en anteckning om **ApplicationId**, **SubscriptionId**, **TenantId** och **lösen ord** tidigare i den här självstudien. **Ditt Azure Storage konto namn** är namnet på det Azure Storage-konto där du lagrar Azure Resource Manager mallfiler. **Resurs gruppens namn** är namnet på den resurs grupp som du använder när du skapar IoT-hubben. Namnet kan vara en befintlig eller ny resurs grupp. **Distributions namnet** är ett namn för distributionen, t. ex. **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-a-template-to-create-an-iot-hub"></a>Skicka en mall för att skapa en IoT-hubb

Använd en JSON-mall och parameter fil för att skapa en IoT-hubb i din resurs grupp. Du kan också använda en Azure Resource Manager mall för att göra ändringar i en befintlig IoT-hubb.

1. I Solution Explorer högerklickar du på projektet, klickar på **Lägg till** och sedan på **nytt objekt**. Lägg till en JSON-fil med namnet **template.jspå** i projektet.

2. Om du vill lägga till en standard IoT-hubb till regionen **USA, östra** , ersätter du innehållet i **template.jspå** med följande resurs definition. För den aktuella listan över regioner som stöder IoT Hub se [Azure-status][lnk-status]:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. I Solution Explorer högerklickar du på projektet, klickar på **Lägg till** och sedan på **nytt objekt**. Lägg till en JSON-fil med namnet **parameters.jspå** i projektet.

4. Ersätt innehållet i **parameters.jspå** med följande parameter information som anger ett namn för den nya IoT-hubben, till exempel **{dina initialer} mynewiothub**. IoT Hub-namnet måste vara globalt unikt så att det ska innehålla ditt namn eller dina initialer:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

5. I **Server Explorer** ansluter du till din Azure-prenumeration och i ditt Azure Storage-konto skapar du en behållare som heter **templates**. I panelen **Egenskaper** anger du **offentlig Läs** behörighet för **mall** -behållaren till **BLOB**.

6. I **Server Explorer** högerklickar du på behållaren **mallar** och klickar sedan på **Visa BLOB-behållare**. Klicka på knappen **Ladda upp BLOB** , Välj de två filerna **parameters.jspå** och **templates.jspå** och klicka sedan på **Öppna** för att överföra JSON-filerna till behållaren **mallar** . URL: erna för de blobbar som innehåller JSON-data är:

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Lägg till följande metod i program. CS:

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. Lägg till följande kod i **CreateIoTHub** -metoden för att skicka mallen och parametervärdena till Azure Resource Manager:

    ```csharp
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

9. Lägg till följande kod i **CreateIoTHub** -metoden som visar status och nycklar för den nya IoT-hubben:

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Slutför och kör programmet

Du kan nu slutföra programmet genom att anropa metoden **CreateIoTHub** innan du skapar och kör det.

1. Lägg till följande kod i slutet av **main** -metoden:

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Klicka på **build** och **bygg sedan lösning**. Korrigera eventuella fel.

3. Klicka på **Felsök** och **starta sedan fel sökning** för att köra programmet. Det kan ta flera minuter innan distributionen körs.

4. Om du vill kontrol lera att programmet har lagt till den nya IoT-hubben går du till [Azure Portal][lnk-azure-portal] och visar listan över resurser. Du kan också använda PowerShell-cmdleten **Get-AzResource** .

> [!NOTE]
> Det här exempel programmet lägger till en S1-standardIoT Hub som du faktureras för. Du kan ta bort IoT-hubben via [Azure Portal][lnk-azure-portal] eller genom att använda PowerShell-cmdleten **Remove-AzResource** när du är klar.

## <a name="next-steps"></a>Nästa steg
Nu har du distribuerat en IoT-hubb med en Azure Resource Manager-mall med ett C#-program, men du kanske vill utforska ytterligare:

* Läs om funktionerna i [IoT Hub Resource provider REST API][lnk-rest-api].
* Läs [Azure Resource Manager översikt][lnk-azure-rm-overview] för att lära dig mer om funktionerna i Azure Resource Manager.
* För JSON-syntax och egenskaper som ska användas i mallar, se [resurs typer för Microsoft. Devices](/azure/templates/microsoft.devices/iothub-allversions).

Mer information om hur du utvecklar för IoT Hub finns i följande artiklar:

* [Introduktion till C SDK][lnk-c-sdk]
* [SDK:er för Azure IoT][lnk-sdks]

För att ytterligare utforska funktionerna i IoT Hub, se:

* [Distribuera AI till gränsenheter med Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: /rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/quickstart-linux.md