---
title: Snabb start – skicka telemetri till Azure IoT Hub (CLI) snabb start
description: I den här snabb starten visas nya utvecklare som IoT Hub hur du kommer igång med Azure CLI för att skapa en IoT-hubb, skicka telemetri och Visa meddelanden mellan en enhet och hubben.
ms.service: iot-hub
ms.topic: quickstart
ms.custom:
- iot-send-telemetry-cli
- iot-p0-scenario
- 'Role: Cloud Development'
- devx-track-azurecli
ms.author: timlt
author: timlt
ms.date: 11/06/2019
ms.openlocfilehash: 20e7998b4d0ec5a36f8fb8f1ddb04d591c54542b
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624277"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-monitor-it-with-the-azure-cli"></a>Snabb start: skicka telemetri från en enhet till en IoT-hubb och övervaka den med Azure CLI

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. I den här snabb starten använder du Azure CLI för att skapa en IoT Hub och en simulerad enhet, skicka telemetri till hubben och skicka ett meddelande från moln till enhet. Du kan också använda Azure Portal för att visualisera enhets mått. Det här är ett grundläggande arbets flöde för utvecklare som använder CLI för att interagera med ett IoT Hub-program.

## <a name="prerequisites"></a>Förutsättningar
- Om du inte har en Azure-prenumeration kan du [skapa en kostnads fri](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Azure CLI. Du kan köra alla kommandon i den här snabb starten med hjälp av Azure Cloud Shell, ett interaktivt CLI-gränssnitt som körs i din webbläsare. Om du använder Cloud Shell behöver du inte installera något. Om du föredrar att använda CLI lokalt kräver den här snabb starten Azure CLI version 2.0.76 eller senare. Kör az --version för att se versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal
Logga in på Azure Portal på https://portal.azure.com.

Oavsett om du kör CLI lokalt eller i Cloud Shell bör du låta portalen vara öppen i webbläsaren.  Du använder det senare i den här snabb starten.

## <a name="launch-the-cloud-shell"></a>Starta Cloud Shell
I det här avsnittet startar du en instans av Azure Cloud Shell. Om du använder CLI lokalt går du vidare till avsnittet [förbereda två CLI-sessioner](#prepare-two-cli-sessions).

Så här startar du Cloud Shell:

1. Välj knappen **Cloud Shell** i den övre högra meny raden i Azure Portal. 

    ![Knappen Azure Portal Cloud Shell](media/quickstart-send-telemetry-cli/cloud-shell-button.png)

    > [!NOTE]
    > Om det här är första gången du använder Cloud Shell, så blir du ombedd att skapa lagring, vilket krävs för att använda Cloud Shell.  Välj en prenumeration för att skapa ett lagrings konto och Microsoft Azure fil resurs. 

2. Välj önskad CLI-miljö i list rutan **Välj miljö** . I den här snabb starten används **bash** -miljön. Alla följande CLI-kommandon fungerar i PowerShell-miljön. 

    ![Välj CLI-miljö](media/quickstart-send-telemetry-cli/cloud-shell-environment.png)

## <a name="prepare-two-cli-sessions"></a>Förbereda två CLI-sessioner

I det här avsnittet förbereder du två Azure CLI-sessioner. Om du använder Cloud Shell kan du köra de två sessionerna i separata flikar i webbläsaren. Om du använder en lokal CLI-klient kan du köra två separata CLI-instanser. Du använder den första sessionen som en simulerad enhet och den andra sessionen för att övervaka och skicka meddelanden. Om du vill köra ett kommando väljer du **Kopiera** för att kopiera ett kodblock i den här snabb starten, klistra in det i din Shell-session och kör det.

Azure CLI kräver att du är inloggad på ditt Azure-konto. All kommunikation mellan Azure CLI-sessionen och din IoT Hub autentiseras och krypteras. Därför behöver inte den här snabb starten ytterligare autentisering som du använder med en riktig enhet, till exempel en anslutnings sträng.

*  Kör kommandot [AZ Extension Add](/cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true) för att lägga till Microsoft Azure IoT-tillägget för Azure CLI till CLI-gränssnittet. IOT-tillägget lägger till IoT Hub-, IoT Edge-och IoT Device Provisioning-tjänst (DPS)-kommandon i Azure CLI.

   ```azurecli
   az extension add --name azure-iot
   ```
   
   När du har installerat Azure IOT-tillägget behöver du inte installera det igen under en Cloud Shell session. 

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

*  Öppna en andra CLI-session.  Om du använder Cloud Shell väljer du **Öppna ny session**. Om du använder CLI lokalt öppnar du en andra instans. 

    >[!div class="mx-imgBorder"]
    >![Öppna ny Cloud Shell-session](media/quickstart-send-telemetry-cli/cloud-shell-new-session.png)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub
I det här avsnittet använder du Azure CLI för att skapa en resurs grupp och en IoT Hub.  En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. En IoT Hub fungerar som en central meddelande hubb för dubbelriktad kommunikation mellan IoT-programmet och enheterna. 

> [!TIP]
> Du kan också skapa en Azure-resurs grupp, en IoT Hub och andra resurser med hjälp av [Azure Portal](iot-hub-create-through-portal.md), [Visual Studio Code](iot-hub-create-use-iot-toolkit.md)eller andra programmerings metoder.  

1. Kör kommandot [AZ Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create&preserve-view=true) för att skapa en resurs grupp. Följande kommando skapar en resurs grupp med namnet *MyResourceGroup* på den *östra* platsen. 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Kör kommandot [AZ IoT Hub Create](/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-create&preserve-view=true) för att skapa en IoT-hubb. Det kan ta några minuter att skapa en IoT-hubb. 

    *YourIotHubName*. Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb. Ett IoT Hub-namn måste vara globalt unikt i Azure. Den här plats hållaren används i resten av den här snabb starten för att representera namnet på din IoT-hubb.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-and-monitor-a-device"></a>Skapa och övervaka en enhet
I det här avsnittet skapar du en simulerad enhet i den första CLI-sessionen. Den simulerade enheten skickar telemetri till IoT-hubben. I den andra CLI-sessionen övervakar du händelser och telemetri och skickar ett meddelande från moln till enhet till den simulerade enheten.

Skapa och starta en simulerad enhet:
1. Kör kommandot [AZ IoT Hub Device-Identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest#ext-azure-iot-az-iot-hub-device-identity-create&preserve-view=true) i den första CLI-sessionen. Detta skapar den simulerade enhets identiteten. 

    *YourIotHubName*. Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb. 

    *simDevice*. Du kan använda det här namnet direkt för den simulerade enheten i resten av den här snabb starten. Du kan också använda ett annat namn. 

    ```azurecli
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName} 
    ```

1. Kör kommandot [AZ IoT Device simulering](/cli/azure/ext/azure-iot/iot/device?view=azure-cli-latest#ext-azure-iot-az-iot-device-simulate&preserve-view=true) i den första CLI-sessionen.  Detta startar den simulerade enheten. Enheten skickar telemetri till din IoT-hubb och tar emot meddelanden från den.  

    *YourIotHubName*. Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

Övervaka en enhet:
1. I den andra CLI-sessionen kör du kommandot [AZ IoT Hub Monitor-Events](/cli/azure/ext/azure-iot/iot/hub?view=azure-cli-latest#ext-azure-iot-az-iot-hub-monitor-events&preserve-view=true) . Detta börjar övervaka den simulerade enheten. Utdata visar telemetri som den simulerade enheten skickar till IoT-hubben.

    *YourIotHubName*. Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb. 

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

    ![Cloud Shell övervaka händelser](media/quickstart-send-telemetry-cli/cloud-shell-monitor.png)

1. När du har övervakat den simulerade enheten i den andra CLI-sessionen trycker du på CTRL + C för att stoppa övervakningen. 

## <a name="use-the-cli-to-send-a-message"></a>Använda CLI för att skicka ett meddelande
I det här avsnittet använder du den andra CLI-sessionen för att skicka ett meddelande till den simulerade enheten.

1. I den första CLI-sessionen bekräftar du att den simulerade enheten körs. Om enheten har stoppats kör du följande kommando för att starta den:

    *YourIotHubName*. Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. I den andra CLI-sessionen kör du kommandot [AZ IoT Device C2D-Message Send](/cli/azure/ext/azure-iot/iot/device/c2d-message?view=azure-cli-latest#ext-azure-iot-az-iot-device-c2d-message-send&preserve-view=true) . Detta skickar ett meddelande från moln till enhet från IoT-hubben till den simulerade enheten. Meddelandet innehåller en sträng och två nyckel/värde-par.  

    *YourIotHubName*. Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb. 

    ```azurecli
    az iot device c2d-message send -d simDevice --data "Hello World" --props "key0=value0;key1=value1" -n {YourIoTHubName}
    ```
    Du kan också skicka meddelanden från moln till enhet med hjälp av Azure Portal. Det gör du genom att gå till sidan Översikt för IoT Hub, välja **IoT-enheter**, välja den simulerade enheten och välja **meddelande till enhet**. 

1. I den första CLI-sessionen bekräftar du att den simulerade enheten tog emot meddelandet. 

    ![Cloud Shell meddelande från moln till enhet](media/quickstart-send-telemetry-cli/cloud-shell-receive-message.png)

1. När du har granskat meddelandet stänger du den andra CLI-sessionen. Se till att den första CLI-sessionen är öppen. Du kan använda den för att rensa resurser i ett senare steg.

## <a name="view-messaging-metrics-in-the-portal"></a>Visa meddelande mått i portalen
Med Azure Portal kan du hantera alla aspekter av dina IoT Hub och enheter. I ett typiskt IoT Hub-program som matar in telemetri från enheter kanske du vill övervaka enheter eller visa mått för telemetri för enheter. 

För att visualisera meddelande mått i Azure Portal:
1. Välj **alla resurser** i den vänstra navigerings menyn i portalen. Här visas alla resurser i din prenumeration, inklusive IoT-hubben som du skapade. 

1. Välj länken på IoT-hubben som du skapade. Portalen visar navets översikts sida.

1. Välj **mått** i den vänstra rutan i IoT Hub. 

    ![IoT Hub meddelande mått](media/quickstart-send-telemetry-cli/iot-hub-portal-metrics.png)

1. Ange ditt IoT Hub-namn i **omfånget**.

2. Välj *standard mått för IoT Hub* i **mått namn området**.

3. Välj det *totala antalet meddelanden som används* i **måttet**. 

4. Hovra med mus pekaren över det del av tids linjen där enheten skickade meddelanden. Det totala antalet meddelanden vid en tidpunkt visas i det nedre vänstra hörnet på tids linjen.

    ![Visa Azure IoT Hub mått](media/quickstart-send-telemetry-cli/iot-hub-portal-view-metrics.png)

5. Du kan också använda List rutan **mått** om du vill visa andra mått på den simulerade enheten. Till exempel *levererar C2D-meddelanden slutförda* eller *totala enheter (för hands version)*. 

## <a name="clean-up-resources"></a>Rensa resurser
Om du inte längre behöver de Azure-resurser som skapats i den här snabb starten kan du ta bort dem med hjälp av Azure CLI.

Om du fortsätter till nästa rekommenderade artikel kan du behålla de resurser som du redan har skapat och återanvända dem. 

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. 

Ta bort en resursgrupp med namnet:
1. Kör kommandot [AZ Group Delete](/cli/azure/group?view=azure-cli-latest#az-group-delete&preserve-view=true) . Detta tar bort resurs gruppen, IoT Hub och den enhets registrering som du skapade.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Kör kommandot [AZ Group List](/cli/azure/group?view=azure-cli-latest#az-group-list&preserve-view=true) för att bekräfta att resurs gruppen har tagits bort.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Nästa steg
I den här snabb starten använde du Azure CLI för att skapa en IoT-hubb, skapa en simulerad enhet, skicka telemetri, övervaka telemetri, skicka ett meddelande från moln till enhet och rensa resurser. Du använde Azure Portal för att visualisera meddelande mått på enheten.

Om du är en enhets utvecklare är det föreslagna nästa steg att se snabb starten för telemetri som använder Azure IoT-enhetens SDK för C. du kan också se en av de tillgängliga Azure IoT Hub telemetri-artiklarna för telemetri på önskat språk eller SDK.

> [!div class="nextstepaction"]
> [Snabb start: skicka telemetri från en enhet till en IoT Hub (C)](quickstart-send-telemetry-c.md)