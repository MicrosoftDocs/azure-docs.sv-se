---
title: Självstudie – synkronisera enhets tillstånd från Azure IoT Hub | Microsoft Docs
description: Självstudie – lär dig hur du använder enhets dubbla för att konfigurera dina enheter från molnet och få status-och efterlevnadsprinciper från dina enheter.
services: iot-hub
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/21/2019
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-js
- devx-track-azurecli
ms.openlocfilehash: 912e59197970e60f57a13c049ffadbd2ad938a9b
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621419"
---
<!-- **TODO** Update publish config with repo paths before publishing! -->

# <a name="tutorial-configure-your-devices-from-a-back-end-service"></a>Självstudier: Konfigurera dina enheter från en serverdelstjänst

Förutom att ta emot telemetri från dina enheter kan du behöva konfigurera dina enheter från din serverdelstjänst. När du skickar en önskad konfiguration till dina enheter vill du kanske också ta emot uppdateringar av status och efterlevnad från dessa enheter. Du kan till exempel ställa in ett måldrifttemperaturområde för en enhet eller hämta versionsinformation för den fasta programvaran från dina enheter.

Om du vill synkronisera statusinformation mellan en enhet och en IoT-hubb använder du _enhetstvillingar_. En [enhetstvilling](iot-hub-devguide-device-twins.md) är ett JSON-dokument som är associerat med en specifik enhet och lagras i IoT-hubben i molnet där du kan [frågan](iot-hub-devguide-query-language.md) det. En enhetstvilling innehåller _önskade egenskaper_, _rapporterade egenskaper_ och _taggar_. En önskad egenskap ställs in av ett serverdelsprogram och läses av en enhet. En rapporterade egenskap ställs in av en enhet och läsas av ett serverdelsprogram. En tagg ställs in av ett serverdelsprogram och skickas aldrig till en enhet. Använd taggar för att organisera din enhet. Den här kursen visar hur du använder önskade och rapporterade egenskaper för att synkronisera tillståndsinformation:

![Tvillingssammanfattning](media/tutorial-device-twins/DeviceTwins.png)

I den här självstudien utför du följande åtgärder:

> [!div class="checklist"]
> * Skapa en IoT-hubb och lägg till en testenhet i identitetsregistret.
> * Använda önskade egenskaper för att skicka statusinformation till den simulerade enheten.
> * Använda rapporterade egenskaper för att ta emot statusinformation från den simulerade enheten.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

De två exempelprogram som du kör i den här snabbstarten skrivs med Node.js. Du behöver Node.js v10. x. x eller senare på din utvecklings dator.

Du kan hämta Node.js för flera plattformar från [NodeJS.org](https://nodejs.org).

Du kan kontrollera den aktuella versionen av Node.js på utvecklingsdatorn med följande kommando:

```cmd/sh
node --version
```

Ladda ned exempelprojektet för Node.js från https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip och extrahera ZIP-arkivet.

Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här självstudien använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="set-up-azure-resources"></a>Ställa in Azure-resurser

För att slutföra den här kursen måste din Azure-prenumeration innehålla en IoT-hubb med en enhet som har lagts till i enhetsidentitetsregistret. Posten i enhetsidentitetsregistret gör att den simulerade enheten som du kör i den här kursen kan ansluta till din hubb.

Om du inte redan har en IoT-hubb konfigurerad i din prenumeration kan du konfigurera en med följande CLI-skript. Det här skriptet använder namnet **tutorial-iot-hub** för IoT-hubben. Du bör ersätta det här namnet med ditt eget unika namn när du kör det. Skriptet skapar resursgruppen och hubben i regionen **USA, centrala**, vilket du kan ändra till en region som ligger närmare till dig. Skriptet hämtar din anslutningssträng för IoT-hubbtjänsten, som du använder i serverdelexemplet för att ansluta till din IoT-hubb:

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed:
az extension add --name azure-iot

# Create a resource group:
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription:
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --sku F1

# Make a note of the service connection string, you need it later:
az iot hub show-connection-string --name $hubname --policy-name service -o table

```

Den här kursen använder en simulerad enhet som heter **MyTwinDevice**. Med följande skript lägger du till den här enheten till din identitetsregistret och hämtar sin anslutningssträng:

```azurecli-interactive
# Set the name of your IoT hub:
hubname=tutorial-iot-hub

# Create the device in the identity registry:
az iot hub device-identity create --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Retrieve the device connection string, you need this later:
az iot hub device-identity show-connection-string --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

## <a name="send-state-information"></a>Skicka statusinformation

Du använder önskade egenskaper för att skicka statusinformation från ett serverdelprogram till en enhet. I det här avsnittet visas hur du:

* Tar emot och bearbetar önskade egenskaper på en enhet.
* Skickar önskade egenskaper från ett serverdelsprogram.

Om du vill visa den simulerade enhetens exempelkod som tar emot egenskaper, navigera till mappen **iot-hubb/Tutorials/DeviceTwins** i exempelprojektet för Node.js som du hämtat. Öppna SimulatedDevice.js i en valfri textredigerare.

I följande avsnitt beskrivs koden som körs på den simulerade enheten som svarar på önskade egenskapsändringar som skickas från serverdelsprogrammet:

### <a name="retrieve-the-device-twin-object"></a>Hämta enhetens tvillingobjekt

Använd följande kod för att ansluta till din IoT-hubb med en enhetsanslutningssträng:

[!code-javascript[Create IoT Hub client](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=createhubclient&highlight=2 "Create IoT Hub client")]

Följande kod hämtar en tvilling från klientobjektet:

[!code-javascript[Get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=gettwin&highlight=2 "Get twin")]

### <a name="sample-desired-properties"></a>Exempel på önskade egenskaper

Du kan strukturera dina önskade egenskaper på ett sätt som passar ditt program. Det här exemplet används en toppnivåegenskap som heter **fanOn** och grupperar de övriga egenskaperna i separata **komponenter**. Följande JSO- utdrag visar strukturen för egenskaperna som den här självstudiekursen använder:

[!code[Sample desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/desired.json "Sample desired properties")]

### <a name="create-handlers"></a>Skapa hanterare

Du kan skapa hanterare för uppdateringar av önskade egenskaper som kan hantera uppdateringar på olika nivåer i JSON-hierarkin. Till exempel ser den här hanteraren alla önskade egenskapsändringar som skickas till enheten från ett serverdelsprogram. Variabeln **delta** innehåller de önskade egenskaperna som skickas från lösningens serverdel:

[!code-javascript[Handle all properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=allproperties&highlight=2 "Handle all properties")]

Följande hanterare reagerar endast på ändringar som gjorts i den önskade egenskapen **fanOn**:

[!code-javascript[Handle fan property](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=fanproperty&highlight=2 "Handle fan property")]

### <a name="handlers-for-multiple-properties"></a>Hanterare för flera egenskaper

I exemplet önskade egenskaper för JSON som vi såg tidigare innehåller noden **klimatförändringar** under **komponenter** två egenskaper: **minTemperature** och **maxTemperature**.

En enhet som har lokala **tvillingobjekt** lagrar en fullständig uppsättning önskade och rapporterade egenskaper. **Delta** som skickas från en slutpunkt kanske endast uppdaterar en delmängd av egenskaperna. I följande kodavsnitt, om den simulerade enheten tar emot en uppdatering för antingen **minTemperature** eller **maxTemperature**, används värdet i den lokala tvillingen för det andra värdet för att konfigurera enheten :

[!code-javascript[Handle climate component](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=climatecomponent&highlight=2 "Handle climate component")]

Det lokala **tvillingobjektet** lagrar en fullständig uppsättning önskade och rapporterade egenskaper. **Delta** som skickas från en slutpunkt kanske endast uppdaterar en delmängd av egenskaperna.

### <a name="handle-insert-update-and-delete-operations"></a>Hantera infogning, uppdatering och borttagning

De egenskaper som skickas från serverdelen indikerar vilken åtgärd som utförs på en viss önskad egenskap. Din kod måste härleda åtgärden från den aktuella uppsättningen egenskaper som lagras lokalt och de ändringar som skickas från hubben.

Följande utdrag visar hur den simulerade enheten infogar, uppdaterar och tar bort i listan över **komponenter** i önskade egenskaper. Du kan se hur du använder **null**-värden för att ange att en komponent ska tas bort:

[!code-javascript[Handle components](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=components&highlight=2,6,13 "Handle components")]

### <a name="send-desired-properties-to-a-device-from-the-back-end"></a>Skicka önskade egenskaper till en enhet från serverdelen

Du har sett hur en enhet använder hanterare för att ta emot uppdateringar av önskade egenskaper. Det här avsnittet visar hur du skickar önskade egenskapsändringar till en enhet från ett serverdelsprogram.

Om du vill visa den simulerade enhetens exempelkod som tar emot egenskaper, navigera till mappen **iot-hubb/Tutorials/DeviceTwins** i exempelprojektet för Node.js som du hämtat. Öppna ServiceClient.js i en valfri textredigerare.

Följande kodavsnitt visar hur du ansluter till enhetsidentitetsregistret och får åtkomst till tvillingen för en specifik enhet:

[!code-javascript[Create registry and get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=getregistrytwin&highlight=2,6 "Create registry and get twin")]

Följande utdrag visar olika *korrigeringsfiler* för önskade egenskaper som serverdelsprogrammet skickar till enheten:

[!code-javascript[Patches sent to device](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=patches&highlight=2,12,26,41,56 "Patches sent to device")]

Följande utdrag visar hur serverdelsprogrammet skickar en önskad egenskapsuppdatering till en enhet:

[!code-javascript[Send desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=senddesiredproperties&highlight=2 "Send desired properties")]

### <a name="run-the-applications"></a>Köra programmen

I det här avsnittet kan du köra två exempelprogram för att se när ett serverdelsprogram skickar önskade egenskapsuppdateringar till ett simulerad enhetsprogram.

Om du vill köra simulerade enhetsprogram och serverdelsprogram behöver du anslutningssträngar för enheten och tjänsten. Du antecknade anslutningssträngar när du skapade resurserna i början av den här kursen.

Om du vill köra det simulerade enhetsprogrammet, öppna ett gränssnitt eller kommandotolkfönster och navigera till mappen **iot-hub/Tutorials/DeviceTwins** i Node.js-projektet som du hämtade. Kör sedan följande kommandon:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Öppna ett annat fönster för gränssnitt eller en kommandotolk för att köra serverdelsprogrammet. Navigera sedan till mappen **iot-hub/Tutorials/DeviceTwins** i Node.js-projektet som du hämtade. Kör sedan följande kommandon:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

Följande skärmbild visar utdata från det simulerade enhetsprogrammet och visar hur det hanterar en uppdatering för den önskade egenskapen **maxTemperature**. Du kan se hur både toppnivåhanteraren och klimatkomponenthanteraren körs:

![Skärm bild som visar hur både toppnivå hanteraren och klimat komponentens hanterare körs.](./media/tutorial-device-twins/SimulatedDevice1.png)

Följande skärmbild visar utdata från det serverdelsprogrammet och visar hur det skickar en uppdatering för den önskade egenskapen **maxTemperature**:

![Skärm bild som visar utdata från Server dels programmet och markerar hur den skickar en uppdatering.](./media/tutorial-device-twins/BackEnd1.png)

## <a name="receive-state-information"></a>Ta emot statusinformation

Ditt serverdelsprogram tar emot statusinformation från en enhet som rapporterade egenskaper. En enhet anger de rapporterade egenskaperna och skickar dem till din hubb. Ett serverdelsprogram kan läsa de aktuella värdena för de rapporterade egenskaperna från enhetstvillingen som lagras i din hubb.

### <a name="send-reported-properties-from-a-device"></a>Skicka rapporterade egenskaper från en enhet

Du kan skicka uppdateringar till rapporterade egenskapsvärden som en korrigeringsfil. Följande kodfragment visar en mall för korrigeringsfilen som den simulerade enheten skickar. Den simulerade enheten uppdaterar fälten i korrigeringsfilen innan den skickas till hubben:

[!code-javascript[Reported properties patches](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=reportedpatch&highlight=2 "Reported properties patches")]

Den simulerade enheten använder följande funktion för att skicka korrigeringsfilen som innehåller de rapporterade egenskaperna till hubben:

[!code-javascript[Send reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=sendreportedproperties&highlight=2 "Send reported properties")]

### <a name="process-reported-properties"></a>Bearbeta rapporterade egenskaper

Ett serverdelsprogram har åtkomst till de aktuella rapporterade egenskapsvärdena för en enhet via tvillingenheten. Följande utdrag visar hur serverdelsprogrammet läser rapporterade egenskapsvärden för den simulerade enheten:

[!code-javascript[Display reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=displayreportedproperties&highlight=2 "Display reported properties")]

### <a name="run-the-applications"></a>Köra programmen

I det här avsnittet kan du köra två exempelprogram för att se när ett simulerat enhetsprogram skickar önskade egenskapsuppdateringar till ett simulerad serverdelsprogram.

Du kör samma två exempelprogram som du körde om du vill se hur egenskaper skickas till en enhet.

Om du vill köra simulerade enhetsprogram och serverdelsprogram behöver du anslutningssträngar för enheten och tjänsten. Du antecknade anslutningssträngar när du skapade resurserna i början av den här kursen.

Om du vill köra det simulerade enhetsprogrammet, öppna ett gränssnitt eller kommandotolkfönster och navigera till mappen **iot-hub/Tutorials/DeviceTwins** i Node.js-projektet som du hämtade. Kör sedan följande kommandon:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Öppna ett annat fönster för gränssnitt eller en kommandotolk för att köra serverdelsprogrammet. Navigera sedan till mappen **iot-hub/Tutorials/DeviceTwins** i Node.js-projektet som du hämtade. Kör sedan följande kommandon:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

Följande skärmbild visar utdata från det simulerade enhetsprogrammet och visar hur det skickar en rapporterad egenskapsuppdatering till din hubb:

![Simulerad enhet](./media/tutorial-device-twins/SimulatedDevice2.png)

Följande skärm bild visar utdata från Server dels programmet och visar hur den tar emot och bearbetar en rapporterad egenskaps uppdatering från en enhet:

![Serverdelsprogram](./media/tutorial-device-twins/BackEnd2.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att slutföra nästa självstudiekurs så lämna resursgruppen och IoT-hubben och återanvänd dem senare.

Om du inte behöver IoT-hubben längre kan du ta bort den och resursgruppen i portalen. Det gör du genom att markera **tutorial-iot-hub-rg**-resursgruppen som innehåller din IoT-hubb och klicka på **Ta bort**.

Du kan också använda CLI:

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien beskrivs hur du synkroniserar statusinformation mellan dina enheter och din IoT Hub. Gå vidare till nästa kurs att lära dig mer att använda tvillingenheter för att implementera en process för uppdatering av fast programvara.

> [!div class="nextstepaction"]
> [Implementera en uppdateringsprocess för enhetens inbyggda programvara](tutorial-firmware-update.md)
