---
title: Snabb start – konfigurera de IoT-resurser du behöver för IoT Plug and Play | Microsoft Docs
description: Snabb start – skapa en instans av IoT Hub-och Device Provisioning-tjänsten som ska användas med IoT-Plug and Play snabb starter och självstudier.
author: dominicbetts
ms.author: dobett
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5446f8481439dabffe67f9b00b5f65b1da495746
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742661"
---
# <a name="quickstart-set-up-your-environment-for-the-iot-plug-and-play-quickstarts-and-tutorials"></a>Snabb start: Konfigurera din miljö för IoT Plug and Play snabb starter och självstudier

Innan du kan slutföra någon av IoT-Plug and Play snabb starter och självstudier måste du konfigurera en IoT-hubb och enhets etablerings tjänsten (DPS) i din Azure-prenumeration. Du behöver också lokala kopior av de konsolfiler som används av exempel programmen och Azure IoT Explorer-verktyget.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Du kan undvika kravet på att installera Azure CLI lokalt genom att använda Azure Cloud Shell för att konfigurera moln tjänsterna.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resources"></a>Skapa resurserna

Skapa en Azure-resurs grupp för resurserna:

```azurecli-interactive
az group create --name my-pnp-resourcegroup --location centralus
```

Skapa en IoT-hubb. Följande kommando använder namnet `my-pnp-hub` som ett exempel för namnet på den IoT-hubb som ska skapas. Välj ett unikt namn för din IoT Hub som ska användas i stället för `my-pnp-hub` :

```azurecli-interactive
az iot hub create --name my-pnp-hub --resource-group my-pnp-resourcegroup --sku F1 --partition-count 2
```

Skapa en DPS-instans. Följande kommando använder namnet `my-pnp-dps` som ett exempel för namnet på den DPS-instans som ska skapas. Välj ett unikt namn för DPS-instansen som ska användas i stället för `my-pnp-dps` :

```azurecli-interactive
az iot dps create --name my-pnp-dps --resource-group my-pnp-resourcegroup
```

Använd följande kommandon för att länka DPS-instansen till din IoT-hubb. Ersätt `my-pnp-dps` och `my-pnp-hub` med de unika namn som du valde tidigare:

```azurecli-interactive
hubConnectionString=$(az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString -o tsv)
az iot dps linked-hub create --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --location centralus --connection-string $hubConnectionString
```

## <a name="retrieve-the-settings"></a>Hämta inställningarna

Vissa snabb starter och självstudier använder anslutnings strängen för din IoT Hub. Du behöver också anslutnings strängen när du konfigurerar verktyget Azure IoT Explorer. Hämta anslutnings strängen och anteckna den nu. Ersätt `my-pnp-hub` med det unika namn som du har valt för din IoT-hubb:

```azurecli-interactive
az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString
```

De flesta snabb starter och självstudier använder *ID-omfånget* för DPS-konfigurationen. Hämta ID-omfånget och anteckna det nu. Ersätt `my-pnp-dps` med det unika namn som du har valt för din DPS-instans:

```azurecli-interactive
az iot dps show --name my-pnp-dps --query properties.idScope
```

Alla snabb starter och självstudier använder en DPS-registrering. Använd följande kommando för att skapa en `my-pnp-device` *enskild enhets registrering* i DPS-instansen. Ersätt `my-pnp-dps` med det unika namn som du har valt för din DPS-instans. Anteckna registrerings-ID och primär nyckel värden som ska användas i snabb starter och självstudier:

```azurecli-interactive
az iot dps enrollment create --attestation-type symmetrickey --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --enrollment-id my-pnp-device --device-id my-pnp-device --query '{registrationID:registrationId,primaryKey:attestation.symmetricKey.primaryKey}'
```

## <a name="create-environment-variables"></a>Skapa miljövariabler

Skapa fem miljövariabler för att konfigurera exemplen i snabb starterna och självstudierna för att använda enhets etablerings tjänsten (DPS) för att ansluta till din IoT-hubb:

* **IOTHUB_DEVICE_SECURITY_TYPE**: värdet `DPS` .
* **IOTHUB_DEVICE_DPS_ID_SCOPE**: DPS-ID-omfånget som du antecknade tidigare.
* **IOTHUB_DEVICE_DPS_DEVICE_ID**: värdet `my-pnp-device` .
* **IOTHUB_DEVICE_DPS_DEVICE_KEY**: den primära nyckeln för registrering som du antecknade tidigare.
* **IOTHUB_DEVICE_DPS_ENDPOINT**: värdet `global.azure-devices-provisioning.net`

Tjänst exemplen behöver följande miljövariabler för att identifiera hubben och enheten för att ansluta till:

* **IOTHUB_CONNECTION_STRING**: den IoT Hub-anslutningssträng som du antecknade tidigare.
* **IOTHUB_DEVICE_ID**: `my-pnp-device` .

Till exempel i ett Linux bash-gränssnitt:

```bash
export IOTHUB_DEVICE_SECURITY_TYPE="DPS"
export IOTHUB_DEVICE_DPS_ID_SCOPE="<Your ID scope>"
export IOTHUB_DEVICE_DPS_DEVICE_ID="my-pnp-device"
export IOTHUB_DEVICE_DPS_DEVICE_KEY="<Your enrolment primary key>"
export IOTHUB_DEVICE_DPS_ENDPOINT="global.azure-devices-provisioning.net"
export IOTHUB_CONNECTION_STRING="<Your IoT hub connection string>"
export IOTHUB_DEVICE_ID="my-pnp-device"
```

Till exempel på kommando raden i Windows:

```cmd
set IOTHUB_DEVICE_SECURITY_TYPE=DPS
set IOTHUB_DEVICE_DPS_ID_SCOPE=<Your ID scope>
set IOTHUB_DEVICE_DPS_DEVICE_ID=my-pnp-device
set IOTHUB_DEVICE_DPS_DEVICE_KEY=<Your enrolment primary key>
set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
set IOTHUB_CONNECTION_STRING=<Your IoT hub connection string>
set IOTHUB_DEVICE_ID=my-pnp-device
```

## <a name="download-the-model-files"></a>Ladda ned modell filerna

Snabb starterna och självstudierna använder exempel modell filer för temperatur styrenheten och termostat-enheter. Så här hämtar du exempel modell filerna:

1. Skapa en mapp med namnet *modeller* på den lokala datorn.

1. Högerklicka på [TemperatureController.jspå](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json) och spara JSON-filen i mappen *modeller* .

1. Högerklicka på [Thermostat.jspå](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json) och spara JSON-filen i mappen *modeller* .

## <a name="install-the-azure-iot-explorer"></a>Installera Azure IoT Explorer

Snabb starterna och självstudierna använder **Azure IoT Explorer** -verktyget. Gå till [Azure IoT Explorer-versioner](https://github.com/Azure/azure-iot-explorer/releases) och expandera listan med till gångar för den senaste versionen. Hämta och installera den senaste versionen av programmet för ditt operativ system.

Första gången du kör verktyget uppmanas du att ange anslutnings strängen för IoT Hub. Använd anslutnings strängen som du antecknade tidigare.

Konfigurera verktyget för att använda de modell filer som du laddade ned tidigare. Välj **IoT plug and Play inställningar** på Start sidan i verktyget och sedan **+ Lägg till > lokal mapp**. Välj mappen *modeller* som du skapade tidigare. Välj **Spara** för att spara inställningarna.

Mer information finns i [Installera och använda Azure IoT Explorer](howto-use-iot-explorer.md).

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda IoT Hub och DPS-instansen för alla IoT Plug and Play snabb starter och självstudier, så du behöver bara slutföra stegen i den här artikeln en gång. När du är klar kan du ta bort dem från prenumerationen med följande kommando:

```azurecli-interactive
az group delete --name my-pnp-resourcegroup
```

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat din miljö kan du prova någon av snabb starterna eller självstudierna som:

> [!div class="nextstepaction"]
> [Anslut ett exempel på IoT Plug and Play Device-program till IoT Hub](quickstart-connect-device.md)
