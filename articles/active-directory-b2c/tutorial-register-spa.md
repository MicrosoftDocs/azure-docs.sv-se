---
title: 'Självstudie: registrera ett program med en enda sida'
titleSuffix: Azure AD B2C
description: I den här självstudien får du lära dig hur du registrerar ett enda sid program (SPA) i Azure Active Directory B2C att använda Azure Portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d2eaf1dce432821dcfc693dc69dcf975a3d8be8d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92503869"
---
# <a name="tutorial-register-a-single-page-application-spa-in-azure-active-directory-b2c"></a>Självstudie: registrera ett program med en sida (SPA) i Azure Active Directory B2C

Innan dina [program](application-types.md) kan interagera med Azure Active Directory B2C (Azure AD B2C) måste de registreras i en klient som du hanterar. Den här självstudien visar hur du registrerar ett enda webb program ("SPA") med hjälp av Azure Portal.

## <a name="overview-of-authentication-options"></a>Översikt över autentiseringsalternativ

Många moderna webb program skapas som program på klient sidan ("SPAs"). Utvecklare skriver dem med hjälp av Java Script eller ett SPA-ramverk, till exempel vinkel, Vue och reagera. Dessa program körs i en webbläsare och har olika egenskaper för autentisering än traditionella webb program på Server sidan.

Azure AD B2C innehåller **två** alternativ för att aktivera program med en enda sida för att logga in användare och hämta token för att få åtkomst till backend-tjänster eller webb-API: er:

### <a name="authorization-code-flow-with-pkce"></a>Flöde för auktoriseringskod (med PKCE)
- [OAuth 2,0 Authorization Code Flow (med PKCE)](./authorization-code-flow.md). Med auktoriseringskod-flödet kan programmet byta auktoriseringskod för **ID-** token för att representera den autentiserade **användare och de åtkomsttoken som** krävs för att anropa skyddade API: er. Dessutom returneras **uppdaterings** -token som ger långsiktig åtkomst till resurser för användare utan att det krävs någon interaktion med dessa användare. 

Detta är den **rekommenderade** metoden. Om du har en token med begränsad livs längd kan ditt program anpassa sig till [moderna webbläsare cookies sekretess begränsningar](../active-directory/develop/reference-third-party-cookies-spas.md), till exempel Safari ITP.

För att dra nytta av det här flödet kan programmet använda ett autentiseringspaket som stöder det, t. ex. [MSAL.js 2. x](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa). 

![Program med en enda sida – auth](./media/tutorial-single-page-app/spa-app-auth.svg)

### <a name="implicit-grant-flow"></a>Implicit beviljande flöde
- [OAuth 2,0 implicit flöde](implicit-flow-single-page-application.md). Vissa ramverk, t. ex. [MSAL.js 1. x](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp), stöder bara det implicita tilldelnings flödet. Med det implicita tilldelnings flödet kan programmet Hämta **ID-** **och åtkomsttoken** . Till skillnad från flödet för auktoriseringskod returnerar inte det implicita tilldelnings flödet en **uppdateringstoken**. 

![Program med en sida – implicit](./media/tutorial-single-page-app/spa-app.svg)

Det här autentiseringsschemat omfattar inte program scenarier som använder plattforms oberoende JavaScript-ramverk som Electron och reagerar-Native. De här scenarierna kräver ytterligare funktioner för interaktion med de ursprungliga plattformarna.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Om du inte redan har skapat din egen [Azure AD B2C-klient](tutorial-create-tenant.md)skapar du en nu. Du kan använda en befintlig Azure AD B2C klient.

## <a name="register-the-spa-application"></a>Registrera SPA-programmet

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Välj **Appregistreringar** och välj sedan **ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *spaapp1*.
1. Under **konto typer som stöds** väljer du **konton i valfri identitets leverantör eller organisations katalog (för autentisering av användare med användar flöden)**
1. Under **omdirigerings-URI** väljer du **en Enkels Ides applikation (Spa)** och anger sedan `https://jwt.ms` i text rutan URL.

    Omdirigerings-URI: n är den slut punkt som användaren skickas till av auktoriseringsservern (Azure AD B2C i det här fallet) när dess interaktion med användaren har slutförts och till vilken en åtkomsttoken eller auktoriseringskod skickas vid lyckad auktorisering. I ett produktions program är det vanligt vis en offentligt tillgänglig slut punkt där appen körs, t `https://contoso.com/auth-response` . ex.. För testnings ändamål som den här självstudien kan du ställa in den till `https://jwt.ms` , ett Microsoft-ägda webb program som visar det avkodade innehållet i en token (innehållet i token aldrig lämnar webbläsaren). Under utveckling av appar kan du lägga till slut punkten där ditt program lyssnar lokalt, t `http://localhost:5000` . ex.. Du kan när som helst lägga till och ändra omdirigerings-URI: er i dina registrerade program.

    Följande begränsningar gäller för omdirigerings-URI: er:

    * Svars-URL: en måste börja med schemat `https` , om du inte använder `localhost` .
    * Svars-URL: en är Skift läges känslig. Dess fall måste matcha fallet med URL-sökvägen till det program som körs. Om ditt program t. ex. ingår som en del av sökvägen `.../abc/response-oidc` ska du inte ange `.../ABC/response-oidc` i svars-URL: en. Eftersom webbläsaren behandlar sökvägar som Skift läges känsliga, kan cookies som är kopplade till `.../abc/response-oidc` uteslutas om de omdirigeras till den Skift läges fel matchnings bara `.../ABC/response-oidc` URL: en.

1. Under **behörigheter** markerar du kryss rutan *bevilja administratörs medgivande till OpenID och offline_access behörighet* .
1. Välj **Register** (Registrera).


## <a name="enable-the-implicit-flow"></a>Aktivera det implicita flödet
Om du använder det implicita flödet måste du aktivera det implicita tilldelnings flödet i appens registrering.

1. På den vänstra menyn, under **Hantera**, väljer du **autentisering**.
1. Under **implicit beviljande** väljer du kryss rutorna för **åtkomst-tokens** och **ID-token** .
1. Välj **Spara**.

## <a name="migrate-from-the-implicit-flow"></a>Migrera från det implicita flödet

Om du har ett befintligt program som använder det implicita flödet rekommenderar vi att du migrerar till att använda auktoriseringskod-flödet med hjälp av ett ramverk som stöder det, t. ex. [MSAL.js 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser).

När alla dina produktions program med en enda sida som representeras av en registrerad app-registrering använder auktoriseringskod-flödet, inaktiverar du de implicita inställningarna för flödes tilldelning. 

1. På den vänstra menyn, under **Hantera**, väljer du **autentisering**.
1. Under **implicit beviljande**, avmarkera båda kryss rutorna **åtkomsttoken** och ID- **token** .
1. Välj **Spara**.

Program som använder det implicita flödet kan fortsätta att fungera om du låter det implicita flödet vara aktiverat (markerat).

* * *

## <a name="next-steps"></a>Nästa steg

Nu ska du lära dig hur du skapar användar flöden för att göra det möjligt för användarna att registrera sig, logga in och hantera sina profiler.

> [!div class="nextstepaction"]
> [Skapa användar flöden i Azure Active Directory B2C >](tutorial-create-user-flows.md)
