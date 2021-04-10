---
title: Rikt linjer för anpassning av appar | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om program anpassnings rikt linjer för Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/31/2020
ms.author: ryanwi
ms.reviewer: arielgo, jiml
ms.custom: aaddev, signin_art
ms.openlocfilehash: 236e82ab97244e1428441f83295f6a5d4ed56350
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582000"
---
# <a name="branding-guidelines-for-applications"></a>Varumärkesriktlinjer för program

När du utvecklar program med Microsoft Identity Platform måste du dirigera dina kunder när de vill använda sitt arbets-eller skol konto (hanteras i Azure AD) eller ett personligt konto för registrering och inloggning i ditt program.

I den här artikeln kommer du att:

- Lära dig om de två typer av användarkonton som hanteras av Microsoft och hur du refererar till Azure AD-konton i ditt program
- Lär dig kraven för att använda Microsofts logo typ i din app
- Ladda ned de officiella bilderna för **Logga in** eller **Logga in med Microsoft** för användning i din app
- Läs mer om vad du behöver göra och tänka på när det gäller varumärkesanpassning och navigering

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Personliga konton jämfört med arbets- eller skolkonton från Microsoft

Microsoft hanterar två typer av användarkonton:

- **Personliga konton** (kallades tidigare Windows Live ID). De här kontona sätts upp mellan *enskilda* användare och Microsoft. De ger åtkomst till konsumentenheter och konsumenttjänster från Microsoft. De här kontona är avsedda för personligt bruk.
- **Arbets- eller skolkonton.** De här kontona hanteras av Microsoft, åt organisationer som använder Azure Active Directory. Dessa konton används för att logga in på Microsoft 365 och andra företags tjänster från Microsoft.

Microsofts arbets- eller skolkonton tilldelas vanligtvis till slutanvändare (anställda, studenter, myndighetsanställda) av organisationerna (företag, skola/universitet, myndighet). Dessa konton är hanterade direkt i molnet (i Azure AD-plattformen) eller synkroniseras till Azure AD från en lokal katalog, t. ex. Windows Server Active Directory. Microsoft *övervakar* arbets- och skolkonton, men de ägs och kontrolleras av organisationen.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Referenser till Azure AD-konton i programmet

Microsoft visar inte varumärkena Azure och Active Directory för slutanvändare och det bör inte du heller göra.

- När användaren har loggat in använder du organisationens namn och logotyp så mycket som möjligt. Det är bättre än att använda allmänna termer, exempelvis ”organisationen”.
- När användare inte är inloggade kan du benämna deras konton som arbets- eller skolkonton och använda Microsoft-logotypen för att förmedla att Microsoft hanterar de här kontona. Använd inte termer som företagskonto och affärskonto. Det förvirrar användaren.

## <a name="user-account-pictogram"></a>Piktogram för användarkonto

I en tidigare version av de här riktlinjerna rekommenderar vi ett blått piktogram. Vi har fått återkoppling från användare och utvecklare och rekommenderar nu att du använder Microsofts logotyp istället. Microsoft-logotypen hjälper användarna att förstå att de kan återanvända det konto som de använder med Microsoft 365 eller andra Microsoft-företag för att logga in på din app.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Registrera sig och logga in med Azure AD

Det kan finnas flera olika sätt att registrera sig och logga in i ditt program. Följande avsnitt visar hur båda scenarierna går till.

**Om slutanvändare kan registrera sig för ditt program (till exempel för en kostnadsfri utvärderingsversion)**: Du kan visa en **logga in**-knapp där användare kan komma åt din app med sitt arbetskonto eller personliga konto. Azure AD visar en samtyckestext första gången de öppnar din app.

**Om appen kräver behörigheter som endast administratörer kan godkänna, eller om programmet kräver organisationslicensiering**: Separera administratörshämtning från användarinloggning. Knappen för att **hämta den här appen** omdirigerar administratörer för att logga in och ber dem sedan att ge samtycke åt användare i organisationen. Det här har den ytterligare fördelen att prompter om slutanvändargodkännande undertrycks.

## <a name="visual-guidance-for-app-acquisition"></a>Skaffa app – visuell vägledning

Den länk du lägger in för apphämtning måste omdirigera användaren till Azure AD-godkännandesidan (auktorisering). Administratören måste kunna godkänna så att appen får åtkomst till organisationens data, som tillhandahålls av Microsoft. Information om hur du begär åtkomst beskrivs i artikeln om att [integrera app med Azure Active Directory](./quickstart-register-app.md).

När administratörer har godkänt din app kan de välja att lägga till den i sina användares Microsoft 365 App Launcher-upplevelse (nås från rutmärket och från [https://portal.office.com/myapps](https://portal.office.com/myapps) ). Om du vill annonsera den här funktionen kan du till exempel skriva ”Lägg till den här appen i organisationen” och visa en knapp enligt följande exempel:

![Knapp som visar text för Microsoft-logotypen och "Lägg till i min organisation"](./media/howto-add-branding-in-azure-ad-apps/add-to-my-org.png)

Vi rekommenderar dock att du skriver förklarande text istället för att förlita dig på knappar. Exempel:

> *Om du redan använder Microsoft 365 eller andra företags tjänster från Microsoft kan du bevilja <your_app_name> åtkomst till din organisations data. Detta gör att användarna kan komma åt <your_app_name> med sina befintliga arbets konton.*

Du kan ladda ned den officiella Microsoft-logotypen och använda den i din app. Högerklicka på den du vill använda och spara den på datorn.

| Tillgång                                | PNG-format | SVG-format |
| ------------------------------------ | ---------- | ---------- |
| Microsoft-logotyp  | ![Nedladdnings bar Microsoft-logotyp i PNG-format](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_mssymbol_19.png) | ![Nedladdnings bar Microsoft-logotyp i SVG-format](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_mssymbol_19.svg) |

## <a name="visual-guidance-for-sign-in"></a>Visuell vägledning för inloggning

I din app bör det finnas en inloggningsknapp som omdirigerar användarna till inloggningsslutpunkten som motsvarar det protokoll du använder för att integrera med Azure AD. I det här avsnittet finns information om hur knappen ska se ut.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Piktogram och Logga in med Microsoft

Det är associationerna som Microsoft-logotypen och uttrycket Logga in med Microsoft ger som unikt representerar Azure AD bland eventuella andra identitetsleverantörer som fungerar med din app. Om det inte finns tillräckligt med utrymme för Logga in med Microsoft går det bra att förkorta det till Logga in. Du kan välja ett ljust eller ett mörkt färgschema för knapparna.

I det här diagrammet visar vi Microsofts riktlinjer när du använder tillgångarna med din app. Riktlinjerna gäller för Logga in med Microsoft och det kortare Logga in.

![Visar redlines för "Logga in med Microsoft"](./media/howto-add-branding-in-azure-ad-apps/sign-in-with-microsoft-redlines.png)

Du kan ladda ned de officiella bilderna och använda dem i din app. Högerklicka på den du vill använda och spara den på datorn.

| Tillgång                                | PNG-format | SVG-format |
| ------------------------------------ | ---------- | ---------- |
| Logga in med Microsoft (mörkt tema)  | ![Nedladdnings bar "Logga in med Microsoft"-knapp, mörkt tema PNG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark.png) | ![Nedladdnings bar "Logga in med Microsoft"-knapp, mörkt tema SVG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark.svg) |
| Logga in med Microsoft (ljust tema) | ![Nedladdnings bar "Logga in med Microsoft"-knapp ljus tema PNG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light.png) | ![Nedladdnings bar "Logga in med Microsoft"-knapp (ljust tema SVG)](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light.svg) |
| Logga in (mörkt tema)                 | ![Nedladdnings bar "logga in"-mörkt tema PNG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark_short.png) | ![Nedladdnings bar "logga in"-mörkt tema SVG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark_short.svg) |
| Logga in (ljust tema)                | ![Nedladdnings bar "Sign in"-tema PNG för kort knapp](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light_short.png) | ![Nedladdnings bar "Sign in"-tema för lätt tema](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light_short.svg) |

## <a name="branding-dos-and-donts"></a>Rekommendationer för varumärke

**JA.** Skriv ”arbets- eller skolkonto” i kombination med knappen Logga in med Microsoft. Det här är en fras användarna känner igen och vet hur de ska använda. **NEJ.** Skriv inte andra termer, exempelvis företagskonto, jobbkonto eller affärskonto.

Använd **inte** "Microsoft 365-ID" eller "Azure ID". Microsoft 365 är också namnet på ett konsument erbjudande från Microsoft, som inte använder Azure AD för autentisering.

**NEJ.** Ändra inte på Microsoft-logotypen.

**NEJ.** Visa inte varumärkena Azure och Active Directory för slutanvändarna. Det går bra att använda de här termerna när du riktar dig till utvecklare, IT-proffs och administratörer.

## <a name="navigation-dos-and-donts"></a>Rekommendationer vid navigering

**JA.** Gör det möjligt för användare att logga ut och växla till ett annat användarkonto. De flesta har bara ett privat konto hos Microsoft/Facebook/Google/Twitter, men många har flera konton som de använder i sitt arbete. Funktioner för flera inloggade användare kommer snart.
