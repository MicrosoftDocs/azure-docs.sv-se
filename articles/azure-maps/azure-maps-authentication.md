---
title: Autentisering med Microsoft Azure Maps
titleSuffix: Azure Maps
description: 'Lär dig mer om två sätt att autentisera begär anden i Azure Maps: autentisering med delad nyckel och Azure Active Directory (Azure AD).'
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 9c973859c8b7a3d04693946f50377837c3538b85
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101094095"
---
# <a name="authentication-with-azure-maps"></a>Autentisering med Azure Maps

Azure Maps stöder två sätt att autentisera begär Anden: autentisering med delad nyckel och [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) . I den här artikeln beskrivs båda autentiseringsmetoderna för att hjälpa din implementering av Azure Maps-tjänster.

> [!NOTE]
> För att förbättra säker kommunikation med Azure Maps stöder vi nu Transport Layer Security (TLS) 1,2 och vi drar tillbaka support för TLS 1,0 och 1,1. Om du för närvarande använder TLS 1. x kan du utvärdera din TLS 1,2-beredskap och utveckla en migrations plan med de tester som beskrivs i [lösa TLS 1,0-problemet](/security/solving-tls1-problem).

## <a name="shared-key-authentication"></a>Autentisering med delad nyckel

 Primära och sekundära nycklar genereras efter att Azure Maps kontot har skapats. Du uppmanas att använda den primära nyckeln som prenumerations nyckel när du anropar Azure Maps med autentisering med delad nyckel. Autentisering med delad nyckel skickar en nyckel som genereras av ett Azure Maps-konto till en Azure Maps-tjänst. Lägg till *prenumerations nyckeln* som en parameter till URL: en för varje begäran om att Azure Maps tjänster. Den sekundära nyckeln kan användas i scenarier som löpande nyckel ändringar.  

Information om hur du visar dina nycklar i Azure Portal finns i [hantera autentisering](./how-to-manage-authentication.md#view-authentication-details).

> [!TIP]
> Av säkerhets skäl rekommenderar vi att du roterar mellan dina primära och sekundära nycklar. Om du vill rotera nycklar uppdaterar du appen så att den använder den sekundära nyckeln, distribuerar och trycker sedan på knappen cykel/uppdatera bredvid den primära nyckeln för att generera en ny primär nyckel. Den gamla primär nyckeln kommer att inaktive ras. Mer information om nyckel rotation finns i [konfigurera Azure Key Vault med nyckel rotation och granskning](../key-vault/secrets/tutorial-rotation-dual.md)

## <a name="azure-ad-authentication"></a>Azure AD-autentisering

Azure-prenumerationer levereras med en Azure AD-klient för att möjliggöra detaljerad åtkomst kontroll. Azure Maps tillhandahåller autentisering för Azure Maps-tjänster med hjälp av Azure AD. Azure AD ger identitets-baserad autentisering för användare och program som registrerats i Azure AD-klienten.

Azure Maps accepterar **OAuth 2,0** -åtkomsttoken för Azure AD-klienter som är associerade med en Azure-prenumeration som innehåller ett Azure Maps-konto. Azure Maps accepterar också token för:

* Azure AD-användare
* Partner program som använder behörigheter som delegerats av användare
* Hanterade identiteter för Azure-resurser

Azure Maps skapar ett *unikt ID (klient-ID)* för varje Azure Maps-konto. Du kan begära token från Azure AD när du kombinerar det här klient-ID: t med ytterligare parametrar.

Mer information om hur du konfigurerar Azure AD och begär token för Azure Maps finns i [hantera autentisering i Azure Maps](./how-to-manage-authentication.md).

Allmän information om hur du autentiserar med Azure AD finns i [Vad är autentisering?](../active-directory/develop/authentication-vs-authorization.md).

### <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Hanterade identiteter för Azure-resurser och Azure Maps

[Hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) ger Azure-tjänster med ett automatiskt hanterat program baserat säkerhets objekt som kan autentiseras med Azure AD. Med rollbaserad åtkomst kontroll i Azure (Azure RBAC) kan det hanterade identitets säkerhets objekt ha behörighet att få åtkomst till Azure Maps tjänster. Några exempel på hanterade identiteter är: Azure App Service, Azure Functions och Azure Virtual Machines. En lista över hanterade identiteter finns i [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

### <a name="configuring-application-azure-ad-authentication"></a>Konfigurera Azure AD-autentisering för program

Program kommer att autentiseras med Azure AD-klienten med hjälp av ett eller flera scenarier som stöds av Azure AD. Varje scenario för Azure AD-program representerar olika krav baserat på affärs behov. Vissa program kan kräva användar inloggnings upplevelser och andra program kan kräva en program inloggnings upplevelse. Mer information finns i [autentiserings flöden och program scenarier](../active-directory/develop/authentication-flows-app-scenarios.md).

När programmet har tagit emot en åtkomsttoken skickar SDK: n och/eller programmet en HTTPS-begäran med följande uppsättning obligatoriska HTTP-huvuden förutom andra REST API HTTP-huvuden:

| Rubrik namn    | Värde               |
| :------------- | :------------------ |
| x-ms-client-id | 30d7cc….9f55        |
| Auktorisering  | Bearer eyJ0e….HNIVN |

> [!NOTE]
> `x-ms-client-id` är det Azure Maps kontobaserade GUID som visas på sidan Azure Maps autentisering.

Här är ett exempel på en Azure Maps Route-begäran som använder en Azure AD OAuth Bearer-token:

```http
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

Information om hur du visar ditt klient-ID finns i [Visa information om autentisering](./how-to-manage-authentication.md#view-authentication-details).

## <a name="authorization-with-role-based-access-control"></a>Auktorisering med rollbaserad åtkomst kontroll

Azure Maps stöder åtkomst till alla huvud typer för [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md) , inklusive: enskilda Azure AD-användare, grupper, program, Azure-resurser och Azure Managed-identiteter. Huvud typer beviljas en uppsättning behörigheter, även kallat en roll definition. En roll definition ger behörighet att REST API åtgärder. Att tillämpa åtkomst till ett eller flera Azure Maps-konton kallas för ett omfång. När du använder ett huvud konto, en roll definition och ett omfång skapas en roll tilldelning. 

I nästa avsnitt beskrivs begrepp och komponenter i Azure Maps integrering med Azure RBAC. Som en del av processen för att konfigurera ditt Azure Maps-konto är en Azure AD-katalog kopplad till Azure-prenumerationen som Azure Maps-kontot finns i. 

När du konfigurerar Azure RBAC väljer du ett säkerhets objekt och tillämpar det på en roll tilldelning. Information om hur du lägger till roll tilldelningar på Azure Portal finns i [tilldela Azure-roller](../role-based-access-control/role-assignments-portal.md).

### <a name="picking-a-role-definition"></a>Plocka en roll definition

Följande roll definitions typer finns för att stödja program scenarier.

| Definition av Azure-roll       | Beskrivning                                                                                              |
| :-------------------------- | :------------------------------------------------------------------------------------------------------- |
| Azure Maps data läsare      | Ger åtkomst till oföränderliga Azure Maps REST-API: er.                                                       |
| Azure Maps data deltagare | Ger åtkomst till föränderligt Azure Maps REST-API: er. Föränderlighet definieras av åtgärderna: Skriv och ta bort. |
| Definition av anpassad roll      | Skapa en utformad roll för att aktivera flexibel begränsad åtkomst till Azure Maps REST-API: er.                      |

Vissa Azure Maps-tjänster kan kräva förhöjda privilegier för att utföra Skriv-eller borttagnings åtgärder på Azure Maps REST API: er. Rollen Azure Maps data deltagare krävs för tjänster som tillhandahåller Skriv-eller borttagnings åtgärder. I följande tabell beskrivs vilka tjänster Azure Maps data deltagaren kan använda för när du använder Skriv-eller borttagnings åtgärder på den aktuella tjänsten. Om bara Läs åtgärder används för tjänsten kan Azure Maps data läsare användas i stället för Azure Maps data deltagare.

| Azure Maps tjänst | Azure Maps roll definition  |
| :----------------- | :-------------------------- |
| Data               | Azure Maps data deltagare |
| Creator            | Azure Maps data deltagare |
| Spatial            | Azure Maps data deltagare |

Information om hur du visar dina Azure RBAC-inställningar finns i [så här konfigurerar du Azure RBAC för Azure Maps](./how-to-manage-authentication.md).

#### <a name="custom-role-definitions"></a>Anpassade roll definitioner

En aspekt av program säkerhet är att tillämpa principen om minsta behörighet. Den här principen innebär att säkerhetsobjektet endast ska beviljas åtkomst som krävs, och som inte har ytterligare åtkomst. Att skapa anpassade roll definitioner kan stödja användnings fall som kräver ytterligare granularitet för åtkomst kontroll. Om du vill skapa en anpassad roll definition kan du välja vissa data åtgärder som ska tas med eller undantas för definitionen.

Definitionen av den anpassade rollen kan sedan användas i en roll tilldelning för alla säkerhets objekt. Mer information om anpassade roll definitioner för Azure finns i [Azure-anpassade roller](../role-based-access-control/custom-roles.md).

Här följer några exempel scenarier där anpassade roller kan förbättra program säkerheten.

| Scenario                                                                                                                                                                                                                 | Åtgärd (er) för anpassade roll data                                                                                                                  |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| En offentlig eller interaktiv inloggnings webb sida med grundläggande kart paneler och inga andra REST-API: er.                                                                                                                              | `Microsoft.Maps/accounts/services/render/read`                                                                                              |
| Ett program som bara kräver omvänt och inte andra REST-API: er.                                                                                                                                             | `Microsoft.Maps/accounts/services/search/read`                                                                                              |
| En roll för ett säkerhets objekt som begär läsning av Azure Maps Creators baserade kart data och REST-API: er för bas kart panel.                                                                                                 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/render/read`                                                |
| En roll för ett säkerhets objekt som kräver läsning, skrivning och borttagning av skapare baserade kart data. Detta kan definieras som en roll för kart data redigerare men tillåter inte åtkomst till andra REST-API: er som bas kart paneler. | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/data/write`, `Microsoft.Maps/accounts/services/data/delete` |

### <a name="understanding-scope"></a>Förstå omfattning

När du skapar en roll tilldelning definieras den i Azure-resursens hierarki. Högst upp i hierarkin är en [hanterings grupp](../governance/management-groups/overview.md) och den lägsta är en Azure-resurs, till exempel ett Azure Maps konto.
Genom att tilldela en roll tilldelning till en resurs grupp kan du ge åtkomst till flera Azure Maps konton eller resurser i gruppen.

> [!TIP]
> Microsofts allmänna rekommendation är att tilldela åtkomst till Azure Maps konto omfånget, eftersom den förhindrar **oönskad åtkomst till andra Azure Maps konton som** finns i samma Azure-prenumeration.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure RBAC i
> [!div class="nextstepaction"]
> [Rollbaserad Azure-åtkomstkontroll](../role-based-access-control/overview.md)

Mer information om hur du autentiserar ett program med Azure AD och Azure Maps finns i
> [!div class="nextstepaction"]
> [Hantera autentisering i Azure Maps](./how-to-manage-authentication.md)

Mer information om hur du autentiserar Azure Maps Kartkontroll med Azure AD finns i
> [!div class="nextstepaction"]
> [Använd Azure Maps Kartkontroll](./how-to-use-map-control.md)