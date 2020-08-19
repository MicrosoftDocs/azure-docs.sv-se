---
title: Autentisering och auktorisering
description: Lär dig mer om det inbyggda stödet för autentisering och auktorisering i Azure App Service och Azure Functions, och hur det kan hjälpa till att skydda din app mot obehörig åtkomst.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: b6984e16d1ddcee7d3f276ddcdf8c89609f14fe5
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271030"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Autentisering och auktorisering i Azure App Service och Azure Functions

Azure App Service tillhandahåller stöd för inbyggd autentisering och auktorisering, så att du kan logga in användare och få åtkomst till data genom att skriva minimalt eller ingen kod i din webbapp, RESTful-API och mobil Server del och även [Azure Functions](../azure-functions/functions-overview.md). Den här artikeln beskriver hur App Service underlättar autentisering och auktorisering för din app.

Säker autentisering och auktorisering kräver djupgående förståelse av säkerhet, inklusive Federation, kryptering, [JSON Web token (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) -hantering, [beviljande typer](https://oauth.net/2/grant-types/)och så vidare. App Service tillhandahåller de här verktygen så att du kan lägga mer tid och energi på att tillhandahålla affärs värde till din kund.

> [!IMPORTANT]
> Du behöver inte använda den här funktionen för autentisering och auktorisering. Du kan använda de sammanslagna säkerhetsfunktionerna i ditt webb ramverk alternativt, eller så kan du skriva egna verktyg. Tänk dock på att [Chrome 80 gör större ändringar i dess implementering av SameSite för cookies](https://www.chromestatus.com/feature/5088147346030592) (lanserings datum runt den 2020 mars) och anpassad fjärrautentisering eller andra scenarier som förlitar sig på att en cookie-post på flera platser kan brytas när klientens webbläsare uppdateras. Lösningen är komplicerad eftersom den behöver stöd för olika SameSite-beteenden för olika webbläsare. 
>
> ASP.NET Core 2,1 och senare versioner som är värd för App Service har redan korrigerats för den här avbrytande ändringen och hanterar Chrome 80 och äldre webbläsare på lämpligt sätt. Dessutom har samma korrigerings fil för ASP.NET Framework-4.7.2 distribuerats på App Service instanserna i januari 2020. Mer information finns i [Azure App Service cookie-uppdatering för SameSite](https://azure.microsoft.com/updates/app-service-samesite-cookie-update/).
>

> [!NOTE]
> Autentiserings-/auktoriserings funktionen kallas även för "enkel autentisering".

> [!NOTE]
> Om du aktiverar den här funktionen så kommer **alla** icke-säkra HTTP-förfrågningar till programmet att omdirigeras automatiskt till https, oavsett App Service konfigurations inställningen för att [tvinga https](configure-ssl-bindings.md#enforce-https). Om det behövs kan du inaktivera detta via `requireHttps` inställningen i [konfigurations filen för autentiseringsinställningarna](app-service-authentication-how-to.md#configuration-file-reference), men du måste sedan vara noga med att se till att inga säkerhetstoken någonsin överförs över osäkra http-anslutningar.

Information som är specifik för interna mobilappar finns i [användarautentisering och auktorisering för mobila appar med Azure App Service](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Så här fungerar det

### <a name="on-windows"></a>På Windows

Modulen för autentisering och auktorisering körs i samma sandbox som din program kod. När den är aktive rad passerar varje inkommande HTTP-begäran genom den innan den hanteras av din program kod.

![Ett arkitektur diagram som visar begär Anden som fångas upp av en process i plats sand boxen som samverkar med identitets leverantörer innan trafik till den distribuerade platsen tillåts](media/app-service-authentication-overview/architecture.png)

Den här modulen hanterar flera saker för din app:

- Autentiserar användare med den angivna providern
- Verifierar, lagrar och uppdaterar tokens
- Hanterar den autentiserade sessionen
- Infogar identitets information i begärandehuvuden

Modulen körs separat från din program kod och konfigureras med hjälp av app-inställningar. Inga SDK: er, specifika språk eller ändringar av din program kod krävs. 

### <a name="on-containers"></a>På behållare

Modulen för autentisering och auktorisering körs i en separat behållare som är isolerad från program koden. Med hjälp av det som är känt som [ambassadör-mönstret](https://docs.microsoft.com/azure/architecture/patterns/ambassador)samverkar den inkommande trafiken för att utföra liknande funktioner som i Windows. Eftersom den inte körs i processen är det möjligt att ingen direkt integrering med specifika språk ramverk är möjlig. men relevant information som appen behöver skickas genom att använda begärandehuvuden enligt beskrivningen nedan.

### <a name="userapplication-claims"></a>Användar-/program anspråk

För alla språk ramverk gör App Service anspråk i inkommande token (oavsett om det kommer från en autentiserad användare eller ett klient program) som är tillgängligt för din kod genom att mata in dem i begärandehuvuden. För ASP.NET 4,6-appar fyller App Service [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) med den autentiserade användarens anspråk, så du kan följa standard kod mönstret för .net, inklusive `[Authorize]` attributet. På samma sätt fyller App Service för PHP-appar `_SERVER['REMOTE_USER']` variabeln. För Java-appar är anspråken [tillgängliga från Tomcat-servlet](configure-language-java.md#authenticate-users-easy-auth).

För [Azure Functions](../azure-functions/functions-overview.md) `ClaimsPrincipal.Current` är inte ifyllt för .NET-kod, men du kan fortfarande hitta användar anspråk i begärandehuvuden eller hämta `ClaimsPrincipal` objektet från kontexten för begäran eller till och med en bindnings parameter. Mer information finns i [arbeta med klient identiteter](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities) .

Mer information finns i [användar anspråk för åtkomst](app-service-authentication-how-to.md#access-user-claims).

> [!NOTE]
> För tillfället stöder ASP.NET Core inte att den aktuella användaren fyller den aktuella användaren med funktionen autentisering/auktorisering. En del [tredje part är dock fristående komponenter med öppen källkod](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth) som kan hjälpa dig att fylla i denna lucka.
>

### <a name="token-store"></a>Tokenarkiv

App Service tillhandahåller en inbyggd token-lagringsplats, som är en lagrings plats med token som är associerade med användare av dina webbappar, API: er eller interna mobilappar. När du aktiverar autentisering med vilken provider som helst är det här token-arkivet omedelbart tillgängligt för din app. Om din program kod behöver komma åt data från dessa providers för användarens räkning, t. ex.: 

- publicera på den autentiserade användarens Facebook-tidslinje
- Läs användarens företags data med hjälp av Microsoft Graph API

Du måste vanligt vis skriva kod för att samla in, lagra och uppdatera dessa token i ditt program. Med tokenarkivet hämtar du bara [tokens](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) när du behöver dem och [instruerar App Service att uppdatera dem](app-service-authentication-how-to.md#refresh-identity-provider-tokens) när de blir ogiltiga. 

ID-token, åtkomsttoken och uppdaterade token cachelagras för den autentiserade sessionen och de är bara tillgängliga för den associerade användaren.  

Om du inte behöver arbeta med tokens i din app kan du inaktivera tokenarkivet på sidan **autentisering/auktorisering** i appen.

### <a name="logging-and-tracing"></a>Loggning och spårning

Om du [aktiverar program loggning](troubleshoot-diagnostic-logs.md)ser du autentiserings-och verifierings spårningar direkt i dina loggfiler. Om du ser ett autentiseringsfel som du inte förväntade dig, kan du enkelt hitta all information genom att titta i dina befintliga program loggar. Om du aktiverar [spårning av misslyckade begär Anden](troubleshoot-diagnostic-logs.md)kan du se exakt vilken roll som modulen autentisering och auktorisering kan ha spelat i en misslyckad begäran. Leta efter referenser till en modul med namnet i spårnings loggarna `EasyAuthModule_32/64` . 

## <a name="identity-providers"></a>Identitetsprovidrar

App Service använder [federerade identiteter](https://en.wikipedia.org/wiki/Federated_identity), där en tredjeparts identitets leverantör hanterar användar identiteter och autentiseringspaket åt dig. Fem identitets leverantörer är tillgängliga som standard: 

| Leverantör | Inloggnings slut punkt |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Microsoft-konto](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |
| Valfri [OpenID Connect](https://openid.net/connect/) -Provider (för hands version) | `/.auth/login/<providerName>` |

När du aktiverar autentisering och auktorisering med någon av dessa leverantörer, är dess inloggnings slut punkt tillgänglig för användarautentisering och för verifiering av autentiseringstoken från providern. Du kan ge dina användare ett valfritt antal inloggnings alternativ enkelt.

Det finns en [äldre utöknings Sök väg][custom-auth] för integrering med andra identitets leverantörer eller en anpassad auth-lösning, men detta rekommenderas inte. Överväg i stället att använda OpenID Connect-supporten.

## <a name="authentication-flow"></a>Autentiseringsflöde

Autentiserings flödet är detsamma för alla providers, men varierar beroende på om du vill logga in med providerns SDK:

- Utan Provider SDK: appen delegerar federerad inloggning till App Service. Detta är vanligt vis fallet med webb läsar appar som kan presentera providerns inloggnings sida för användaren. Server koden hanterar inloggnings processen, så den kallas även _Server-dirigerat flöde_ eller _Server flöde_. Det här fallet gäller för webb läsar appar. Den gäller även för inbyggda appar som signerar användare i med hjälp av Mobile Apps klient-SDK eftersom SDK öppnar en webbvy för att logga in användare med App Service autentisering. 
- Med Provider SDK: appen loggar användare i providern manuellt och skickar sedan autentiseringstoken till App Service för verifiering. Detta är vanligt vis fallet med webbläsarbaserade appar, som inte kan visa leverantörens inloggnings sida för användaren. Program koden hanterar inloggnings processen, så den kallas även _klient-dirigerat flöde_ eller _klient flöde_. Det här fallet gäller för REST-API: er, [Azure Functions](../azure-functions/functions-overview.md)och Java Script Browser-klienter, samt webb läsar appar som behöver mer flexibilitet i inloggnings processen. Den gäller även för interna mobilappar som signerar användare med hjälp av providerns SDK.

> [!NOTE]
> Anrop från en betrodd webbapp i App Service till en annan REST API i App Service eller [Azure Functions](../azure-functions/functions-overview.md) kan autentiseras med hjälp av det serverbaserade flödet. Mer information finns i [Anpassa autentisering och auktorisering i App Service](app-service-authentication-how-to.md).
>

I tabellen nedan visas stegen i Authentication Flow.

| Steg | Utan SDK för Provider | Med SDK för Provider |
| - | - | - |
| 1. Logga in användare | Omdirigerar klienten till `/.auth/login/<provider>` . | Klient koden signerar användaren direkt med providerns SDK och tar emot en autentiseringstoken. Mer information finns i leverantörens dokumentation. |
| 2. efter autentisering | Providern omdirigerar klienten till `/.auth/login/<provider>/callback` . | Klient koden [publicerar token från providern](app-service-authentication-how-to.md#validate-tokens-from-providers) till `/.auth/login/<provider>` för verifiering. |
| 3. upprätta en autentiserad session | App Service lägger till autentiserad cookie i svaret. | App Service returnerar dess egen autentiseringstoken till klient koden. |
| 4. betjäna autentiserat innehåll | Klienten inkluderar autentiserings-cookie i efterföljande begär Anden (hanteras automatiskt av webbläsare). | Klient koden presenterar autentiseringstoken i `X-ZUMO-AUTH` huvudet (hanteras automatiskt av Mobile Apps klient-SDK: er). |

För klient webbläsare kan App Service automatiskt dirigera alla oautentiserade användare till `/.auth/login/<provider>` . Du kan också visa användare med en eller flera `/.auth/login/<provider>` Länkar för att logga in i din app med valfri leverantör.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Beteende för auktorisering

I [Azure Portal](https://portal.azure.com)kan du konfigurera App Service auktorisering med ett antal beteenden när en inkommande begäran inte autentiseras.

![En skärm bild som visar List rutan "åtgärd som ska vidtas när begäran inte är autentiserad"](media/app-service-authentication-overview/authorization-flow.png)

Följande rubriker beskriver alternativen.

### <a name="allow-anonymous-requests-no-action"></a>Tillåt anonyma begär Anden (ingen åtgärd)

Med det här alternativet överlåts auktoriseringen av oautentiserad trafik till program koden. För autentiserade begär Anden skickar App Service även information om autentiseringen i HTTP-huvudena. 

Det här alternativet ger större flexibilitet vid hantering av anonyma begär Anden. Du kan till exempel [presentera flera inloggnings leverantörer](app-service-authentication-how-to.md#use-multiple-sign-in-providers) för dina användare. Du måste dock skriva kod. 

### <a name="allow-only-authenticated-requests"></a>Tillåt endast autentiserade begär Anden

Alternativet är att **Logga in med \<provider> **. App Service dirigerar om alla anonyma begär anden till `/.auth/login/<provider>` för den leverantör du väljer. Om den anonyma begäran kommer från en ursprunglig mobilapp är det returnerade svaret en `HTTP 401 Unauthorized` .

Med det här alternativet behöver du inte skriva någon autentiseringsmetod i din app. Bättre auktorisering, till exempel rollbaserad auktorisering, kan hanteras genom att inspektera användarens anspråk (se [användar anspråk för åtkomst](app-service-authentication-how-to.md#access-user-claims)).

> [!CAUTION]
> Att begränsa åtkomsten på det här sättet gäller alla anrop till appen, vilket kanske inte är önskvärt för appar som vill ha en offentligt tillgänglig start sida, som i många program med en enda sida.

## <a name="more-resources"></a>Fler resurser

[Självstudie: autentisera och auktorisera användare från slut punkt till slut punkt i Azure App Service (Windows)](tutorial-auth-aad.md)  
[Självstudie: autentisera och auktorisera användare från slut punkt till slut punkt i Azure App Service för Linux](containers/tutorial-auth-aad.md)  
[Anpassa autentisering och auktorisering i App Service](app-service-authentication-how-to.md) 
 [.Net Core-integrering av Azure AppService EasyAuth (tredje part)](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth) 
 [Få Azure App Service autentisering arbeta med .net Core (tredje part)](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication)

Providerspecifika instruktions guider:

* [Så här konfigurerar du din app för att använda Azure Active Directory-inloggning][AAD]
* [Så här konfigurerar du din app för att använda Facebook-inloggning][Facebook]
* [Så här konfigurerar du din app för att använda Google-inloggning][Google]
* [Så här konfigurerar du din app för att använda Microsoft-kontoinloggning][MSA]
* [Så här konfigurerar du din app för att använda Twitter-inloggning][Twitter]
* [Så här konfigurerar du din app för att använda en OpenID Connect-Provider för inloggning (för hands version)][OIDC]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md
[OIDC]: configure-authentication-provider-openid-connect.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
