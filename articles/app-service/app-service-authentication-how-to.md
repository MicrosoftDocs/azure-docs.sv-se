---
title: Avancerad användning av AuthN/AuthZ
description: Lär dig att anpassa autentiserings- och auktoriseringsfunktionen i App Service för olika scenarier och hämta användaranspråk och olika token.
ms.topic: article
ms.date: 03/29/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 9335bb62e494fab50f7beadf3d7bbc423d80cf14
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775735"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Avancerad användning av autentisering och auktorisering i Azure App Service

Den här artikeln visar hur du anpassar den inbyggda autentiseringen och auktoriseringen [i App Service](overview-authentication-authorization.md), och för att hantera identiteter från ditt program. 

Kom igång snabbt genom att gå någon av följande självstudier:

* [Självstudie: Autentisera och auktorisera användare från slutpunkt till slutpunkt i Azure App Service](tutorial-auth-aad.md)
* [Så här konfigurerar du din app för att använda Microsoft Identity Platform-inloggning](configure-authentication-provider-aad.md)
* [Så här konfigurerar du din app för att använda Facebook-inloggning](configure-authentication-provider-facebook.md)
* [Så här konfigurerar du din app för att använda Google-inloggning](configure-authentication-provider-google.md)
* [Så här konfigurerar du din app för att använda Twitter-inloggning](configure-authentication-provider-twitter.md)
* [Konfigurera din app för att logga in med en OpenID Connect provider (förhandsversion)](configure-authentication-provider-openid-connect.md)
* [Så här konfigurerar du din app för inloggning med hjälp av en Logga in med Apple (förhandsversion)](configure-authentication-provider-apple.md)

## <a name="use-multiple-sign-in-providers"></a>Använda flera inloggningsproviders

Portalkonfigurationen är inte ett nyckelordnat sätt att presentera flera inloggningsproviders för dina användare (till exempel både Facebook och Twitter). Det är dock inte svårt att lägga till funktionerna i din app. Stegen beskrivs på följande sätt:

Börja med att konfigurera **var och en** av den identitetsprovider som du vill aktivera på sidan Autentisering/auktorisering i Azure Portal.

I **Åtgärd att vidta när begäran inte har autentiserats** väljer du Tillåt **anonyma begäranden (ingen åtgärd).**

På inloggningssidan, i navigeringsfältet eller på någon annan plats i appen lägger du till en inloggningslänk till var och en av de leverantörer som du har aktiverat ( `/.auth/login/<provider>` ). Exempel:

```html
<a href="/.auth/login/aad">Log in with the Microsoft Identity Platform</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
<a href="/.auth/login/apple">Log in with Apple</a>
```

När användaren klickar på en av länkarna öppnas respektive inloggningssida för att logga in användaren.

Om du vill omdirigera användaren efter inloggningen till en anpassad URL använder du frågesträngsparametern (ska inte förväxlas med omdirigerings-URI:n i `post_login_redirect_url` din identitetsproviderkonfiguration). Om du till exempel vill navigera till `/Home/Index` användaren efter inloggningen använder du följande HTML-kod:

```html
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Verifiera token från providers

Vid en klientstyrd inloggning loggar programmet in användaren till providern manuellt och skickar sedan autentiseringstoken till App Service för verifiering (se [Autentiseringsflöde).](overview-authentication-authorization.md#authentication-flow) Själva verifieringen ger dig inte åtkomst till önskade appresurser, men en lyckad validering ger dig en sessionstoken som du kan använda för att komma åt appresurser. 

För att verifiera providertoken måste App Service först konfigureras med önskad provider. När du har hämtat autentiseringstoken från providern publicerar du token till `/.auth/login/<provider>` vid körningen för verifiering. Exempel: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Tokenformatet varierar något beroende på providern. Mer information finns i följande tabell:

| Providervärde | Krävs i begärandetexten | Kommentarer |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | Egenskapen `expires_in` är valfri. <br/>När du begär token från Live-tjänster ska du alltid begära `wl.basic` omfånget. |
| `google` | `{"id_token":"<id_token>"}` | Egenskapen `authorization_code` är valfri. När detta anges kan den även eventuellt åtföljas av `redirect_uri` egenskapen . |
| `facebook`| `{"access_token":"<user_access_token>"}` | Använd en giltig [användaråtkomsttoken](https://developers.facebook.com/docs/facebook-login/access-tokens) från Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Om providertoken verifieras korrekt returnerar API:et med en `authenticationToken` i svarstexten, som är din sessionstoken. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

När du har den här sessionstoken kan du komma åt skyddade appresurser genom att lägga till `X-ZUMO-AUTH` -huvudet i dina HTTP-begäranden. Exempel: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Logga ut från en session

Användare kan initiera en ut logga ut genom att `GET` skicka en begäran till appens `/.auth/logout` slutpunkt. Begäran `GET` gör följande:

- Rensar autentiseringscookies från den aktuella sessionen.
- Tar bort den aktuella användarens token från tokenarkivet.
- För Azure Active Directory och Google utför en ut logga ut på serversidan på identitetsprovidern.

Här är en enkel utloggningslänk på en webbsida:

```html
<a href="/.auth/logout">Sign out</a>
```

Som standard omdirigerar en lyckad ut logga ut klienten till URL:en `/.auth/logout/done` . Du kan ändra omdirigeringssidan efter utdata genom att lägga till `post_logout_redirect_uri` frågeparametern. Exempel:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Vi rekommenderar att du [kodar](https://wikipedia.org/wiki/Percent-encoding) värdet för `post_logout_redirect_uri` .

När du använder fullständigt kvalificerade URL:er måste URL:en antingen finnas i samma domän eller konfigureras som en tillåten extern omdirigerings-URL för din app. I följande exempel, för att omdirigera `https://myexternalurl.com` till som inte finns i samma domän:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Kör följande kommando i [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Bevara URL-fragment

När användarna loggar in på din app vill de vanligtvis omdirigeras till samma avsnitt på samma sida, till exempel `/wiki/Main_Page#SectionZ` . Men eftersom [URL-fragment](https://wikipedia.org/wiki/Fragment_identifier) (till exempel ) aldrig skickas till servern bevaras de inte som standard när OAuth-inloggningen har slutförts och omdirigerar tillbaka till din `#SectionZ` app. Användarna får sedan en icke-optimal upplevelse när de behöver navigera till önskad fästpunkt igen. Den här begränsningen gäller för alla autentiseringslösningar på serversidan.

När App Service autentisering kan du bevara URL-fragment i OAuth-inloggningen. Det gör du genom att ange en appinställning med `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` namnet `true` . Du kan göra det i [Azure Portal](https://portal.azure.com), eller bara köra följande kommando i [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Få åtkomst till användaranspråk

App Service skickar användaranspråk till ditt program med hjälp av särskilda huvuden. Externa begäranden tillåts inte ange dessa huvuden, så de finns bara om de anges av App Service. Några exempelhuvuden är:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Kod som är skriven på val annat språk eller ramverk kan hämta den information som behövs från dessa huvuden. För ASP.NET 4.6-appar anges **ClaimsPrincipal** automatiskt med lämpliga värden. ASP.NET Core tillhandahåller dock inte något mellanprogram för autentisering som integreras med App Service användaranspråk. En tillfällig lösning finns [i MaximeRouiller.Azure.AppService.EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

Om [tokenarkivet](overview-authentication-authorization.md#token-store) är aktiverat för din app kan du också få ytterligare information om den autentiserade användaren genom att anropa `/.auth/me` . De Mobile Apps server-SDK:erna ger hjälpmetoder för att arbeta med dessa data. Mer information finns i Så här använder du [Azure Mobile Apps Node.js SDK](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity)och Arbeta med SDK för [.NET-serverserver för Azure Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Hämta token i appkod

Från serverkoden matas providerspecifika token in i begärandehuvudet så att du enkelt kan komma åt dem. I följande tabell visas möjliga tokenrubriknamn:

| Leverantör | Rubriknamn |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook-token | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Skicka en HTTP-begäran till `GET` `/.auth/me` [(tokenarkivet](overview-authentication-authorization.md#token-store) måste vara aktiverat) från klientkoden (till exempel en mobilapp eller JavaScript i webbläsaren). Den returnerade JSON:en har providerspecifika token.

> [!NOTE]
> Åtkomsttoken är till för åtkomst till providerresurser, så de finns bara om du konfigurerar din provider med en klienthemlighet. Information om hur du hämtar uppdateringstoken finns i Uppdatera åtkomsttoken.

## <a name="refresh-identity-provider-tokens"></a>Uppdatera token för identitetsprovider

När leverantörens åtkomsttoken (inte [sessionstoken](#extend-session-token-expiration-grace-period)) upphör att gälla måste du omauktorera användaren innan du använder denna token igen. Du kan undvika tokenförfallotid genom `GET` att göra ett `/.auth/refresh` anrop till slutpunkten för ditt program. När den anropas App Service uppdaterar automatiskt åtkomsttoken i [tokenarkivet](overview-authentication-authorization.md#token-store) för den autentiserade användaren. Efterföljande begäranden om token med din appkod hämtar de uppdaterade token. För att tokenuppdateringen ska fungera måste dock tokenarkivet innehålla [uppdateringstoken](https://auth0.com/learn/refresh-tokens/) för din leverantör. Hur du hämtar uppdateringstoken dokumenteras av varje provider, men följande lista är en kort sammanfattning:

- **Google:** Lägg till en `access_type=offline` frågesträngsparameter i `/.auth/login/google` API-anropet. Om du använder Mobile Apps SDK kan du lägga till parametern till en av `LogicAsync` överlagringarna (se [Google Refresh Tokens](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook:** Tillhandahåller inte uppdateringstoken. Långlivade token upphör att gälla om 60 dagar (se [Förfallodatum för Facebook och Tillägg för åtkomsttoken).](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)
- **Twitter:** Åtkomsttoken upphör inte att gälla (se Vanliga frågor och [svar om Twitter OAuth](https://developer.twitter.com/en/docs/authentication/faq)).
- **Azure Active Directory:** Gör [https://resources.azure.com](https://resources.azure.com) följande i :
    1. Längst upp på sidan väljer du **Läsa/skriva.**
    2. I den vänstra webbläsaren navigerar du **till** prenumerationer > **_\<subscription\_name_** > **resourceGroups** > ** **_>-providers \<resource\_group\_name> _ >  **Microsoft.Web**  >  **sites** > **_ \<app\_name> _** >   >  **config authsettings**. 
    3. Klicka på **Redigera**.
    4. Ändra följande egenskap. Ersätt _\<app\_id>_ med Azure Active Directory program-ID för den tjänst som du vill komma åt.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Klicka **på Placera**. 

När providern har konfigurerats kan du hitta [uppdateringstoken och](#retrieve-tokens-in-app-code) förfallotiden för åtkomsttoken i tokenarkivet. 

Om du vill uppdatera din åtkomsttoken när som helst anropar du `/.auth/refresh` på val annat språk. Följande kodfragment använder jQuery för att uppdatera dina åtkomsttoken från en JavaScript-klient.

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

Om en användare återkallar de behörigheter som beviljats till din app kan `/.auth/me` anropet till misslyckas med ett `403 Forbidden` svar. Om du vill diagnostisera fel kontrollerar du programloggarna för mer information.

## <a name="extend-session-token-expiration-grace-period"></a>Utöka respitperioden för förfallotid för sessionstoken

Den autentiserade sessionen upphör att gälla efter 8 timmar. När en autentiserad session upphör att gälla finns en respitperiod på 72 timmar som standard. Inom den här respitperioden kan du uppdatera sessionstoken med App Service utan att omauthenticera användaren. Du kan bara anropa `/.auth/refresh` när din sessionstoken blir ogiltig och du inte behöver spåra tokens upphörande själv. När respitperioden på 72 timmar har löpt ut måste användaren logga in igen för att få en giltig sessionstoken.

Om 72 timmar inte räcker för dig kan du utöka den här förfallotiden. Att utöka förfallotiden under en längre period kan ha betydande säkerhetskonsekvenser (till exempel när en autentiseringstoken läcks eller blir stulen). Du bör därför lämna standardvärdet 72 timmar eller ange det minsta värdet för tilläggsperioden.

Om du vill utöka standardförfallotiden kör du följande kommando i [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Respitperioden gäller endast för den App Service autentiserade sessionen, inte token från identitetsproviders. Det finns ingen respitperiod för de utgångna providertoken. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Begränsa domänen för inloggningskonton

Både Microsoft-konto och Azure Active Directory kan du logga in från flera domäner. Microsoft-konto tillåter till exempel _outlook.com,_ _live.com_ och _hotmail.com_ konton. Azure AD tillåter val av antal anpassade domäner för inloggningskontona. Men du kanske vill påskynda användarna direkt till din egen varumärkesindelade Azure AD-inloggningssida (till exempel `contoso.com` ). Följ dessa steg om du vill föreslå domännamnet för inloggningskontona.

I [https://resources.azure.com](https://resources.azure.com) går du till **prenumerationer** > **_\<subscription\_name_** > **resourceGroups** > ** **_> \<resource\_group\_name> _**providers**  >  **Microsoft.Web**  >  **sites** > **_ \<app\_name> _** >   >  **config authsettings**. 

Klicka **på** Redigera, ändra följande egenskap och klicka sedan på **Placera**. Se till att ersätta _\<domain\_name>_ med den domän som du vill använda.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Den här inställningen lägger till `domain_hint` frågesträngsparametern i omdirigerings-URL:en för inloggning. 

> [!IMPORTANT]
> Det är möjligt för klienten att ta bort parametern efter att `domain_hint` ha tagit emot omdirigerings-URL:en och sedan logga in med en annan domän. Så även om den här funktionen är praktisk så är den inte en säkerhetsfunktion.
>

## <a name="authorize-or-deny-users"></a>Auktorisera eller neka användare

Även App Service tar hand om det enklaste auktoriseringsfallet (d.v.s. avvisa icke-autentiseringsbegäranden), kan din app kräva mer information om auktoriseringsbeteende, till exempel att begränsa åtkomsten till endast en viss grupp användare. I vissa fall måste du skriva anpassad programkod för att tillåta eller neka åtkomst till den inloggade användaren. I andra fall kan App Service eller din identitetsprovider hjälpa dig utan att kräva kodändringar.

- [Servernivå](#server-level-windows-apps-only)
- [Nivå för identitetsprovider](#identity-provider-level)
- [Programnivå](#application-level)

### <a name="server-level-windows-apps-only"></a>Servernivå (endast Windows-appar)

För alla Windows-appar kan du definiera auktoriseringsbeteendet för IIS-webbservern genom att redigera *Web.config* filen. Linux-appar använder inte IIS och kan inte konfigureras via *Web.config*.

1. Navigera till `https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. I webbläsarutforskaren för dina App Service navigerar du till *site/wwwroot*. Om en *Web.config* inte finns skapar du den genom att välja **+**  >  **Ny fil**. 

1. Välj pennan för *Web.config* redigera den. Lägg till följande konfigurationskod och klicka på **Spara**. Om *Web.config* redan finns lägger du bara till `<authorization>` elementet med allt i det. Lägg till de konton som du vill tillåta i `<allow>` elementet .

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

### <a name="identity-provider-level"></a>Nivå för identitetsprovider

Identitetsprovidern kan tillhandahålla viss nyckelnyckelauktorisering. Exempel:

- För [Azure App Service](configure-authentication-provider-aad.md)kan du [hantera åtkomst på företagsnivå](../active-directory/manage-apps/what-is-access-management.md) direkt i Azure AD. Anvisningar finns i [Ta bort en användares åtkomst till ett program.](../active-directory/manage-apps/methods-for-removing-user-access.md)
- För [Google](configure-authentication-provider-google.md)kan Google API-projekt som tillhör en organisation konfigureras för att endast tillåta åtkomst till användare i din organisation (se Googles supportsida för att konfigurera [](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) [ **OAuth 2.0).**](https://support.google.com/cloud/answer/6158849?hl=en)

### <a name="application-level"></a>Programnivå

Om någon av de andra nivåerna inte ger den auktorisering du behöver, eller om din plattform eller [identitetsprovider](#access-user-claims)inte stöds, måste du skriva anpassad kod för att auktorisera användare baserat på användaranspråken .

## <a name="updating-the-configuration-version"></a>Uppdatera konfigurationsversionen

Det finns två versioner av hanterings-API:et för autentiserings-/auktoriseringsfunktionen. V2-versionen krävs för autentiseringsupplevelsen i Azure Portal. En app som redan använder V1-API:et kan uppgradera till V2-versionen när några ändringar har gjorts. Mer specifikt måste hemlighetskonfigurationen flyttas till inställningar för platslåst program. Detta kan göras automatiskt från avsnittet "Autentisering" i portalen för din app.

> [!WARNING]
> Migrering till V2 inaktiverar hantering av App Service-autentiserings-/auktoriseringsfunktionen för ditt program via vissa klienter, till exempel dess befintliga upplevelse i Azure Portal, Azure CLI och Azure PowerShell. Detta kan inte ångras.

V2-API:et stöder inte skapande eller redigering av Microsoft-konto som en distinkt provider som gjordes i V1. I stället utnyttjar den konvergerade [Microsoft Identity Platform](../active-directory/develop/v2-overview.md) för att logga in användare med både Azure AD-konton och personliga Microsoft-konton. När du växlar till V2-API:et används V1 Azure Active Directory konfigurationen för att konfigurera Microsoft Identity Platform-providern. V1 Microsoft-kontoprovidern kommer att fortsätta i migreringsprocessen och fortsätta att fungera som vanligt, men vi rekommenderar att du flyttar till den nyare Microsoft Identity Platform-modellen. Mer [information finns i Support för registreringar av Microsoft-kontoleverantörer.](#support-for-microsoft-account-provider-registrations)

Den automatiserade migreringsprocessen flyttar providerhemligheter till programinställningar och konverterar sedan resten av konfigurationen till det nya formatet. Så här använder du den automatiska migreringen:

1. Gå till appen i portalen och välj **menyalternativet** Autentisering.
1. Om appen har konfigurerats med V1-modellen visas knappen **Uppgradera.**
1. Granska beskrivningen i bekräftelsemeddelandet. Om du är redo att utföra migreringen klickar du **på Uppgradera** i prompten.

### <a name="manually-managing-the-migration"></a>Hantera migreringen manuellt

Med följande steg kan du migrera programmet manuellt till V2-API:et om du inte vill använda den automatiska version som nämns ovan.

#### <a name="moving-secrets-to-application-settings"></a>Flytta hemligheter till programinställningar

1. Hämta din befintliga konfiguration med hjälp av V1-API:et:

   ```azurecli
   # For Web Apps
   az webapp auth show -g <group_name> -n <site_name>

   # For Azure Functions
   az functionapp auth show -g <group_name> -n <site_name>
   ```

   I den resulterande JSON-nyttolasten noterar du det hemliga värde som används för varje provider som du har konfigurerat:

   * Aad: `clientSecret`
   * Google: `googleClientSecret`
   * Facebook: `facebookAppSecret`
   * Twitter: `twitterConsumerSecret`
   * Microsoft-konto: `microsoftAccountClientSecret`

   > [!IMPORTANT]
   > De hemliga värdena är viktiga säkerhetsautentiseringsuppgifter och bör hanteras noggrant. Dela inte dessa värden eller spara dem på en lokal dator.

1. Skapa platslåst programinställningar för varje hemligt värde. Du kan välja namnet på varje programinställning. Värdet ska matcha det du fick i föregående steg eller referera till en [Key Vault hemlighet](./app-service-key-vault-references.md?toc=/azure/azure-functions/toc.json) som du har skapat med det värdet.

   Om du vill skapa inställningen kan du använda Azure Portal eller köra en variant av följande för varje provider:

   ```azurecli
   # For Web Apps, Google example    
   az webapp config appsettings set -g <group_name> -n <site_name> --slot-settings GOOGLE_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>

   # For Azure Functions, Twitter example
   az functionapp config appsettings set -g <group_name> -n <site_name> --slot-settings TWITTER_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>
   ```

   > [!NOTE]
   > Programinställningarna för den här konfigurationen ska markeras som platslåst, vilket innebär att de inte flyttas mellan miljöer under en [växlingsåtgärd.](./deploy-staging-slots.md) Det beror på att själva autentiseringskonfigurationen är kopplad till miljön. 

1. Skapa en ny JSON-fil med namnet `authsettings.json` . Ta de utdata som du fick tidigare och ta bort varje hemligt värde från det. Skriv återstående utdata till filen och se till att ingen hemlighet ingår. I vissa fall kan konfigurationen ha matriser som innehåller tomma strängar. Se till att `microsoftAccountOAuthScopes` det inte gör det, och om det gör det växlar du det värdet till `null` .

1. Lägg till en egenskap `authsettings.json` som pekar på programinställningsnamnet som du skapade tidigare för varje provider:
 
   * Aad: `clientSecretSettingName`
   * Google: `googleClientSecretSettingName`
   * Facebook: `facebookAppSecretSettingName`
   * Twitter: `twitterConsumerSecretSettingName`
   * Microsoft-konto: `microsoftAccountClientSecretSettingName`

   En exempelfil efter den här åtgärden kan se ut ungefär så här, i det här fallet endast konfigurerad för AAD:

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
           "clientSecret": "",
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

1. Skicka den här filen som den nya autentiserings-/auktoriseringskonfigurationen för din app:

   ```azurecli
   az rest --method PUT --url "/subscriptions/<subscription_id>/resourceGroups/<group_name>/providers/Microsoft.Web/sites/<site_name>/config/authsettings?api-version=2020-06-01" --body @./authsettings.json
   ```

1. Kontrollera att appen fortfarande fungerar som förväntat efter den här gesten.

1. Ta bort filen som användes i föregående steg.

Nu har du migrerat appen för att lagra identitetsproviderhemligheter som programinställningar.

#### <a name="support-for-microsoft-account-provider-registrations"></a>Stöd för registreringar av Microsoft-kontoprovider

Om din befintliga konfiguration innehåller en Microsoft-kontoprovider och inte innehåller en Azure Active Directory-provider kan du växla konfigurationen till Azure Active Directory-providern och sedan utföra migreringen. Gör så här:

1. Gå till [**Appregistreringar**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) i Azure Portal och leta reda på registreringen som är associerad med din Microsoft-kontoleverantör. Det kan finnas under rubriken "Program från personligt konto".
1. Gå till sidan "Autentisering" för registreringen. Under "Omdirigerings-URI:er" bör du se en post som slutar på `/.auth/login/microsoftaccount/callback` . Kopiera den här URI:en.
1. Lägg till en ny URI som matchar den som du nyss kopierade, förutom att den i stället slutar i `/.auth/login/aad/callback` . Detta gör att registreringen kan användas av konfigurationen App Service autentisering/auktorisering.
1. Gå till konfiguration App Service autentisering/auktorisering för din app.
1. Samla in konfigurationen för Microsoft-kontoprovidern.
1. Konfigurera Azure Active Directory providern med hjälp av hanteringsläget "Avancerat" och ange de värden för klient-ID och klienthemlighet som du samlade in i föregående steg. För Utfärdar-URL använder du Använd och ersätter med autentiseringsslutpunkten för din molnmiljö (t.ex. " " för global Azure) och ersätter med ditt `<authentication-endpoint>/<tenant-id>/v2.0` *\<authentication-endpoint>* [](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) https://login.microsoftonline.com *\<tenant-id>* katalog-ID **(klientorganisation).**
1. När du har sparat konfigurationen testar du inloggningsflödet genom att navigera i webbläsaren till slutpunkten på webbplatsen `/.auth/login/aad` och slutföra inloggningsflödet.
1. Nu har du kopierat konfigurationen, men den befintliga microsoft-kontoproviderkonfigurationen finns kvar. Innan du tar bort den kontrollerar du att alla delar av appen refererar till Azure Active Directory-providern via inloggningslänkar osv. Kontrollera att alla delar av din app fungerar som förväntat.
1. När du har verifierat att saker fungerar mot AAD Azure Active Directory providern kan du ta bort konfigurationen för Microsoft-kontoprovidern.

> [!WARNING]
> Du kan konvergera de två registreringarna genom att ändra kontotyperna [som stöds för](../active-directory/develop/supported-accounts-validation.md) AAD-appregistreringen. Detta skulle dock tvinga fram en ny fråga om medgivande för Microsoft-kontoanvändare, och dessa användares identitetsanspråk kan vara olika i strukturen, särskilt när värden ändras eftersom ett nytt `sub` app-ID används. Den här metoden rekommenderas inte om du inte förstår den noggrant. Du bör i stället vänta på stöd för de två registreringarna på V2 API-ytan.

#### <a name="switching-to-v2"></a>Växla till V2

När ovanstående steg har utförts går du till appen i Azure Portal. Välj avsnittet "Autentisering (förhandsversion)". 

Du kan också göra en PUT-begäran mot `config/authsettingsv2` resursen under platsresursen. Schemat för nyttolasten är samma som det som avbildas i avsnittet [Konfigurera med en](#config-file) fil.

## <a name="configure-using-a-file-preview"></a><a name="config-file"> </a>Konfigurera med hjälp av en fil (förhandsversion)

Dina autentiseringsinställningar kan eventuellt konfigureras via en fil som tillhandahålls av distributionen. Detta kan krävas av vissa förhandsgranskningsfunktioner i App Service autentisering/auktorisering.

> [!IMPORTANT]
> Kom ihåg att din appnyttolast, och därför den här filen, kan flyttas mellan miljöer, som med [platser](./deploy-staging-slots.md). Det är troligt att du vill ha en annan appregistrering fäst på varje fack, och i dessa fall bör du fortsätta att använda standardkonfigurationsmetoden i stället för att använda konfigurationsfilen.

### <a name="enabling-file-based-configuration"></a>Aktivera filbaserad konfiguration

> [!CAUTION]
> När du aktiverar filbaserad konfiguration i förhandsversionen inaktiveras hanteringen av App Service-autentiserings-/auktoriseringsfunktionen för ditt program via vissa klienter, till exempel Azure Portal, Azure CLI och Azure PowerShell.

1. Skapa en ny JSON-fil för konfigurationen i roten av projektet (distribuerad till D:\home\site\wwwroot i webb-/funktionsappen). Fyll i önskad konfiguration enligt den [filbaserade konfigurationsreferensen](#configuration-file-reference). Om du ändrar en Azure Resource Manager konfiguration översätter du egenskaperna som avbildas i `authsettings` samlingen till konfigurationsfilen.

2. Ändra den befintliga konfigurationen, som avbildas i de [Azure Resource Manager](../azure-resource-manager/management/overview.md) API:erna under `Microsoft.Web/sites/<siteName>/config/authsettings` . Om du vill ändra detta kan du använda [en Azure Resource Manager mall](../azure-resource-manager/templates/overview.md) eller ett verktyg som [Azure Resource Explorer](https://resources.azure.com/). I samlingen authsettings måste du ange tre egenskaper (och kan ta bort andra):

    1.  Ange `enabled` till "true"
    2.  Ange `isAuthFromFile` till "true"
    3.  Ange `authFilePath` till namnet på filen (till exempel "auth.jspå")

> [!NOTE]
> Formatet för `authFilePath` varierar mellan olika plattformar. I Windows stöds både relativa och absoluta sökvägar. Relativ rekommenderas. För Linux stöds endast absoluta sökvägar för närvarande, så värdet för inställningen ska vara "/home/site/wwwroot/auth.json" eller liknande.

När du har gjort den här konfigurationsuppdateringen används innehållet i filen för att definiera beteendet för App Service autentisering/auktorisering för platsen. Om du vill återgå till Azure Resource Manager kan du göra det genom att återgå `isAuthFromFile` till "false".

### <a name="configuration-file-reference"></a>Referens för konfigurationsfil

Alla hemligheter som refereras från konfigurationsfilen måste lagras som [programinställningar.](./configure-common.md#configure-app-settings) Du kan namnge inställningarna vad du vill. Se bara till att referenserna från konfigurationsfilen använder samma nycklar.

Följande tar bort möjliga konfigurationsalternativ i filen:

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

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>Fäst appen i en specifik autentiseringskörningsversion

När du aktiverar autentisering/auktorisering matas plattforms mellanprogram in i din HTTP-begäranspipeline enligt beskrivningen i [funktionsöversikten.](overview-authentication-authorization.md#how-it-works) Det här plattforms mellanprogram uppdateras regelbundet med nya funktioner och förbättringar som en del av rutinmässiga plattformsuppdateringar. Som standard körs webb- eller funktionsappen på den senaste versionen av plattformens mellanprogram. Dessa automatiska uppdateringar är alltid bakåtkompatibla. I sällsynta fall då den här automatiska uppdateringen introducerar ett körningsproblem för din webb- eller funktionsapp kan du dock tillfälligt återställa till den tidigare mellanprogramversionen. I den här artikeln förklaras hur du tillfälligt fäster en app på en specifik version av mellanprogrammet för autentisering.

### <a name="automatic-and-manual-version-updates"></a>Automatiska och manuella versionsuppdateringar 

Du kan fästa appen på en specifik version av plattformens mellanprogram genom att ange `runtimeVersion` en inställning för appen. Din app körs alltid på den senaste versionen om du inte uttryckligen vill fästa tillbaka den till en specifik version. Det kommer att finnas några versioner som stöds samtidigt. Om du fäster till en ogiltig version som inte längre stöds använder appen den senaste versionen i stället. Om du alltid vill köra den senaste versionen anger `runtimeVersion` du till ~1. 

### <a name="view-and-update-the-current-runtime-version"></a>Visa och uppdatera den aktuella körningsversionen

Du kan ändra körningsversionen som används av din app. Den nya körningsversionen bör börja gälla när appen har startats om. 

#### <a name="view-the-current-runtime-version"></a>Visa den aktuella körningsversionen

Du kan visa den aktuella versionen av mellanprogrammet för plattformsautentisering antingen med hjälp av Azure CLI eller via någon av de inbyggda HTTP-slutpunkterna för versionen i din app.

##### <a name="from-the-azure-cli"></a>Från Azure CLI

Använd Azure CLI och visa den aktuella mellanprogramsversionen med [kommandot az webapp auth show.](/cli/azure/webapp/auth#az_webapp_auth_show)

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

I den här koden `<my_app_name>` ersätter du med namnet på din app. Ersätt även `<my_resource_group>` med namnet på resursgruppen för din app.

Du ser fältet `runtimeVersion` i CLI-utdata. Det liknar följande exempelutdata, som har trunkerats för tydlighetens skull: 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>Från versionsslutpunkten

Du kan också använda slutpunkten /.auth/version i en app för att visa den aktuella mellanprogramsversionen som appen körs på. Det liknar följande exempelutdata:
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>Uppdatera den aktuella körningsversionen

Med hjälp av Azure CLI kan du uppdatera `runtimeVersion` inställningen i appen med kommandot az [webapp auth update.](/cli/azure/webapp/auth#az_webapp_auth_update)

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

Ersätt `<my_app_name>` med namnet på din app. Ersätt även `<my_resource_group>` med namnet på resursgruppen för din app. Ersätt också `<version>` med en giltig version av 1.x-körningen eller för den senaste `~1` versionen. Du hittar versionsanteckningarna om de olika körningsversionerna [här] ( för https://github.com/Azure/app-service-announcements) att avgöra vilken version som ska fästas.

Du kan köra det här kommandot [från Azure Cloud Shell](../cloud-shell/overview.md) genom att **välja Prova** i föregående kodexempel. Du kan också använda [Azure CLI lokalt för att](/cli/azure/install-azure-cli) köra det här kommandot när du har kört az [login](/cli/azure/reference-index#az_login) för att logga in.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Autentisera och auktorisera användare från slutpunkt till slutpunkt](tutorial-auth-aad.md)
