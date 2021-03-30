---
title: Skicka APN VOIP-meddelanden med Azure Notification Hubs
description: Lär dig hur du skickar APN VOIP-meddelanden via Azure Notification Hubs (stöds inte av officiellt).
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "80146893"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>Använd APN VOIP via Notification Hubs (stöds inte officiellt)

Du kan använda APN VOIP-meddelanden via Azure Notification Hubs; Det finns dock inget statligt stöd för det här scenariot.

## <a name="considerations"></a>Överväganden

Om du fortfarande väljer att skicka APN VOIP-meddelanden via Notification Hubs bör du vara medveten om följande begränsningar:

- Att skicka ett VOIP-meddelande kräver att `apns-topic` sidhuvudet anges till programpaket-ID + `.voip` suffixet. Exempel: för en exempel-app med paket-ID `com.microsoft.nhubsample` : t måste `apns-topic` rubriken anges till `com.microsoft.nhubsample.voip.`

   Den här metoden fungerar inte bra med Azure Notification Hubs eftersom appens paket-ID måste konfigureras som en del av hubbens APN-autentiseringsuppgifter och värdet kan inte ändras. Notification Hubs tillåter inte heller att värdet för `apns-topic` huvudet åsidosätts vid körning.

   Om du vill skicka VOIP-meddelanden måste du konfigurera en separat meddelande hubb med `.voip` ID för programpaketet.

- Om du skickar ett VOIP-meddelande måste `apns-push-type` rubriken anges till värdet `voip` .

   För att hjälpa kunder med över gången till iOS 13 Notification Hubs försöker att härleda rätt värde för `apns-push-type` huvudet. Den normala logiken är avsiktligt enkel, vilket innebär att du slipper bryta standard meddelanden. Den här metoden orsakar tyvärr problem med VOIP-meddelanden, eftersom Apple behandlar VOIP-meddelanden som ett specialfall som inte följer samma regler som standard meddelanden.

   Om du vill skicka VOIP-meddelanden måste du ange ett explicit värde för `apns-push-type` rubriken.

- Notification Hubs begränsar APN-nyttolaster till 4 KB, som dokumenteras av Apple. För VOIP-meddelanden tillåter Apple nytto laster upp till 5 KB. Notification Hubs skiljer inte mellan vanliga och VOIP-meddelanden. Därför är alla meddelanden begränsade till 4 KB.

   Om du vill skicka VOIP-meddelanden får du inte överskrida storleks gränsen på 4 KB.

## <a name="next-steps"></a>Nästa steg

Mer information finns här på följande länkar:

- [Dokumentation för `apns-topic` och `apns-push-type` rubriker och värden, inklusive särskilda fall för VoIP-meddelanden](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns).

- [Dokumentation för begränsning av nytto Last storlek](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification).

- [Notification Hubs uppdateringar för iOS 13](push-notification-updates-ios-13.md#apns-push-type).
