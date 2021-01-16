---
title: Microsoft Identity Platform-omfattningar, behörigheter, & medgivande
description: Läs mer om auktorisering i slut punkten för Microsoft Identity Platform, inklusive omfång, behörigheter och medgivande.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur, marsma
ms.custom: aaddev, fasttrack-edit, contperf-fy21q1, identityplatformtop40
ms.openlocfilehash: 35499810ae13a8ddc5b7bb6306deafef0ef24e0f
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98246799"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Behörigheter och medgivande i slutpunkten för Microsoft Identity Platform

Program som integreras med Microsoft Identity Platform följer en auktoriserings modell som ger användare och administratörer kontroll över hur data kan nås. Implementeringen av auktoriserings modellen har uppdaterats på Microsoft Identity Platform-slutpunkten. Den ändrar hur en app måste interagera med Microsoft Identity Platform. Den här artikeln beskriver de grundläggande begreppen i den här verifierings modellen, inklusive omfång, behörigheter och medgivande.

## <a name="scopes-and-permissions"></a>Omfattningar och behörigheter

Microsoft Identity Platform implementerar [OAuth 2,0](active-directory-v2-protocols.md) -Authorization-protokollet. OAuth 2,0 är en metod som innebär att en app från tredje part kan komma åt webb värd resurser för en användares räkning. Alla webb värd resurser som integreras med Microsoft Identity Platform har en resurs identifierare eller en URI för *program-ID*. 

Här följer några exempel på Microsofts webb värd resurser:

* Microsoft Graph: `https://graph.microsoft.com`
* Microsoft 365 e-post-API: `https://outlook.office.com`
* Azure Key Vault: `https://vault.azure.net`

Samma sak gäller för alla resurser från tredje part som har integrerats med Microsoft Identity Platform. Alla dessa resurser kan också definiera en uppsättning behörigheter som kan användas för att dela upp resursens funktioner i mindre segment. [Microsoft Graph](https://graph.microsoft.com) har exempelvis definierat behörigheter för att utföra följande uppgifter, bland annat:

* Läs en användares kalender
* Skriv till en användares kalender
* Skicka e-post som en användare

På grund av dessa typer av behörighets definitioner har resursen detaljerad kontroll över sina data och hur API-funktioner exponeras. En app från tredje part kan begära dessa behörigheter från användare och administratörer, som måste godkänna begäran innan appen kan komma åt data eller agera för användarens räkning. 

När en resurss funktion delas upp i små behörighets uppsättningar kan appar från tredje part byggas för att begära enbart de behörigheter som de behöver för att utföra sin funktion. Användare och administratörer kan veta vilka data som appen kan komma åt. Och de kan vara mer säkra på att appen inte fungerar med skadliga avsikter. Utvecklare bör alltid följa principen om minsta behörighet och endast begära de behörigheter de behöver för att deras program ska fungera.

I OAuth 2,0 kallas dessa typer av behörighets uppsättningar *omfattningar*. De kallas också ofta för *behörigheter*. I Microsoft Identity Platform representeras en behörighet som ett sträng värde. För Microsoft Graph exemplet här är strängvärdet för varje behörighet:

* Läs en användares kalender genom att använda `Calendars.Read`
* Skriv till en användares kalender genom att använda `Calendars.ReadWrite`
* Skicka e-post som en användare med hjälp av `Mail.Send`

En app begär oftast dessa behörigheter genom att ange omfattningarna i begär anden till Microsoft Identity Platform permission-slutpunkten. Vissa behörigheter för hög behörighet kan dock endast beviljas genom administratörs medgivande. De kan begäras eller beviljas genom att använda [slut punkten för administratörs medgivande](#admin-restricted-permissions). Läs mer om att läsa mer.

## <a name="permission-types"></a>Behörighets typer

Microsoft Identity Platform stöder två typer av behörigheter: *delegerade behörigheter* och *program behörigheter*.

* **Delegerade behörigheter** används av appar där en inloggad användare finns. För dessa appar skickas användaren eller administratören till de behörigheter som appen begär. Appen är delegerad behörighet att fungera som den inloggade användaren när den gör anrop till mål resursen. 

    Vissa delegerade behörigheter kan skickas till av andra än administratörer. Men vissa privilegier med hög behörighet kräver [Administratörs medgivande](#admin-restricted-permissions). Information om vilka administratörs roller som kan godkänna delegerade behörigheter finns i [Administratörs roll behörigheter i Azure Active Directory (Azure AD)](../roles/permissions-reference.md).

* **Program behörigheter** används av appar som körs utan att en inloggad användare finns, till exempel appar som körs som bakgrunds tjänster eller daemon. Endast [en administratör kan](#requesting-consent-for-an-entire-tenant) godkänna program behörigheter.

_Gällande behörigheter_ är de behörigheter som appen har när begär anden till mål resursen görs. Det är viktigt att förstå skillnaden mellan de delegerade behörigheter och program behörigheter som din app har beviljats och de effektiva behörigheter som din app beviljas när den gör anrop till mål resursen.

- För delegerade behörigheter är de _effektiva behörigheterna_ för din app den minsta privilegierade skärnings punkten för de delegerade behörigheterna som appen har beviljats (genom medgivande) och behörigheten för den för tillfället inloggade användaren. Din app kan aldrig ha fler behörigheter än den inloggade användaren. 

   Inom organisationer kan privilegierna för den inloggade användaren fastställas av en princip eller av medlemskap i en eller flera administratörs roller. Information om vilka administratörs roller som kan godkänna delegerade behörigheter finns i [Administratörs roll behörigheter i Azure AD](../roles/permissions-reference.md).

   Anta till exempel att din app har beviljats _User. readwrite. all_ delegerad behörighet. Den här behörigheten ger i princip din app behörighet att läsa och uppdatera profilen för alla användare i en organisation. Om den inloggade användaren är global administratör kan appen uppdatera profilen för varje användare i organisationen. Men om den inloggade användaren inte har någon administratörs roll kan appen bara uppdatera profilen för den inloggade användaren. Det går inte att uppdatera profilerna för andra användare i organisationen eftersom användaren som har behörighet att agera på uppdrag av inte har dessa behörigheter.

- För program behörigheter är de _effektiva behörigheterna_ för din app den fullständiga behörighets nivån som anges av behörigheten. Till exempel kan en app som har behörigheten _User. readwrite. all_ program behörighet uppdatera profilen för alla användare i organisationen.

## <a name="openid-connect-scopes"></a>OpenID Connect-omfång

Microsoft Identity Platform-implementeringen av OpenID Connect har några väldefinierade omfattningar som också finns på Microsoft Graph:,, `openid` `email` `profile` och `offline_access` . `address` `phone` OpenID Connect-scope stöds inte.

Om du begär OpenID Connect-scope och en token får du en token för att anropa [UserInfo-slutpunkten](userinfo.md).

### <a name="openid"></a>OpenID

Om en app loggar in genom att använda [OpenID Connect](active-directory-v2-protocols.md)måste den begära `openid` omfånget. `openid`Omfattningen visas på sidan för godkännande av arbets konto som den **inloggning som du** har behörighet för. På sidan personal Microsoft-konto medgivande visas den som **Visa din profil och ansluta till appar och tjänster med hjälp av Microsoft-konto** -behörighet. 

Genom att använda den här behörigheten kan en app ta emot en unik identifierare för användaren i form av `sub` anspråket. Behörigheten ger också appen åtkomst till UserInfo-slutpunkten. `openid`Omfånget kan användas på Microsoft Identity Platform token-slutpunkt för att förvärva ID-token. Appen kan använda dessa token för autentisering.

### <a name="email"></a>e-post

`email`Omfånget kan användas med `openid` omfånget och andra omfång. Den ger appen åtkomst till användarens primära e-postadress i form av `email` anspråket. 

`email`Anspråket ingår bara i en token om en e-postadress är associerad med användar kontot, vilket inte alltid är fallet. Om appen använder `email` omfånget måste appen kunna hantera ett fall där det inte `email` finns något anspråk i token.

### <a name="profile"></a>profil

`profile`Omfånget kan användas med `openid` omfånget och andra omfång. Den ger appen åtkomst till en stor mängd information om användaren. Den information som den kan komma åt inkluderar, men är inte begränsad till, användarens förnamn, efter namn, önskat användar namn och objekt-ID. 

En fullständig lista över de `profile` anspråk som är tillgängliga i `id_tokens` parametern för en speciell användare finns i [ `id_tokens` referensen](id-tokens.md).

### <a name="offline_access"></a>offline_access

[ `offline_access` Omfånget](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) ger din app åtkomst till resurser för användarens räkning under en längre tid. På sidan medgivande visas det här omfånget som den **kvarhållna åtkomsten till data som du har gett åtkomst till** behörighet. 

När en användare godkänner `offline_access` omfånget kan din app ta emot uppdateringstoken från slut punkten för Microsoft Identity Platform-token. Uppdateringstoken är lång. Din app kan hämta nya åtkomsttoken när äldre upphör att gälla.

> [!NOTE]
> Den här behörigheten visas för tillfället på alla godkännande sidor, även för flöden som inte tillhandahåller en uppdateringstoken (till exempel det [implicita flödet](v2-oauth2-implicit-grant-flow.md)). Den här installationen löser scenarier där en klient kan börja i det implicita flödet och sedan flyttas till det kod flöde där en uppdateringstoken förväntas.

På Microsoft Identity Platform (begär Anden som gjorts till v 2.0-slutpunkten) måste appen uttryckligen begära `offline_access` omfånget för att ta emot uppdateringstoken. När du löser in en auktoriseringskod i [OAuth 2,0-auktoriseringskod](active-directory-v2-protocols.md)får du bara en åtkomsttoken från `/token` slut punkten. 

Åtkomsttoken är giltig under en kort tid. Den upphör vanligt vis att gälla om en timme. Vid det här tillfället måste appen omdirigera användaren tillbaka till `/authorize` slut punkten för att få en ny auktoriseringskod. Under omdirigeringen, beroende på typ av app, kan användaren behöva ange sina autentiseringsuppgifter igen eller ge tillstånd igen till behörigheter.

Mer information om hur du hämtar och använder uppdaterade token finns i referens för [Microsoft Identity Platform-protokollet](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Begära individuell användar medgivande

I en [OpenID Connect-eller OAuth 2,0](active-directory-v2-protocols.md) -auktoriseringsbegäran kan en app begära de behörigheter som krävs med hjälp av `scope` Frågeparametern. Till exempel, när en användare loggar in i en app, skickar appen en begäran som följande exempel. (Rad brytningar har lagts till för läsbarhet.)

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

`scope`Parametern är en blankstegsavgränsad lista med delegerade behörigheter som appen begär. Varje behörighet anges genom att lägga till behörighet svärdet till resursens identifierare (program-ID-URI). I förfrågan-exemplet måste appen ha behörighet att läsa användarens kalender och skicka e-post som användare.

När användaren har angett sina autentiseringsuppgifter söker Microsoft Identity Platform-slutpunkten efter en matchande post för *användar medgivande*. Om användaren inte har samtyckt till någon av de begärda behörigheterna tidigare och om administratören inte har samtyckt till dessa behörigheter åt hela organisationen, ber Microsoft Identity Platform-slutpunkten användaren att bevilja de begärda behörigheterna.

För tillfället `offline_access` inkluderas behörigheten ("Behåll åtkomst till data som du har fått åtkomst till") och `user.read` ("logga in och läsa din profil") automatiskt i det första medgivande till ett program.  De här behörigheterna krävs vanligt vis för korrekt app-funktionalitet. `offline_access`-Behörigheten ger appen åtkomst till att uppdatera tokens som är viktiga för inbyggda appar och webbappar. `user.read`Behörigheten ger till gång till `sub` anspråket. Det gör att klienten eller appen kan identifiera användaren över tid och komma åt elementära användar information.

![Exempel skärm bild som visar ditt arbets konto medgivande.](./media/v2-permissions-and-consent/work_account_consent.png)

När användaren godkänner behörighets förfrågningen registreras medgivande. Användaren behöver inte godkännas igen när de senare loggar in i programmet.

## <a name="requesting-consent-for-an-entire-tenant"></a>Begära medgivande för en hel klient

När en organisation köper en licens eller prenumeration för ett program vill organisationen ofta att proaktivt konfigurera programmet för användning av alla medlemmar i organisationen. Som en del av den här processen kan en administratör bevilja medgivande för programmet att agera åt alla användare i klient organisationen. Om administratören ger godkännande för hela klienten, ser inte organisationens användare en godkännande sida för programmet.

För att begära medgivande för delegerade behörigheter för alla användare i en klient organisation kan din app använda slut punkten för administratörs medgivande.

Dessutom måste program använda den administrativa medgivande slut punkten för att begära program behörigheter.

## <a name="admin-restricted-permissions"></a>Administratör – begränsade behörigheter

Vissa behörigheter med hög behörighet i Microsoft-resurser kan anges till *admin-begränsad*. Här följer några exempel på följande typer av behörigheter:

* Läs alla användares fullständiga profiler med hjälp av `User.Read.All`
* Skriva data till en organisations katalog med hjälp av `Directory.ReadWrite.All`
* Läs alla grupper i en organisations katalog med hjälp av `Groups.Read.All`

Även om en konsument användare kan ge en program åtkomst till den här typen av data kan inte organisations användare bevilja åtkomst till samma uppsättning känsliga företags data. Om programmet begär åtkomst till någon av de här behörigheterna från en organisations användare får användaren ett fel meddelande om att de inte har behörighet att godkänna appens behörigheter.

Om din app kräver omfång för administratörs begränsade behörigheter måste en organisations administratör samtycka till dessa omfattningar för användarens räkning. För att undvika att Visa prompter till användare som begär medgivande för behörigheter som de inte kan bevilja, kan appen använda slut punkten för administratörs medgivande. Slutpunkten för administratörs medgivande beskrivs i nästa avsnitt.

Om programmet begär hög behörighet delegerade behörigheter och en administratör beviljar behörigheterna via slut punkten för administratörs medgivande beviljas medgivande för alla användare i klient organisationen.

Om programmet begär program behörigheter och en administratör beviljar behörigheterna via slut punkten för administratörs medgivande, sker inte detta anslag för någon specifik användares räkning. I stället beviljas klient programmet behörigheter *direkt*. Dessa typer av behörigheter används endast av daemon-tjänster och andra ej interaktiva program som körs i bakgrunden.

## <a name="using-the-admin-consent-endpoint"></a>Använda slut punkten för administratörs medgivande

När du har använt admin medgivande-slutpunkten för att bevilja administratörs tillåtelse är du klar. Användarna behöver inte vidta några ytterligare åtgärder. När administratörs medgivande beviljas kan användarna få en åtkomsttoken via ett typiskt auth-flöde. Den resulterande åtkomsttoken har de behörigheter som har skickats.

När en företags administratör använder ditt program och dirigeras till auktoriserad slut punkt identifierar Microsoft Identity Platform användarens roll. Den frågar om företags administratören vill samtycka åt hela klient organisationen för de behörigheter som du har begärt. Du kan istället använda en dedikerad administratörs medgivande slut punkt för att proaktivt begära en administratör att bevilja behörighet åt hela klient organisationen. Den här slut punkten krävs också för att begära program behörigheter. Det går inte att begära program behörigheter med hjälp av en auktoriserad slut punkt.

Om du följer de här stegen kan din app begära behörigheter för alla användare i en klient, inklusive administrations begränsade omfång. Den här åtgärden har hög behörighet. Använd bara åtgärden om det behövs för ditt scenario.

Om du vill se ett kod exempel som implementerar stegen går du till [exemplet på admin-begränsade omfattningar](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2) i GitHub.

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Begär behörigheterna i appens registrerings Portal

I appens registrerings Portal kan program ange de behörigheter som krävs, inklusive både delegerade behörigheter och program behörigheter. Med den här inställningen kan du använda `/.default` omfånget och Azure Portal **beviljar godkännande** .  

I allmänhet ska behörigheterna definieras statiskt för ett visst program. De bör vara en supermängd av de behörigheter som appen kommer att begära dynamiskt eller stegvis.

> [!NOTE]
>Program behörigheter kan endast begäras genom användning av [`/.default`](#the-default-scope) . Så om din app behöver program behörigheter kontrollerar du att de finns med i appens registrerings Portal.

Så här konfigurerar du listan med statiskt begärda behörigheter för ett program:

1. Gå till ditt program i snabb starten <a href="https://go.microsoft.com/fwlink/?linkid=2083908" target="_blank">Azure Portal- <span class="docon docon-navigate-external x-hidden-focus"></span> Appregistreringar</a> .
1. Välj ett program eller [skapa en app](quickstart-register-app.md) om du inte redan har gjort det.
1. På sidan **Översikt** för program under **Hantera** väljer du API- **behörigheter**  >  **Lägg till en behörighet**.
1. Välj **Microsoft Graph** i listan över tillgängliga API: er. Lägg sedan till de behörigheter som din app kräver.
1. Välj **Lägg till behörigheter**.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Rekommenderat: Logga in användaren i appen

När du skapar ett program som använder den administrativa medgivande slut punkten behöver appen normalt en sida eller vy där administratören kan godkänna appens behörigheter. Den här sidan kan vara:

* En del av appens registrerings flöde.
* En del av appens inställningar.
* Ett dedikerat "Connect"-flöde. 

I många fall är det klokt att appen endast visar den här "Connect"-vyn när en användare har loggat in med ett arbets Microsoft-konto-eller skol Microsoft-konto.

När du loggar in användaren i din app kan du identifiera den organisation som administratören tillhör innan du ber dem att godkänna de nödvändiga behörigheterna. Även om det här steget inte är absolut nödvändigt kan det hjälpa dig att skapa en mer intuitiv upplevelse för dina organisations användare. 

Följ [självstudierna för Microsoft Identity Platform Protocol](active-directory-v2-protocols.md)för att logga in användaren i.

### <a name="request-the-permissions-from-a-directory-admin"></a>Begär behörigheter från en katalog administratör

När du är redo att begära behörigheter från din organisations administratör kan du omdirigera användaren till Microsoft Identity Platform admin medgivande-slutpunkten.

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| Parameter        | Villkor        | Beskrivning                                                                                |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | Krävs | Den katalog klient som du vill begära behörighet från. Det kan anges i ett GUID eller eget namn format. Eller så kan det användas allmänt med organisationer, som du ser i exemplet. Använd inte "common" eftersom personliga konton inte kan ge administrativt medgivande, förutom i kontexten för en klient. För att säkerställa bästa kompatibilitet med personliga konton som hanterar klienter använder du klient-ID när det är möjligt. |
| `client_id` | Obligatorisk | Program-ID: t (klienten) som [Azure Portal – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) -upplevelsen som har tilldelats din app. |
| `redirect_uri` | Obligatorisk |Den omdirigerings-URI där du vill att svaret på din app ska hanteras. Det måste exakt matcha en av de omdirigerings-URI: er som du registrerade i registrerings portalen för appen. |
| `state` | Rekommenderas | Ett värde som ingår i begäran som också kommer att returneras i svaret från token. Det kan vara en sträng med valfritt innehåll som du vill ha. Använd tillstånd för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, t. ex. sidan eller vyn de var på. |
|`scope`        | Obligatorisk        | Definierar den uppsättning behörigheter som begärs av programmet. Omfattningar kan vara antingen statiska (med [`/.default`](#the-default-scope) ) eller dynamiska.  Den här uppsättningen kan omfatta OpenID Connect-scope ( `openid` , `profile` , `email` ). Om du behöver program behörigheter måste du använda `/.default` för att begära den statiskt konfigurerade listan med behörigheter.  |


I det här läget kräver Azure AD en klient administratör för att logga in för att slutföra begäran. Administratören uppmanas att godkänna alla behörigheter som du har begärt i `scope` parametern.  Om du använde ett statiskt ( `/.default` )-värde kommer det att fungera som v 1.0 admin medgivande-slutpunkten och begära medgivande för alla omfattningar som finns i de nödvändiga behörigheterna för appen.

#### <a name="successful-response"></a>Lyckat svar

Om administratören godkänner behörigheterna för din app ser det lyckade svaret ut så här:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parameter | Beskrivning |
| --- | --- |
| `tenant` | Den katalog klient som beviljade programmet de behörigheter som begärdes, i GUID-format. |
| `state` | Ett värde som ingår i begäran som också kommer att returneras i svaret från token. Det kan vara en sträng med valfritt innehåll som du vill ha. Statusen används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, t. ex. sidan eller vyn de var på. |
| `admin_consent` | Kommer att anges till `True` . |

#### <a name="error-response"></a>Fel svar

Om administratören inte godkänner behörigheterna för din app ser det misslyckade svaret ut så här:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | Beskrivning |
| --- | --- |
| `error` | En fel kods sträng som kan användas för att klassificera typer av fel som inträffar. Den kan också användas för att reagera på fel. |
| `error_description` | Ett fel meddelande som kan hjälpa en utvecklare att identifiera rotor saken till ett fel. |

När du har fått ett lyckat svar från slut punkten för administratörs medgivande har appen fått de behörigheter som begärdes. Sedan kan du begära en token för den resurs som du vill använda.

## <a name="using-permissions"></a>Använda behörigheter

När användaren har fått behörighet till appen kan appen Hämta åtkomsttoken som representerar appens behörighet att få åtkomst till en resurs i viss kapacitet. En åtkomsttoken kan bara användas för en enskild resurs. Men kodade inuti åtkomsttoken är alla behörigheter som din app har beviljats för resursen. För att få en åtkomsttoken kan din app göra en begäran till Microsoft Identity Platform token-slutpunkten, så här:

```HTTP
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Du kan använda den resulterande åtkomsttoken i HTTP-begäranden till resursen. Det tyder tillförlitligt på den resurs som din app har rätt behörighet för att utföra en speciell uppgift.

Mer information om OAuth 2,0-protokollet och hur du hämtar åtkomsttoken finns i [protokoll referens för Microsoft Identity Platform](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>/.Default-omfånget

Du kan använda `/.default` omfånget för att migrera dina appar från v 1.0-slut punkten till Microsoft Identity Platform-slutpunkten. `/.default`Omfattningen är inbyggd för varje program som refererar till den statiska listan med behörigheter som kon figurer ATS i program registreringen. 

`scope`Värdet `https://graph.microsoft.com/.default` är samma som `resource=https://graph.microsoft.com` för v 1.0-slut punkten. Genom att ange `https://graph.microsoft.com/.default` omfånget i sin begäran begär ditt program en åtkomsttoken som innehåller omfång för varje Microsoft Graph behörighet som du har valt för appen i appens registrerings Portal. Omfattningen konstrueras med hjälp av resurs-URI och `/.default` . Så om resurs-URI: n är är `https://contosoApp.com` det begärda omfånget `https://contosoApp.com/.default` .  I de fall där du måste ta med ett andra snedstreck för att kunna begära token, se [avsnittet om avslutande snedstreck](#trailing-slash-and-default).

`/.default`Omfånget kan användas i alla OAuth 2,0-flöden. Men det är nödvändigt i flödet för det aktiva [flödet](v2-oauth2-on-behalf-of-flow.md) och [klientens autentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md). Du behöver också det när du använder v2-administratörens medgivande slut punkt för att begära program behörigheter.

Klienter kan inte kombinera statiska ( `/.default` ) medgivande och dynamiskt medgivande i en enskild begäran. Detta `scope=https://graph.microsoft.com/.default+mail.read` resulterar i ett fel eftersom det kombinerar omfångs typer.

### <a name="default-and-consent"></a>/.default och medgivande

`/.default`Omfattningen utlöser även slut punkts beteendet för v 1.0 `prompt=consent` . Den begär tillstånd för alla behörigheter som programmet har registrerat, oavsett resurs. Om den ingår som en del av begäran `/.default` returnerar omfånget en token som innehåller omfattningarna för den begärda resursen.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default när användaren redan har gett sitt medgivande

`/.default`Omfånget är funktions sätt identiskt med beteendet för den `resource` -koncentriska v 1.0-slut punkten. Det bär även medgivande beteendet för v 1.0-slut punkten. Det innebär att `/.default` endast en medgivande-prompt utlöses om användaren har beviljats ingen behörighet mellan klienten och resursen. 

Om detta tillstånd finns innehåller den returnerade token alla omfattningar som användaren har beviljat för resursen. Men om ingen behörighet har beviljats eller om `prompt=consent` parametern har angetts visas en medgivande-prompt för alla omfattningar som klient programmet har registrerat.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Exempel 1: användaren eller klient administratören har beviljat behörigheter

I det här exemplet har användaren eller en klient administratör beviljat `mail.read` och `user.read` Microsoft Graph behörighet till klienten. 

Om klienten begär detta `scope=https://graph.microsoft.com/.default` visas ingen medgivande fråga, oavsett innehållet i klient programmets registrerade behörigheter för Microsoft Graph. Den returnerade token innehåller omfattningarna `mail.read` och `user.read` .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Exempel 2: användaren har inte beviljats behörigheter mellan klienten och resursen

I det här exemplet har användaren inte beviljat ett medgivande mellan klienten och Microsoft Graph. Klienten har registrerats för behörigheterna `user.read` och `contacts.read` . Den har också registrerats för Azure Key Vault omfattningen `https://vault.azure.net/user_impersonation` . 

När klienten begär en token för `scope=https://graph.microsoft.com/.default` , ser användaren en godkännande sida för `user.read` omfånget, `contacts.read` omfattningen och Key Vault `user_impersonation` omfattningarna. Den returnerade token innehåller `user.read` endast `contacts.read` omfattningarna och. Den kan endast användas för Microsoft Graph.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-more-scopes"></a>Exempel 3: användaren har samtyckt och klienten begär fler omfattningar

I det här exemplet har användaren redan samtyckt till `mail.read` för-klienten. Klienten har registrerats för `contacts.read` omfånget. 

När klienten begär en token genom `scope=https://graph.microsoft.com/.default` att använda och begär medgivande via `prompt=consent` , ser användaren en godkännande sida för alla (och endast) de behörigheter som programmet har registrerat. `contacts.read`Omfattningen är på medgivande sidan `mail.read` , men inte. Den returnerade token är för Microsoft Graph. Den innehåller `mail.read` och `contacts.read` .

### <a name="using-the-default-scope-with-the-client"></a>Använda/.default-scope med klienten

I vissa fall kan en klient begära sitt eget `/.default` omfång. Följande exempel visar det här scenariot.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //Code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Det här kod exemplet skapar en godkännande sida för alla registrerade behörigheter om de föregående beskrivningarna av medgivande och `/.default` gäller för scenariot. Sedan returnerar koden en `id_token` , i stället för en åtkomsttoken.  

Det här beteendet hanterar vissa äldre klienter som flyttas från Azure AD Authentication Library (ADAL) till Microsoft Authentication Library (MSAL). Den här installationen *bör* inte användas av nya klienter som riktar sig mot Microsoft Identity Platform-slutpunkten.

### <a name="client-credentials-grant-flow-and-default"></a>Tilldelnings flöde och/.default för klientautentiseringsuppgifter  

En annan användning av `/.default` är att begära program behörigheter (eller *roller*) i en modul som inte är interaktiv, som en daemon-app som använder sig av det beviljande flödet för klientautentiseringsuppgifter för att anropa ett webb- [](v2-oauth2-client-creds-grant-flow.md) API.

Information om hur du skapar program behörigheter (roller) för ett webb-API finns i [lägga till app-roller i ditt program](howto-add-app-roles-in-azure-ad-apps.md).

Begär Anden om klientautentiseringsuppgifter i klient programmet *måste* innehålla `scope={resource}/.default` . Här `{resource}` är webb-API: t som din app avser att anropa. Att utfärda en begäran om klient referenser genom att använda enskilda program behörigheter (roller) stöds *inte* . Alla program behörigheter (roller) som har beviljats för webb-API: et ingår i den returnerade åtkomsttoken.

Om du vill bevilja åtkomst till de program behörigheter som du definierar, inklusive beviljande av administratörs medgivande för programmet, se [Konfigurera ett klient program för att få åtkomst till ett webb-API](quickstart-configure-app-access-web-apis.md).

### <a name="trailing-slash-and-default"></a>Avslutande snedstreck och/.default

Vissa resurs-URI: er har ett avslutande snedstreck, till exempel i `https://contoso.com/` motsats till `https://contoso.com` . Det avslutande snedstrecket kan orsaka problem med verifiering av token. Problem inträffar främst när en token begärs för Azure Resource Manager ( `https://management.azure.com/` ). I det här fallet innebär ett avslutande snedstreck i resurs-URI: n att det måste finnas ett snedstreck när token begärs.  Så när du begär en token för `https://management.azure.com/` och använder `/.default` måste du begära `https://management.azure.com//.default` (Observera det dubbla snedstrecket!). Om du i allmänhet kontrollerar att token har utfärdats och om token nekas av API: et som ska acceptera det, kan du överväga att lägga till ett andra snedstreck och försöka igen. 

## <a name="troubleshooting-permissions-and-consent"></a>Felsöka behörigheter och medgivande

Fel söknings steg finns i ett [oväntat fel vid godkännande av ett program](../manage-apps/application-sign-in-unexpected-user-consent-error.md).

## <a name="next-steps"></a>Nästa steg

* [ID-token i Microsoft Identity Platform](id-tokens.md)
* [Åtkomsttoken i Microsoft Identity Platform](access-tokens.md)
