---
title: Routnings-och tagg uttryck i Azure Notification Hubs
description: Lär dig att dirigera och tagga uttryck för Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 4ff8225522e79e2be40682fb5e4823777dde2aa0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88998311"
---
# <a name="routing-and-tag-expressions"></a>Uttryck för Routning och tagg

## <a name="overview"></a>Översikt

Med tagg uttryck kan du rikta in specifika uppsättningar av enheter eller mer specifikt registreringar när du skickar ett push-meddelande via Notification Hubs.

## <a name="targeting-specific-registrations"></a>Rikta in sig på specifika registreringar

Det enda sättet att rikta in sig på specifika meddelande registreringar är att associera Taggar med dem och sedan rikta dessa taggar. Som beskrivs i [registrerings hantering](notification-hubs-push-notification-registration-management.md), för att ta emot push-meddelanden, måste en app registrera en enhets referens i en Notification Hub. När appen skapar en registrering på en Notification Hub kan program Server delen skicka push-meddelanden till den. Programmets Server del kan välja vilka registreringar som ska riktas mot ett särskilt meddelande på följande sätt:

1. **Sändning**: alla registreringar i Notification Hub får meddelandet.
2. **Tagg**: alla registreringar som innehåller den angivna taggen får meddelandet.
3. **Tag-uttryck**: alla registreringar vars uppsättning taggar matchar det angivna uttrycket får meddelandet.

## <a name="tags"></a>Taggar

En tagg kan vara valfri sträng, upp till 120 tecken, med alfanumeriska tecken och följande icke-alfanumeriska tecken: ' ', ' ', ', ' ' `_` `@` , ' `#` `.` `:` `-` '. I följande exempel visas ett program som du kan ta emot popup-meddelanden från om specifika musik grupper. I det här scenariot är ett enkelt sätt att skicka meddelanden till etikett registreringar med taggar som representerar de olika banden, som i följande figur:

![Översikt över Taggar](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

I bilden når meddelandet som taggats med **Beatles** bara den surfplatta som har registrerats med taggen **Beatles**.

Mer information om hur du skapar registreringar för taggar finns i [registrerings hantering](notification-hubs-push-notification-registration-management.md).

Du kan skicka meddelanden till taggar med hjälp av metoden skicka aviseringar för `Microsoft.Azure.NotificationHubs.NotificationHubClient` klassen i [Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK. Du kan också använda Node.js eller REST-API: er för push-meddelanden.  Här är ett exempel på hur du använder SDK: n.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You requested a Beatles notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You requested a Wailers notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");
```

Taggar får inte vara företablerade och kan referera till flera app-/regionsspecifika begrepp. Till exempel kan användare av det här exempel programmet kommentera band och vill ta emot popup-fönster, inte bara för kommentarer på deras favorit band, utan även för alla kommentarer från sina vänner, oavsett vilket band de kommenterar. Följande bild visar ett exempel på det här scenariot:

![Taggar för vänner](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

I det här exemplet är Alice intresse rad av uppdateringar för Beatles och Bob är intresse rad av uppdateringar för Wailers. Bob är också intresse rad av Kalles kommentarer, och Kalle är intresse rad av Wailers. När ett meddelande skickas för Kalle-kommentaren på Beatles skickar Notification Hubs den till både Alice och Bob.

Även om du kan koda flera frågor i taggar (till exempel `band_Beatles` eller `follows_Charlie` ), är Taggar enkla strängar och inte egenskaper med värden. En registrering matchar endast förekomsten eller frånvaron av en speciell tagg.

En fullständig steg-för-steg-guide om hur du använder taggar för att skicka till intresse grupper finns i avsnittet om att [dela nyheter](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> Azure Notification Hubs stöder högst 60 Taggar per registrering.

## <a name="using-tags-to-target-users"></a>Använda taggar för mål användare

Ett annat sätt att använda taggar är att identifiera alla enheter som är associerade med en viss användare. Du kan tagga en registrering med en tagg som innehåller användar-ID: t, som i följande figur:

![Tagga användare](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

I bilden når meddelandet taggade `user_Alice` alla enheter som taggats med `user_Alice` .

## <a name="tag-expressions"></a>Tagg uttryck

Det finns fall där meddelanden måste riktas mot en uppsättning registreringar som identifieras inte av en enskild tagg, utan av ett booleskt uttryck med hjälp av taggar.

Överväg ett idrotts program som skickar en påminnelse till alla i Boston om ett spel mellan den röda Sox och kardinalerna. Om klient programmet registrerar taggar för intresse i team och plats, ska meddelandet vara riktat till alla i Boston som är intresserade av antingen den röda Sox eller kardinalerna. Det här tillståndet kan uttryckas med följande booleska uttryck:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![Tagg uttryck](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

Etikett uttryck stöder vanliga booleska operatorer som `AND` ( `&&` ), `OR` ( `||` ) och `NOT` ( `!` ). de kan också innehålla parenteser. Tagg uttryck med endast `OR` operatörer kan referera till 20 Taggar; uttryck med `AND` operatorer, men inga `OR` operatorer kan referera till 10 taggar. annars är tagg uttryck begränsade till 6 taggar.

Här är ett exempel på hur du skickar meddelanden med tagg uttryck med hjälp av SDK:

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

String userTag = "(location_Boston && !follows_Cardinals)";

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
```
