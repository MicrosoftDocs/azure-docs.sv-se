---
title: Fel sökning i Azure Communication Services
description: Lär dig hur du samlar in den information du behöver för att felsöka kommunikations tjänst lösningen.
author: manoskow
manager: jken
services: azure-communication-services
ms.author: manoskow
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: daa89380894a57e58191edd95303a2160846da04
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492701"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Fel sökning i Azure Communication Services

I det här dokumentet får du hjälp med fel sökning av problem som kan uppstå i kommunikations tjänst lösningen. Om du ska felsöka SMS kan du [Aktivera leverans rapportering med event Grid](../quickstarts/telephony-sms/handle-sms-events.md) för att avbilda information om SMS-leverans.

## <a name="getting-help"></a>Få hjälp

Vi uppmuntrar utvecklare att skicka frågor, föreslå funktioner och rapportera problem som problem. För att göra detta har vi en [dedikerad support-och hjälp alternativ sida](../support.md) som visar dina alternativ för support.

För att hjälpa dig att felsöka vissa typer av problem kan du bli ombedd att ange någon av följande delar av informationen:

* **MS-CV-ID**: detta ID används för att felsöka samtal och meddelanden.
* **Anrops-ID**: detta ID används för att identifiera kommunikations tjänst anrop.
* **SMS-meddelande-ID**: detta ID används för att identifiera SMS-meddelanden.
* **Anrops loggar**: dessa loggar innehåller detaljerad information som kan användas för att felsöka anrop och nätverks problem.


## <a name="access-your-ms-cv-id"></a>Få åtkomst till ditt MS-CV ID

MS-CV ID kan nås genom att konfigurera diagnostik i `clientOptions` objekt instansen när du initierar klient biblioteken. Diagnostik kan konfigureras för alla Azure-klient bibliotek, till exempel chatt, identitet och VoIP-samtal.

### <a name="client-options-example"></a>Exempel på klient alternativ

Följande kodfragment demonstrerar diagnostik-konfigurationen. När klient biblioteken används med diagnostik aktive rad kommer diagnostikinformation att skickas till den konfigurerade händelse lyssnaren:

# <a name="c"></a>[C#](#tab/csharp)
```
// 1. Import Azure.Core.Diagnostics
using Azure.Core.Diagnostics;

// 2. Initialize an event source listener instance
using var listener = AzureEventSourceListener.CreateConsoleLogger();
Uri endpoint = new Uri("https://<RESOURCE-NAME>.communication.azure.net");
var (token, communicationUser) = await GetCommunicationUserAndToken();
CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(token);

// 3. Setup diagnostic settings
var clientOptions = new ChatClientOptions()
{
    Diagnostics =
    {
        LoggedHeaderNames = { "*" },
        LoggedQueryParameters = { "*" },
        IsLoggingContentEnabled = true,
    }
};

// 4. Initialize the ChatClient instance with the clientOptions
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential, clientOptions);
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync("Thread Topic", new[] { new ChatThreadMember(communicationUser) });
```

# <a name="python"></a>[Python](#tab/python)
```
from azure.communication.chat import ChatClient, CommunicationUserCredential
endpoint = "https://communication-services-sdk-live-tests-for-python.communication.azure.com"
chat_client = ChatClient(
    endpoint,
    CommunicationUserCredential(token),
    http_logging_policy=your_logging_policy)
```
---

## <a name="access-your-call-id"></a>Åtkomst till ditt anrops-ID

När du skickar en supportbegäran via Azure Portal som rör anrop till anrop kan du bli ombedd att ange ID för det anrop som du refererar till. Detta kan nås via det anropande klient biblioteket:

# <a name="javascript"></a>[JavaScript](#tab/javascript)
```javascript
// `call` is an instance of a call created by `callAgent.call` or `callAgent.join` methods
console.log(call.id)
```

# <a name="ios"></a>[iOS](#tab/ios)
```objc
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// todo: the code snippet suggests it's a property while the comment suggests it's a method call
print(call.callId)
```

# <a name="android"></a>[Android](#tab/android)
```java
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// `call` is an instance of a call created by `callAgent.call(…)` or `callAgent.join(…)` methods
Log.d(call.getCallId())
```
---

## <a name="access-your-sms-message-id"></a>Få åtkomst till SMS-meddelandets ID

För SMS-problem kan du samla in meddelande-ID: t från objektet Response.

# <a name="net"></a>[.NET](#tab/dotnet)
```
// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
async function main() {
  const result = await smsClient.send({
    from: "+18445792722",
    to: ["+1972xxxxxxx"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true // Optional parameter
  });
console.log(result); // your message ID will be in the result
}
```
---

## <a name="enable-and-access-call-logs"></a>Aktivera och få åtkomst till samtals loggar

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Följande kod kan användas för att konfigurera `AzureLogger` för att spara loggar till-konsolen med hjälp av Java Script-klient biblioteket:

```javascript
import { AzureLogger } from '@azure/logger';

AzureLogger.verbose = (...args) => { console.info(...args); }
AzureLogger.info = (...args) => { console.info(...args); }
AzureLogger.warning = (...args) => { console.info(...args); }
AzureLogger.error = (...args) => { console.info(...args); }

callClient = new CallClient({logger: AzureLogger});
```

# <a name="ios"></a>[iOS](#tab/ios)

När du utvecklar för iOS lagras dina loggar i `.blog` filer. Observera att du inte kan visa loggarna direkt eftersom de är krypterade.

Dessa kan nås genom att öppna Xcode. Gå till Windows > enheter och simulatorer > enheter. Välj din enhet. Under installerade appar väljer du ditt program och klickar på "Hämta behållare".

Då får du en `xcappdata` fil. Högerklicka på filen och välj Visa paket innehåll. Därefter ser du de `.blog` filer som du sedan kan koppla till din support förfrågan för Azure.

# <a name="android"></a>[Android](#tab/android)

När du utvecklar för Android lagras dina loggar i `.blog` filer. Observera att du inte kan visa loggarna direkt eftersom de är krypterade.

På Android Studio navigerar du till enhetens fil Utforskaren genom att välja Visa > verktyget Windows > Device File Explorer från både simulatorn och enheten. `.blog`Filen kommer att finnas i programmets katalog, vilket bör se ut ungefär så här `/data/data/[app_name_space:com.contoso.com.acsquickstartapp]/files/acs_sdk.blog` . Du kan bifoga filen till din support förfrågan.


---

## <a name="calling-client-library-error-codes"></a>Anrops fel koder för klient bibliotek

Azure Communication Services som anropar klient biblioteket använder följande felkoder för att felsöka anrops problem. De här fel koderna exponeras genom `call.callEndReason` egenskapen när ett anrop har slutförts.

| Felkod | Beskrivning | Åtgärd att vidta |
| -------- | ---------------| ---------------|
| 403 | Förbjuden/autentiseringsfel. | Se till att din token för kommunikations tjänster är giltig och inte har upphört att gälla. |
| 404 | Anropet hittades inte. | Kontrol lera att numret du ringer (eller ring du ansluter till) finns. |
| 408 | Tids gränsen uppnåddes för anrops kontroll. | Tids gränsen för uppringnings styrenheten nåddes i väntan på protokoll meddelanden från användar slut punkter. Se till att klienterna är anslutna och tillgängliga. |
| 410 | Lokal medie stack eller fel i medie infrastruktur. | Se till att du använder det senaste klient biblioteket i en miljö som stöds. |
| 430 | Det gick inte att leverera meddelandet till klient programmet. | Kontrol lera att klient programmet körs och är tillgängligt. |
| 480 | Fjärrklientens slut punkt har inte registrerats. | Se till att Fjärrslutpunkten är tillgänglig. |
| 481 | Det gick inte att hantera inkommande anrop. | Skicka en support förfrågan via Azure Portal. |
| 487 | Anropet avbröts, avböjdes lokalt, avslutades på grund av ett problem med slut punkts avvikelse eller kunde inte generera medie erbjudandet. | Förväntat beteende. |
| 490, 491, 496, 487, 498 | Nätverks problem med lokal slut punkt. | Kontrol lera nätverket. |
| 500, 503, 504 | Infrastruktur fel i kommunikations tjänster. | Skicka en support förfrågan via Azure Portal. |
| 603 | Anropet har avvisats globalt av deltagare i fjärr kommunikations tjänster | Förväntat beteende. |

## <a name="related-information"></a>Relaterad information
- [Loggar och diagnostik](logging-and-diagnostics.md)
- [Mått](metrics.md)
