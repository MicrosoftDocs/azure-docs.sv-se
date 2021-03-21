---
title: Azure IoT-enhets hantering med IoT-tillägg för Azure CLI | Microsoft Docs
description: Använd IoT-tillägget för Azure CLI-verktyget för Azure IoT Hub enhets hantering, med de direkta metoderna och de dubbla alternativen för önskade egenskaper för hantering.
author: chrissie926
manager: ''
keywords: Azure IoT-enhets hantering, Azure IoT Hub enhets hantering, enhets hantering IoT, IoT Hub enhets hantering
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 499c0c065cb38313c98ed9412ab1f08d70dbc654
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199534"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Använd IoT-tillägget för Azure CLI för Azure IoT Hub enhets hantering

![Diagram från slut punkt till slut punkt](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension) är ett IoT-tillägg med öppen källkod som lägger till funktionerna i [Azure CLI](/cli/azure/overview). Azure CLI innehåller kommandon för att interagera med Azure Resource Manager-och hanterings slut punkter. Du kan till exempel använda Azure CLI för att skapa en virtuell Azure-dator eller en IoT-hubb. Ett CLI-tillägg gör det möjligt för en Azure-tjänst att förstärka Azure CLI och ger dig till gång till ytterligare tjänstspecifika funktioner. IoT-tillägget ger IoT-utvecklare kommando rads åtkomst till alla IoT Hub, IoT Edge och IoT Hub Device Provisioning Service funktioner.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Hanterings alternativ          | Uppgift  |
|----------------------------|-----------|
| Direkta metoder             | Se till att en enhet fungerar som att starta eller stoppa sändning av meddelanden eller starta om enheten.                                        |
| Dubbla önskade egenskaper    | Placera en enhet i vissa tillstånd, till exempel att ställa in en indikator på grönt eller ställa in ett intervall för telemetri-sändning till 30 minuter.         |
| Dubbla rapporterade egenskaper   | Hämta rapporterat tillstånd för en enhet. Enheten rapporterar till exempel LAMPAn att blinka nu.                                    |
| Dubbla Taggar                  | Lagra enhetsspecifika metadata i molnet. Till exempel distributions platsen för en Vending-dator.                         |
| Enhets dubbla frågor        | Fråga alla enheter så att de kan hämta de dubbla med valfria villkor, till exempel identifiera vilka enheter som är tillgängliga för användning. |

Mer detaljerad information om skillnaderna och vägledning om hur du använder dessa alternativ finns i [kommunikations vägledning för enhet till moln](iot-hub-devguide-d2c-guidance.md) och [kommunikation från moln till enhet](iot-hub-devguide-c2d-guidance.md).

Enhetstvillingar är JSON-dokument som lagrar information om enhetstillstånd (metadata, konfigurationer och villkor). IoT Hub sparar en enhet för varje enhet som ansluter till den. Mer information om enhets uppflätade finns i [Kom igång med enhets dubbla](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Detta får du får lära dig

Du lär dig att använda IoT-tillägget för Azure CLI med olika hanterings alternativ på din utvecklings dator.

## <a name="what-you-do"></a>Vad du gör

Kör Azure CLI och IoT-tillägget för Azure CLI med olika hanterings alternativ.

## <a name="what-you-need"></a>Detta behöver du

* Slutför själv studie kursen om [Raspberry Pi online Simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) eller någon av enhets självstudierna. till exempel [Raspberry Pi med node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Dessa objekt behandlar följande krav:

  - En aktiv Azure-prenumeration.
  - En Azure IoT-hubb under din prenumeration.
  - Ett klient program som skickar meddelanden till Azure IoT Hub.

* Kontrol lera att enheten körs med klient programmet under den här självstudien.

* [Python 2.7 x eller python 3. x](https://www.python.org/downloads/)

* Azure CLI. Om du behöver installera det kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Som minst måste din Azure CLI-version vara 2.0.70 eller högre. Validera med `az –version`.

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

## <a name="device-twin-desired-properties"></a>Enhetens dubbla önskade egenskaper

Ange önskat egenskaps intervall = 3000 genom att köra följande kommando:

```azurecli
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Den här egenskapen kan läsas från din enhet.

## <a name="device-twin-reported-properties"></a>Enhetens dubbla rapporterade egenskaper

Hämta enhetens rapporterade egenskaper genom att köra följande kommando:

```azurecli
az iot hub device-twin show -n <your hub name> -d <your device id>
```

En av de dubbla rapporterade egenskaperna är $metadata. $lastUpdated, som visar senaste gången enhets appen uppdaterade den rapporterade egenskapen.

## <a name="device-twin-tags"></a>Enhetens dubbla Taggar

Visa Taggar och egenskaper för enheten genom att köra följande kommando:

```azurecli
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Lägg till en fält roll = temperatur&fuktighet till enheten genom att köra följande kommando:

```azurecli
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Enhets dubbla frågor

Fråga enheter med en tagg för Role = "temperatur&fuktighet" genom att köra följande kommando:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Fråga alla enheter förutom de med en tagg för roll = temperatur&fuktighet genom att köra följande kommando:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du övervakar meddelanden från enheten till molnet och skickar meddelanden från molnet till enheten mellan din IoT-enhet och Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]