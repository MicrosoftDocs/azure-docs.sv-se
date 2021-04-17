---
title: Migrera till MSAL.NET
titleSuffix: Microsoft identity platform
description: Lär dig mer om skillnaderna mellan Microsoft Authentication Library for .NET (MSAL.NET) och Azure AD Authentication Library for .NET (ADAL.NET) och hur du migrerar till MSAL.NET.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 49b5da2da72e78226db19f5d8881073577aee5b0
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575531"
---
# <a name="migrating-applications-to-msalnet"></a>Migrera program till MSAL.NET

Både Microsoft Authentication Library för .NET (MSAL.NET) och Azure AD-autentiseringsbiblioteket för .NET (ADAL.NET) används för att autentisera Azure AD-entiteter och begära token från Azure AD. Hittills har de flesta utvecklare arbetat med Azure AD för utvecklarplattformen (v1.0) för att autentisera Azure AD-identiteter (arbets- och skolkonton) genom att begära token med hjälp av Azure AD Authentication Library (ADAL). Använda MSAL:

- du kan autentisera en bredare uppsättning Microsoft-identiteter (Azure AD-identiteter och Microsoft-konton och sociala och lokala konton via Azure AD B2C) eftersom den använder Microsoft Identity Platform,
- användarna får den bästa upplevelsen för enkel inloggning.
- ditt program kan aktivera inkrementellt medgivande, och det är enklare att stödja villkorlig åtkomst
- du drar nytta av innovationen.

**MSAL.NET eller Microsoft.Identity.Web är nu de autentiseringsbibliotek som** rekommenderas för användning med Microsoft Identity Platform. Inga nya funktioner kommer att implementeras ADAL.NET. Arbetet fokuserar på att förbättra MSAL.

I den här artikeln beskrivs skillnaderna mellan Microsoft Authentication Library för .NET (MSAL.NET) och Azure AD-autentiseringsbiblioteket för .NET (ADAL.NET) och hjälper dig att migrera till MSAL.

## <a name="should-you-migrate-to-msalnet-or-to-microsoftidentityweb"></a>Bör du migrera till MSAL.NET eller till Microsoft.Identity.Web

Innan du går in närmare på MSAL.NET kontra ADAL.NET kanske du vill kontrollera om du vill använda MSAL.NET eller en abstraktion på högre nivå som [Microsoft.Identity.Web](microsoft-identity-web.md)

Mer information om beslutsträdet nedan finns i [Bör jag endast använda MSAL.NET? eller en abstraktion på högre nivå?](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Is-MSAL.NET-right-for-me%3F)

:::image type="content" source="media/msal-net-migration/decision-diagram.png" alt-text="Blockdiagram som förklarar hur du väljer om du behöver använda MSAL.NET och Microsoft.Identity.Web eller både och när du migrerar från ADAL.NET":::

## <a name="differences-between-adal-and-msal-apps"></a>Skillnader mellan ADAL- och MSAL-appar

I de flesta fall vill du använda MSAL.NET microsoft-identitetsplattformen, som är den senaste generationens Microsoft-autentiseringsbibliotek. Med MSAL.NET hämtar du token för användare som loggar in i ditt program med Azure AD -konton (arbets- och skolkonton), Microsoft-konton (personliga) konton (MSA) eller Azure AD B2C.

Om du redan är bekant med slutpunkten för Azure AD för utvecklare (v1.0) (och ADAL.NET) kanske du vill läsa Vad är annorlunda med [Microsoft Identity Platform?](../azuread-dev/azure-ad-endpoint-comparison.md).

Du måste dock fortfarande använda ADAL.NET om ditt program behöver logga in användare med tidigare versioner [av Active Directory Federation Services (AD FS) (ADFS).](/windows-server/identity/active-directory-federation-services) Mer information finns i [ADFS-stöd.](https://aka.ms/msal-net-adfs-support)

I följande bild sammanfattas några av skillnaderna mellan ADAL.NET och MSAL.NET ![ kod sida vid sida](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>NuGet-paket och namnrymder

ADAL.NET från [NuGet-paketet Microsoft.IdentityModel.Clients.ActiveDirectory.](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) namnområdet som ska användas är `Microsoft.IdentityModel.Clients.ActiveDirectory` .

Om du MSAL.NET måste du lägga till [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet-paketet och använda `Microsoft.Identity.Client` namnområdet

### <a name="scopes-not-resources"></a>Omfång, inte resurser

ADAL.NET hämtar token för *resurser*, men MSAL.NET hämtar token för *omfång .* Ett antal MSAL.NET AcquireToken-åsidosättningar kräver en parameter med namnet scopes( `IEnumerable<string> scopes` ). Den här parametern är en enkel lista över strängar som deklarerar önskade behörigheter och resurser som begärs. Välkända omfång är Microsoft Graph [omfånget](https://docs.microsoft.com/graph/permissions-reference).

Det är också möjligt i MSAL.NET att komma åt v1.0-resurser. Mer information finns [i Omfång för ett v1.0-program.](#scopes-for-a-web-api-accepting-v10-tokens)

### <a name="core-classes"></a>Kärnklasser

- ADAL.NET använder [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) som representation av din anslutning till säkerhetstokentjänsten (STS) eller auktoriseringsservern via en utfärdare. Däremot är MSAL.NET runt [klientprogram](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications). Den innehåller två separata klasser: `PublicClientApplication` och `ConfidentialClientApplication`

- Hämta token: ADAL.NET och MSAL.NET har samma autentiseringssamtal ( och för ADAL.NET, och och i MSAL.NET), men `AcquireTokenAsync` med olika parametrar som `AcquireTokenSilentAsync` `AcquireTokenInteractive` `AcquireTokenSilent` krävs. En skillnad är att i MSAL.NET behöver du inte längre skicka in för ditt program i `ClientID` varje AcquireTokenXX-anrop. Anges faktiskt `ClientID` bara en gång när du skapar ( eller `IPublicClientApplication` `IConfidentialClientApplication` ).

### <a name="iaccount-not-iuser"></a>IAccount inte IUser

ADAL.NET manipulerade användare. En användare är dock en mänsklig agent eller en programvaruagent, men den kan ha/äga/vara ansvarig för ett eller flera konton i Microsofts identitetssystem (flera Azure AD-konton, Azure AD B2C, Personliga Microsoft-konton).

MSAL.NET 2.x definierar nu begreppet Konto (via IAccount-gränssnittet). Den här stora ändringen ger rätt semantik: det faktum att samma användare kan ha flera konton i olika Azure AD-kataloger. Det MSAL.NET också bättre information i gästscenarier eftersom hemkontoinformation tillhandahålls.

Mer information om skillnaderna mellan IUser och IAccount finns i [MSAL.NET 2.x](https://aka.ms/msal-net-2-released).

### <a name="exceptions"></a>Undantag

#### <a name="interaction-required-exceptions"></a>Undantag som krävs för interaktion

MSAL.NET har mer explicita undantag. Om tyst autentisering till exempel misslyckas i ADAL är proceduren att fånga upp undantaget och leta efter `user_interaction_required` felkoden:

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == "user_interaction_required")
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

Se information i [Det rekommenderade mönstret för att hämta en token](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) med ADAL.NET

Med MSAL.NET fångar du `MsalUiRequiredException` upp enligt beskrivningen i [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token).

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Hantera undantag för anspråksutmaning

I ADAL.NET hanteras anspråksutmaningundantag på följande sätt:

- `AdalClaimChallengeException` är ett undantag (härkomst från ) som utlösts av tjänsten om en resurs kräver fler anspråk från användaren (till exempel `AdalServiceException` tvåfaktorsautentisering). Medlemmen `Claims` innehåller vissa JSON-fragment med anspråken, som förväntas.
- Fortfarande i ADAL.NET måste det offentliga klientprogram som tar emot det här undantaget anropa `AcquireTokenInteractive` åsidosättningen med en anspråksparameter. Den här `AcquireTokenInteractive` åsidosättningen av försöker inte ens träffa cacheminnet eftersom det inte är nödvändigt. Anledningen är att token i cacheminnet inte har rätt anspråk (annars skulle `AdalClaimChallengeException` inte ha utkastts). Därför behöver du inte titta på cacheminnet. Observera att kan tas emot i en WebAPI som gör OBO, medan måste anropas i ett offentligt klientprogram som anropar detta `ClaimChallengeException` `AcquireTokenInteractive` webb-API.
- Mer information, inklusive exempel, finns i Hantera [AdalClaimCclaimgeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception)

I MSAL.NET hanteras anspråksutmaningundantag på följande sätt:

- `Claims`visas i `MsalServiceException` .
- Det finns en `.WithClaim(claims)` metod som kan användas för `AcquireTokenInteractive` byggaren.

### <a name="supported-grants"></a>Bidrag som stöds

Alla bidrag stöds inte ännu i MSAL.NET v2.0-slutpunkten. Följande är en sammanfattning som jämför ADAL.NET och MSAL. NET:s bidrag som stöds.

#### <a name="public-client-applications"></a>Offentliga klientprogram

Här är de bidrag som stöds i ADAL.NET och MSAL.NET för Desktop- och Mobile-program

Bevilja | ADAL.NET | MSAL.NET
----- |----- | -----
Interaktiv | [Interaktiv autentisering](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [Hämta token interaktivt i MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively)
Integrerad Windows-autentisering | [Integrerad autentisering i Windows (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Integrerad Windows-autentisering](msal-authentication-flows.md#integrated-windows-authentication)
Användarnamn/lösenord | [Hämta token med användarnamn och lösenord](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Autentisering med användarnamn och lösenord](msal-authentication-flows.md#usernamepassword)
Enhetskodflöde | [Enhetsprofil för enheter utan webbläsare](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Enhetskodflöde](msal-authentication-flows.md#device-code)

#### <a name="confidential-client-applications"></a>Konfidentiella klientprogram

Här är de bidrag som stöds i ADAL.NET och MSAL.NET för webbprogram, webb-API:er och daemonprogram:

Typ av app | Bevilja | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Webbapp, webb-API, daemon | Klientautentiseringsuppgifter | [Flöden för klientidentifiering i ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Flöden för klientidentifiering i MSAL.NET](msal-authentication-flows.md#client-credentials)
Webb-API | På uppdrag av | [Tjänst-till-tjänst-anrop för användarens räkning med ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [På uppdrag av i MSAL.NET](msal-authentication-flows.md#on-behalf-of)
Webbapp | Autentiseringskod | [Hämta token med auktoriseringskoder på webbappar med ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Hämta token med auktoriseringskoder på webbappar med A MSAL.NET](msal-authentication-flows.md#authorization-code)

### <a name="cache-persistence"></a>Cachepersistence

ADAL.NET kan du utöka klassen för att implementera önskad persistensfunktion på plattformar utan en säker lagring (.NET Framework och .NET Core) med hjälp `TokenCache` av `BeforeAccess` metoderna och `BeforeWrite` . Mer information finns i [Serialisering av tokencache i ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization).

MSAL.NET gör tokencachen till en förseglad klass, vilket tar bort möjligheten att utöka den. Därför måste implementeringen av tokencachepersistence vara i form av en hjälpklass som interagerar med den förseglade tokencachen. Den här interaktionen beskrivs i [Serialisering av tokencache i MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization).

## <a name="signification-of-the-common-authority"></a>Signering av den gemensamma auktoriteten

Om du använder behörigheten i v1.0 tillåter du att användarna loggar in med alla `https://login.microsoftonline.com/common` AAD-konton (för alla organisationer). Se [Auktoritetsverifiering i ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

Om du använder auktoriteten i v2.0 tillåter du att användarna loggar in med valfri AAD-organisation eller ett `https://login.microsoftonline.com/common` personligt Microsoft-konto (MSA). Om MSAL.NET vill begränsa inloggningen till ett AAD-konto (samma beteende som med ADAL.NET) använder du `https://login.microsoftonline.com/organizations` . Mer information finns i `authority` parametern i [det offentliga klientprogrammet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication).

## <a name="v10-and-v20-tokens"></a>v1.0- och v2.0-token

Det finns två versioner av token:
- v1.0-token
- v2.0-token

V1.0-slutpunkten (som används av ADAL) sänder endast v1.0-token.

V2.0-slutpunkten (används av MSAL) ger dock den version av token som webb-API:et accepterar. En egenskap för webb-API:ets programmanifest gör det möjligt för utvecklare att välja vilken version av token som accepteras. Se `accessTokenAcceptedVersion` i [referensdokumentationen för](reference-app-manifest.md) programmanifestet.

Mer information om v1.0- och v2.0-token finns [i Azure Active Directory åtkomsttoken](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Omfång för ett webb-API som accepterar v1.0-token

OAuth2-behörigheter är behörighetsomfång som ett v1.0 webb-API-program (resurs) exponerar för klientprogram. Dessa behörighetsomfång kan beviljas till klientprogram under medgivande. Se avsnittet om oauth2Permissions i Azure Active Directory [programmanifestet](./reference-app-manifest.md).

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Omfång för att begära åtkomst till specifika OAuth2-behörigheter för ett v1.0-program

Om du vill hämta token för ett program som accepterar v1.0-token (till exempel Microsoft Graph-API:et, som är , måste du skapa genom att sammanfoga en önskad resursidentifierare med en önskad https://graph.microsoft.com) `scopes` OAuth2-behörighet för resursen.

Om du till exempel vill komma åt i namnet på användaren ett v1.0-webb-API som app-ID-URI `ResourceId` är vill du använda:

```csharp
var scopes = new [] { ResourceId+"/user_impersonation" };
```

Om du vill läsa och skriva med MSAL.NET Azure Active Directory med hjälp av Microsoft Graph-API:et ( skapar du en lista över omfång som i https://graph.microsoft.com/) följande kodfragment:

```csharp
string ResourceId = "https://graph.microsoft.com/"; 
string[] scopes = { ResourceId + "Directory.Read", ResourceId + "Directory.Write" }
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Varning: Om du har ett eller två snedstreck i omfånget som motsvarar ett v1.0-webb-API

Om du vill skriva det omfång som motsvarar Azure Resource Manager API ( begär du följande https://management.core.windows.net/) omfång (observera de två snedstrecken).

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Det beror på att Resource Manager API förväntar sig ett snedstreck i sitt målgruppsanspråk ( ), och sedan finns det ett snedstreck för att separera `aud` API-namnet från omfånget.

Logiken som används av Azure AD är följande:
- För ADAL-slutpunkt (v1.0) med en v1.0-åtkomsttoken (det enda möjliga), aud=resource
- För MSAL (v2.0-slutpunkt) som frågar en åtkomsttoken för en resurs som accepterar v2.0-token, aud=resource. Appid
- För MSAL (v2.0-slutpunkt) som frågar en åtkomsttoken för en resurs som accepterar en v1.0-åtkomsttoken (vilket är fallet ovan) parsar Azure AD den önskade målgruppen från det begärda omfånget genom att ta allt före det sista snedstrecket och använda det som resursidentifierare. Om https: /database.windows.net förväntar sig målgruppen " " måste du därför begära ett omfång \/ https://database.windows.net/ på https: \/ /database.windows.net//.default. Se även problem #[747:](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)Resurs-URL:ens avslutande snedstreck utelämnas, vilket orsakade sql-autentiseringsfel #747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Omfång för att begära åtkomst till alla behörigheter för ett v1.0-program

Om du till exempel vill hämta en token för alla statiska omfång för ett v1.0-program måste du använda

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] { ResourceId+"/.default" };
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Omfång som ska begäras för flödet av klient autentiseringsuppgifter/daemon-appen

När det gäller flödet för klient autentiseringsuppgifter skulle omfånget som ska överföras också vara `/.default` . Det här omfånget talar om för Azure AD: "alla behörigheter på appnivå som administratören har samtyckt till i programregistreringen.

## <a name="adal-to-msal-migration"></a>Migrering från ADAL till MSAL

I ADAL.NET v2. X, uppdateringstoken exponerades så att du kan utveckla lösningar kring användningen av dessa token genom att cachelagra dem och använda metoderna som tillhandahålls av `AcquireTokenByRefreshToken` ADAL 2.x.
Några av dessa lösningar användes i scenarier som:
* Långvariga tjänster som gör åtgärder, inklusive uppdatering av instrumentpaneler åt användarna medan användarna inte längre är anslutna.
* WebFarm-scenarier för att göra det möjligt för klienten att ta RT till webbtjänsten (cachelagring görs på klientsidan, krypterad cookie och inte på serversidan)

MSAL.NET inte exponerar uppdateringstoken av säkerhetsskäl: MSAL hanterar uppdatering av token åt dig.

Som tur är MSAL.NET nu ett API som gör att du kan migrera dina tidigare uppdateringstoken (som har köpts med ADAL) till `IConfidentialClientApplication` :

```csharp
/// <summary>
/// Acquires an access token from an existing refresh token and stores it and the refresh token into
/// the application user token cache, where it will be available for further AcquireTokenSilent calls.
/// This method can be used in migration to MSAL from ADAL v2 and in various integration
/// scenarios where you have a RefreshToken available.
/// (see https://aka.ms/msal-net-migration-adal2-msal2)
/// </summary>
/// <param name="scopes">Scope to request from the token endpoint.
/// Setting this to null or empty will request an access token, refresh token and ID token with default scopes</param>
/// <param name="refreshToken">The refresh token from ADAL 2.x</param>
IByRefreshToken.AcquireTokenByRefreshToken(IEnumerable<string> scopes, string refreshToken);
```

Med den här metoden kan du ange den tidigare använda uppdateringstoken tillsammans med de omfång (resurser) som du önskar. Uppdateringstoken kommer att utbytas mot en ny och cachelagras i ditt program.

Eftersom den här metoden är avsedd för scenarier som inte är typiska är den inte lättillgänglig med utan `IConfidentialClientApplication` att först typbesätta den till `IByRefreshToken` .

Det här kodfragmentet visar viss migreringskod i ett konfidentiellt klientprogram. `GetCachedRefreshTokenForSignedInUser` hämta uppdateringstoken som lagrades i en tidigare version av programmet som använde för att utnyttja ADAL 2.x. `GetTokenCacheForSignedInUser` deserialiserar en cache för den inloggade användaren (eftersom konfidentiella klientprogram ska ha en cache per användare).

```csharp
TokenCache userCache = GetTokenCacheForSignedInUser();
string rt = GetCachedRefreshTokenForSignedInUser();

IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(clientId)
 .WithAuthority(Authority)
 .WithRedirectUri(RedirectUri)
 .WithClientSecret(ClientSecret)
 .Build();
IByRefreshToken appRt = app as IByRefreshToken;

AuthenticationResult result = await appRt.AcquireTokenByRefreshToken(null, rt)
                                         .ExecuteAsync()
                                         .ConfigureAwait(false);
```

En åtkomsttoken och ID-token returneras i AuthenticationResult medan din nya uppdateringstoken lagras i cacheminnet.

Du kan också använda den här metoden för olika integreringsscenarier där du har en tillgänglig uppdateringstoken.

## <a name="next-steps"></a>Nästa steg

Mer information om omfången finns i [Omfång, behörigheter och medgivande på Microsoft Identity Platform](v2-permissions-and-consent.md)
