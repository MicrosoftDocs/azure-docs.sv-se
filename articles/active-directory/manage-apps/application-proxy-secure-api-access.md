---
title: 'Få åtkomst till lokala API: er med Azure AD-programproxy'
description: 'Azure Active Directoryens programproxy tillåter inbyggda appar säker åtkomst till API: er och affärs logik som du är värd för lokalt eller på virtuella datorer i molnet.'
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/12/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 9341646f32f6a2e05397b072d3f63186964fbd88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258990"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Säker åtkomst till lokala API: er med Azure AD-programproxy

Du kan ha affärs logik-API: er som körs lokalt eller som finns på virtuella datorer i molnet. Dina inbyggda Android-, iOS-, Mac-eller Windows-appar behöver interagera med API-slutpunkterna för att använda data eller tillhandahålla användar interaktion. Med Azure AD-programproxy och [Microsoft Authentication Library (MSAL)](../azuread-dev/active-directory-authentication-libraries.md) kan dina interna appar få säker åtkomst till dina lokala API: er. Azure Active Directory-programproxy är en snabbare och säkrare lösning än att öppna brand Väggs portar och kontrol lera autentisering och auktorisering på App-lagret.

Den här artikeln beskriver hur du konfigurerar en Azure AD-programproxy-lösning som är värd för en webb-API-tjänst som interna appar kan komma åt.

## <a name="overview"></a>Översikt

Följande diagram visar ett traditionellt sätt att publicera lokala API: er. Den här metoden kräver att du öppnar inkommande portar 80 och 443.

![Traditionell API-åtkomst](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

Följande diagram visar hur du kan använda Azure-AD-programproxy för att publicera API: er på ett säkert sätt utan att öppna några inkommande portar:

![Azure AD-programproxy API-åtkomst](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Azure AD-programproxy bildar lösningens stamnät, arbetar som en offentlig slut punkt för API-åtkomst och tillhandahåller autentisering och auktorisering. Du kan komma åt dina API: er från en mängd olika plattformar med hjälp av [MSAL-biblioteken (Microsoft Authentication Library)](../azuread-dev/active-directory-authentication-libraries.md) .

Eftersom Azure AD-programproxy-autentisering och-auktorisering bygger på Azure AD kan du använda villkorlig åtkomst för Azure AD för att se till att endast betrodda enheter kan komma åt API: er som publicerats via programproxy. Använd Azure AD Join eller Azure AD hybrid anslutet för Station ära datorer och Intune Hanterat för enheter. Du kan också dra nytta av Azure Active Directory Premium funktioner som Azure AD Multi-Factor Authentication och datorn inlärnings skydd för [Azure Identity Protection](../identity-protection/overview-identity-protection.md).

## <a name="prerequisites"></a>Förutsättningar

För att följa den här genom gången behöver du:

- Administratörs åtkomst till en Azure-katalog med ett konto som kan skapa och registrera appar
- Exempel webb-API och interna klient program från [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp)

## <a name="publish-the-api-through-application-proxy"></a>Publicera API: t via Application Proxy

Om du vill publicera ett API utanför intranätet via Application Proxy följer du samma mönster som för att publicera webbappar. Mer information finns i [Självstudier: Lägg till ett lokalt program för fjärråtkomst via Application Proxy i Azure Active Directory](application-proxy-add-on-premises-application.md).

Så här publicerar du SecretAPI Web API via Application Proxy:

1. Bygg och publicera SecretAPI-projektet som en ASP.NET-webbapp på din lokala dator eller intranät. Se till att du har åtkomst till webbappen lokalt.

1. Välj **Azure Active Directory** i [Azure-portalen](https://portal.azure.com). Välj sedan **företags program**.

1. Överst på sidan **företags program – alla program** väljer du **nytt program**.

1. På sidan **Lägg till ett program** väljer du **lokala program**. Sidan **Lägg till ett eget lokalt program** visas.

1. Om du inte har någon Application Proxy-anslutning installerad uppmanas du att installera den. Välj **Hämta Application Proxy Connector** för att ladda ned och installera anslutningen.

1. När du har installerat Application Proxy Connector går du till sidan **Lägg till en egen lokal app** :

   1. Bredvid **namn**, anger du *SecretAPI*.

   1. Bredvid **intern URL** anger du den URL som du använder för att få åtkomst till API: et inifrån intranätet.

   1. Se till att **förautentisering** har angetts till **Azure Active Directory**.

   1. Välj **Lägg till** överst på sidan och vänta tills appen har skapats.

   ![Lägg till API-app](./media/application-proxy-secure-api-access/3-add-api-app.png)

1. På sidan **företags program – alla program** väljer du appen **SecretAPI** .

1. På sidan **SecretAPI-Overview** väljer du **Egenskaper** i det vänstra navigerings fältet.

1. Du vill inte att API: er ska vara tillgängliga för slutanvändare på panelen mina **appar** , så Ställ in **Visible för användare** till **Nej** längst ned på sidan **Egenskaper** och välj sedan **Spara**.

   ![Inte synlig för användare](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)

Du har publicerat ditt webb-API via Azure AD-programproxy. Lägg nu till användare som har åtkomst till appen.

1. På sidan **SecretAPI-översikt** väljer **du användare och grupper** i det vänstra navigerings fältet.

1. På sidan **användare och grupper** väljer du **Lägg till användare**.

1. På sidan **Lägg till tilldelning** väljer **du användare och grupper**.

1. På sidan **användare och grupper** söker du efter och väljer användare som har åtkomst till appen, inklusive minst själv. När du har valt alla användare väljer du **Välj**.

   ![Välj och tilldela användare](./media/application-proxy-secure-api-access/7-select-admin-user.png)

1. Tillbaka på sidan **Lägg till tilldelning** väljer du **tilldela**.

> [!NOTE]
> API: er som använder integrerad Windows-autentisering kan kräva [ytterligare steg](./application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Registrera den inbyggda appen och bevilja åtkomst till API: et

Inbyggda appar är program som har utvecklats för att användas på en viss plattform eller enhet. Innan din inbyggda app kan ansluta till och komma åt ett API måste du registrera den i Azure AD. Följande steg visar hur du registrerar en inbyggd app och ger den åtkomst till webb-API: t som du har publicerat via programproxyn.

Registrera den inbyggda AppProxyNativeAppSample-appen:

1. På sidan Azure Active Directory **Översikt** väljer du **Appregistreringar**. överst i fönstret **Appregistreringar** väljer du **ny registrering**.

1. På sidan **Registrera ett program** :

   1. Under **namn** anger du *AppProxyNativeAppSample*.

   1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).

   1. Under **omdirigerings-URL**, nedrullningsbar listruta och välj **offentlig klient (mobilt & Desktop)** och ange sedan *https://login.microsoftonline.com/common/oauth2/nativeclient* .

   1. Välj **register** och vänta tills appen har registrerats.

      ![Ny programregistrering](./media/application-proxy-secure-api-access/8-create-reg-ga.png)

Nu har du registrerat AppProxyNativeAppSample-appen i Azure Active Directory. Så här ger du din inbyggda app åtkomst till SecretAPI webb-API:

1. På   >  sidan **registrerings program** för Azure Active Directory översikt väljer du appen **AppProxyNativeAppSample** .

1. På sidan **AppProxyNativeAppSample** väljer du **API-behörigheter** i det vänstra navigerings fältet.

1. På sidan **API-behörigheter** väljer du **Lägg till en behörighet**.

1. På sidan första **API-behörighet för begäran** väljer du fliken **API: er min organisation använder** och söker sedan efter och väljer **SecretAPI**.

1. På sidan nästa **begär ande-API-behörighet** markerar du kryss rutan bredvid **user_impersonation** och väljer sedan **Lägg till behörigheter**.

    ![Välj ett API](./media/application-proxy-secure-api-access/10-secretapi-added.png)

1. På sidan **API-behörigheter** kan du välja **bevilja administrativt godkännande för Contoso** för att förhindra att andra användare behöver ett individuellt medgivande till appen.

## <a name="configure-the-native-app-code"></a>Konfigurera koden för den inbyggda appen

Det sista steget är att konfigurera den inbyggda appen. Följande fragment från *Form1. cs* -filen i NativeClient-exempel appen gör att MSAL-biblioteket hämtar token för att begära API-anropet och kopplar det som innehavare till app-huvudet.

   ```
   // Acquire Access Token from AAD for Proxy Application
 IPublicClientApplication clientApp = PublicClientApplicationBuilder
.Create(<App ID of the Native app>)
.WithDefaultRedirectUri() // will automatically use the default Uri for native app
.WithAuthority("https://login.microsoftonline.com/{<Tenant ID>}")
.Build();

AuthenticationResult authResult = null;
var accounts = await clientApp.GetAccountsAsync();
IAccount account = accounts.FirstOrDefault();

IEnumerable<string> scopes = new string[] {"<Scope>"};

try
 {
    authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
 }
    catch (MsalUiRequiredException ex)
 {
     authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();                
 }
 
if (authResult != null)
 {
  //Use the Access Token to access the Proxy Application
  
  HttpClient httpClient = new HttpClient();
  HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
  HttpResponseMessage response = await httpClient.GetAsync("<Proxy App Url>");
 }
```

Om du vill konfigurera den interna appen så att den ansluter till Azure Active Directory och anropa API app proxy, uppdaterar du plats hållarnas värden i *App.config* -filen för NativeClient-exempel appen med värden från Azure AD:

- Klistra in **-ID: t för katalogen (klient)** i `<add key="ida:Tenant" value="" />` fältet. Du kan hitta och kopiera det här värdet (ett GUID) från sidan **Översikt** i någon av dina appar.

- Klistra in **klient-ID: t (AppProxyNativeAppSample Application)** i `<add key="ida:ClientId" value="" />` fältet. Du kan hitta och kopiera det här värdet (ett GUID) från **översikts** sidan för AppProxyNativeAppSample.

- Klistra in **omdirigerings-URI** för AppProxyNativeAppSample i `<add key="ida:RedirectUri" value="" />` fältet. Du kan hitta och kopiera det här värdet (en URI) från sidan AppProxyNativeAppSample- **autentisering** .

- Klistra in SecretAPI **program-ID-URI** i `<add key="todo:TodoListResourceId" value="" />` fältet. Du kan hitta och kopiera det här värdet (en URI) från SecretAPI **exponera en API** -sida.

- Klistra in **URL: en för SecretAPI start sida** i `<add key="todo:TodoListBaseAddress" value="" />` fältet. Du kan hitta och kopiera det här värdet (en URL) från sidan för SecretAPI- **anpassning** .

När du har konfigurerat parametrarna skapar och kör du den inbyggda appen. När du väljer knappen **Logga in** kan du använda appen för att logga in och sedan Visa skärmen lyckades för att bekräfta att den har anslutits till SecretAPI.

![Skärm bild som visar en meddelande hemlighet A P Jag lyckades och en OK-knapp.](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Lägg till ett lokalt program för fjärråtkomst via Application Proxy i Azure Active Directory](application-proxy-add-on-premises-application.md)
- [Snabb start: Konfigurera ett klient program för åtkomst till webb-API: er](../develop/quickstart-configure-app-access-web-apis.md)
- [Så här aktiverar du interna klient program för att interagera med proxyprogram](application-proxy-configure-native-client-application.md)