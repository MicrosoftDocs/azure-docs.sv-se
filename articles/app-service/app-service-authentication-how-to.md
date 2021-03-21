---
title: Avancerad användning av authn/AuthZ
description: Lär dig att anpassa funktionen för autentisering och auktorisering i App Service för olika scenarier och hämta användar anspråk och olika tokens.
ms.topic: article
ms.date: 07/08/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 50587feff29e1c02a639d63d0c99156dcec4f68e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102180878"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Avancerad användning av autentisering och auktorisering i Azure App Service

Den här artikeln visar hur du anpassar den inbyggda [autentiseringen och auktoriseringen i App Service](overview-authentication-authorization.md)och hanterar identiteter från ditt program. 

För att komma igång snabbt, se någon av följande Självstudier:

* [Självstudie: Autentisera och auktorisera användare från slutpunkt till slutpunkt i Azure App Service](tutorial-auth-aad.md)
* [Så här konfigurerar du din app för att använda Azure Active Directory-inloggning](configure-authentication-provider-aad.md)
* [Så här konfigurerar du din app för att använda Facebook-inloggning](configure-authentication-provider-facebook.md)
* [Så här konfigurerar du din app för att använda Google-inloggning](configure-authentication-provider-google.md)
* [Så här konfigurerar du din app för att använda Microsoft-kontoinloggning](configure-authentication-provider-microsoft.md)
* [Så här konfigurerar du din app för att använda Twitter-inloggning](configure-authentication-provider-twitter.md)
* [Så här konfigurerar du din app för inloggning med hjälp av en OpenID Connect-Provider (för hands version)](configure-authentication-provider-openid-connect.md)
* [Så här konfigurerar du din app för inloggning med hjälp av en inloggning med Apple (för hands version)](configure-authentication-provider-apple.md)

## <a name="use-multiple-sign-in-providers"></a>Använd flera inloggnings leverantörer

Portal konfigurationen ger inte till gång till en metod för att presentera flera inloggnings leverantörer för dina användare (till exempel både Facebook och Twitter). Det är dock inte svårt att lägga till funktioner i din app. Stegen beskrivs på följande sätt:

På sidan **autentisering/auktorisering** i Azure Portal konfigurerar du först var och en av identitets leverantören som du vill aktivera.

I **åtgärd som ska vidtas när begäran inte autentiseras** väljer du **Tillåt anonyma begär Anden (ingen åtgärd)**.

På inloggnings sidan eller i navigerings fältet eller på någon annan plats i appen lägger du till en inloggnings länk till alla providers som du har aktiverat ( `/.auth/login/<provider>` ). Exempel:

```html
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
<a href="/.auth/login/apple">Log in with Apple</a>
```

När användaren klickar på en av länkarna öppnas respektive inloggnings sida för att logga in användaren.

Om du vill omdirigera användaren efter inloggning till en anpassad URL, använder du frågesträngparametern `post_login_redirect_url` (ska inte förväxlas med omdirigerings-URI: n i din identitetsprovider). Om du till exempel vill navigera användaren till `/Home/Index` efter inloggning använder du följande HTML-kod:

```html
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Verifiera token från providers

I en klient-riktad inloggning loggar programmet in användaren till providern manuellt och skickar sedan autentiseringstoken till App Service för verifiering (se [Authentication Flow](overview-authentication-authorization.md#authentication-flow)). Den här verifieringen ger i själva verket ingen åtkomst till de resurser som behövs, men en lyckad verifiering ger dig en sessionstoken som du kan använda för att få åtkomst till program resurser. 

För att verifiera providerns token måste App Service-appen först konfigureras med önskad Provider. När du har hämtat autentiseringstoken från providern efter att du har hämtat token, kan du ställa in token `/.auth/login/<provider>` för verifiering. Exempel: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Token-formatet varierar något beroende på providern. I följande tabell finns mer information:

| Leverantörs värde | Krävs i begär ande texten | Kommentarer |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | `expires_in`Egenskapen är valfri. <br/>När du begär token från Live-tjänster ska du alltid begära `wl.basic` omfånget. |
| `google` | `{"id_token":"<id_token>"}` | `authorization_code`Egenskapen är valfri. Om det här alternativet anges kan det även åtföljas av `redirect_uri` egenskapen. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Använd en giltig [token för användar åtkomst](https://developers.facebook.com/docs/facebook-login/access-tokens) från Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Om providerns token verifieras, returnerar API: t med en `authenticationToken` i svars texten, som är din sessionstoken. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

När du har denna sessionstoken kan du komma åt skyddade app-resurser genom att lägga till `X-ZUMO-AUTH` rubriken till dina HTTP-begäranden. Exempel: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Logga ut från en session

Användare kan initiera en utloggning genom att skicka en `GET` begäran till appens `/.auth/logout` slut punkt. `GET`Begäran gör följande:

- Rensar autentiserings-cookies från den aktuella sessionen.
- Tar bort den aktuella användarens token från tokenarkivet.
- För Azure Active Directory och Google utför en utloggning på Server sidan på identitets leverantören.

Här är en enkel utloggningslänk på en webbsida:

```html
<a href="/.auth/logout">Sign out</a>
```

Som standard omdirigerar en lyckad utloggning klienten till URL: en `/.auth/logout/done` . Du kan ändra omdirigerings sidan efter utloggning genom att lägga till `post_logout_redirect_uri` Frågeparametern. Exempel:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Vi rekommenderar att du [kodar](https://wikipedia.org/wiki/Percent-encoding) värdet för `post_logout_redirect_uri` .

När du använder fullständigt kvalificerade URL: er måste URL: en antingen finnas i samma domän eller konfigurerad som en tillåten extern omdirigerings-URL för din app. I följande exempel kan du omdirigera till `https://myexternalurl.com` som inte finns i samma domän:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Kör följande kommando i [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Bevara URL-fragment

När användarna loggar in i din app vill de vanligt vis omdirigeras till samma avsnitt på samma sida, till exempel `/wiki/Main_Page#SectionZ` . Men eftersom [URL-fragment](https://wikipedia.org/wiki/Fragment_identifier) (till exempel `#SectionZ` ) aldrig skickas till servern bevaras de inte som standard när OAuth-inloggningen är klar och omdirigerar tillbaka till din app. Användarna får sedan en optimal upplevelse när de behöver navigera till önskad fäst punkt igen. Den här begränsningen gäller för alla lösningar på Server sidans autentisering.

I App Service autentisering kan du bevara URL-fragment över OAuth-inloggningen. Det gör du genom att ange en app-inställning `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` som kallas för `true` . Du kan göra det i [Azure Portal](https://portal.azure.com)eller genom att köra följande kommando i [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Åtkomst användar anspråk

App Service skickar användar anspråk till ditt program med hjälp av särskilda rubriker. Externa begär Anden får inte ange dessa huvuden, så de finns bara om de anges av App Service. Några exempel rubriker är:

* X-MS-CLIENT-HUVUD NAMN
* X-MS-CLIENT-HUVUD-ID

Kod som har skrivits på valfritt språk eller ramverk kan hämta den information som behövs från dessa huvuden. För ASP.NET 4,6-appar anges **ClaimsPrincipal** automatiskt med lämpliga värden. ASP.NET Core ger dock inte en mellanliggande autentisering som integreras med App Service användar anspråk. En lösning finns i [MaximeRouiller. Azure. AppService. EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

Om [tokenarkivet](overview-authentication-authorization.md#token-store) är aktiverat för din app kan du också hämta ytterligare information om den autentiserade användaren genom att anropa `/.auth/me` . Mobile Apps Server SDK: er ger hjälp metoder för att arbeta med dessa data. Mer information finns i [så här använder du Azure-Mobile Apps Node.js SDK](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity)och [arbetar med .NET-Server-SDK för Azure-Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Hämta token i app Code

Från din server kod matas de providerspecifika tokens in i begär ande huvudet, så att du enkelt kan komma åt dem. I följande tabell visas möjliga namn på token-huvud:

| Leverantör | Rubrik namn |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook-token | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Microsoft-konto | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Skicka en HTTP- `GET` begäran till `/.auth/me` ([token Store](overview-authentication-authorization.md#token-store) måste vara aktive rad) från din klient kod (till exempel en mobilapp eller i en webbläsares java script). Den returnerade JSON-filen har de providerspecifika tokens.

> [!NOTE]
> Åtkomsttoken används för att få åtkomst till leverantörs resurser, så de finns bara om du konfigurerar providern med en klient hemlighet. Information om hur du hämtar uppdateringstoken finns i uppdatera åtkomsttoken.

## <a name="refresh-identity-provider-tokens"></a>Uppdatera token för identitetsprovider

När din leverantörs åtkomsttoken (inte [sessionstoken](#extend-session-token-expiration-grace-period)) går ut måste du autentisera om användaren innan du använder denna token igen. Du kan undvika att token upphör att gälla genom att `GET` ringa till `/.auth/refresh` slut punkten för ditt program. När den anropas uppdaterar App Service automatiskt åtkomsttoken i [tokenarkivet](overview-authentication-authorization.md#token-store) för den autentiserade användaren. Efterföljande begär Anden om token från din app-kod hämtar de uppdaterade tokens. För att token-uppdateringen ska fungera måste dock tokenarkivet innehålla [uppdateringstoken](https://auth0.com/learn/refresh-tokens/) för providern. Sättet att hämta uppdateringstoken dokumenteras av varje provider, men följande lista är en kort sammanfattning:

- **Google**: Lägg till en `access_type=offline` frågesträngparametern till ditt `/.auth/login/google` API-anrop. Om du använder Mobile Apps SDK kan du lägga till parametern i en av `LogicAsync` överlagringarna (se [Google Refresh-token](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: tillhandahåller inte uppdaterade tokens. Token för lång livs längd upphör att gälla om 60 dagar (se [Facebook-förfallo tid och tillägg för](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)åtkomsttoken).
- **Twitter**: åtkomsttoken upphör inte att gälla (se [vanliga frågor och svar om Twitter](https://developer.twitter.com/en/docs/authentication/faq)).
- **Microsoft-konto**: Välj omfånget när du [konfigurerar autentiseringsinställningar för Microsoft-konton](configure-authentication-provider-microsoft.md) `wl.offline_access` .
- **Azure Active Directory**: i [https://resources.azure.com](https://resources.azure.com) utför du följande steg:
    1. Välj **Läs/skriv** längst upp på sidan.
    2. I den vänstra webbläsaren navigerar du till **prenumerationer** > * *_\<subscription\_name_** > **resourceGroups** > * *_ \<resource\_group\_name> _* * > **providers**  >  **Microsoft. Web**  >  **Sites** > * *_ \<app\_name> _ * * > **config**  >  **authsettings**. 
    3. Klicka på **Redigera**.
    4. Ändra följande egenskap. Ersätt _\<app\_id>_ med Azure Active Directory program-ID för den tjänst som du vill komma åt.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Klicka på **Lägg**. 

När din provider har kon figurer ATS [hittar du uppdateringstoken och förfallo tid för](#retrieve-tokens-in-app-code) åtkomsttoken i tokenarkivet. 

Om du vill uppdatera din åtkomsttoken när som helst, behöver du bara anropa `/.auth/refresh` ett språk. I följande kodfragment används jQuery för att uppdatera åtkomsttoken från en JavaScript-klient.

```javascript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Om en användare återkallar de behörigheter som har beviljats för din app, kan ditt anrop till `/.auth/me` Miss lyckas med ett `403 Forbidden` svar. Information om hur du diagnostiserar fel finns i program loggarna.

## <a name="extend-session-token-expiration-grace-period"></a>Förläng förfallo period för token för session

Den autentiserade sessionen upphör att gälla efter 8 timmar. När en autentiserad session går ut är en respitperiod på 72 timmar som standard. Inom den här Grace-perioden kan du uppdatera sessionstoken med App Service utan att autentisera användaren. Du kan bara anropa `/.auth/refresh` när sessionstoken blir ogiltig, och du behöver inte längre spåra giltighets tiden för token. När perioden på 72-timmen har löpt ut måste användaren logga in igen för att få en giltig sessionstoken.

Om 72 timmar inte är tillräckligt lång tid för dig kan du utöka det här förfallo fönstret. Att förlänga förfallo tiden under en lång period kan medföra betydande säkerhets konsekvenser (till exempel när en autentiseringstoken läcker eller blir stulen). Därför bör du lämna den till standardvärdet 72 timmar eller ange förlängnings perioden till det minsta värdet.

Om du vill utöka standard fönstret för förfallo datum kör du följande kommando i [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Grace-perioden gäller endast för den App Service autentiserade sessionen, inte tokens från identitets leverantörerna. Det finns ingen respitperiod för förfallna providers-token. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Begränsa domänen för inloggnings konton

Med både Microsoft-konto och Azure Active Directory kan du logga in från flera domäner. Till exempel kan Microsoft-konto tillåta _Outlook.com_-, _live.com_-och _hotmail.com_ -konton. Azure AD tillåter valfritt antal anpassade domäner för inloggnings kontona. Men du kanske vill påskynda dina användare direkt till din egen anpassade Azure AD-inloggnings sida (till exempel `contoso.com` ). Följ dessa steg om du vill föreslå domän namnet för inloggnings kontona.

I [https://resources.azure.com](https://resources.azure.com) navigerar du till **prenumerationer** > * *_\<subscription\_name_** > **resourceGroups** > * *_ \<resource\_group\_name> _* * > **providers**  >  **Microsoft. Web**  >  **Sites** > *_* \<app\_name> _ * * > **config**  >  **authsettings**. 

Klicka på **Redigera**, ändra följande egenskap och klicka sedan på **Lägg** till. Se till att ersätta _\<domain\_name>_ med den domän som du vill använda.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Den här inställningen lägger till `domain_hint` frågesträngparametern i URL: en för inloggnings omdirigering. 

> [!IMPORTANT]
> Det är möjligt för klienten att ta bort `domain_hint` parametern efter att ha tagit emot omdirigerings-URL: en och sedan logga in med en annan domän. När den här funktionen är praktisk är den inte en säkerhetsfunktion.
>

## <a name="authorize-or-deny-users"></a>Auktorisera eller neka användare

Medan App Service tar hand om det enklaste auktoriserings fallet (dvs. neka oautentiserade begär Anden) kan din app kräva mer detaljerade funktioner för auktorisering, till exempel begränsa åtkomsten till endast en speciell användar grupp. I vissa fall måste du skriva anpassad program kod för att tillåta eller neka åtkomst till den inloggade användaren. I andra fall kan App Service eller identitets leverantören eventuellt hjälpa dig utan att behöva ändra kod.

- [Server nivå](#server-level-windows-apps-only)
- [Identitets leverantörs nivå](#identity-provider-level)
- [Program nivå](#application-level)

### <a name="server-level-windows-apps-only"></a>Server nivå (endast Windows-appar)

För alla Windows-appar kan du definiera behörighets beteendet för IIS-webbservern genom att redigera *Web.config* -filen. Linux-appar använder inte IIS och kan inte konfigureras via *Web.config*.

1. Navigera till `https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. I webbläsarens Utforskaren för dina App Service-filer navigerar du till *plats/wwwroot*. Om en *Web.config* inte finns skapar du den genom att välja **+**  >  **ny fil**. 

1. Välj blyertspennan för *Web.config* för att redigera den. Lägg till följande konfigurations kod och klicka på **Spara**. Om *Web.config* redan finns, lägger du bara till `<authorization>` elementet med allt i det. Lägg till de konton som du vill tillåta i `<allow>` elementet.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>Identitets leverantörs nivå

Identitets leverantören kan ge viss behörighet för att aktivera nycklar. Exempel:

- För [Azure App Service](configure-authentication-provider-aad.md)kan du [Hantera åtkomst på företags nivå](../active-directory/manage-apps/what-is-access-management.md) direkt i Azure AD. Instruktioner finns i [så här tar du bort en användares åtkomst till ett program](../active-directory/manage-apps/methods-for-removing-user-access.md).
- Google [-API](configure-authentication-provider-google.md)-projekt som tillhör en [organisation](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) kan konfigureras så att de endast tillåter åtkomst till användare i din organisation (se [Google ' **Setting Up The OAuth 2,0** support Page](https://support.google.com/cloud/answer/6158849?hl=en)).

### <a name="application-level"></a>Program nivå

Om någon av de andra nivåerna inte ger dig den auktorisering du behöver, eller om din plattform eller identitets leverantör inte stöds, måste du skriva anpassad kod för att auktorisera användare baserat på [användar anspråk](#access-user-claims).

## <a name="updating-the-configuration-version-preview"></a>Uppdaterar konfigurations versionen (för hands version)

Det finns två versioner av hanterings-API: et för funktionen autentisering/auktorisering. Preview v2-versionen krävs för "Authentication (Preview)"-upplevelsen i Azure Portal. En app som redan använder v1-API: n kan uppgradera till v2-versionen när några ändringar har gjorts. Mer specifikt måste den hemliga konfigurationen flyttas till inställningar för plats-tröga program. Konfigurationen av Microsoft-konto leverantören stöds inte heller i v2.

> [!WARNING]
> Vid migrering till v2-förhands granskning inaktive ras hanteringen av funktionen App Service autentisering/auktorisering för ditt program via vissa klienter, till exempel dess befintliga upplevelse i Azure Portal, Azure CLI och Azure PowerShell. Detta kan inte ångras. Under för hands versionen uppmuntras inte migrering av produktions arbets belastningar eller stöds. Du bör bara följa stegen i det här avsnittet för test program.

### <a name="moving-secrets-to-application-settings"></a>Flytta hemligheter till program inställningar

1. Hämta din befintliga konfiguration med v1-API: et:

   ```azurecli
   # For Web Apps
   az webapp auth show -g <group_name> -n <site_name>

   # For Azure Functions
   az functionapp auth show -g <group_name> -n <site_name>
   ```

   I den resulterande JSON-nyttolasten noterar du det hemliga värde som används för varje provider som du har konfigurerat:

   * AAD `clientSecret`
   * Google `googleClientSecret`
   * Facebook `facebookAppSecret`
   * Twitter `twitterConsumerSecret`
   * Microsoft-konto: `microsoftAccountClientSecret`

   > [!IMPORTANT]
   > De hemliga värdena är viktiga säkerhets referenser och bör hanteras noggrant. Dela inte dessa värden eller spara dem på en lokal dator.

1. Skapa inställningar för plats för tröga program för varje hemligt värde. Du kan välja namnet på varje program inställning. Värdet måste matcha det du fick i föregående steg eller [referera till en Key Vault hemlighet](./app-service-key-vault-references.md?toc=/azure/azure-functions/toc.json) som du har skapat med det värdet.

   Om du vill skapa inställningen kan du använda Azure Portal eller köra en variant av följande för varje provider:

   ```azurecli
   # For Web Apps, Google example    
   az webapp config appsettings set -g <group_name> -n <site_name> --slot-settings GOOGLE_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>

   # For Azure Functions, Twitter example
   az functionapp config appsettings set -g <group_name> -n <site_name> --slot-settings TWITTER_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>
   ```

   > [!NOTE]
   > Program inställningarna för den här konfigurationen ska markeras som plats-tröghet, vilket innebär att de inte flyttas mellan miljöer under en [plats växlings åtgärd](./deploy-staging-slots.md). Detta beror på att din konfiguration av autentisering är kopplad till miljön. 

1. Skapa en ny JSON-fil med namnet `authsettings.json` . Ta de utdata du fick tidigare och ta bort varje hemligt värde från det. Skriv återstående utdata till filen och se till att ingen hemlighet ingår. I vissa fall kan konfigurationen ha matriser som innehåller tomma strängar. Se till att `microsoftAccountOAuthScopes` inte, och om det gör det, så ändra värdet till `null` .

1. Lägg till en egenskap som `authsettings.json` pekar på det program inställnings namn som du skapade tidigare för varje provider:
 
   * AAD `clientSecretSettingName`
   * Google `googleClientSecretSettingName`
   * Facebook `facebookAppSecretSettingName`
   * Twitter `twitterConsumerSecretSettingName`
   * Microsoft-konto: `microsoftAccountClientSecretSettingName`

   En exempel fil efter den här åtgärden kan se ut ungefär så här, i det här fallet endast konfigurerad för AAD:

   ```json
   {
       "id": "/subscriptions/00d563f8-5b89-4c6a-bcec-c1b9f6d607e0/resourceGroups/myresourcegroup/providers/Microsoft.Web/sites/mywebapp/config/authsettings",
       "name": "authsettings",
       "type": "Microsoft.Web/sites/config",
       "location": "Central US",
       "properties": {
           "enabled": true,
           "runtimeVersion": "~1",
           "unauthenticatedClientAction": "AllowAnonymous",
           "tokenStoreEnabled": true,
           "allowedExternalRedirectUrls": null,
           "defaultProvider": "AzureActiveDirectory",
           "clientId": "3197c8ed-2470-480a-8fae-58c25558ac9b",
           "clientSecret": null,
           "clientSecretSettingName": "MICROSOFT_IDENTITY_AUTHENTICATION_SECRET",
           "clientSecretCertificateThumbprint": null,
           "issuer": "https://sts.windows.net/0b2ef922-672a-4707-9643-9a5726eec524/",
           "allowedAudiences": [
               "https://mywebapp.azurewebsites.net"
           ],
           "additionalLoginParams": null,
           "isAadAutoProvisioned": true,
           "aadClaimsAuthorization": null,
           "googleClientId": null,
           "googleClientSecret": null,
           "googleClientSecretSettingName": null,
           "googleOAuthScopes": null,
           "facebookAppId": null,
           "facebookAppSecret": null,
           "facebookAppSecretSettingName": null,
           "facebookOAuthScopes": null,
           "gitHubClientId": null,
           "gitHubClientSecret": null,
           "gitHubClientSecretSettingName": null,
           "gitHubOAuthScopes": null,
           "twitterConsumerKey": null,
           "twitterConsumerSecret": null,
           "twitterConsumerSecretSettingName": null,
           "microsoftAccountClientId": null,
           "microsoftAccountClientSecret": null,
           "microsoftAccountClientSecretSettingName": null,
           "microsoftAccountOAuthScopes": null,
           "isAuthFromFile": "false"
       }   
   }
   ```

1. Skicka den här filen som den nya autentiserings-/auktoriserings konfigurationen för din app:

   ```azurecli
   az rest --method PUT --url "/subscriptions/<subscription_id>/resourceGroups/<group_name>/providers/Microsoft.Web/sites/<site_name>/config/authsettings?api-version=2020-06-01" --body @./authsettings.json
   ```

1. Kontrol lera att din app fortfarande fungerar som förväntat efter den här gesten.

1. Ta bort filen som användes i föregående steg.

Du har nu migrerat appen för att lagra identitets leverantörs hemligheter som program inställningar.

### <a name="support-for-microsoft-account-registrations"></a>Stöd för Microsoft-konto registreringar

V2-API: t stöder för närvarande inte Microsoft-konto som en distinkt Provider. I stället utnyttjar den konvergerade [Microsoft Identity Platform](../active-directory/develop/v2-overview.md) för inloggning av användare med personliga Microsoft-konton. När du växlar till v2 API, används v1 Azure Active Directory-konfigurationen för att konfigurera Microsoft Identity Platform-providern.

Om din befintliga konfiguration innehåller en Microsoft-Provider och inte innehåller en Azure Active Directory-Provider kan du växla konfigurationen till Azure Active Directory-providern och sedan utföra migreringen. Gör så här:

1. Gå till [**Appregistreringar**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) i Azure Portal och hitta registreringen som är kopplad till din Microsoft-tjänstleverantör. Det kan finnas i rubriken "program från personligt konto".
1. Gå till sidan "autentisering" för registreringen. Under "omdirigerings-URI: er" bör du se en post som slutar på `/.auth/login/microsoftaccount/callback` . Kopiera denna URI.
1. Lägg till en ny URI som matchar den som du precis kopierat, förutom att den är i stället `/.auth/login/aad/callback` . Detta gör att registreringen kan användas av App Service autentiserings-/auktoriserings konfiguration.
1. Navigera till App Service autentiserings-/auktoriserings konfiguration för din app.
1. Samla in konfigurationen för Microsoft-konto leverantören.
1. Konfigurera Azure Active Directory-providern med hjälp av hanterings läget Avancerat, och ange de klient-ID och klient hemlighets värden som du samlade in i föregående steg. Använd use `<authentication-endpoint>/<tenant-id>/v2.0` och Ersätt *\<authentication-endpoint>* med [slut punkten för autentiseringen för din moln miljö](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) (t. ex. " https://login.microsoftonline.com " för Global Azure) och Ersätt *\<tenant-id>* med din **katalog (klient) ID**"för utfärdar-URL: en.
1. När du har sparat konfigurationen testar du inloggnings flödet genom att navigera i webbläsaren till `/.auth/login/aad` slut punkten på platsen och slutföra inloggnings flödet.
1. Nu har du kopierat konfigurationen till, men den befintliga konfigurationen av Microsoft-providern finns kvar. Innan du tar bort den måste du kontrol lera att alla delar av appen refererar Azure Active Directory-providern via inloggnings länkar osv. Kontrol lera att alla delar av din app fungerar som förväntat.
1. När du har verifierat att saker fungerar mot AAD Azure Active Directory-providern kan du ta bort konfigurationen av Microsoft-providern.

Vissa appar kan redan ha separata registreringar för Azure Active Directory-och Microsoft-konto. Dessa appar kan inte migreras just nu. 

> [!WARNING]
> Det är möjligt att konvergera de två registreringarna genom att ändra de [konto typer som stöds](../active-directory/develop/supported-accounts-validation.md) för AAD-appens registrering. Detta kan dock medföra ett nytt medgivande för användare av Microsoft-konto, och dessa användares identitets anspråk kan skilja sig åt i strukturen, `sub` särskilt när du ändrar värden eftersom ett nytt app-ID används. Den här metoden rekommenderas inte om den inte är grundligt förstått. Du bör istället vänta på stöd för de två registreringarna i v2 API-ytan.

### <a name="switching-to-v2"></a>Växlar till v2

När ovanstående steg har utförts navigerar du till appen i Azure Portal. Välj avsnittet "Authentication (för hands version)". 

Du kan också göra en begäran-begäran mot `config/authsettingsv2` resursen under plats resursen. Schemat för nytto lasten är detsamma som det fångas i avsnittet [Konfigurera användning av en fil](#config-file) .

## <a name="configure-using-a-file-preview"></a><a name="config-file"> </a>Konfigurera med hjälp av en fil (förhands granskning)

Inställningarna för autentisering kan alternativt konfigureras via en fil som tillhandahålls av din distribution. Detta kan krävas av vissa förhands gransknings funktioner i App Service autentisering/auktorisering.

> [!IMPORTANT]
> Kom ihåg att din program nytto last och därför kan gå mellan miljöer, precis som med [platser](./deploy-staging-slots.md). Det är troligt att du vill att en annan app-registrering fästs på varje plats, och i dessa fall bör du fortsätta att använda standard konfigurations metoden i stället för att använda konfigurations filen.

### <a name="enabling-file-based-configuration"></a>Aktivera filbaserad konfiguration

> [!CAUTION]
> Under för hands versionen inaktiverar den filbaserade konfigurationen hanteringen av funktionen App Service autentisering/auktorisering för ditt program via vissa klienter, till exempel Azure Portal, Azure CLI och Azure PowerShell.

1. Skapa en ny JSON-fil för din konfiguration i roten för ditt projekt (distribuerad till katalogen d:\home\site\wwwroot i din webb-/Function-app). Fyll i önskad konfiguration enligt den [filbaserade konfigurations referensen](#configuration-file-reference). Om du ändrar en befintlig Azure Resource Manager-konfiguration ska du se till att översätta egenskaperna som anges i `authsettings` samlingen till konfigurations filen.

2. Ändra den befintliga konfigurationen, som samlas in i [Azure Resource Manager](../azure-resource-manager/management/overview.md) API: er under `Microsoft.Web/sites/<siteName>/config/authsettings` . Om du vill ändra detta kan du använda en [Azure Resource Manager mall](../azure-resource-manager/templates/overview.md) eller ett verktyg som [Azure Resource Explorer](https://resources.azure.com/). I authsettings-samlingen måste du ange tre egenskaper (och eventuellt ta bort andra):

    1.  Ange `enabled` till "true"
    2.  Ange `isAuthFromFile` till "true"
    3.  Ange `authFilePath` till namnet på filen (till exempel "auth.jspå")

> [!NOTE]
> Formatet `authFilePath` varierar mellan olika plattformar. I Windows stöds både relativa och absoluta sökvägar. Relativ rekommenderas. För Linux stöds endast absoluta sökvägar för närvarande, så värdet för inställningen bör vara "/Home/site/wwwroot/auth.jspå" eller liknande.

När du har gjort den här konfigurations uppdateringen kommer innehållet i filen att användas för att definiera beteendet för App Service autentisering/auktorisering för platsen. Om du vill återgå till Azure Resource Manager konfiguration kan du göra det genom `isAuthFromFile` att ändra tillbaka till "false".

### <a name="configuration-file-reference"></a>Konfigurations fil referens

Alla hemligheter som kommer att refereras från konfigurations filen måste lagras som [program inställningar](./configure-common.md#configure-app-settings). Du kan ge de inställningar du önskar. Se bara till att referenserna från konfigurations filen använder samma nycklar.

Följande förbrukar möjliga konfigurations alternativ i filen:

```json
{
    "platform": {
        "enabled": <true|false>
    },
    "globalValidation": {
        "unauthenticatedClientAction": "RedirectToLoginPage|AllowAnonymous|Return401|Return403",
        "redirectToProvider": "<default provider alias>",
        "excludedPaths": [
            "/path1",
            "/path2"
        ]
    },
    "httpSettings": {
        "requireHttps": <true|false>,
        "routes": {
            "apiPrefix": "<api prefix>"
        },
        "forwardProxy": {
            "convention": "NoProxy|Standard|Custom",
            "customHostHeaderName": "<host header value>",
            "customProtoHeaderName": "<proto header value>"
        }
    },
    "login": {
        "routes": {
            "logoutEndpoint": "<logout endpoint>"
        },
        "tokenStore": {
            "enabled": <true|false>,
            "tokenRefreshExtensionHours": "<double>",
            "fileSystem": {
                "directory": "<directory to store the tokens in if using a file system token store (default)>"
            },
            "azureBlobStorage": {
                "sasUrlSettingName": "<app setting name containing the sas url for the Azure Blob Storage if opting to use that for a token store>"
            }
        },
        "preserveUrlFragmentsForLogins": <true|false>,
        "allowedExternalRedirectUrls": [
            "https://uri1.azurewebsites.net/",
            "https://uri2.azurewebsites.net/",
            "url_scheme_of_your_app://easyauth.callback"
        ],
        "cookieExpiration": {
            "convention": "FixedTime|IdentityDerived",
            "timeToExpiration": "<timespan>"
        },
        "nonce": {
            "validateNonce": <true|false>,
            "nonceExpirationInterval": "<timespan>"
        }
    },
    "identityProviders": {
        "azureActiveDirectory": {
            "enabled": <true|false>,
            "registration": {
                "openIdIssuer": "<issuer url>",
                "clientId": "<app id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_AAD_SECRET",
            },
            "login": {
                "loginParameters": [
                    "paramName1=value1",
                    "paramName2=value2"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "facebook": {
            "enabled": <true|false>,
            "registration": {
                "appId": "<app id>",
                "appSecretSettingName": "APP_SETTING_CONTAINING_FACEBOOK_SECRET"
            },
            "graphApiVersion": "v3.3",
            "login": {
                "scopes": [
                    "public_profile",
                    "email"
                ]
            },
        },
        "gitHub": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GITHUB_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "google": {
            "enabled": true,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GOOGLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "twitter": {
            "enabled": <true|false>,
            "registration": {
                "consumerKey": "<consumer key>",
                "consumerSecretSettingName": "APP_SETTING_CONTAINING TWITTER_CONSUMER_SECRET"
            }
        },
        "apple": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "openIdConnectProviders": {
            "<providerName>": {
                "enabled": <true|false>,
                "registration": {
                    "clientId": "<client id>",
                    "clientCredential": {
                        "clientSecretSettingName": "<name of app setting containing client secret>"
                    },
                    "openIdConnectConfiguration": {
                        "authorizationEndpoint": "<url specifying authorization endpoint>",
                        "tokenEndpoint": "<url specifying token endpoint>",
                        "issuer": "<url specifying issuer>",
                        "certificationUri": "<url specifying jwks endpoint>",
                        "wellKnownOpenIdConfiguration": "<url specifying .well-known/open-id-configuration endpoint - if this property is set, the other properties of this object are ignored, and authorizationEndpoint, tokenEndpoint, issuer, and certificationUri are set to the corresponding values listed at this endpoint>"
                    }
                },
                "login": {
                    "nameClaimType": "<name of claim containing name>",
                    "scopes": [
                        "openid",
                        "profile",
                        "email"
                    ],
                    "loginParameterNames": [
                        "paramName1=value1",
                        "paramName2=value2"
                    ],
                }
            },
            //...
        }
    }
}
```

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>Fästa din app till en viss Authentication runtime-version

När du aktiverar autentisering/auktorisering matas plattformens mellanprogram in i pipeline för HTTP-begäran enligt beskrivningen i [funktions översikten](overview-authentication-authorization.md#how-it-works). Plattformens mellanprogram uppdateras regelbundet med nya funktioner och förbättringar som en del av rutin plattforms uppdateringar. Som standard körs din webb-eller Function-app på den senaste versionen av plattforms oberoende program varan. Dessa automatiska uppdateringar är alltid bakåtkompatibla. Men i den sällsynta händelse att den automatiska uppdateringen introducerar ett körnings problem för din webb-eller Function-app kan du tillfälligt återställa till den tidigare versionen av mellanprogram. Den här artikeln förklarar hur du tillfälligt fäster en app till en angiven version av autentisering mellanprogram.

### <a name="automatic-and-manual-version-updates"></a>Automatiska och manuella versions uppdateringar 

Du kan fästa din app till en speciell version av plattformens mellanprogram genom att ställa in en `runtimeVersion` inställning för appen. Appen körs alltid på den senaste versionen om du inte väljer att uttryckligen fästa den på en specifik version. Det finns ett par versioner som stöds i taget. Om du fäster på en ogiltig version som inte längre stöds använder appen den senaste versionen i stället. Om du alltid vill köra den senaste versionen väljer `runtimeVersion` du ~ 1. 

### <a name="view-and-update-the-current-runtime-version"></a>Visa och uppdatera den aktuella körnings versionen

Du kan ändra den körnings version som används av din app. Den nya körnings versionen börjar gälla när du startar om appen. 

#### <a name="view-the-current-runtime-version"></a>Visa den aktuella körnings versionen

Du kan visa den aktuella versionen av plattforms oberoende mellanprogram, antingen med hjälp av Azure CLI eller via någon av de inbyggda HTTP-slutpunkterna i din app.

##### <a name="from-the-azure-cli"></a>Från Azure CLI

Använd Azure CLI för att visa den aktuella mellan versionen med kommandot [AZ webapp auth show](/cli/azure/webapp/auth#az-webapp-auth-show) .

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

I den här koden ersätter du `<my_app_name>` med namnet på din app. Ersätt även `<my_resource_group>` med namnet på resurs gruppen för din app.

`runtimeVersion`Fältet i CLI-utdata visas. Det ser ut ungefär som i följande exempel utdata, som har trunkerats för tydlighets skull: 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>Från versions slut punkten

Du kan också trycka på/.auth/version-slutpunkten i en app för att visa den aktuella mellanprogram versionen som appen körs på. Det ser ut ungefär som i följande exempel:
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>Uppdatera den aktuella körnings versionen

Med Azure CLI kan du uppdatera `runtimeVersion` inställningen i appen med kommandot [AZ webapp auth Update](/cli/azure/webapp/auth#az-webapp-auth-update) .

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

Ersätt `<my_app_name>` med namnet på din app. Ersätt även `<my_resource_group>` med namnet på resurs gruppen för din app. Ersätt också `<version>` med en giltig version av 1. x-körningsmiljön eller `~1` för den senaste versionen. Du hittar viktig information om de olika körnings versionerna [här] ( https://github.com/Azure/app-service-announcements) som hjälper dig att fastställa vilken version som ska fästas på.

Du kan köra det här kommandot från [Azure Cloud Shell](../cloud-shell/overview.md) genom att välja **prova** i föregående kod exempel. Du kan också använda [Azure CLI lokalt](/cli/azure/install-azure-cli) för att köra det här kommandot när du har kört [AZ-inloggning](/cli/azure/reference-index#az-login) för att logga in.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Autentisera och auktorisera användare från slutpunkt till slutpunkt](tutorial-auth-aad.md)
