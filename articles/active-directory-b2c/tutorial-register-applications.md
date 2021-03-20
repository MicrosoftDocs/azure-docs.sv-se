---
title: 'Självstudie: registrera ett program'
titleSuffix: Azure AD B2C
description: Följ den här självstudien för att lära dig hur du registrerar ett webb program i Azure Active Directory B2C att använda Azure Portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2ec67669edeb52af1044c97c984eb6ba36fd1a0b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579645"
---
# <a name="tutorial-register-a-web-application-in-azure-active-directory-b2c"></a>Självstudie: registrera ett webb program i Azure Active Directory B2C

Innan dina [program](application-types.md) kan interagera med Azure Active Directory B2C (Azure AD B2C) måste de registreras i en klient som du hanterar. Den här självstudien visar hur du registrerar ett webb program med hjälp av Azure Portal. 

Ett "webb program" syftar på ett traditionellt webb program som utför merparten av program logiken på servern. De kan skapas med hjälp av ramverk som ASP.NET Core, maven (Java), kolv (python) och Express (Node.js).

> [!IMPORTANT]
> Om du använder ett enda webb program ("SPA") i stället (t. ex. genom att använda vinkel, Vue eller reagera), lär du dig [hur du registrerar ett program](tutorial-register-spa.md)med en sida.
> 
> Om du använder en inbyggd app i stället (t. ex. iOS, Android, mobil & Desktop), lär du dig [hur du registrerar ett internt klient program](add-native-application.md).

## <a name="prerequisites"></a>Krav
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Om du inte redan har skapat din egen [Azure AD B2C-klient](tutorial-create-tenant.md)skapar du en nu. Du kan använda en befintlig Azure AD B2C klient.

## <a name="register-a-web-application"></a>Registrera ett webbprogram

Om du vill registrera ett webb program i din Azure AD B2C klient kan du använda vår nya enhetliga **Appregistreringar** upplevelse eller äldre  **program (bakåtkompatibelt)** . [Läs mer om den nya upplevelsen](./app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[Appregistreringar](#tab/app-reg-ga/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Välj **Appregistreringar** och välj sedan **ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *webapp1*.
1. Under **konto typer som stöds** väljer du **konton i valfri identitets leverantör eller organisations katalog (för autentisering av användare med användar flöden)**.
1. Under **omdirigerings-URI** väljer du **webb** och anger sedan `https://jwt.ms` i text rutan URL.

    Omdirigerings-URI: n är den slut punkt som användaren skickas till av auktoriseringsservern (Azure AD B2C i det här fallet) när dess interaktion med användaren har slutförts och till vilken en åtkomsttoken eller auktoriseringskod skickas vid lyckad auktorisering. I ett produktions program är det vanligt vis en offentligt tillgänglig slut punkt där appen körs, t `https://contoso.com/auth-response` . ex.. För testnings ändamål som den här självstudien kan du ställa in den till `https://jwt.ms` , ett Microsoft-ägda webb program som visar det avkodade innehållet i en token (innehållet i token aldrig lämnar webbläsaren). Under utveckling av appar kan du lägga till slut punkten där ditt program lyssnar lokalt, t `https://localhost:5000` . ex.. Du kan när som helst lägga till och ändra omdirigerings-URI: er i dina registrerade program.

    Följande begränsningar gäller för omdirigerings-URI: er:

    * Svars-URL: en måste börja med schemat `https` .
    * Svars-URL: en är Skift läges känslig. Dess fall måste matcha fallet med URL-sökvägen till det program som körs. Om ditt program t. ex. ingår som en del av sökvägen `.../abc/response-oidc` ska du inte ange `.../ABC/response-oidc` i svars-URL: en. Eftersom webbläsaren behandlar sökvägar som Skift läges känsliga, kan cookies som är kopplade till `.../abc/response-oidc` uteslutas om de omdirigeras till den Skift läges fel matchnings bara `.../ABC/response-oidc` URL: en.

1. Under **behörigheter** markerar du kryss rutan *bevilja administratörs medgivande till OpenID och offline_access behörighet* .
1. Välj **Register** (Registrera).

#### <a name="applications-legacy"></a>[Program (bakåtkompatibelt)](#tab/applications-legacy/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Välj **program (bakåtkompatibelt)** och välj sedan **Lägg till**.
1. Ange ett namn på programmet. Till exempel *webapp1*.
1. För **Inkludera webb program/webb-API** väljer du **Ja**.
1. För **Svars-URL** anger du en slutpunkt dit Azure AD B2C ska returnera de token som programmet begär. Du kan till exempel ange att den ska lyssna lokalt på `http://localhost:5000` . Du kan när som helst lägga till och ändra omdirigerings-URI: er i dina registrerade program.

    Följande begränsningar gäller för omdirigerings-URI: er:

    * Svars-URL: en måste börja med schemat `https` , om du inte använder `localhost` .
    * Svars-URL: en är Skift läges känslig. Dess fall måste matcha fallet med URL-sökvägen till det program som körs. Om ditt program t. ex. ingår som en del av sökvägen `.../abc/response-oidc` ska du inte ange `.../ABC/response-oidc` i svars-URL: en. Eftersom webbläsaren behandlar sökvägar som Skift läges känsliga, kan cookies som är kopplade till `.../abc/response-oidc` uteslutas om de omdirigeras till den Skift läges fel matchnings bara `.../ABC/response-oidc` URL: en.

1. Välj **skapa** för att slutföra program registreringen.

* * *

## <a name="create-a-client-secret"></a>Skapa en klient hemlighet

För ett webb program måste du skapa en program hemlighet. Klient hemligheten kallas även för ett *program lösen ord*. Hemligheten kommer att användas av ditt program för att byta ut en auktoriseringskod för en åtkomsttoken.

#### <a name="app-registrations"></a>[Appregistreringar](#tab/app-reg-ga/)

1. På sidan **Azure AD B2C-Appregistreringar** väljer du det program som du skapade, till exempel *webapp1*.
1. På den vänstra menyn, under **Hantera**, väljer du **certifikat & hemligheter**.
1. Välj **Ny klienthemlighet**.
1. Ange en beskrivning av klient hemligheten i rutan **Beskrivning** . Till exempel *clientsecret1*.
1. Under **upphör ande** väljer du en varaktighet för vilken hemligheten är giltig och väljer sedan **Lägg till**.
1. Registrera hemlighetens **värde** för användning i klient program koden. Detta hemliga värde visas aldrig igen när du lämnar den här sidan. Du använder det här värdet som program hemlighet i programmets kod.

#### <a name="applications-legacy"></a>[Program (bakåtkompatibelt)](#tab/applications-legacy/)

1. På sidan **Azure AD B2C-program** väljer du det program som du skapade, till exempel *webapp1*.
1. Välj **nycklar** och välj sedan **generera nyckel**.
1. Välj **Spara** för att Visa nyckeln. Anteckna **appnyckel**-värdet. Du använder det här värdet som program hemlighet i programmets kod.

* * *

> [!NOTE]
> Av säkerhets synpunkt kan du regelbundet återställa program hemligheten eller omedelbart i nödfall. Alla program som integreras med Azure AD B2C bör förberedas för att hantera en hemlig överrullning-händelse, oavsett hur ofta den kan inträffa. Du kan ange två program hemligheter, så att ditt program kan fortsätta att använda den gamla hemligheten under en program hemlighets rotations händelse. Upprepa stegen i det här avsnittet om du vill lägga till en annan klient hemlighet. 

## <a name="enable-id-token-implicit-grant"></a>Aktivera ID-token implicit beviljande

Den implicita tilldelningens definitions egenskaper är att tokens, till exempel ID och åtkomsttoken, returneras direkt från Azure AD B2C till programmet. För webbappar, till exempel ASP.NET Core webbappar och [https://jwt.ms](https://jwt.ms) , som begär en ID-token direkt från slut punkten för auktorisering, aktiverar du det implicita tilldelnings flödet i appens registrering.

1. På den vänstra menyn, under **Hantera**, väljer du **autentisering**.
1. Under implicit beviljande väljer du kryss rutorna för **åtkomst-tokens** och **ID-token** .
1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Registrera ett webbprogram
> * Skapa en klient hemlighet

Nu ska du lära dig hur du skapar användar flöden för att göra det möjligt för användarna att registrera sig, logga in och hantera sina profiler.

> [!div class="nextstepaction"]
> [Skapa användar flöden i Azure Active Directory B2C >](tutorial-create-user-flows.md)
