---
title: Ge feedback om risker i Azure Active Directory Identity Protection
description: Hur och varför bör du ge feedback om identifieringar av identitets skydds risker.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 939ffc9e2a8fb8ce3a25dc212e3df34dc6bb2ec1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88950365"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Så här gör du för att ge risk feedback i Azure AD Identity Protection

Med Azure AD Identity Protection kan du ge feedback om riskbedömningen. I följande dokument visas de scenarier där du vill ge feedback om Azure AD Identity Protections riskbedömning och hur vi införlivar det.

## <a name="what-is-a-detection"></a>Vad är en identifiering?

Identifiering av identitets skydd är en indikation på misstänkt aktivitet från ett identitets risk perspektiv. Dessa misstänkta aktiviteter kallas risk identifieringar. Dessa identitetsbaserade identifieringar kan baseras på heuristik, maskin inlärning eller kan komma från partner produkter. Dessa identifieringar används för att fastställa inloggnings risker och användar risker.

* Användar risken representerar sannolikheten för att en identitet komprometteras.
* Inloggnings risk representerar sannolikheten för att en inloggning är komprometterad (till exempel att inloggningen inte är auktoriserad av identitets ägaren).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Varför ska jag ge risk för feedback till Azure ADs riskbedömningar? 

Det finns flera orsaker till varför du bör ge feedback om Azure AD:

- **Azure AD-användarens eller inloggnings risk utvärderingen hittades felaktigt**. En inloggning som visas i rapporten "riskfyllda inloggningar" var till exempel ofarlig och all identifiering av inloggningen var falska positiva identifieringar.
- **Du verifierade att Azure AD: s användare eller inloggnings riskbedömning är korrekt**. Till exempel har en inloggning som visas i rapporten riskfyllda inloggningar varit skadlig och du vill att Azure AD ska veta att alla identifieringar av inloggningen var sanna positiva.
- **Du har reparerat risken för den användaren utanför Azure AD Identity Protection** och du vill att användarens risk nivå ska uppdateras.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Hur används min risk feedback i Azure AD?

Azure AD använder din feedback för att uppdatera risken för underliggande användare och/eller inloggning och noggrannheten i dessa händelser. Den här feedbacken hjälper till att skydda slutanvändaren. När du till exempel har bekräftat att en inloggning har komprometterats ökar Azure AD omedelbart användarens risk och den sammanlagda risken för inloggning (inte real tids risk) till hög. Om den här användaren ingår i din användar risk princip för att tvinga hög risk användare att återställa sina lösen ord på ett säkert sätt, kommer användaren automatiskt att åtgärda dem nästa gången de loggar in.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Hur ska jag ge risk för feedback och vad som händer under huven?

Här är scenarier och mekanismer för att ge feedback till Azure AD.

| Scenario | Hur ger jag feedback? | Vad händer under huven? | Kommentarer |
| --- | --- | --- | --- |
| **Inloggning är inte komprometterad (falskt positivt)** <br> Rapporten riskfyllda inloggningar visar en risk inloggning [risk tillstånd = i fara], men den inloggningen har inte komprometterats. | Välj inloggning och klicka på bekräfta inloggning säker. | Azure AD flyttar inloggnings summan risk till ingen [risk tillstånd = bekräftat säker, Risk nivå (agg regering) =-] och kommer att återföra sin inverkan på användar risken. | För närvarande är alternativet "bekräfta inloggning säker" endast tillgängligt i rapporten om riskfyllda inloggningar. |
| **Inloggning komprometterad (sant positivt)** <br> Rapporten riskfyllda inloggningar visar en risk inloggning [risk tillstånd = i fara] med låg risk [risk nivå (agg regering) = låg] och att inloggningen faktiskt har komprometterats. | Välj inloggningen och klicka på bekräfta att inloggningen är komprometterad. | Azure AD flyttar inloggnings mängdens sammanlagda risk och användar risken till hög [risk tillstånd = bekräftat komprometterad; Risk nivå = hög]. | För närvarande finns inte alternativet bekräfta att inloggningen är komprometterat i rapporten om riskfyllda inloggningar. |
| **Komprometterad användare (sant positivt)** <br> Rapporten "riskfyllda användare" visar en risk användare [risk tillstånd = i fara] med låg risk [risk nivå = låg] och användaren har komprometterats. | Välj användaren och klicka på bekräfta komprometterad användare. | Azure AD kommer att flytta användar risken till hög [risk tillstånd = bekräftat komprometterad; Risk nivå = hög] och lägger till en ny "administratör som bekräftat att användaren har komprometterats". | Alternativet bekräfta användare som är komprometterat är för närvarande endast tillgängligt i rapporten riskfyllda användare. <br> Identifierings administratören bekräftade att användaren har komprometterats visas på fliken risk identifieringar som inte är länkade till en inloggning i rapporten riskbaserade användare. |
| **Användaren åtgärdade utanför Azure AD Identity Protection (sant positiv + åtgärdad)** <br> Rapporten "riskfyllda användare" visar en risk användare och har sedan reparerat användaren utanför Azure AD Identity Protection. | 1. Markera användaren och klicka på bekräfta komprometterad användare. (Den här processen bekräftar till Azure AD att användaren verkligen har komprometterats.) <br> 2. vänta tills användarens risk nivå går till hög. (Den här gången ger Azure AD den tid som krävs för att ta ovanstående feedback till risk motorn.) <br> 3. Välj användaren och klicka på "Stäng användar risk". (Den här processen bekräftar till Azure AD att användaren inte längre komprometteras.) |  Azure AD flyttar användar risken till ingen [risk tillstånd = avstängd; Risk nivå =-] och stänger risken för alla befintliga inloggningar som har aktiva risker. | Om du klickar på "ignorera användar risk" stängs all risk för användaren och tidigare inloggningar. Det går inte att utföra den här åtgärden. |
| **Användaren är inte komprometterad (falskt positivt)** <br> Rapporten "riskfyllda användare" visar vid risk användare, men användaren är inte komprometterad. | Välj användaren och klicka på "Stäng användar risk". (Den här processen bekräftar till Azure AD att användaren inte har komprometterats.) | Azure AD flyttar användar risken till ingen [risk tillstånd = avstängd; Risk nivå =-]. | Om du klickar på "ignorera användar risk" stängs all risk för användaren och tidigare inloggningar. Det går inte att utföra den här åtgärden. |
| Jag vill stänga användar risken, men jag är inte säker på om användaren är komprometterad/säker. | Välj användaren och klicka på "Stäng användar risk". (Den här processen bekräftar till Azure AD att användaren inte längre komprometteras.) | Azure AD flyttar användar risken till ingen [risk tillstånd = avstängd; Risk nivå =-]. | Om du klickar på "ignorera användar risk" stängs all risk för användaren och tidigare inloggningar. Det går inte att utföra den här åtgärden. Vi rekommenderar att du reparerar användaren genom att klicka på Återställ lösen ord eller be användaren att återställa eller ändra sina autentiseringsuppgifter på ett säkert sätt. |

Feedback om användar risk identifieringar i identitets skydd bearbetas offline och kan ta lite tid att uppdatera. I kolumnen risk bearbetnings tillstånd visas det aktuella läget för feedback-bearbetning.

![Risk bearbetnings tillstånd för riskfyllda användar rapporter](./media/howto-identity-protection-risk-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Nästa steg

- [Referens för Azure Active Directory Identity Protection risk identifiering](./concept-identity-protection-risks.md)