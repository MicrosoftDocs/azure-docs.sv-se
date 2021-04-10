---
title: Azure Event Hubs – .NET-undantag
description: Den här artikeln innehåller en lista över undantag och föreslagna åtgärder för Azure Event Hubs .NET-meddelanden.
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 09/23/2020
ms.author: spelluru
ms.openlocfilehash: af2d820fd255d041ade21a00d7d7a9ac6b12fcba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98745910"
---
# <a name="eventhubsexception---net"></a>EventHubsException – .NET
En **EventHubsException** utlöses när en åtgärd som är speciell för Event Hubs har orsakat ett problem, inklusive både fel i tjänsten och som är beroende av klienten. 

## <a name="exception-information"></a>Undantags information
Undantaget innehåller följande sammanhangsbaserade information för att hjälpa till med att förstå kontexten för felet och dess relativa allvarlighets grad. 

- **IsTransient**: identifierar om undantaget anses vara återställnings Bart eller inte. I de fall där det ansågs tillfälligt har rätt princip för återförsök redan tillämpats och nya försök misslyckades.
- **Orsak**: innehåller en uppsättning välkända orsaker till det fel som hjälper till att kategorisera och klargöra rotor saken. Dessa orsaker är avsedda att användas för att tillämpa undantags filtrering och annan logik när du inspekterar texten i ett undantags meddelande skulle inte vara idealiskt. Några viktiga orsaker är:
    - **Klienten stängde**: inträffar när en Event Hub-klient redan har stängts eller tagits bort. Vi rekommenderar att du kontrollerar program koden för att se till att objekt från Event Hubs klient biblioteket skapas och stängs i det avsedda omfånget.
    - **Tids gräns för tjänst**: anger att Event Hubs tjänsten inte svarade på en åtgärd inom den förväntade tiden. Det här problemet kan ha orsakats av ett tillfälligt problem med nätverket eller tjänsten. Tjänsten Event Hubs kanske inte har slutfört begäran. statusen är inte känd. Vi rekommenderar att du försöker verifiera det aktuella läget och försöker igen om det behövs.
    - **Kvoten överskreds**: anger att det finns för många aktiva Läs åtgärder för en enskild konsument grupp. Den här gränsen beror på nivån på Event Hubs namn området och det kan krävas att flytta till en högre nivå. Ett alternativ är att skapa ytterligare konsument grupper och se till att antalet konsument klient läsningar för en grupp ligger inom gränsen. Mer information finns i [Azure Event Hubs kvoter och begränsningar](event-hubs-quotas.md).
    - **Meddelandets storlek överskreds**: händelse data som en maximal storlek tillåts både för en enskild händelse och en grupp händelser. Den innehåller data för händelsen och eventuella associerade metadata och system kostnader. För att lösa det här felet minskar du antalet händelser som skickas i en grupp eller minskar storleken på data som ingår i meddelandet. Eftersom storleks gränserna kan ändras kan du läsa mer i [Azure Event Hubs kvoter och begränsningar](event-hubs-quotas.md) för mer information.
    - **Konsumenten** är frånkopplad: en konsument klient kopplades från tjänsten Event Hub från Event Hub-instansen. Det inträffar vanligt vis när en konsument med en högre ägar nivå försäkrar ägarskap över en partition och konsument grupp par.
    - Det gick **inte att hitta resursen**: Event Hubs tjänsten kunde inte hitta någon resurs, till exempel en händelsehubben, konsument grupp eller partition. Den kan ha tagits bort eller också är det problem med själva Event Hubss tjänsten.

## <a name="handling-exceptions"></a>Hantering av undantag
Du kan reagera på en speciell felorsak för **EventHubException**  på flera sätt. Ett sätt är att tillämpa en exception filter-sats som en del av catch-blocket.

```csharp
try
{
    // Read events using the consumer client
}
catch (EventHubsException ex) where 
    (ex.Reason == EventHubsException.FailureReason.ConsumerDisconnected)
{
    // Take action based on a consumer being disconnected
}
```

## <a name="next-steps"></a>Nästa steg
Det finns andra undantag som dokumenteras i den [äldre artikeln](event-hubs-messaging-exceptions.md). En del av dem gäller endast för den äldre Event Hubs .NET-klient biblioteket.