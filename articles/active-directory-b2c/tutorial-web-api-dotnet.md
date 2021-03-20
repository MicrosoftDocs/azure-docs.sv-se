---
title: 'Självstudie: bevilja åtkomst till ett ASP.NET webb-API'
titleSuffix: Azure AD B2C
description: Självstudie om hur du använder Active Directory B2C för att skydda ett ASP.NET webb-API och anropa det från en ASP.NET-webbapp.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: devx-track-csharp, mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 84bf0ddd194cd59a7e728c0c50dbe3cbad2afa4a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555645"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Självstudie: bevilja åtkomst till ett ASP.NET webb-API med hjälp av Azure Active Directory B2C

Den här självstudien visar hur du anropar en skyddad webb-API-resurs i Azure Active Directory B2C (Azure AD B2C) från ett ASP.NET-webbprogram.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till ett program för webb-API
> * Konfigurera omfånget för ett webb-API
> * Ge behörigheter till webb-API:t
> * Konfigurera exemplet för att använda programmet

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Slutför stegen och kraven i [själv studie kursen: aktivera autentisering i ett webb program med hjälp av Azure Active Directory B2C](tutorial-web-app-dotnet.md).

## <a name="add-a-web-api-application"></a>Lägga till ett program för webb-API

Webb-API-resurser måste vara registrerade i klientorganisationen innan de kan godkänna och svara på en begäran från en skyddad resurs från klientprogram som använder en åtkomsttoken.

Om du vill registrera ett program i din Azure AD B2C klient kan du använda vår nya enhetliga **Appregistreringar** upplevelse eller äldre  **program (äldre)** . [Läs mer om den nya upplevelsen](./app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[Appregistreringar](#tab/app-reg-ga/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **Appregistreringar** och välj sedan **ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *webapi1*.
1. Under **omdirigerings-URI** väljer du **webb** och anger sedan en slut punkt där Azure AD B2C ska returnera de token som programmet begär. I den här självstudien körs exemplet lokalt och lyssnar på `https://localhost:44332`.
1. Välj **Register** (Registrera).
1. Registrera **program-ID: t (Client)** för användning i ett senare steg.

#### <a name="applications-legacy"></a>[Program (bakåtkompatibelt)](#tab/applications-legacy/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **program (bakåtkompatibelt)** och välj sedan **Lägg till**.
5. Ange ett namn på programmet. Till exempel *webapi1*.
6. För **Inkludera webb program/webb-API** väljer du **Ja**.
7. För **Svars-URL** anger du en slutpunkt dit Azure AD B2C ska returnera de token som programmet begär. I den här självstudien körs exemplet lokalt och lyssnar på `https://localhost:44332`.
8. För **URI för app-ID** anger du den identifierare som används för webb-API:t. Den fullständiga URI-identifieraren inklusive domänen skapas åt dig. Till exempel `https://contosotenant.onmicrosoft.com/api`.
9. Klicka på **Skapa**.
10. På egenskapssidan antecknar du program-ID:t du kommer att använda när du konfigurerar webbappen.

* * *

## <a name="configure-scopes"></a>Konfigurera omfång

Omfång är ett sätt att styra åtkomsten till skyddade resurser. Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Till exempel kan användare av webb-API:et ha både läs- och skrivbehörighet, eller så har användare av webb-API:et kanske bara läsbehörighet. I den här självstudiekursen använder du omfång för att definiera läs- och skrivrättigheter för webb-API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Bevilja behörigheter

Om du vill anropa ett skyddat webb-API från ett program måste du ge programmet åtkomst till API:t. I den obligatoriska föregående självstudien skapade du en webbapp i Azure AD B2C med namnet *webapp1*. Du använder det här programmet till att anropa webb-API:t.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Programmet har registrerats för att anropa det skyddade webb-API:et. En användare autentiserar med Azure AD B2C för att använda programmet. Programmet får ett auktoriseringsbeviljande från Azure AD B2C som ger tillgång till det skyddade webb-API:et.

## <a name="configure-the-sample"></a>Konfigurera exemplet

När webb-API:et är registrerat och har ett definierat omfång konfigurerar du webb-API:et så det använder din Azure AD B2C-klientorganisation. I den här självstudiekursen konfigurerar du ett webb-API-exempel. Exempelwebb-API:et ingår i det projekt som du laddade ned i den obligatoriska föregående självstudien.

Exempellösningen innehåller två projekt:

* **TaskWebApp** – Skapa och redigera en uppgiftslista. Exemplet använder användarflödet för **registrering eller inloggning** för att registrera eller logga in användare.
* **TaskService** – Har stöd för att skapa, läsa, uppdatera och ta bort funktionen för uppgiftslistor. API:et skyddas av Azure AD B2C och anropas av TaskWebApp.

### <a name="configure-the-web-application"></a>Konfigurera webbappen

1. Öppna **B2C-WebAPI-DotNet**-lösningen i Visual Studio.
1. Öppna **Web.config** i projektet **TaskWebApp** .
1. Kör API:et lokalt genom att använda inställningen för lokala värden för **api:TaskServiceUrl**. Ändra Web.config enligt följande:

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

1. Konfigurera en URI för API:et. Det här är den URI som webbappen använder för att göra API-begäran. Ställ även in de behörigheter som krävs.

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

### <a name="configure-the-web-api"></a>Konfigurera webb-API

1. Öppna **Web.config** i projektet **TaskService** .
1. Ställ in API:t så det använder din klientorganisation.

    ```csharp
    <add key="ida:AadInstance" value="https://<Your tenant name>.b2clogin.com/{0}/{1}/v2.0/.well-known/openid-configuration" />
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

1. Ange klient-ID: t för program-ID: t för det registrerade webb-API-programmet, *webapi1*.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

1. Uppdatera användar flödes inställningen med namnet på ditt användar flöde för registrering och inloggning, *B2C_1_signupsignin1*.

    ```csharp
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_signupsignin1" />
    ```

1. Konfigurera omfattnings inställningen så att den matchar de som du skapade i portalen.

    ```csharp
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

## <a name="run-the-sample"></a>Kör exemplet

Du måste köra både **TaskWebApp**- och **TaskService**-projektet.

1. Högerklicka på lösningen i Solution Explorer och välj **Ange startprojekt...**.
1. Välj **Flera startprojekt**.
1. Ändra **Åtgärd** för båda projekten till **Start**.
1. Spara ändringarna genom att klicka på **OK**.
1. Tryck på **F5** för att köra båda programmen. Varje program öppnas i ett eget webbläsarfönster.
    * `https://localhost:44316/` är webb programmet.
    * `https://localhost:44332/` är webb-API:t.

1. I webb programmet väljer du **registrering/logga** in för att logga in på webb programmet. Använd det konto som du skapade tidigare.
1. När du har loggat in väljer **du att göra-lista** och skapar ett objekt för att göra-listan.

När du skapar en post i listan skickar webbappen en begäran till webb-API:et om att skapa listposten. Det skyddade webb programmet anropar webb-API: t som skyddas av Azure AD B2C.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Lägga till ett program för webb-API
> * Konfigurera omfånget för ett webb-API
> * Ge behörigheter till webb-API:t
> * Konfigurera exemplet för att använda programmet

> [!div class="nextstepaction"]
> [Lägg till identitets leverantörer till dina program i Azure Active Directory B2C](add-identity-provider.md)