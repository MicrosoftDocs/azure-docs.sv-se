---
title: 'Snabb start: anropa ett ASP.NET-webb-API som skyddas av Microsoft Identity Platform | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten lär du dig att anropa ett ASP.NET webb-API som skyddas av Microsoft Identity Platform från ett Windows Desktop-program (WPF).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: ec8fd05c0661178cc07b9165793c9f34f2463948
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98754323"
---
# <a name="quickstart-call-an-aspnet-web-api-thats-protected-by-microsoft-identity-platform"></a>Snabb start: anropa ett ASP.NET webb-API som skyddas av Microsoft Identity Platform

I den här snabb starten laddar du ned och kör ett kod exempel som visar hur du skyddar ett ASP.NET webb-API genom att begränsa åtkomsten till dess resurser till endast behöriga konton. Exemplet stöder auktorisering av personliga Microsoft-konton och konton i valfri Azure Active Directory (Azure AD)-organisation.

I artikeln används också en Windows Presentation Foundation-app (WPF) för att visa hur du kan begära en åtkomsttoken för att få åtkomst till ett webb-API.

## <a name="prerequisites"></a>Krav

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Visual Studio 2017 eller 2019. Hämta [Visual Studio kostnads fritt](https://www.visualstudio.com/downloads/).

## <a name="clone-or-download-the-sample"></a>Klona eller hämta exemplet

Du kan hämta exemplet på något av två sätt:

* Klona den från ditt gränssnitt eller kommando raden:
   ```console
   git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
   ```
* [Ladda ned den som en zip-fil](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

## <a name="register-your-web-api"></a>Registrera ditt webb-API

I det här avsnittet registrerar du ditt webb-API i **Appregistreringar** i Azure Portal.

### <a name="choose-your-azure-ad-tenant"></a>Välj din Azure AD-klient

Om du vill registrera dina appar manuellt väljer du den Azure Active Directory (Azure AD) som du vill skapa dina appar för.

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. Om ditt konto finns i fler än en Azure AD-klient väljer du din profil längst upp till höger och väljer sedan **Växla katalog**.
1. Ändra din portal-session till den Azure AD-klient som du vill använda.

### <a name="register-the-todolistservice-app"></a>Registrera TodoListService-appen

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
1. Sök efter och välj **Azure Active Directory**.
1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
1. Ange ett **namn** för programmet, till exempel `AppModelv2-NativeClient-DotNet-TodoListService` . Användare av appen kan se det här namnet och du kan ändra det senare.
1. För **konto typer som stöds** väljer du **konton i valfri organisations katalog**.
1. Välj **Registrera** för att skapa programmet.
1. På sidan **Översikt över** appar letar du efter **programmets (klient) ID-** värde och registrerar det sedan för senare användning. Du behöver den för att konfigurera Visual Studio-konfigurationsfilen för det här projektet (det vill säga `ClientId` i *TodoListService\Web.config* -filen).
1. Under **Hantera** väljer du **exponera ett API**  >  **Lägg till ett omfång**. Godkänn den föreslagna program-ID-URI: n ( `api://{clientId}` ) genom att välja **Spara och fortsätt** och ange följande information:

    1. Ange i **omfångs namn** `access_as_user` .
    1. För **vem som kan** godkänna, se till att alternativet **Administratörer och användare** är markerat.
    1. I rutan **visnings namn för administratörs godkännande** anger du `Access TodoListService as a user` .
    1. I rutan **Beskrivning av administratörs godkännande** anger du `Accesses the TodoListService web API as a user` .
    1. I rutan **användar medgivande visnings namn** anger du `Access TodoListService as a user` .
    1. I rutan **Beskrivning av användar medgivande** anger du `Accesses the TodoListService web API as a user` .
    1. Behåll **aktiverat** för **tillstånd**.
1. Välj **Lägg till omfattning**.

### <a name="configure-the-service-project"></a>Konfigurera tjänste projektet

Konfigurera tjänste projektet så att det matchar det registrerade webb-API: et genom att göra följande:

1. Öppna lösningen i Visual Studio och öppna *Web.config* -filen under roten för TodoListService-projektet.

1. Ersätt värdet för `ida:ClientId` parametern med klient-ID: t (program-ID) från det program som du precis registrerade i **Appregistreringar** portalen.

### <a name="add-the-new-scope-to-the-appconfig-file"></a>Lägg till det nya omfånget i app.config-filen

Om du vill lägga till det nya omfånget i TodoListClient *app.config* -filen gör du följande:

1. I TodoListClient-projektets rotmapp öppnar du *app.config* -filen.

1. Klistra in program-ID: t från programmet som du precis registrerade för ditt TodoListService `TodoListServiceScope` -projekt i parametern och Ersätt `{Enter the Application ID of your TodoListService from the app registration portal}` strängen.

  > [!NOTE]
  > Kontrol lera att program-ID: t använder följande format: `api://{TodoListService-Application-ID}/access_as_user` (där `{TodoListService-Application-ID}` är det GUID som representerar program-ID: t för din TodoListService-app).

## <a name="register-the-todolistclient-client-app"></a>Registrera klient programmet för TodoListClient

I det här avsnittet registrerar du TodoListClient-appen i **Appregistreringar** i Azure Portal och konfigurerar sedan koden i TodoListClient-projektet. Om klienten och servern anses vara *samma program* kan du återanvända programmet som är registrerat i steg 2. Använd samma program om du vill att användarna ska logga in med ett personligt Microsoft-konto.

### <a name="register-the-app"></a>Registrera appen

Registrera TodoListClient-appen genom att göra följande:

1. Gå till Microsoft Identity Platform för utvecklare [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) -portalen.
1. Välj **ny registrering**.
1. När **sidan Registrera ett program** öppnas anger du programmets registrerings information:

    1. I avsnittet **namn** anger du ett meningsfullt program namn som ska visas för användarna av appen (till exempel **NativeClient-dotNet-TodoListClient**).
    1. För **konto typer som stöds** väljer du **konton i valfri organisations katalog**.
    1. Välj **Registrera** för att skapa programmet.

   > [!NOTE]
   > Standardvärdet för  `ida:Tenant` är inställt på i TodoListClient Projectapp.config-filen `common` . Möjliga värden är:
   > - `common`: Du kan logga in med ett arbets-eller skol konto eller ett personligt Microsoft-konto (eftersom du har valt **konton i valfri organisations katalog** i steg 3b).
   > - `organizations`: Du kan logga in med ett arbets-eller skol konto.
   > - `consumers`: Du kan bara logga in med ett personligt Microsoft-konto.

1. På sidan **Översikt över** app väljer du **autentisering** och gör sedan följande:

    1. Under **plattforms konfiguration** väljer du knappen **Lägg till en plattform** .
    1. För **mobil-och skriv bords program** väljer du **mobil-och skriv bords program**.
    1. Markera kryss rutan för **omdirigering av URI: er** **https://login.microsoftonline.com/common/oauth2/nativeclient** .
    1. Välj **Konfigurera**.

1. Välj **API-behörigheter** och gör sedan följande:

    1. Välj knappen **Lägg till en behörighet**.
    1. Välj fliken **Mina API: er** .
    1. I listan över API: er väljer du **AppModelv2-NativeClient-dotNet-TODOLISTSERVICE API** eller det namn som du angav för webb-API: et.
    1. Markera kryss rutan **access_as_user** behörighet om den inte redan är markerad. Använd Sök fältet om det behövs.
    1. Välj knappen **Lägg till behörigheter** .

### <a name="configure-your-project"></a>Konfigurera projektet

Gör så här för att konfigurera ditt TodoListClient-projekt:

1. På sidan **Översikt** i **Appregistreringar** Portal kopierar du värdet för **program-ID: t (klient)**.

1. Från rotmappen för TodoListClient-projektet öppnar du *app.config* -filen och klistrar in program-ID-värdet i `ida:ClientId` parametern.

## <a name="run-your-todolistclient-project"></a>Kör ditt TodoListClient-projekt

Gör så här för att köra TodoListClient-projektet:

1. Tryck på F5 för att öppna TodoListClient-projektet. Projekt sidan bör öppnas.

1. Längst upp till höger väljer du **Logga** in och loggar sedan in med samma autentiseringsuppgifter som du använde för att registrera ditt program, eller logga in som en användare i samma katalog.

   Om du loggar in för första gången kan du uppmanas att godkänna TodoListService webb-API.

   För att hjälpa dig att komma åt TodoListService webb-API och ändra *att göra-* listan, begär inloggningen också en åtkomsttoken till *access_as_users* omfånget.

## <a name="pre-authorize-your-client-application"></a>För att auktorisera ditt klient program

Ett sätt som du kan tillåta att användare från andra kataloger har åtkomst till ditt webb-API är att förkonfigurera klient programmet för att få åtkomst till ditt webb-API. Du gör detta genom att lägga till program-ID: t från klient programmet i listan över förauktoriserade program för ditt webb-API. Genom att lägga till en förauktoriserad klient ger du användarna åtkomst till ditt webb-API utan att behöva ange medgivande. Gör följande för att auktorisera din klient app:

1. Öppna egenskaperna för din TodoListService-app i **Appregistreringar** -portalen.
1. I avsnittet **exponera ett API** , under **auktoriserade klient program**, väljer du **Lägg till ett klient program**.
1. I rutan **klient-ID** klistrar du in program-ID: t för TodoListClient-appen.
1. I avsnittet **auktoriserade omfattningar** väljer du omfattning för `api://<Application ID>/access_as_user` webb-API: et.
1. Välj **Lägg till program**.

### <a name="run-your-project"></a>Köra projektet

1. Tryck på F5 för att köra projektet. TodoListClient-appen bör öppnas.
1. I det övre högra hörnet väljer du **Logga** in och loggar sedan in med en personlig Microsoft-konto, till exempel live.com eller hotmail.com, eller ett arbets-eller skol konto.

## <a name="optional-limit-sign-in-access-to-certain-users"></a>Valfritt: begränsa inloggnings åtkomst till vissa användare

När du har följt stegen ovan kan alla personliga konton, t. ex. outlook.com eller live.com eller arbets-eller skol konton från organisationer som är integrerade med Azure AD, begära tokens och få åtkomst till ditt webb-API.

Använd något av följande alternativ för att ange vem som kan logga in på ditt program:

### <a name="option-1-limit-access-to-a-single-organization-single-tenant"></a>Alternativ 1: begränsa åtkomsten till en enskild organisation (enskild klient)

Du kan begränsa inloggnings åtkomsten till ditt program till användar konton som finns i en enda Azure AD-klient, inklusive *gäst konton* för den klienten. Det här scenariot är vanligt för *branschspecifika program*.

1. Öppna filen *App_Start \STARTUP.auth* och ändra värdet för metadata-slutpunkten som skickas till `OpenIdConnectSecurityTokenProvider` till `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` . Du kan också använda klient namnet, till exempel `contoso.onmicrosoft.com` .
2. I samma fil ställer du in `ValidIssuer` egenskapen på `TokenValidationParameters` till `"https://sts.windows.net/{Tenant ID}/"` och anger `ValidateIssuer` argumentet till `true` .

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Alternativ 2: Använd en anpassad metod för att validera utfärdare

Du kan implementera en anpassad metod för att verifiera utfärdare med hjälp av- `IssuerValidator` parametern. Mer information om den här parametern finns i [TokenValidationParameters-klass](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet&preserve-view=true).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg
Läs mer om det skyddade webb-API-scenariot som Microsoft Identity Platform stöder:
> [!div class="nextstepaction"]
> [Skyddat webb-API-scenario](scenario-protected-web-api-overview.md)
