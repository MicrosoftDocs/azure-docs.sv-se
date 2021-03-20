---
title: Översikt över Azure Active Directory Identity Protection säkerhet
description: Lär dig hur säkerhets översikten ger dig en inblick i din organisations säkerhets position.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95c589289d77597be2550673944c8fa21902e0fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93098475"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Azure Active Directory Identity Protection – Säkerhetsöversikt

I [säkerhets översikten](https://aka.ms/IdentityProtectionRefresh) i Azure Portal får du en inblick i din organisations säkerhets position. Det hjälper dig att identifiera potentiella attacker och förstå effektiviteten hos dina principer.

Säkerhets översikten är i stort sett uppdelad i två delar:

- Trender, till vänster, ger en tids linje med risk i din organisation.
- Panelerna till höger markerar du de viktiga pågående problemen i din organisation och föreslår hur du snabbt vidtar åtgärder.

:::image type="content" source="./media/concept-identity-protection-security-overview/01.png" alt-text="Skärm bild av Azure Portal säkerhets översikt. Liggande diagram visar antalet risker över tid. Sammanfattningar sammanfattar information om användare och inloggningar." border="false":::
  
## <a name="trends"></a>Trends

### <a name="new-risky-users-detected"></a>Nya riskfyllda användare har identifierats

Det här diagrammet visar antalet nya riskfyllda användare som identifierades under den valda tids perioden. Du kan filtrera visningen av det här diagrammet efter användar risk nivå (låg, medium, hög). Hovra över UTC-datumets steg för att se antalet riskfyllda användare som har identifierats för den dagen. Om du klickar på det här diagrammet visas rapporten riskfyllda användare. Överväg att ändra lösen ordet för att reparera användare som är utsatta för risk.

### <a name="new-risky-sign-ins-detected"></a>Nya riskfyllda inloggningar upptäcktes

Det här diagrammet visar antalet riskfyllda inloggningar som har identifierats under den valda tids perioden. Du kan filtrera visningen av det här diagrammet efter inloggnings risk typ (i real tid eller mängd) och inloggnings risk nivån (låg, medium, hög). Oskyddade inloggningar har slutförts i real tids risk inloggningar som inte är MFA-anropade. (Obs! inloggnings program som är riskfyllda på grund av att det inte går att skydda dem i real tid med hjälp av inloggnings risk principer). Hovra över UTC-datumen för att se hur många inloggningar som har identifierats vid risk för den dagen. Om du klickar på det här diagrammet visas rapporten "riskfyllda inloggningar".

## <a name="tiles"></a>Ikoner
 
### <a name="high-risk-users"></a>Användare med hög risk

Panelen "hög risk användare" visar det senaste antalet användare med hög sannolikhet för identitets kompromisser. Dessa bör vara en högsta prioritet för undersökning. Ett klick på panelen "hög risk användare" kommer att omdirigeras till en filtrerad vy av rapporten "riskfyllda användare" som endast visar användare med risk nivån hög. Med den här rapporten kan du läsa mer och åtgärda dessa användare med lösen ords återställning.

:::image type="content" source="./media/concept-identity-protection-security-overview/02.png" alt-text="Skärm bild av Azure Portal säkerhets översikt med paneler som är synliga för användare med hög risk och medelhög risk och andra riskfaktorer." border="false":::

### <a name="medium-risk-users"></a>Medel stora risk användare
Panelen "medel risk användare" visar det senaste antalet användare med medelhög sannolikhet för identitets kompromisser. Ett klick på panelen "medelhög risk användare" omdirigeras till en filtrerad vy av rapporten "riskfyllda användare" som endast visar användare med risk nivån medel. Med hjälp av den här rapporten kan du ytterligare undersöka och åtgärda dessa användare.

### <a name="unprotected-risky-sign-ins"></a>Oskyddade inloggnings tillägg

Panelen "oskyddade riskfyllda inloggningar" visar den senaste veckan antal lyckade, real tids riskfyllda inloggningar som inte blockerades eller MFA har anropats av en princip för villkorlig åtkomst, identitets skydds princip eller användarspecifika MFA. Detta är potentiellt komprometterade inloggningar som har lyckats och inte MFA-anrop. Använd en princip för inloggnings risker för att skydda sådana inloggningar i framtiden. Ett klick på panelen oskyddade riskfyllda inloggningar omdirigeras till bladet konfiguration av inloggnings risk princip där du kan konfigurera inloggnings risk principen så att den kräver MFA på en inloggning med en angiven risk nivå.

### <a name="legacy-authentication"></a>Äldre autentisering

Panelen "äldre autentisering" visar den senaste veckans antal äldre autentiseringar med risk som finns i din organisation. Äldre autentiseringsprotokoll stöder inte modern säkerhets metoder, till exempel ett MFA. Du kan använda en princip för villkorlig åtkomst för att förhindra äldre autentisering. Klicka på panelen "äldre autentisering" för att omdirigera dig till "Identity Secure Scores".

### <a name="identity-secure-score"></a>Identifiera säkra Poäng för identitet

Mät värden för identiteten säker Poäng och jämför din säkerhets position med bransch mönster. Om du klickar på panelen identitets säkra poäng (förhands granskning) omdirigeras du till bladet "identitets säkra poäng" där du kan lära dig mer om att förbättra din säkerhets position.

## <a name="next-steps"></a>Nästa steg

- [Vad är risk?](concept-identity-protection-risks.md)

- [Principer som är tillgängliga för att minimera risker](concept-identity-protection-policies.md)
