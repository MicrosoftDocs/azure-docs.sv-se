---
title: 'Självstudie: bevilja åtkomst till ett Node.js webb-API från ett Skriv bords program'
description: Själv studie kurs om hur du använder Active Directory B2C för att skydda ett Node.js webb-API och anropa det från en .NET Desktop-app.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 12951f25feb6f3710b8d35fbc682caeeb480e788
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555615"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Självstudier: Bevilja åtkomst till ett Node.js-webb-API från en skrivbordsapp med Azure Active Directory B2C

Den här självstudien visar hur du anropar en Node.js webb-API som skyddas av Azure Active Directory B2C (Azure AD B2C) från en Windows Presentation Foundation (WPF) Desktop-app, som också skyddas av Azure AD B2C.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till ett program för webb-API
> * Konfigurera omfånget för ett webb-API
> * Ge behörigheter till webb-API:t
> * Uppdatera exemplet så att programmet används

## <a name="prerequisites"></a>Förutsättningar

Slutför stegen och kraven i [Självstudier: autentisera användare i en intern Skriv bords klient](tutorial-desktop-app.md).

## <a name="add-a-web-api-application"></a>Lägga till ett program för webb-API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurera omfång

Omfång är ett sätt att styra åtkomsten till skyddade resurser. Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Vissa användare kan till exempel ha både läs- och skrivåtkomst medan andra bara har skrivskyddad åtkomst. I den här självstudiekursen definierar du läs- och skrivrättigheter för webb-API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Registrera värdet under **omfattningar** för `demo.read` omfånget som ska användas i ett senare steg när du konfigurerar Skriv bords programmet. Det fullständiga värdet för omfattning liknar `https://contosob2c.onmicrosoft.com/api/demo.read` .

## <a name="grant-permissions"></a>Bevilja behörigheter

Om du vill anropa ett skyddat webb-API från ett internt klient program måste du ge de registrerade interna klient program behörigheterna till det webb-API som du registrerade i Azure AD B2C.

I den nödvändiga självstudien har du registrerat ett internt klient program med namnet *nativeapp1*. Följande steg konfigurerar den interna program registreringen med de API-omfattningar som du exponerade för *webapi1* i föregående avsnitt. Detta gör det möjligt för Skriv bords programmet att hämta en åtkomsttoken från Azure AD B2C att webb-API: et kan använda för att verifiera och ge begränsad åtkomst till resurser. Du konfigurerar och kör både desktop program och webb-API-kod exempel senare i självstudien.

Om du vill registrera ett program i din Azure AD B2C klient kan du använda vår nya enhetliga **Appregistreringar** upplevelse eller äldre  **program (äldre)** . [Läs mer om den nya upplevelsen](./app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[Appregistreringar](#tab/app-reg-ga/)

1. Välj **Appregistreringar** och välj sedan det interna klient program som ska ha åtkomst till API: et. Till exempel *nativeapp1*.
1. Under **Hantera**, Välj **API-behörigheter**.
1. Under **konfigurerade behörigheter** väljer du **Lägg till en behörighet**.
1. Välj fliken **Mina API: er** .
1. Välj det API som det interna klient programmet ska beviljas åtkomst till. Till exempel *webapi1*.
1. Under **behörighet** expanderar du **demo** och väljer sedan de omfattningar som du definierade tidigare. Till exempel *demo. Read* och *demo. Write*.
1. Välj **Lägg till behörigheter**. Vänta några minuter innan du fortsätter till nästa steg.
1. Välj **bevilja administrativt godkännande för (ditt klient namn)**.
1. Välj ditt inloggade administratörs konto eller logga in med ett konto i Azure AD B2C-klienten som har tilldelats minst administratörs rollen för *moln program* .
1. Välj **Acceptera**.
1. Välj **Uppdatera** och verifiera sedan att "beviljat..." visas under **status** för båda omfattningarna. Det kan ta några minuter innan behörigheterna har spridits.

#### <a name="applications-legacy"></a>[Program (bakåtkompatibelt)](#tab/applications-legacy/)

1. Välj **program (bakåtkompatibelt)** och välj sedan *nativeapp1*.
1. Välj **API-åtkomst** och därefter **Lägg till**.
1. I listrutan **Välj API** väljer du *webapi1*.
1. I list rutan **Välj omfång** väljer du de omfattningar som du definierade tidigare. Till exempel *demo. Read* och *demo. Write*.
1. Välj **OK**.

* * *

En användare autentiserar med Azure AD B2C för att använda WPF-skrivbordsappen. Skrivbordsappen får ett auktoriseringsbeviljande från Azure AD B2C som ger tillgång till det skyddade webb-API:t.

## <a name="configure-the-samples"></a>Konfigurera exemplen

Nu när webb-API: et är registrerat och du har konfigurerat scope och behörigheter, konfigurerar du Skriv bords program och webb-API-exempel för att använda din Azure AD B2C-klient.

### <a name="update-the-desktop-application"></a>Uppdatera Skriv bords programmet

I ett krav för den här artikeln ändrade du ett [WPF Desktop-program](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) för att aktivera inloggning med ett användar flöde i din Azure AD B2C klient. I det här avsnittet uppdaterar du samma program för att referera till webb-API: t som du registrerade tidigare, *webapi1*.

1. Öppna **Active-Directory-B2C-WPF-** lösningen ( `active-directory-b2c-wpf.sln` ) i Visual Studio.
1. Öppna *app.XAML.cs* -filen i **Active-Directory-B2C-WPF-** projektet och leta upp följande variabel definitioner.
    1. Ersätt värdet för `ApiScopes` variabeln med värdet du registrerade tidigare när du definierade **demonstrationen. Läs** omfattning.
    1. Ersätt värdet för `ApiEndpoint` variabeln med den **omdirigerings-URI** som du registrerade tidigare när du registrerade webb-API: t (till exempel *webapi1*) i din klient organisation.

    Här är ett exempel:

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>Hämta och uppdatera API-exemplet för Node.js

Sedan hämtar du Node.js Web API Code-exemplet från GitHub och konfigurerar det så att det använder det webb-API som du registrerade i Azure AD B2C-klienten.

[Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) eller klona exempelwebbappen från GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

Exempelwebb-API:et för Node.js använder Passport.js-biblioteket för att aktivera Azure AD B2C att skydda anrop till API: et.

1. Öppna filen `index.js`.
1. Uppdatera dessa variabel definitioner med följande värden. Ändra `<web-API-application-ID>` till **program-ID: t (klient)** för det webb-API som du registrerade tidigare (*webapi1*). Ändra `<your-b2c-tenant>` till namnet på din Azure AD B2C-klient.

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. Eftersom du kör API: et lokalt uppdaterar du sökvägen i vägen för GET-metoden till `/` i stället för demonstrations appens plats för `/hello` :

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>Kör exemplen

### <a name="run-the-nodejs-web-api"></a>Kör Node.js webb-API

1. Starta en kommandotolk för Node.js.
2. Gå till den katalog som innehåller Node.js-exemplet. Till exempel `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Kör följande kommandon:
    ```console
    npm install && npm update
    ```
    ```console
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Kör skrivbordsappen

1. Öppna lösningen **active-directory-b2c-wpf** i Visual Studio.
2. Tryck på **F5** för att köra skrivbordsappen.
3. Logga in med e-postadressen och lösenordet som skapades i [självstudien för autentisering av användare med Azure Active Directory B2C i en skrivbordsapp](tutorial-desktop-app.md).
4. Välj **anrops-API** -knappen.

Skriv bords programmet gör en begäran till det lokala webb-API: et, och vid verifiering av en giltig åtkomsttoken visas den inloggade användarens visnings namn.

![Visnings namn som visas i det övre fönstret i WPF Desktop-programmet](./media/tutorial-desktop-app-webapi/desktop-app-01-post-api-call.png)

Ditt Skriv bords program, som skyddas av Azure AD B2C, anropar det lokalt kör webb-API: t som också skyddas av Azure AD B2C.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Lägga till ett program för webb-API
> * Konfigurera omfånget för ett webb-API
> * Ge behörigheter till webb-API:t
> * Uppdatera exemplet så att programmet används

> [!div class="nextstepaction"]
> [Lägg till identitets leverantörer till dina program i Azure Active Directory B2C](add-identity-provider.md)