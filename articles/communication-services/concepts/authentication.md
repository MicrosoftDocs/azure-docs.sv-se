---
title: Autentisera till Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Lär dig mer om de olika sätt som en app eller tjänst kan autentisera till kommunikations tjänster.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 0146ff9ce3ec4821bee7ce34700ca4198bb23ddc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598872"
---
# <a name="authenticate-to-azure-communication-services"></a>Autentisera till Azure Communication Services

Varje klient interaktion med Azure Communication Services måste autentiseras. I en typisk arkitektur, se [klient-och server arkitektur](./client-and-server-architecture.md), *åtkomst nycklar* eller *hanterade identiteter* används för autentisering.

En annan typ av autentisering använder *token för användar åtkomst* för att autentisera mot tjänster som kräver användar deltagande. Chatt-eller anrops tjänsten använder till exempel *token för användar åtkomst* så att användarna kan läggas till i en tråd och ha konversationer med varandra.

## <a name="authentication-options"></a>Autentiseringsalternativ

I följande tabell visas klient biblioteken för Azure Communication Services och deras autentiseringsalternativ:

| Klient bibliotek    | Autentiserings alternativ                               |
| ----------------- | ----------------------------------------------------|
| Identitet          | Åtkomst nyckel eller hanterad identitet                      |
| SMS               | Åtkomst nyckel eller hanterad identitet                      |
| Telefonnummer     | Åtkomst nyckel eller hanterad identitet                      |
| Sänder           | Token för användar åtkomst                                   |
| Chatt              | Token för användar åtkomst                                   |

Varje Authorization-alternativ beskrivs kortfattat nedan:

### <a name="access-key"></a>Åtkomstnyckel

Autentisering av åtkomst nycklar är lämpligt för tjänst program som körs i en betrodd tjänst miljö. Du hittar din åtkomst nyckel i Azure Communication Services-portalen. Tjänst programmet använder det som autentiseringsuppgifter för att initiera motsvarande klient bibliotek. Se ett exempel på hur det används i [identitets klient biblioteket](../quickstarts/access-tokens.md). 

Eftersom åtkomst nyckeln är en del av anslutnings strängen för din resurs motsvarar autentiseringen med en anslutnings sträng autentisering med en åtkomst nyckel.

Om du vill anropa ACS-API: er manuellt med hjälp av en åtkomst nyckel måste du signera begäran. Signering av begäran förklaras i detalj i en [själv studie kurs](../tutorials/hmac-header-tutorial.md).

### <a name="managed-identity"></a>Hanterad identitet

Hanterade identiteter ger överlägsen säkerhet och enkel användning över andra alternativ för auktorisering. Genom att använda Azure AD undviker du till exempel att du behöver lagra din konto åtkomst nyckel i din kod, som du gör med åtkomst nyckel behörighet. Även om du kan fortsätta att använda åtkomst nyckel godkännande med kommunikations tjänst program, rekommenderar Microsoft att du flyttar till Azure AD där det är möjligt. 

[Skapa ett registrerat program från Azure CLI](../quickstarts/managed-identity-from-cli.md)om du vill konfigurera en hanterad identitet. Sedan kan slut punkten och autentiseringsuppgifterna användas för att autentisera klient biblioteken. Se exempel på hur [hanterad identitet](../quickstarts/managed-identity.md) används.

### <a name="user-access-tokens"></a>Token för användar åtkomst

Token för användar åtkomst genereras med identitets klient biblioteket och associeras med användare som skapats i identitets klient biblioteket. Se ett exempel på hur du [skapar användare och skapar token](../quickstarts/access-tokens.md). Sedan används token för användar åtkomst för att autentisera deltagare som har lagts till i konversationer i chatten eller genom att anropa SDK. Mer information finns i [lägga till chatt till din app](../quickstarts/chat/get-started.md). Autentiseringen av användar åtkomst-token skiljer sig jämfört med åtkomst nyckel och hanterad identitetsautentisering i som används för att autentisera en användare i stället för en skyddad Azure-resurs.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa och hantera resurser](../quickstarts/create-communication-resource.md) 
>  för kommunikations tjänster [Skapa ett Azure Active Directory hanterat identitets program från Azure CLI](../quickstarts/managed-identity-from-cli.md) 
>  [Skapa åtkomsttoken för användare](../quickstarts/access-tokens.md)

Mer information finns i följande artiklar:
- [Lär dig mer om klient-och server arkitektur](../concepts/client-and-server-architecture.md)
