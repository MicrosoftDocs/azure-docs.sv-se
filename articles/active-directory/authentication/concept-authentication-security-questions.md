---
title: Autentiseringsmetod för säkerhets frågor – Azure Active Directory
description: Lär dig mer om att använda säkerhets frågor i Azure Active Directory för att hjälpa till att förbättra och säkra inloggnings händelser
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7229dabd690e5932fbd297992e09782eda85a002
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96744150"
---
# <a name="authentication-methods-in-azure-active-directory---security-questions"></a>Autentiseringsmetoder i Azure Active Directory-säkerhets frågor

Säkerhets frågor används inte som autentiseringsmetod under en inloggnings händelse. I stället kan säkerhets frågor användas under processen för självbetjäning för återställning av lösen ord (SSPR) för att bekräfta vem du är. Administratörs konton kan inte använda säkerhets frågor som verifieringsmetod med SSPR.

När användarna registrerar sig för SSPR uppmanas de att välja de autentiseringsmetoder som ska användas. Om de väljer att använda säkerhets frågor, väljs de från en uppsättning frågor att fråga efter och ger sina egna svar.

![Skärm bild av Azure Portal som visar autentiseringsmetoder och alternativ för säkerhets frågor](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> Säkerhets frågor lagras privat och säkert på ett användar objekt i katalogen och kan bara besvaras av användare under registreringen. Det finns inget sätt för en administratör att läsa eller ändra en användares frågor eller svar.

Säkerhets frågor kan vara mindre säkra än andra metoder eftersom vissa personer kanske känner till svaren på en annan användares frågor. Om du använder säkerhets frågor med SSPR rekommenderar vi att du använder dem tillsammans med en annan metod. En användare kan uppmanas att använda Microsoft Authenticator appen eller telefonen för att verifiera sin identitet under SSPR-processen och välja säkerhets frågor enbart om de inte har sin telefon eller registrerade enhet.

## <a name="predefined-questions"></a>Fördefinierade frågor

Följande fördefinierade säkerhets frågor är tillgängliga för användning som en verifieringsmetod med SSPR. Alla dessa säkerhets frågor översätts och lokaliseras till den fullständiga uppsättningen Microsoft 365 språk baserat på användarens språk:

* I vilken stad uppfyller du din första make/maka/partner?
* I vilken stad uppfyllde dina föräldrar?
* I vilken stad har du närmaste aktiva syskon?
* I vilken stad föddes din far?
* I vilken stad var ditt första jobb?
* I vilken stad föddes din mamma?
* Vilken stad var du på årets nya år 2000?
* Vad är det sista namnet på din favorit lärare i gymnasie skolan?
* Vad är namnet på en skola som du har använt till men inte var?
* Vad är namnet på den plats där du höll din första bröllops mottagning?
* Vad är ditt fars mellan namn?
* Vad är ditt favorit mat?
* Vad är din mormor för för-och efter namn?
* Vad är din mammas mellan namn?
* Vad är ditt äldsta syskons födelsedags månad och år? (till exempel november 1985)
* Vad är ditt äldsta syskons mellan namn?
* Vad är din farfar-farfar för för-och efter namn?
* Vad är ditt unga syskons mellan namn?
* Vilken skola deltog du i sjätte klass?
* Vad hette din barns bästa vänner?
* Vad hette det första och sista namnet på din första större?
* Vilket är det sista namnet på din favorit lärare i din favorit klass?
* Vad är märke och modell för din första bil eller motorcykel?
* Vad hette den första skolan som du deltog i?
* Vad var namnet på det sjukhus där du föddes?
* Vad är namnet på gatan för ditt första barn hemma?
* Vad hette din barns hjälte?
* Vad är namnet på ditt favorit fyllda djur?
* Vad hette ditt första hus djur?
* Vad hade ditt barn smek namn?
* Vad var din favorit sport i gymnasie skola?
* Vad var ditt första jobb?
* Vilka var de sista fyra siffrorna i ditt barns telefonnummer?
* Vad gjorde du när du var ung, vad gjorde du när du blev vuxna?
* Vilken är den mest berömda person som du någonsin har uppfyllt?

## <a name="custom-security-questions"></a>Anpassade säkerhets frågor

För ytterligare flexibilitet kan du definiera egna säkerhets frågor. Den maximala längden för en anpassad säkerhets fråga är 200 tecken.

Anpassade säkerhets frågor lokaliseras inte automatiskt som med standard säkerhets frågorna. Alla anpassade frågor visas på samma språk som de är angivna i det administrativa användar gränssnittet, även om användarens webbläsare är annorlunda. Om du behöver lokaliserade frågor bör du använda de fördefinierade frågorna.

## <a name="security-question-requirements"></a>Krav för säkerhets fråga

För både standard-och anpassade säkerhets frågor gäller följande krav och begränsningar:

* Gränsen för minsta antal svars tecken är tre tecken.
* Gränsen för maximalt antal svars tecken är 40 tecken.
* Användare kan inte besvara samma fråga mer än en gången.
* Användare kan inte ange samma svar på fler än en fråga.
* Alla teckenuppsättningar kan användas för att definiera frågor och svar, inklusive Unicode-tecken.
* Antalet frågor som definieras måste vara större än eller lika med antalet frågor som krävs för registrering.

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång finns i [självstudien för självbetjäning för återställning av lösen ord (SSPR)][tutorial-sspr].

Mer information om SSPR-koncept finns i [hur Azure AD självbetjäning för återställning av lösen ord fungerar][concept-sspr].

Läs mer om hur du konfigurerar autentiseringsmetoder med hjälp av [Microsoft Graph REST API beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[concept-sspr]: concept-sspr-howitworks.md
