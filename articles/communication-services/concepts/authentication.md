---
title: Autentisera till Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Lär dig mer om de olika sätt som en app eller tjänst kan autentisera till kommunikations tjänster.
author: GrantMeStrength
manager: jken
services: azure-communication-services
ms.author: jken
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 1267fc53bd6dcbae504b01610267059545353dc5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101655912"
---
# <a name="authenticate-to-azure-communication-services"></a>Autentisera till Azure Communication Services

Varje klient interaktion med Azure Communication Services måste autentiseras. I en typisk arkitektur, se [klient-och server arkitektur](./client-and-server-architecture.md), *åtkomst nycklar* eller *hanterad identitet* används i den betrodda användar åtkomst tjänsten för att skapa användare och utfärda token. Och *användar åtkomst-token* som utfärdas av den betrodda tjänsten för användar åtkomst används för klient program för att få åtkomst till andra kommunikations tjänster, till exempel chatt eller uppringnings tjänsten.

Azure Communication Services SMS-tjänsten accepterar också *åtkomst nycklar* eller *hanterad identitet* för autentisering. Detta sker vanligt vis i ett tjänst program som körs i en betrodd tjänst miljö.

Varje Authorization-alternativ beskrivs kortfattat nedan:

- **Åtkomst nyckel** autentisering för SMS-och identitets åtgärder. Autentisering av åtkomst nycklar är lämpligt för tjänst program som körs i en betrodd tjänst miljö. Du hittar åtkomst nyckeln i Azure Communication Services-portalen. För att autentisera med en åtkomst nyckel använder ett tjänst program åtkomst nyckeln som autentiseringsuppgifter för att initiera motsvarande SMS-eller identitets klient bibliotek. mer information finns i [skapa och hantera åtkomsttoken](../quickstarts/access-tokens.md). Eftersom åtkomst nyckeln är en del av anslutnings strängen för din resurs, se [skapa och hantera kommunikations tjänst resurser](../quickstarts/create-communication-resource.md), autentisering med anslutnings sträng motsvarar autentisering med åtkomst nyckel.
- **Hanterad identitets** autentisering för SMS-och identitets åtgärder. Hanterad identitet, se [hanterad identitet](../quickstarts/managed-identity.md), är lämplig för tjänst program som körs i en betrodd tjänst miljö. För att autentisera med en hanterad identitet skapar ett tjänst program en autentiseringsuppgift med ID och en hemlighet för den hanterade identiteten och initierar sedan motsvarande SMS-eller identitets klient bibliotek, se [skapa och hantera åtkomsttoken](../quickstarts/access-tokens.md).
- **Token** -autentisering för användar åtkomst för chatt och uppringning. Token för användar åtkomst gör att dina klient program kan autentisera mot Azure-kommunikation och att anropa tjänster. Dessa tokens skapas i en "betrodd användar åtkomst tjänst" som du skapar. De tillhandahålls sedan till klient enheter som använder token för att initiera chatten och anropa klient biblioteken. Mer information finns i [lägga till chatt till din app](../quickstarts/chat/get-started.md) till exempel.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa och hantera resurser](../quickstarts/create-communication-resource.md) 
>  för kommunikations tjänster [Skapa ett Azure Active Directory hanterat identitets program från Azure CLI](../quickstarts/managed-identity-from-cli.md) 
>  [Skapa token för användar åtkomst](../quickstarts/access-tokens.md)

Mer information finns i följande artiklar:
- [Lär dig mer om klient-och server arkitektur](../concepts/client-and-server-architecture.md)
