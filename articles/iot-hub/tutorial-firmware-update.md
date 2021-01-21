---
title: Självstudie – uppdatera enhetens inbyggda program vara via Azure IoT Hub | Microsoft Docs
description: Självstudie – lär dig hur du implementerar en uppdaterings process för enhetens inbyggda program vara som kan utlösas från ett Server dels program som är anslutet till din IoT Hub.
services: iot-hub
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/28/2019
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-js
- devx-track-azurecli
ms.openlocfilehash: 807de3c41ec8026edd2b2d8859eb70863ae5697b
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621403"
---
# <a name="tutorial-implement-a-device-firmware-update-process"></a>Självstudie: Implementera en uppdateringsprocess för enhetens inbyggda programvara

Du kan behöva uppdatera den inbyggda programvaran på enheter som är anslutna till din IoT Hub. Du kanske vill lägga till nya funktioner i den inbyggda programvaran eller tillämpa säkerhetskorrigeringar. I många IoT-scenarier är det inte möjligt att fysiskt besöka och uppdatera den inbyggda programvaran manuellt för dina enheter. I den här självstudien får du lära dig att starta och övervaka processen för uppdatering av inbyggd programvara via fjärranslutning med ett serverdelsprogram anslutet till din hubb.

Om du vill skapa och övervaka uppdateringsprocessen för den inbyggda programvaran, skapar serverdelsprogrammet i den här självstudien en _konfiguration_ i din IoT Hub. Den [automatiska enhetshanteringen](./iot-hub-automatic-device-management.md) i IoT Hub använder den här konfigurationen för att uppdatera en uppsättning _önskade egenskaper för enhetstvillingar_ på alla kylaggregat. De önskade egenskaperna anger informationen om uppdateringen av den inbyggda programvara som krävs. När kylaggregaten kör uppdateringsprocessen för den inbyggda programvaran rapporterar de status till serverdelsprogrammet med hjälp av _rapporterade egenskaper för enhetstvillingar_. Serverdelsprogrammet kan använda konfigurationen för att övervaka de rapporterade egenskaperna från enheten och spåra uppdateringsprocessen för den inbyggda programvaran så att den kan slutföras:

![Uppdateringsprocessen för den inbyggda programvaran](media/tutorial-firmware-update/Process.png)

I den här självstudien slutför du följande uppgifter:

> [!div class="checklist"]
> * Skapa en IoT Hub och lägg till en testenhet i identitetsregistret.
> * Skapa en konfiguration för att definiera uppdateringen av den inbyggda programvaran.
> * Simulera uppdateringsprocessen för inbyggd programvara på en enhet.
> * Ta emot statusuppdateringar från enheten allteftersom uppdateringen av den inbyggda programvaran fortskrider.

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

För att slutföra den här självstudien måste din Azure-prenumeration innehålla en IoT Hub med en enhet som har lagts till i enhetsidentitetsregistret. Posten i enhetsidentitetsregistret gör att den simulerade enheten som du kör i den här kursen kan ansluta till din hubb.

Om du inte redan har en IoT-hubb i din prenumeration kan du konfigurera ett konto med följande CLI-skript. Det här skriptet använder namnet **tutorial-iot-hub** för IoT-hubben. Du bör ersätta det här namnet med ditt eget unika namn när du kör det. Skriptet skapar resursgruppen och hubben i regionen **USA, centrala**, vilket du kan ändra till en region som ligger närmare till dig. Skriptet hämtar anslutningssträngen för IoT-hubbtjänsten som du använder i serverdelsexemplet för att ansluta till din IoT Hub:

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Create a resource group
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --sku F1

# Make a note of the service connection string, you need it later
az iot hub show-connection-string --name $hubname --policy-name service -o table

```

I den här självstudien används en simulerad enhet med namnet **MyFirmwareUpdateDevice**. Följande skript lägger till enheten till identitetsregistret för din enhet, anger ett taggvärde och hämtar anslutningssträngen:

```azurecli-interactive
# Set the name of your IoT hub
hubname=tutorial-iot-hub

# Create the device in the identity registry
az iot hub device-identity create --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Add a device type tag
az iot hub device-twin update --device-id MyFirmwareUpdateDevice --hub-name $hubname --set tags='{"devicetype":"chiller"}'

# Retrieve the device connection string, you need this later
az iot hub device-identity show-connection-string --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

> [!TIP]
> Om du kör dessa kommandon i en Windows-kommandotolk eller Powershell-prompt, se sidan om [tips för azure-iot-cli-utökning](https://github.com/Azure/azure-iot-cli-extension/wiki/Tips) för information om hur du citerar JSON-strängar.

## <a name="start-the-firmware-update"></a>Starta uppdateringen av den inbyggda programvaran

Du skapar en [automatisk enhetshanteringskonfiguration](iot-hub-automatic-device-management.md#create-a-configuration) i serverdelsprogrammet för att påbörja uppdateringen av den inbyggda programvaran på alla enheter som är taggade med **enhetstypen** kylaggregat. I det här avsnittet visas hur du:

* Skapa en konfiguration från ett serverdelsprogram.
* Övervaka att jobbet har slutförts.

### <a name="use-desired-properties-to-start-the-firmware-upgrade-from-the-back-end-application"></a>Använd önskade egenskaper för att starta uppdateringen av den inbyggda programvaran från serverdelsprogrammet

Om du vill visa koden för serverdelsprogrammet som skapar konfigurationen, navigera till mappen **iot-hub/Tutorials/FirmwareUpdate** i exempelprojektet för Node.js som du laddat ned. Öppna ServiceClient.js i en valfri textredigerare.

Serverdelsprogrammet skapar följande konfiguration:

[!code-javascript[Automatic device management configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=configuration "Automatic device management configuration")]

Konfigurationen innehåller följande avsnitt:

* `content` anger önskade egenskaper för den inbyggda programvara som ska skickas till de valda enheterna.
* `metrics` anger vilka frågor som ska köras för att rapportera status för uppdateringen.
* `targetCondition` väljer vilka enheter som ska få uppdateringen.
* `priorty` sätter den prioriteten för den här konfigurationen i relation till andra konfigurationer.

Serverdelsprogrammet använder följande kod för att skapa konfigurationen för att ange de önskade egenskaperna:

[!code-javascript[Create configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=createConfiguration "Create configuration")]

Efter att programmet skapat konfigurationen övervakar den uppdateringen:

[!code-javascript[Monitor firmware update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=monitorConfiguration "Monitor firmware update")]

Konfigurationen rapporterar två typer av värden:

* Systemmått som visar hur många enheter som är mål och hur många som redan har den valda uppdateringen.
* Generella mått som genereras av de frågor du skickar till konfigurationen. I den här självstudien rapporterar frågorna hur många enheter som är i varje fas av uppdateringen.

### <a name="respond-to-the-firmware-upgrade-request-on-the-device"></a>Svara på uppgraderingsbegäran för inbyggd programvara på enheten

Om du vill visa den simulerade enhetskoden som hanterar önskade egenskaper för den inbyggda programvaran som skickas från serverdelsprogrammet, navigera till mappen **iot-hub/Tutorials/FirmwareUpdate** i exempelprojektet för Node.js som du laddat ned. Öppna SimulatedDevice.js i en valfri textredigerare.

Programmet för den simulerade enheten skapar en hanterare för uppdateringar för de önskade egenskaperna för **properties.desired.firmware** i enhetstvillingen. I hanteraren utförs vissa grundläggande kontroller för de önskade egenskaperna innan uppdateringen startas:

[!code-javascript[Handle desired property update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=initiateUpdate "Handle desired property update")]

## <a name="update-the-firmware"></a>Uppdatera den inbyggda programvaran

Funktionen **initiateFirmwareUpdateFlow** kör uppdateringen. Den här funktionen använder **vattenfallsfunktionen** för att köra faser av uppdateringen i följd efter varandra. I det här exemplet består uppdateringen av fyra faser. Den första fasen laddar ned avbildningen, den andra fasen verifierar avbildningen med en kontrollsumma, den tredje fasen applicerar avbildningen och den sista installationsfasen startar om enheten:

[!code-javascript[Firmware update flow](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=firmwareupdateflow "Firmware update flow")]

Under uppdateringen rapporterar den simulerade enheten fortskridandet med hjälp av rapporterade egenskaper:

[!code-javascript[Reported properties](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=reportedProperties "Reported properties")]

Följande kod visar implementeringen av nedladdningsfasen. Under nedladdningsfasen använder den simulerade enheten rapporterade egenskaper för att skicka statusinformation till serverdelsprogrammet:

[!code-javascript[Download image phase](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=downloadimagephase "Download image phase")]

## <a name="run-the-sample"></a>Kör exemplet

I det här avsnittet körs två exempelprogram för att se när serverdelsprogrammet skapar konfigurationen för att hantera uppdateringen av den inbyggda programvaran på den simulerade enheten.

Om du vill köra simulerade enhetsprogram och serverdelsprogram behöver du anslutningssträngar för enheten och tjänsten. Du antecknade anslutningssträngar när du skapade resurserna i början av den här kursen.

Om du vill köra det simulerade enhetsprogrammet, öppna ett gränssnitt eller kommandotolkfönster och navigera till mappen **iot-hub/Tutorials/FirmwareUpdate** i Node.js-projektet som du laddade ned. Kör sedan följande kommandon:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Öppna ett annat fönster för gränssnitt eller en kommandotolk för att köra serverdelsprogrammet. Navigera sedan till mappen **iot-hub/Tutorials/FirmwareUpdate** i Node.js-projektet som du laddade ned. Kör sedan följande kommandon:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

Följande skärmbild visar utdata från det simulerade enhetsprogrammet och visar hur det svarar på uppdateringen av önskade egenskaper från serverdelsprogrammet:

![Simulerad enhet](./media/tutorial-firmware-update/SimulatedDevice.png)

Följande skärmbild visar utdata från serverdelsprogrammet och visar hur det skapar en konfiguration för uppdatering av den önskade egenskapen:

![Skärm bild som visar utdata från Server dels programmet.](./media/tutorial-firmware-update/BackEnd1.png)

Följande skärmbild visar utdata från serverdelsprogrammet och visar hur det övervakar måtten för uppdateringen av den önskade egenskapen på den simulerade enheten:

![Serverdelsprogram](./media/tutorial-firmware-update/BackEnd2.png)

Eftersom automatisk enhets konfiguration körs när den skapas och var femte minut, kanske du inte ser alla status uppdateringar som skickas till Server dels programmet. Du kan också visa måtten i portalen för avsnittet **Automatic device management -> IoT device configuration** (Automatisk enhetshantering -> Konfiguration av IoT-enhet) i din IoT Hub:

![Visa konfiguration i portalen](./media/tutorial-firmware-update/portalview.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att slutföra nästa självstudiekurs så lämna resursgruppen och IoT-hubben och återanvänd dem senare.

Om du inte behöver IoT-hubben längre kan du ta bort den och resursgruppen i portalen. Det gör du genom att markera **tutorial-iot-hub-rg**-resursgruppen som innehåller din IoT-hubb och klicka på **Ta bort**.

Du kan också använda CLI:

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig att implementera en uppdateringsprocess för den inbyggda programvaran för dina anslutna enheter. Fortsätt med nästa självstudie för att lära dig att använda Azure IoT Hub-portalverktyg och Azure CLI-kommandon för att testa anslutningen för enheten.

> [!div class="nextstepaction"]
> [Använda en simulerad enhet för att testa anslutningen till din IoT-hubb](tutorial-connectivity.md)
