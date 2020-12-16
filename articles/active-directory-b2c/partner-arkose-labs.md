---
title: Arkose Labs med Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Lär dig hur du integrerar Azure AD B2C-autentisering med Arkose Labs för att skydda dig mot bot-attacker, konto upptagnings attacker och falska konto öppningar.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2c7eea87101a36edb0d77026489ea351b601158b
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584603"
---
# <a name="tutorial-for-configuring-arkose-labs-with-azure-active-directory-b2c"></a>Självstudie för att konfigurera Arkose Labs med Azure Active Directory B2C

I den här självstudien lär du dig att integrera Azure AD B2C-autentisering med Arkose Labs. Arkose Labs hjälper organisationer mot bot-attacker, konto upptagnings attacker och falska konto öppningar.  

## <a name="prerequisites"></a>Krav

För att komma igång behöver du:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* [En Azure AD B2C klient](tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I följande diagram beskrivs hur Arkose Labs integreras med Azure AD B2C.

![Arkitektur diagram för Arkose Labs](media/partner-arkose-labs/arkose-architecture-diagram.png)

| Steg  | Beskrivning |
|---|---|
|1     | En användare loggar in med ett konto som skapats tidigare. När användaren väljer Skicka, visas en tvingande Arkose Labs-utmaning. När användaren har slutfört utmaningen skickas statusen till Arkose Labs för att generera en token.        |
|2     |  Arkose Labs skickar tillbaka token till Azure AD B2C.       |
|3     |  Innan inloggnings formuläret skickas skickas token till Arkose Labs för verifiering.       |
|4     |  Arkose skickar tillbaka ett lyckat eller misslyckat resultat från utmaningen.       |
|5     |  Om utmaningen har slutförts skickas ett inloggnings formulär till Azure AD B2C och Azure AD B2C Slutför autentiseringen.       |
|   |   |

## <a name="onboard-with-arkose-labs"></a>Publicera med Arkose Labs

1. Börja med att kontakta [Arkose Labs](https://www.arkoselabs.com/book-a-demo/) och skapa ett konto.

2. När ditt konto har skapats går du till https://dashboard.arkoselabs.com/login .

3. I instrument panelen navigerar du till plats inställningar för att hitta din offentliga nyckel och privata nyckel. Den här informationen kommer att behövas senare för att konfigurera Azure AD B2C.

## <a name="integrate-with-azure-ad-b2c"></a>Integrera med Azure AD B2C

### <a name="part-1--create-blob-storage-to-store-the-custom-html"></a>Del 1 – Skapa Blob Storage för att lagra den anpassade HTML-koden

Följ dessa steg om du vill skapa ett lagrings konto:  

1. Logga in på Azure Portal.

2. Se till att använda den katalog som innehåller din Azure-prenumeration. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller din prenumeration. Den här katalogen skiljer sig åt från den som innehåller din Azure B2C-klient.

3. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj  **lagrings konton**.

4. Välj **Lägg till**.

5. Under  **resurs grupp** väljer du  **Skapa ny**, anger ett namn för den nya resurs gruppen och väljer sedan **OK**.

6. Ange ett namn för lagringskontot. Namnet du väljer måste vara unikt för Azure, mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.

7. Välj platsen för lagrings kontot eller acceptera standard platsen.

8. Acceptera alla andra standardvärden, Välj   **Granska & skapa**  >  **skapa**.

9. När lagrings kontot har skapats väljer  **du gå till resurs**.

#### <a name="create-a-container"></a>Skapa en container

1. På sidan Översikt för lagrings kontot väljer du  **blobbar**.

2. Välj   **behållare**, ange ett namn för behållaren, Välj   **BLOB** (endast anonym Läs åtkomst för blobbar) och välj sedan **OK**.

#### <a name="enable-cross-origin-resource-sharing-cors"></a>Aktivera resurs delning mellan ursprung (CORS)

Azure AD B2C kod i en webbläsare använder en modern och standard metod för att läsa in anpassat innehåll från en URL som du anger i ett användar flöde. CORS gör att begränsade resurser på en webb sida kan begäras från andra domäner.

1. I menyn väljer du  **CORS**.

2. För  **tillåtna ursprung** anger du  `https://your-tenant-name.b2clogin.com` . Ersätt ditt-Tenant-namn med namnet på din Azure AD B2C-klient. Till exempel,  `https://fabrikam.b2clogin.com`. Använd små bokstäver när du anger ditt klient namn.

3. För  **tillåtna metoder** väljer du  **Hämta**, **Placera** och  **alternativ**.

4. För **tillåtna huvuden** anger du en asterisk (*).

5. För **exponerade rubriker** anger du en asterisk (*).

6. Ange 200 för **högsta ålder**.

   ![Arkose Labs-registrering och inloggning](media/partner-arkose-labs/signup-signin-arkose.png)

7. Välj **Spara**.

### <a name="part-2--set-up-a-back-end-server"></a>Del 2 – Konfigurera en backend-server

Hämta git-bash och följ stegen nedan:

1. Följ anvisningarna för att [skapa en webbapp](../app-service/quickstart-php.md)tills meddelandet "Grattis!Du har distribuerat din första PHP-app till App Service "visar.

2. Öppna din lokala mapp och Byt namn på filen **index. php** till **verify-token. php**.

3. Öppna filen verify-token. php med det nya namnet och:

   a. Ersätt innehållet med innehållet från filen verify-token. php som finns i [GitHub-lagringsplatsen](https://github.com/ArkoseLabs/Azure-AD-B2C).

   b. Ersätt <private_key> på rad 3 med din privata nyckel från Arkose Labs-instrumentpanelen.

4. I det lokala terminalfönstret sparar du ändringarna i git och skickar sedan kod ändringarna till Azure genom att skriva följande i git bash:

   ``git commit -am "updated output"``

   ``git push azure main``  

### <a name="part-3---final-setup"></a>Del 3 – slutlig installation

#### <a name="store-the-custom-html"></a>Lagra den anpassade HTML-koden

1. Öppna index.html-filen som lagras i [GitHub-lagringsplatsen](https://github.com/ArkoseLabs/Azure-AD-B2C).

2. Ersätt alla instanser av `<tenantname>` med ditt B2C-klient namn (med andra ord `<tenantname>.b2clogin.com` ). Det måste finnas fyra instanser.

3. Ersätt `<appname>` med namnet på appen som du skapade i del 2, steg 1.

   ![Skärm bild som visar Arkose Labs Azure CLI](media/partner-arkose-labs/arkose-azure-cli.png)

4. Ersätt `<public_key>` på rad 64 med den offentliga nyckel du fick från Arkose Labs-instrumentpanelen.

5. Ladda upp index.html-filen till blob-lagringen som skapats ovan.

6. Gå till **lagrings**  >  **container**  >  **uppladdning**.

#### <a name="set-up-azure-ad-b2c"></a>Konfigurera Azure AD B2C

> [!NOTE]
> Om du inte redan har en, [skapar du en Azure AD B2C klient](tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.

1. Skapa ett användar flöde baserat på informationen [här](tutorial-create-user-flows.md). Stoppa när du når avsnittet **testa användar flödet**.

2. Aktivera Java Script i ditt [användar flöde](javascript-and-page-layout.md).

3. På sidan samma användar flöde aktiverar du anpassad sid-URL: gå till sidan layout för **användar flöde**  >    >  **Använd anpassat sid innehåll**  =  **Ja**  >  **Infoga anpassad sid-URL**.
Den här anpassade sid-URL: en hämtas från platsen för index.html-filen i blob-lagringen  

   ![Skärm bild som visar Arkose Labs Storage-URL](media/partner-arkose-labs/arkose-storage-url.png)

## <a name="test-the-user-flow"></a>Testa användar flödet

1. Öppna Azure AD B2C klient organisation och välj **användar flöden** under **principer**.

2. Välj ditt tidigare skapade användar flöde.

3. Välj **Kör användar flöde** och välj inställningarna:

   a. **Program** – Välj den registrerade appen (EXEMPLET är JWT).

   b. **Svars-URL** – Välj omdirigerings-URL: en.

   c. Välj **Kör användar flöde**.

4. Gå igenom registrerings flödet och skapa ett konto.

5. Logga ut.

6. Gå igenom inloggnings flödet.

7. Ett Arkose Labs-pussel visas när du har valt **Fortsätt**.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Anpassade principer i Azure AD B2C](custom-policy-overview.md)

- [Kom igång med anpassade principer i Azure AD B2C](custom-policy-get-started.md?tabs=applications)