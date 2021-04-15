---
title: Händelsedomäner i Azure Event Grid
description: Den här artikeln beskriver hur du använder händelsedomäner för att hantera flödet av anpassade händelser till olika företagsorganisationer, kunder eller program.
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: 32c06ac55f667ec9807c7952127c2cf0f0384024
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374717"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Förstå händelsedomäner för att hantera Event Grid avsnitt

Den här artikeln beskriver hur du använder händelsedomäner för att hantera flödet av anpassade händelser till olika företagsorganisationer, kunder eller program. Använd händelsedomäner för att:

* Hantera händelsearkitekturer för flera mål i stor skala.
* Hantera din auktorisering och autentisering.
* Partitionera dina ämnen utan att hantera dem individuellt.
* Undvik att publicera individuellt till var och en av dina ämnesslutpunkter.

## <a name="event-domain-overview"></a>Översikt över händelsedomän

En händelsedomän är ett hanteringsverktyg för ett stort antal Event Grid som är relaterade till samma program. Du kan se det som ett metaämne som kan ha tusentals enskilda ämnen.

Händelsedomäner ger dig samma arkitektur som används av Azure-tjänster som Storage och IoT Hub för att publicera deras händelser. De gör att du kan publicera händelser till tusentals ämnen. Domäner ger dig också auktoriserings- och autentiseringskontroll över varje ämne så att du kan partitionera dina klienter.

## <a name="example-use-case"></a>Exempel på användningsfall
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="access-management"></a>Åtkomsthantering

Med en domän får du mer information om auktorisering och autentiseringskontroll över varje ämne via rollbaserad åtkomstkontroll i Azure (Azure RBAC). Du kan använda dessa roller för att begränsa varje klientorganisation i ditt program till endast de ämnen som du vill ge dem åtkomst till.

Azure RBAC i händelsedomäner fungerar på samma sätt [som](security-authorization.md) hanterad åtkomstkontroll fungerar i resten av Event Grid och Azure. Använd Azure RBAC för att skapa och framtvinga anpassade rolldefinitioner i händelsedomäner.

### <a name="built-in-roles"></a>Inbyggda roller

Event Grid har två inbyggda rolldefinitioner som gör det enklare att arbeta med händelsedomäner i Azure RBAC. Dessa roller är **EventGrid EventSubscription-deltagare (förhandsversion)** och **EventGrid EventSubscription-läsare (förhandsversion).** Du tilldelar dessa roller till användare som behöver prenumerera på ämnen i din händelsedomän. Du kan begränsa rolltilldelningen till det ämne som användarna behöver prenumerera på.

Information om dessa roller finns [i Inbyggda roller för Event Grid](security-authorization.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Prenumerera på ämnen

Att prenumerera på händelser i ett ämne i [](./custom-event-quickstart.md) en händelsedomän är detsamma som att skapa en händelseprenumeration i ett anpassat ämne eller prenumerera på en händelse från en Azure-tjänst.

> [!IMPORTANT]
> Domänämnet anses vara **en automatiskt hanterad** resurs i Event Grid. Du kan skapa en händelseprenumeration i domänämnesomfånget utan att skapa domänämnet. I det här Event Grid automatiskt domänämnet för din räkning. Naturligtvis kan du fortfarande välja att skapa domänämnet manuellt. Det här beteendet gör att du kan oroa dig för en mindre resurs när du hanterar ett stort antal domänämnen. När den senaste prenumerationen på ett domänämne tas bort tas även domänämnet bort oavsett om domänämnet har skapats manuellt eller skapats automatiskt. 

### <a name="domain-scope-subscriptions"></a>Prenumerationer på domänomfång

Händelsedomäner tillåter även prenumerationer med domänomfång. En händelseprenumeration på en händelsedomän tar emot alla händelser som skickas till domänen oavsett vilket ämne händelserna skickas till. Prenumerationer på domänomfång kan vara användbara för hanterings- och granskningsändamål.

## <a name="publishing-to-an-event-domain"></a>Publicera till en händelsedomän

När du skapar en händelsedomän får du en publiceringsslutpunkt som liknar om du har skapat ett ämne i Event Grid. 

Om du vill publicera händelser till ett ämne i en händelsedomän, push-skicka händelserna till domänens slutpunkt på samma sätt som [för ett anpassat ämne.](./post-to-custom-topic.md) Den enda skillnaden är att du måste ange det ämne som du vill att händelsen ska levereras till.

Till exempel skulle publicering av följande matris med händelser skicka händelsen med `"id": "1111"` till ämnet medan händelsen med skulle skickas till ämnet `foo` `"id": "2222"` `bar` :

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

Händelsedomäner hanterar publicering till ämnen åt dig. I stället för att publicera händelser till varje ämne som du hanterar individuellt kan du publicera alla dina händelser till domänens slutpunkt. Event Grid ser till att varje händelse skickas till rätt ämne.

## <a name="limits-and-quotas"></a>Begränsningar och kvoter
Här är de gränser och kvoter som är relaterade till händelsedomäner:

- 100 000 ämnen per händelsedomän 
- 100 händelsedomäner per Azure-prenumeration 
- 500 händelseprenumerationer per ämne i en händelsedomän
- 50 prenumerationer på domänomfång 
- 5 000 händelser per sekund (till en domän)

Om dessa gränser inte passar dig kan du öppna en supportbiljett eller skicka ett e-postmeddelande till [askgrid@microsoft.com](mailto:askgrid@microsoft.com) . 

## <a name="pricing"></a>Priser
Händelsedomäner använder samma [driftpriser](https://azure.microsoft.com/pricing/details/event-grid/) som alla andra funktioner i Event Grid använder.

Åtgärder fungerar på samma sätt i händelsedomäner som i anpassade ämnen. Varje ingress av en händelse till en händelsedomän är en åtgärd, och varje leveransförsök för en händelse är en åtgärd.



## <a name="next-steps"></a>Nästa steg

* Mer information om att konfigurera händelsedomäner, skapa ämnen, skapa händelseprenumerationer och publicera händelser finns i [Hantera händelsedomäner.](./how-to-event-domains.md)
