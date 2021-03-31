---
title: Migrera till den nya Developer-portalen från den äldre Developer-portalen
titleSuffix: Azure API Management
description: Lär dig hur du migrerar från den äldre Developer-portalen till den nya Developer-portalen i API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: f5105c685de4b3ccdffe69eec8ee8eeb32976c1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92326028"
---
# <a name="migrate-to-the-new-developer-portal"></a>Migrera till den nya Developer-portalen

Den här artikeln beskriver de steg som du måste vidta för att migrera från den föråldrade gamla portalen till den nya Developer-portalen i API Management.

> [!IMPORTANT]
> Den äldre Developer-portalen är nu inaktuell och kommer endast att få säkerhets uppdateringar. Du kan fortsätta att använda det, enligt vanligt, tills det upphör att gälla i oktober 2023 när det tas bort från alla API Management tjänster.

![API Management Developer-portalen](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="improvements-in-new-developer-portal"></a>Förbättringar i den nya Developer-portalen

Den nya Developer-portalen hanterar många begränsningar för den inaktuella portalen. Den innehåller ett [visuellt dra-och-släpp-redigerings program för att redigera innehåll](api-management-howto-developer-portal-customize.md) och en särskild panel för designers för att formatera webbplatsen. Sidor, anpassningar och konfiguration sparas som Azure Resource Manager resurser i API Management-tjänsten, vilket gör att du kan [Automatisera Portal distributioner](api-management-howto-developer-portal.md#automate). Slutligen är portalens kodbas öppen-källa, så att [du kan utöka den med anpassade funktioner](api-management-howto-developer-portal.md#managed-vs-self-hosted).

## <a name="how-to-migrate-to-new-developer-portal"></a>Så här migrerar du till den nya Developer-portalen

Den nya Developer-portalen är inte kompatibel med den inaktuella portalen och automatisk migrering är inte möjlig. Du måste manuellt återskapa innehållet (sidor, text, mediefiler) och anpassa utseendet på den nya portalen. Exakta steg varierar beroende på portalens anpassningar och komplexitet. I [självstudien för Developer-portalen](api-management-howto-developer-portal-customize.md) hittar du vägledning. Återstående konfiguration, som listan över API: er, produkter, användare, identitets leverantörer, delas automatiskt mellan båda portalerna.

> [!IMPORTANT]
> Om du har startat den nya Developer-portalen förut, men inte har gjort några ändringar, [återställer du standard innehållet](api-management-howto-developer-portal.md#preview-to-ga) för att uppdatera den till den senaste versionen.

När du migrerar från den inaktuella portalen bör du tänka på följande ändringar:

- Om du exponerar din utvecklings Portal via en anpassad domän, [tilldelar du en domän](configure-custom-domain.md) till den nya Developer-portalen. Använd alternativet **Developer Portal** i list rutan i Azure Portal.
- [Använd en CORS-princip](api-management-howto-developer-portal.md#cors) på dina API: er för att aktivera den interaktiva test konsolen.
- Om du infogar anpassad CSS för att formatera portalen måste du [Replikera formateringen med hjälp av den inbyggda design panelen](api-management-howto-developer-portal-customize.md). CSS-inmatning tillåts inte i den nya portalen.
- Du kan endast mata in anpassade JavaScript [-versioner i den nya portalen](api-management-howto-developer-portal.md#managed-vs-self-hosted).
- Om din API Management finns i ett virtuellt nätverk och exponeras för Internet via Application Gateway, [se den här dokumentations artikeln](api-management-howto-integrate-internal-vnet-appgateway.md) för exakta konfigurations steg. Du måste:

    - Aktivera anslutning till API Managementens hanterings slut punkt.
    - Aktivera anslutning till den nya Portal slut punkten.
    - Inaktivera de valda brand Väggs reglerna för webb program.

- Om du har ändrat standardmallarna för e-postaviseringar som innehåller en explicit definierad föråldrad portal-URL, ändrar du dem till antingen använda portalens URL-parameter eller pekar på den nya portalens URL. Om mallarna använder den inbyggda portal-URL-parametern i stället krävs inga ändringar.
- *Problem* och *program* stöds inte i den nya Developer-portalen.
- Direkt integrering med Facebook, Microsoft, Twitter och Google som identitets leverantörer stöds inte i den nya Developer-portalen. Du kan integrera med dessa leverantörer via Azure AD B2C.
- Om du använder delegering ändrar du retur-URL: en i dina program och använder [API-slutpunkten *Hämta delad åtkomsttoken*](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) i stället för URL-slutpunkten *generera SSO* .
- Om du använder Azure AD som identitets leverantör:

    - Ändra retur-URL: en i programmet så att den pekar på den nya Developer Portal-domänen.
    - Ändra suffixet för retur-URL: en i programmet från `/signin-aad` till `/signin` .

- Om du använder Azure AD B2C som identitets leverantör:

    - Ändra retur-URL: en i programmet så att den pekar på den nya Developer Portal-domänen.
    - Ändra suffixet för retur-URL: en i programmet från `/signin-aad` till `/signin` .
    - Ta med *angivet namn* *, efter namn och* *användarens objekt-ID* i program anspråk.

- Om du använder OAuth 2,0 i den interaktiva test konsolen ändrar du retur-URL: en i programmet så att den pekar på den nya Developer Portal-domänen och ändrar suffixet:

    - Från `/docs/services/[serverName]/console/oauth2/authorizationcode/callback` till `/signin-oauth/code/callback/[serverName]` för det utfärdande flödet för auktoriseringskod.
    - Från `/docs/services/[serverName]/console/oauth2/implicit/callback` till `/signin-oauth/implicit/callback` för det implicita tilldelnings flödet.
- Om du använder OpenID Connect i den interaktiva test konsolen ändrar du retur-URL: en i programmet så att den pekar på den nya Developer Portal-domänen och ändrar suffixet:

    - Från `/docs/services/[serverName]/console/openidconnect/authorizationcode/callback` till `/signin-oauth/code/callback/[serverName]` för det utfärdande flödet för auktoriseringskod.
    - Från `/docs/services/[serverName]/console/openidconnect/implicit/callback` till `/signin-oauth/implicit/callback` för det implicita tilldelnings flödet.

## <a name="next-steps"></a>Nästa steg

Läs mer om Developer-portalen:

- [Översikt över Azure API Management-utvecklarportalen](api-management-howto-developer-portal.md)
- [Komma åt och anpassa Developer-portalen](api-management-howto-developer-portal-customize.md)