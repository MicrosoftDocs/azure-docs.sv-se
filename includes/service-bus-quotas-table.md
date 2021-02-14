---
title: ta med fil
description: ta med fil
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 07/15/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 46e5400627e4d2896265ed95410c8afcb918043b
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106283"
---
I följande tabell visas kvot information som är speciell för Azure Service Bus meddelande tjänster. För information om priser och andra kvoter för Service Bus, se [Service Bus prissättning](https://azure.microsoft.com/pricing/details/service-bus/).

| Kvot namn | Omfång | Kommentarer | Värde |
| --- | --- | --- | --- |
| Maximalt antal Basic-eller standard-namnområden per Azure-prenumeration |Namnområde |Efterföljande förfrågningar om fler Basic-eller standard-namnområden avvisas av Azure Portal. |100|
| Maximalt antal Premium-namnområden per Azure-prenumeration |Namnområde |Efterföljande begär Anden om ytterligare Premium-namnområden avvisas av portalen. |100 |
| Köns-eller ämnes storlek |Entitet |Definieras när kön eller ämnet skapas. <br/><br/> Efterföljande inkommande meddelanden avvisas och ett undantag tas emot av den anropande koden. |1, 2, 3, 4 GB eller 5 GB.<br /><br />I Premium-SKU: n och standard-SKU: n med [partitionering](../articles/service-bus-messaging/service-bus-partitioning.md) aktive rad är den maximala kön eller ämnes storleken 80 GB. |
| Antalet samtidiga anslutningar i ett namn område |Namnområde |Efterföljande begär Anden om ytterligare anslutningar avvisas och ett undantag tas emot av den anropande koden. REST-åtgärder räknas inte mot samtidiga TCP-anslutningar. |NET Messaging: 1 000.<br /><br />AMQP: 5 000. |
| Antal samtidiga mottagnings begär Anden för en kö, ett ämne eller en prenumerations enhet |Entitet |Efterföljande mottagnings begär Anden avvisas och ett undantag tas emot av den anropande koden. Den här kvoten gäller för det kombinerade antalet samtidiga mottagnings åtgärder för alla prenumerationer på ett ämne. |5 000 |
| Antal ämnen eller köer per namnrymd |Namnområde |Efterföljande begär Anden om att skapa ett nytt ämne eller en kö i namn området avvisas. Därför genereras ett fel meddelande om det konfigureras via [Azure Portal][Azure portal]. Om det anropas från hanterings-API: et tas ett undantag emot av den anropande koden. |10 000 för nivån Basic eller standard. Det totala antalet ämnen och köer i ett namn område måste vara mindre än eller lika med 10 000. <br/><br/>För Premium-nivån 1 000 per meddelande enhet (MU). |
| Antal [partitionerade ämnen eller köer](../articles/service-bus-messaging/service-bus-partitioning.md) per namnrymd |Namnområde |Efterföljande begär Anden om att skapa ett nytt, partitionerat ämne eller kö i namn området avvisas. Därför genereras ett fel meddelande om det konfigureras via [Azure Portal][Azure portal]. Om det anropas från hanterings-API: t tas undantags **QuotaExceededException** emot av den anropande koden. |Nivåerna Basic och standard: 100.<br/><br/>Partitionerade entiteter stöds inte på [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) -nivån.<br/><br />Varje partitionerad kö eller ämne räknas mot kvoten på 1 000 entiteter per namnrymd. |
| Maximal storlek för valfri sökväg för meddelande enhet: kö eller ämne |Entitet |- |260 tecken. |
| Maximal storlek för alla meddelande enhets namn: namn område, prenumeration eller prenumerations regel |Entitet |- |50 tecken. |
| Maximal storlek för ett [meddelande-ID](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Entitet |- | 128 |
| Maximal storlek för ett meddelande [-sessions-ID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | Entitet |- | 128 |
| Meddelande storlek för en kö, ett ämne eller en prenumerations enhet |Entitet |Inkommande meddelanden som överskrider dessa kvoter avvisas och ett undantag tas emot av den anropande koden. |Maximal meddelande storlek: 256 KB för [Standard nivån](../articles/service-bus-messaging/service-bus-premium-messaging.md), 1 MB för [Premium-nivån](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />På grund av systemets omkostnader är den här gränsen lägre än dessa värden.<br /><br />Maximal sidhuvud storlek: 64 KB.<br /><br />Maximalt antal huvud egenskaper i egenskaps uppsättningen: **byte/int. MaxValue**.<br /><br />Maximal egenskaps storlek i egenskaps uppsättningen: ingen explicit gräns. Begränsas av maximal sidhuvud storlek. |
| Meddelande egenskap storlek för en kö, ett ämne eller en prenumerations enhet |Entitet | Undantaget `SerializationException` genereras. |Maximal egenskaps storlek för meddelandet för varje egenskap är 32 000. Den ackumulerade storleken för alla egenskaper får inte överstiga 64 000. Den här gränsen gäller för hela huvudet för det asynkrona [meddelandet](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), som har både användar egenskaper och system egenskaper, till exempel [sekvensnummer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [etikett](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)och [meddelande-ID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid). |
| Antal prenumerationer per ämne |Entitet |Efterföljande begär Anden om att skapa ytterligare prenumerationer för avsnittet avvisas. Som ett resultat visas ett fel meddelande om det konfigureras via portalen. Om det anropas från hanterings-API: et tas ett undantag emot av den anropande koden. |2 000 per ämne för nivån standard och Premium. |
| Antal SQL-filter per ämne |Entitet |Efterföljande begär Anden om att skapa ytterligare filter i avsnittet avvisas och ett undantag tas emot av den anropande koden. |2 000 |
| Antal korrelations filter per ämne |Entitet |Efterföljande begär Anden om att skapa ytterligare filter i avsnittet avvisas och ett undantag tas emot av den anropande koden. |100 000 |
| Storlek på SQL-filter eller åtgärder |Namnområde |Efterföljande begär Anden om att skapa ytterligare filter avvisas och ett undantag tas emot av den anropande koden. |Max längden för filter villkors sträng: 1 024 (1 K).<br /><br />Maximal längd för regel åtgärds sträng: 1 024 (1 K).<br /><br />Maximalt antal uttryck per regel åtgärd: 32. |
| Antal [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) -regler per namnrymd, kö eller ämne |Entitet, namnrymd |Efterföljande begär Anden om att skapa ytterligare regler avvisas och ett undantag tas emot av den anropande koden. |Maximalt antal regler per entitetstyp: 12. <br /><br /> Regler som är konfigurerade i ett Service Bus namn område gäller för alla typer: köer, ämnen. |
| Antal meddelanden per transaktion | Transaktion | Ytterligare inkommande meddelanden avvisas och ett undantag som anger att "det går inte att skicka fler än 100 meddelanden i en enskild transaktion" tas emot av den anropande koden. | 100 <br /><br /> För åtgärderna **send ()** och **SendAsync ()** . |
| Antal regler för virtuella nätverk och IP-filter | Namnområde | &nbsp; | 128 | 

[Azure portal]: https://portal.azure.com
