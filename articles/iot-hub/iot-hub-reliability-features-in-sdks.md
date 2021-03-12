---
title: 'Hantera IoT Hub anslutning & Reliable Messaging med enhets-SDK: er'
description: 'Lär dig hur du kan förbättra din enhets anslutning och dina meddelanden när du använder Azure IoT Hub-enhetens SDK: er'
services: iot-hub
author: robinsh
ms.author: robinsh
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
ms.custom:
- amqp
- mqtt
- devx-track-csharp
ms.openlocfilehash: efd16f0e8dd6ba952b647fbfbd6f35fc05ffd4be
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615847"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>Hantera anslutningar och Reliable Messaging med hjälp av Azure IoT Hub enhets-SDK: er

Den här artikeln innehåller rikt linjer som hjälper dig att utforma enhets program som är mer elastiska. Det visar hur du kan dra nytta av anslutningen och Reliable Messaging-funktionerna i Azure IoT-enhetens SDK: er. Målet med den här guiden är att hjälpa dig att hantera följande scenarier:

* Korrigera en avbruten nätverks anslutning

* Växla mellan olika nätverks anslutningar

* Återansluter på grund av tillfälliga anslutnings fel i tjänsten

Implementerings informationen kan variera beroende på språk. Mer information finns i API-dokumentationen eller i det angivna SDK:

* [C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [SDK för Node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python) (pålitlighet ännu inte implementerat)

## <a name="designing-for-resiliency"></a>Designa för elasticitet

IoT-enheter är ofta beroende av icke-kontinuerliga eller instabila nätverks anslutningar (till exempel GSM eller satellit). Fel kan uppstå när enheter interagerar med molnbaserade tjänster på grund av tillfällig tillgänglighet på tjänster och infrastruktur nivå eller tillfälliga fel. Ett program som körs på en enhet måste hantera mekanismerna för anslutning, åter anslutning och omprövnings logik för att skicka och ta emot meddelanden. Kraven på strategi för återförsök är också beroende av enhetens IoT-scenario, kontext, funktioner.

Med SDK: er för Azure IoT Hub-enheter kan du förenkla anslutning och kommunikation från moln till enhet och från enhet till moln. Dessa SDK: er ger ett robust sätt att ansluta till Azure IoT Hub och en omfattande uppsättning alternativ för att skicka och ta emot meddelanden. Utvecklare kan också ändra befintlig implementering för att anpassa en bättre strategi för återförsök i ett specifikt scenario.

Relevanta SDK-funktioner som stöder anslutningar och Reliable Messaging beskrivs i följande avsnitt.

## <a name="connection-and-retry"></a>Anslutning och försök igen

Det här avsnittet ger en översikt över de mönster för anslutning och återförsök som är tillgängliga när du hanterar anslutningar. Den beskriver implementerings vägledningen för att använda en annan princip för återförsök i enhets programmet och listar relevanta API: er från enhetens SDK: er.

### <a name="error-patterns"></a>Fel mönster

Anslutnings problem kan inträffa på många nivåer:

* Nätverks fel: frånkopplade socket-och namn matchnings fel

* Fel på protokoll nivå för HTTP-, AMQP-och MQTT-transport: frånkopplade länkar eller utgångna sessioner

* Fel på program nivå som orsakas av antingen lokala misstag: ogiltiga autentiseringsuppgifter eller tjänst beteende (till exempel överskrida kvoten eller begränsningen)

Enhets-SDK: erna identifierar fel på alla tre nivåer. OS-relaterade fel och maskin varu fel identifieras och hanteras inte av enhets-SDK: erna. SDK-designen baseras på [den tillfälliga fel hanterings vägledningen](/azure/architecture/best-practices/transient-faults#general-guidelines) från Azure Architecture Center.

### <a name="retry-patterns"></a>Återförsöks mönster

Följande steg beskriver processen för att försöka igen när anslutnings fel upptäcks:

1. SDK identifierar felet och det associerade felet i nätverket, protokollet eller programmet.

2. SDK: n använder fel filtret för att fastställa feltypen och bestämmer om ett nytt försök krävs.

3. Om SDK: n identifierar ett **oåterkalleligt fel**, stoppas åtgärder som anslutning, skicka och ta emot. SDK meddelar användaren. Exempel på fel som inte går att återställa är ett autentiseringsfel och ett felaktigt slut punkts fel.

4. Om SDK: n identifierar ett **återställnings Bart fel** görs ett nytt försök enligt den angivna återförsöks principen tills den definierade tids gränsen har gått ut.  Observera att SDK använder **exponentiella säkerhets kopieringar med Darr-** återförsöks princip som standard.
5. När den definierade tids gränsen går ut slutar SDK: n att försöka ansluta eller skicka. Användaren meddelas.

6. Med SDK kan användaren bifoga en motringning för att ta emot ändringar av anslutnings status.

SDK: er tillhandahåller tre principer för återförsök:

* **Exponentiell säkerhets kopiering med Darr**: den här standard principen för återförsök tenderar att vara aggressiv vid starten och sakta ned över tid tills den når en maximal fördröjning. Designen baseras på vägledning om [återförsök från Azure Architecture Center](/azure/architecture/best-practices/retry-service-specific). 

* **Anpassat återförsök**: för vissa SDK-språk kan du utforma en anpassad princip för återförsök som passar bäst för ditt scenario och sedan mata in det i RetryPolicy. Det anpassade återförsöket är inte tillgängligt på C SDK och stöds för närvarande inte på python SDK. Python SDK återansluter vid behov.

* **Inget återförsök**: du kan ställa in principen för återförsök till "ingen återförsök", vilket inaktiverar logiken för omförsök. SDK försöker ansluta en gång och skicka ett meddelande en gång, förutsatt att anslutningen upprättas. Den här principen används vanligt vis i scenarier med bandbredd eller kostnads problem. Om du väljer det här alternativet går det inte att återställa meddelanden som inte kan skickas.

### <a name="retry-policy-apis"></a>Kör om princip-API: er

   | SDK | SetRetryPolicy-metod | Princip implementeringar | Implementerings vägledning |
   |-----|----------------------|--|--|
   |  C/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Standard**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Anpassad:** Använd tillgängliga [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Inga återförsök:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [C/iOS-implementering](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy)        | **Standard**: [ExponentialBackoffWithJitter-klass](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Anpassad:** implementera [RetryPolicy-gränssnitt](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Inga återförsök:** [noretry-klass](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Java-implementering](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DeviceClient. SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy) | **Standard**: [ExponentialBackoff-klass](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff)<BR>**Anpassad:** implementera [IRetryPolicy-gränssnitt](/dotnet/api/microsoft.azure.devices.client.iretrypolicy)<BR>**Inga återförsök:** [noretry-klass](/dotnet/api/microsoft.azure.devices.client.noretry) | [C#-implementering](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | Nod| [setRetryPolicy](/javascript/api/azure-iot-device/client) | **Standard**: [ExponentialBackoffWithJitter-klass](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java) | [Node-implementering](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   | Python| Stöds för närvarande inte | Stöds för närvarande inte | Stöds för närvarande inte |

Följande kod exempel illustrerar det här flödet:

#### <a name="net-implementation-guidance"></a>Vägledning för .NET-implementering

Följande kod exempel visar hur du definierar och anger standard principen för återförsök:

   ```csharp
   // define/set default retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

För att undvika hög CPU-användning begränsas försöken om koden Miss lyckas omedelbart. Till exempel när det inte finns något nätverk eller dirigerar till målet. Minimi tiden för att köra nästa nytt försök är 1 sekund.

Om tjänsten svarar med ett begränsnings fel är återförsöks principen annorlunda och kan inte ändras via offentliga API:

   ```csharp
   // throttled retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), 
     TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5)); SetRetryPolicy(retryPolicy);
   ```

Mekanismen för omförsök stoppas efter `DefaultOperationTimeoutInMilliseconds` , som för närvarande är inställd på 4 minuter.

#### <a name="other-languages-implementation-guidance"></a>Vägledning om implementering av andra språk

Granska följande implementerings dokument för kod exempel på andra språk. Lagrings platsen innehåller exempel som demonstrerar användningen av API: er för återförsök.

* [C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [SDK för Node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python) (pålitlighet ännu inte implementerat)

## <a name="next-steps"></a>Nästa steg

* [Använd SDK:er för enhet och tjänst](./iot-hub-devguide-sdks.md)

* [Använda SDK för IoT-enheter för C](./iot-hub-device-sdk-c-intro.md)

* [Utveckla för begränsade enheter](./iot-hub-devguide-develop-for-constrained-devices.md)

* [Utveckla för mobila enheter](./iot-hub-how-to-develop-for-mobile-devices.md)

* [Felsöka enhets koppling](iot-hub-troubleshoot-connectivity.md)