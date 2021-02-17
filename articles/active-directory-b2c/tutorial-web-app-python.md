---
title: 'Självstudie: aktivera autentisering i ett python-webbprogram'
titleSuffix: Azure AD B2C
description: I den här självstudien får du lära dig hur du använder Azure Active Directory B2C för att tillhandahålla användar inloggning för ett webb program för python-flaskor.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 06/12/2020
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.custom: devx-track-python
ms.openlocfilehash: 56e92a698446bd417c74820d6da662ad9ee55c77
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555680"
---
# <a name="tutorial-enable-authentication-in-a-python-web-application-with-azure-ad-b2c"></a>Självstudie: aktivera autentisering i ett python-webbprogram med Azure AD B2C

Den här självstudien visar hur du använder Azure Active Directory B2C (Azure AD B2C) för att registrera och logga in användare i en python-flaska.

I de här självstudierna har du

> [!div class="checklist"]
> * Lägg till en svars-URL i ett program som är registrerat i Azure AD B2C klient organisationen
> * Hämta ett kod exempel från GitHub
> * Ändra exempel programmets kod så att den fungerar med din klient
> * Registrera dig med ditt användar flöde för registrering/inloggning

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande Azure AD B2C resurser på plats innan du fortsätter med stegen i den här självstudien:

* [Azure AD B2C klient](tutorial-create-tenant.md)
* [Programmet är registrerat](tutorial-register-applications.md) i din klient och dess *program (klient) ID* och *klient hemlighet*
* [Användar flöden som skapats](tutorial-create-user-flows.md) i din klient organisation

Dessutom behöver du följande i din lokala utvecklings miljö:

* [Visual Studio Code](https://code.visualstudio.com/) eller en annan kod redigerare
* [Python](https://nodejs.org/en/download/) 2.7 + eller 3 +

## <a name="add-a-redirect-uri"></a>Lägg till en omdirigerings-URI

I den andra själv studie kursen som du avslutade som en del av förutsättningarna registrerade du ett webb program i Azure AD B2C. Om du vill aktivera kommunikation med kod exemplet i den här självstudien lägger du till en svars-URL (kallas även en omdirigerings-URI) till program registreringen.

Om du vill uppdatera ett program i din Azure AD B2C klient kan du använda vår nya enhetliga **Appregistreringar** upplevelse eller äldre  **program (äldre)** . [Läs mer om den nya upplevelsen](./app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[Appregistreringar](#tab/app-reg-ga/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **Appregistreringar**, Välj fliken **ägda program** och välj sedan *webapp1* -programmet.
1. Under **Hantera** väljer du **autentisering**.
1. Under **webb** väljer du länken **Lägg till URI** och anger sedan `http://localhost:5000/getAToken` i text rutan.
1. Välj **Spara**.

#### <a name="applications-legacy"></a>[Program (bakåtkompatibelt)](#tab/applications-legacy/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Azure AD B2C**.
1. Välj **program (bakåtkompatibelt)** och välj sedan *webapp1* -programmet.
1. Under **Svars-URL** lägger du till `http://localhost:5000/getAToken`.
1. Välj **Spara**.
* * *

## <a name="get-the-sample-code"></a>Hämta exempelkoden

I den här självstudien konfigurerar du ett kod exempel som du hämtar från GitHub för att arbeta med din B2C-klient. Exemplet visar hur ett webb program för python-flaskor kan använda Azure AD B2C för användar registrering och inloggning.

[Hämta en. ZIP-arkiv](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip) eller klona [kod exempel lagrings platsen](https://github.com/Azure-Samples/ms-identity-python-webapp) från GitHub.

```console
git clone https://github.com/Azure-Samples/ms-identity-python-webapp.git
```

## <a name="update-the-sample"></a>Uppdatera exemplet

När du har fått exemplet konfigurerar du programmet så att det använder din Azure AD B2C klient, program registrering och användar flöden.

I projektets rot Katalog:

1. Byt namn på filen *app_config. py* till *app_config. py. Old*
1. Byt namn på *app_config_b2c. py* till *app_config. py*

Uppdatera det nya namnet *app_config. py* med värden för din Azure AD B2C klient-och program registrering som du skapade som en del av förutsättningarna.

1. Öppna filen *app_config. py* i redigeraren.
1. Uppdatera `b2c_tenant` värdet med namnet på din Azure AD B2C-klient, till exempel *contosob2c*.
1. Uppdatera vart och ett av `*_user_flow` värdena för att matcha namnen på de användar flöden som du skapade som en del av förutsättningarna.
1. Uppdatera `CLIENT_ID` värdet med **program-ID: t (klient)** för det webb program som du registrerade som en del av förutsättningarna.
1. Uppdatera `CLIENT_SECRET` värdet med värdet för den **klient hemlighet** som du skapade i kraven. För ökad säkerhet kan du överväga att lagra den i stället i en **miljö variabel** som rekommenderas i kommentarerna.

Det översta avsnittet i *app_config. py* bör nu se ut ungefär som i följande kodfragment:

```python
import os

b2c_tenant = "contosob2c"
signupsignin_user_flow = "B2C_1_signupsignin1"
editprofile_user_flow = "B2C_1_profileediting1"
resetpassword_user_flow = "B2C_1_passwordreset1"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "22222222-2222-2222-2222-222222222222" # Placeholder - for use ONLY during testing.
# In a production app, we recommend you use a more secure method of storing your secret,
# like Azure Key Vault. Or, use an environment variable as described in Flask's documentation:
# https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
# CLIENT_SECRET = os.getenv("CLIENT_SECRET")
# if not CLIENT_SECRET:
#     raise ValueError("Need to define CLIENT_SECRET environment variable")
```

> [!WARNING]
> Vi rekommenderar att du **inte lagrar hemligheter i klartext** i din program kod. Variabeln hårdkodad används i kod exemplet för *endast bekvämlighet*. Överväg att använda en miljö variabel eller ett hemligt arkiv som Azure Key Vault.

## <a name="run-the-sample"></a>Kör exemplet

1. I konsolen eller terminalen växlar du till den katalog som innehåller exemplet. Exempel:

    ```console
    cd ms-identity-python-webapp
    ```
1. Kör följande kommandon för att installera de nödvändiga paketen från PyPi och kör webb programmet på den lokala datorn:

    ```console
    pip install -r requirements.txt
    flask run --host localhost --port 5000
    ```

    Konsol fönstret visar port numret för programmet som körs lokalt:

    ```console
     * Serving Flask app "app" (lazy loading)
     * Environment: production
       WARNING: This is a development server. Do not use it in a production deployment.
       Use a production WSGI server instead.
     * Debug mode: off
     * Running on http://localhost:5000/ (Press CTRL+C to quit)
    ```

1. Bläddra till `http://localhost:5000` för att Visa webb programmet som körs på den lokala datorn.

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-01.png" alt-text="Webbläsare som visar webb programmet python-flaskan körs lokalt":::

### <a name="sign-up-using-an-email-address"></a>Registrera sig med en e-postadress

Det här exempel programmet stöder registrering, inloggning och lösen ords återställning. I den här självstudien får du registrera dig med en e-postadress.

1. Välj **Logga** in för att starta det *B2C_1_signupsignin1* användar flöde du angav i ett tidigare steg.
1. Azure AD B2C visar en inloggnings sida som innehåller en registrerings länk. Eftersom du ännu inte har ett konto väljer du länken **Registrera dig nu** .
1. Arbets flödet för registrering visar en sida där du kan samla in och verifiera användarens identitet med hjälp av en e-postadress. Arbets flödet för registrering samlar även in användarens lösen ord och de begärda attribut som definierats i användar flödet.

    Använd en giltig e-postadress och verifiera med verifieringskoden. Ställ in ett lösenord. Ange värden för de begärda attributen.

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-02.png" alt-text="Sidan registrera visas i Azure AD B2C användar flöde":::

1. Välj **skapa** för att skapa ett lokalt konto i katalogen Azure AD B2C.

När du väljer **skapa**, visar programmet namnet på den inloggade användaren.

:::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-03.png" alt-text="Webbläsare som visar webb programmet python flaska med inloggad användare":::

Om du vill testa inloggningen väljer du länken **Logga ut** och väljer sedan **Logga in** och loggar in med den e-postadress och det lösen ord som du angav när du registrerade dig.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du konfigurerat ett python-webbprogram så att det fungerar med ett användar flöde i din Azure AD B2C klient för att ge dig möjlighet att registrera och logga in. Du har slutfört de här stegen:

> [!div class="checklist"]
> * En svars-URL lades till i ett program som är registrerat i Azure AD B2C klient organisationen
> * Ett kod exempel har hämtats från GitHub
> * Ändrade exempel programmets kod så att den fungerar med din klient
> * Registrerat med ditt användar flöde för registrering/inloggning

Nu ska du lära dig hur du anpassar användar flödes sidor som visas för dina användare genom att Azure AD B2C:

> [!div class="nextstepaction"]
> [Anpassa gränssnittet för användar upplevelser i Azure AD B2C >](customize-ui.md)