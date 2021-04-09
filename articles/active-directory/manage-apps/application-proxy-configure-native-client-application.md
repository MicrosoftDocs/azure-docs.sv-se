---
title: Publicera inbyggda klient program – Azure AD | Microsoft Docs
description: Beskriver hur du aktiverar inbyggda klient program för att kommunicera med Azure AD-programproxy Connector för att ge säker fjärråtkomst till dina lokala appar.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/12/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e830b52accefa0bcb76cc3434cae2fe94eed97db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258670"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Så här aktiverar du interna klient program för att interagera med proxyprogram

Du kan använda Azure Active Directory (Azure AD) Application Proxy för att publicera webbappar, men det kan också användas för att publicera interna klient program som är konfigurerade med Microsoft Authentication Library (MSAL). Interna klient program skiljer sig från webbappar eftersom de är installerade på en enhet, medan webb program nås via en webbläsare.

För att stödja interna klient program, accepterar programproxy Azure AD-utfärdade tokens som skickas i huvudet. Application Proxy-tjänsten utför autentiseringen för användarna. Den här lösningen använder inte Application-token för autentisering.

![Förhållandet mellan slutanvändare, Azure AD och publicerade program](./media/application-proxy-configure-native-client-application/richclientflow.png)

Om du vill publicera interna program använder du Microsoft Authentication Library, som tar hand om autentiseringen och stöder många klient miljöer. Programproxyn passar i den [Skriv bords app som anropar ett webb-API för ett inloggat användar](../develop/authentication-flows-app-scenarios.md#desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user) scenario.

Den här artikeln vägleder dig genom de fyra stegen för att publicera ett internt program med programproxy och Azure AD-autentiseringspaket.

## <a name="step-1-publish-your-proxy-application"></a>Steg 1: publicera ditt proxy-program

Publicera ditt proxy-program på samma sätt som andra program och tilldela användare åtkomst till ditt program. Mer information finns i [Publicera program med Application Proxy](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>Steg 2: registrera ditt egna program

Du måste nu registrera ditt program i Azure AD, enligt följande:

1. Logga in på [Azure Active Directory Portal](https://aad.portal.azure.com/). **Instrument panelen** för **Azure Active Directory administrations Center** visas.
1. I sid panelen väljer du **Azure Active Directory**. Sidan **Azure Active Directory** översikt visas.
1. I översikts panelen för Azure AD väljer du **Appregistreringar**. Listan över alla app-registreringar visas.
1. Välj **ny registrering**. Sidan **Registrera ett program** visas.

   ![Skapa en ny app-registrering i Azure Portal](./media/application-proxy-configure-native-client-application/create.png)

1. Ange ett visnings namn för ditt program i **namn** rubriken.
1. Under rubriken **konto typer som stöds** väljer du en åtkomst nivå med hjälp av följande rikt linjer:

   - Om du bara vill rikta dig till konton som är interna för din organisation väljer du **konton i den här organisations katalogen**.
   - Om du bara vill rikta in dig på affärs-eller utbildnings kunder väljer du **konton i valfri organisations katalog**.
   - Om du vill rikta in dig på den bredaste uppsättningen Microsoft-identiteter väljer du **konton i alla organisations kataloger och personliga Microsoft-konton**.
1. Under **omdirigerings-URI** väljer du **offentlig klient (mobilt & Desktop)** och anger sedan omdirigerings-URI `https://login.microsoftonline.com/common/oauth2/nativeclient` för programmet.
1. Välj och Läs **Microsofts plattforms principer** och välj sedan **Registrera**. En översikts sida för den nya program registreringen skapas och visas.

Mer detaljerad information om hur du skapar en ny program registrering finns i [integrera program med Azure Active Directory](../develop/quickstart-register-app.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>Steg 3: bevilja åtkomst till ditt proxy-program

Nu när du har registrerat ditt interna program kan du ge det åtkomst till andra program i katalogen, i det här fallet för att få åtkomst till proxy-programmet. Så här aktiverar du det interna programmet som ska exponeras för proxy-programmet:

1. Välj **API-behörigheter** i list rutan på sidan ny program registrering. Sidan **API-behörigheter** för den nya program registreringen visas.
1. Välj **Lägg till en behörighet**. Sidan **begär API-behörigheter** visas.
1. Under **Välj en API** -inställning väljer du de **API: er som min organisation använder**. En lista visas med de program i din katalog som exponerar API: er.
1. Skriv i sökrutan eller rulla för att hitta det proxy-program som du publicerade i [steg 1: publicera ditt proxy-program](#step-1-publish-your-proxy-application)och välj sedan proxy-programmet.
1. I listan **vilken typ av behörigheter kräver ditt program?** rubrik väljer du behörighets typ. Om det interna programmet behöver åtkomst till proxyprogram-API: et som den inloggade användaren väljer du **delegerade behörigheter**.
1. I rubriken **Välj behörigheter** väljer du önskad behörighet och väljer **Lägg till behörigheter**. Sidan **API-behörigheter** för ditt ursprungliga program visar nu proxy-programmet och behörighets-API: et som du har lagt till.

## <a name="step-4-add-the-microsoft-authentication-library-to-your-code-net-c-sample"></a>Steg 4: Lägg till Microsoft Authentication Library till din kod (.NET C#-exempel)

Redigera den interna program koden i MSAL (Authentication Library) för att inkludera följande text: 

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

Du hittar den information som krävs i exempel koden i Azure AD-portalen. gör så här:

| Information krävs | Så här hittar du det i Azure AD-portalen |
| --- | --- |
| \<Tenant ID> | **Azure Active Directory**  >  **Egenskaper**  >  **Katalog-ID** |
| \<App ID of the Native app> | **Program registrering**  >  *ditt ursprungliga program*  >  **Översikt**  >  **Program-ID** |
| \<Scope> | **Program registrering**  >  *ditt ursprungliga program*  >  **API-behörigheter** > Klicka på behörighets-API: t (user_impersonation) > en panel med texten **user_impersonation** visas till höger. > området är URL: en i redigerings rutan.
| \<Proxy App Url> | den externa URL: en och sökvägen till API: et

När du redigerar MSAL-koden med dessa parametrar kan användarna autentisera till interna klient program även när de är utanför företags nätverket.

## <a name="next-steps"></a>Nästa steg

Mer information om det inbyggda program flödet finns i [interna appar i Azure Active Directory](../azuread-dev/native-app.md).

Lär dig mer om [att konfigurera enkel inloggning till program i Azure Active Directory](sso-options.md#choosing-a-single-sign-on-method).