---
title: 'Snabb start: lägga till användar inloggning till en Node.js-webbapp | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten får du lära dig hur du implementerar autentisering i ett Node.js-webbprogram med hjälp av OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, devx-track-js
ms.openlocfilehash: bd750a05f34a18a1260226fb979a82cc620dfbfb
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178287"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Snabb start: lägga till inloggning med OpenID Anslut till en Node.js webbapp

I den här snabb starten hämtar och kör du ett kod exempel som visar hur du konfigurerar OpenID Connect-autentisering i ett webb program som skapats med hjälp av Node.js med Express. Exemplet är utformat för att köras på vilken plattform som helst.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/en/download/).

## <a name="register-your-application"></a>Registrera ditt program

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
1. Sök efter och välj **Azure Active Directory**.
1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
1. Ange ett **namn** för programmet, till exempel `MyWebApp` . Användare av appen kan se det här namnet och du kan ändra det senare.
1. I avsnittet **konto typer som stöds** väljer du **konton i valfri organisations katalog och personliga Microsoft-konton (t. ex. Skype, Xbox, Outlook.com)**.

    Om det finns fler än en omdirigerings-URI måste du lägga till dem från fliken **autentisering** senare när appen har skapats.

1. Välj **Registrera** för att skapa appen.
1. På sidan **Översikt** för appen letar du reda på **programmets (klient) ID-** värde och registrerar det för senare. Du behöver det här värdet för att konfigurera programmet senare i det här projektet.
1. Under **Hantera** väljer du **autentisering**.
1. Välj **Lägg till en plattforms**  >  **webbplats**. 
1. I avsnittet **omdirigerings-URI** anger du `http://localhost:3000/auth/openid/return` .
1. Ange en **utloggnings-URL** `https://localhost:3000` .
1. I avsnittet implicit bidrag kontrollerar du **ID-tokens** som det här exemplet kräver att det [implicita tilldelnings flödet](./v2-oauth2-implicit-grant-flow.md) aktive ras för att logga in användaren.
1. Välj **Konfigurera**.
1. Under **Hantera** väljer du **certifikat & hemligheter**  >  **ny klient hemlighet**.
1. Ange en nyckel Beskrivning (för instansens program hemlighet).
1. Välj en nyckel varaktighet på **minst ett år, i två år** eller **upphör aldrig att gälla**.
1. Välj **Lägg till**. Nyckelvärdet kommer att visas. Kopiera nyckelvärdet och spara det på en säker plats för senare användning.


## <a name="download-the-sample-application-and-modules"></a>Ladda ned exempel programmet och moduler

Sedan ska du klona exemplet lagrings platsen och installera NPM-modulerna.

Från gränssnittet eller kommando raden:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

eller

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

Kör kommandot från projektets rot Katalog:

`$ npm install`

## <a name="configure-the-application"></a>Konfigurera programmet

Ange parametrarna i `exports.creds` config.js enligt anvisningarna.

* Uppdatera `<tenant_name>` i `exports.identityMetadata` med Azure AD-klient namnet för formatet \* . onmicrosoft.com.
* Uppdatera `exports.clientID` med det program-ID som du antecknade från App Registration.
* Uppdatera `exports.clientSecret` med program hemligheten noterad från App Registration.
* Uppdatera `exports.redirectUrl` med omdirigerings-URI: n noterad från App Registration.

**Valfri konfiguration för produktions program:**

* Uppdatera `exports.destroySessionUrl` i config.js om du vill använda en annan `post_logout_redirect_uri` .

* Ange `exports.useMongoDBSessionStore` i config.js till true, om du vill använda [mongoDB](https://www.mongodb.com) eller andra [kompatibla sessioner](https://github.com/expressjs/session#compatible-session-stores).
Standardlagringsplatsen i det här exemplet är `express-session` . Standardsessions-lagringsplatsen är inte lämplig för produktion.

* Uppdatera `exports.databaseUri` om du vill använda mongoDB och en annan databas-URI.

* Uppdatera `exports.mongoDBSessionMaxAge` . Här kan du ange hur länge du vill behålla en session i mongoDB. Enheten är sekund (er).

## <a name="build-and-run-the-application"></a>Skapa och kör appen

Starta mongoDB-tjänsten. Om du använder mongoDB-frågearkivet i den här appen måste du [Installera mongoDB](http://www.mongodb.org/) och starta tjänsten först. Om du använder standard arkivet kan du hoppa över det här steget.

Kör appen med följande kommando från kommando raden.

```
$ node app.js
```

**Är servern utmatnings svår att förstå?:** Vi använder `bunyan` för att logga i det här exemplet. Konsolen är inte särskilt begriplig för dig om du inte även installerar Bunyan och kör servern som ovan, men som rör den via Bunyan-binärfilen:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>Du är klar!

Du kommer att ha en server igång `http://localhost:3000` .

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg
Läs mer om webb program scenariot som Microsoft Identity Platform stöder:
> [!div class="nextstepaction"]
> [Webbapp som loggar in i användar scenario](scenario-web-app-sign-user-overview.md)