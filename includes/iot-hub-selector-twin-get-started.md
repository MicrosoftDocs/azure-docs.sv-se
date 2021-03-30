---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.openlocfilehash: c412f7f3e20e4d04083e457bfb245b850b65e126
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "70050465"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Enhetstvillingar är JSON-dokument som lagrar information om enhetstillstånd, som metadata, konfigurationer och villkor. IoT Hub sparar en enhet för varje enhet som ansluter till den.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Använd enheten:

* Lagra metadata för enheten från Server delen av lösningen.

* Rapportera aktuell statusinformation, till exempel tillgängliga funktioner och villkor, till exempel den anslutnings metod som används, från din enhets app.

* Synkronisera tillstånd för långvariga arbets flöden, till exempel inbyggd program vara och konfigurations uppdateringar, mellan en enhets app och en backend-app.

* Fråga om metadata, konfiguration eller tillstånd för enheten.

Enhets grupper är utformade för synkronisering och för att ställa frågor om enhets konfiguration och villkor. Mer information om när du ska använda enhets uppdelade enheter finns i [förstå enhets dubbla](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Enhetens dubbla objekt lagras i en IoT-hubb och innehåller följande element:

* **Taggar**. Enhetens metadata är bara tillgängliga från Server delen av lösningen.

* **Önskade egenskaper**. JSON-objekt ändras av lösningens Server del och styrs av Device-appen.

* **Rapporterade egenskaper**. JSON-objekt som ändras av Device-appen och som är läsbara av lösningens Server del.

Taggar och egenskaper får inte innehålla matriser, men objekt kan kapslas.

Följande bild visar enhetens dubbla organisation:

![Enhetens dubbla bild som visar funktioner](./media/iot-hub-selector-twin-get-started/twin.png)

Lösningens Server del kan dessutom fråga enheten med varandra baserat på alla dessa data.
Mer information om enhets uppflätade enheter finns i [förstå enhets dubbla](../articles/iot-hub/iot-hub-devguide-device-twins.md). Mer information om frågor finns i [IoT Hub frågespråk](../articles/iot-hub/iot-hub-devguide-query-language.md).


I den här självstudiekursen lär du dig att:

* Skapa en backend-app som lägger till taggar till en enhet och en simulerad enhets app som rapporterar anslutnings kanalen som en rapporterad egenskap på enheten.

* Fråga enheter från din backend-app med hjälp av filter på de taggar och egenskaper som skapats tidigare.
