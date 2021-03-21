---
title: 'Snabb start: lägga till inloggning med Microsoft i en python-webbapp | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten får du lära dig hur en python-webbapp kan logga in användare, hämta en åtkomsttoken från Microsoft Identity Platform och anropa Microsoft Graph API.
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev, devx-track-python, scenarios:getting-started, languages:Python
ms.openlocfilehash: fb38140f09fc7c1eb2c40fc02e8c113cbc6f94a0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100103523"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Snabb start: lägga till inloggning med Microsoft i en python-webbapp

I den här snabb starten laddar du ned och kör ett kod exempel som visar hur ett python-webbprogram kan logga in användare och få en åtkomsttoken för att anropa Microsoft Graph-API: et. Användare med ett personligt Microsoft-konto eller ett konto i en Azure Active Directory (Azure AD) organisation kan logga in i programmet.

Se [hur exemplet fungerar](#how-the-sample-works) för en illustration.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7 +](https://www.python.org/downloads/release/python-2713) eller [python 3 +](https://www.python.org/downloads/release/python-364/)
- [Kolv](http://flask.pocoo.org/), [kolv](https://pypi.org/project/Flask-Session/), [förfrågningar](https://requests.kennethreitz.org/en/master/)
- [MSAL python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
>
> Det finns två alternativ för att starta ditt snabb starts program: Express (alternativ 1) och manuell (alternativ 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Gå till snabb starts upplevelsen för <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonQuickstartPage/sourceType/docs" target="_blank">Azure Portal-Appregistreringar</a> .
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
>
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure-portalen</a>.
> 1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
> 1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
> 1. Ange ett **namn** för programmet, till exempel `python-webapp` . Användare av appen kan se det här namnet och du kan ändra det senare.
> 1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
> 1. Välj **Register** (Registrera).
> 1. På sidan **Översikt över** appar noterar du **programmets (klient) ID-** värde för senare användning.
> 1. Under **Hantera** väljer du **autentisering**.
> 1. Välj **Lägg till en plattforms**  >  **webbplats**.
> 1. Lägg till `http://localhost:5000/getAToken` som **omdirigerings-URI**.
> 1. Välj **Konfigurera**.
> 1. Under **Hantera** väljer du **certifikat & hemligheter**  och i avsnittet **klient hemligheter** väljer du **ny klient hemlighet**.
> 1. Ange en nyckel Beskrivning (till exempel appens hemlighet), lämna standard förfallo datum och välj **Lägg till**.
> 1. Notera **värdet** för **klient hemligheten** för senare användning.
> 1. Under **Hantera** väljer du **API-behörigheter**  >  **Lägg till en behörighet**.
> 1. Se till att fliken **Microsoft API: er** är markerad.
> 1. I avsnittet *vanliga API: er för Microsoft* väljer du **Microsoft Graph**.
> 1. I avsnittet **delegerade behörigheter** kontrollerar du att rätt behörigheter är markerade: **User. ReadBasic. all**. Använd Sök fältet om det behövs.
> 1. Välj knappen **Lägg till behörigheter** .
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
>
> Kod exemplet i den här snabb starten fungerar:
>
> 1. Lägg till en svars-URL som `http://localhost:5000/getAToken` .
> 1. Skapa en klient hemlighet.
> 1. Lägg till Microsoft Graph API: s User. ReadBasic. all delegerad behörighet.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-aspnet-webapp/green-check.png) appen konfigureras med det här attributet

#### <a name="step-2-download-your-project"></a>Steg 2: Ladda ned ditt projekt
> [!div renderon="docs"]
> [Ladda ned kod exemplet](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Hämta projektet och extrahera zip-filen till en lokal mapp närmare rotmappen – till exempel **C:\Azure-samples**
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Ladda ned kod exemplet](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-application"></a>Steg 3: konfigurera programmet
>
> 1. Extrahera zip-filen i en lokal mapp närmare rotkatalogen, till exempel **C:\Azure-Samples**
> 1. Om du använder en Integrated Development Environment öppnar du exemplet i din favorit IDE (valfritt).
> 1. Öppna filen **app_config. py** , som du hittar i rotmappen och Ersätt med följande kodfragment:
>
> ```python
> CLIENT_ID = "Enter_the_Application_Id_here"
> CLIENT_SECRET = "Enter_the_Client_Secret_Here"
> AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
> ```
> Plats:
>
> - `Enter_the_Application_Id_here` – är program-Id för programmet som du har registrerat.
> - `Enter_the_Client_Secret_Here` – är den **klient hemlighet** som du skapade i **certifikat & hemligheter**  för det program som du har registrerat.
> - `Enter_the_Tenant_Name_Here` -är **katalog-ID-** värdet för det program som du har registrerat.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Steg 3: kör kod exemplet

> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Steg 4: kör kod exemplet

1. Du måste installera MSAL python Library, kolv Framework Flask-Sessions för hantering av Server sidan och begär Anden med hjälp av pip på följande sätt:

    ```Shell
    pip install -r requirements.txt
    ```

2. Kör app.py från Shell eller kommando rad:

    ```Shell
    python app.py
    ```
   > [!IMPORTANT]
   > Det här snabbstartsprogrammet använder en klienthemlighet för att identifiera sig som en konfidentiell klient. Eftersom klienthemligheten läggs till som oformaterad text till dina projektfiler rekommenderar vi att du av säkerhetsskäl använder ett certifikat i stället för en klienthemlighet innan programmet används som produktionsprogram. Mer information om hur du använder ett certifikat finns i [följande instruktioner](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Mer information

### <a name="how-the-sample-works"></a>Så här fungerar exemplet
![Visar hur exempel appen som genereras av den här snabb starten fungerar](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>Hämtar MSAL
MSAL är det bibliotek som används för att logga in användare och begära token som används för att få åtkomst till ett API som skyddas av Microsoft Identity Platform.
Du kan lägga till MSAL python i ditt program med hjälp av pip.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>MSAL-initiering
Du kan lägga till referensen i MSAL python genom att lägga till följande kod överst i filen där du kommer att använda MSAL:

```Python
import msal
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om webbappar som loggar in användare i vår scenario serie med flera delar.

> [!div class="nextstepaction"]
> [Scenario: webb program som loggar in användare](scenario-web-app-sign-user-overview.md)
