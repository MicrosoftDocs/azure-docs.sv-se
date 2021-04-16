---
title: Autentiseringsmetod för säkerhetsfrågor – Azure Active Directory
description: Lär dig mer om att använda säkerhetsfrågor Azure Active Directory för att förbättra och skydda inloggningshändelser
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 841391778e0fb8c00f503aa0cc79b5562661e309
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530354"
---
# <a name="authentication-methods-in-azure-active-directory---security-questions"></a>Autentiseringsmetoder i Azure Active Directory – säkerhetsfrågor

Säkerhetsfrågor används inte som autentiseringsmetod under en inloggningshändelse. I stället kan säkerhetsfrågor användas under självbetjäningsprocessen för lösenordsåterställning (SSPR) för att bekräfta vem du är. Administratörskonton kan inte använda säkerhetsfrågor som verifieringsmetod med SSPR.

När användarna registrerar sig för SSPR uppmanas de att välja de autentiseringsmetoder som ska användas. Om de väljer att använda säkerhetsfrågor väljer de från en uppsättning frågor för att fråga efter och sedan ange sina egna svar.

![Skärmbild av Azure Portal som visar autentiseringsmetoder och alternativ för säkerhetsfrågor](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> Säkerhetsfrågor lagras privat och säkert på ett användarobjekt i katalogen och kan endast besvaras av användare under registreringen. Det finns inget sätt för en administratör att läsa eller ändra en användares frågor eller svar.

Säkerhetsfrågor kan vara mindre säkra än andra metoder eftersom vissa kanske känner till svaren på en annan användares frågor. Om du använder säkerhetsfrågor med SSPR rekommenderar vi att du använder dem tillsammans med en annan metod. En användare kan uppmanas att använda Microsoft Authenticator-appen eller telefonautentisering för att verifiera sin identitet under SSPR-processen och välja säkerhetsfrågor endast om de inte har sin telefon eller registrerade enhet med sig.

## <a name="predefined-questions"></a>Fördefinierade frågor

Följande fördefinierade säkerhetsfrågor är tillgängliga för användning som en verifieringsmetod med SSPR. Alla dessa säkerhetsfrågor översätts och lokaliseras till en fullständig uppsättning Microsoft 365 språk baserat på användarens språk i webbläsaren:

* I vilken stad träffar du din första make/partner?
* I vilken stad träffar dina föräldrar?
* I vilken stad bor dina närmaste på samma plats?
* I vilken stad föddes din far?
* I vilken stad var ditt första jobb?
* I vilken stad föddes din mamma?
* Vilken stad var du i på nyår 2000?
* Vad är efternamnet på din favorit lärare i high school?
* Vad heter ett universitet som du har sökt på men inte har varit på?
* Vad är namnet på den plats där du höll din första mottagning?
* Vad är din fars mellannamn?
* Vilken är din favoritmat?
* Vad är din mammas för- och efternamn?
* Vad är din mammas mellannamn?
* Vad är din äldsta födelsedag på samma år som på samma år? (till exempel november 1985)
* Vad är ditt äldsta mellannamn på samma dator?
* Vad är din fars för- och efternamn?
* Vilket är ditt yngste mellannamn på samma dator?
* Vilken skola gick du i för sjätte klass?
* Vad var det första och sista namnet på din bästa vän?
* Vad var det första och sista namnet på din partner?
* Vad var efternamnet på din favoritklasss lärare?
* Vad var modell och modell för din första bil eller motorcykel?
* Vad hette den första skolan du gick i?
* Vad hette det sjukhus där du föddes?
* Vad var namnet på gatuadressen i ditt första hem?
* Vad hette din hero-hero?
* Vad var namnet på ditt favoritkniga djur?
* Vad hette ditt första husdjur?
* Vad var ditt smeknamn för smeknamn?
* Vilken var din favoritsport i skolan?
* Vad var ditt första jobb?
* Vilka var de sista fyra siffrorna i telefonnumret till din telefon?
* Vad ville du bli när du var liten?
* Vem är den mest berömda person som du någonsin har uppfyllt?

## <a name="custom-security-questions"></a>Anpassade säkerhetsfrågor

För ytterligare flexibilitet kan du definiera egna anpassade säkerhetsfrågor. Den maximala längden på en anpassad säkerhetsfråga är 200 tecken.

Anpassade säkerhetsfrågor lokaliseras inte automatiskt som med standardsäkerhetsfrågorna. Alla anpassade frågor visas på samma språk som de anges i det administrativa användargränssnittet, även om användarens språk i webbläsaren skiljer sig åt. Om du behöver lokaliserade frågor bör du använda de fördefinierade frågorna.

## <a name="security-question-requirements"></a>Krav på säkerhetsfrågor

För både standardsäkerhetsfrågor och anpassade säkerhetsfrågor gäller följande krav och begränsningar:

* Den minsta gränsen för svarstecken är tre tecken.
* Maxgränsen för svarstecken är 40 tecken.
* Användarna kan inte besvara samma fråga mer än en gång.
* Användare kan inte ge samma svar på mer än en fråga.
* Valfri teckenuppsättning kan användas för att definiera frågor och svar, inklusive Unicode-tecken.
* Antalet definierade frågor måste vara större än eller lika med det antal frågor som krävdes för att registrera sig.

## <a name="next-steps"></a>Nästa steg

Kom igång genom att gå [självstudien för självbetjäning av lösenordsåterställning (SSPR).][tutorial-sspr]

Mer information om SSPR-begrepp finns i [Så här fungerar självåterställning][concept-sspr]av lösenord i Azure AD.

Läs mer om hur du konfigurerar autentiseringsmetoder med [hjälp av Microsoft Graph REST API](/graph/api/resources/authenticationmethods-overview).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[concept-sspr]: concept-sspr-howitworks.md
