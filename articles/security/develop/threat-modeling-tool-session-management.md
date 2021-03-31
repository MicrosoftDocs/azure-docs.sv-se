---
title: Sessionshantering – Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: Lär dig mer om hantering av sessionshantering för hot som exponeras i Threat Modeling Tool. Se information om minskning och Visa kod exempel.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: has-adal-ref, devx-track-js, devx-track-csharp
ms.openlocfilehash: a1f4d4a3bb78da82753d651e1a73cf244096d5df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94518109"
---
# <a name="security-frame-session-management"></a>Säkerhets ram: sessionshantering
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Implementera korrekt utloggning med ADAL-metoder när du använder Azure AD](#logout-adal)</li></ul> |
| **IoT-enhet** | <ul><li>[Använd begränsade livs längder för genererade SaS-token](#finite-tokens)</li></ul> |
| **Azure Document DB** | <ul><li>[Använd minsta livstid för token för genererade resurs-token](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[Implementera korrekt utloggning med WsFederation-metoder när du använder ADFS](#wsfederation-logout)</li></ul> |
| **Identitets Server** | <ul><li>[Implementera korrekt utloggning när du använder identitets Server](#proper-logout)</li></ul> |
| **Webb program** | <ul><li>[Program som är tillgängliga via HTTPS måste använda säkra cookies](#https-secure-cookies)</li><li>[Alla HTTP-baserade program ska endast ange http för cookie-definition](#cookie-definition)</li><li>[Minimera CSRF-attacker (Cross-Site request förfalskning) på ASP.NET-webbsidor](#csrf-asp)</li><li>[Konfigurera session för inaktivitetens livs längd](#inactivity-lifetime)</li><li>[Implementera korrekt utloggning från programmet](#proper-app-logout)</li></ul> |
| **Webb-API** | <ul><li>[Minimera CSRF-attacker (Cross-Site request förfalskning) på ASP.NET webb-API: er](#csrf-api)</li></ul> |

## <a name="implement-proper-logout-using-adal-methods-when-using-azure-ad"></a><a id="logout-adal"></a>Implementera korrekt utloggning med ADAL-metoder när du använder Azure AD

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure AD | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Om programmet använder en åtkomsttoken som utfärdats av Azure AD ska utloggnings händelse hanteraren anropa |

### <a name="example"></a>Exempel
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Exempel
Den bör också förstöra användarens session genom att anropa session. Abandon ()-metoden. Följande metod visar säker implementering av användar utloggning:
```csharp
    [HttpPost]
        [ValidateAntiForgeryToken]
        public void LogOff()
        {
            string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            AuthenticationContext authContext = new AuthenticationContext(Authority + TenantId, new NaiveSessionCache(userObjectID));
            authContext.TokenCache.Clear();
            Session.Clear();
            Session.Abandon();
            Response.SetCookie(new HttpCookie("ASP.NET_SessionId", string.Empty));
            HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
        } 
```

## <a name="use-finite-lifetimes-for-generated-sas-tokens"></a><a id="finite-tokens"></a>Använd begränsade livs längder för genererade SaS-token

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enhet | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | SaS-token som genereras för autentisering till Azure IoT Hub bör ha en begränsad förfallo period. Behåll SaS-tokens livs längd till ett minimum för att begränsa den tid som de kan spelas upp om token komprometteras.|

## <a name="use-minimum-token-lifetimes-for-generated-resource-tokens"></a><a id="resource-tokens"></a>Använd minsta livstid för token för genererade resurs-token

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Document DB | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Minska tidsintervallet för en resurs-token till ett minsta värde som krävs. Resurs-token har ett giltigt standard TimeSpan på 1 timme.|

## <a name="implement-proper-logout-using-wsfederation-methods-when-using-adfs"></a><a id="wsfederation-logout"></a>Implementera korrekt utloggning med WsFederation-metoder när du använder ADFS

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | ADFS | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Om programmet är beroende av STS-token som utfärdats av ADFS bör utloggnings händelse hanteraren anropa WSFederationAuthenticationModule. FederatedSignOut ()-metoden för att logga ut användaren. Den aktuella sessionen bör också förstöras och sessionstoken-värdet ska återställas och nullified.|

### <a name="example"></a>Exempel
```csharp
        [HttpPost, ValidateAntiForgeryToken]
        [Authorization]
        public ActionResult SignOut(string redirectUrl)
        {
            if (!this.User.Identity.IsAuthenticated)
            {
                return this.View("LogOff", null);
            }

            // Removes the user profile.
            this.Session.Clear();
            this.Session.Abandon();
            HttpContext.Current.Response.Cookies.Add(new System.Web.HttpCookie("ASP.NET_SessionId", string.Empty)
                {
                    Expires = DateTime.Now.AddDays(-1D),
                    Secure = true,
                    HttpOnly = true
                });

            // Signs out at the specified security token service (STS) by using the WS-Federation protocol.
            Uri signOutUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Issuer);
            Uri replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm);
            if (!string.IsNullOrEmpty(redirectUrl))
            {
                replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm + redirectUrl);
            }
           //     Signs out of the current session and raises the appropriate events.
            var authModule = FederatedAuthentication.WSFederationAuthenticationModule;
            authModule.SignOut(false);
        //     Signs out at the specified security token service (STS) by using the WS-Federation
        //     protocol.            
            WSFederationAuthenticationModule.FederatedSignOut(signOutUrl, replyUrl);
            return new RedirectResult(redirectUrl);
        }
```

## <a name="implement-proper-logout-when-using-identity-server"></a><a id="proper-logout"></a>Implementera korrekt utloggning när du använder identitets Server

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Identitets Server | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [IdentityServer3 – federerad utloggning](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Steg** | IdentityServer stöder möjligheten att federera med externa identitets leverantörer. När en användare loggar ut från en överordnad identitetsprovider, kan det vara möjligt att ta emot ett meddelande när användaren loggar ut, beroende på vilket protokoll som används. Den tillåter att IdentityServer meddelar sina klienter så att de även kan logga ut användaren. Läs dokumentationen i avsnittet referenser om du vill ha information om implementeringen.|

## <a name="applications-available-over-https-must-use-secure-cookies"></a><a id="https-secure-cookies"></a>Program som är tillgängliga via HTTPS måste använda säkra cookies

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | EnvironmentType – OnPrem |
| **Referenser**              | [httpCookies-element (ASP.net-inställnings schema)](/previous-versions/dotnet/netframework-4.0/ms228262(v=vs.100)), [HttpCookie. Secure-egenskap](/dotnet/api/system.web.httpcookie.secure) |
| **Steg** | Cookies är normalt bara tillgängliga för den domän för vilken de har omfånget. Definitionen av "domän" innehåller tyvärr inte protokollet, så cookies som skapas via HTTPS är tillgängliga via HTTP. Attributet "Secure" anger den webbläsare som cookien bara ska göras tillgänglig över HTTPS. Se till att alla cookies som anges över HTTPS använder attributet **Secure** . Kravet kan tillämpas i web.config-filen genom att ange attributet requireSSL till true. Det är den bästa metoden eftersom den upprätthåller attributet **säker** för alla aktuella och framtida cookies utan att behöva göra några ytterligare kod ändringar.|

### <a name="example"></a>Exempel
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
Inställningen tillämpas även om HTTP används för att få åtkomst till programmet. Om HTTP används för att få åtkomst till programmet, bryter inställningen programmet eftersom cookies är inställda med attributet Secure och att webbläsaren inte skickar tillbaka dem till programmet.

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Webb formulär, MVC5 |
| **Attribut**              | EnvironmentType – OnPrem |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | När webb programmet är den förlitande parten och IdP är ADFS-Server kan FedAuth token Secure-attribut konfigureras genom att ställa in requireSSL på True i `system.identityModel.services` avsnittet i web.config:|

### <a name="example"></a>Exempel
```csharp
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:20:0" />
    ....  
    </federationConfiguration>
  </system.identityModel.services>
```

## <a name="all-http-based-application-should-specify-http-only-for-cookie-definition"></a><a id="cookie-definition"></a>Alla HTTP-baserade program ska endast ange http för cookie-definition

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Attribut för säker cookie](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Steg** | För att minska risken för att information avslöjas med ett XSS-angrepp (Cross-Site Scripting) introducerades en ny httpOnly – cookies och stöds av alla större webbläsare. Attributet anger att en cookie inte är tillgänglig via skriptet. Genom att använda HttpOnly cookies minskar ett webb program möjligheten att känslig information som finns i cookien kan bli stulen via skript och skickas till angriparens webbplats. |

### <a name="example"></a>Exempel
Alla HTTP-baserade program som använder cookies bör ange HttpOnly i cookie-definitionen genom att implementera följande konfiguration i web.config:
```XML
<system.web>
.
.
   <httpCookies requireSSL="false" httpOnlyCookies="true"/>
.
.
</system.web>
```

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Webb formulär |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [FormsAuthentication. RequireSSL-egenskap](/dotnet/api/system.web.security.formsauthentication.requiressl) |
| **Steg** | Egenskap svärdet RequireSSL anges i konfigurations filen för ett ASP.NET-program med hjälp av requireSSL-attributet för konfigurations elementet. Du kan ange i Web.config-filen för ditt ASP.NET-program om Transport Layer Security (TLS), som tidigare kallades SSL (Secure Sockets Layer), krävs för att returnera en cookie för formulär-autentisering till servern genom att ange requireSSL-attributet.|

### <a name="example"></a>Exempel 
I följande kod exempel anges attributet requireSSL i Web.config-filen.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | MVC5 |
| **Attribut**              | EnvironmentType – OnPrem |
| **Referenser**              | [Konfiguration av Windows Identity Foundation (WIF) – del II](/archive/blogs/alikl/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler) |
| **Steg** | Om du vill ange attributet httpOnly för FedAuth cookies ska hideFromCsript-attributvärdet vara inställt på True. |

### <a name="example"></a>Exempel
Följande konfiguration visar rätt konfiguration:
```XML
<federatedAuthentication>
<cookieHandler mode="Custom"
                       hideFromScript="true"
                       name="FedAuth"
                       path="/"
                       requireSsl="true"
                       persistentSessionLifetime="25">
</cookieHandler>
</federatedAuthentication>
```

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-pages"></a><a id="csrf-asp"></a>Minimera CSRF-attacker (Cross-Site request förfalskning) på ASP.NET-webbsidor

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Förfalskning av begäran mellan webbplatser (CSRF eller XSRF) är en typ av attack där en angripare kan utföra åtgärder i säkerhets kontexten för en annan användares etablerade session på en webbplats. Målet är att ändra eller ta bort innehåll, om mål webbplatsen är exklusivt beroende av sessions-cookies för att autentisera mottagna begär Anden. En angripare kan utnyttja problemet genom att hämta en annan användares webbläsare för att läsa in en URL med ett kommando från en sårbar plats där användaren redan är inloggad. Det finns många sätt för en angripare att göra det, till exempel genom att vara värd för en annan webbplats som läser in en resurs från den sårbara servern eller att användaren klickar på en länk. Angreppet kan förhindras om servern skickar ytterligare en token till klienten, kräver att klienten inkluderar denna token i alla framtida begär Anden och verifierar att alla framtida förfrågningar innehåller en token som hör till den aktuella sessionen, till exempel med hjälp av ASP.NET AntiForgeryToken eller ViewState. |

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | MVC5, MVC6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [XSRF/CSRF-skydd i ASP.NET MVC och Web pages](https://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Steg** | CSRF-och ASP.NET MVC-formulär – Använd `AntiForgeryToken` Hjälp metoden för vyer, infoga en `Html.AntiForgeryToken()` i formuläret, till exempel|

### <a name="example"></a>Exempel
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
```

### <a name="example"></a>Exempel
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Exempel
Vid samma tillfälle ger HTML. AntiForgeryToken () en cookie som kallas __RequestVerificationToken, med samma värde som det slumpmässiga dolda värdet som visas ovan. För att validera ett inkommande formulär inlägg lägger du till [ValidateAntiForgeryToken]-filtret till mål åtgärds metoden. Exempel:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Synkroniseringsfilter som kontrollerar att:
* Inkommande begäran har en cookie med namnet __RequestVerificationToken
* Inkommande begäran har en `Request.Form` post med namnet __RequestVerificationToken
* Dessa cookie `Request.Form` -och värde matchningar antar att alla är väl, så går begäran till som normalt. Men om så inte är fallet angavs inte ett auktoriseringsfel med meddelandet "en obligatorisk token för att förhindra eller var ogiltig". 

### <a name="example"></a>Exempel
CSRF och AJAX: form-token kan vara ett problem för AJAX-begäranden, eftersom en AJAX-begäran kan skicka JSON-data, inte HTML-formulär data. En lösning är att skicka tokens i en anpassad HTTP-rubrik. Följande kod använder kniv-syntax för att generera tokens och lägger sedan till token i en AJAX-begäran. 
```csharp
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }

    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Exempel
När du bearbetar begäran extraherar du tokens från begär ande huvudet. Anropa sedan metoden för antiförfalskning. validate för att validera tokens. Validate-metoden genererar ett undantag om tokens inte är giltiga.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Webb formulär |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Dra nytta av ASP.NET inbyggda funktioner för att Fend bort webb attacker](/previous-versions/dotnet/articles/ms972969(v=msdn.10)#securitybarriers_topic2) |
| **Steg** | CSRF-attacker i WebForms-baserade program kan lösas genom att ange ViewStateUserKey till en slumpmässig sträng som varierar för varje användares användar-ID eller, bättre än sessions-ID. För ett antal tekniska och sociala skäl är sessions-ID en mycket bättre anpassning eftersom ett sessions-ID är oförutsägbart, tids gräns och varierar per användare.|

### <a name="example"></a>Exempel
Här är den kod du behöver ha på alla dina sidor:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a name="set-up-session-for-inactivity-lifetime"></a><a id="inactivity-lifetime"></a>Konfigurera session för inaktivitetens livs längd

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [HttpSessionState. timeout-egenskap](/dotnet/api/system.web.sessionstate.httpsessionstate.timeout) |
| **Steg** | Timeout för session representerar händelsen som inträffar när en användare inte utför någon åtgärd på en webbplats under ett intervall (definieras av webb servern). Händelsen på Server sidan ändrar status för användarsessionen till "ogiltig" (till exempel "används inte längre") och instruerar webb servern att förstöra den (ta bort alla data som finns i den). I följande kod exempel anges attributet session Time to 15 minuter i Web.config-filen.|

### <a name="example"></a>Exempel
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Aktivera hot identifiering i Azure SQL

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Webb formulär |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Formulär element för autentisering (ASP.NET Settings schema)](/previous-versions/dotnet/netframework-4.0/1d3t3c61(v=vs.100)) |
| **Steg** | Ange formulärets cookie-timeout för formulär för autentisering till 15 minuter|

### <a name="example"></a>Exempel
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Webb formulär, MVC5 |
| **Attribut**              | EnvironmentType – OnPrem |
| **Referenser**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Steg** | När webb programmet är förlitande part och ADFS är STS, kan livs längden för autentiserings-cookies – FedAuth-token – anges med följande konfiguration i web.config:|

### <a name="example"></a>Exempel
```XML
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:15:0" />
      <!-- Set requireHttps=true; -->
      <wsFederation passiveRedirectEnabled="true" issuer="http://localhost:39529/" realm="https://localhost:44302/" reply="https://localhost:44302/" requireHttps="true"/>
      <!--
      Use the code below to enable encryption-decryption of claims received from ADFS. Thumbprint value varies based on the certificate being used.
      <serviceCertificate>
        <certificateReference findValue="4FBBBA33A1D11A9022A5BF3492FF83320007686A" storeLocation="LocalMachine" storeName="My" x509FindType="FindByThumbprint" />
      </serviceCertificate>
      -->
    </federationConfiguration>
  </system.identityModel.services>
```

### <a name="example"></a>Exempel
Även om AD FS utfärdade SAML-biljettens livs längd måste anges till 15 minuter, genom att köra följande PowerShell-kommando på ADFS-servern:
```csharp
Set-ADFSRelyingPartyTrust -TargetName "<RelyingPartyWebApp>" -ClaimsProviderName @("Active Directory") -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a name="implement-proper-logout-from-the-application"></a><a id="proper-app-logout"></a>Implementera korrekt utloggning från programmet

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Utför en korrekt utloggning från programmet när användaren trycker på knappen Logga ut. Vid utloggning ska programmet förstöra användarens session och även återställa och upphäver för sessions-cookie, tillsammans med värdet för att återställa och nullifying för autentisering. När flera sessioner är kopplade till en enda användar identitet, måste de delas gemensamt på Server sidan vid timeout eller utloggning. Till sist ser du till att utloggnings funktioner är tillgängliga på varje sida. |

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-apis"></a><a id="csrf-api"></a>Minimera CSRF-attacker (Cross-Site request förfalskning) på ASP.NET webb-API: er

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Förfalskning av begäran mellan webbplatser (CSRF eller XSRF) är en typ av attack där en angripare kan utföra åtgärder i säkerhets kontexten för en annan användares etablerade session på en webbplats. Målet är att ändra eller ta bort innehåll, om mål webbplatsen är exklusivt beroende av sessions-cookies för att autentisera mottagna begär Anden. En angripare kan utnyttja problemet genom att hämta en annan användares webbläsare för att läsa in en URL med ett kommando från en sårbar plats där användaren redan är inloggad. Det finns många sätt för en angripare att göra det, till exempel genom att vara värd för en annan webbplats som läser in en resurs från den sårbara servern eller att användaren klickar på en länk. Angreppet kan förhindras om servern skickar ytterligare en token till klienten, kräver att klienten inkluderar denna token i alla framtida begär Anden och verifierar att alla framtida förfrågningar innehåller en token som hör till den aktuella sessionen, till exempel med hjälp av ASP.NET AntiForgeryToken eller ViewState. |

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | MVC5, MVC6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Förhindra förfalskning av CSRF-attacker (Cross-Site Request) i ASP.NET Web API](https://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Steg** | CSRF och AJAX: form-token kan vara ett problem för AJAX-begäranden, eftersom en AJAX-begäran kan skicka JSON-data, inte HTML-formulär data. En lösning är att skicka tokens i en anpassad HTTP-rubrik. Följande kod använder kniv-syntax för att generera tokens och lägger sedan till token i en AJAX-begäran. |

### <a name="example"></a>Exempel
```Javascript
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }
    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Exempel
När du bearbetar begäran extraherar du tokens från begär ande huvudet. Anropa sedan metoden för antiförfalskning. validate för att validera tokens. Validate-metoden genererar ett undantag om tokens inte är giltiga.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

### <a name="example"></a>Exempel
CSRF-och ASP.NET MVC-formulär – Använd AntiForgeryToken Helper-metoden i vyer. Lägg till en HTML. AntiForgeryToken () i formuläret, till exempel
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Exempel
Exemplet ovan kommer att returnera något som liknar följande:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Exempel
Vid samma tillfälle ger HTML. AntiForgeryToken () en cookie som kallas __RequestVerificationToken, med samma värde som det slumpmässiga dolda värdet som visas ovan. För att validera ett inkommande formulär inlägg lägger du till [ValidateAntiForgeryToken]-filtret till mål åtgärds metoden. Exempel:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Synkroniseringsfilter som kontrollerar att:
* Inkommande begäran har en cookie med namnet __RequestVerificationToken
* Inkommande begäran har en `Request.Form` post med namnet __RequestVerificationToken
* Dessa cookie `Request.Form` -och värde matchningar antar att alla är väl, så går begäran till som normalt. Men om så inte är fallet angavs inte ett auktoriseringsfel med meddelandet "en obligatorisk token för att förhindra eller var ogiltig".

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | MVC5, MVC6 |
| **Attribut**              | Identitetsprovider-ADFS, identitetsprovider – Azure AD |
| **Referenser**              | [Skydda ett webb-API med enskilda konton och lokal inloggning i ASP.NET Web API 2,2](https://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Steg** | Om webb-API: et skyddas med OAuth 2,0 förväntar det sig ett Bearer-token i begärans huvud för begäran och beviljar endast åtkomst till begäran om token är giltig. Till skillnad från cookie-baserad autentisering ansluter inte webbläsare de Bearer-token till begär Anden. Den begär ande klienten måste uttryckligen koppla Bearer-token i begär ande huvudet. För ASP.NET webb-API: er som skyddas med OAuth 2,0 anses därför Bearer-token vara försvars skydd mot CSRF-attacker. Observera att om MVC-delen av programmet använder formulärautentisering (t. ex. använder cookies), måste du använda antiförfalsknings-token i MVC-webbappen. |

### <a name="example"></a>Exempel
Webb-API: et måste informeras om att endast förlita sig på Bearer-token och inte på cookies. Det kan göras med följande konfiguration i `WebApiConfig.Register` metoden:

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

Metoden SuppressDefaultHostAuthentication anger att webb-API: et ska ignorera all autentisering som inträffar innan begäran når webb-API-pipeline, antingen av IIS eller av OWIN mellanprogram. På så sätt kan vi begränsa webb-API: et till att endast autentisera med Bearer-token.