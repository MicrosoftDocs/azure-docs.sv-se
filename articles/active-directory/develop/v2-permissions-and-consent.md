---
title: Microsoft identity platform scopes, permissions, & consent
description: Lär dig mer om auktorisering i Microsoft Identity Platform-slutpunkten, inklusive omfattningar, behörigheter och medgivande.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2021
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur, marsma
ms.custom: aaddev, fasttrack-edit, contperf-fy21q1, identityplatformtop40
ms.openlocfilehash: 2a975a0aba06ecfd010fe328ef6c8cda75290f2b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515591"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform"></a>Behörigheter och medgivande i Microsofts identitetsplattform

Program som integreras med Microsoft Identity-plattformen följer en auktoriseringsmodell som ger användare och administratörer kontroll över hur data kan nås. Implementeringen av auktoriseringsmodellen har uppdaterats på Microsoft Identity-plattformen. Den ändrar hur en app måste interagera med Microsoft Identity-plattformen. Den här artikeln beskriver grundläggande begrepp för den här auktoriseringsmodellen, inklusive omfattningar, behörigheter och medgivande.

## <a name="scopes-and-permissions"></a>Omfång och behörigheter

Microsoft Identity-plattformen implementerar [OAuth 2.0-auktoriseringsprotokollet.](active-directory-v2-protocols.md) OAuth 2.0 är en metod genom vilken en tredjepartsapp kan komma åt webbaserade resurser för en användares räkning. Alla webbaserade resurser som integreras med Microsofts identitetsplattform har en resursidentifierare eller *program-ID-URI.* 

Här följer några exempel på Microsofts webbaserade resurser:

* Microsoft Graph: `https://graph.microsoft.com`
* Microsoft 365 Mail API: `https://outlook.office.com`
* Azure Key Vault: `https://vault.azure.net`

Samma sak gäller för alla resurser från tredje part som har integrerats med Microsoft Identity-plattformen. Alla dessa resurser kan också definiera en uppsättning behörigheter som kan användas för att dela upp funktionerna i resursen i mindre segment. Som exempel har [Microsoft Graph](https://graph.microsoft.com) definierat behörigheter för att utföra följande uppgifter, bland annat:

* Läsa en användares kalender
* Skriva till en användares kalender
* Skicka e-post som en användare

På grund av dessa typer av behörighetsdefinitioner har resursen en finkornig kontroll över sina data och hur API-funktioner exponeras. En tredjepartsapp kan begära dessa behörigheter från användare och administratörer, som måste godkänna begäran innan appen kan komma åt data eller agera för en användares räkning. 

När en resurs funktioner är indelade i små behörighetsuppsättningar kan appar från tredje part byggas för att endast begära de behörigheter som de behöver för att utföra sin funktion. Användare och administratörer kan veta vilka data som appen kan komma åt. Och de kan vara mer säkra på att appen inte beter sig med skadlig avsikt. Utvecklare bör alltid följa principen om minsta behörighet och endast be om de behörigheter de behöver för att deras program ska fungera.

I OAuth 2.0 kallas dessa typer av behörighetsuppsättningar *för omfång*. De kallas också för *behörigheter.* På Microsoft Identity Platform representeras en behörighet som ett strängvärde. En app begär de behörigheter som krävs genom att ange behörigheten i `scope` frågeparametern. Identitetsplattformen stöder flera [väldefinierade OpenID Connect-omfång](#openid-connect-scopes) samt resursbaserade behörigheter (varje behörighet anges genom att behörighetsvärdet till resursens identifierare eller program-ID-URI har angetts). Behörighetssträngen används `https://graph.microsoft.com/Calendars.Read` till exempel för att begära behörighet att läsa användarnas kalendrar i Microsoft Graph.

En app begär oftast dessa behörigheter genom att ange omfången i begäranden till Microsoft Identity Platform för auktorisera slutpunkten. Vissa behörigheter med hög behörighet kan dock endast beviljas via administratörsmedgivande. De kan begäras eller beviljas med hjälp av [slutpunkten för administratörsmedgivande.](#admin-restricted-permissions) Läs vidare om du vill veta mer.

## <a name="permission-types"></a>Behörighetstyper

Microsoft Identity-plattformen stöder två typer av behörigheter: *delegerade behörigheter* och *programbehörigheter.*

* **Delegerade** behörigheter används av appar som har en inloggad användare. För dessa appar samtycker antingen användaren eller en administratör till de behörigheter som appen begär. Appen delegeras behörighet att fungera som den inloggade användaren när den gör anrop till målresursen. 

    Vissa delegerade behörigheter kan godkännas av icke-administratörer. Men vissa högbehörigheter kräver [administratörsmedgivande.](#admin-restricted-permissions) Information om vilka administratörsroller som kan godkänna delegerade behörigheter finns i [Behörigheter för administratörsroll i Azure Active Directory (Azure AD).](../roles/permissions-reference.md)

* **Programbehörigheter** används av appar som körs utan en inloggad användare, till exempel appar som körs som bakgrundstjänster eller daemon. Endast [en administratör kan samtycka till](#requesting-consent-for-an-entire-tenant) programbehörigheter.

_Gällande behörigheter_ är de behörigheter som din app har när den gör begäranden till målresursen. Det är viktigt att förstå skillnaden mellan de delegerade behörigheterna och programbehörigheterna som din app beviljas och de gällande behörigheter som appen beviljas när den gör anrop till målresursen.

- För delegerade  behörigheter är de gällande behörigheterna för din app den minst privilegierade skärningspunkten för de delegerade behörigheter som appen har beviljats (genom medgivande) och behörigheterna för den inloggade användaren. Din app kan aldrig ha fler behörigheter än den inloggade användaren. 

   Inom organisationer kan behörigheterna för den inloggade användaren fastställas av en princip eller genom medlemskap i en eller flera administratörsroller. Information om vilka administratörsroller som kan godkänna delegerade behörigheter finns i [Behörigheter för administratörsroll i Azure AD.](../roles/permissions-reference.md)

   Anta till exempel att din app har beviljats behörigheten _User.ReadWrite.All_ delegerad. Den här behörigheten ger i princip din app behörighet att läsa och uppdatera profilen för alla användare i en organisation. Om den inloggade användaren är en global administratör kan din app uppdatera profilen för alla användare i organisationen. Men om den inloggade användaren inte har någon administratörsroll kan din app bara uppdatera profilen för den inloggade användaren. Den kan inte uppdatera profiler för andra användare i organisationen eftersom den användare som den har behörighet att agera för inte har dessa behörigheter.

- För programbehörigheter är _de gällande behörigheterna_ för din app den fullständiga behörighetsnivån som behörigheten medför. Till exempel kan en app som har _behörigheten User.ReadWrite.All_ uppdatera profilen för varje användare i organisationen.

## <a name="openid-connect-scopes"></a>OpenID Connect omfattningar

Microsoft Identity Platform-implementeringen av OpenID Connect har några väldefinierade omfång som också finns på Microsoft Graph: `openid` `email` , , och `profile` `offline_access` . `address`Omfången OpenID Connect och stöds `phone` inte.

Om du begär OpenID Connect-omfång och en token får du en token för att anropa [UserInfo-slutpunkten](userinfo.md).

### <a name="openid"></a>Openid

Om en app loggar in med [hjälp OpenID Connect](active-directory-v2-protocols.md)måste den begära `openid` omfånget. `openid`Omfånget visas på medgivandesidan för arbetskontot som **behörigheten Logga in.** På sidan för Microsoft-konto personliga behörigheter visas den som Visa din profil och ansluter till appar och tjänster med **din Microsoft-konto** behörighet. 

Med den här behörigheten kan en app ta emot en unik identifierare för användaren i form av `sub` anspråket. Behörigheten ger även appen åtkomst till UserInfo-slutpunkten. `openid`Omfånget kan användas vid tokenslutpunkten för Microsoft Identity Platform för att hämta ID-token. Appen kan använda dessa token för autentisering.

### <a name="email"></a>e-post

`email`Omfånget kan användas med `openid` omfånget och andra omfång. Det ger appen åtkomst till användarens primära e-postadress i form av `email` anspråket. 

Anspråket ingår endast i en token om en e-postadress är associerad med `email` användarkontot, vilket inte alltid är fallet. Om appen använder omfånget måste appen kunna hantera `email` ett fall där det inte finns något anspråk i `email` token.

### <a name="profile"></a>profil

`profile`Omfånget kan användas med `openid` omfånget och andra omfång. Det ger appen åtkomst till en stor mängd information om användaren. Den information som den kan komma åt omfattar, men är inte begränsad till, användarens förnamn, efternamn, användarnamn och objekt-ID. 

En fullständig lista över anspråk `profile` som är tillgängliga i `id_tokens` parametern för en specifik användare finns i [ `id_tokens` referensen](id-tokens.md).

### <a name="offline_access"></a>offline_access

[ `offline_access` Omfånget](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) ger din app åtkomst till resurser för användarens räkning under en längre tid. På sidan för medgivande visas det här omfånget som **Den behåller åtkomsten till data som du har gett åtkomst till behörighet.** 

När en användare godkänner `offline_access` omfånget kan din app ta emot uppdateringstoken från tokenslutpunkten för Microsoft Identity Platform. Uppdateringstoken är långlivade. Din app kan hämta nya åtkomsttoken när äldre upphör att gälla.

> [!NOTE]
> Den här behörigheten visas för närvarande på alla medgivandesidor, även för flöden som inte tillhandahåller en uppdateringstoken (till exempel det [implicita flödet](v2-oauth2-implicit-grant-flow.md)). Den här installationen åtgärdar scenarier där en klient kan börja i det implicita flödet och sedan flytta till kodflödet där en uppdateringstoken förväntas.

På Microsoft Identity-plattformen (begäranden som görs till v2.0-slutpunkten) måste din app uttryckligen begära `offline_access` omfånget för att ta emot uppdateringstoken. Så när du löser in en auktoriseringskod i [OAuth 2.0-auktoriseringskodflödet](active-directory-v2-protocols.md)får du bara en åtkomsttoken från `/token` slutpunkten. 

Åtkomsttoken är giltig under en kort tid. Den upphör vanligtvis att gälla om en timme. Då måste appen omdirigera användaren tillbaka till slutpunkten för att `/authorize` få en ny auktoriseringskod. Beroende på typen av app kan användaren under omdirigeringen behöva ange sina autentiseringsuppgifter igen eller godkänna behörigheterna igen.

Mer information om hur du hämtar och använder uppdateringstoken finns i protokollreferensen [för Microsoft Identity Platform.](active-directory-v2-protocols.md)

## <a name="requesting-individual-user-consent"></a>Begära individuellt användarmedgivande

I en [OpenID Connect eller OAuth 2.0-auktoriseringsbegäran](active-directory-v2-protocols.md) kan en app begära de behörigheter som den behöver med hjälp av `scope` frågeparametern. När en användare till exempel loggar in på en app skickar appen en begäran som i följande exempel. (Radbrytningar läggs till för att göra det lätt att göra det.)

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

Parametern `scope` är en blankstegsavgränsad lista över delegerade behörigheter som appen begär. Varje behörighet anges genom att lägga till behörighetsvärdet till resursens identifierare (program-ID:ts URI). I exemplet med begäran behöver appen behörighet att läsa användarens kalender och skicka e-post som användaren.

När användaren anger sina autentiseringsuppgifter söker Microsoft Identity Platform efter en matchande post med *användarmedgivande.* Om användaren inte har samtyckt till någon av de begärda behörigheterna tidigare, och om administratören inte har samtyckt till dessa behörigheter för hela organisationens räkning, ber Microsofts identitetsplattform användaren att bevilja de begärda behörigheterna.

Just nu ingår behörigheten ("Behåll åtkomst till data som du har gett åtkomst till") och ("Logga in och läsa din profil") automatiskt i det första medgivandet till `offline_access` `user.read` ett program.  Dessa behörigheter krävs vanligtvis för rätt appfunktioner. Behörigheten `offline_access` ger appen åtkomst till uppdateringstoken som är viktiga för interna appar och webbappar. Behörigheten `user.read` ger åtkomst till `sub` anspråket. Det gör att klienten eller appen kan identifiera användaren över tid och få åtkomst till enkel användarinformation.

![Exempel på skärmbild som visar medgivande för arbetskonto.](./media/v2-permissions-and-consent/work_account_consent.png)

När användaren godkänner behörighetsbegäran registreras medgivande. Användaren behöver inte ge sitt medgivande igen när de senare loggar in i programmet.

## <a name="requesting-consent-for-an-entire-tenant"></a>Begära medgivande för en hel klientorganisation

När en organisation köper en licens eller prenumeration för ett program vill organisationen ofta proaktivt konfigurera programmet för användning av alla medlemmar i organisationen. Som en del av den här processen kan en administratör bevilja medgivande för programmet att agera för alla användare i klientorganisationen. Om administratören beviljar medgivande för hela klientorganisationen ser inte organisationens användare någon sida för medgivande för programmet.

Appen kan använda slutpunkten för administratörsmedgivande för att begära medgivande för delegerade behörigheter för alla användare i en klientorganisation.

Dessutom måste program använda slutpunkten för administratörsmedgivande för att begära programbehörigheter.

## <a name="admin-restricted-permissions"></a>Administratörsbegränsade behörigheter

Vissa behörigheter med hög behörighet i Microsoft-resurser kan anges till *administratörsbegränsade*. Här följer några exempel på dessa typer av behörigheter:

* Läsa alla användares fullständiga profiler med hjälp av `User.Read.All`
* Skriva data till en organisations katalog med hjälp av `Directory.ReadWrite.All`
* Läsa alla grupper i en organisations katalog med hjälp av `Groups.Read.All`

Även om en konsumentanvändare kan ge ett program åtkomst till den här typen av data kan organisationsanvändare inte bevilja åtkomst till samma uppsättning känsliga företagsdata. Om ditt program begär åtkomst till någon av dessa behörigheter från en organisationsanvändare får användaren ett felmeddelande om att de inte har behörighet att godkänna appens behörigheter.

Om din app kräver omfång för administratörsbegränsade behörigheter måste en organisations administratör godkänna dessa omfång för organisationens användares räkning. För att undvika att visa frågor för användare som begär medgivande för behörigheter som de inte kan bevilja kan din app använda slutpunkten för administratörsmedgivande. Slutpunkten för administratörsmedgivande tas upp i nästa avsnitt.

Om programmet begär delegerade behörigheter med hög behörighet och en administratör beviljar dessa behörigheter via slutpunkten för administratörsmedgivande, beviljas medgivande för alla användare i klientorganisationen.

Om programmet begär programbehörigheter och en administratör beviljar dessa behörigheter via slutpunkten för administratörsmedgivande görs detta beviljande inte för någon specifik användares räkning. I stället beviljas klientprogrammet behörigheter *direkt*. Dessa typer av behörigheter används endast av daemontjänster och andra icke-interaktiva program som körs i bakgrunden.

## <a name="using-the-admin-consent-endpoint"></a>Använda slutpunkten för administratörsmedgivande

När du använder slutpunkten för administratörsmedgivande för att bevilja administratörsmedgivande är du klar. Användarna behöver inte vidta några ytterligare åtgärder. När administratörsmedgivande har beviljats kan användarna få en åtkomsttoken via ett vanligt auth-flöde. Den resulterande åtkomsttoken har de behörigheter som krävs.

När en global administratör använder ditt program och dirigeras till auktorisera slutpunkten identifierar Microsofts identitetsplattform användarens roll. Den frågar om den globala administratören vill godkänna för hela klientorganisationens räkning för de behörigheter som du har begärt. Du kan i stället använda en dedikerad slutpunkt för administratörsmedgivande för att proaktivt begära att en administratör beviljar behörighet åt hela klientorganisationen. Den här slutpunkten är också nödvändig för att begära programbehörigheter. Programbehörigheter kan inte begäras med hjälp av auktorisera slutpunkten.

Om du följer dessa steg kan din app begära behörigheter för alla användare i en klientorganisation, inklusive administratörsbegränsade omfång. Den här åtgärden har hög behörighet. Använd bara åtgärden om det behövs för ditt scenario.

Ett kodexempel som implementerar stegen finns i exemplet [med administratörsbegränsade omfång](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2) i GitHub.

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Begära behörigheterna i appregistreringsportalen

I appregistreringsportalen kan program lista de behörigheter som de behöver, inklusive både delegerade behörigheter och programbehörigheter. Med den här konfigurationen kan `/.default` omfånget och Azure Portal alternativet **Bevilja administratörsmedgivande.**  

I allmänhet ska behörigheterna statiskt definieras för ett visst program. De bör vara en supermängd av de behörigheter som appen begär dynamiskt eller inkrementellt.

> [!NOTE]
>Programbehörigheter kan bara begäras med hjälp av [`/.default`](#the-default-scope) . Om din app behöver programbehörigheter kontrollerar du att de visas i appregistreringsportalen.

Så här konfigurerar du listan över statiskt begärda behörigheter för ett program:

1. Gå till ditt program i <a href="https://go.microsoft.com/fwlink/?linkid=2083908" target="_blank">Azure Portal – Appregistreringar</a> snabbstart.
1. Välj ett program eller [skapa en app](quickstart-register-app.md) om du inte redan har gjort det.
1. På programmets **översiktssida går du** till Hantera **och väljer** **API-behörigheter Lägg** till en  >  **behörighet**.
1. Välj **Microsoft Graph** listan över tillgängliga API:er. Lägg sedan till de behörigheter som din app kräver.
1. Välj **Lägg till behörigheter.**

### <a name="recommended-sign-the-user-in-to-your-app"></a>Rekommenderat: Logga in användaren i din app

När du skapar ett program som använder slutpunkten för administratörsmedgivande behöver appen normalt en sida eller vy där administratören kan godkänna appens behörigheter. Den här sidan kan vara:

* En del av appens inloggningsflöde.
* En del av appens inställningar.
* Ett dedikerat "anslut"-flöde. 

I många fall är det klokt att appen bara visar den här anslutningsvyn när en användare har loggat in med en arbets- Microsoft-konto eller Microsoft-konto.

När du loggar in användaren i din app kan du identifiera organisationen som administratören tillhör innan du ber dem att godkänna de behörigheter som krävs. Även om det här steget inte är absolut nödvändigt kan det hjälpa dig att skapa en mer intuitiv upplevelse för organisationens användare. 

Om du vill logga in användaren följer du [självstudierna för Microsoft Identity Platform-protokollet](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Begära behörigheter från en katalogadministratör

När du är redo att begära behörigheter från din organisations administratör kan du omdirigera användaren till slutpunkten för administratörsmedgivande för Microsoft Identity Platform.

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
| `tenant` | Krävs | Den katalogklient som du vill begära behörighet från. Det kan anges i ett GUID eller eget namnformat. Eller så kan den refereras allmänt till organisationer, som du ser i exemplet. Använd inte "gemensamma", eftersom personliga konton inte kan ge administratörsmedgivande förutom i kontexten för en klientorganisation. Använd klientorganisations-ID:t när det är möjligt för att säkerställa bästa kompatibilitet med personliga konton som hanterar klienter. |
| `client_id` | Obligatorisk | Det program-ID (klient) som [Azure Portal – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) till din app. |
| `redirect_uri` | Obligatorisk |Omdirigerings-URI:en där du vill att svaret ska skickas för din app att hantera. Den måste exakt matcha en av de omdirigerings-URI:er som du registrerade i appregistreringsportalen. |
| `state` | Rekommenderas | Ett värde som ingår i begäran som också returneras i tokensvaret. Det kan vara en sträng med val annat innehåll. Använd tillståndet för att koda information om användarens tillstånd i appen innan autentiseringsbegäran gjordes, till exempel den sida eller vy som användaren var på. |
|`scope`        | Obligatorisk        | Definierar den uppsättning behörigheter som begärs av programmet. Omfång kan vara antingen statiska (med [`/.default`](#the-default-scope) ) eller dynamiska.  Den här uppsättningen kan OpenID Connect omfång ( `openid` , `profile` , `email` ). Om du behöver programbehörigheter måste du använda `/.default` för att begära den statiskt konfigurerade listan över behörigheter.  |


I det här läget kräver Azure AD att en innehavaradministratör loggar in för att slutföra begäran. Administratören uppmanas att godkänna alla behörigheter som du har begärt i `scope` parametern .  Om du använde ett statiskt ( ) värde fungerar det som v1.0-slutpunkten för administratörsmedgivande och begär medgivande för alla omfång som finns i de behörigheter som `/.default` krävs för appen.

#### <a name="successful-response"></a>Lyckat svar

Om administratören godkänner behörigheterna för din app ser svaret ut så här:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parameter | Beskrivning |
| --- | --- |
| `tenant` | Katalogklientorganisationen som beviljat programmet de behörigheter som begärdes, i GUID-format. |
| `state` | Ett värde som ingår i begäran som också returneras i tokensvaret. Det kan vara en sträng med val annat innehåll. Tillståndet används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran gjordes, till exempel den sida eller vy som användaren var på. |
| `admin_consent` | Anges till `True` . |

#### <a name="error-response"></a>Felsvar

Om administratören inte godkänner behörigheterna för din app ser det misslyckade svaret ut så här:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | Beskrivning |
| --- | --- |
| `error` | En felkodssträng som kan användas för att klassificera typer av fel som inträffar. Det kan också användas för att reagera på fel. |
| `error_description` | Ett specifikt felmeddelande som kan hjälpa en utvecklare att identifiera rotorsaken till ett fel. |

När du har fått ett lyckat svar från slutpunkten för administratörsmedgivande har appen fått de behörigheter som begärdes. Därefter kan du begära en token för den resurs som du vill använda.

## <a name="using-permissions"></a>Använda behörigheter

När användaren har gett sitt medgivande till behörigheter för din app kan din app hämta åtkomsttoken som representerar appens behörighet att komma åt en resurs i viss kapacitet. En åtkomsttoken kan bara användas för en enskild resurs. Men kodad i åtkomsttoken är alla behörigheter som din app har beviljats för den resursen. Om du vill hämta en åtkomsttoken kan din app göra en begäran till tokenslutpunkten för Microsoft Identity Platform, så här:

```HTTP
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Du kan använda den resulterande åtkomsttoken i HTTP-begäranden till resursen. Den anger på ett tillförlitligt sätt för resursen att din app har rätt behörighet att utföra en viss uppgift.

Mer information om OAuth 2.0-protokollet och hur du hämtar åtkomsttoken finns i referensen för Slutpunktsprotokoll för [Microsoft Identity Platform.](active-directory-v2-protocols.md)

## <a name="the-default-scope"></a>Omfånget /.default

Du kan använda `/.default` omfånget för att migrera dina appar från v1.0-slutpunkten till slutpunkten för Microsoft Identity Platform. `/.default`Omfånget är inbyggt för varje program som refererar till den statiska listan över behörigheter som konfigurerats för programregistreringen. 

Värdet `scope` är `https://graph.microsoft.com/.default` funktionellt samma som på `resource=https://graph.microsoft.com` v1.0-slutpunkten. Genom att ange omfånget i sin begäran begär programmet en åtkomsttoken som innehåller omfång för varje Microsoft Graph-behörighet som du har valt för appen i `https://graph.microsoft.com/.default` appregistreringsportalen. Omfånget skapas med hjälp av resurs-URI och `/.default` . Så om resurs-URI:en är `https://contosoApp.com` är det begärda omfånget `https://contosoApp.com/.default` .  Om du måste inkludera ett andra snedstreck för att begära token korrekt kan du läsa avsnittet [om avslutande snedstreck](#trailing-slash-and-default).

`/.default`Omfånget kan användas i alla OAuth 2.0-flöden. Men det är nödvändigt i [flödet On-Behalf-Of och](v2-oauth2-on-behalf-of-flow.md) [klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md)flödet . Du behöver den också när du använder slutpunkten för v2-administratörsmedgivande för att begära programbehörigheter.

Klienter kan inte kombinera statiskt ( `/.default` ) medgivande och dynamiskt medgivande i en enda begäran. Därför `scope=https://graph.microsoft.com/.default+mail.read` resulterar det i ett fel eftersom den kombinerar omfångstyper.

### <a name="default-and-consent"></a>/.default och consent

`/.default`Omfånget utlöser även beteendet för v1.0-slutpunkten. `prompt=consent` Den begär medgivande för alla behörigheter som programmet har registrerat, oavsett resurs. Om den ingår som en del av begäran returnerar `/.default` omfånget en token som innehåller omfången för den begärda resursen.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default när användaren redan har gett sitt medgivande

`/.default`Omfånget är funktionellt identiskt med beteendet för `resource` den -centric v1.0-slutpunkten. Den har även medgivandebeteendet för v1.0-slutpunkten. Det vill `/.default` säga utlöser en fråga om medgivande endast om användaren inte har beviljat någon behörighet mellan klienten och resursen. 

Om ett sådant medgivande finns innehåller den returnerade token alla omfång som användaren har beviljats för resursen. Men om ingen behörighet har beviljats eller om parametern har angetts visas en fråga om medgivande för alla omfång som `prompt=consent` klientprogrammet har registrerat.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Exempel 1: Användaren eller klientadministratören har beviljats behörigheter

I det här exemplet har användaren eller en innehavaradministratör `mail.read` beviljat `user.read` Microsoft Graph behörighet till klienten. 

Om klienten begär visas ingen fråga om medgivande, oavsett innehållet i klientprogrammets registrerade behörigheter `scope=https://graph.microsoft.com/.default` för Microsoft Graph. Den returnerade token innehåller omfången `mail.read` och `user.read` .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Exempel 2: Användaren har inte beviljats behörighet mellan klienten och resursen

I det här exemplet har användaren inte beviljat medgivande mellan klienten och Microsoft Graph. Klienten har registrerats för behörigheterna `user.read` och `contacts.read` . Den har också registrerats för Azure Key Vault `https://vault.azure.net/user_impersonation` omfånget . 

När klienten begär en token för ser användaren en medgivandesida för omfånget, omfånget och `scope=https://graph.microsoft.com/.default` `user.read` Key Vault `contacts.read` `user_impersonation` omfattningar. Den returnerade token innehåller endast `user.read` `contacts.read` omfången och . Det kan bara användas mot Microsoft Graph.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-more-scopes"></a>Exempel 3: Användaren har samtyckt till och klienten begär fler omfång

I det här exemplet har användaren redan samtyckt `mail.read` till för klienten. Klienten har registrerats för `contacts.read` omfånget. 

När klienten begär en token med hjälp av och begär medgivande via ser användaren en medgivandesida för alla (och endast) de behörigheter som `scope=https://graph.microsoft.com/.default` `prompt=consent` programmet har registrerat. `contacts.read`Omfånget finns på medgivandesidan men är `mail.read` det inte. Den token som returneras är för Microsoft Graph. Den innehåller `mail.read` och `contacts.read` .

### <a name="using-the-default-scope-with-the-client"></a>Använda omfånget /.default med klienten

I vissa fall kan en klient begära ett eget `/.default` omfång. Följande exempel visar det här scenariot.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //Code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Det här kodexe exemplet skapar en medgivandesida för alla registrerade behörigheter om föregående beskrivningar av medgivande och `/.default` gäller för scenariot. Koden returnerar sedan en `id_token` , i stället för en åtkomsttoken.  

Det här beteendet rymmer vissa äldre klienter som flyttar från Azure AD Authentication Library (ADAL) till Microsoft Authentication Library (MSAL). Den här *konfigurationen ska inte* användas av nya klienter som är mål för Microsofts identitetsplattform.

### <a name="client-credentials-grant-flow-and-default"></a>Klientautentiseringsuppgifter beviljar flöde och /.default  

En annan användning av är att begära programbehörigheter (eller roller ) i ett icke-interaktivt program, till exempel en daemonapp som använder det klientautentiseringsuppgifter som beviljar flödet för att anropa `/.default` ett webb-API.  [](v2-oauth2-client-creds-grant-flow.md)

Information om hur du skapar programbehörigheter (roller) för ett webb-API finns i [Lägga till approller i ditt program](howto-add-app-roles-in-azure-ad-apps.md).

Begäranden om klientautentiseringsuppgifter i *klientappen måste* innehålla `scope={resource}/.default` . Här är `{resource}` webb-API:et som appen har för avsikt att anropa. Att utfärda en begäran om klientautentiseringsuppgifter med hjälp av enskilda programbehörigheter (roller) *stöds* inte. Alla programbehörigheter (roller) som har beviljats för webb-API:et ingår i den returnerade åtkomsttoken.

Information om hur du beviljar åtkomst till de programbehörigheter som du definierar, inklusive att bevilja administratörsmedgivande för programmet, finns i Konfigurera ett klientprogram för åtkomst [till ett webb-API.](quickstart-configure-app-access-web-apis.md)

### <a name="trailing-slash-and-default"></a>Avslutande snedstreck och /.default

Vissa resurs-URI:er har ett avslutande snedstreck, `https://contoso.com/` till exempel i stället för `https://contoso.com` . Det avslutande snedstrecket kan orsaka problem med tokenvalidering. Problem uppstår främst när en token begärs för Azure Resource Manager ( `https://management.azure.com/` ). I det här fallet innebär ett avslutande snedstreck på resurs-URI:en att snedstrecket måste finnas när token begärs.  Så när du begär en token för och använder måste du begära (observera det `https://management.azure.com/` dubbla `/.default` `https://management.azure.com//.default` snedstrecket!). Om du i allmänhet kontrollerar att token utfärdas, och om token avvisas av API:et som ska godkänna den, bör du överväga att lägga till ett andra snedstreck och försöka igen. 

## <a name="troubleshooting-permissions-and-consent"></a>Felsöka behörigheter och medgivande

Felsökningssteg finns i [Oväntat fel när du utför medgivande till ett program.](../manage-apps/application-sign-in-unexpected-user-consent-error.md)

## <a name="next-steps"></a>Nästa steg

* [ID-token på Microsofts identitetsplattform](id-tokens.md)
* [Åtkomsttoken på Microsofts identitetsplattform](access-tokens.md)
