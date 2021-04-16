---
title: Ordlista för microsoft identity platform developer | Azure
description: En lista över termer för vanliga begrepp och funktioner för microsoft-identitetsplattformsutvecklare.
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
ms.openlocfilehash: 930341b60f785c2c618be4ee235225519a08aaa6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530059"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Ordlista för utvecklare av Microsoft Identity Platform

Den här artikeln innehåller definitioner för några av de viktigaste utvecklarbegreppen och terminologin, som är användbara när du lär dig mer om programutveckling med Microsoft Identity Platform.

## <a name="access-token"></a>åtkomsttoken

En typ av [säkerhetstoken](#security-token) som utfärdats av en [auktoriseringsserver](#authorization-server)och som används av ett [klientprogram](#client-application) för att få åtkomst till [en skyddad resursserver](#resource-server). I form av [en JSON Web Token (JWT)][JWT]innehåller token vanligtvis den auktorisering som beviljats till klienten av resursägaren [för](#resource-owner)en begärd åtkomstnivå. Token innehåller alla tillämpliga anspråk om ämnet, vilket gör att klientprogrammet kan använda det som en form av [autentiseringsuppgifter](#claim) vid åtkomst till en viss resurs. Detta eliminerar också behovet av att resursägaren exponerar autentiseringsuppgifter för klienten.

Åtkomsttoken är bara giltiga under en kort tidsperiod och kan inte återkallas. En auktoriseringsserver kan också [utfärda en uppdateringstoken](#refresh-token) när åtkomsttoken utfärdas. Uppdateringstoken tillhandahålls vanligtvis endast till konfidentiella klientprogram.

Åtkomsttoken kallas ibland "Användare+app" eller "Endast app", beroende på vilka autentiseringsuppgifter som representeras. Till exempel när ett klientprogram använder:

* [Auktoriseringskod beviljar](#authorization-grant), slutanvändaren autentiseras först som resursägare, vilket delegerar auktorisering till klienten för att få åtkomst till resursen. Klienten autentiserar efteråt när åtkomsttoken inhämtas. Token kan ibland refereras till mer specifikt som en "Användare+App"-token, eftersom den representerar både den användare som auktoriserat klientprogrammet och programmet.
* ["Klientautentiseringsuppgifter"](#authorization-grant)beviljar , klienten tillhandahåller den enda autentiseringen som fungerar utan resursägarens autentisering/auktorisering, så att token ibland kan kallas en "app-only"-token.

Mer information [finns i Tokenreferens för Microsoft][AAD-Tokens-Claims] Identity Platform.

## <a name="application-id-client-id"></a>program-ID (klient-ID)

Den unika identifieraren som Azure AD utfärdar till en programregistrering som identifierar ett specifikt program och tillhörande konfigurationer. Det här[program-ID:t (klient-ID)](https://tools.ietf.org/html/rfc6749#page-15)används när autentiseringsförfrågningar och tillhandahålls till autentiseringsbiblioteken under utvecklingstiden. Program-ID :t (klient-ID) är inte en hemlighet.

## <a name="application-manifest"></a>programmanifest

En funktion som tillhandahålls [av Azure Portal][AZURE-portal], som skapar en JSON-representation av programmets identitetskonfiguration, som används som en mekanism för att uppdatera dess associerade [Application-][Graph-App-Resource] och [ServicePrincipal-entiteter.][Graph-Sp-Resource] Mer [information finns i Förstå Azure Active Directory-programmanifestet.][AAD-App-Manifest]

## <a name="application-object"></a>programobjekt

När du registrerar/uppdaterar ett program [i Azure Portal][AZURE-portal]skapar/uppdaterar portalen både ett programobjekt och ett motsvarande tjänsthuvudnamnsobjekt för den klientorganisationen. [](#service-principal-object) Programobjektet  definierar programmets identitetskonfiguration globalt (för alla klienter där det har åtkomst), vilket ger en  mall som dess motsvarande tjänsthuvudnamnsobjekt härleds från för användning lokalt vid körning (i en specifik klient).

Mer information finns i [Program- och tjänsthuvudnamnsobjekt.][AAD-App-SP-Objects]

## <a name="application-registration"></a>programregistrering

För att ett program ska kunna integreras med och delegera identitets- och åtkomsthanteringsfunktioner till Azure AD måste det registreras med en Azure [AD-klientorganisation.](#tenant) När du registrerar ditt program med Azure AD tillhandahåller du en identitetskonfiguration för ditt program så att det kan integreras med Azure AD och använda funktioner som:

* Robust hantering av enskilda Sign-On med Azure AD Identity Management och [OpenID Connect][OpenIDConnect] protokollimplementering
* A brokered access to [protected resources](#resource-server) by [client applications](#client-application), via OAuth 2.0 authorization [server](#authorization-server)
* [Ramverk för medgivande](#consent) för hantering av klientåtkomst till skyddade resurser, baserat på resursägarens auktorisering.

Se [Integrera program med Azure Active Directory][AAD-Integrating-Apps] för mer information.

## <a name="authentication"></a>autentisering

En part måste ange giltiga autentiseringsuppgifter, vilket utgör grunden för att skapa ett säkerhetsobjekt som ska användas för identitets- och åtkomstkontroll. Under till [exempel ett OAuth2-auktoriseringstilldelning](#authorization-grant) fyller [](#resource-owner) den part som autentiserar rollen som resursägare eller klientprogram, beroende på vilket beviljande som används. [](#client-application)

## <a name="authorization"></a>auktorisering

Att bevilja en autentiserad säkerhetsobjekt behörighet att göra något. Det vinns två huvudsakliga typer i Azure AD-programmeringsmodellen:

* Under ett flöde för beviljande av [](#resource-owner) [OAuth2-auktorisering:](#authorization-grant) när resursägaren beviljar auktorisering till klientprogrammet [så](#client-application)att klienten får åtkomst till resursägarens resurser.
* Under resursåtkomst av klienten: enligt implementeras av [](#claim) resursservern [,](#resource-server)med anspråksvärden som finns i åtkomsttoken för att fatta beslut om åtkomstkontroll baserat på dem. [](#access-token)

## <a name="authorization-code"></a>auktoriseringskod

En kortlivad "token" [](#client-application) som tillhandahålls till ett klientprogram av auktoriseringsslutpunkten [som](#authorization-endpoint)en del av flödet för "auktoriseringskod" beviljar en av de fyra OAuth2-auktoriseringarna . [](#authorization-grant) Koden returneras till klientprogrammet som svar [](#resource-owner)på autentisering av en resursägare, vilket anger att resursägaren har delegerat behörighet att komma åt de begärda resurserna. Som en del av flödet löses koden in senare mot en [åtkomsttoken](#access-token).

## <a name="authorization-endpoint"></a>auktoriseringsslutpunkt

En av de slutpunkter som implementeras av [](#resource-owner) auktoriseringsservern [används](#authorization-server)för att interagera med resursägaren för att ge ett auktoriseringstilldelning under ett OAuth2-auktoriseringstilldelningsflöde. [](#authorization-grant) Beroende på vilket flöde för auktoriseringsbektoring som används kan det faktiska beviljandet variera, inklusive en [auktoriseringskod](#authorization-code) eller [säkerhetstoken](#security-token).

Mer information finns i avsnitten om [][OAuth2-AuthZ-Endpoint] OAuth2-specifikationens auktoriseringstyper och auktoriseringsslutpunkt samt [][OAuth2-AuthZ-Grant-Types] [OpenIDConnect-specifikationen.][OpenIDConnect-AuthZ-Endpoint]

## <a name="authorization-grant"></a>auktoriserings beviljande

En autentiseringsbehörighet som representerar [resursägarens behörighet att komma](#resource-owner) [](#authorization) åt dess skyddade resurser, beviljad till ett [klientprogram](#client-application). Ett klientprogram kan använda någon av de fyra beviljandetyperna som definieras av [OAuth2 Authorization Framework][OAuth2-AuthZ-Grant-Types] för att få ett beviljande, beroende på klienttyp/krav: "beviljande av auktoriseringskod", "beviljande av klientautentiseringsuppgifter", "implicit beviljande" och "beviljande av autentiseringsuppgifter för resursägares lösenord". De autentiseringsuppgifter som returneras till klienten är antingen en åtkomsttoken [eller](#access-token)en auktoriseringskod [(byts](#authorization-code) senare mot en åtkomsttoken), beroende på vilken typ av auktoriseringsbektoring som används.

## <a name="authorization-server"></a>auktoriseringsserver

Enligt [OAuth2 Authorization Framework][OAuth2-Role-Def], ansvarar servern för att utfärda [](#client-application) åtkomsttoken till [](#resource-owner) klienten efter att ha autentiserat resursägaren och fått dess auktorisering. Ett [klientprogram interagerar](#client-application) med auktoriseringsservern vid körning via dess auktoriserings- och [tokenslutpunkter,](#token-endpoint) i enlighet med OAuth2-definierade [auktoriseringsbegöd.](#authorization-grant) [](#authorization-endpoint)

När det gäller Microsoft Identity Platform-programintegrering implementerar Microsoft Identity-plattformen auktoriseringsserverrollen för Azure AD-program och Api:er för Microsoft-tjänster, till exempel [Microsoft Graph API:er][Microsoft-Graph].

## <a name="claim"></a>Hävdar

En [säkerhetstoken](#security-token) innehåller anspråk som tillhandahåller försäkran om [](#client-application) en entitet (till exempel ett klientprogram eller [resursägare](#resource-owner)) till en annan entitet (till [exempel resursservern](#resource-server)). Anspråk är namn-/värdepar som vidarebefordrar fakta om tokenämnet (till exempel [](#authorization-server)säkerhetsobjekt som autentiserades av auktoriseringsservern ). Anspråken som finns i en viss token är beroende av flera variabler, inklusive typ av token, vilken typ av autentiseringsuppgifter som används för att autentisera ämnet, programkonfigurationen osv.

Mer information [finns i tokenreferensen för Microsoft][AAD-Tokens-Claims] Identity Platform.

## <a name="client-application"></a>klientprogram

Enligt [OAuth2 Authorization Framework][OAuth2-Role-Def]definieras ett program som gör begäranden om skyddade resurser för [resursägarens räkning.](#resource-owner) Termen "klient" innebär inte några specifika maskinvaruimplementeringsegenskaper (till exempel om programmet körs på en server, ett skrivbord eller andra enheter).

Ett klientprogram begär [auktorisering](#authorization) från en resursägare för att delta i ett beviljandeflöde för [OAuth2-auktorisering](#authorization-grant) och kan komma åt API:er/data för resursägarens räkning. OAuth2 Authorization [Framework][OAuth2-Client-Types]definierar två typer av klienter , "konfidentiella" och "offentliga", baserat på klientens möjlighet att upprätthålla sekretessen för sina autentiseringsuppgifter. Program kan implementera en webbklient [(konfidentiell)](#web-client) som körs på en webbserver, en intern klient [(offentlig)](#native-client) som är installerad på en enhet eller en [användaragentbaserad klient (offentlig)](#user-agent-based-client) som körs i en enhets webbläsare.

## <a name="consent"></a>Samtycke

Processen för en [resursägare som beviljar](#resource-owner) auktorisering till ett klientprogram [,](#client-application)för att få åtkomst till skyddade resurser under specifika behörigheter [,](#permissions)för resursägarens räkning. Beroende på vilka behörigheter som begärs av klienten uppmanas en administratör eller användare att ge sitt medgivande för att tillåta åtkomst till deras organisation/enskilda data. Observera att i [ett scenario med flera](#multi-tenant-application) klienter registreras programmets tjänsthuvudnamn också i klienten för den medgivande användaren. [](#service-principal-object)

Mer information [finns i Ramverk](consent-framework.md) för medgivande.

## <a name="id-token"></a>ID-token

En [OpenID Connect][OpenIDConnect-ID-Token] [säkerhetstoken](#security-token) som [](#authorization-server) tillhandahålls av auktoriseringsserverns auktoriseringsslutpunkt [,](#authorization-endpoint)som innehåller anspråk som rör autentisering av en [slutanvändares](#claim) [resursägare](#resource-owner). Precis som en åtkomsttoken representeras ID-token också som en digitalt [signerad JSON Web Token (JWT).][JWT] Till skillnad från en åtkomsttoken används dock inte anspråk för en ID-token för syften som rör resursåtkomst och specifikt åtkomstkontroll.

Mer information [finns i tokenreferensen för Microsoft][AAD-Tokens-Claims] Identity Platform.

## <a name="microsoft-identity-platform"></a>Microsoft identitetsplattform

Microsoft Identity Platform är en utveckling av identitetstjänsten Azure Active Directory (Azure AD) och utvecklarplattformen. Den hjälper utvecklare att bygga program som loggar in alla Microsoft-identiteter, får tokens för att anropa Microsoft Graph, andra Microsoft API:er eller API:er som utvecklare har byggt. Det är en komplett plattform som består av en autentiseringstjänst, bibliotek, programregistrering och konfiguration, fullständig utvecklardokumentation, kodexempel och annat utvecklarinnehåll. Microsoft Identity-plattformen stöder branschstandardprotokoll som OAuth 2.0 och OpenID Connect.

## <a name="multi-tenant-application"></a>program för flera innehavare

En klass med program som [](#consent) möjliggör inloggning och medgivande av användare som etablerats i valfri Azure [](#tenant)AD-klientorganisation, inklusive andra klienter än den där klienten är registrerad. [Interna klientprogram](#native-client) är multiklient som [](#web-client) standard, medan webbklient- och [webbresurs-/API-program](#resource-server) kan välja mellan en eller flera klienter. Däremot skulle ett webbprogram som registrerats som en enda klientorganisation endast tillåta inloggningar från användarkonton som etablerats i samma klientorganisation som den där programmet har registrerats.

Mer information finns i Så här loggar du [in en Azure AD-användare][AAD-Multi-Tenant-Overview] med programmönstret för flera innehavare.

## <a name="native-client"></a>intern klient

En typ av [klientprogram](#client-application) som installeras inbyggt på en enhet. Eftersom all kod körs på en enhet betraktas den som en "offentlig" klient eftersom den inte kan lagra autentiseringsuppgifter privat/konfidentiellt. Mer [information finns i OAuth2-klienttyper][OAuth2-Client-Types] och -profiler.

## <a name="permissions"></a>behörigheter

Ett [klientprogram får](#client-application) åtkomst till en [resursserver genom att](#resource-server) deklarera behörighetsbegäranden. Det finns två typer:

* "Delegerade" behörigheter, [](#scopes) som anger omfångsbaserad åtkomst med delegerad auktorisering från den [inloggade](#resource-owner)resursägaren , visas för resursen vid körning som ["scp"-anspråk](#claim) i klientens [åtkomsttoken](#access-token).
* Programbehörigheter, som [](#roles) anger rollbaserad åtkomst med hjälp av klientprogrammets autentiseringsuppgifter/identitet, visas för resursen vid körning som ["rollanspråk"](#claim) i klientens åtkomsttoken.

De visar också under [medgivandeprocessen,](#consent) vilket ger administratören eller resursägaren möjlighet att bevilja/neka klientåtkomst till resurser i klientorganisationen.

Behörighetsbegäranden konfigureras på **sidan API-behörigheter** för ett program i [Azure Portal][AZURE-portal]genom att välja önskade "Delegerade behörigheter" och "Programbehörigheter" (det senare kräver medlemskap i rollen Global administratör). Eftersom en [offentlig klient](#client-application) inte på ett säkert sätt kan underhålla [](#client-application) autentiseringsuppgifter kan den bara begära delegerade behörigheter, medan en konfidentiell klient kan begära både delegerade behörigheter och programbehörigheter. Klientens programobjekt [lagrar de deklarerade](#application-object) behörigheterna i dess [requiredResourceAccess-egenskap][Graph-App-Resource].

## <a name="refresh-token"></a>uppdatera token

En typ av [säkerhetstoken](#security-token) som utfärdats [](#client-application) av en auktoriseringsserver [och](#authorization-server)som används av ett klientprogram för att begära en ny åtkomsttoken innan åtkomsttoken upphör att gälla. [](#access-token) Vanligtvis i form av en [JSON Web Token (JWT).][JWT]

Till skillnad från åtkomsttoken kan uppdateringstoken återkallas. Om ett klientprogram försöker begära en ny åtkomsttoken med hjälp av en uppdateringstoken som har återkallats nekar [](#resource-server) auktoriseringsservern begäran och klientprogrammet har inte längre behörighet att komma åt resursservern för resursägarens [räkning.](#resource-owner)

## <a name="resource-owner"></a>resursägare

Enligt [OAuth2 Authorization Framework definieras en][OAuth2-Role-Def]entitet som kan bevilja åtkomst till en skyddad resurs. När resursägaren är en person kallas den för en slutanvändare. Till exempel när ett [klientprogram](#client-application) vill komma åt en användares postlåda via [Microsoft Graph API,][Microsoft-Graph]krävs behörighet från resursägaren för postlådan.

## <a name="resource-server"></a>resursserver

Enligt [OAuth2 Authorization Framework][OAuth2-Role-Def], en server som är värd för skyddade resurser, som kan acceptera och svara på begäranden om skyddade resurser av klientprogram som presenterar en [åtkomsttoken](#access-token). [](#client-application) Kallas även för en skyddad resursserver eller resursprogram.

En resursserver exponerar API:er och tillämpar [](#scopes) åtkomst till sina skyddade resurser via omfång och roller [med](#roles)hjälp av OAuth 2.0 Authorization Framework. Exempel är [Microsoft Graph-API:et][Microsoft-Graph] som ger åtkomst till Azure AD-klientdata och Microsoft 365-API:er som ger åtkomst till data som e-post och kalender.

Precis som ett klientprogram upprättas resursprogrammets identitetskonfiguration [via](#application-registration) registrering i en Azure AD-klientorganisation som tillhandahåller både programmets och tjänstens huvudnamnsobjekt. Vissa API:er från Microsoft, till exempel API:Microsoft Graph, har förregistrerade tjänsthuvudnamn tillgängliga i alla klienter under etableringen.

## <a name="roles"></a>roller

Precis [som omfång](#scopes)är roller ett sätt för en [resursserver](#resource-server) att styra åtkomsten till sina skyddade resurser. Det finns två typer: en "användarroll" implementerar rollbaserad åtkomstkontroll för användare/grupper som kräver åtkomst till [](#client-application) resursen, medan en "programroll" implementerar samma sak för klientprogram som kräver åtkomst.

Roller är resursdefinierade strängar (till exempel "utgifts godkännare", "Skrivskyddad", "Directory.ReadWrite.All"), hanteras i [Azure Portal][AZURE-portal] via resursens programmanifest [och](#application-manifest)lagras i resursens [appRoles-egenskap][Graph-Sp-Resource]. Den Azure Portal används också för att tilldela användare till "användarroller" och konfigurera klientprogrambehörigheter för åtkomst till en "programroll". [](#permissions)

En detaljerad beskrivning av de programroller som exponeras av Microsoft Graph-API:et [finns i Graph API behörighetsomfång][Graph-Perm-Scopes]. Stegvisa implementeringsexempel finns i Lägga till eller ta bort [Azure-rolltilldelningar med hjälp av Azure Portal][AAD-RBAC].

## <a name="scopes"></a>scopes

Precis [som](#roles)roller är omfång ett sätt för en [resursserver](#resource-server) att styra åtkomsten till dess skyddade resurser. Omfång används för att [implementera omfångsbaserad][OAuth2-Access-Token-Scopes] åtkomstkontroll för ett klientprogram som har fått delegerad åtkomst till resursen av dess ägare. [](#client-application)

Omfång är resursdefinierade strängar (till exempel "Mail.Read", "Directory.ReadWrite.All"), som hanteras i [Azure Portal][AZURE-portal] via resursens programmanifest [och](#application-manifest)lagras i resursens [oauth2Permissions-egenskap][Graph-Sp-Resource]. Den Azure Portal används också för att konfigurera klientprogram-delegerade [behörigheter för åtkomst](#permissions) till ett omfång.

En bra namngivningskonvention är att använda formatet "resource.operation.constraint". En detaljerad beskrivning av de omfång som exponeras av Microsoft Graph API finns i [Graph API behörighetsomfång][Graph-Perm-Scopes]. Information om omfång som exponeras av Microsoft 365-tjänster finns [i Microsoft 365 api-behörighetsreferens.][O365-Perm-Ref]

## <a name="security-token"></a>säkerhetstoken

Ett signerat dokument som innehåller anspråk, till exempel en OAuth2-token eller SAML 2.0-försäkran. För en OAuth2-auktoriseringsbektoring är en åtkomsttoken (OAuth2), uppdateringstoken och en [ID-token](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) typer av säkerhetstoken, som alla implementeras som [en JSON Web Token (JWT).][JWT] [](#authorization-grant) [](#access-token) [](#refresh-token)

## <a name="service-principal-object"></a>objekt för tjänstens huvudnamn

När du registrerar/uppdaterar ett program [i Azure Portal][AZURE-portal]skapar/uppdaterar [](#application-object) portalen både ett programobjekt och ett motsvarande tjänsthuvudnamnsobjekt för den klientorganisationen. Programobjektet  definierar programmets identitetskonfiguration globalt (i alla klienter där det associerade programmet har beviljats åtkomst) och är  den mall som dess motsvarande tjänsthuvudnamnsobjekt härleds från för användning lokalt vid körning (i en specifik klient).

Mer information finns i [Program- och tjänsthuvudnamnsobjekt.][AAD-App-SP-Objects]

## <a name="sign-in"></a>sign-in

Processen för ett [klientprogram](#client-application) som initierar slutanvändarautentisering och samlar [](#security-token) in relaterat tillstånd i syfte att hämta en säkerhetstoken och ange omfång för programsessionen till det tillståndet. Tillstånd kan innehålla artefakter som användarprofilinformation och information som härleds från tokenanspråk.

Inloggningsfunktionen för ett program används vanligtvis för att implementera enkel inloggning (SSO). Det kan också föregås av en "registrering"-funktion, som startpunkt för en slutanvändare för att få åtkomst till ett program (vid första inloggningen). Registreringsfunktionen används för att samla in och bevara ytterligare tillstånd som är specifikt för användaren och kan kräva [användarens medgivande.](#consent)

## <a name="sign-out"></a>utloggning

Processen att avauthenticera en slutanvändare, koppla från användartillståndet som associeras med klientprogramsessionen [under inloggningen](#sign-in) [](#client-application)

## <a name="tenant"></a>tenant

En instans av en Azure AD-katalog kallas en Azure AD-klientorganisation. Den innehåller flera funktioner, bland annat:

* en registertjänst för integrerade program
* autentisering av användarkonton och registrerade program
* REST-slutpunkter som krävs för att stödja olika protokoll, [](#authorization-endpoint)inklusive OAuth2 och SAML, inklusive auktoriseringsslutpunkten, [tokenslutpunkten](#token-endpoint) och den "gemensamma" slutpunkten som används av [program med flera innehavare.](#multi-tenant-application)

Azure AD-klienter skapas/associeras med Azure och Microsoft 365-prenumerationer under registrering, vilket ger Identity &-åtkomsthanteringsfunktioner för prenumerationen. Azure-prenumerationsadministratörer kan också skapa ytterligare Azure AD-klienter via Azure Portal. Information [om hur du kan få Azure Active Directory klientorganisation][AAD-How-To-Tenant] finns i Så här hämtar du en klientorganisation. Mer [information om relationen][AAD-How-Subscriptions-Assoc] mellan prenumerationer och en Azure AD-klientorganisation finns i Associera eller lägga till en Azure-prenumeration till din Azure Active Directory-klientorganisation och anvisningar om hur du associerar eller lägger till en prenumeration i en Azure AD-klientorganisation.

## <a name="token-endpoint"></a>tokenslutpunkt

En av de slutpunkter som implementeras av [auktoriseringsservern för](#authorization-server) att stödja OAuth2-auktorisering [beviljar](#authorization-grant). Beroende på beviljandet kan den användas för att hämta en åtkomsttoken [(och](#access-token) relaterad "uppdatera"-token) till en klient [eller](#client-application) [ID-token](#id-token) när den används [med OpenID Connect-protokollet.][OpenIDConnect]

## <a name="user-agent-based-client"></a>Användaragentbaserad klient

En typ [av](#client-application) klientprogram som laddar ned kod från en webbserver och körs inom en användaragent (till exempel en webbläsare), till exempel en ensidesapplikation (SPA). Eftersom all kod körs på en enhet betraktas den som en "offentlig" klient eftersom den inte kan lagra autentiseringsuppgifter privat/konfidentiellt. Mer information finns i [OAuth2-klienttyper och -profiler.][OAuth2-Client-Types]

## <a name="user-principal"></a>användarens huvudnamn

På samma sätt som ett tjänsthuvudnamnsobjekt används för att representera en programinstans är ett användarobjekt en annan typ av säkerhetsobjekt, som representerar en användare. Resurstypen Microsoft Graph [][Graph-User-Resource] användaren definierar schemat för ett användarobjekt, inklusive användarrelaterade egenskaper som för- och efternamn, användarens huvudnamn, katalogrollmedlemskap osv. Detta tillhandahåller konfiguration av användaridentitet för Azure AD för att upprätta ett användarhuvudnamn vid körning. Användarens huvudnamn används för att representera en autentiserad [](#consent) användare för enkel inloggning, registrera delegering av medgivande, fatta beslut om åtkomstkontroll osv.

## <a name="web-client"></a>webbklient

En typ av [klientprogram](#client-application) som kör all kod på en webbserver och kan fungera som en "konfidentiell" klient genom att på ett säkert sätt lagra dess autentiseringsuppgifter på servern. Mer information finns i [OAuth2-klienttyper och -profiler.][OAuth2-Client-Types]

## <a name="next-steps"></a>Nästa steg

Utvecklarhandboken för [Microsoft Identity][AAD-Dev-Guide] Platform är landningssidan för alla utvecklingsrelaterade ämnen om Microsoft [][AAD-How-To-Integrate] Identity Platform, inklusive en översikt över programintegrering och grunderna i [Microsofts][AAD-Auth-Scenarios]identitetsplattformsautentisering och autentiseringsscenarier som stöds. Du kan också hitta kodexempel & självstudier om hur du kommer igång snabbt på [GitHub.](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=)

Använd följande kommentarsavsnitt för att ge feedback och hjälp med att förfina och forma det här innehållet, inklusive begäranden om nya definitioner eller uppdatering av befintliga!

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[Graph-Perm-Scopes]: /graph/permissions-reference
[Graph-App-Resource]: /graph/api/resources/application
[Graph-Sp-Resource]: /graph/api/resources/serviceprincipal
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
[JWT]: https://tools.ietf.org/html/rfc7519
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
