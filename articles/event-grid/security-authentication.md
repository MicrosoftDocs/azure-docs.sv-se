---
title: Autentisera händelseleverans till händelsehanterare (Azure Event Grid)
description: Den här artikeln beskriver olika sätt att autentisera leverans till händelsehanterare i Azure Event Grid.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 7db258ee152e4b1c46362e74e0246b80513ca9f2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777265"
---
# <a name="authenticate-event-delivery-to-event-handlers-azure-event-grid"></a>Autentisera händelseleverans till händelsehanterare (Azure Event Grid)
Den här artikeln innehåller information om autentisering av händelseleverans till händelsehanterare. Den visar också hur du skyddar webhook-slutpunkterna som används för att ta emot händelser från Event Grid med hjälp av Azure Active Directory (Azure AD) eller en delad hemlighet.

## <a name="use-system-assigned-identities-for-event-delivery"></a>Använda system tilldelade identiteter för händelseleverans
Du kan aktivera en system tilldelad hanterad identitet för ett ämne eller en domän och använda identiteten för att vidarebefordra händelser till mål som stöds, till exempel Service Bus-köer och ämnen, händelsehubbbar och lagringskonton.

Här är stegen: 

1. Skapa ett ämne eller en domän med en system tilldelad identitet eller uppdatera ett befintligt ämne eller en domän för att aktivera identitet. 
1. Lägg till identiteten i en lämplig roll (till exempel Service Bus Data Sender) på målet (till exempel en Service Bus kö).
1. När du skapar händelseprenumerationer aktiverar du användningen av identiteten för att leverera händelser till målet. 

Detaljerade stegvisa instruktioner finns i Händelseleverans [med en hanterad identitet.](managed-service-identity.md)


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Autentisera händelseleverans till webhookslutpunkter
I följande avsnitt beskrivs hur du autentiserar händelseleverans till webhook-slutpunkter. Du måste använda en valideringshandskakningsmekanism oavsett vilken metod du använder. Mer [information finns i Webhook-händelseleverans.](webhook-event-delivery.md) 


### <a name="using-azure-active-directory-azure-ad"></a>Använda Azure Active Directory (Azure AD)
Du kan skydda webhook-slutpunkten som används för att ta emot händelser från Event Grid med hjälp av Azure AD. Du måste skapa ett Azure AD-program, skapa en roll och tjänstens huvudnamn i ditt program som auktoriserar Event Grid och konfigurera händelseprenumerationen så att den använder Azure AD-programmet. Lär dig hur [du konfigurerar Azure Active Directory med Event Grid](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Använda klienthemlighet som frågeparameter
Du kan också skydda din webhook-slutpunkt genom att lägga till frågeparametrar till webhookens mål-URL som anges som en del av skapandet av en händelseprenumeration. Ange att en av frågeparametrarna ska vara en klienthemlighet, till exempel en [åtkomsttoken](https://en.wikipedia.org/wiki/Access_token) eller en delad hemlighet. Event Grid-tjänsten innehåller alla frågeparametrar i varje begäran om händelseleverans till webhooken. Webhook-tjänsten kan hämta och verifiera hemligheten. Om klienthemligheten uppdateras måste även händelseprenumerationen uppdateras. För att undvika leveransfel under den här hemliga rotationen måste webhooken acceptera både gamla och nya hemligheter under en begränsad tid innan händelseprenumerationen uppdateras med den nya hemligheten. 

Eftersom frågeparametrar kan innehålla klienthemligheter hanteras de med extra försiktighet. De lagras som krypterade och är inte tillgängliga för tjänstoperatörer. De loggas inte som en del av tjänstloggarna/spårningarna. När du hämtar egenskaperna för händelseprenumerationen returneras inte målfrågeparametrarna som standard. Exempel: [parametern --include-full-endpoint-url](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_show) ska användas i Azure [CLI](/cli/azure).

Mer information om hur du levererar händelser till webhooks finns i [Webhook event delivery (Webhook-händelseleverans)](webhook-event-delivery.md)

> [!IMPORTANT]
> Azure Event Grid stöder endast  HTTPS-webhookslutpunkter. 

## <a name="endpoint-validation-with-cloudevents-v10"></a>Slutpunktsvalidering med CloudEvents v1.0
Om du redan är bekant med Event Grid kanske du känner till handskakningen för slutpunktsverifiering för att förhindra missbruk. CloudEvents v1.0 implementerar sin egen semantik [för missbruksskydd med](webhook-event-delivery.md) hjälp av **HTTP OPTIONS-metoden.** Läs mer om det i [HTTP 1.1 Web Hooks för händelseleverans – version 1.0.](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection) När du använder CloudEvents-schemat för utdata använder Event Grid CloudEvents v1.0-missbruksskydd i stället för Event Grid för valideringshändelsen. Mer information finns i [Använda CloudEvents v1.0-schema med Event Grid](cloudevents-schema.md). 


## <a name="next-steps"></a>Nästa steg
Se [Autentisera publiceringsklienter](security-authenticate-publishing-clients.md) om du vill veta mer om autentisering av klienter som publicerar händelser till ämnen eller domäner. 
