---
title: Undersök risk Azure Active Directory Identity Protection
description: Lär dig hur du undersöker riskfyllda användare, identifieringar och inloggningar i Azure Active Directory Identity Protection
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
ms.openlocfilehash: be72c2152bdb8e1155d2dd29547f93ba3605d462
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95251101"
---
# <a name="how-to-investigate-risk"></a>Anvisningar: Undersöka risk

Identitets skydd ger organisationer med tre rapporter som de kan använda för att undersöka identitets risker i sin miljö. Dessa rapporter är **riskfyllda användare**, **riskfyllda inloggningar** och **risk identifieringar**. Undersökningen av händelser är en nyckel för att bättre förstå och identifiera svaga punkter i din säkerhets strategi.

I alla tre rapporter kan du hämta händelser i. CSV-format för ytterligare analys utanför Azure Portal. Rapporterna riskfyllda användare och riskfyllda inloggningar gör det möjligt att hämta de senaste 2500 posterna, medan rapporten risk identifieringar gör det möjligt att hämta de senaste 5000-posterna.

Organisationer kan dra nytta av Microsoft Graph API-integreringar för att samla in data med andra källor som de kan ha åtkomst till som en organisation.

De tre rapporterna finns i **Azure Portal**  >  **Azure Active Directory**  >  **säkerhet**.

## <a name="navigating-the-reports"></a>Navigera i rapporterna

Varje rapport öppnas med en lista över alla identifieringar för den period som visas överst i rapporten. Varje rapport gör det möjligt att lägga till eller ta bort kolumner baserat på administratörs preferenser. Administratörer kan välja att hämta data i. CSV eller. JSON-format. Rapporter kan filtreras med filtren högst upp i rapporten.

Om du väljer enskilda poster kan ytterligare poster visas överst i rapporten, till exempel möjligheten att bekräfta att en inloggning är komprometterad eller säker, bekräfta en användare som komprometterad eller ignorera användar risken.

Om du väljer enskilda poster expanderas ett informations fönster under identifieringarna. I vyn information kan administratörer undersöka och utföra åtgärder för varje identifiering. 

![Exempel på identitets skydds rapport som visar riskfyllda inloggningar och information](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>Riskfyllda användare

Med den information som rapporteras av riskfyllda användare kan administratörerna hitta:

- Vilka användare är utsatta för risker har de haft risk åtgärdade eller har haft risk har avslagits?
- Information om identifieringar
- Historik över alla riskfyllda inloggningar
- Risk historik
 
Administratörer kan sedan välja att vidta åtgärder för dessa händelser. Administratörer kan välja att:

- Återställ användar lösen ordet
- Bekräfta komprometterade användare
- Ignorera användar risk
- Blockera användare från att logga in
- Undersök ytterligare med Azure ATP

## <a name="risky-sign-ins"></a>Riskfyllda inloggningar

Rapporten över riskfyllda inloggningar innehåller filtrerings bara data för upp till de senaste 30 dagarna (1 månad).

Med informationen i rapporten om riskfyllda inloggningar kan administratörerna hitta:

- Vilka inloggningar som klassificeras som risk, bekräftat, bekräftat, bekräftat, avstängda eller åtgärdade.
- Real tids-och sammanställda risk nivåer kopplade till inloggnings försök.
- Utlösta identifierings typer
- Tillämpade principer för villkorlig åtkomst
- MFA-information
- Enhets information
- Programinformation
- Plats information

Administratörer kan sedan välja att vidta åtgärder för dessa händelser. Administratörer kan välja att:

- Bekräfta inloggnings problem
- Bekräfta inloggning på ett säkert sätt

> [!NOTE] 
> Identitets skyddet utvärderar risker för alla autentiserings flöden, oavsett om de är interaktiva eller icke-interaktiva. I inloggnings rapporten visas dock bara de interaktiva inloggnings programmen. Du kan se riskfyllda inloggningar som inträffat på icke-interaktiva inloggningar, men inloggningen visas inte i rapporten Azure AD-inloggningar.

## <a name="risk-detections"></a>Riskidentifieringar

Rapporten risk identifieringar innehåller filtrerings bara data för upp till de senaste 90 dagarna (tre månader).

Med den information som tillhandahålls av rapporten risk identifiering kan administratörerna hitta:

- Information om varje risk identifiering, inklusive typ.
- Andra risker som utlöses på samma tid
- Plats för inloggnings försök
- Länka till mer information från Microsoft Cloud App Security (MCAS).

Administratörer kan sedan välja att återgå till användarens risk-eller inloggnings rapport för att vidta åtgärder baserat på insamlad information.

> [!NOTE] 
> Vårt system kan upptäcka att risk händelsen som bidragit till risk användar risk poängen var falska positiva eller att användar risken reparerades med hjälp av principen, t. ex. att utföra en MFA-prompt eller en säker lösen ords ändring. Därför kommer vårt system att ignorera risk läget och en risk information om "AI Confirmed Sign-in Safe"-ytan och den kommer inte längre att bidra till användarens risk. 


## <a name="next-steps"></a>Nästa steg

- [Principer som är tillgängliga för att minimera risker](concept-identity-protection-policies.md)

- [Aktivera inloggnings-och användar risk principer](howto-identity-protection-configure-risk-policies.md)
