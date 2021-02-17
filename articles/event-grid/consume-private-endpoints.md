---
title: Leverera händelser med hjälp av tjänsten Private Link
description: Den här artikeln beskriver hur du kan undvika begränsningen av att inte leverera händelser med hjälp av tjänsten Private Link.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: 4343740ea6c34c9ae282723b79007f7035785b04
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548630"
---
# <a name="deliver-events-using-private-link-service"></a>Leverera händelser med hjälp av tjänsten Private Link
För närvarande går det inte att leverera händelser med [privata slut punkter](../private-link/private-endpoint-overview.md). Det finns inget stöd om du har strikta krav på nätverks isolering där dina levererade händelse trafik inte får lämna det privata IP-utrymmet. 

## <a name="use-managed-identity"></a>Använda hanterad identitet
Men om dina krav kräver ett säkert sätt att skicka händelser med hjälp av en krypterad kanal och en känd identitet på avsändaren (i det här fallet Event Grid) med hjälp av offentligt IP-utrymme kan du leverera händelser till Event Hubs, Service Bus eller Azure Storage tjänst med ett anpassat ämne i Azure Event Grid eller en domän med Systemhanterad identitet. Mer information om hur du levererar händelser med hanterad identitet finns i [händelse leverans med hjälp av en hanterad identitet](managed-service-identity.md). 

Sedan kan du använda en privat länk som kon figurer ATS i Azure Functions eller webhooken som distribueras i det virtuella nätverket för att hämta händelser. Se exemplet: [Anslut till privata slut punkter med Azure Functions](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).


:::image type="content" source="./media/consume-private-endpoints/deliver-private-link-service.svg" alt-text="Leverera via privat länk tjänst":::


Under den här konfigurationen går trafiken över den offentliga IP/Internet från Event Grid till Event Hubs, Service Bus eller Azure Storage, men kanalen kan krypteras och en hanterad identitet för Event Grid används. Om du konfigurerar Azure Functions eller webhook som har distribuerats till det virtuella nätverket för att använda en Event Hubs, Service Bus eller Azure Storage via privat länk kommer det att finnas kvar i Azure i den här delen av trafiken.

## <a name="deliver-events-to-event-hubs-using-managed-identity"></a>Leverera händelser till Event Hubs med hjälp av hanterad identitet
Följ dessa steg om du vill leverera händelser till händelse nav i Event Hubs namn område med hanterad identitet:

1. [Aktivera systemtilldelad identitet för ett ämne eller en domän](managed-service-identity.md#create-a-custom-topic-or-domain-with-an-identity). 
1. [Lägg till identiteten i rollen **Azure Event Hubs data Sender** i Event Hubs-namnområdet](../event-hubs/authenticate-managed-identity.md#to-assign-azure-roles-using-the-azure-portal).
1. [Aktivera inställningen **Tillåt att betrodda Microsoft-tjänster kringgår den här brand Väggs** inställningen på din Event Hubs-namnrymd](../event-hubs/event-hubs-service-endpoints.md#trusted-microsoft-services). 
1. [Konfigurera händelse prenumerationen](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) som använder en händelsehubben som en slut punkt för att använda den systemtilldelade identiteten.

## <a name="deliver-events-to-service-bus-using-managed-identity"></a>Leverera händelser till Service Bus med hjälp av hanterad identitet
Följ dessa steg om du vill leverera händelser till Service Bus köer eller ämnen i Service Bus-namnrymden med hanterad identitet:

1. [Aktivera systemtilldelad identitet för ett ämne eller en domän](managed-service-identity.md#create-a-custom-topic-or-domain-with-an-identity). 
1. Lägg till identiteten i rollen [Azure Service Bus data avsändare](/service-bus-messaging/service-bus-managed-service-identity.md#azure-built-in-roles-for-azure-service-bus) i Service Bus-namnrymden
1. [Aktivera inställningen **Tillåt att betrodda Microsoft-tjänster kringgår den här brand Väggs** inställningen på din Service Bus-namnrymd](../service-bus-messaging/service-bus-service-endpoints.md#trusted-microsoft-services). 
1. [Konfigurera händelse prenumerationen](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) som använder en Service Bus kö eller ett ämne som en slut punkt för att använda den systemtilldelade identiteten.

## <a name="deliver-events-to-storage"></a>Leverera händelser till lagring 
Följ dessa steg om du vill leverera händelser till lagrings köer med hanterad identitet:

1. [Aktivera systemtilldelad identitet för ett ämne eller en domän](managed-service-identity.md#create-a-custom-topic-or-domain-with-an-identity).
1. Lägg till identiteten i den [avsändare rollen för lagrings köns data meddelande](../storage/common/storage-auth-aad-rbac-portal.md) i Azure Storage kö.
1. [Konfigurera händelse prenumerationen](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) som använder en Service Bus kö eller ett ämne som en slut punkt för att använda den systemtilldelade identiteten.


## <a name="next-steps"></a>Nästa steg
Mer information om att leverera händelser med hjälp av en hanterad identitet finns i [händelse leverans med hjälp av en hanterad identitet](managed-service-identity.md). 
