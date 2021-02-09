---
title: Använd ARM-mallen för att publicera Azure-IoT Hub, lagrings konto, dirigera meddelanden
description: Använd ARM-mallen för att publicera Azure-IoT Hub, lagrings konto, dirigera meddelanden
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: robinsh
ms.custom: mvc, subject-armqs
ms.openlocfilehash: fc8ddba2ec9b7bc9f1c2db8673ab805810afe17e
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981297"
---
# <a name="quickstart-deploy-an-azure-iot-hub-and-a-storage-account-using-an-arm-template"></a>Snabb start: Distribuera ett Azure-IoT Hub och ett lagrings konto med en ARM-mall

I den här snabb starten använder du en Azure Resource Manager mall (ARM-mall) för att skapa en IoT Hub som dirigerar meddelanden till Azure Storage och ett lagrings konto för att lagra meddelandena. När du har lagt till en virtuell IoT-enhet manuellt i hubben för att skicka meddelanden, konfigurerar du anslutnings informationen i ett program som kallas  *arm-Read-Write* för att skicka meddelanden från enheten till hubben. Hubben är konfigurerad så att meddelanden som skickas till hubben dirigeras automatiskt till lagrings kontot. I slutet av den här snabb starten kan du öppna lagrings kontot och se de meddelanden som skickas.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten anropas `101-iothub-auto-route-messages` från [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/101-iothub-auto-route-messages).

:::code language="json" source="~/quickstart-templates/101-iothub-auto-route-messages/azuredeploy.json":::

Två Azure-resurser definieras i mallen:

- [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft. Devices/IotHubs](/azure/templates/microsoft.devices/iothubs)

## <a name="deploy-the-template-and-run-the-sample-app"></a>Distribuera mallen och kör exempel appen

Det här avsnittet innehåller stegen för att distribuera mallen, skapa en virtuell enhet och köra arm-Read-Write-programmet för att skicka meddelanden.

1. Skapa resurserna genom att distribuera ARM-mallen.

    > [!TIP]
    > Klicka på knappen nedan för att starta distributionen av mallen. När den körs ställer du in arm-Read-Write-programmet att köra.

    [![Distribuera till Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

1. Ladda ned och zippa upp [IoT C#-exempel](/samples/azure-samples/azure-iot-samples-csharp/azure-iot-samples-for-csharp-net/).

1. Öppna ett kommando fönster och gå till mappen där du zippade IoT C#-exempel. Hitta mappen med filen arm-Read-Write. CSPROJ. Du skapar miljövariablerna i det här kommando fönstret. Logga in på [Azure Portal](https://portal.azure.com) för att hämta nycklarna. Välj **resurs grupper** och välj sedan den resurs grupp som används för den här snabb starten.

   ![Välj resurs grupp](./media/horizontal-arm-route-messages/01-select-resource-group.png)

1. Du ser IoT Hub-och lagrings kontot som skapades när du distribuerade ARM-mallen. Vänta tills mallen har distribuerats fullständigt innan du fortsätter. Välj sedan din resurs grupp för att se dina resurser.

   ![Visa resurser i resurs gruppen](./media/horizontal-arm-route-messages/02-view-resources-in-group.png)

1. Du behöver **hubbens namn**. Välj hubben i listan över resurser. Kopiera namnet på Hub överst i IoT Hub-avsnittet till Urklipp i Windows.

   ![Kopiera hubbens namn](./media/horizontal-arm-route-messages/03-copy-hub-name.png)

    Ersätt Hub-namnet i det här kommandot där det anges och kör kommandot i kommando fönstret:

    ```cmd
    SET IOT_HUB_URI=<hub name goes here>.azure-devices-net;
    ```

   Det här exemplet ser ut så här:

   ```cmd
   SET IOT_HUB_URI=ContosoTestHubdlxlud5h.azure-devices-net;
   ```

1. Nästa miljö variabel är IoT-enhetens nyckel. Lägg till en ny enhet i hubben genom att välja **IoT-enheter** på IoT Hub-menyn för hubben.

   ![Välj IoT-enheter](./media/horizontal-arm-route-messages/04-select-iot-devices.png)

1. På höger sida av skärmen väljer du **+ ny** för att lägga till en ny enhet.

   Fyll i det nya enhets namnet. I den här snabb starten används ett namn som börjar med **contoso-test-Device**. Spara enheten och öppna sedan skärmen igen för att hämta enhets nyckeln. (Nyckeln skapas åt dig när du stänger fönstret.) Välj antingen den primära eller sekundära nyckeln och kopiera den till Urklipp i Windows. I kommando fönstret anger du kommandot som ska köras och trycker sedan på **RETUR**. Kommandot bör se ut så här men med enhets nyckeln inklistrad i:

   ```cmd
   SET IOT_DEVICE_KEY=<device-key-goes-here>
   ```

1. Den sista miljövariabeln är **enhets-ID**. I kommando fönstret ställer du in kommandot och kör det.

   ```cmd
   SET IOT_DEVICE_ID=<device-id-goes-here>
   ```

   Detta ser ut så här:

   ```cmd
   SET IOT_DEVICE_ID=Contoso-Test-Device
   ```

1. Om du vill se de miljövariabler som du har definierat, skriver du in SET på kommando raden och trycker på **RETUR** och letar efter de som börjar med **IoT**.

   ![Se miljövariabler](./media/horizontal-arm-route-messages/06-environment-variables.png)

    Nu har miljövariablerna angetts, kör programmet från samma kommando fönster. Eftersom du använder samma fönster, är variablerna tillgängliga i minnet när du kör programmet.

1. Kör programmet genom att skriva följande kommando i kommando fönstret och trycka på **RETUR**.

    `dotnet run arm-read-write`

   Programmet genererar och visar meddelanden i-konsolen när de skickar varje meddelande till IoT Hub. Hubben konfigurerades i ARM-mallen för automatisk routning. Meddelanden som innehåller texten `level = storage` dirigeras automatiskt till lagrings kontot. Låt appen köras i 10 till 15 minuter och tryck sedan på **RETUR** en gång eller två gånger tills den slutar köras.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

1. Logga in på [Azure Portal](https://portal.azure.com) och Välj resurs gruppen och välj sedan lagrings kontot.

1. Öka detalj nivån för lagrings kontot tills du hittar filer.

   ![Titta på lagrings kontots filer](./media/horizontal-arm-route-messages/07-see-storage.png)

1. Välj en av filerna och välj **Ladda ned** och ladda ned filen till en plats som du kan hitta senare. Det kommer att ha ett namn som är numeriskt, till exempel 47. Lägg till _. txt_ i slutet och dubbelklicka sedan på filen för att öppna den.

1. När du öppnar filen är varje rad för ett annat meddelande. bröd texten i varje meddelande krypteras också. Det måste finnas för att du ska kunna utföra frågor mot meddelandets brödtext.

   ![Visa de meddelanden som skickats](./media/horizontal-arm-route-messages/08-messages.png)

   > [!NOTE]
   > Dessa meddelanden kodas i UTF-32 och base64. Om du läser tillbaka meddelandet måste du avkoda det från base64 och UTF-32 för att kunna läsa det som ASCII. Om du är intresse rad kan du använda metoden ReadOneRowFromFile i operationsföljden för att läsa en för från någon av dessa meddelandefiler och avkoda den till ASCII. ReadOneRowFromFile finns i den IoT C#-exempel lagrings plats som du zippade för den här snabb starten. Här är sökvägen överst i mappen: *./IoT-Hub/tutorials/routing/SimulatedDevice/program.cs.* Ange det booleska värdet `readTheFile` sant och hårdkoda sökvägen till filen på disken. den öppnas och översätts till den första raden i filen.

Du har distribuerat en ARM-mall för att skapa en IoT Hub och ett lagrings konto och köra ett program för att skicka meddelanden till hubben. Meddelandena lagras sedan automatiskt på lagrings kontot där de kan visas.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort de resurser som lagts till under den här snabb starten loggar du in på [Azure Portal](https://portal.azure.com). Välj **resurs grupper** och leta sedan reda på resurs gruppen som du använde för den här snabb starten. Välj resurs gruppen och välj sedan *ta bort*. Alla resurser i gruppen tas bort.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: skapa och distribuera din första ARM-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
