---
title: Vad är läget endast i rapporten för villkorlig åtkomst? -Azure Active Directory
description: Hur kan du använda en rapport i läge med distribution av principer för villkorlig åtkomst
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: deced953089fcbb2640cbf3e402db24cb511e769
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935894"
---
# <a name="what-is-conditional-access-report-only-mode"></a>Vad är läget endast i rapporten för villkorlig åtkomst?

Villkorlig åtkomst används ofta av våra kunder för att skydda dig genom att använda rätt åtkomst kontroller i rätt situationer. Dock är en av utmaningarna med att distribuera en princip för villkorlig åtkomst i din organisation att fastställa konsekvenserna för slutanvändarna. Det kan vara svårt att förutse antalet och namnen på de användare som påverkas av vanliga distributions initiativ, till exempel att blockera äldre autentisering, kräva Multi-Factor Authentication för en population av användare eller implementera inloggnings risk principer. 

Endast rapport läge är ett nytt tillstånd för villkorlig åtkomst som gör att administratörer kan utvärdera effekten av principer för villkorlig åtkomst innan de aktive ras i deras miljö.  Med den här versionen av endast rapport läge:

- Principer för villkorlig åtkomst kan aktive ras i endast rapport läge.
- Under inloggningen utvärderas principer i endast rapport läge men tillämpas inte.
- Resultaten loggas i flikarna **villkorlig åtkomst** och **endast rapporter** i inloggnings loggen.
- Kunder med en Azure Monitor prenumeration kan övervaka påverkan av sina principer för villkorlig åtkomst med hjälp av arbets boken för villkorlig åtkomst.

> [!WARNING]
> Principer i läge för endast rapporter som kräver kompatibla enheter kan begära att användare på Mac, iOS och Android väljer ett enhets certifikat under princip utvärderingen, även om enhetens efterlevnad inte tillämpas. Dessa prompter kan upprepas tills enheten blir kompatibel. För att förhindra att slutanvändare får meddelanden under inloggningen utesluter du Mac-, iOS-och Android-enheter från enbart rapport principer som utför kontroll av enhetskompatibilitet. Observera att endast rapport läge är tillämpligt för principer för villkorlig åtkomst med "användar åtgärder"-omfattningen.

![Fliken endast rapporter i inloggnings loggen för Azure AD](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>Princip resultat

När en princip i endast rapport läge utvärderas för en specifik inloggning, finns det fyra nya möjliga resultat värden:

| Resultat | Beskrivning |
| --- | --- |
| Endast rapport: lyckad | Alla konfigurerade princip villkor, obligatoriska icke-interaktiva beviljade kontroller och kontroller av sessionen var uppfyllda. Till exempel uppfylls ett Multi-Factor Authentication-krav av ett MFA-anspråk som redan finns i token eller en kompatibel enhets princip är uppfylld genom att utföra en enhets kontroll på en kompatibel enhet. |
| Endast rapport: problem | Alla konfigurerade princip villkor uppfylldes men det var inte alla nödvändiga icke-interaktiva beviljade kontroller eller kontroll av sessionen uppfyllda. En princip gäller till exempel en användare där en blockerande kontroll har kon figurer ATS, eller en enhet som inte uppfyller en kompatibel enhets princip. |
| Endast rapport: användar åtgärd krävs | Alla konfigurerade princip villkor uppfylldes men användar åtgärd krävs för att uppfylla de nödvändiga kontroll-eller sessions kontrollerna. Med endast rapport läge uppmanas användaren inte att uppfylla de nödvändiga kontrollerna. Användarna uppmanas till exempel inte att använda Multi-Factor Authentication-utmaningar eller användnings villkor.   |
| Endast rapport: används inte | Alla konfigurerade princip villkor uppfylldes inte. Användaren är till exempel exkluderad från principen eller så gäller principen endast vissa betrodda namngivna platser. |

## <a name="conditional-access-insights-workbook"></a>Arbets bok för villkorlig åtkomst

Administratörer har möjlighet att skapa flera principer i enbart rapport läge, så det är nödvändigt att förstå både den enskilda effekten av varje princip och den kombinerade effekten av flera principer som utvärderas tillsammans. Med den nya arbets boken för villkorlig åtkomst kan administratörer visualisera villkorliga åtkomst frågor och övervaka effekten av en princip för ett specifikt tidsintervall, en uppsättning program och användare. 
 
## <a name="next-steps"></a>Nästa steg

[Konfigurera endast rapport läge för en princip för villkorlig åtkomst](howto-conditional-access-insights-reporting.md)
