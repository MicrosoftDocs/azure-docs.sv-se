---
title: Händelse domäner i Azure Event Grid
description: Den här artikeln beskriver hur du använder händelse domäner för att hantera flödet av anpassade händelser till olika affärs organisationer, kunder eller program.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 46a50a8ecc50bd1b80efcba41228564df1c36c9f
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198684"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Förstå händelse domäner för att hantera Event Grid ämnen

Den här artikeln beskriver hur du använder händelse domäner för att hantera flödet av anpassade händelser till olika affärs organisationer, kunder eller program. Använd händelse domäner för att:

* Hantera händelse arkitekturer för flera klient organisationer i stor skala.
* Hantera din auktorisering och autentisering.
* Partitionera dina ämnen utan att behöva hantera dem individuellt.
* Undvik att individuellt publicera till var och en av dina ämnes slut punkter.

## <a name="event-domain-overview"></a>Översikt över händelse domän

En händelse domän är ett hanterings verktyg för ett stort antal Event Grid ämnen relaterade till samma program. Du kan tänka på det som ett meta-ämne som kan ha tusentals enskilda ämnen.

Händelse domäner är tillgängliga för dig samma arkitektur som används av Azure-tjänster (t. ex. lagring och IoT Hub) för att publicera sina händelser. De gör att du kan publicera händelser till tusentals ämnen. Domäner ger dig också auktoriserings-och verifierings kontroll över varje ämne så att du kan partitionera dina klienter.

## <a name="example-use-case"></a>Exempel på användningsfall
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="access-management"></a>Åtkomsthantering

Med en domän får du detaljerad auktorisering och verifierings kontroll över varje ämne via rollbaserad åtkomst kontroll i Azure (Azure RBAC). Du kan använda dessa roller för att begränsa varje klient i ditt program till endast de ämnen som du vill ge dem åtkomst till.

Azure RBAC i händelse domäner fungerar på samma sätt som [hanterad åtkomst kontroll](security-authorization.md) fungerar i resten av event Grid och Azure. Använd Azure RBAC för att skapa och genomdriva anpassade roll definitioner i händelse domäner.

### <a name="built-in-roles"></a>Inbyggda roller

Event Grid har två inbyggda roll definitioner som gör det enklare för Azure RBAC att arbeta med händelse domäner. De här rollerna är **EventGrid EventSubscription Contributor (för hands version)** och **EventGrid EventSubscription Reader (för hands version)**. Du tilldelar dessa roller till användare som behöver prenumerera på ämnen i din händelse domän. Du omfångerar roll tilldelningen till endast det ämne som användarna behöver för att prenumerera på.

Information om dessa roller finns i [inbyggda roller för Event Grid](security-authorization.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Prenumererar på ämnen

Att prenumerera på händelser för ett ämne i en händelse domän är detsamma som att [skapa en händelse prenumeration i ett anpassat ämne](./custom-event-quickstart.md) eller prenumerera på en händelse från en Azure-tjänst.

### <a name="domain-scope-subscriptions"></a>Domän omfattnings prenumerationer

Händelse domäner tillåter även DNS-omfångs prenumerationer. En händelse prenumeration på en händelse domän får alla händelser som skickas till domänen, oavsett vilket ämne händelserna skickas till. DNS-omfångs prenumerationer kan vara användbara för hanterings-och gransknings syfte.

## <a name="publishing-to-an-event-domain"></a>Publicera till en händelse domän

När du skapar en händelse domän får du en publicerings slut punkt som liknar om du har skapat ett ämne i Event Grid. 

Om du vill publicera händelser till ett ämne i en händelse domän, pusha händelserna till domänens slut punkt på [samma sätt som du skulle göra för ett anpassat ämne](./post-to-custom-topic.md). Den enda skillnaden är att du måste ange det ämne som du vill att händelsen ska leverera till.

Exempel: om du publicerar följande händelse mat ris skickas händelse med `"id": "1111"` till ämne `foo` medan händelsen med `"id": "2222"` skulle skickas till ämnet `bar` :

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

Händelse domäner hanterar publicering till ämnen. I stället för att publicera händelser till varje ämne som du hanterar individuellt kan du publicera alla dina händelser till domänens slut punkt. Event Grid ser till att varje händelse skickas till rätt ämne.

## <a name="limits-and-quotas"></a>Begränsningar och kvoter
Här följer de gränser och kvoter som rör händelse domäner:

- 100 000 ämnen per händelse domän 
- 100 händelse domäner per Azure-prenumeration 
- 500 händelse prenumerationer per ämne i en händelse domän
- 50 domän omfångs prenumerationer 
- antal 5 000 händelser per sekund (till en domän)

Om dessa begränsningar inte passar dig kan du kontakta produkt teamet genom att öppna ett support ärende eller genom att skicka ett e-postmeddelande till [askgrid@microsoft.com](mailto:askgrid@microsoft.com) . 

## <a name="pricing"></a>Prissättning
Händelse domäner använder samma [Operations-prissättning](https://azure.microsoft.com/pricing/details/event-grid/) som alla andra funktioner i Event Grid använda.

Åtgärder fungerar på samma sätt i händelse domäner som i anpassade ämnen. Varje ingress av en händelse till en händelse domän är en åtgärd, och varje leverans försök för en händelse är en åtgärd.



## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar händelse domäner, skapar ämnen, skapar händelse prenumerationer och publicerar händelser finns i [Hantera händelse domäner](./how-to-event-domains.md).
