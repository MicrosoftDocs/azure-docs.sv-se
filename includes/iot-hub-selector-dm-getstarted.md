---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 239ea2eb4d5bc8d326d5ca503a18b149252dc1be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "69558734"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Backend-appar kan använda Azure IoT Hub primitiver, till exempel [enhets dubbla][lnk-devtwin] och [direkta metoder][lnk-c2dmethod], för att fjärrstarta och övervaka enhets hanterings åtgärder på enheter. Den här självstudien visar hur en backend-app och en app-app kan arbeta tillsammans för att initiera och övervaka en fjärromstart av enheten med hjälp av IoT Hub.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Använd en direkt metod för att initiera enhets hanterings åtgärder (t. ex. omstart, fabriks återställning och uppdatering av inbyggd program vara) från en backend-app i molnet. Enheten ansvarar för:

* Hantering av metoden Request som skickas från IoT Hub.

* Initierar motsvarande enhetsspecifika åtgärd på enheten.

* Tillhandahålla status uppdateringar via *rapporterade egenskaper* till IoT Hub.

Du kan använda en backend-app i molnet för att köra enhets dubbla frågor för att rapportera om förloppet för enhets hanterings åtgärderna.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
