---
title: Migrera till MSAL.NET
titleSuffix: Microsoft identity platform
description: Lär dig mer om skillnaderna mellan Microsoft Authentication Library för .NET (MSAL.NET) och Azure AD Authentication Library för .NET (ADAL.NET) och hur du migrerar till MSAL.NET.
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
ms.openlocfilehash: b437efcfa2b0bb2a725929ae0253f48d97d11552
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98754825"
---
# <a name="migrating-applications-to-msalnet"></a>Migrera program till MSAL.NET

Både Microsoft Authentication Library för .NET (MSAL.NET) och Azure AD Authentication Library för .NET (ADAL.NET) används för att autentisera Azure AD-entiteter och begära token från Azure AD. Fram till nu har de flesta utvecklare arbetat med Azure AD för utvecklare Platform (v 1.0) för att autentisera Azure AD-identiteter (arbets-och skol konton) genom att begära token med Azure AD Authentication Library (ADAL). Använda MSAL:

- Du kan autentisera en bredare uppsättning Microsoft-identiteter (Azure AD-identiteter och Microsoft-konton, och sociala och lokala konton via Azure AD B2C) när du använder Microsoft Identity Platform,
- användarna får bästa möjliga upplevelse med enkel inloggning.
- ditt program kan möjliggöra stegvist godkännande och stöd för villkorlig åtkomst är enklare
- du får nytta av innovationen.

**MSAL.net är nu det rekommenderade auth-biblioteket som ska användas med Microsoft Identity Platform**. Inga nya funktioner kommer att implementeras på ADAL.NET. Ansträngningarna fokuserar på att förbättra MSAL.

Den här artikeln beskriver skillnaderna mellan Microsoft Authentication Library för .NET (MSAL.NET) och Azure AD Authentication Library för .NET (ADAL.NET) och hjälper dig att migrera till MSAL.

## <a name="differences-between-adal-and-msal-apps"></a>Skillnader mellan ADAL och MSAL-appar

I de flesta fall vill du använda MSAL.NET och Microsoft Identity Platform, som är den senaste generationen av Microsoft Authentication Libraries. Med MSAL.NET kan du hämta token för användare som loggar in till ditt program med Azure AD (arbets-och skol konton), Microsoft (personliga) konton (MSA) eller Azure AD B2C.

Om du redan är bekant med slut punkten för Azure AD för utvecklare (v 1.0) (och ADAL.NET) kanske du vill läsa [vad som skiljer sig från Microsoft Identity Platform?](../azuread-dev/azure-ad-endpoint-comparison.md).

Du måste dock fortfarande använda ADAL.NET om ditt program behöver logga in användare med tidigare versioner av [Active Directory Federation Services (AD FS) (ADFS)](/windows-server/identity/active-directory-federation-services). Mer information finns i [ADFS-stöd](https://aka.ms/msal-net-adfs-support).

I följande bild sammanfattas några skillnader mellan ADAL.NET och MSAL.NET ![ sida vid sida](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>NuGet-paket och namn områden

ADAL.NET används från [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet-paketet. namn området som ska användas är `Microsoft.IdentityModel.Clients.ActiveDirectory` .

Om du vill använda MSAL.NET måste du lägga till paketet [Microsoft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet och använda `Microsoft.Identity.Client` namn området

### <a name="scopes-not-resources"></a>Webbprogramsomfattande omfattningar

ADAL.NET hämtar token för *resurser*, men MSAL.net hämtar token för *omfattningar*. Ett antal MSAL.NET AcquireToken-åsidosättningar kräver en parameter med namnet scope ( `IEnumerable<string> scopes` ). Den här parametern är en enkel lista med strängar som deklarerar önskade behörigheter och resurser som begärs. Välkända omfattningar är [Microsoft graphs omfång](/graph/permissions-reference).

Det går också att komma åt v 1.0-resurser med MSAL.NET. Se information i [omfattningar för ett v 1.0-program](#scopes-for-a-web-api-accepting-v10-tokens).

### <a name="core-classes"></a>Core-klasser

- ADAL.NET använder [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) som representation av anslutningen till säkerhetstokentjänst (STS) eller auktoriseringsservern via en utfärdare. I motsats är MSAL.NET utformad runt [klient program](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications). Den innehåller två separata klasser: `PublicClientApplication` och `ConfidentialClientApplication`

- Hämtar token: ADAL.NET och MSAL.NET har samma autentiserings anrop ( `AcquireTokenAsync` och  `AcquireTokenSilentAsync` för ADAL.net, och `AcquireTokenInteractive` och `AcquireTokenSilent` i MSAL.net), men med olika parametrar krävs. En skillnad är att i MSAL.NET behöver du inte längre skicka in `ClientID` programmet i varje AcquireTokenXX-anrop. Faktiskt `ClientID` anges bara en gång när du skapar ( `IPublicClientApplication` eller `IConfidentialClientApplication` ).

### <a name="iaccount-not-iuser"></a>IAccount inte IUser

ADAL.NET manipulerade användare. En användare är dock en person eller en program varu agent, men den kan ha/äger ansvar för ett eller flera konton i Microsoft Identity system (flera Azure AD-konton, Azure AD B2C, Microsoft personal-konton).

MSAL.NET 2. x definierar nu konceptet konto (via IAccount-gränssnittet). Den här avbrytande ändringen ger rätt semantik: det faktum att samma användare kan ha flera konton i olika Azure AD-kataloger. MSAL.NET tillhandahåller också bättre information i gäst scenarier, eftersom det finns information om hem kontot.

Mer information om skillnaderna mellan IUser och IAccount finns i [MSAL.NET 2. x](https://aka.ms/msal-net-2-released).

### <a name="exceptions"></a>Undantag

#### <a name="interaction-required-exceptions"></a>Åtgärd krävs undantag

MSAL.NET har fler explicita undantag. Till exempel, när tyst autentisering Miss lyckas i ADAL, är proceduren att fånga undantags felet och leta efter `user_interaction_required` felkoden:

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

Se information i [rekommenderat mönster för att hämta en token](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) med ADAL.net

Med hjälp av MSAL.NET fångar du `MsalUiRequiredException` enligt beskrivningen i [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token).

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Hantera krav kontroll undantag

I ADAL.NET hanteras anspråks utmanings undantag på följande sätt:

- `AdalClaimChallengeException` är ett undantag (härlett från `AdalServiceException` ) som utlösts av tjänsten om en resurs kräver fler anspråk från användaren (t. ex. tvåfaktorautentisering). `Claims`Medlemmen innehåller vissa JSON-fragment med anspråken, som förväntas.
- I ADAL.NET är det offentliga klient programmet som tar emot det här undantaget nödvändigt att anropa `AcquireTokenInteractive` åsidosättningen med en anspråks parameter. Den här åsidosättningen av `AcquireTokenInteractive` försöker inte ens att nå cachen eftersom det inte är nödvändigt. Orsaken är att token i cacheminnet inte har rätt anspråk (annars `AdalClaimChallengeException` skulle a inte ha utlösts). Därför behöver du inte titta på cachen. Observera att `ClaimChallengeException` kan tas emot i en WebAPI att göra OBO, medan `AcquireTokenInteractive` måste anropas i ett offentligt klient program som anropar webb-API: et.
- Mer information, inklusive exempel finns i hantera [AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception)

I MSAL.NET hanteras anspråks utmanings undantag på följande sätt:

- `Claims`Visas i `MsalServiceException` .
- Det finns en `.WithClaim(claims)` metod som kan användas för `AcquireTokenInteractive` verktyget.

### <a name="supported-grants"></a>Bidrag som stöds

Alla bidrag stöds ännu inte i MSAL.NET och v 2.0-slutpunkten. Följande är en sammanfattning som jämför ADAL.NET och MSAL. NETTO Support bidragen.

#### <a name="public-client-applications"></a>Offentliga klient program

Här följer de bidrag som stöds i ADAL.NET och MSAL.NET för Station ära och mobila program

Bevilja | ADAL.NET | MSAL.NET
----- |----- | -----
Interaktiv | [Interaktiv autentisering](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [Förvärva token interaktivt i MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively)
Integrerad Windows-autentisering | [Integrerad autentisering i Windows (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Integrerad Windows-autentisering](msal-authentication-flows.md#integrated-windows-authentication)
Användar namn/lösen ord | [Hämtar token med användar namn och lösen ord](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Lösenordsautentisering för användar namn](msal-authentication-flows.md#usernamepassword)
Enhets kod flöde | [Enhets profil för enheter utan webbläsare](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Enhets kod flöde](msal-authentication-flows.md#device-code)

#### <a name="confidential-client-applications"></a>Konfidentiella klient program

Här är de bidrag som stöds i ADAL.NET och MSAL.NET för webb program, webb-API: er och daemon-program:

Typ av app | Bevilja | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Webbapp, webb-API, daemon | Klientautentiseringsuppgifter | [Klientens autentiseringsuppgifter flödar i ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Klientens autentiseringsuppgifter flödar i MSAL.NET](msal-authentication-flows.md#client-credentials)
Webb-API | På uppdrag av | [Tjänst-till-tjänst-anrop för användarens räkning med ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [På uppdrag av i MSAL.NET](msal-authentication-flows.md#on-behalf-of)
Webbapp | Auth-kod | [Hämta token med auktoriseringsregler i Web Apps med ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Hämta token med auktoriseringsregler i Web Apps med en MSAL.NET](msal-authentication-flows.md#authorization-code)

### <a name="cache-persistence"></a>Beständighet i cacheminnet

Med ADAL.NET kan du utöka- `TokenCache` klassen för att implementera de önskade beständiga funktionerna på plattformar utan ett säkert lagrings utrymme (.NET Framework och .net Core) med hjälp av `BeforeAccess` metoderna, och `BeforeWrite` . Mer information finns i [cachelagring av token i ADAL.net](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization).

MSAL.NET gör att token cachelagrar en förseglad klass och tar bort möjligheten att utöka den. Därför måste din implementation av cachelagring av token-cachen vara i form av en hjälp klass som interagerar med den förseglade token-cachen. Den här interaktionen beskrivs i [cachelagring av token i MSAL.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization).

## <a name="signification-of-the-common-authority"></a>Signification för den gemensamma utfärdaren

Om du använder-utfärdaren i v 1.0 kan `https://login.microsoftonline.com/common` användarna logga in med alla AAD-konton (för alla organisationer). Se [verifiering av auktoritet i ADAL.net](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

Om du använder `https://login.microsoftonline.com/common` -utfärdaren i v 2.0 kan användarna logga in med en AAD-organisation eller ett Microsoft-konto (MSA). I MSAL.NET, om du vill begränsa inloggningen till ett AAD-konto (samma beteende som med ADAL.NET), måste du använda `https://login.microsoftonline.com/organizations` . Mer information finns i- `authority` parametern i det [offentliga klient programmet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication).

## <a name="v10-and-v20-tokens"></a>v 1.0-och v 2.0-token

Det finns två versioner av tokens:
- v 1.0-token
- v 2.0-token

V 1.0-slutpunkten (används av ADAL) avger endast v 1.0-token.

V 2.0-slutpunkten (används av MSAL) genererar dock versionen av den token som webb-API: n accepterar. En egenskap hos applikations manifestet för webb-API: et gör det möjligt för utvecklare att välja vilken version av token som godkänns. Se `accessTokenAcceptedVersion` i referens dokumentationen för [applikations manifestet](reference-app-manifest.md) .

Mer information om v 1.0-och v 2.0-token finns i [Azure Active Directory åtkomsttoken](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Omfattningar för ett webb-API som accepterar v 1.0-token

OAuth2-behörigheter är behörighets omfattningar som ett v 1.0-webb-API (resurs) exponerar för klient program. Dessa behörighets omfattningar kan beviljas till klient program under medgivande. Se avsnittet om oauth2Permissions i [Azure Active Directory applikations manifest](./reference-app-manifest.md).

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Omfattningar för att begära åtkomst till specifika OAuth2-behörigheter för ett v 1.0-program

Om du vill hämta token för ett program som accepterar v 1.0-token (till exempel Microsoft Graph-API, som https://graph.microsoft.com) du behöver skapa `scopes` genom att sammanfoga ett önskat resurs-ID med önskad OAuth2-behörighet för resursen.

Om du till exempel vill komma åt namnet på användaren a v 1.0 webb-API som app-ID URI är `ResourceId` , vill du använda:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

Om du vill läsa och skriva med MSAL.NET Azure Active Directory med hjälp av Microsoft Graph API ( https://graph.microsoft.com/) , skapar du en lista över omfattningar som i följande kodfragment:

```csharp
string ResourceId = "https://graph.microsoft.com/"; 
string[] scopes = { ResourceId + "Directory.Read", ResourceId + "Directory.Write" }
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Varning! om du har ett eller två snedstreck i omfånget som motsvarar ett v 1.0-webb-API

Om du vill skriva det omfång som motsvarar Azure Resource Manager-API: t (måste https://management.core.windows.net/) du begära följande omfång (Observera de två snedstrecken)

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Detta beror på att Resource Manager-API förväntar sig ett snedstreck i mål grupps anspråket ( `aud` ) och det finns ett snedstreck för att avgränsa API-namnet från omfånget.

Den logik som används av Azure AD är följande:
- För ADAL-slutpunkt (v 1.0) med en v 1.0-åtkomsttoken (endast möjlig), AUD = Resource
- För MSAL (v 2.0-slutpunkt) som efterfrågar en åtkomsttoken för en resurs som accepterar v 2.0-token, AUD = Resource. Undanta
- För MSAL (v 2.0-slutpunkt) som frågar en åtkomsttoken för en resurs som accepterar en v 1.0-åtkomsttoken (vilket är fallet ovan), parsar Azure AD den önskade mål gruppen från det begärda omfånget genom att ta allt före det sista snedstrecket och använda det som resurs-ID. Om https: \/ /Database.Windows.net förväntar sig en mål grupp av " https://database.windows.net/ " måste du begära ett omfånget https: \/ /Database.Windows.net//.default. Se även problem #[747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): resurs-URL: en avslutande snedstreck utelämnas, vilket orsakade SQL-auth-fel #747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Omfattningar för att begära åtkomst till alla behörigheter för ett v 1.0-program

Om du till exempel vill hämta en token för alla statiska omfånget för ett v 1.0-program måste en använda

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Omfattningar som ska begäras i händelse av Flow/daemon-app för klientens autentiseringsuppgifter

Om det gäller klientens Credential-flöde, är det också den omfattning som passas `/.default` . Det här omfånget talar om för Azure AD: "alla de behörigheter på program nivå som administratören har samtyckt till i program registreringen.

## <a name="adal-to-msal-migration"></a>ADAL till MSAL-migrering

I ADAL.NET v2. X visade uppdateringstoken att du kan utveckla lösningar kring användningen av dessa token genom att cachelagra dem och använda de `AcquireTokenByRefreshToken` metoder som tillhandahålls av ADAL 2. x.
Några av dessa lösningar användes i scenarier som:
* Tids krävande tjänster som utför åtgärder, inklusive uppdatering av instrument paneler för användarens räkning, är inte längre anslutna.
* Webb server scenarier för att göra det möjligt för klienten att ta RT till webb tjänsten (cachelagring görs på klient sidan, krypterad cookie och inte Server sida)

MSAL.NET visar inte uppdateringstoken, av säkerhets skäl: MSAL hanterar uppdatering av tokens.

Lyckligt vis har MSAL.NET nu ett API som gör att du kan migrera dina tidigare uppdateringstoken (erhållna med ADAL) till `IConfidentialClientApplication` :

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

Med den här metoden kan du ange den tidigare använda uppdateringstoken tillsammans med eventuella omfång (resurser) som du vill ha. Uppdateringstoken kommer att bytas ut mot en ny och cachelagras i ditt program.

Eftersom den här metoden är avsedd för scenarier som inte är typiska går det inte att komma åt den `IConfidentialClientApplication` utan att först omvandla den till `IByRefreshToken` .

Det här kodfragmentet visar en kod för migrering i ett konfidentiellt klient program. `GetCachedRefreshTokenForSignedInUser` Hämta uppdateringstoken som lagrades i en del lagrings utrymmen av en tidigare version av programmet som använde för att utnyttja ADAL 2. x. `GetTokenCacheForSignedInUser` deserialiserar en cache för den inloggade användaren (som konfidentiella klient program bör ha en cache per användare).

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

Du ser en åtkomsttoken och en ID-token som returnerades i din AuthenticationResult medan din nya uppdateringstoken lagras i cacheminnet.

Du kan också använda den här metoden för olika integrations scenarier där du har en uppdaterad token tillgänglig.

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om omfattningarna i [omfattningar, behörigheter och medgivande i Microsoft Identity Platform](v2-permissions-and-consent.md)
