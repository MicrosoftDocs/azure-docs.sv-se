---
title: Azure IoT-enhetshantering med IoT-tillägg för Azure CLI | Microsoft Docs
description: Använd IoT-tillägget för Azure CLI-verktyget för Azure IoT Hub för enhetshantering med Direct-metoderna och tvillingens önskade egenskapshanteringsalternativ.
author: chrissie926
manager: ''
keywords: azure iot device management, azure iot hub device management, device management iot, iot hub device management
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: a5bc7e195efd62f430fdf2aa0cb606dbcff79528
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567204"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Använda IoT-tillägget för Azure CLI för Azure IoT Hub enhetshantering

![Diagram från end-to-end](media/iot-hub-get-started-e2e-diagram/2.png)

I den här artikeln får du lära dig hur du använder IoT-tillägget för Azure CLI med olika hanteringsalternativ på utvecklingsdatorn. [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension) är ett IoT-tillägg med öppen källkod som lägger till funktionerna i [Azure CLI.](/cli/azure/overview) Azure CLI innehåller kommandon för att interagera med Azure Resource Manager och hanteringsslutpunkter. Du kan till exempel använda Azure CLI för att skapa en virtuell Azure-dator eller en IoT-hubb. Med ett CLI-tillägg kan en Azure-tjänst utöka Azure CLI så att du får tillgång till ytterligare tjänstspecifika funktioner. IoT-tillägget ger IoT-utvecklare kommandoradsåtkomst till alla IoT Hub, IoT Edge och IoT Hub Device Provisioning Service funktioner.

| Hanteringsalternativ          | Uppgift  |
|----------------------------|-----------|
| Direkta metoder             | Gör så att en enhet till exempel startar eller slutar skicka meddelanden eller startar om enheten.                                        |
| Önskade egenskaper för tvilling    | Placera en enhet i vissa tillstånd, till exempel att ange en lysdiod till grön eller ställa in telemetrisändintervallet till 30 minuter.         |
| Tvillingrapporterade egenskaper   | Hämta det rapporterade tillståndet för en enhet. Enheten rapporterar till exempel att lysdioden blinkar nu.                                    |
| Tvillingtaggar                  | Lagra enhetsspecifika metadata i molnet. Till exempel distributionsplatsen för en varuautomat.                         |
| Frågor om enhetstvilling        | Fråga alla enhetstvillingarna för att hämta dessa tvillingar med godtyckliga villkor, till exempel identifiera de enheter som är tillgängliga för användning. |

Mer detaljerad förklaring av skillnaderna och vägledningen [](iot-hub-devguide-d2c-guidance.md) om hur du använder dessa alternativ finns i Vägledning för kommunikation från enhet till moln och Vägledning för kommunikation från moln [till enhet.](iot-hub-devguide-c2d-guidance.md)

Enhetstvillingar är JSON-dokument som lagrar information om enhetstillstånd (metadata, konfigurationer och villkor). IoT Hub en enhetstvilling för varje enhet som ansluter till den. Mer information om enhetstvillingarna finns i [Kom igång med enhetstvillingarna.](iot-hub-node-node-twin-getstarted.md)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

## <a name="prerequisites"></a>Förutsättningar

* Slutför [självstudien om Raspberry Pi-onlinesimulatorn](iot-hub-raspberry-pi-web-simulator-get-started.md) eller någon av självstudierna om enheten. Du kan till exempel gå till [Raspberry Pi med node.js](iot-hub-raspberry-pi-kit-node-get-started.md) eller till någon av snabbstarterna för att skicka [telemetri.](quickstart-send-telemetry-dotnet.md) De här artiklarna omfattar följande krav:

  * En aktiv Azure-prenumeration.
  * En Azure IoT-hubb under din prenumeration.
  * Ett klientprogram som skickar meddelanden till din Azure IoT-hubb.

* Kontrollera att enheten körs med klientprogrammet under den här självstudien.

* [Python 2.7x eller Python 3.x](https://www.python.org/downloads/)

* The Azure CLI. Om du behöver installera det kan du gå [till Installera Azure CLI.](/cli/azure/install-azure-cli) Din Azure CLI-version måste minst vara 2.0.70 eller senare. Validera med `az –version`.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* Installera IoT-tillägget. Det enklaste sättet är att köra `az extension add --name azure-iot`. I [IoT-tilläggets Viktigt-fil](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) beskrivs olika sätt att installera tillägget.

## <a name="sign-in-to-your-azure-account"></a>Logga in på ditt Azure-konto

Logga in på ditt Azure-konto genom att köra följande kommando:

```azurecli
az login
```

## <a name="direct-methods"></a>Direkta metoder

```azurecli
az iot hub invoke-device-method --device-id <your device id> \
  --hub-name <your hub name> \
  --method-name <the method name> \
  --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Önskade egenskaper för enhetstvilling

Ange ett önskat egenskapsintervall = 3 000 genom att köra följande kommando:

```azurecli
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Den här egenskapen kan läsas från din enhet.

## <a name="device-twin-reported-properties"></a>Rapporterade egenskaper för enhetstvilling

Hämta rapporterade egenskaper för enheten genom att köra följande kommando:

```azurecli
az iot hub device-twin show -n <your hub name> -d <your device id>
```

En av de tvillingrapporterade egenskaperna $metadata.$lastUpdated, vilket visar den senaste gången enhetsappen uppdaterade sin rapporterade egenskapsuppsättning.

## <a name="device-twin-tags"></a>Taggar för enhetstvilling

Visa taggar och egenskaper för enheten genom att köra följande kommando:

```azurecli
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Lägg till en fältroll = temperatur&luftfuktigheten till enheten genom att köra följande kommando:

```azurecli
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Frågor om enhetstvilling

Fråga enheter med en rolltagg = "temperature&humidity" genom att köra följande kommando:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Fråga alla enheter utom de med taggen role = "temperature&humidity" genom att köra följande kommando:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du övervakar "enhet till molnet"-meddelanden och skickar meddelanden från moln till enhet mellan din IoT-enhet och Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]