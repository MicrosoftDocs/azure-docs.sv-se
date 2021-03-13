---
title: Felsöka AMQP-fel i Azure Event Hubs | Microsoft Docs
description: Innehåller en lista med AMQP-fel som du kan få när du använder Azure Event Hubs och orsaken till dessa fel.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 409552bb6176f2023901b4518646fbfcb2d51adf
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103235058"
---
# <a name="amqp-errors-in-azure-event-hubs"></a>AMQP-fel i Azure Event Hubs
Den här artikeln innehåller några av de fel som du får när du använder AMQP med Azure Event Hubs. De är alla standard beteenden för tjänsten. Du kan undvika dem genom att skicka/ta emot samtal via anslutningen/länken, som automatiskt återskapar anslutningen/länken.

## <a name="link-is-closed"></a>Länken är stängd 
Följande fel visas när AMQP-anslutningen och länken är aktiva men inga anrop (till exempel skicka eller ta emot) görs med länken i 10 minuter. Därför stängs länken. Anslutningen är fortfarande öppen.

"AMQP: Link: Detached-FORCEDOS: länken" G2:7223832: user.tenant0.cud_00000000000-0000-0000-0000-00000000000000 "tvingas att frånkopplas av koordinatorn på grund av att det uppstod fel i utgivaren (link164614). Frånkopplat ursprung: AmqpMessagePublisher. IdleTimerExpired: tids gräns för inaktivitet: 00:10:00. TrackingId: 00000000000000000000000000000000000000_G2_B3, SystemTracker:: ämne: topic, tidsstämpel: 2/16/2018 11:10:40 PM "

## <a name="connection-is-closed"></a>Anslutningen är stängd
Följande fel visas på AMQP-anslutningen när alla länkar i anslutningen har stängts eftersom det inte fanns någon aktivitet (inaktiv) och en ny länk inte har skapats på 5 minuter.

"Fel {condition = AMQP: anslutning: tvång, beskrivning =" anslutningen var inaktiv under mer än den tillåtna 300000 millisekunder och är stängd av containern "LinkTracker". TrackingId: 00000000000000000000000000000000000_G21, SystemTracker: gateway5, timestamp: 2019-03-06T17:32:00 ', info = null} "

## <a name="link-isnt-created"></a>Länken har inte skapats 
Du ser det här felet när en ny AMQP-anslutning skapas, men en länk inte skapas inom 1 minut från skapandet av AMQP-anslutningen.

"Fel {condition = AMQP: anslutning: tvång, beskrivning =" anslutningen var inaktiv under mer än den tillåtna 60000 millisekunder och är stängd av containern "LinkTracker". TrackingId: 0000000000000000000000000000000000000_G21, SystemTracker: gateway5, timestamp: 2019-03-06T18:41:51, info = null} "

## <a name="next-steps"></a>Nästa steg
Mer information om AMQP finns i [AMQP 1,0 Protocol guide](../service-bus-messaging/service-bus-amqp-protocol-guide.md).
