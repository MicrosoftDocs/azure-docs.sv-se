---
title: Microsoft Identity Platform-omfattningar, behörigheter och medgivande
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
ms.openlocfilehash: 14b6d6ecc6523199102fd3ef9370fe901c4ff51d
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97355706"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Behörigheter och medgivande i slutpunkten för Microsoft Identity Platform

Program som integreras med Microsoft Identity Platform följer en auktoriserings modell som ger användare och administratörer kontroll över hur data kan nås. Implementeringen av auktoriserings modellen har uppdaterats på Microsoft Identity Platform-slutpunkten och den ändrar hur en app måste interagera med Microsoft Identity Platform. Den här artikeln beskriver de grundläggande begreppen i den här verifierings modellen, inklusive omfång, behörigheter och medgivande.

## <a name="scopes-and-permissions"></a>Omfattningar och behörigheter

Microsoft Identity Platform implementerar [OAuth 2,0](active-directory-v2-protocols.md) -Authorization-protokollet. OAuth 2,0 är en metod som innebär att en app från tredje part kan komma åt webb värd resurser för en användares räkning. Alla webb värd resurser som integreras med Microsoft Identity Platform har en resurs identifierare eller en URI för *program-ID*. Till exempel är några av Microsofts webb värd resurser:

* Microsoft Graph: `https://graph.microsoft.com`
* Microsoft 365 e-post-API: `https://outlook.office.com`
* Azure Key Vault: `https://vault.azure.net`

> [!NOTE]
> Vi rekommenderar starkt att du använder Microsoft Graph i stället för Microsoft 365 e-postapi, osv.

Samma sak gäller för alla resurser från tredje part som har integrerats med Microsoft Identity Platform. Alla dessa resurser kan också definiera en uppsättning behörigheter som kan användas för att dela upp resursens funktioner i mindre segment. [Microsoft Graph](https://graph.microsoft.com) har exempelvis definierat behörigheter för att utföra följande uppgifter, bland annat:

* Läs en användares kalender
* Skriv till en användares kalender
* Skicka e-post som en användare

Genom att definiera dessa typer av behörigheter har resursen detaljerad kontroll över sina data och hur API-funktioner exponeras. En app från tredje part kan begära dessa behörigheter från användare och administratörer, som måste godkänna begäran innan appen kan komma åt data eller agera för användarens räkning. Genom att dela upp resursens funktioner i mindre behörighets uppsättningar kan appar från tredje part byggas för att begära enbart de angivna behörigheter som de behöver för att utföra sin funktion. Användare och administratörer kan veta exakt vilka data som appen har åtkomst till, och de kan vara mer säkra på att de inte fungerar med skadlig avsikt. Utvecklare bör alltid följa konceptet med minsta behörighet och endast begära de behörigheter de behöver för att deras program ska fungera.

I OAuth 2,0 kallas dessa typer av behörigheter för *omfattningar*. De kallas också ofta för *behörigheter*. En behörighet visas i Microsoft Identity Platform som ett sträng värde. Om du fortsätter med Microsoft Graph exemplet är strängvärdet för varje behörighet:

* Läs en användares kalender genom att använda `Calendars.Read`
* Skriv till en användares kalender genom att använda `Calendars.ReadWrite`
* Skicka e-post som en användare med hjälp av `Mail.Send`

En app begär oftast dessa behörigheter genom att ange omfattningarna i begär anden till Microsoft Identity Platform permission-slutpunkten. Vissa behörigheter för hög behörighet kan dock endast beviljas genom administratörs medgivande och begärs/beviljas med hjälp av [Administratörs medgivande slut punkten](#admin-restricted-permissions). Läs vidare om du vill veta mer.

## <a name="permission-types"></a>Behörighets typer

Microsoft Identity Platform stöder två typer av behörigheter: **delegerade behörigheter** och **program behörigheter**.

* **Delegerade behörigheter** används av appar där en inloggad användare finns. För dessa appar skickas användaren eller administratören till de behörigheter som appen begär, och appen är delegerad behörighet att fungera som den inloggade användaren när den gör anrop till mål resursen. Vissa delegerade behörigheter kan skickas till av icke-administratörer, men vissa högre privilegier kräver [Administratörs medgivande](#admin-restricted-permissions). Information om vilka administratörs roller som kan godkänna delegerade behörigheter finns i [Administratörs roll behörigheter i Azure AD](../roles/permissions-reference.md).

* **Program behörigheter** används av appar som körs utan att en inloggad användare finns. till exempel appar som körs som bakgrunds tjänster eller daemon.  Program behörigheter kan bara godkännas [av en administratör](#requesting-consent-for-an-entire-tenant).

_Gällande behörigheter_ är de behörigheter som appen kommer att ha när de gör förfrågningar till mål resursen. Det är viktigt att förstå skillnaden mellan de delegerade och program behörigheter som din app beviljas och dess gällande behörigheter när du gör anrop till mål resursen.

- För delegerade behörigheter är den _effektiva behörigheten_ för din app den minst privilegierade skärnings punkten för de delegerade behörigheterna som appen har beviljats (via medgivande) och behörigheten för den för tillfället inloggade användaren. Din app kan aldrig ha fler behörigheter än den inloggade användaren. Inom organisationer kan behörigheter för den inloggade användaren fastställas med en princip eller av medlemskap i en eller flera administratörsroller. Information om vilka administratörs roller som kan godkänna delegerade behörigheter finns i [Administratörs roll behörigheter i Azure AD](../roles/permissions-reference.md).

   Anta till exempel att din app har beviljats _User. readwrite. all_ delegerad behörighet. Den här behörigheten ger i princip din app behörighet att läsa och uppdatera profilen för alla användare i en organisation. Om den inloggade användaren är en global administratör, kommer din app att kunna uppdatera profilen för alla användare i organisationen. Men om den inloggade användaren inte har någon administratörs roll kan appen bara uppdatera profilen för den inloggade användaren. Den kommer inte att kunna uppdatera profilerna för andra användare i organisationen, eftersom den användare som den har behörighet att agera på uppdrag åt inte har den behörigheten.

- För program behörigheter är den _effektiva behörigheten_ för appen den fullständiga behörighets nivån som anges av behörigheten. Till exempel kan en app som har behörigheten _User. readwrite. all_ program behörighet uppdatera profilen för alla användare i organisationen.

## <a name="openid-connect-scopes"></a>OpenID Connect-omfång

Microsoft Identity Platform-implementeringen av OpenID Connect har några väldefinierade omfattningar som också finns på Microsoft Graph: `openid` ,, `email` `profile` och `offline_access` . `address` `phone` OpenID Connect-scope stöds inte.

Om du begär OIDC-omfattningarna och en token får du en token för att anropa [UserInfo-slutpunkten](userinfo.md).

### <a name="openid"></a>OpenID

Om en app utför inloggning med hjälp av [OpenID Connect](active-directory-v2-protocols.md)måste den begära `openid` omfånget. `openid`Omfånget visas på sidan för godkännande av arbets konto som "logga in dig" och på sidan personal Microsoft-konto medgivande som "Visa din profil och Anslut till appar och tjänster med hjälp av din Microsoft-konto"-behörighet. Med den här behörigheten kan en app ta emot en unik identifierare för användaren i form av `sub` anspråket. Den ger också appen åtkomst till UserInfo-slutpunkten. `openid`Omfånget kan användas på Microsoft Identity Platform token-slutpunkten för att förvärva ID-token som kan användas av appen för autentisering.

### <a name="email"></a>e-post

`email`Omfånget kan användas med `openid` omfattningen och andra. Den ger appen åtkomst till användarens primära e-postadress i form av `email` anspråket. `email`Anspråket ingår bara i en token om en e-postadress är associerad med användar kontot, vilket inte alltid är fallet. Om den använder `email` omfånget bör din app förberedas för att hantera ett fall där `email` anspråket inte finns i token.

### <a name="profile"></a>profil

`profile`Omfånget kan användas med `openid` omfattningen och andra. Den ger appen åtkomst till en stor mängd information om användaren. Den information som den kan komma åt inkluderar, men är inte begränsad till, användarens förnamn, efter namn, önskat användar namn och objekt-ID. En fullständig lista över de profil anspråk som är tillgängliga i id_tokens parameter för en speciell användare finns i [ `id_tokens` referensen](id-tokens.md).

### <a name="offline_access"></a>offline_access

[ `offline_access` Omfånget](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) ger din app åtkomst till resurser för användarens räkning under en längre tid. På sidan medgivande visas denna omfattning som "Behåll åtkomst till data som du har gett dem behörighet till". När en användare godkänner `offline_access` omfånget kan din app ta emot uppdateringstoken från slut punkten för Microsoft Identity Platform-token. Uppdateringstoken är lång. Din app kan hämta nya åtkomsttoken när äldre upphör att gälla.

> [!NOTE]
> Den här behörigheten visas på alla godkännande skärmar idag, även för flöden som inte tillhandahåller en uppdateringstoken (det [implicita flödet](v2-oauth2-implicit-grant-flow.md)). Detta gäller scenarier där en klient kan börja i det implicita flödet och sedan flyttas till det kod flöde där en uppdateringstoken förväntas.

På Microsoft Identity Platform (begär Anden som gjorts till v 2.0-slutpunkten) måste appen uttryckligen begära `offline_access` omfånget för att ta emot uppdateringstoken. Det innebär att när du löser in en auktoriseringskod i [OAuth 2,0-Authorization Code Flow](active-directory-v2-protocols.md)får du bara en åtkomsttoken från `/token` slut punkten. Åtkomsttoken är giltig under en kort tid. Åtkomsttoken går vanligt vis ut om en timme. Vid det här tillfället måste appen omdirigera användaren tillbaka till `/authorize` slut punkten för att få en ny auktoriseringskod. Under omdirigeringen, beroende på typ av app, kan användaren behöva ange sina autentiseringsuppgifter igen eller ge tillstånd igen till behörigheter.

Mer information om hur du hämtar och använder uppdaterade token finns i referens för [Microsoft Identity Platform-protokollet](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Begära individuell användar medgivande

I en [OpenID Connect-eller OAuth 2,0](active-directory-v2-protocols.md) -auktoriseringsbegäran kan en app begära de behörigheter som krävs med hjälp av `scope` Frågeparametern. Till exempel, när en användare loggar in i en app, skickar appen en begäran som följande exempel (med rad brytningar som har lagts till för läsbarhet):

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

När användaren har angett sina autentiseringsuppgifter söker Microsoft Identity Platform-slutpunkten efter en matchande post för *användar medgivande*. Om användaren inte har samtyckt till någon av de begärda behörigheterna tidigare, eller om en administratör har samtyckt till dessa behörigheter åt hela organisationen, ber Microsoft Identity Platform-slutpunkten användaren att bevilja de begärda behörigheterna.

> [!NOTE]
>För närvarande `offline_access` inkluderas behörigheterna ("Behåll åtkomst till data som du har fått åtkomst till") och `user.read` ("logga in och läsa din profil") automatiskt i det första medgivandeet till ett program.  De här behörigheterna krävs vanligt vis för korrekt app-funktionalitet – `offline_access` ger appen åtkomst till att uppdatera tokens, kritiskt för ursprungliga appar och webbappar, samtidigt `user.read` som den ger åtkomst till `sub` anspråket, så att klienten eller appen kan identifiera användaren över tid och komma åt elementära användar information.

![Exempel skärm bild som visar godkännande av arbets konto](./media/v2-permissions-and-consent/work_account_consent.png)

När användaren godkänner behörighets förfrågningen registreras medgivande och användaren behöver inte godkänna det igen på efterföljande inloggningar till programmet.

## <a name="requesting-consent-for-an-entire-tenant"></a>Begära medgivande för en hel klient

När en organisation köper en licens eller prenumeration för ett program vill organisationen ofta konfigurera programmet för användning av alla medlemmar i organisationen. Som en del av den här processen kan en administratör bevilja medgivande för programmet att agera åt alla användare i klient organisationen. Om administratören ger godkännande för hela klient organisationen ser inte organisationens användare en godkännande sida för programmet.

För att begära medgivande för delegerade behörigheter för alla användare i en klient organisation kan din app använda slut punkten för administratörs medgivande.

Dessutom måste program använda den administrativa medgivande slut punkten för att begära program behörigheter.

## <a name="admin-restricted-permissions"></a>Administratör – begränsade behörigheter

Vissa behörigheter med hög behörighet i Microsoft-eko systemet kan anges till *admin-begränsad*. Exempel på dessa typer av behörigheter är följande:

* Läs alla användares fullständiga profiler med hjälp av `User.Read.All`
* Skriva data till en organisations katalog med hjälp av `Directory.ReadWrite.All`
* Läs alla grupper i en organisations katalog med hjälp av `Groups.Read.All`

Även om en konsument användare kan ge en program åtkomst till den här typen av data, begränsas organisationens användare från att bevilja åtkomst till samma uppsättning känsliga företags data. Om programmet begär åtkomst till någon av de här behörigheterna från en organisations användare får användaren ett fel meddelande om att de inte har behörighet att godkänna appens behörigheter.

Om din app kräver åtkomst till administrations begränsade omfång för organisationer, bör du begära dem direkt från en företags administratör, även genom att använda slut punkten för administratörs medgivande, som beskrivs härnäst.

Om programmet begär hög behörighet delegerade behörigheter och en administratör beviljar behörigheterna via slut punkten för administratörs medgivande beviljas medgivande för alla användare i klient organisationen.

Om programmet begär program behörigheter och en administratör beviljar behörigheterna via slut punkten för administratörs medgivande, utförs inte detta anslag för någon specifik användares räkning. I stället beviljas klient programmet behörigheter *direkt*. Dessa typer av behörigheter används endast av daemon-tjänster och andra icke-interaktiva program som körs i bakgrunden.

## <a name="using-the-admin-consent-endpoint"></a>Använda slut punkten för administratörs medgivande

> [!NOTE]
> Observera att när du har beviljat administratörs medgivande med hjälp av administratörs slut punkten har du beviljat godkännandet och användarna behöver inte utföra några ytterligare åtgärder. Efter att ha beviljat administratörs tillåtelse kan användare få en åtkomsttoken via ett typiskt auth-flöde och den resulterande åtkomsttoken har de behörigheter som har skickats.

När en företags administratör använder ditt program och dirigeras till behörighets slut punkten identifierar Microsoft Identity Platform användarens roll och ber dem om de vill ge tillstånd åt hela klient organisationen för de behörigheter som du har begärt. Det finns dock en dedikerad slut punkt för administratörs medgivande som du kan använda om du vill proaktivt begära att en administratör ger behörighet åt hela klient organisationen. Du måste också använda den här slut punkten för att begära program behörigheter (som inte kan begäras med hjälp av auktorisera slut punkten).

Om du följer de här stegen kan din app begära behörigheter för alla användare i en klient, inklusive administrations begränsade omfång. Detta är en åtgärd med hög behörighet och bör bara utföras om det behövs för ditt scenario.

Om du vill se ett kod exempel som implementerar stegen går du till [exemplet admin-begränsade omfattningar](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Begär behörigheterna i appens registrerings Portal

Program kan anteckna vilka behörigheter de behöver (både delegerade och program) i registrerings portalen för appen.  Detta gör att du kan använda `/.default` omfånget och Azure Portal beviljat administrativt medgivande.  I allmänhet är det bäst att se till att de behörigheter som definierats statiskt för ett visst program är en supermängd av de behörigheter som begärs dynamiskt/stegvis.

> [!NOTE]
>Program behörigheter kan bara begäras genom användning av [`/.default`](#the-default-scope) – så om appen behöver program behörigheter kontrollerar du att de visas i appens registrerings Portal.

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>Konfigurera listan med statiskt begärda behörigheter för ett program

1. Gå till ditt program i [Azure Portal – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) erfarenhet eller [skapa en app](quickstart-register-app.md) om du inte redan har gjort det.
2. Leta upp avsnittet **API-behörigheter** och i API-behörigheterna klickar du på Lägg till en behörighet.
3. Välj **Microsoft Graph** i listan över tillgängliga API: er och Lägg sedan till de behörigheter som din app kräver.
3. **Spara** appens registrering.

### <a name="recommended-sign-the-user-into-your-app"></a>Rekommenderat: signera användaren i din app

När du skapar ett program som använder den administrativa medgivande slut punkten behöver appen normalt en sida eller vy där administratören kan godkänna appens behörigheter. Den här sidan kan ingå i appens registrerings flöde, en del av appens inställningar, eller så kan det vara ett dedikerat "Connect"-flöde. I många fall är det meningsfullt att appen visar vyn "Anslut" bara när en användare har loggat in med ett arbets-eller skol Microsoft-konto.

När du registrerar användaren i din app kan du identifiera den organisation som administratören tillhör innan de ber dem att godkänna de nödvändiga behörigheterna. Även om det inte är absolut nödvändigt kan det hjälpa dig att skapa en mer intuitiv upplevelse för dina organisations användare. Följ våra [självstudier för Microsoft Identity Platform Protocol](active-directory-v2-protocols.md)för att logga in användaren i.

### <a name="request-the-permissions-from-a-directory-admin"></a>Begär behörigheter från en katalog administratör

När du är redo att begära behörigheter från din organisations administratör kan du omdirigera användaren till Microsoft Identity Platform *admin medgivande-slutpunkten*.

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
| `tenant` | Krävs | Den katalog klient som du vill begära behörighet från. Kan tillhandahållas i GUID eller eget namn format eller allmänt refereras till organisationer som visas i exemplet. Använd inte "common", eftersom personliga konton inte kan tillhandahålla administrativt medgivande, förutom i kontexten för en klient. För att säkerställa bästa kompatibilitet med personliga konton som hanterar klienter använder du klient-ID när det är möjligt. |
| `client_id` | Obligatorisk | **Program-ID: t (klienten)** som [Azure Portal – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) -upplevelsen som har tilldelats din app. |
| `redirect_uri` | Obligatorisk |Den omdirigerings-URI där du vill att svaret på din app ska hanteras. Det måste exakt matcha en av de omdirigerings-URI: er som du registrerade i registrerings portalen för appen. |
| `state` | Rekommenderas | Ett värde som ingår i begäran som också kommer att returneras i svaret från token. Det kan vara en sträng med valfritt innehåll som du vill ha. Använd tillstånd för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, t. ex. sidan eller vyn de var på. |
|`scope`        | Obligatorisk        | Definierar den uppsättning behörigheter som begärs av programmet. Detta kan vara antingen statiskt (med [`/.default`](#the-default-scope) ) eller dynamiska omfång.  Detta kan inkludera OIDC-omfattningarna ( `openid` , `profile` , `email` ). Om du behöver program behörigheter måste du använda `/.default` för att begära den statiskt konfigurerade listan med behörigheter.  |


I det här läget kräver Azure AD en klient administratör för att logga in för att slutföra begäran. Administratören uppmanas att godkänna alla behörigheter som du har begärt i `scope` parametern.  Om du har använt ett statiskt ( `/.default` )-värde kommer det att fungera som v 1.0 admin medgivande-slutpunkten och begära medgivande för alla omfattningar som finns i de nödvändiga behörigheterna för appen.

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
| `error` | En fel kods sträng som kan användas för att klassificera typer av fel som inträffar och som kan användas för att reagera på fel. |
| `error_description` | Ett fel meddelande som kan hjälpa en utvecklare att identifiera rotor saken till ett fel. |

När du har fått ett lyckat svar från slut punkten för administratörs medgivande har appen fått de behörigheter som begärdes. Sedan kan du begära en token för den resurs som du vill använda.

## <a name="using-permissions"></a>Använda behörigheter

När användaren har fått behörighet till appen kan appen Hämta åtkomsttoken som representerar appens behörighet att få åtkomst till en resurs i viss kapacitet. En åtkomsttoken kan bara användas för en enda resurs, men kodad i åtkomsttoken är alla behörigheter som din app har beviljats för resursen. För att få en åtkomsttoken kan din app göra en begäran till Microsoft Identity Platform token-slutpunkten, så här:

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

Du kan använda `/.default` omfånget för att migrera dina appar från v 1.0-slut punkten till Microsoft Identity Platform-slutpunkten. Det här är en inbyggd omfattning för varje program som refererar till den statiska listan med behörigheter som kon figurer ATS i program registreringen. `scope`Värdet `https://graph.microsoft.com/.default` är samma sak som v 1.0-slut punkterna `resource=https://graph.microsoft.com` – dvs begär en token med scope på Microsoft Graph att programmet har registrerats för i Azure Portal.  Den konstrueras med hjälp av resurs-URI + `/.default` (t. ex. om resurs-URI: n är `https://contosoApp.com` , så är det begärda omfånget `https://contosoApp.com/.default` ).  Se [avsnittet om avslutande snedstreck](#trailing-slash-and-default) i fall där du måste ta med ett andra snedstreck för att begära token korrekt.

/.Default-omfånget kan användas i alla OAuth 2,0-flöden, men det är nödvändigt i flödet för det aktiva flödet och [klientens autentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md), samt när du använder v2 [-](v2-oauth2-on-behalf-of-flow.md) administratörens medgivande slut punkt för att begära program behörigheter.

> [!NOTE]
> Klienter kan inte kombinera statiska ( `/.default` ) och dynamiskt medgivande i en enskild begäran. Därför `scope=https://graph.microsoft.com/.default+mail.read` leder det till ett fel på grund av en kombination av omfångs typer.

### <a name="default-and-consent"></a>/.default och medgivande

`/.default`Omfattningen utlöser även slut punkts beteendet för v 1.0 `prompt=consent` . Den begär tillstånd för alla behörigheter som registrerats av programmet, oavsett resurs. Om omfånget ingår som en del av begäran, `/.default` returnerar omfånget en token som innehåller omfattningarna för den begärda resursen.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default när användaren redan har gett sitt medgivande

Eftersom `/.default` är funktions identiskt för `resource` beteendet för den-koncentriska v 1.0-slutpunkten, tar den med sig även av medgivande beteendet för v 1.0-slut punkten. Dvs `/.default` utlöser bara en medgivande-prompt om ingen behörighet har beviljats mellan klienten och resursen av användaren. Om ett sådant medgivande finns returneras en token som innehåller alla omfattningar som beviljats av användaren för resursen. Men om ingen behörighet har beviljats eller `prompt=consent` parametern har angetts visas en medgivande-prompt för alla omfattningar som registrerats av klient programmet.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Exempel 1: användaren eller klient administratören har beviljat behörigheter

I det här exemplet har användaren (eller klient administratören) gett klienten Microsoft Graph behörigheter `mail.read` och `user.read` . Om klienten gör en begäran om så `scope=https://graph.microsoft.com/.default` visas ingen medgivande fråga oavsett innehållet i de klient program som har registrerat behörigheter för Microsoft Graph. En token returneras som innehåller omfattningarna `mail.read` och `user.read` .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Exempel 2: användaren har inte beviljats behörigheter mellan klienten och resursen

I det här exemplet finns det inget medgivande för användaren mellan klienten och Microsoft Graph. Klienten har registrerat sig för `user.read` -och `contacts.read` -behörigheterna, samt Azure Key Vault omfattning `https://vault.azure.net/user_impersonation` . När klienten begär en token för `scope=https://graph.microsoft.com/.default` , kommer användaren att se ett medgivande fönster för `user.read` -, `contacts.read` -och Key Vault- `user_impersonation` omfattningarna. Den returnerade token har bara `user.read` `contacts.read` omfattningarna och kan bara användas för Microsoft Graph.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>Exempel 3: användaren har samtyckt och klienten begär ytterligare omfång

I det här exemplet har användaren redan samtyckt till `mail.read` för-klienten. Klienten har registrerats för `contacts.read` omfånget i registreringen. När klienten gör en begäran om en token som använder `scope=https://graph.microsoft.com/.default` och begär medgivande via `prompt=consent` , kommer användaren att se en medgivande skärm för alla (och endast) de behörigheter som registrerats av programmet. `contacts.read` kommer att finnas på medgivande skärmen, men `mail.read` kommer inte. Den returnerade token är för Microsoft Graph och kommer att innehålla `mail.read` och `contacts.read` .

### <a name="using-the-default-scope-with-the-client"></a>Använda/.default-scope med klienten

Det finns ett specialfall av `/.default` omfånget där en klient begär sitt eget `/.default` omfång. Följande exempel visar det här scenariot.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Detta skapar en godkännande skärm för alla registrerade behörigheter (om det är tillämpligt baserat på ovanstående beskrivningar av medgivande och `/.default` ), returnerar en id_token i stället för en åtkomsttoken.  Det här beteendet finns för vissa äldre klienter som flyttas från ADAL till MSAL och **ska inte** användas av nya klienter som är riktade till Microsoft Identity Platform-slutpunkten.

### <a name="client-credentials-grant-flow-and-default"></a>Tilldelnings flöde och/.default för klientautentiseringsuppgifter

En annan användning av `/.default` är när du begär program behörigheter (eller *roller*) i ett icke-interaktivt program som en daemon-app som använder den begär ande typen av [klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md) för att anropa ett webb-API.

Information om hur du skapar program behörigheter (roller) för ett webb-API finns i [så här gör du: Lägg till app-roller i ditt program](howto-add-app-roles-in-azure-ad-apps.md).

Begär Anden om klientautentiseringsuppgifter i klient programmet **måste** innehålla `scope={resource}/.default` , där `{resource}` är webb-API: et som din app avser att anropa. Det finns **inte** stöd för att utfärda en begäran om klientautentiseringsuppgifter med enskilda program behörigheter (roller). Alla program behörigheter (roller) som har beviljats för webb-API: et kommer att ingå i den returnerade åtkomsttoken.

Om du vill bevilja åtkomst till de program behörigheter som du definierar, inklusive beviljande av administratörs medgivande för programmet, se [snabb start: Konfigurera ett klient program för att få åtkomst till ett webb-API](quickstart-configure-app-access-web-apis.md).

### <a name="trailing-slash-and-default"></a>Avslutande snedstreck och/.default

Vissa resurs-URI: er har ett avslutande snedstreck ( `https://contoso.com/` till skillnad från `https://contoso.com` ), vilket kan orsaka problem med verifieringen av token.  Detta kan ske främst när du begär en token för Azure Resource Management ( `https://management.azure.com/` ), som har ett avslutande snedstreck i resurs-URI: n och som kräver att den finns när token begärs.  När du begär en token för `https://management.azure.com/` och använder `/.default` måste du därför begära `https://management.azure.com//.default` det dubbla snedstrecket!

I allmänhet – om du har verifierat att token har utfärdats och token avvisas av API: et som ska acceptera den, bör du överväga att lägga till ett andra snedstreck och försöka igen. Detta inträffar eftersom inloggnings servern utvärderar en token med den mål grupp som matchar URI: erna i `scope` parametern-med `/.default` borttagna från slutet.  Om det här tar bort det avslutande snedstrecket bearbetar inloggnings servern begäran och validerar den mot resurs-URI: n, även om de inte längre matchar – detta är icke-standard och bör inte förlitas av ditt program.

## <a name="troubleshooting-permissions-and-consent"></a>Felsöka behörigheter och medgivande

Om du eller ditt programs användare ser oväntade fel under medgivande processen, se den här artikeln för fel söknings steg: [oväntat fel vid godkännande av ett program](../manage-apps/application-sign-in-unexpected-user-consent-error.md).

## <a name="next-steps"></a>Nästa steg

* [ID-token | Microsoft Identity Platform](id-tokens.md)
* [Åtkomsttoken | Microsoft Identity Platform](access-tokens.md)
