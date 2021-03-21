---
title: Inbyggda edgeAgent Direct-metoder – Azure IoT Edge
description: Övervaka och hantera en IoT Edge-distribution med hjälp av inbyggda direkta metoder i modulen IoT Edge agent körning
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 52cd7817594c5c2a1d4e3a4ca9c56891df594cd6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201115"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Kommunicera med edgeAgent med hjälp av inbyggda direkta metoder

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Övervaka och hantera IoT Edge-distributioner med hjälp av de direkta metoder som ingår i modulen för IoT Edge agent. Direkta metoder implementeras på enheten och kan sedan anropas från molnet. IoT Edge agenten innehåller direkta metoder som hjälper dig att övervaka och hantera dina IoT Edge-enheter via fjärr anslutning.

Mer information om direkta metoder, hur du använder dem och hur du implementerar dem i dina egna moduler finns i [förstå och anropa direkt metoder från IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md).

Namnen på dessa direkta metoder hanteras Skift läges okänsliga.

## <a name="ping"></a>Pinga

**Ping** -metoden är användbar för att kontrol lera om IoT Edge körs på en enhet eller om enheten har en öppen anslutning till IoT Hub. Använd den här direkta metoden för att pinga IoT Edge-agenten och hämta dess status. En lyckad ping returnerar en tom nytto last och **"status": 200**.

Exempel:

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

I Azure Portal anropar du metoden med metod namnet `ping` och en tom JSON-nyttolast `{}` .

![Anropa direkt metoden ping i Azure Portal](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>Starta om modul

**RestartModule** -metoden möjliggör fjärrhantering av moduler som körs på en IoT Edge enhet. Om en modul rapporterar ett felaktigt tillstånd eller ett dåligt fel, kan du aktivera den IoT Edge agenten för att starta om den. Ett slutfört omstart-kommando returnerar en tom nytto last och **"status": 200**.

Metoden RestartModule finns i IoT Edge version 1.0.9 och senare. 

Du kan använda metoden RestartModule Direct i alla moduler som körs på en IoT Edge enhet, inklusive själva edgeAgent-modulen. Men om du använder den här direkta metoden för att stänga av edgeAgent får du inte ett lyckat resultat eftersom anslutningen avbryts medan modulen startas om.

Exempel:

```azurecli
az iot hub invoke-module-method --method-name 'RestartModule' -n <hub name> -d <device name> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "id": "<module name>"
    }
'
```

Anropa metoden med metod namnet `RestartModule` och följande JSON-nyttolast i Azure Portal:

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Anropa Direct-metoden ' RestartModule ' i Azure Portal](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="diagnostic-direct-methods"></a>Diagnostiska direkta metoder

* [GetModuleLogs](how-to-retrieve-iot-edge-logs.md#retrieve-module-logs): Hämta modul loggar infogat i svaret på den direkta metoden.
* [UploadModuleLogs](how-to-retrieve-iot-edge-logs.md#upload-module-logs): Hämta modul loggar och ladda upp dem till Azure Blob Storage.
* [UploadSupportBundle](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics): Hämta modul loggar med hjälp av ett support paket och ladda upp en zip-fil till Azure Blob Storage.
* [GetTaskStatus](how-to-retrieve-iot-edge-logs.md#get-upload-request-status): kontrol lera statusen för en uppladdnings logg eller begäran om support paket.

Dessa diagnostiska direkta metoder är tillgängliga från och med 1.0.10-versionen.

## <a name="next-steps"></a>Nästa steg

[Egenskaperna för IoT Edge agenten och IoT Edge Hub-modulen är dubbla](module-edgeagent-edgehub.md)
