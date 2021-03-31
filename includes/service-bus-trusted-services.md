---
title: ta med fil
description: ta med fil
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 03/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cd4d5de5d93e4aea862aaabd10fb5d3c6d45cb1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102510638"
---
## <a name="trusted-microsoft-services"></a>Betrodda Microsoft-tjänster
När du aktiverar inställningen **Tillåt att betrodda Microsoft-tjänster kringgår den här brand Väggs** inställningen beviljas följande tjänster åtkomst till dina Service Bus-resurser.

| Betrodd tjänst | Användnings scenarier som stöds | 
| --------------- | ------------------------- | 
| Azure Event Grid | Tillåter Azure Event Grid att skicka händelser till köer eller ämnen i Service Bus namn området. Du måste också utföra följande steg: <ul><li>Aktivera systemtilldelad identitet för ett ämne eller en domän</li><li>Lägg till identiteten i rollen Azure Service Bus data avsändare i Service Bus-namnrymden</li><li>Konfigurera sedan händelse prenumerationen som använder en Service Bus kö eller ett ämne som en slut punkt för att använda den systemtilldelade identiteten.</li></ul> <p>Mer information finns i [händelse leverans med en hanterad identitet](../articles/event-grid/managed-service-identity.md)</p>|
| Azure API Management | <p>Med tjänsten API Management kan du skicka meddelanden till en Service Bus kö/ett ämne i namn området för Service Bus.</p><ul><li>Du kan utlösa anpassade arbets flöden genom att skicka meddelanden till Service Bus kö/ämne när ett API anropas med hjälp av [principen för att skicka begäran](../articles/api-management/api-management-sample-send-request.md).</li><li>Du kan också behandla en Service Bus kö/ämne som Server del i ett API. En exempel princip finns i [autentisera med hjälp av en hanterad identitet för att få åtkomst till en Service Bus kö eller ett ämne](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Service%20Bus.xml). Du måste också utföra följande steg:<ol><li>Aktivera systemtilldelad identitet på API Management-instansen. Instruktioner finns i [använda hanterade identiteter i Azure API Management](../articles/api-management/api-management-howto-use-managed-service-identity.md).</li><li>Lägg till identiteten i rollen **Azure Service Bus data avsändare** i Service Bus-namnrymden</li></ol></li></ul> | 