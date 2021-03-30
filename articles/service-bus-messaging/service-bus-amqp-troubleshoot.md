---
title: Felsöka AMQP-fel i Azure Service Bus | Microsoft Docs
description: Innehåller en lista med AMQP-fel som du kan få när du använder Azure Service Bus och orsaken till dessa fel.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 88b10940e0b910f50e6ccf7f8c53134fa7f0ba2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88064357"
---
# <a name="amqp-errors-in-azure-service-bus"></a>AMQP fel i Azure Service Bus
Den här artikeln innehåller några av de fel som du får när du använder AMQP med Azure Service Bus. De är alla standard beteenden för tjänsten. Du kan undvika dem genom att skicka/ta emot samtal via anslutningen/länken, som automatiskt återskapar anslutningen/länken.

## <a name="link-is-closed"></a>Länken är stängd 
Följande fel visas när AMQP-anslutningen och länken är aktiva men inga anrop (till exempel skicka eller ta emot) görs med länken i 10 minuter. Därför stängs länken. Anslutningen är fortfarande öppen.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>Anslutningen är stängd
Följande fel visas på AMQP-anslutningen när alla länkar i anslutningen har stängts eftersom det inte fanns någon aktivitet (inaktiv) och en ny länk inte har skapats på 5 minuter.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>Ingen länk har skapats 
Du ser det här felet när en ny AMQP-anslutning skapas, men en länk inte skapas inom 1 minut av skapandet av AMQP-anslutningen.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om AMQP och Service Bus kan du gå till följande länkar:

* [Översikt över Service Bus AMQP]
* [AMQP 1.0-protokollguide]
* [AMQP i Service Bus för Windows Server]

[Översikt över Service Bus AMQP]: service-bus-amqp-overview.md
[AMQP 1.0-protokollguide]: service-bus-amqp-protocol-guide.md
[AMQP i Service Bus för Windows Server]: /previous-versions/service-bus-archive/dn282144(v=azure.100)