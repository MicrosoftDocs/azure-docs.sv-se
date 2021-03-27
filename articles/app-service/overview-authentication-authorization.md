---
title: Autentisering och auktorisering
description: Lär dig mer om det inbyggda stödet för autentisering och auktorisering i Azure App Service och Azure Functions, och hur det kan hjälpa till att skydda din app mot obehörig åtkomst.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 35513abdfb61d889abdbd4af7125b1fbb556d7b8
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612763"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Autentisering och auktorisering i Azure App Service och Azure Functions

Azure App Service innehåller inbyggda funktioner för autentisering och auktorisering (kallas ibland "enkel autentisering"), så du kan logga in användare och få åtkomst till data genom att skriva minimalt eller ingen kod i din webbapp, RESTful-API och mobil Server del och även [Azure Functions](../azure-functions/functions-overview.md). Den här artikeln beskriver hur App Service underlättar autentisering och auktorisering för din app.

## <a name="why-use-the-built-in-authentication"></a>Varför ska jag använda den inbyggda autentiseringen?

Du behöver inte använda den här funktionen för autentisering och auktorisering. Du kan använda de sammanslagna säkerhetsfunktionerna i ditt webb ramverk alternativt, eller så kan du skriva egna verktyg. Du måste dock se till att din lösning är uppdaterad med de senaste säkerhets-, protokoll-och webb läsar uppdateringarna.

Det kan ta stor ansträngning att implementera en säker lösning för autentisering (inloggnings användare) och auktorisering (vilket ger åtkomst till säkra data). Du måste se till att följa bästa praxis och standarder för branschen och hålla din implementering uppdaterad. Med den inbyggda autentiseringen för App Service och Azure Functions kan du spara tid och ansträngning genom att tillhandahålla autentisering med federerade identitets leverantörer, så att du kan fokusera på resten av ditt program.

- Med Azure App Service kan du integrera olika auth-funktioner i din webbapp eller API utan att implementera dem själv.
- Den är byggd direkt i plattformen och kräver inte några särskilda språk, SDK, säkerhets kunskaper eller ens kod som används.
- Du kan integrera med flera olika inloggnings leverantörer. Till exempel Azure AD, Facebook, Google, Twitter.

## <a name="identity-providers"></a>Identitetsprovidrar

App Service använder [federerade identiteter](https://en.wikipedia.org/wiki/Federated_identity), där en tredjeparts identitets leverantör hanterar användar identiteter och autentiseringspaket åt dig. Följande identitets leverantörer är tillgängliga som standard:

| Leverantör | Inloggnings slut punkt | How-To vägledning |
| - | - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` | [App Service Azure AD-inloggning](configure-authentication-provider-aad.md) |
| [Microsoft-konto](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` | [App Service inloggning för Microsoft-konto](configure-authentication-provider-microsoft.md) |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` | [App Service Facebook-inloggning](configure-authentication-provider-facebook.md) |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` | [App Service Google-inloggning](configure-authentication-provider-google.md) |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` | [App Service Twitter-inloggning](configure-authentication-provider-twitter.md) |
| Valfri [OpenID Connect](https://openid.net/connect/) -Provider (för hands version) | `/.auth/login/<providerName>` | [App Service OpenID Connect login](configure-authentication-provider-openid-connect.md) |

När du aktiverar autentisering och auktorisering med någon av dessa leverantörer, är dess inloggnings slut punkt tillgänglig för användarautentisering och för verifiering av autentiseringstoken från providern. Du kan ge dina användare ett valfritt antal inloggnings alternativ.

## <a name="considerations-for-using-built-in-authentication"></a>Att tänka på när du använder inbyggd autentisering

Om du aktiverar den här funktionen kommer alla begär anden till ditt program att omdirigeras automatiskt till HTTPS, oavsett App Service konfigurations inställningen för att tvinga HTTPS. Du kan inaktivera detta med  `requireHttps` inställningen i v2-konfigurationen. Vi rekommenderar dock att du använder HTTPS, och du bör se till att inga säkerhetstoken någonsin överförs över osäkra HTTP-anslutningar.

App Service kan användas för autentisering med eller utan begränsning av åtkomst till webbplatsens innehåll och API: er. Om du vill begränsa åtkomsten till appar enbart till autentiserade användare anger du **åtgärd som ska vidtas när begäran inte autentiseras** att logga in med en av de konfigurerade identitets leverantörerna. Om du vill autentisera men inte begränsa åtkomsten anger du **åtgärden som ska vidtas när begäran inte autentiseras** till Tillåt anonyma begär Anden (ingen åtgärd).

> [!NOTE]
> Du bör ge varje app registrering av sin egen behörighet och sitt medgivande. Undvik att dela behörighet mellan miljöer genom att använda separata registrerings program för olika distributions platser. När du testar ny kod kan den här övningen hjälpa till att förhindra att problem påverkar produktions programmet.

## <a name="how-it-works"></a>Så här fungerar det

[Funktions arkitektur i Windows (distribution utan behållare)](#feature-architecture-on-windows-non-container-deployment))

[Funktions arkitektur på Linux och behållare](#feature-architecture-on-linux-and-containers)

[Autentiseringspaket](#authentication-flow)

[Beteende för auktorisering](#authorization-behavior)

[Användar-och program anspråk](#user-and-application-claims)

[Tokenarkiv](#token-store)

[Loggning och spårning](#logging-and-tracing)

#### <a name="feature-architecture-on-windows-non-container-deployment"></a>Funktions arkitektur i Windows (distribution utan behållare)

Modulen för autentisering och auktorisering körs i samma sandbox som din program kod. När den är aktive rad passerar varje inkommande HTTP-begäran genom den innan den hanteras av din program kod.

:::image type="content" source="media/app-service-authentication-overview/architecture.png" alt-text="Ett arkitektur diagram som visar begär Anden som fångas upp av en process i plats sand boxen som samverkar med identitets leverantörer innan trafik till den distribuerade platsen tillåts" lightbox="media/app-service-authentication-overview/architecture.png":::

Den här modulen hanterar flera saker för din app:

- Autentiserar användare med den angivna providern
- Verifierar, lagrar och uppdaterar tokens
- Hanterar den autentiserade sessionen
- Infogar identitets information i begärandehuvuden

Modulen körs separat från din program kod och konfigureras med hjälp av app-inställningar. Inga SDK: er, specifika språk eller ändringar av din program kod krävs. 

#### <a name="feature-architecture-on-linux-and-containers"></a>Funktions arkitektur på Linux och behållare

Modulen för autentisering och auktorisering körs i en separat behållare som är isolerad från program koden. Med hjälp av det som är känt som [ambassadör-mönstret](/azure/architecture/patterns/ambassador)samverkar den inkommande trafiken för att utföra liknande funktioner som i Windows. Eftersom den inte körs i processen är det möjligt att ingen direkt integrering med specifika språk ramverk är möjlig. men relevant information som appen behöver skickas genom att använda begärandehuvuden enligt beskrivningen nedan.

#### <a name="authentication-flow"></a>Autentiseringsflöde

Autentiserings flödet är detsamma för alla providers, men varierar beroende på om du vill logga in med providerns SDK:

- Utan Provider SDK: appen delegerar federerad inloggning till App Service. Detta är vanligt vis fallet med webb läsar appar som kan presentera providerns inloggnings sida för användaren. Server koden hanterar inloggnings processen, så den kallas även _Server-dirigerat flöde_ eller _Server flöde_. Det här fallet gäller för webb läsar appar. Den gäller även för inbyggda appar som signerar användare i med hjälp av Mobile Apps klient-SDK eftersom SDK öppnar en webbvy för att logga in användare med App Service autentisering.
- Med Provider SDK: appen loggar användare i providern manuellt och skickar sedan autentiseringstoken till App Service för verifiering. Detta är vanligt vis fallet med webbläsarbaserade appar, som inte kan visa leverantörens inloggnings sida för användaren. Program koden hanterar inloggnings processen, så den kallas även _klient-dirigerat flöde_ eller _klient flöde_. Det här fallet gäller för REST-API: er, [Azure Functions](../azure-functions/functions-overview.md)och Java Script Browser-klienter, samt webb läsar appar som behöver mer flexibilitet i inloggnings processen. Den gäller även för interna mobilappar som signerar användare med hjälp av providerns SDK.

Anrop från en betrodd webbapp i App Service till en annan REST API i App Service eller [Azure Functions](../azure-functions/functions-overview.md) kan autentiseras med hjälp av det serverbaserade flödet. Mer information finns i [Anpassa autentisering och auktorisering i App Service](app-service-authentication-how-to.md).

I tabellen nedan visas stegen i Authentication Flow.

| Steg | Utan SDK för Provider | Med SDK för Provider |
| - | - | - |
| 1. Logga in användare | Omdirigerar klienten till `/.auth/login/<provider>` . | Klient koden signerar användaren direkt med providerns SDK och tar emot en autentiseringstoken. Mer information finns i leverantörens dokumentation. |
| 2. efter autentisering | Providern omdirigerar klienten till `/.auth/login/<provider>/callback` . | Klient koden [publicerar token från providern](app-service-authentication-how-to.md#validate-tokens-from-providers) till `/.auth/login/<provider>` för verifiering. |
| 3. upprätta en autentiserad session | App Service lägger till autentiserad cookie i svaret. | App Service returnerar dess egen autentiseringstoken till klient koden. |
| 4. betjäna autentiserat innehåll | Klienten inkluderar autentiserings-cookie i efterföljande begär Anden (hanteras automatiskt av webbläsare). | Klient koden presenterar autentiseringstoken i `X-ZUMO-AUTH` huvudet (hanteras automatiskt av Mobile Apps klient-SDK: er). |

För klient webbläsare kan App Service automatiskt dirigera alla oautentiserade användare till `/.auth/login/<provider>` . Du kan också visa användare med en eller flera `/.auth/login/<provider>` Länkar för att logga in i din app med valfri leverantör.

<a name="authorization"></a>

#### <a name="authorization-behavior"></a>Beteende för auktorisering

I [Azure Portal](https://portal.azure.com)kan du konfigurera App Service auktorisering med ett antal beteenden när en inkommande begäran inte autentiseras.

![En skärm bild som visar List rutan "åtgärd som ska vidtas när begäran inte är autentiserad"](media/app-service-authentication-overview/authorization-flow.png)

Följande rubriker beskriver alternativen.

**Tillåt anonyma begär Anden (ingen åtgärd)**

Med det här alternativet överlåts auktoriseringen av oautentiserad trafik till program koden. För autentiserade begär Anden skickar App Service även information om autentiseringen i HTTP-huvudena.

Det här alternativet ger större flexibilitet vid hantering av anonyma begär Anden. Du kan till exempel [presentera flera inloggnings leverantörer](app-service-authentication-how-to.md#use-multiple-sign-in-providers) för dina användare. Du måste dock skriva kod.

**Tillåt endast autentiserade begär Anden**

Alternativet är att **Logga in med \<provider>**. App Service dirigerar om alla anonyma begär anden till `/.auth/login/<provider>` för den leverantör du väljer. Om den anonyma begäran kommer från en ursprunglig mobilapp är det returnerade svaret en `HTTP 401 Unauthorized` .

Med det här alternativet behöver du inte skriva någon autentiseringsmetod i din app. Bättre auktorisering, till exempel rollbaserad auktorisering, kan hanteras genom att inspektera användarens anspråk (se [användar anspråk för åtkomst](app-service-authentication-how-to.md#access-user-claims)).

> [!CAUTION]
> Att begränsa åtkomsten på det här sättet gäller alla anrop till appen, vilket kanske inte är önskvärt för appar som vill ha en offentligt tillgänglig start sida, som i många program med en enda sida.

> [!NOTE]
> Som standard kan alla användare i din Azure AD-klient begära en token för ditt program från Azure AD. Du kan [Konfigurera programmet i Azure AD](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) om du vill begränsa åtkomsten till din app till en definierad uppsättning användare.


#### <a name="user-and-application-claims"></a>Användar-och program anspråk

För alla språk ramverk gör App Service anspråk i inkommande token (oavsett om det kommer från en autentiserad användare eller ett klient program) som är tillgängligt för din kod genom att mata in dem i begärandehuvuden. För ASP.NET 4,6-appar fyller App Service [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) med den autentiserade användarens anspråk, så du kan följa standard kod mönstret för .net, inklusive `[Authorize]` attributet. På samma sätt fyller App Service för PHP-appar `_SERVER['REMOTE_USER']` variabeln. För Java-appar är anspråken [tillgängliga från Tomcat-servlet](configure-language-java.md#authenticate-users-easy-auth).

För [Azure Functions](../azure-functions/functions-overview.md) `ClaimsPrincipal.Current` är inte ifyllt för .NET-kod, men du kan fortfarande hitta användar anspråk i begärandehuvuden eller hämta `ClaimsPrincipal` objektet från kontexten för begäran eller till och med en bindnings parameter. Mer information finns i [arbeta med klient identiteter](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities) .

Mer information finns i [användar anspråk för åtkomst](app-service-authentication-how-to.md#access-user-claims).

För tillfället stöder ASP.NET Core inte att den aktuella användaren fyller den aktuella användaren med funktionen autentisering/auktorisering. En del [tredje part är dock fristående komponenter med öppen källkod](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth) som kan hjälpa dig att fylla i denna lucka.

#### <a name="token-store"></a>Tokenarkiv

App Service tillhandahåller en inbyggd token-lagringsplats, som är en lagrings plats med token som är associerade med användare av dina webbappar, API: er eller interna mobilappar. När du aktiverar autentisering med vilken provider som helst är det här token-arkivet omedelbart tillgängligt för din app. Om din program kod behöver komma åt data från dessa providers för användarens räkning, t. ex.:

- publicera på den autentiserade användarens Facebook-tidslinje
- Läs användarens företags data med hjälp av Microsoft Graph API

Du måste vanligt vis skriva kod för att samla in, lagra och uppdatera dessa token i ditt program. Med tokenarkivet hämtar du bara [tokens](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) när du behöver dem och [instruerar App Service att uppdatera dem](app-service-authentication-how-to.md#refresh-identity-provider-tokens) när de blir ogiltiga. 

ID-token, åtkomsttoken och uppdaterade token cachelagras för den autentiserade sessionen och de är bara tillgängliga för den associerade användaren.  

Om du inte behöver arbeta med tokens i din app kan du inaktivera tokenarkivet på sidan **autentisering/auktorisering** i appen.

#### <a name="logging-and-tracing"></a>Loggning och spårning

Om du [aktiverar program loggning](troubleshoot-diagnostic-logs.md)ser du autentiserings-och verifierings spårningar direkt i dina loggfiler. Om du ser ett autentiseringsfel som du inte förväntade dig, kan du enkelt hitta all information genom att titta i dina befintliga program loggar. Om du aktiverar [spårning av misslyckade begär Anden](troubleshoot-diagnostic-logs.md)kan du se exakt vilken roll som modulen autentisering och auktorisering kan ha spelat i en misslyckad begäran. Leta efter referenser till en modul med namnet i spårnings loggarna `EasyAuthModule_32/64` .

## <a name="more-resources"></a>Fler resurser

- [Anvisningar: Konfigurera App Service eller Azure Functions appen för att använda Azure AD-inloggning](configure-authentication-provider-aad.md)
- [Avancerad användning av autentisering och auktorisering i Azure App Service](app-service-authentication-how-to.md)

Stickprov
- [Självstudie: Lägg till autentisering i din webbapp som körs på Azure App Service](scenario-secure-app-authentication-app-service.md)
- [Självstudie: autentisera och auktorisera användare från slut punkt till slut punkt i Azure App Service (Windows eller Linux)](tutorial-auth-aad.md)
- [.NET Core-integrering av Azure AppService EasyAuth (tredje part)](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)
- [Få Azure App Service autentisering arbeta med .NET Core (tredje part)](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication)
