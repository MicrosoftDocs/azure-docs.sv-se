---
title: Självstudie – konfigurera meddelanderoutning för Azure IoT Hub med Azure CLI
description: Självstudie – konfigurera meddelanderoutning för Azure IoT Hub med hjälp av Azure CLI och Azure Portal
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: d7a10aac82286616273f7283bc3fba74f2c58f35
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98625222"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>Självstudie: Använd Azure CLI och Azure Portal för att konfigurera IoT Hub meddelanderoutning

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>Använd Azure CLI för att skapa bas resurserna

I den här självstudien används Azure CLI för att skapa bas resurser och sedan används [Azure Portal](https://portal.azure.com) för att visa hur du konfigurerar meddelanderoutning och konfigurerar den virtuella enheten för testning.

Kopiera och klistra in skriptet nedan i Cloud Shell och tryck på RETUR. Den kör skriptet en rad i taget. Detta skapar bas resurserna för den här självstudien, inklusive lagrings kontot, IoT Hub, Service Bus namnrymd och Service Bus kö.

Det finns flera resurs namn som måste vara globalt unika, till exempel IoT Hub namn och lagrings konto namn. För att göra det enklare läggs dessa resurs namn till med ett slumpmässigt alfanumeriskt värde som kallas *randomValue*. RandomValue skapas en gång överst i skriptet och läggs till i resurs namnen vid behov i hela skriptet. Om du inte vill att den ska vara slumpmässig, kan du ange den som en tom sträng eller ett angivet värde.

> [!TIP]
> Ett tips om fel sökning: det här skriptet använder fortsättnings symbolen (det omvända snedstrecket `\` ) för att göra skriptet mer läsbart. Om du har problem med att köra skriptet kontrollerar du att Cloud Shell-sessionen körs `bash` och att det inte finns några blank steg efter något av omvänt snedstrecken.
>

```azurecli-interactive
# This retrieves the subscription id of the account 
#   in which you're logged in.
# This field is used to set up the routing queries.
subscriptionID=$(az account show --query id)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
iotHubName=ContosoTestHub$randomValue 
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub for the 'events' endpoint.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

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
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# The Service Bus namespace must be globally unique, 
#   so add a random value to the end.
sbNamespace=ContosoSBNamespace$randomValue
echo "Service Bus namespace = " $sbNamespace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNamespace \
    --location $location

# The Service Bus queue name must be globally unique, 
#   so add a random value to the end.
sbQueueName=ContosoSBQueue$randomValue
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNamespace \
    --resource-group $resourceGroup

```

Nu när bas resurserna har kon figurer ATS kan du konfigurera meddelanderoutning i [Azure Portal](https://portal.azure.com).

## <a name="set-up-message-routing"></a>Konfigurera meddelandedirigering

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>Dirigera till ett lagrings konto

Konfigurera nu routning för lagringskontot. Du gå till fönstret meddelanderoutning och lägger till en väg. När du lägger till vägen, definierar du en ny slutpunkt för den. När den här routningen har kon figurer ATS skrivs meddelanden där **nivå** egenskapen är inställd på **Storage** till ett lagrings konto automatiskt. 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

1. I [Azure Portal](https://portal.azure.com)väljer du **resurs grupper** och väljer sedan din resurs grupp. I den här självstudien används **ContosoResources**.

2. Välj IoT-hubben under resurs listan. I självstudien används **ContosoTestHub**.

3. Välj **meddelanderoutning**. I fönstret **meddelanderoutning** väljer du +**Lägg till**. I fönstret **Lägg till en väg** väljer du +**Lägg till slut punkt** bredvid fältet slut punkt för att visa de slut punkter som stöds, som visas i följande bild:

   ![Börja lägga till en slut punkt för en väg](./media/tutorial-routing/message-routing-add-a-route-with-storage-endpoint-ver2.png)

4. Välj **lagring**. Du ser rutan **Lägg till en lagrings slut punkt** .

   ![Lägga till en slut punkt](./media/tutorial-routing/message-routing-add-storage-endpoint-ver2.png)

5. Ange ett namn på slutpunkten. I den här självstudien används **ContosoStorageEndpoint**.

6. Välj **Välj en behållare**. Det tar dig till en lista över dina lagringskonton. Välj det som du skapade i förberedelsesteget. I den här självstudiekursen används **contosostorage**. Den visar en lista över containrar i det lagringskontot. **Välj** den behållare som du konfigurerade i förberedelse stegen. I självstudien används **contosoresults**. Du kommer tillbaka till fönstret **Lägg till en lagrings slut punkt** och ser de val du gjort.

7. Ange kodningen till AVRO eller JSON. I den här självstudiekursen används standardinställningar för resten av fälten. Det här fältet blir grå om den valda regionen inte stöder JSON-kodning.

   > [!NOTE]
   > Du kan ange formatet för blobnamnet med hjälp av **Format på blobfilens namn**. Standardvärdet är `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. Formatet måste innehålla {iothub}, {partition}, {YYYY}, {MM}, {DD}, {HH} och {mm} i valfri ordning.
   >
   > Exempel med standardinställt namnformat för blobfilen: om hubbnamnet är ContosoTestHub och datum/tid är 30 oktober 2018 kl. 10:56 ser blobnamnet ut så här: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > Blobarna är skrivna i Avro-format.
   >

8. Välj **skapa** för att skapa lagrings slut punkten och Lägg till den i vägen. Du kommer tillbaka till fönstret **Lägg till en väg**.

9. Nu slutför du resten av informationen för routningsfrågan. Den här frågan anger kriterier för att skicka meddelanden till den lagringscontainer du just lade till som en slutpunkt. Fyll i fälten på skärmen.

   **Namn**: Ange ett namn för din routningsfråga. I den här självstudien används **ContosoStorageRoute**.

   **Slutpunkt**: visar den slutpunkt som du nyss skapade.

   **Datakälla**: Välj **Enhetstelemetrimeddelanden** från listrutan.

   **Aktivera väg**: se till att det här fältet är inställt på `enabled` .
   
   **Routningsfråga**: Ange `level="storage"` som frågesträng.

   ![Skapa en cirkulations fråga för lagrings kontot](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   Välj **Spara**. När den är klar returneras den till fönstret meddelanderoutning där du kan se din nya routningsfråga för lagring. Stäng rutan Vägar så att du kommer tillbaka till sidan Resursgrupp.

### <a name="route-to-a-service-bus-queue"></a>Dirigera till en Service Bus kö

Konfigurera nu routning för Service Bus-kön. Du gå till fönstret meddelanderoutning och lägger till en väg. När du lägger till vägen, definierar du en ny slutpunkt för den. När den här vägen har kon figurer ATS skrivs meddelanden där **nivå** egenskapen är **kritisk** till den Service Bus kön, som utlöser en Logic app, som sedan skickar ett e-postmeddelande med informationen.

1. På sidan resurs grupp väljer du din IoT Hub och väljer sedan **meddelanderoutning**.

2. I fönstret **meddelanderoutning** väljer du +**Lägg till**.

3. I fönstret **Lägg till en väg** väljer du +**Lägg till** bredvid fältet slut punkt. Välj **Service Bus-kö**. Du får fram fönstret **Lägg till Service Bus-slutpunkt**.

   ![Lägga till en Service Bus-slutpunkt](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Fyll i fälten:

   **Slutpunktsnamn**: Ange ett namn på slutpunkten. I den här självstudien används **ContosoSBQueueEndpoint**.
   
   **Service Bus namnrymd**: Använd List rutan för att välja Service Bus-namnområdet som du angav i förberedelse stegen. I den här självstudien används **ContosoSBNamespace**.

   **Service Bus kö**: Använd List rutan för att välja kön Service Bus. I den här självstudien används **contososbqueue**.

5. Välj **skapa** för att lägga till Service Bus Queue-slutpunkten. Du kommer tillbaka till fönstret **Lägg till en väg**.

6. Nu slutför du resten av routningsfrågeinformationen. Den här frågan anger kriterierna för att skicka meddelanden till den Service Bus-kö du just lade till som en slutpunkt. Fyll i fälten på skärmen. 

   **Namn**: Ange ett namn för din routningsfråga. I den här självstudien används **ContosoSBQueueRoute**. 

   **Slutpunkt**: visar den slutpunkt som du nyss skapade.

   **Datakälla**: Välj **Enhetstelemetrimeddelanden** från listrutan.

   **Routningsfråga**: Ange `level="critical"` som frågesträng. 

   ![Skapa en cirkulations fråga för Service Bus kön](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Välj **Spara**. När den återgår till Vägar-fönstret så ser du båda dina nya vägar som de visas här.

   ![Vägarna som du nyss konfigurerat](./media/tutorial-routing/message-routing-show-both-routes.png)

8. Du kan se de anpassade slut punkter som du ställer in genom att välja fliken **anpassade slut punkter** .

   ![Den anpassade slut punkten som du nyss konfigurerade](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Stäng fönstret Meddelanderoutning så att du kommer tillbaka till Resursgruppfönstret.

## <a name="create-a-simulated-device"></a>Skapa en simulerad enhet

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat resurserna och meddelande vägarna har kon figurer ATS kan du gå vidare till nästa självstudie för att lära dig hur du skickar meddelanden till IoT-hubben och ser att de dirigeras till olika destinationer. 

> [!div class="nextstepaction"]
> [Del 2 – Visa resultatet av meddelanderoutning](tutorial-routing-view-message-routing-results.md)