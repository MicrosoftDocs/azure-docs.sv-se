---
title: Självstudie – använda Azure IoT Hub meddelande-anrikning
description: Självstudie som visar hur du använder meddelande anrikninger för Azure IoT Hub-meddelanden
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 7ae85fa2559b8313bd35a761945bff946563e935
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199789"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Självstudie: Använd Azure IoT Hub meddelande-anrikning

*Meddelande anrikninger* beskriver möjligheten för Azure IoT Hub att *stämpla* meddelanden med ytterligare information innan meddelandena skickas till den angivna slut punkten. En anledning till att använda meddelande berikare är att inkludera data som kan användas för att förenkla bearbetningen i den underordnade bearbetningen. För att till exempel kunna identifiera enhets telemetri med en enhets rik tagg kan kunderna minska belastningen på kunder för att göra enhetens dubbla API-anrop för den här informationen. Mer information finns i [Översikt över meddelande anrikninger](iot-hub-message-enrichments-overview.md).

I den här självstudien får du se två sätt för att skapa och konfigurera de resurser som behövs för att testa meddelande berikarna för en IoT-hubb. Resurserna innehåller ett lagrings konto med två lagrings behållare. En behållare innehåller de berikade meddelandena och en annan behållare innehåller de ursprungliga meddelandena. Dessutom ingår en IoT-hubb för att ta emot meddelanden och dirigera dem till lämplig lagrings behållare baserat på om de är omfattande eller inte.

* Den första metoden är att använda Azure CLI för att skapa resurser och konfigurera meddelanderoutning. Sedan definierar du anrikningerna manuellt med hjälp av [Azure Portal](https://portal.azure.com).

* Den andra metoden är att använda en Azure Resource Manager mall för att skapa både resurserna *och* konfigurationerna för meddelanderoutning och meddelande berikare.

När konfigurationerna för meddelanderoutning och meddelande förbrukare har avslut ATS använder du ett program för att skicka meddelanden till IoT Hub. Hubben dirigerar sedan dem till båda lagrings behållarna. Endast meddelanden som skickas till slut punkten för den **berikade** lagrings behållaren är omfattande.

Här är de uppgifter du utför för att slutföra den här självstudien:

**Använda IoT Hub meddelande anrikning**
> [!div class="checklist"]
> * Första metoden: skapa resurser och konfigurera meddelanderoutning med hjälp av Azure CLI. Konfigurera meddelande berikarna manuellt med hjälp av [Azure Portal](https://portal.azure.com).
> * Andra metoden: skapa resurser och konfigurera meddelanderoutning och meddelande berikare med hjälp av en Resource Manager-mall. 
> * Kör en app som simulerar en IoT-enhet som skickar meddelanden till hubben.
> * Visa resultaten och kontrol lera att meddelandets anrikning fungerar som förväntat.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Installera [Visual Studio](https://www.visualstudio.com/).

- Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här självstudien använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>Hämta databas för IoT C#-exempel

Ladda ned [IoT C#-exemplen](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) från GitHub och packa upp dem. Den här lagrings platsen har flera program, skript och Resource Manager-mallar. De som ska användas för de här självstudierna är följande:

* För den manuella metoden finns det ett CLI-skript som används för att skapa resurserna. Det här skriptet finns i/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli. azcli. Det här skriptet skapar resurserna och konfigurerar meddelanderoutning. När du har kört skriptet skapar du meddelandena manuellt med hjälp av [Azure Portal](https://portal.azure.com).
* För den automatiserade metoden finns det en Azure Resource Manager-mall. Mallen finns i/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.js. Den här mallen skapar resurserna, konfigurerar meddelanderoutning och konfigurerar sedan meddelande Förvarningen.
* Det tredje program du använder är appen för enhets simulering, som du använder för att skicka meddelanden till IoT-hubben och testa meddelande berikarna.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Konfigurera och konfigurera manuellt med hjälp av Azure CLI

Förutom att skapa nödvändiga resurser konfigurerar Azure CLI-skriptet även de två vägarna till de slut punkter som är separata lagrings behållare. Mer information om hur du konfigurerar meddelanderoutning finns i [själv studie kursen om routning](tutorial-routing.md). När resurserna har kon figurer ATS använder du [Azure Portal](https://portal.azure.com) för att konfigurera meddelande anrikninger för varje slut punkt. Fortsätt sedan till test steget.

> [!NOTE]
> Alla meddelanden dirigeras till båda slut punkterna, men endast meddelanden som skickas till slut punkten med konfigurerade meddelande berikar kommer att berikas.
>

Du kan använda skriptet som följer eller så kan du öppna skriptet i/Resources-mappen för den hämtade lagrings platsen. Skriptet utför följande steg:

* Skapa en IoT-hubb.
* Skapa ett lagringskonto.
* Skapa två behållare i lagrings kontot. En behållare är för de berikade meddelandena och en annan behållare är för meddelanden som inte är omfattande.
* Konfigurera routning för de två olika lagrings kontona:
    * Skapa en slut punkt för varje lagrings konto behållare.
    * Skapa en väg för varje slut punkt för lagrings konto behållare.

Det finns flera resurs namn som måste vara globalt unika, till exempel IoT Hub-namn och lagrings konto namn. För att göra det enklare att köra skriptet läggs dessa resurs namn till med ett slumpmässigt alfanumeriskt värde som kallas *randomValue*. Det slumpmässiga värdet genereras en gång överst i skriptet. Den läggs till i resurs namnen vid behov i hela skriptet. Om du inte vill att värdet ska vara slumpmässigt kan du ange det som en tom sträng eller till ett speciellt värde.

Om du inte redan har gjort det öppnar du ett Azure [Cloud Shell-fönster](https://shell.azure.com) och ser till att det är inställt på bash. Öppna skriptet i den zippade lagrings platsen, Välj CTRL + A för att markera alla och välj sedan Ctrl + C för att kopiera det. Du kan också kopiera följande CLI-skript eller öppna det direkt i Cloud Shell. Klistra in skriptet i Cloud Shell fönstret genom att högerklicka på kommando raden och välja **Klistra in**. Skriptet kör en instruktion i taget. När skriptet har körts klart väljer du **RETUR** för att kontrol lera att det kör det senaste kommandot. Följande kod block visar det skript som används, med kommentarer som förklarar vad det gör.

Här följer resurserna som skapats av skriptet. *Förrikat* innebär att resursen är för meddelanden med anrikninger. *Original* innebär att resursen avser meddelanden som inte är berikade.

| Namn | Värde |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| container namn | originalspråket  |
| container namn | avancerad och  |
| IoT-enhetens namn | Contoso-test – enhet |
| IoT Hub namn | ContosoTestHubMsgEn |
| lagrings konto namn | contosostorage |
| slut punkts namn 1 | ContosoStorageEndpointOriginal |
| slut punkts namn 2 | ContosoStorageEndpointEnriched|
| flödes namn 1 | ContosoStorageRouteOriginal |
| flödes namn 2 | ContosoStorageRouteEnriched |

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

I det här läget är resurserna alla konfigurerade och meddelande dirigeringen har kon figurer ATS. Du kan visa konfigurationen för meddelanderoutning i portalen och konfigurera meddelande Förvarningen för meddelanden till den **berikade** lagrings behållaren.

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Konfigurera meddelande anrikningen manuellt med hjälp av Azure Portal

1. Gå till din IoT-hubb genom att välja **resurs grupper**. Välj sedan den resurs grupp som har kon figurer ATS för den här självstudien (**ContosoResourcesMsgEn**). Hitta IoT Hub i listan och markera den. Välj **meddelanderoutning** för IoT Hub.

   ![Välj meddelanderoutning](./media/tutorial-message-enrichments/select-iot-hub.png)

   Fönstret meddelanderoutning innehåller tre flikar med namnet **vägar**, **anpassade slut punkter** och **utöka meddelanden**. Bläddra bland de första två flikarna för att se konfigurationen som konfigureras av skriptet. Använd den tredje fliken för att lägga till meddelande berikare. Nu ska vi utöka meddelanden till slut punkten för den lagrings behållare som heter **berikad**. Fyll i namnet och värdet och välj sedan slut punkts **ContosoStorageEndpointEnriched** i list rutan. Här är ett exempel på hur du konfigurerar en anrikning som lägger till IoT Hub-namnet i meddelandet:

   ![Lägg till första berikning](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Lägg till de här värdena i listan för ContosoStorageEndpointEnriched-slutpunkten.

   | Tangent | Värde | Slut punkt (nedrullningsbar lista) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | DeviceLocation | $twin. taggar. location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |Kund | 6ce345b8-1e4a-411E-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Om enheten inte har någon enhet, så stämplas värdet du anger som en sträng för värdet i meddelandets berikare. Om du vill se enhetens dubbla information går du till din hubb i portalen och väljer **IoT-enheter**. Välj din enhet och välj sedan **enhet** , högst upp på sidan.
   >
   > Du kan redigera den dubbla informationen för att lägga till taggar, till exempel plats, och ange det till ett särskilt värde. Mer information finns i [Understand and use device twins in IoT Hub](iot-hub-devguide-device-twins.md) (Förstå och använda enhetstvillingar i IoT Hub).

3. När du är klar bör fönstret se ut ungefär så här:

   ![Tabell med alla omfattande tillägg](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Välj **Verkställ** för att spara ändringarna. Gå vidare till avsnittet [testa meddelande berikare](#test-message-enrichments) .

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Skapa och konfigurera med hjälp av en Resource Manager-mall
Du kan använda en Resource Manager-mall för att skapa och konfigurera resurser, meddelanderoutning och meddelande anrikning.

1. Logga in på Azure-portalen. Välj **+ skapa en resurs** för att öppna en sökruta. Ange *mall distribution* och Sök efter den. I resultat fönstret väljer du **malldistribution (distribuera med anpassad mall)**.

   ![Malldistribution i Azure Portal](./media/tutorial-message-enrichments/template-select-deployment.png)

1. Välj **skapa** i rutan **malldistribution** .

1. I fönstret **Anpassad distribution** väljer du **Bygg en egen mall i redigeraren**.

1. I fönstret **Redigera mall** väljer du **Läs in fil**. Utforskaren visas. Leta upp **template_messageenrichments.js** filen i den zippade lagrings platsen-filen i **/IoT-Hub/tutorials/routing/SimulatedDevice/Resources**. 

   ![Välj mall från lokal dator](./media/tutorial-message-enrichments/template-select.png)

1. Välj **Öppna** för att läsa in mallfilen från den lokala datorn. Den läses in och visas i fönstret Redigera.

   Den här mallen har kon figurer ATS för att använda ett globalt unikt namn och lagrings konto namn för IoT Hub genom att lägga till ett slumpmässigt värde i slutet av standard namnen, så att du kan använda mallen utan att göra några ändringar i den.

   Här är de resurser som skapas genom att läsa in mallen. **Förrikat** innebär att resursen är för meddelanden med anrikninger. **Original** innebär att resursen avser meddelanden som inte är berikade. Detta är samma värden som används i Azure CLI-skriptet.

   | Namn | Värde |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | container namn | originalspråket  |
   | container namn | avancerad och  |
   | IoT-enhetens namn | Contoso-test – enhet |
   | IoT Hub namn | ContosoTestHubMsgEn |
   | lagrings konto namn | contosostorage |
   | slut punkts namn 1 | ContosoStorageEndpointOriginal |
   | slut punkts namn 2 | ContosoStorageEndpointEnriched|
   | flödes namn 1 | ContosoStorageRouteOriginal |
   | flödes namn 2 | ContosoStorageRouteEnriched |

1. Välj **Spara**. Fönstret **Anpassad distribution** visas och visar alla parametrar som används av mallen. Det enda fält du behöver ange är **resurs grupp**. Skapa antingen en ny eller Välj en från List rutan.

   Här är den övre halvan av det **anpassade distributions** fönstret. Du kan se var du fyller i resurs gruppen.

   ![Övre halvan av anpassat distributions fönster](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Här är den nedre halvan av det **anpassade distributions** fönstret. Du kan se resten av parametrarna och villkoren. 

   ![Nedre halvan av anpassat distributions fönster](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Markera kryss rutan för att godkänna de allmänna villkoren. Välj sedan **köp** för att fortsätta med mallen distribution.

1. Vänta tills mallen är fullständigt distribuerad. Välj klock ikonen längst upp på skärmen för att kontrol lera förloppet. När den är klar fortsätter du till avsnittet [testa meddelandets berikare](#test-message-enrichments) .

## <a name="test-message-enrichments"></a>Testa meddelanden

Välj **resurs grupper** om du vill visa meddelande berikarna. Välj sedan den resurs grupp som du använder för den här självstudien. Välj IoT-hubben i listan över resurser och gå till **meddelanden**. Konfigurationen av meddelanderoutning och de konfigurerade berikarna visas.

Nu när meddelande berikarna har kon figurer ATS för slut punkten kan du köra det simulerade enhets programmet för att skicka meddelanden till IoT Hub. Hubben konfigurerades med inställningar som utför följande aktiviteter:

* Meddelanden som dirigeras till ContosoStorageEndpointOriginal för lagrings slut punkter kommer inte att vara omfattande och lagras i lagrings behållaren `original` .

* Meddelanden som dirigeras till ContosoStorageEndpointEnriched för lagrings slut punkter kommer att beskrivas och lagras i lagrings behållaren `enriched` .

Det simulerade enhets programmet är ett av programmen i den zippade nedladdningen. Programmet skickar meddelanden för var och en av de olika metoderna för meddelanderoutning i [operationsföljden](tutorial-routing.md), som innehåller Azure Storage.

Dubbelklicka på lösnings filen **IoT_SimulatedDevice. SLN** för att öppna koden i Visual Studio och öppna sedan **program.cs**. Ersätt markörens IoT Hub-namn `{your hub name}` . Formatet på värd namnet för IoT Hub är **{ditt Hubbs namn}. Azure-Devices.net**. I den här självstudien är hubbens värd namn ContosoTestHubMsgEn.azure-devices.net. Ersätt sedan den enhets nyckel som du sparade tidigare när du körde skriptet för att skapa resurserna för markören `{your device key}` .

Om du inte har enhets nyckeln kan du hämta den från portalen. När du har loggat in går du till **resurs grupper**, väljer din resurs grupp och väljer sedan din IoT Hub. Titta på **IoT-enheter** för test enheten och välj din enhet. Välj kopierings ikonen bredvid **primär nyckel** för att kopiera den till Urklipp.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Kör och testa

Kör konsol programmet i några minuter. De meddelanden som skickas visas på konsol skärmen i programmet.

Appen skickar ett nytt enhet till molnet-meddelande till IoT-hubben varje sekund. Meddelandet innehåller ett JSON-serialiserat objekt med enhets-ID, temperatur, luftfuktighet och meddelandenivå, där standardinställningen är `normal`. Den tilldelas slumpmässigt en nivå av `critical` eller `storage` , vilket gör att meddelandet dirigeras till lagrings kontot eller till standard slut punkten. Meddelanden som skickas till den **berikade** behållaren i lagrings kontot kommer att vara omfattande.

Visa data när flera lagrings meddelanden har skickats.

1. Välj **Resursgrupper**. Hitta din resurs grupp, **ContosoResourcesMsgEn** och välj den.

2. Välj ditt lagrings konto, som är **contosostorage**. Välj **Storage Explorer (för hands version)** i det vänstra fönstret.

   ![Välj Storage Explorer](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Välj **BLOB-behållare** för att se de två behållare som kan användas.

   ![Se behållare i lagrings kontot](./media/tutorial-message-enrichments/show-blob-containers.png)

Meddelandena i behållaren som heter **berikade** har meddelanden som ingår i meddelandena. Meddelandena i behållaren som kallas **original** har RAW-meddelanden utan några berikningar. Öka detalj nivån till en av behållarna tills du kommer till slutet och öppna den senaste meddelande filen. Gör sedan samma sak för den andra behållaren för att kontrol lera att det inte finns några omfattande tillägg i meddelanden i behållaren.

När du tittar på meddelanden som har berikats bör du se "mina IoT Hub" med hubbens namn och plats och kund-ID, så här:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Här är ett meddelande som inte kan utökas. Observera att "mina IoT Hub", "devicelocation" och "customerID" inte visas här eftersom dessa fält har lagts till av berikningarna. Den här slut punkten har inga berikningar.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen om du vill ta bort alla resurser som du skapade i den här självstudien. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet tar du bort IoT-hubben, lagringskontot och själva resursgruppen.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Rensa resurser med hjälp av Azure CLI

Om du vill ta bort resursgruppen använder du kommandot [az group delete](/cli/azure/group#az-group-delete). Återkallande som `$resourceGroup` ställdes in på **ContosoResourcesMsgEn** i början av den här självstudien.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du konfigurerat och testat lägga till meddelande anrikninger för att IoT Hub meddelanden med hjälp av följande steg:

**Använda IoT Hub meddelande anrikning**

> [!div class="checklist"]
> * Första metoden: skapa resurser och konfigurera meddelanderoutning med hjälp av Azure CLI. Konfigurera meddelande berikarna manuellt med hjälp av [Azure Portal](https://portal.azure.com).
> * Andra metoden: skapa resurser och konfigurera meddelanderoutning och meddelande anrikningar med hjälp av en Azure Resource Manager mall.
> * Kör en app som simulerar en IoT-enhet som skickar meddelanden till hubben.
> * Visa resultaten och kontrol lera att meddelandets anrikning fungerar som förväntat.

Mer information om meddelande berikare finns i [Översikt över meddelande anrikninger](iot-hub-message-enrichments-overview.md).

Mer information om meddelanderoutning finns i följande artiklar:

> [!div class="nextstepaction"]
> [Använd IoT Hub meddelanderoutning för att skicka meddelanden från enheten till molnet till olika slut punkter](iot-hub-devguide-messages-d2c.md)

> [!div class="nextstepaction"]
> [Självstudie: IoT Hub routning](tutorial-routing.md)