---
title: Självstudie – Använda Azure IoT Hub-meddelandeberikning
description: Självstudie som visar hur du använder meddelandeberikningar för Azure IoT Hub meddelanden
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 0d6c90120d050b6896161f50332faf447c3ed67b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788879"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Självstudie: Använda Azure IoT Hub-meddelandeberikning

*Meddelandeberikningar* beskriver möjligheten för Azure IoT Hub att *stämpla* meddelanden med ytterligare information innan meddelandena skickas till den avsedda slutpunkten. Ett skäl till att använda meddelandeberikningar är att inkludera data som kan användas för att förenkla bearbetningen nedströms. Att utöka enhettelemetrimeddelanden med en tagg för enhetstvilling kan till exempel minska belastningen på kunder för att göra API-anrop för enhetstvilling för den här informationen. Mer information finns i [Översikt över meddelandeberikningar.](iot-hub-message-enrichments-overview.md)

I den här självstudien får du se två sätt att skapa och konfigurera de resurser som behövs för att testa meddelandeberikelserna för en IoT-hubb. Resurserna innehåller ett lagringskonto med två lagringscontainrar. En container innehåller de berikade meddelandena och en annan container innehåller de ursprungliga meddelandena. En IoT-hubb ingår också för att ta emot meddelanden och dirigera dem till lämplig lagringscontainer baserat på om de är berikade eller inte.

* Den första metoden är att använda Azure CLI för att skapa resurserna och konfigurera meddelanderoutning. Sedan definierar du berikaningarna manuellt med hjälp av [Azure Portal](https://portal.azure.com).

* Den andra metoden är att använda en Azure Resource Manager för  att skapa både resurser och konfigurationer för meddelanderoutning och meddelandeberikningar.

När konfigurationerna för meddelanderoutning och meddelandeberikning är klara använder du ett program för att skicka meddelanden till IoT-hubben. Hubben dirigerar dem sedan till båda lagringscontainrarna. Endast meddelanden som skickas till slutpunkten för den **berikade** lagringscontainern berikas.

Här är de uppgifter du utför för att slutföra den här självstudien:

**Använda IoT Hub-meddelandeberikning**
> [!div class="checklist"]
> * Första metoden: Skapa resurser och konfigurera meddelanderoutning med hjälp av Azure CLI. Konfigurera meddelandeberikningarna manuellt med hjälp av [Azure Portal](https://portal.azure.com).
> * Andra metoden: Skapa resurser och konfigurera meddelanderoutning och meddelandeberikningar med hjälp av Resource Manager mall. 
> * Kör en app som simulerar en IoT-enhet som skickar meddelanden till hubben.
> * Visa resultatet och kontrollera att meddelandeberikningarna fungerar som förväntat.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Installera [Visual Studio](https://www.visualstudio.com/).

- Kontrollera att port 8883 är öppen i brandväggen. Enhetsexempel i den här självstudien använder MQTT-protokollet, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att komma runt det här problemet finns i [Ansluta till IoT Hub (MQTT).](iot-hub-mqtt-support.md#connecting-to-iot-hub)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>Hämta lagringsplatsen för IoT C#-exempel

Ladda ned [IoT C#-exemplen](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) från GitHub och packa upp dem. Den här lagringsplatsen har flera program, skript och Resource Manager mallar i den. De som ska användas för den här självstudien är följande:

* För den manuella metoden finns det ett CLI-skript som används för att skapa resurserna. Det här skriptet finns i /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Det här skriptet skapar resurserna och konfigurerar meddelanderoutning. När du har kört det här skriptet skapar du meddelandeberikelserna manuellt med hjälp [av Azure Portal](https://portal.azure.com).
* För den automatiserade metoden finns det en Azure Resource Manager mall. Mallen finns i /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.json. Den här mallen skapar resurserna, konfigurerar meddelanderoutning och konfigurerar sedan meddelandeberikningarna.
* Det tredje programmet du använder är enhetssimuleringsappen, som du använder för att skicka meddelanden till IoT-hubben och testa meddelandeberikningarna.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Konfigurera manuellt med hjälp av Azure CLI

Förutom att skapa nödvändiga resurser konfigurerar Azure CLI-skriptet även de två vägarna till slutpunkterna som är separata lagringscontainrar. Mer information om hur du konfigurerar meddelanderoutning finns i [självstudien Routning.](tutorial-routing.md) När resurserna har konfigurerats använder du Azure Portal för [att](https://portal.azure.com) konfigurera meddelandeberikningar för varje slutpunkt. Fortsätt sedan till teststeget.

> [!NOTE]
> Alla meddelanden dirigeras till båda slutpunkterna, men endast meddelanden som går till slutpunkten med konfigurerade meddelandeberikningar utökas.
>

Du kan använda skriptet som följer, eller så kan du öppna skriptet i mappen /resources på den nedladdade lagringsplatsen. Skriptet utför följande steg:

* Skapa en IoT-hubb.
* Skapa ett lagringskonto.
* Skapa två containrar i lagringskontot. En container är för berikade meddelanden och en annan container är för meddelanden som inte berikas.
* Konfigurera routning för de två olika lagringskontona:
    * Skapa en slutpunkt för varje lagringskontocontainer.
    * Skapa en väg till var och en av slutpunkterna för lagringskontots container.

Det finns flera resursnamn som måste vara globalt unika, till exempel namnet på IoT-hubben och lagringskontots namn. För att göra det enklare att köra skriptet läggs resursnamnen till med ett slumpmässigt alfanumeriskt värde som kallas *randomValue.* Det slumpmässiga värdet genereras en gång överst i skriptet. Den läggs till i resursnamnen efter behov i skriptet. Om du inte vill att värdet ska vara slumpmässigt kan du ange det till en tom sträng eller till ett specifikt värde.

Om du inte redan har gjort det öppnar du ett Azure [Cloud Shell-fönster](https://shell.azure.com) och ser till att det är inställt på Bash. Öppna skriptet i den uppackade lagringsplatsen, välj Ctrl + A för att markera allt och välj sedan Ctrl + C för att kopiera det. Du kan också kopiera följande CLI-skript eller öppna det direkt i Cloud Shell. Klistra in skriptet i Cloud Shell genom att högerklicka på kommandoraden och välja Klistra **in**. Skriptet kör en instruktion i taget. När skriptet har stoppat väljer du **Retur** för att kontrollera att det kör det sista kommandot. Följande kodblock visar det skript som används, med kommentarer som förklarar vad det gör.

Här är de resurser som skapas av skriptet. *Berikad* innebär att resursen är för meddelanden med berikande information. *Ursprungligt* innebär att resursen är för meddelanden som inte berikas.

| Name | Värde |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| containernamn | Ursprungliga  |
| containernamn | Berikad  |
| Namn på IoT-enhet | Contoso-Test-Device |
| IoT Hub namn | ContosoTestHubMsgEn |
| lagringskontonamn | contosostorage |
| slutpunktens namn 1 | ContosoStorageEndpointOriginal |
| slutpunktens namn 2 | ContosoStorageEndpointEnriched|
| vägnamn 1 | ContosoStorageRouteOriginal |
| route Name 2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-iot

# Set the values for the resource names that
#   don't have to be globally unique.
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
# The storage account name must be globally unique,
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key.
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for the first container, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for the second container, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

Nu är alla resurser konfigurerade och meddelanderoutning har konfigurerats. Du kan visa konfigurationen för meddelanderoutning i portalen och konfigurera meddelandeberikningarna för meddelanden som går till den **berikade** lagringscontainern.

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Konfigurera meddelandeberikningarna manuellt med hjälp av Azure Portal

1. Gå till din IoT-hubb genom att välja **Resursgrupper.** Välj sedan den resursgrupp som har ställts in för den här **självstudien ( ContosoResourcesMsgEn**). Leta upp IoT-hubben i listan och välj den. Välj **Meddelanderoutning** för IoT-hubben.

   ![Välj meddelanderoutning](./media/tutorial-message-enrichments/select-iot-hub.png)

   Meddelanderoutningsfönstret har tre flikar med **etiketten Vägar,** **Anpassade slutpunkter** och **Berika meddelanden.** Bläddra bland de två första flikarna för att se konfigurationen som har ställts in av skriptet. Använd den tredje fliken för att lägga till meddelandeberikningar. Nu ska vi utöka meddelanden som går till slutpunkten för lagringscontainern **enriched**. Fyll i namn och värde och välj sedan slutpunkten **ContosoStorageEndpointEnriched** i listrutan. Här är ett exempel på hur du ställer in en berikning som lägger till namnet på IoT-hubben i meddelandet:

   ![Lägg till första berikande](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Lägg till dessa värden i listan för contosoStorageEndpointEnriched-slutpunkten.

   | Tangent | Värde | Slutpunkt (listrutan) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | DeviceLocation | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |Customerid | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Om enheten inte har en tvilling stämplas värdet som du lägger här som en sträng för värdet i meddelandeberikningarna. Om du vill se information om enhetstvillingen går du till hubben i portalen och väljer **IoT-enheter.** Välj din enhet och välj sedan **Enhetstvilling** överst på sidan.
   >
   > Du kan redigera tvillinginformationen för att lägga till taggar, till exempel plats, och ange den till ett specifikt värde. Mer information finns i [Understand and use device twins in IoT Hub](iot-hub-devguide-device-twins.md) (Förstå och använda enhetstvillingar i IoT Hub).

3. När du är klar bör fönstret se ut ungefär så här:

   ![Tabell med alla berikanden tillagda](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Spara **ändringarna genom** att välja Tillämpa. Gå vidare till [avsnittet Testmeddelandeberikningar.](#test-message-enrichments)

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Skapa och konfigurera med en Resource Manager mall
Du kan använda en Resource Manager för att skapa och konfigurera resurser, meddelanderoutning och meddelandeberikningar.

1. Logga in på Azure-portalen. Välj **+ Skapa en resurs** för att öppna en sökruta. Ange *malldistribution* och sök efter den. I resultatfönstret väljer du Malldistribution **(distribuera med anpassad mall).**

   ![Malldistribution i Azure Portal](./media/tutorial-message-enrichments/template-select-deployment.png)

1. Välj **Skapa** i **Malldistribution** fönstret.

1. I fönstret **Anpassad distribution** väljer du Skapa en egen mall **i redigeraren**.

1. I fönstret **Redigera mall** väljer du Läs **in fil**. Utforskaren visas. Leta upp **template_messageenrichments.jspå** filen i den uppackade lagringsplatsen i **/iot-hub/Tutorials/Routing/SimulatedDevice/resources**. 

   ![Välj mall från den lokala datorn](./media/tutorial-message-enrichments/template-select.png)

1. Välj **Öppna** för att läsa in mallfilen från den lokala datorn. Den läses in och visas i redigeringsfönstret.

   Den här mallen är konfigurerad att använda ett globalt unikt IoT-hubbnamn och lagringskontonamn genom att lägga till ett slumpmässigt värde i slutet av standardnamnen, så att du kan använda mallen utan att göra några ändringar i den.

   Här är de resurser som skapas genom att läsa in mallen. **Berikad** innebär att resursen är till för meddelanden med berikande information. **Ursprungligt** innebär att resursen är för meddelanden som inte berikas. Det här är samma värden som används i Azure CLI-skriptet.

   | Name | Värde |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | containernamn | Ursprungliga  |
   | containernamn | Berikad  |
   | IoT-enhetsnamn | Contoso-Test-Device |
   | IoT Hub namn | ContosoTestHubMsgEn |
   | lagringskontonamn | contosostorage |
   | slutpunktens namn 1 | ContosoStorageEndpointOriginal |
   | slutpunktens namn 2 | ContosoStorageEndpointEnriched|
   | vägnamn 1 | ContosoStorageRouteOriginal |
   | route Name 2 | ContosoStorageRouteEnriched |

1. Välj **Spara**. Fönstret **Anpassad distribution** visas och visar alla parametrar som används av mallen. Det enda fält som du behöver ange är **Resursgrupp**. Skapa antingen en ny eller välj en från listrutan.

   Här är den övre delen av **fönstret Anpassad** distribution. Du kan se var du fyller i resursgruppen.

   ![Övre halvan av fönstret Anpassad distribution](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Här är den nedre halvan av **fönstret Anpassad** distribution. Du kan se resten av parametrarna och villkoren. 

   ![Nedre halvan av fönstret Anpassad distribution](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Markera kryssrutan för att godkänna villkoren. Välj sedan **Köp för** att fortsätta med malldistributionen.

1. Vänta tills mallen har distribuerats fullständigt. Välj klockikonen överst på skärmen för att kontrollera förloppet. När det är klart fortsätter du till [avsnittet Test message enrichments (Testa meddelandeberikningar).](#test-message-enrichments)

## <a name="test-message-enrichments"></a>Testa meddelandeberikningar

Om du vill visa meddelandeberikelserna väljer **du Resursgrupper**. Välj sedan den resursgrupp som du använder för den här självstudien. Välj IoT-hubben i listan över resurser och gå till **Meddelandefunktion.** Meddelanderoutningskonfigurationen och de konfigurerade berikandena visas.

Nu när meddelandeberikningarna har konfigurerats för slutpunkten kör du programmet Simulerad enhet för att skicka meddelanden till IoT-hubben. Hubben konfigurerades med inställningar som utför följande uppgifter:

* Meddelanden som dirigeras till lagringsslutpunkten ContosoStorageEndpointOriginal berikas inte och lagras i lagringscontainern `original` .

* Meddelanden som dirigeras till lagringsslutpunkten ContosoStorageEndpointEnriched berikas och lagras i lagringscontainern `enriched` .

Programmet Simulerad enhet är ett av programmen i den uppackade nedladdningen. Programmet skickar meddelanden för var och en av de olika metoderna för meddelanderoutning i [självstudien Routning](tutorial-routing.md), som innehåller Azure Storage.

Dubbelklicka på lösningsfilen **IoT_SimulatedDevice.sln** för att öppna koden i Visual Studio och öppna **sedan Program.cs**. Ersätt IoT-hubbnamnet för markören `{your hub name}` . Formatet för IoT-hubbens värdnamn är **{ditt hubbnamn}.azure-devices.net**. I den här självstudien är hubbens värdnamn ContosoTestHubMsgEn.azure-devices.net. Ersätt sedan enhetsnyckeln som du sparade tidigare när du körde skriptet för att skapa resurserna för markören `{your device key}` .

Om du inte har enhetsnyckeln kan du hämta den från portalen. När du har loggat in går **du till Resursgrupper,** väljer din resursgrupp och väljer sedan din IoT-hubb. Titta under **IoT-enheter** för testenheten och välj din enhet. Välj kopieringsikonen bredvid **Primärnyckel för** att kopiera den till Urklipp.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Kör och testa

Kör konsolprogrammet i några minuter. Meddelandena som skickas visas på programmets konsolskärm.

Appen skickar ett nytt enhet till molnet-meddelande till IoT-hubben varje sekund. Meddelandet innehåller ett JSON-serialiserat objekt med enhets-ID, temperatur, luftfuktighet och meddelandenivå, där standardinställningen är `normal`. Den tilldelar slumpmässigt en nivå på eller , vilket gör att meddelandet dirigeras till `critical` `storage` lagringskontot eller till standardslutpunkten. Meddelandena som skickas till **den berikade** containern i lagringskontot berikas.

När flera lagringsmeddelanden har skickats kan du visa data.

1. Välj **Resursgrupper**. Leta upp resursgruppen **ContosoResourcesMsgEn** och välj den.

2. Välj ditt lagringskonto, **som är contosostorage**. Välj sedan **Storage Explorer (förhandsversion)** i den vänstra rutan.

   ![Välj Storage Explorer](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Välj **BLOBCONTAINRAR** för att se de två containrar som kan användas.

   ![Se containrarna i lagringskontot](./media/tutorial-message-enrichments/show-blob-containers.png)

Meddelandena i containern med namnet **enriched** innehåller meddelandeberikaderna i meddelandena. Meddelandena i containern som **kallas ursprungliga** har rådatameddelanden utan berikning. Öka detaljstorleken i en av containrarna tills du kommer längst ned och öppna den senaste meddelandefilen. Gör sedan samma sak för den andra containern för att kontrollera att inga berikande uppgifter har lagts till i meddelanden i containern.

När du tittar på meddelanden som har berikats bör du se "min IoT Hub" med hubbnamnet och platsen och kund-ID:t, så här:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Här är ett ej berikat meddelande. Observera att "min IoT Hub", "devicelocation" och "customerID" inte visas här eftersom dessa fält läggs till av berikandena. Den här slutpunkten har inga berikanden.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort alla resurser som du skapade i den här självstudien tar du bort resursgruppen. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet tar du bort IoT-hubben, lagringskontot och själva resursgruppen.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Rensa resurser med hjälp av Azure CLI

Om du vill ta bort resursgruppen använder du kommandot [az group delete](/cli/azure/group#az_group_delete). Kom ihåg `$resourceGroup` att var **inställt på ContosoResourcesMsgEn i** början av den här självstudien.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerade och testade du att lägga till IoT Hub meddelanden med hjälp av följande steg:

**Använda IoT Hub-meddelandeberikning**

> [!div class="checklist"]
> * Första metoden: Skapa resurser och konfigurera meddelanderoutning med hjälp av Azure CLI. Konfigurera meddelandeberikningarna manuellt med hjälp av [Azure Portal](https://portal.azure.com).
> * Andra metoden: Skapa resurser och konfigurera meddelanderoutning och meddelandeberikningar med hjälp av en Azure Resource Manager mall.
> * Kör en app som simulerar en IoT-enhet som skickar meddelanden till hubben.
> * Visa resultatet och kontrollera att meddelandeberikningarna fungerar som förväntat.

Mer information om meddelandeberikningar finns i [Översikt över meddelandeberikningar.](iot-hub-message-enrichments-overview.md)

Mer information om meddelanderoutning finns i följande artiklar:

> [!div class="nextstepaction"]
> [Använd IoT Hub för att skicka enhet-till-moln-meddelanden till olika slutpunkter](iot-hub-devguide-messages-d2c.md)

> [!div class="nextstepaction"]
> [Självstudie: IoT Hub routning](tutorial-routing.md)