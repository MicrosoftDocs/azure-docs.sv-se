---
title: Förstå den inbyggda slut punkten för Azure IoT Hub | Microsoft Docs
description: Guide för utvecklare – beskriver hur du använder den inbyggda, Event Hub-kompatibla slut punkten för att läsa meddelanden från enheten till molnet.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom:
- amqp
- 'Role: Cloud Development'
ms.openlocfilehash: 4bb33721625f4fc752745ce2b43051c90b3aaa74
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92147681"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Läsa meddelanden från enhet till moln från den inbyggda slutpunkten

Som standard dirigeras meddelanden till den inbyggda slut punkten för servicen (**meddelanden/händelser**) som är kompatibel med [Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). Den här slut punkten exponeras för närvarande endast med [AMQP](https://www.amqp.org/) -protokollet på port 5671. En IoT-hubb visar följande egenskaper så att du kan kontrol lera de inbyggda Event Hub- **meddelanden/händelser** som är kompatibla med meddelande slut punkter.

| Egenskap            | Beskrivning |
| ------------------- | ----------- |
| **Antal partitioner** | Ange den här egenskapen vid skapande för att definiera antalet [partitioner](../event-hubs/event-hubs-features.md#partitions) för inmatningen från enhet till moln. |
| **Kvarhållningstid**  | Den här egenskapen anger hur lång tid i dagar som meddelanden ska behållas av IoT Hub. Standardvärdet är en dag, men det kan ökas till sju dagar. |

IoT Hub tillåter datakvarhållning i den inbyggda Event Hubs i högst 7 dagar. Du kan ange Retentions tiden när du skapar IoT Hub. Data lagrings tiden i IoT Hub beror på din IoT Hub-nivå och enhets typ. Med den inbyggda Event Hubs kan den inbyggda kvarhålla meddelanden med maximal meddelande storlek upp till minst 24 timmars kvot. Till exempel ger 1 S1-enhet IoT Hub tillräckligt med lagrings utrymme för att behålla minst 400K-meddelanden av 4K-storlek. Om enheterna skickar mindre meddelanden kan de bevaras under längre tid (upp till 7 dagar) beroende på hur mycket lagrings utrymme som används. Vi garanterar att du behåller data för den angivna Retentions tiden som minst. Meddelanden upphör att gälla och kommer inte att vara tillgängliga efter att Retentions tiden har gått ut. 

Med IoT Hub kan du också hantera konsument grupper på den inbyggda slut punkten för att få en enhet till molnet. Du kan ha upp till 20 konsument grupper för varje IoT Hub.

Om du använder [meddelanderoutning](iot-hub-devguide-messages-d2c.md) och [återställnings vägen](iot-hub-devguide-messages-d2c.md#fallback-route) är aktive rad, går alla meddelanden som inte matchar en fråga på någon väg till den inbyggda slut punkten. Om du inaktiverar den här återställnings vägen utelämnas meddelanden som inte matchar någon fråga.

Du kan ändra Retentions tiden, antingen via programmering med hjälp av [IoT Hub Resource Provider REST-API: er](/rest/api/iothub/iothubresource)eller med [Azure Portal](https://portal.azure.com).

IoT Hub visar den inbyggda slut punkten **meddelanden/händelser** för dina backend-tjänster för att läsa meddelanden från enheten till molnet som tas emot av hubben. Den här slut punkten är kompatibel med Event Hub, vilket gör att du kan använda någon av de mekanismer som Event Hubs tjänsten har stöd för för att läsa meddelanden.

## <a name="read-from-the-built-in-endpoint"></a>Läs från den inbyggda slut punkten

Vissa produkt integrationer och Event Hubs SDK: er är medvetna om IoT Hub och låter dig använda anslutnings strängen för IoT Hub-tjänsten för att ansluta till den inbyggda slut punkten.

När du använder Event Hubs SDK: er eller produkt integrationer som inte är IoT Hub behöver du ett Event Hub-kompatibelt slut punkts-och Event Hub-kompatibelt namn. Du kan hämta dessa värden från portalen på följande sätt:

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din IoT-hubb.

2. Klicka på **inbyggda slut punkter**.

3. Avsnittet **Events** innehåller följande värden: **partitioner**, **Event Hub-kompatibla namn**, **Event Hub-kompatibel slut punkt**, **kvarhållningsperiod** och **konsument grupper**.

    ![Inställningar för enhet till moln](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

I portalen innehåller fältet Event Hub-kompatibel slut punkt en fullständig Event Hubs anslutnings sträng som ser ut så här: **slut punkt = SB://abcd1234namespace.ServiceBus.Windows.net/; SharedAccessKeyName = iothubowner; SharedAccessKey = keykeykeykeykeykey =; EntityPath = iothub-ehub-ABCD-1234-123456**. Om SDK: n som du använder kräver andra värden blir de:

| Name | Värde |
| ---- | ----- |
| Slutpunkt | sb://abcd1234namespace.servicebus.windows.net/ |
| Värdnamn | abcd1234namespace.servicebus.windows.net |
| Namnområde | abcd1234namespace |

Du kan sedan välja en princip för delad åtkomst från List rutan som visas i skärm bilden ovan. Det visar bara principer som har **ServiceConnect** -behörighet att ansluta till den angivna händelsehubben.

SDK: er som du kan använda för att ansluta till den inbyggda Event Hub-kompatibla slut punkten som IoT Hub visar är:

| Språk | SDK | Exempel |
| -------- | --- | ------ |
| .NET | https://www.nuget.org/packages/Azure.Messaging.EventHubs | [Snabbstart](quickstart-send-telemetry-dotnet.md) |
| Java | https://mvnrepository.com/artifact/com.azure/azure-messaging-eventhubs | [Snabbstart](quickstart-send-telemetry-java.md) |
| Node.js | https://www.npmjs.com/package/@azure/event-hubs | [Snabbstart](quickstart-send-telemetry-node.md) |
| Python | https://pypi.org/project/azure-eventhub/ | [Snabbstart](quickstart-send-telemetry-python.md) |

Produkt integreringarna som du kan använda med den inbyggda Event Hub-kompatibla slut punkten som IoT Hub visar är:

* [Azure Functions](../azure-functions/index.yml). Se [bearbetning av data från IoT Hub med Azure Functions](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Azure Stream Analytics](../stream-analytics/index.yml). Se [strömma data som indata i Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Time Series Insights](../time-series-insights/index.yml). Se [lägga till en händelse källa för IoT Hub i din Time Series Insights-miljö](../time-series-insights/how-to-ingest-data-iot-hub.md).
* [Apache Storm kanalen](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Du kan visa [kanalen-källan](https://github.com/apache/storm/tree/master/external/storm-eventhubs) på GitHub.
* [Apache Spark-integrering](../hdinsight/spark/apache-spark-ipython-notebook-machine-learning.md).
* [Azure Databricks](/azure/azure-databricks/).

## <a name="next-steps"></a>Nästa steg

* Mer information om IoT Hub slut punkter finns i [IoT Hub slut punkter](iot-hub-devguide-endpoints.md).

* [Snabb starterna](quickstart-send-telemetry-node.md) visar hur du skickar meddelanden från enheten till molnet från simulerade enheter och läser meddelandena från den inbyggda slut punkten. 

Mer information finns i självstudierna [Process IoT Hub enhet-till-moln-meddelanden med hjälp av rutter](tutorial-routing.md) .

* Om du vill skicka meddelanden från enheten till molnet till anpassade slut punkter, se [Använd meddelande vägar och anpassade slut punkter för meddelanden från enhet till moln](iot-hub-devguide-messages-read-custom.md).