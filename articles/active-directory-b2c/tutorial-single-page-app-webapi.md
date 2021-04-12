---
title: 'Självstudie: skydda ett Node.js webb-API med Azure AD B2C och bevilja åtkomst till ett program med en enda sida (SPA)'
titleSuffix: Azure AD B2C
description: I den här självstudien får du lära dig hur du använder Active Directory B2C för att skydda ett Node.js webb-API och anropa det från ett enda webb program.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 737810a7d07d0d97b2e42acffa17fdd32986c48b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93421098"
---
# <a name="tutorial-protect-and-grant-access-to-a-nodejs-web-api-from-a-single-page-application-with-azure-ad-b2c"></a>Självstudie: skydda och bevilja åtkomst till ett Node.js webb-API från ett program med en sida med Azure AD B2C

Den här självstudien visar hur du anropar ett Azure Active Directory B2C (Azure AD B2C)-skyddat Node.js webb-API från ett program på en enda sida.

I den här självstudien finns den andra i en serie i två delar:

> [!div class="checklist"]
> * Skapa en webb-API-programregistrering i din Azure AD B2C klient
> * Konfigurera omfång för webb-API: et
> * Ge behörigheter till webb-API:t
> * Ändra ett webb-API-kod exempel för att arbeta med din klient

I den [första självstudien](tutorial-single-page-app.md) i den här serien laddade du ned kod exemplet och ändrade det för att logga in användare med ett användar flöde i din Azure AD B2C klient.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Slutför stegen och kraven i [Självstudier: aktivera autentisering i ett program med en sida med Azure AD B2C](tutorial-single-page-app.md)
* [Visual Studio Code](https://code.visualstudio.com/) eller en annan kod redigerare
* [Node.js](https://nodejs.org/en/download/)

## <a name="add-a-web-api-application"></a>Lägga till ett program för webb-API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurera omfång

Omfång är ett sätt att styra åtkomsten till skyddade resurser. Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Vissa användare kan till exempel ha både läs- och skrivåtkomst medan andra bara har skrivskyddad åtkomst. I den här självstudien definierar du både Läs-och Skriv behörighet för webb-API: et.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Registrera värdet under **omfattningar** för `demo.read` omfånget som ska användas i ett senare steg när du konfigurerar ett program med en sida. Det fullständiga värdet för omfattning liknar `https://contosob2c.onmicrosoft.com/api/demo.read` .

## <a name="grant-permissions"></a>Bevilja behörigheter

Om du vill anropa ett skyddat webb-API från ett annat program måste du ge den program behörighet till webb-API: et.

I den nödvändiga självstudien har du skapat ett program med en enda sida med namnet *spaapp1*. I den här självstudien konfigurerar du programmet för att anropa det webb-API som du skapade i ett tidigare avsnitt, *spaapp1*.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Webb programmet med en sida har nu beviljats behörigheter till det skyddade webb-API: et för de angivna omfattningarna. En användare autentiseras med Azure AD B2C för att använda ett program med en sida. En app med en enda sida får en åtkomsttoken från Azure AD B2C för åtkomst till det skyddade webb-API: et.

## <a name="configure-the-sample"></a>Konfigurera exemplet

Nu när webb-API: et är registrerat och du har definierat omfattningar, konfigurerar du webb-API-koden så att den fungerar med din Azure AD B2C-klient. I den här självstudien konfigurerar du ett exempel Node.js webb-API som du hämtar från GitHub.

[Hämta ett \* zip-arkiv](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) eller klona exempel webb-API-projektet från GitHub. Du kan också gå direkt till [Azure-samples/Active-Directory-B2C-JavaScript-NodeJS-WebAPI](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) -projektet på GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="configure-the-web-api"></a>Konfigurera webb-API

1. Öppna filen *config.js* i kod redigeraren.
1. Ändra variabel värden så att de motsvarar de program registreringar som du skapade tidigare. Uppdatera även `policyName` med det användar flöde som du skapade som en del av förutsättningarna. Till exempel *B2C_1_signupsignin1*.
    
    ```json
    "credentials": {
        "tenantName": "<your-tenant-name>",
        "clientID": "<your-webapi-application-ID>"
    },
    "policies": {
        "policyName": "B2C_1_signupsignin1"
    },
    "resource": {
        "scope": ["demo.read"] 
    },
    ```

#### <a name="enable-cors"></a>Aktivera CORS

Du måste aktivera [CORS](https://expressjs.com/en/resources/middleware/cors.html) i webb-API: et om du vill tillåta att ett program med en enda sida anropar Node.js webb-API. I ett produktions program bör du vara försiktig med vilken domän som gör begäran, men i den här självstudien tillåter du begär Anden från valfri domän.

Om du vill aktivera CORS använder du följande mellanprogram. I exemplet Node.js webb-API-kod i den här självstudien har den redan lagts till i *index.js* -filen.

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

### <a name="configure-the-single-page-application"></a>Konfigurera ett program med en sida

En Enkels Ides applikation (SPA) från [föregående självstudie](tutorial-single-page-app.md) i serien använder Azure AD B2C för användarens registrering och inloggning, och som standard anropar det Node.js webb-API som skyddas av *fabrikamb2c* demo-klienten.

I det här avsnittet uppdaterar du webb programmet på en sida för att anropa det Node.js webb-API som skyddas av *din* Azure AD B2C klient (och som du kör på den lokala datorn).

Ändra inställningarna i SPA:

1. I det [Active-Directory-B2C-JavaScript-msal-singlepageapp][github-js-spa] -projekt som du laddade ned eller klonade i föregående självstudie, öppnar du *apiConfig.js* -filen i mappen *JavaScriptSPA* .
1. Konfigurera exemplet med URI: n för *demonstrationen. Läs* omfattning som du skapade tidigare och URL: en för webb-API: et.
    1. I `apiConfig` definitionen ersätter du `b2cScopes` värdet med fullständig URI för *demonstrationen. Läs* omfattning (det **omfattnings** värde som du registrerade tidigare).
    1. Ändra domänen i `webApi` värdet till den omdirigerings-URI som du lade till när du registrerade webb-API-programmet i ett tidigare steg.

    Eftersom API: et är tillgängligt i `/hello` slut punkten lämnar du */Hello* i URI: n.

    `apiConfig`Definitionen bör likna följande kodblock, men med B2C-klientens namn i stället för `<your-tenant-name>` :

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/hello" // '/hello' should remain in the URI
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Köra SPA-och webb-API: et

Nu är du redo att testa programmets begränsade åtkomst till API: et. Kör både webb-API: et för Node.js och ett program med en enda sida i Java Script på den lokala datorn. Logga sedan in på den enda sidan och välj knappen **anropa API** för att initiera en begäran till det skyddade API: et.

Även om båda programmen körs lokalt när du följer den här själv studie kursen har du konfigurerat dem att använda Azure AD B2C för säker registrering/inloggning och för att bevilja åtkomst till det skyddade webb-API: et.

### <a name="run-the-nodejs-web-api"></a>Kör Node.js webb-API

1. Öppna ett konsol fönster och ändra till den katalog som innehåller Node.js Web API-exemplet. Exempel:

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. Kör följande kommandon:

    ```console
    npm install && npm update
    node index.js
    ```

    Konsol fönstret visar port numret där programmet finns.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>Köra appen med en sida

1. Öppna ett annat konsol fönster och ändra till den katalog som innehåller SPA-exemplet för Java Script. Exempel:

    ```console
    cd ms-identity-b2c-javascript-spa
    ```

1. Kör följande kommandon:

    ```console
    npm install && npm update
    npm start
    ```

    Konsolfönstret visar portnumret där programmet finns.

    ```console
    Listening on port 6420...
    ```

1. Navigera till `http://localhost:6420` i webbläsaren om du vill visa programmet.

    ![Exempel program för en enda sida som visas i webbläsare](./media/tutorial-single-page-app-webapi/tutorial-01-sample-app-browser.png)

1. Logga in med e-postadressen och lösen ordet som du använde i [föregående självstudie](tutorial-single-page-app.md). När inloggningen är klar bör du se `User 'Your Username' logged-in` meddelandet.
1. Välj **anrops-API** -knappen. SPA får ett bemyndigande från Azure AD B2C och ger sedan till gång till det skyddade webb-API: t för att visa namnet på den inloggade användaren:

    ![Ett program med en sida i webbläsaren som visar användar namn JSON-resultat som returnerades av API](./media/tutorial-single-page-app-webapi/tutorial-02-call-api.png)

## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

> [!div class="checklist"]
> * En webb-API-programregistrering har skapats i din Azure AD B2C klient
> * Konfigurerade omfång för webb-API: et
> * Beviljas behörigheter till webb-API: et
> * Ändrade ett webb-API-kod exempel för att fungera med din klient

Nu när du har sett en SPA-begäran en resurs från ett skyddat webb-API, får du en djupare förståelse för hur dessa program typer interagerar med varandra och med Azure AD B2C.

> [!div class="nextstepaction"]
> [Program typer som kan användas i Active Directory B2C >](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
