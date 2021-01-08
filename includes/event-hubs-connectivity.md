---
title: ta med fil
description: ta med fil
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7ebb9dbce020086a716872c86221b97b4b7a6653
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97978897"
---
### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Vilka portar måste jag öppna i brand väggen? 
Du kan använda följande protokoll med Azure Event Hubs för att skicka och ta emot händelser:

- Advanced Message Queueing Protocol 1,0 (AMQP)
- Hypertext Transfer Protocol 1,1 med TLS (HTTPS)
- Apache Kafka

Se följande tabell för utgående portar som du måste öppna för att kunna använda dessa protokoll för att kommunicera med Azure Event Hubs. 

| Protokoll | Portar | Information | 
| -------- | ----- | ------- | 
| AMQP | 5671 och 5672 | Se [AMQP-protokoll guide](../articles/service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTPS | 443 | Den här porten används för HTTP/REST API och för AMQP. |
| Kafka | 9093 | Se [använda Event Hubs från Kafka-program](../articles/event-hubs/event-hubs-for-kafka-ecosystem-overview.md)

HTTPS-porten krävs för utgående kommunikation även när AMQP används över Port 5671, eftersom flera hanterings åtgärder som utförs av klient-SDK: er och förvärv av tokens från Azure Active Directory (när de används) körs över HTTPS. 

De officiella Azure-SDK: erna använder vanligt vis AMQP-protokollet för att skicka och ta emot händelser från Event Hubs. Protokoll alternativet AMQP-över-WebSockets körs via port TCP 443 precis som HTTP API, men är i övrigt identiskt med enkel AMQP. Det här alternativet har högre första anslutnings latens på grund av extra handskakningar i tur och upp till något mer som kompromisser för att dela HTTPS-porten. Om det här läget är valt räcker TCP-port 443 för kommunikation. Med följande alternativ kan du välja läget för enkel AMQP eller AMQP WebSockets:

| Språk | Alternativ   |
| -------- | ----- |
| .NET     | [EventHubConnectionOptions. TransportType](/dotnet/api/azure.messaging.eventhubs.eventhubconnectionoptions.transporttype?view=azure-dotnet&preserve-view=true) -egenskapen med [EventHubsTransportType. AmqpTcp](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype?view=azure-dotnet&preserve-view=true) eller [EventHubsTransportType. AmqpWebSockets](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype?view=azure-dotnet&preserve-view=true) |
| Java     | [com. Microsoft. Azure. eventhubs. EventProcessorClientBuilder. transporttype](/java/api/com.azure.messaging.eventhubs.eventprocessorclientbuilder.transporttype?view=azure-java-stable&preserve-view=true) med [AmqpTransportType. AMQP](/java/api/com.azure.core.amqp.amqptransporttype?view=azure-java-stable&preserve-view=true) eller [AmqpTransportType.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype?view=azure-java-stable&preserve-view=true) |
| Nod  | [EventHubConsumerClientOptions](/javascript/api/@azure/event-hubs/eventhubconsumerclientoptions?view=azure-node-latest&preserve-view=true) har en- `webSocketOptions` egenskap. |
| Python | [EventHubConsumerClient.transport_type](/python/api/azure-eventhub/azure.eventhub.eventhubconsumerclient?view=azure-python&preserve-view=true) med [TransportType. AMQP](/python/api/azure-eventhub/azure.eventhub.transporttype?view=azure-python) eller [TransportType. AmqpOverWebSocket](/python/api/azure-eventhub/azure.eventhub.transporttype?view=azure-python&preserve-view=true) |

### <a name="what-ip-addresses-do-i-need-to-allow"></a>Vilka IP-adresser måste jag tillåta?
När du arbetar med Azure måste du ibland tillåta vissa IP-adressintervall eller URL: er i företagets brand vägg eller proxy för att få åtkomst till alla Azure-tjänster som du använder eller försöker använda. Kontrol lera att trafiken tillåts på IP-adresser som används av Event Hubs. För IP-adresser som används av Azure Event Hubs: se [Azure IP-intervall och service märken – offentligt moln](https://www.microsoft.com/download/details.aspx?id=56519).

Kontrol lera också att IP-adressen för ditt namn område är tillåten. Följ dessa steg om du vill hitta rätt IP-adresser som tillåts för dina anslutningar:

1. Kör följande kommando från en kommando tolk: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Anteckna IP-adressen som returnerades i `Non-authoritative answer` . 

Om du använder **zon redundans** för ditt namn område måste du utföra några extra steg: 

1. Först kör du nslookup i namn området.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Anteckna namnet i avsnittet **icke-auktoritativt svar** , vilket är i något av följande format: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Kör nslookup för var och en med suffix S1, S2 och S3 för att hämta IP-adresserna för alla tre instanser som körs i tre tillgänglighets zoner. 

    > [!NOTE]
    > Den IP-adress som returnerades av `nslookup` kommandot är inte en statisk IP-adress. Det förblir dock konstant tills den underliggande distributionen tas bort eller flyttas till ett annat kluster.

### <a name="what-client-ips-are-sending-events-to-or-receiving-events-from-my-namespace"></a>Vilka klient-IP-adresser skickar händelser till eller tar emot händelser från min namnrymd?
Börja med att aktivera [IP-filtrering](../articles/event-hubs/event-hubs-ip-filtering.md) i namn området. 

Aktivera sedan diagnostikloggar för [Event Hubs händelser för virtuella nätverks anslutningar](../articles/event-hubs/event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema) genom att följa anvisningarna i [Aktivera diagnostikloggar](../articles/event-hubs/event-hubs-diagnostic-logs.md#enable-diagnostic-logs). Du ser IP-adressen för vilken anslutningen nekas.

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

> [!IMPORTANT]
> Virtuella nätverks loggar skapas endast om namn området tillåter åtkomst från **vissa IP-adresser** (IP filter regler). Om du inte vill begränsa åtkomsten till ditt namn område med dessa funktioner och fortfarande vill hämta virtuella nätverks loggar för att spåra IP-adresser för klienter som ansluter till Event Hubs-namnrymden, kan du använda följande lösning: aktivera IP-filtrering och Lägg till det totala adresser bara IPv4-intervallet (1.0.0.0/1-255.0.0.0/1). Event Hubs stöder inte IPv6-adress intervall. 

> [!NOTE]
> För närvarande går det inte att fastställa käll-IP för ett enskilt meddelande eller en händelse. 