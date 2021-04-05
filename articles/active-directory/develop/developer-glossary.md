---
title: Ord lista för Microsoft Identity Platform-utvecklare | Azure
description: En lista med villkor för vanliga koncept och funktioner för Microsoft Identity Platform-utvecklare.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.openlocfilehash: 77a4e7389952b81df13dae929dc1aec664fcc0b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755653"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Ord lista för Microsoft Identity Platform-utvecklare

Den här artikeln innehåller definitioner av några av de grundläggande koncepten och terminologin för utvecklare, som är användbara när du lär dig mer om program utveckling med Microsoft Identity Platform.

## <a name="access-token"></a>åtkomsttoken

En typ av [säkerhetstoken](#security-token) utfärdad av en [auktoriseringsprincip](#authorization-server)och används av ett [klient program](#client-application) för att få åtkomst till en [skyddad resurs Server](#resource-server). Vanligt vis i form av en [JSON Web token (JWT)][JWT], token återspeglar den auktorisering som beviljats till klienten av [resurs ägaren](#resource-owner), för en begärd åtkomst nivå. Token innehåller alla tillämpliga [anspråk](#claim) om ämnet, vilket gör att klient programmet kan använda det som en typ av autentiseringsuppgift vid åtkomst till en specifik resurs. Detta eliminerar också behovet av resurs ägaren att exponera autentiseringsuppgifter för klienten.

Åtkomsttoken är bara giltiga under en kort tids period och kan inte återkallas. En Authorization-Server kan också utfärda en [uppdateringstoken](#refresh-token) när åtkomsttoken utfärdas. Uppdaterings-token tillhandahålls vanligt vis endast för konfidentiella klient program.

Åtkomsttoken kallas ibland "användare + app" eller "endast app-only", beroende på vilka autentiseringsuppgifter som visas. Till exempel när ett klient program använder:

* [Auktoriserings koden "auktoriseringskod"](#authorization-grant), slutanvändaren autentiserar sig först som resurs ägare och delegerar auktoriseringen till klienten för att få åtkomst till resursen. Klienten autentiseras efteråt när åtkomsttoken hämtas. Token kan ibland kallas mer specifikt som "user + app"-token, eftersom den representerar både den användare som har auktoriserat klient programmet och programmet.
* Med [auktoriseringen "klientautentiseringsuppgifter"](#authorization-grant)ger klienten den enda autentiseringen, som fungerar utan resurs ägarens autentisering/auktorisering, så att token ibland kan kallas "endast app-only"-token.

Mer information finns i [referens för Microsoft Identity Platform-token][AAD-Tokens-Claims] .

## <a name="application-id-client-id"></a>program-ID (klient-ID)

Den unika identifieraren för Azure AD-problem med en program registrering som identifierar ett särskilt program och de associerade konfigurationerna. Detta program-ID ([klient-ID](https://tools.ietf.org/html/rfc6749#page-15)) används vid autentisering av autentiseringsbegäranden och tillhandahålls till autentiseringsinställningarna i utvecklings tiden. Program-ID: t (klient-ID) är inte ett hemligt objekt.

## <a name="application-manifest"></a>program manifest

En funktion som tillhandahålls av [Azure Portal][AZURE-portal], som skapar en JSON-representation av programmets identitets konfiguration, som används som en mekanism för att uppdatera dess associerade [program][Graph-App-Resource] -och [ServicePrincipal][Graph-Sp-Resource] -entiteter. Mer information finns i [förstå Azure Active Directory program manifestet][AAD-App-Manifest] .

## <a name="application-object"></a>program objekt

När du registrerar/uppdaterar ett program i [Azure Portal][AZURE-portal], skapar portalen/uppdaterar både ett program objekt och ett motsvarande [tjänst huvud objekt](#service-principal-object) för den klienten. Programobjektet *definierar* programmets identitets konfiguration globalt (för alla klienter där det har åtkomst), vilket ger en mall som dess motsvarande tjänst huvud objekt är *härledda* för användning lokalt vid körning (i en specifik klient).

Mer information finns i [objekt för program-och tjänst objekt][AAD-App-SP-Objects].

## <a name="application-registration"></a>programregistrering

För att göra det möjligt för ett program att integrera med och delegera identitets-och åtkomst hanterings funktioner till Azure AD måste det registreras med en Azure AD- [klient](#tenant). När du registrerar ditt program med Azure AD ger du en identitets konfiguration för ditt program, så att det kan integreras med Azure AD och använda funktioner som:

* Robust hantering av enskilda Sign-On med hjälp av Azure AD Identity Management och [OpenID Connect][OpenIDConnect] protocol implementation
* Broker-åtkomst till [skyddade resurser](#resource-server) av [klient program](#client-application)via OAuth 2,0- [auktoriseringsservern](#authorization-server)
* [Medgivande ramverk](#consent) för att hantera klient åtkomst till skyddade resurser, baserat på autentisering av resurs ägare.

Mer information finns i [integrera program med Azure Active Directory][AAD-Integrating-Apps] .

## <a name="authentication"></a>autentisering

Syftet med att begränsa en part för legitima autentiseringsuppgifter, och tillhandahålla grunden för att skapa ett säkerhets objekt som ska användas för identitets-och åtkomst kontroll. När en [OAuth2 beviljas](#authorization-grant) till exempel, fyller den part som autentiserar rollen som antingen [resurs ägare](#resource-owner) eller [klient program](#client-application), beroende på vilket bidrag som används.

## <a name="authorization"></a>auktorisering

Åtgärden att ge ett autentiserat säkerhets objekt behörighet att göra något. Det vinns två huvudsakliga typer i Azure AD-programmeringsmodellen:

* Under ett [OAuth2-auktoriseringsarkiv](#authorization-grant) : När [resurs ägaren](#resource-owner) ger tillåtelse till [klient programmet](#client-application), så att klienten får åtkomst till resurs ägarens resurser.
* Under resurs åtkomst av klienten: som implementeras av [resurs servern](#resource-server), med [anspråks](#claim) värden som finns i [åtkomsttoken för att fatta](#access-token) beslut om åtkomst kontroll baserat på dem.

## <a name="authorization-code"></a>auktoriseringskod

En kort livs längd "token" som tillhandahålls till ett [klient program](#client-application) av [behörighets slut punkten](#authorization-endpoint), som en del av flödet "auktoriseringskod", ett av de fyra OAuth2 för [auktorisering](#authorization-grant). Koden returneras till klient programmet som svar på autentisering av en [resurs ägare](#resource-owner), vilket indikerar att resurs ägaren har delegerat behörighet att komma åt de begärda resurserna. Som en del av flödet löses koden senare för en [åtkomsttoken](#access-token).

## <a name="authorization-endpoint"></a>behörighets slut punkt

En av slut punkterna som implementeras av [auktoriseringsservern](#authorization-server), används för att interagera med [resurs ägaren](#resource-owner) för att ge en [auktoriserad tilldelning](#authorization-grant) under ett OAuth2 Authorization Grant-flöde. Beroende på vilket flöde för auktoriserings tilldelning som används kan det faktiska bidraget variera, inklusive en [auktoriseringskod](#authorization-code) eller [säkerhetstoken](#security-token).

Mer information finns [i avsnitten om][OAuth2-AuthZ-Endpoint] [auktoriserings typer][OAuth2-AuthZ-Grant-Types] för OAuth2 och i avsnittet om [OpenIDConnect][OpenIDConnect-AuthZ-Endpoint] -specifikation.

## <a name="authorization-grant"></a>bevilja auktorisering

En autentiseringsuppgift som representerar [resurs ägarens](#resource-owner) [behörighet](#authorization) att komma åt dess skyddade resurser, beviljas till ett [klient program](#client-application). Ett klient program kan använda en av de [fyra tilldelnings typerna som definieras av OAuth2 Authorization Framework][OAuth2-AuthZ-Grant-Types] för att erhålla ett bidrag, beroende på klient typ/krav: "Authorization Code Grant", "klientautentiseringsuppgifter", "implicit beviljande" och "resurs ägarens lösen ord för autentiseringsuppgifter". Autentiseringsuppgiften som returneras till klienten är antingen en [åtkomsttoken](#access-token)eller en [auktoriseringskod](#authorization-code) (som utbyts senare för en åtkomsttoken), beroende på vilken typ av beviljande av auktorisering som används.

## <a name="authorization-server"></a>Authorization Server

Som definieras av [OAuth2 Authorization Framework][OAuth2-Role-Def], är servern ansvarig för att utfärda åtkomsttoken till [klienten](#client-application) efter att ha autentiserat [resurs ägaren](#resource-owner) och fått sin auktorisering. Ett [klient program](#client-application) interagerar med auktoriseringsservern vid körning via dess [auktoriserings](#authorization-endpoint) -och [token](#token-endpoint) -slutpunkter, i enlighet med de OAuth2 definierade [behörighets bidragen](#authorization-grant).

Om du använder Microsoft Identity Platform Application Integration implementerar Microsoft Identity Platform rollen auktoriseringsservern för Azure AD-program och API: er för Microsoft-tjänster, till exempel [Microsoft Graph API: er][Microsoft-Graph].

## <a name="claim"></a>Visa

En [säkerhetstoken](#security-token) innehåller anspråk, som anger intyg om en entitet (till exempel ett [klient program](#client-application) eller en [resurs ägare](#resource-owner)) till en annan entitet (till exempel [resurs servern](#resource-server)). Anspråk är namn/värde-par som vidarebefordrar fakta om token-ämnet (till exempel säkerhets objekt som autentiserats av [auktoriseringsservern](#authorization-server)). De anspråk som finns i en specifik token är beroende av flera variabler, inklusive typen av token, vilken typ av autentiseringsuppgift som används för att autentisera ämnet, program konfigurationen osv.

Mer information finns i [referens för Microsoft Identity Platform-token][AAD-Tokens-Claims] .

## <a name="client-application"></a>klient program

Som definieras av [OAuth2 Authorization Framework][OAuth2-Role-Def], ett program som gör skyddade resurs begär Anden åt [resurs ägarens](#resource-owner)räkning. Termen "klient" innebär inte några särskilda egenskaper för maskin varu implementering (till exempel om programmet körs på en server, en stationär dator eller andra enheter).

Ett klient program begär [tillstånd](#authorization) från en resurs ägare att delta i ett [OAuth2-auktoriseringsarkiv](#authorization-grant) , och kan komma åt API: er/data på resurs ägarens vägnar. OAuth2 Authorization Framework [definierar två typer av klienter][OAuth2-Client-Types], "konfidentiell" och "offentlig", baserat på klientens möjlighet att bibehålla sekretessen för sina autentiseringsuppgifter. Program kan implementera en [webb klient (konfidentiell)](#web-client) som körs på en webb server, en [intern klient (offentlig)](#native-client) som är installerad på en enhet eller en [klient baserad på användar agent (offentlig)](#user-agent-based-client) som körs i en enhets webbläsare.

## <a name="consent"></a>givit

Processen för en [resurs ägare](#resource-owner) som beviljar auktorisering till ett [klient program](#client-application)för att komma åt skyddade resurser under specifika [behörigheter](#permissions)för resurs ägarens räkning. Beroende på vilka behörigheter som begärs av klienten, kommer en administratör eller användare att be om medgivande för att tillåta åtkomst till respektive organisation/enskilda data. Obs! i ett scenario [med flera innehavare](#multi-tenant-application) registreras även programmets [tjänst objekt](#service-principal-object) i klient organisationen för den medgivande användaren.

Mer information finns i [medgivande ramverket](consent-framework.md) .

## <a name="id-token"></a>ID-token

En [OpenID Connect][OpenIDConnect-ID-Token] - [säkerhetstoken](#security-token) som tillhandahålls av en [Authorization-servers](#authorization-server) [Authorization-slutpunkt](#authorization-endpoint), som innehåller [anspråk](#claim) som rör autentiseringen av en slut användar [resurs ägare](#resource-owner). Precis som en åtkomsttoken representeras ID-tokens också som en digitalt signerad [JSON Web token (JWT)][JWT]. Till skillnad från en åtkomsttoken, används inte en ID-token-anspråk för syfte som är relaterade till resurs åtkomst och särskilt åtkomst kontroll.

Mer information finns i [referens för Microsoft Identity Platform-token][AAD-Tokens-Claims] .

## <a name="microsoft-identity-platform"></a>Microsoft identitetsplattform

Microsoft Identity Platform är en utveckling av den Azure Active Directory (Azure AD) Identity service och Developer Platform. Den hjälper utvecklare att bygga program som loggar in alla Microsoft-identiteter, får tokens för att anropa Microsoft Graph, andra Microsoft API:er eller API:er som utvecklare har byggt. Det är en komplett plattform som består av en autentiseringstjänst, bibliotek, program registrering och konfiguration, fullständig dokumentation om utvecklare, kod exempel och annat innehåll i utvecklare. Microsoft Identity-plattformen stöder branschstandardprotokoll som OAuth 2.0 och OpenID Connect.

## <a name="multi-tenant-application"></a>program för flera innehavare

En program klass som möjliggör inloggning och [godkännande](#consent) av användare som har upprättas i en Azure AD- [klient](#tenant), inklusive andra innehavare än den där klienten är registrerad. [Interna klient](#native-client) program är flera klienter som standard, medan [webb klient](#web-client) -och [webb resurs/API-](#resource-server) program kan välja mellan en eller flera innehavare. Ett webb program som är registrerat som en enskild klient tillåter dock bara inloggningar från användar konton som etablerades i samma klient organisation som det där programmet har registrerats.

Mer information finns i [så här loggar du in på valfri Azure AD-användare med program mönstret för flera innehavare][AAD-Multi-Tenant-Overview] .

## <a name="native-client"></a>intern klient

En typ av [klient program](#client-application) som installeras internt på en enhet. Eftersom all kod körs på en enhet betraktas den som en "offentlig" klient på grund av att det inte går att lagra autentiseringsuppgifter privat/konfidentiellt. Mer information finns i [OAuth2-klientens typer och profiler][OAuth2-Client-Types] .

## <a name="permissions"></a>behörigheter

Ett [klient program](#client-application) får åtkomst till en [resurs Server](#resource-server) genom att deklarera behörighets begär Anden. Det finns två typer:

* "Delegerade" behörigheter, som anger [scope-baserad](#scopes) åtkomst med delegerad auktorisering från den inloggade [resurs ägaren](#resource-owner), visas för resursen vid körning som ["SCP"-anspråk](#claim) i klientens [åtkomsttoken](#access-token).
* "Program"-behörigheter, som anger [rollbaserad](#roles) åtkomst med hjälp av klient programmets autentiseringsuppgifter/identitet, visas för resursen vid körnings tillfället som ["roles"-anspråk](#claim) i klientens åtkomsttoken.

De har också en yta under [medgivande](#consent) processen, vilket ger administratören eller resurs ägaren möjlighet att bevilja eller neka klient åtkomst till resurser i klienten.

Behörighets begär Anden konfigureras på sidan **API-behörigheter** för ett program i [Azure Portal][AZURE-portal]genom att välja önskade "delegerade behörigheter" och "program behörigheter" (den senare kräver medlemskap i den globala administratörs rollen). Eftersom en [offentlig klient](#client-application) inte kan upprätthålla autentiseringsuppgifter på ett säkert sätt kan den bara begära delegerade behörigheter, medan en [konfidentiell klient](#client-application) har möjlighet att begära både delegerade och program behörigheter. Klientens [program objekt](#application-object) lagrar de deklarerade behörigheterna i dess [requiredResourceAccess-egenskap][Graph-App-Resource].

## <a name="refresh-token"></a>uppdatera token

En typ av [säkerhetstoken](#security-token) som utfärdats av en [Authorization Server](#authorization-server)och som används av ett [klient program](#client-application) för att begära [en ny åtkomsttoken](#access-token) innan åtkomsttoken upphör att gälla. Vanligt vis i form av en [JSON Web token (JWT)][JWT].

Till skillnad från åtkomsttoken kan du återkalla uppdatering av token. Om ett klient program försöker begära en ny åtkomsttoken med en uppdateringstoken som har återkallats, kommer auktoriseringsservern att neka begäran och klient programmet kommer inte längre ha behörighet att komma åt [resurs servern](#resource-server) för [resurs ägarens](#resource-owner)räkning.

## <a name="resource-owner"></a>resurs ägare

Som definieras av [OAuth2 Authorization Framework][OAuth2-Role-Def], en entitet som kan bevilja åtkomst till en skyddad resurs. När resurs ägaren är en person kallas den för en slutanvändare. Till exempel, när ett [klient program](#client-application) vill få åtkomst till en användares post låda via [Microsoft Graph-API][Microsoft-Graph], krävs behörighet från resurs ägaren till post lådan.

## <a name="resource-server"></a>resurs Server

Som definieras av [OAuth2 Authorization Framework][OAuth2-Role-Def], en server som är värd för skyddade resurser, som kan acceptera och svara på skyddade resurs begär Anden från [klient program](#client-application) som visar en [åtkomsttoken](#access-token). Kallas även för en skyddad resurs Server eller resurs program.

En resurs Server exponerar API: er och tvingar åtkomst till sina skyddade resurser via [omfattningar](#scopes) och [roller](#roles)med hjälp av auktoriserings ramverket för OAuth 2,0. Exempel på detta är [Microsoft Graph-API][Microsoft-Graph] som ger åtkomst till Azure AD-klientens data och de Microsoft 365-API: er som ger åtkomst till data, till exempel e-post och kalender.

Precis som ett klient program upprättas resurs programmets identitets konfiguration via [registrering](#application-registration) i en Azure AD-klient, vilket ger både program-och tjänstens huvud objekt. Vissa API: er från Microsoft, till exempel Microsoft Graph API, har förregistrerade tjänst huvud namn som är tillgängliga i alla klienter under etableringen.

## <a name="roles"></a>roller

Precis som [omfång](#scopes)ger roller ett sätt för en [resurs Server](#resource-server) att styra åtkomsten till dess skyddade resurser. Det finns två typer: en "användar roll" implementerar rollbaserad åtkomst kontroll för användare/grupper som behöver åtkomst till resursen, medan en program roll implementerar samma för [klient program](#client-application) som kräver åtkomst.

Roller är resurs definierade strängar (till exempel "utgifts god kännare", "skrivskyddad", "Directory. ReadWrite. all"), som hanteras i [Azure Portal][AZURE-portal] via resursens [program manifest](#application-manifest)och lagras i resursens [appRoles-egenskap][Graph-Sp-Resource]. Azure Portal används också för att tilldela användare till "User"-roller och konfigurera klient [program behörigheter](#permissions) för åtkomst till en "program"-roll.

En detaljerad beskrivning av de program roller som exponeras av Microsoft Graph API finns i [Graph API behörighets omfattningar][Graph-Perm-Scopes]. Ett steg-för-steg-implementerings exempel finns i [lägga till eller ta bort Azure Role-tilldelningar med hjälp av Azure Portal][AAD-RBAC].

## <a name="scopes"></a>scopes

Precis som med [roller](#roles)ger omfattningar ett sätt för en [resurs Server](#resource-server) att styra åtkomsten till dess skyddade resurser. Omfattningar används för att implementera [begränsad][OAuth2-Access-Token-Scopes] åtkomst kontroll, för ett [klient program](#client-application) som har fått delegerad åtkomst till resursen av dess ägare.

Omfattningar är resurs definierade strängar (till exempel "mail. Read", "Directory. ReadWrite. all"), hanteras i [Azure Portal][AZURE-portal] via resursens [program manifest](#application-manifest)och lagras i resursens [oauth2Permissions-egenskap][Graph-Sp-Resource]. Azure Portal används också för att konfigurera klient programmet [delegerade behörigheter](#permissions) för åtkomst till en omfattning.

En metod för att namnge rekommendationer är att använda formatet "Resource. operation. constraint". En detaljerad beskrivning av de omfång som exponeras av Microsoft Graph API finns i [Graph API behörighets omfattningar][Graph-Perm-Scopes]. För omfattningar som exponeras av Microsoft 365 Services, se [referens för Microsoft 365-API-behörigheter][O365-Perm-Ref].

## <a name="security-token"></a>säkerhetstoken

Ett signerat dokument som innehåller anspråk, till exempel en OAuth2-token eller SAML 2,0-kontroll. För en OAuth2 [Authorization-beviljande](#authorization-grant), är en [åtkomsttoken](#access-token) (OAuth2), [uppdateringstoken](#refresh-token)och en [ID-token](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) typer av säkerhetstoken, som alla implementeras som en [JSON Web token (JWT)][JWT].

## <a name="service-principal-object"></a>tjänstens huvud namns objekt

När du registrerar/uppdaterar ett program i [Azure Portal][AZURE-portal], skapar portalen/uppdaterar både ett [program objekt](#application-object) och ett motsvarande tjänst huvud objekt för den klienten. Programobjektet *definierar* programmets identitets konfiguration globalt (för alla klienter där det associerade programmet har beviljats åtkomst) och är den mall som tillhör ande tjänstens huvud objekt *härleds* för användning lokalt vid körning (i en specifik klient).

Mer information finns i [objekt för program-och tjänst objekt][AAD-App-SP-Objects].

## <a name="sign-in"></a>sign-in

Processen för ett [klient program](#client-application) som initierar autentisering av slutanvändare och fångar relaterat tillstånd, i syfte att förvärva en [säkerhetstoken](#security-token) och omfånget för programsessionen till det aktuella läget. Tillstånd kan innehålla artefakter som användar profil information och information som härletts från token-anspråk.

Inloggnings funktionen för ett program används vanligt vis för att implementera enkel inloggning (SSO). Den kan också föregås av en "registrerings funktion", som start punkt för en slutanvändare för att få åtkomst till ett program (vid första inloggningen). Registrerings funktionen används för att samla in och bevara ytterligare tillstånd som är särskilt för användaren, och det kan kräva [användar medgivande](#consent).

## <a name="sign-out"></a>utloggning

Processen för att Avautentisera en slutanvändare som kopplar bort det användar tillstånd som är associerat med [klient program](#client-application) sessionen under [inloggningen](#sign-in)

## <a name="tenant"></a>tenant

En instans av en Azure AD-katalog kallas en Azure AD-klient. Den innehåller flera funktioner, inklusive:

* en register tjänst för integrerade program
* autentisering av användar konton och registrerade program
* REST-slutpunkter som krävs för att stödja olika protokoll, inklusive OAuth2 och SAML, inklusive slut punkt för [behörighet](#authorization-endpoint), [token-slutpunkt](#token-endpoint) och den "vanliga" slut punkt som används av [program med flera innehavare](#multi-tenant-application).

Azure AD-klienter skapas/associeras med Azure och Microsoft 365 prenumerationer under registreringen, vilket ger identitets & åtkomst hanterings funktioner för prenumerationen. Azure-prenumerations administratörer kan också skapa ytterligare Azure AD-klienter via Azure Portal. Se [hur du får en Azure Active Directory-klient][AAD-How-To-Tenant] för information om de olika sätt som du kan få åtkomst till en klient. Se [associera eller lägga till en Azure-prenumeration till din Azure Active Directory-klient][AAD-How-Subscriptions-Assoc] om du vill ha mer information om relationen mellan prenumerationer och en Azure AD-klient och anvisningar om hur du associerar eller lägger till en prenumeration i en Azure AD-klient.

## <a name="token-endpoint"></a>token-slutpunkt

En av slut punkterna som implementeras av [auktoriseringsservern](#authorization-server) för att stödja OAuth2- [auktorisering](#authorization-grant). Beroende på tilldelningen kan den användas för att hämta en åtkomsttoken (och relaterad "Refresh" [-token](#access-token) ) till en [klient](#client-application), eller [ID-token](#id-token) när den används med [OpenID Connect][OpenIDConnect] -protokollet.

## <a name="user-agent-based-client"></a>Användar agent-baserad klient

En typ av [klient program](#client-application) som laddar ned kod från en webb server och körs i en användar agent (till exempel en webbläsare), t. ex. ett enda webb program (Spa). Eftersom all kod körs på en enhet betraktas den som en "offentlig" klient på grund av att det inte går att lagra autentiseringsuppgifter privat/konfidentiellt. Mer information finns i [OAuth2-klient typer och-profiler][OAuth2-Client-Types].

## <a name="user-principal"></a>användarens huvud namn

På liknande sätt som ett tjänst huvud objekt används för att representera en program instans, är ett huvud objekt för användare en annan typ av säkerhets objekt, som representerar en användare. Den Microsoft Graph [användarens resurs typ][Graph-User-Resource] definierar schemat för ett användar objekt, inklusive användarspecifika egenskaper, till exempel för-och efter namn, User Principal Name, katalog roll medlemskap osv. Detta ger användar identitets konfigurationen för Azure AD för att upprätta ett huvud konto för användare vid körning. Användarens huvud namn används för att representera en autentiserad användare för enkel inloggning, registrering av [medgivande](#consent) , att fatta beslut om åtkomst kontroll osv.

## <a name="web-client"></a>webb klient

En typ av [klient program](#client-application) som kör all kod på en webb server och kan fungera som en "konfidentiell" klient genom att säkert lagra dess autentiseringsuppgifter på servern. Mer information finns i [OAuth2-klient typer och-profiler][OAuth2-Client-Types].

## <a name="next-steps"></a>Nästa steg

[Microsoft Identity Platform Developer-guiden][AAD-Dev-Guide] är den landnings sida som används för alla Microsoft Identity Platform Development-relaterade ämnen, inklusive en översikt över [program integrering][AAD-How-To-Integrate] och grunderna för autentisering med [Microsoft Identity Platform och stöd för autentisering][AAD-Auth-Scenarios]. Du kan också hitta kod exempel & själv studie kurser om hur du snabbt kommer igång med [GitHub](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=).

Använd följande kommentarer-avsnitt för att ge feedback och hjälp med att förfina och forma det här innehållet, inklusive begär Anden om nya definitioner eller uppdatering av befintliga.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[Graph-Perm-Scopes]: /graph/permissions-reference
[Graph-App-Resource]: /graph/api/resources/application
[Graph-Sp-Resource]: /graph/api/resources/serviceprincipal?view=graph-rest-beta&preserve-view=true
[Graph-User-Resource]: /graph/api/resources/user
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]:howto-convert-app-to-be-multi-tenant.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: /graph/permissions-reference
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken