---
title: Migrera till den nya utvecklarportalen från den äldre utvecklarportalen
titleSuffix: Azure API Management
description: Lär dig hur du migrerar från den äldre utvecklarportalen till den nya utvecklarportalen i API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.openlocfilehash: e4f9f3822b58886f7d453d52402b078d8401133f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738890"
---
# <a name="migrate-to-the-new-developer-portal"></a>Migrera till den nya utvecklarportalen

Den här artikeln beskriver de steg du måste vidta för att migrera från den föråldrade äldre portalen till den nya utvecklarportalen i API Management.

> [!IMPORTANT]
> Den äldre utvecklarportalen är nu inaktuell och får endast säkerhetsuppdateringar. Du kan fortsätta att använda den, som vanligt, tills den tas ur bruk i oktober 2023, när den tas bort från alla API Management tjänster.

![API Management utvecklarportalen](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="improvements-in-new-developer-portal"></a>Förbättringar i den nya utvecklarportalen

Den nya utvecklarportalen hanterar många begränsningar i den inaktuella portalen. Den har en [visuell dra och släpp-redigerare för redigering av innehåll](api-management-howto-developer-portal-customize.md) och en särskild panel där designers kan formatera webbplatsen. Sidor, anpassningar och konfiguration sparas som Azure Resource Manager i din API Management-tjänst, vilket gör att du [kan automatisera portaldistributioner.](automate-portal-deployments.md) Slutligen är portalens kodbas öppen källkod, så du [kan utöka den med anpassade funktioner.](api-management-howto-developer-portal.md#managed-vs-self-hosted)

## <a name="how-to-migrate-to-new-developer-portal"></a>Så här migrerar du till den nya utvecklarportalen

Den nya utvecklarportalen är inte kompatibel med den inaktuella portalen och automatiserad migrering är inte möjlig. Du måste återskapa innehållet manuellt (sidor, text, mediefiler) och anpassa utseendet på den nya portalen. De exakta stegen varierar beroende på portalens anpassningar och komplexitet. Se [självstudien för utvecklarportalen](api-management-howto-developer-portal-customize.md) för vägledning. Återstående konfiguration, till exempel listan över API:er, produkter, användare och identitetsproviders, delas automatiskt mellan båda portalerna.

> [!IMPORTANT]
> Om du har startat den nya utvecklarportalen tidigare, men inte har gjort några ändringar, återställer du standardinnehållet för att uppdatera det till den senaste versionen.

Tänk på följande ändringar när du migrerar från den inaktuella portalen:

- Om du exponerar utvecklarportalen via en anpassad domän [tilldelar du en domän till](configure-custom-domain.md) den nya utvecklarportalen. Använd alternativet **Utvecklarportal** i listrutan i Azure Portal.
- [Tillämpa en CORS-princip](developer-portal-faq.md#cors) på dina API:er för att aktivera den interaktiva testkonsolen.
- Om du matar in anpassad CSS för att formatera portalen måste du [replikera formateringen med hjälp av den inbyggda designpanelen](api-management-howto-developer-portal-customize.md). CSS- injection tillåts inte i den nya portalen.
- Du kan bara mata in anpassad JavaScript [i den egenvärdbaserade versionen av den nya portalen](api-management-howto-developer-portal.md#managed-vs-self-hosted).
- Om ditt API Management är i ett virtuellt nätverk och exponeras mot Internet via Application Gateway [kan](api-management-howto-integrate-internal-vnet-appgateway.md) du läsa den här dokumentationsartikeln för exakta konfigurationssteg. Du måste:

    - Aktivera anslutning till API Management hanteringsslutpunkten.
    - Aktivera anslutning till den nya portalslutpunkten.
    - Inaktivera valda Web Application Firewall regler.

- Om du har ändrat standardmallarna för e-postaviseringar så att de innehåller en explicit definierad inaktuell portal-URL ändrar du dem så att de antingen använder portalens URL-parameter eller pekar på den nya portalens URL. Om mallarna använder den inbyggda portal-URL-parametern i stället krävs inga ändringar.
- *Problem* och *program* stöds inte i den nya utvecklarportalen.
- Direkt integrering med Facebook, Microsoft, Twitter och Google som identitetsproviders stöds inte i den nya utvecklarportalen. Du kan integrera med dessa leverantörer via Azure AD B2C.
- Om du använder delegering ändrar du retur-URL:en i dina program och använder API-slutpunkten Get [ *Shared Access Token* istället](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) för Generate *SSO URL* endpoint (Skapa URL för enkel inloggning).
- Om du använder Azure AD som identitetsprovider:

    - Ändra retur-URL:en i programmet så att den pekar på den nya utvecklarportaldomänen.
    - Ändra suffixet för retur-URL:en i ditt program `/signin-aad` från till `/signin` .

- Om du använder Azure AD B2C som identitetsprovider:

    - Ändra retur-URL:en i programmet så att den pekar på den nya utvecklarportaldomänen.
    - Ändra suffixet för retur-URL:en i ditt program från `/signin-aad` till `/signin` .
    - Inkludera *förnamn,* *efternamn* och *användarens objekt-ID* i programanspråken.

- Om du använder OAuth 2.0 i den interaktiva testkonsolen ändrar du retur-URL:en i programmet så att den pekar på den nya utvecklarportaldomänen och ändrar suffixet:

    - Från `/docs/services/[serverName]/console/oauth2/authorizationcode/callback` till `/signin-oauth/code/callback/[serverName]` för auktoriseringskodens beviljandeflöde.
    - Från `/docs/services/[serverName]/console/oauth2/implicit/callback` till för det `/signin-oauth/implicit/callback` implicita beviljandeflödet.
- Om du använder OpenID Connect i den interaktiva testkonsolen ändrar du retur-URL:en i programmet så att den pekar på den nya utvecklarportaldomänen och ändrar suffixet:

    - Från `/docs/services/[serverName]/console/openidconnect/authorizationcode/callback` till `/signin-oauth/code/callback/[serverName]` för auktoriseringskodens beviljandeflöde.
    - Från `/docs/services/[serverName]/console/openidconnect/implicit/callback` till för det `/signin-oauth/implicit/callback` implicita beviljandeflödet.

## <a name="next-steps"></a>Nästa steg

Läs mer om utvecklarportalen:

- [Översikt över Azure API Management-utvecklarportalen](api-management-howto-developer-portal.md)
- [Få åtkomst till och anpassa utvecklarportalen](api-management-howto-developer-portal-customize.md)