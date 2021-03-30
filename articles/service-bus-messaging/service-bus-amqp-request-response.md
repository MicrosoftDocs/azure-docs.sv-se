---
title: AMQP 1,0-begäran/svar-åtgärder i Azure Service Bus
description: Den här artikeln definierar listan över AMQP-baserade åtgärder för begäran/svar i Microsoft Azure Service Bus.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: b845f4086ee1ac4fe868571c1754caf6d29b9021
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88064425"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1,0 i Microsoft Azure Service Bus: Request-Response-based Operations

I den här artikeln definieras en lista över åtgärder baserade på Microsoft Azure Service Bus begäran/svars tid. Den här informationen baseras på arbets utkastet AMQP Management version 1,0.  
  
En detaljerad AMQP 1,0-protokoll guide, som beskriver hur Service Bus implementerar och bygger på den tekniska specifikationen för OASIS AMQP finns i [AMQP 1,0 i Azure Service Bus och Event Hubs protokoll guide][AMQP 1,0 Protocol guide].  
  
## <a name="concepts"></a>Begrepp  
  
### <a name="entity-description"></a>Entitetsbeskrivning  

En beskrivning av entiteten refererar till antingen en Service Bus [QueueDescription-klass](/dotnet/api/microsoft.servicebus.messaging.queuedescription), [TopicDescription-klass](/dotnet/api/microsoft.servicebus.messaging.topicdescription)eller [SubscriptionDescription Class](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) -objekt.  
  
### <a name="brokered-message"></a>Service Broker-meddelande  

Representerar ett meddelande i Service Bus, som är mappat till ett AMQP-meddelande. Mappningen definieras i [Service Bus AMQP-protokoll guide](service-bus-amqp-protocol-guide.md).  
  
## <a name="attach-to-entity-management-node"></a>Koppla till noden entitets hantering  

Alla åtgärder som beskrivs i det här dokumentet följer ett mönster för begäran/svar, är begränsade till en entitet och kräver att du ansluter till en nod för entitets hantering.  
  
### <a name="create-link-for-sending-requests"></a>Skapa länk för att skicka begär Anden  

Skapar en länk till noden hantering för att skicka begär Anden.  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>Skapa länk för att ta emot svar  

Skapar en länk för att ta emot svar från noden hantering.  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>Överför ett begär ande meddelande  

Överför ett meddelande om begäran.  
Du kan lägga till ett transaktions tillstånd för åtgärder som har stöd för transaktioner.

```
requestLink.sendTransfer(
        Message(
                properties: {
                        message-id: <request id>,
                        reply-to: "<my response link unique address>"
                },
                application-properties: {
                        "operation" -> "<operation>",
                }
        ),
        [Optional] State = transactional-state: {
                txn-id: <txn-id>
        }
)
```
  
### <a name="receive-a-response-message"></a>Ta emot ett svars meddelande  

Tar emot svarsmeddelandet från svars länken.  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
Svars meddelandet har följande format:
  
```
Message(
properties: {
        correlation-id: <request id>
    },
    application-properties: {
            "statusCode" -> <status code>,
            "statusDescription" -> <status description>,
           },
)

```
  
### <a name="service-bus-entity-address"></a>Service Bus enhets adress  

Service Bus entiteter måste åtgärdas på följande sätt:  
  
|Entitetstyp|Adress|Exempel|  
|-----------------|-------------|-------------|  
|kö|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|ämne|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|prenumeration|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Meddelande åtgärder  
  
### <a name="message-renew-lock"></a>Meddelande förnyelse lås  

Utökar meddelande låset vid den tid som anges i beskrivningen av entiteten.  
  
#### <a name="request"></a>Förfrågan  

Begär ande meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|operation|sträng|Ja|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Inga|Åtgärds serverns tids gräns i millisekunder.|  
  
 Meddelande texten för begäran måste bestå av ett AMQP-Value-avsnitt som innehåller en karta med följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|matris med uuid|Ja|Meddelande lås tokens som ska förnyas.|  

> [!NOTE]
> Lås tokens är `DeliveryTag` egenskapen för mottagna meddelanden. Se följande exempel i [.NET SDK](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) som hämtar dessa. Token kan också visas i "DeliveryAnnotations" som "x-opt-lock-token", men detta är inte garanterat och `DeliveryTag` bör vara lämpligt. 
> 
  
#### <a name="response"></a>Svarsåtgärder  

Svars meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades.|  
|statusDescription|sträng|No|Beskrivning av status.|  
  
Svars meddelande texten måste bestå av ett AMQP-Value-avsnitt som innehåller en karta med följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|förfallo tider|matris med tidsstämpel|Ja|Token för meddelande låset nya förfallo datum som motsvarar de Lås token för begäran.|  
  
### <a name="peek-message"></a>Granska meddelande  

Granskar meddelanden utan att låsa.  
  
#### <a name="request"></a>Förfrågan  

Begär ande meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|operation|sträng|Ja|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Inga|Åtgärds serverns tids gräns i millisekunder.|  
  
Meddelande texten för begäran måste bestå av ett **AMQP-Value-** avsnitt som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|Ja|Serie nummer från vilket du vill starta Peek.|  
|`message-count`|int|Ja|Maximalt antal meddelanden att granska.|  
  
#### <a name="response"></a>Svarsåtgärder  

Svars meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – innehåller fler meddelanden<br /><br /> 204: inget innehåll – inga fler meddelanden|  
|statusDescription|sträng|No|Beskrivning av status.|  
  
Svars meddelande texten måste bestå av ett **AMQP-Value-** avsnitt som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|meddelanden|lista över kartor|Ja|Lista över meddelanden där varje karta representerar ett meddelande.|  
  
Kartan som representerar ett meddelande måste innehålla följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|meddelande|matris med byte|Ja|AMQP 1,0-Wired-kodat meddelande.|  
  
### <a name="schedule-message"></a>Schema meddelande  

Schemalägger meddelanden. Den här åtgärden stöder transaktion.
  
#### <a name="request"></a>Förfrågan  

Begär ande meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|operation|sträng|Ja|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Inga|Åtgärds serverns tids gräns i millisekunder.|  
  
Meddelande texten för begäran måste bestå av ett **AMQP-Value-** avsnitt som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|meddelanden|lista över kartor|Ja|Lista över meddelanden där varje karta representerar ett meddelande.|  
  
Kartan som representerar ett meddelande måste innehålla följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|meddelande-ID|sträng|Ja|`amqpMessage.Properties.MessageId` som sträng|  
|sessions-ID|sträng|No|`amqpMessage.Properties.GroupId as string`|  
|partition – nyckel|sträng|No|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|via-partition-nyckel|sträng|No|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|meddelande|matris med byte|Ja|AMQP 1,0-Wired-kodat meddelande.|  
  
#### <a name="response"></a>Svarsåtgärder  

Svars meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades.|  
|statusDescription|sträng|No|Beskrivning av status.|  
  
Svars meddelande texten måste bestå av ett **AMQP-Value-** avsnitt som innehåller en karta med följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|sekvenser – siffror|matris med lång|Ja|Ordnings nummer för schemalagda meddelanden. Sekvensnumret används för att avbryta.|  
  
### <a name="cancel-scheduled-message"></a>Avbryt schemalagt meddelande  

Avbryter schemalagda meddelanden.  
  
#### <a name="request"></a>Förfrågan  

Begär ande meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|operation|sträng|Ja|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Inga|Åtgärds serverns tids gräns i millisekunder.|  
  
Meddelande texten för begäran måste bestå av ett **AMQP-Value-** avsnitt som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|sekvenser – siffror|matris med lång|Ja|Ordnings nummer för schemalagda meddelanden som ska avbrytas.|  
  
#### <a name="response"></a>Svarsåtgärder  

Svars meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades.|  
|statusDescription|sträng|No|Beskrivning av status.|   
  
## <a name="session-operations"></a>Session åtgärder  
  
### <a name="session-renew-lock"></a>Session förnyar lås  

Utökar meddelande låset vid den tid som anges i beskrivningen av entiteten.  
  
#### <a name="request"></a>Förfrågan  

Begär ande meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|operation|sträng|Ja|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Inga|Åtgärds serverns tids gräns i millisekunder.|  
  
Meddelande texten för begäran måste bestå av ett **AMQP-Value-** avsnitt som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|sessions-ID|sträng|Ja|Sessions-ID.|  
  
#### <a name="response"></a>Svarsåtgärder  

Svars meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – innehåller fler meddelanden<br /><br /> 204: inget innehåll – inga fler meddelanden|  
|statusDescription|sträng|No|Beskrivning av status.|  
  
Svars meddelande texten måste bestå av ett **AMQP-Value-** avsnitt som innehåller en karta med följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|dag|timestamp|Ja|Ny förfallo tid.|  
  
### <a name="peek-session-message"></a>Granska sessions meddelande  

Granskar sessions meddelanden utan att låsa.  
  
#### <a name="request"></a>Förfrågan  

Begär ande meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|operation|sträng|Ja|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Inga|Åtgärds serverns tids gräns i millisekunder.|  
  
Meddelande texten för begäran måste bestå av ett **AMQP-Value-** avsnitt som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|från-sekvens-nummer|long|Ja|Serie nummer från vilket du vill starta Peek.|  
|antal meddelanden|int|Ja|Maximalt antal meddelanden att granska.|  
|sessions-ID|sträng|Ja|Sessions-ID.|  
  
#### <a name="response"></a>Svarsåtgärder  

Svars meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – innehåller fler meddelanden<br /><br /> 204: inget innehåll – inga fler meddelanden|  
|statusDescription|sträng|No|Beskrivning av status.|  
  
Svars meddelande texten måste bestå av ett **AMQP-Value-** avsnitt som innehåller en karta med följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|meddelanden|lista över kartor|Ja|Lista över meddelanden där varje karta representerar ett meddelande.|  
  
 Kartan som representerar ett meddelande måste innehålla följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|meddelande|matris med byte|Ja|AMQP 1,0-Wired-kodat meddelande.|  
  
### <a name="set-session-state"></a>Ange sessionstillstånd  

Anger status för en session.  
  
#### <a name="request"></a>Förfrågan  

Begär ande meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|operation|sträng|Ja|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|Inga|Åtgärds serverns tids gräns i millisekunder.|  
  
Meddelande texten för begäran måste bestå av ett **AMQP-Value-** avsnitt som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|sessions-ID|sträng|Ja|Sessions-ID.|  
|sessionstillstånd|byte mat ris|Ja|Täckande binära data.|  
  
#### <a name="response"></a>Svarsåtgärder  

Svars meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades|  
|statusDescription|sträng|No|Beskrivning av status.|  
  
### <a name="get-session-state"></a>Hämta sessionstillstånd  

Hämtar status för en session.  
  
#### <a name="request"></a>Förfrågan  

Begär ande meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|operation|sträng|Ja|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Inga|Åtgärds serverns tids gräns i millisekunder.|  
  
Meddelande texten för begäran måste bestå av ett **AMQP-Value-** avsnitt som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|sessions-ID|sträng|Ja|Sessions-ID.|  
  
#### <a name="response"></a>Svarsåtgärder  

Svars meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades|  
|statusDescription|sträng|No|Beskrivning av status.|  
  
Svars meddelande texten måste bestå av ett **AMQP-Value-** avsnitt som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|sessionstillstånd|byte mat ris|Ja|Täckande binära data.|  
  
### <a name="enumerate-sessions"></a>Räkna upp sessioner  

Räknar upp sessioner i en meddelande enhet.  
  
#### <a name="request"></a>Förfrågan  

Begär ande meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|operation|sträng|Ja|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Inga|Åtgärds serverns tids gräns i millisekunder.|  
  
Meddelande texten för begäran måste bestå av ett **AMQP-Value-** avsnitt som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|Senast uppdaterad – tid|timestamp|Ja|Filtrera om du bara vill inkludera sessioner som uppdateras efter en bestämd tid.|  
|hoppa över|int|Ja|Hoppa över ett antal sessioner.|  
|top|int|Ja|Maximalt antal sessioner.|  
  
#### <a name="response"></a>Svarsåtgärder  

Svars meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – innehåller fler meddelanden<br /><br /> 204: inget innehåll – inga fler meddelanden|  
|statusDescription|sträng|No|Beskrivning av status.|  
  
Svars meddelande texten måste bestå av ett **AMQP-Value-** avsnitt som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|hoppa över|int|Ja|Antal överhoppade sessioner om status koden är 200.|  
|sessioner-ID: n|matris med strängar|Ja|Matris med sessions-ID: n om status koden är 200.|  
  
## <a name="rule-operations"></a>Regel åtgärder  
  
### <a name="add-rule"></a>Lägg till regel  
  
#### <a name="request"></a>Förfrågan  

Begär ande meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|operation|sträng|Ja|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Inga|Åtgärds serverns tids gräns i millisekunder.|  
  
Meddelande texten för begäran måste bestå av ett **AMQP-Value-** avsnitt som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|regel namn|sträng|Ja|Regel namn, inklusive namn på prenumeration och ämnen.|  
|regel – Beskrivning|map|Ja|Regel Beskrivning enligt vad som anges i nästa avsnitt.|  
  
Mappningen **regel-Beskrivning** måste innehålla följande poster, där **SQL-filter** och **korrelations filter** kan vara ömsesidigt uteslutande:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|SQL-filter|map|Ja|`sql-filter`, enligt vad som anges i nästa avsnitt.|  
|korrelations filter|map|Ja|`correlation-filter`, enligt vad som anges i nästa avsnitt.|  
|SQL-regel – åtgärd|map|Ja|`sql-rule-action`, enligt vad som anges i nästa avsnitt.|  
  
SQL-filtrets mappning måste innehålla följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|uttryck|sträng|Ja|SQL filter-uttryck.|  
  
**Korrelations filter** kartan måste innehålla minst en av följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|korrelations-id|sträng|No||  
|meddelande-ID|sträng|No||  
|på|sträng|No||  
|svar till|sträng|No||  
|etikett|sträng|No||  
|sessions-ID|sträng|No||  
|svar-till-session-ID|sträng|No||  
|innehålls typ|sträng|No||  
|properties|map|Inga|Mappar till Service Bus [BrokeredMessage. Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).|  
  
**SQL-regel – åtgärds** kartan måste innehålla följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|uttryck|sträng|Ja|SQL-åtgärds uttryck.|  
  
#### <a name="response"></a>Svarsåtgärder  

Svars meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades|  
|statusDescription|sträng|No|Beskrivning av status.|  
  
### <a name="remove-rule"></a>Ta bort regel  
  
#### <a name="request"></a>Förfrågan  

Begär ande meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|operation|sträng|Ja|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Inga|Åtgärds serverns tids gräns i millisekunder.|  
  
Meddelande texten för begäran måste bestå av ett **AMQP-Value-** avsnitt som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|regel namn|sträng|Ja|Regel namn, inklusive namn på prenumeration och ämnen.|  
  
#### <a name="response"></a>Svarsåtgärder  

Svars meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades|  
|statusDescription|sträng|No|Beskrivning av status.|  
  
### <a name="get-rules"></a>Hämta regler

#### <a name="request"></a>Förfrågan

Begär ande meddelandet måste innehålla följande program egenskaper:

|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|operation|sträng|Ja|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|Inga|Åtgärds serverns tids gräns i millisekunder.|  

Meddelande texten för begäran måste bestå av ett **AMQP-Value-** avsnitt som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|top|int|Ja|Antalet regler som ska hämtas på sidan.|  
|hoppa över|int|Ja|Antalet regler som ska hoppas över. Definierar start indexet (+ 1) i listan över regler. | 

#### <a name="response"></a>Svarsåtgärder

Svars meddelandet innehåller följande egenskaper:

|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades|  
|regler| kartans matris|Ja|Regel mat ris. Varje regel representeras av en karta.|

Varje mappnings post i matrisen innehåller följande egenskaper:

|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|regel – Beskrivning|matris med beskrivna objekt|Ja|`com.microsoft:rule-description:list` med AMQP-kod som beskrivs 0x0000013700000004| 

`com.microsoft.rule-description:list` är en matris med beskrivna objekt. Matrisen innehåller följande:

|Index|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
| 0 | matris med beskrivna objekt | Ja | `filter` som anges nedan. |
| 1 | matris med beskrivande objekt | Ja | `ruleAction` som anges nedan. |
| 2 | sträng | Ja | regelns namn. |

`filter` kan vara av någon av följande typer:

| Beskrivnings namn | Beskrivnings kod | Värde |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | SQL-filter |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Korrelations filter |
| `com.microsoft:true-filter:list` | 0x000001370000007 | Sant filter som representerar 1 = 1 |
| `com.microsoft:false-filter:list` | 0x000001370000008 | Falskt filter som representerar 1 = 0 |

`com.microsoft:sql-filter:list` är en förtecknad matris som innehåller:

|Index|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
| 0 | sträng | Ja | SQL filter-uttryck |

`com.microsoft:correlation-filter:list` är en förtecknad matris som innehåller:

|Index (om det finns)|Värdetyp|Värde innehåll|  
|---------|----------------|--------------|
| 0 | sträng | Korrelations-ID |
| 1 | sträng | Meddelande-ID |
| 2 | sträng | Om du vill |
| 3 | sträng | Svara på |
| 4 | sträng | Etikett |
| 5 | sträng | Sessions-ID |
| 6 | sträng | Svara på sessions-ID|
| 7 | sträng | Innehållstyp |
| 8 | Karta | Karta över definierade egenskaper för program |

`ruleAction` kan vara någon av följande typer:

| Beskrivnings namn | Beskrivnings kod | Värde |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | Tom regel åtgärd-ingen regel åtgärd finns |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | Åtgärd för SQL-regel |

`com.microsoft:sql-rule-action:list` är en matris med beskrivna objekt vars första post är en sträng som innehåller uttrycket för SQL-Regelns åtgärd.

## <a name="deferred-message-operations"></a>Uppskjutna meddelande åtgärder  
  
### <a name="receive-by-sequence-number"></a>Ta emot efter sekvensnummer  

Tar emot uppskjutna meddelanden efter ordnings nummer.  
  
#### <a name="request"></a>Förfrågan  

Begär ande meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|operation|sträng|Ja|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Inga|Åtgärds serverns tids gräns i millisekunder.|  
  
Meddelande texten för begäran måste bestå av ett **AMQP-Value-** avsnitt som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|sekvenser – siffror|matris med lång|Ja|Serie nummer.|  
|mottagare-likvidera-läge|ubyte|Ja|**Mottagar** läge för mottagaren som anges i AMQP Core v 1.0.|  
  
#### <a name="response"></a>Svarsåtgärder  

Svars meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades|  
|statusDescription|sträng|No|Beskrivning av status.|  
  
Svars meddelande texten måste bestå av ett **AMQP-Value-** avsnitt som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|meddelanden|lista över kartor|Ja|Lista över meddelanden där varje karta representerar ett meddelande.|  
  
Kartan som representerar ett meddelande måste innehålla följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|Lås-token|uuid|Ja|Lås token om `receiver-settle-mode` är 1.|  
|meddelande|matris med byte|Ja|AMQP 1,0-Wired-kodat meddelande.|  
  
### <a name="update-disposition-status"></a>Uppdatera dispositions status  

Uppdaterar dispositions statusen för uppskjutna meddelanden. Den här åtgärden stöder transaktioner.
  
#### <a name="request"></a>Förfrågan  

Begär ande meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|operation|sträng|Ja|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Inga|Åtgärds serverns tids gräns i millisekunder.|  
  
Meddelande texten för begäran måste bestå av ett **AMQP-Value-** avsnitt som innehåller en **karta** med följande poster:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|disposition-status|sträng|Ja|ATS<br /><br /> Avbryt<br /><br /> avstängd|  
|Lås-token|matris med uuid|Ja|Meddelande lås tokens för att uppdatera dispositions status.|  
|obeställbara meddelanden kön – orsak|sträng|No|Kan anges om dispositions status är **inaktive** rad.|  
|obeställbara meddelanden kön – Beskrivning|sträng|No|Kan anges om dispositions status är **inaktive** rad.|  
|egenskaper för att ändra|map|Inga|Lista över Service Bus sammanslagna meddelande egenskaper som ska ändras.|  
  
#### <a name="response"></a>Svarsåtgärder  

Svars meddelandet måste innehålla följande program egenskaper:  
  
|Nyckel|Värdetyp|Obligatorisk|Värde innehåll|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-svarskod [RFC2616]<br /><br /> 200: OK – lyckades, annars misslyckades|  
|statusDescription|sträng|No|Beskrivning av status.|

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om AMQP och Service Bus kan du gå till följande länkar:

* [Översikt över Service Bus AMQP]
* [AMQP 1.0-protokollguide]
* [AMQP i Service Bus för Windows Server]

[Översikt över Service Bus AMQP]: service-bus-amqp-overview.md
[AMQP 1.0-protokollguide]: service-bus-amqp-protocol-guide.md
[AMQP i Service Bus för Windows Server]: /previous-versions/service-bus-archive/dn282144(v=azure.100)