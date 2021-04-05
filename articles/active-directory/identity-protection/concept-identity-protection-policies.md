---
title: Azure AD Identity Protection principer
description: Identifiera de tre principer som är aktiverade med Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28a9080ce878e262573adf0b3c79394079c09ca2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835976"
---
# <a name="identity-protection-policies"></a>Principer om identitetsskydd

Azure Active Directory Identity Protection innehåller tre standard principer som administratörer kan välja att aktivera. Dessa principer omfattar begränsad anpassning men gäller för de flesta organisationer. Alla principer gör det möjligt att utesluta användare, till exempel ditt [nödfalls åtkomst-eller brytar-glas administratörs konton](../roles/security-emergency-access.md).

![Principer om identitetsskydd](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-ad-mfa-registration-policy"></a>Registrerings princip för Azure AD MFA

Identitets skydd kan hjälpa organisationer att distribuera Azure AD Multi-Factor Authentication (MFA) med hjälp av en princip för villkorlig åtkomst som kräver registrering vid inloggning. Att aktivera den här principen är ett bra sätt att se till att nya användare i din organisation har registrerat sig för MFA på den första dagen. Multi-Factor Authentication är en av de självbetjänings metoder som används för risk händelser inom identitets skydd. Med självbetjäning kan dina användare vidta åtgärder på egen hand för att minska antalet support samtals volymer.

Mer information om Azure AD Multi-Factor Authentication finns i artikeln [så här fungerar det: Azure AD Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Princip för inloggningsrisk

Identitets skydd analyserar signaler från varje inloggning, både i real tid och offline, och beräknar risk poängen baserat på sannolikheten att inloggningen inte utfördes av användaren. Administratörer kan fatta ett beslut baserat på den här risk Poäng signalen för att upprätthålla organisationens krav. Administratörer kan välja att blockera åtkomst, tillåta åtkomst eller tillåta åtkomst men kräver Multi-Factor Authentication.

Om risken identifieras kan användarna utföra Multi-Factor Authentication för att själv åtgärda och stänga den riskfyllda inloggnings händelsen för att förhindra onödig brus för administratörer.

> [!NOTE] 
> Användare måste tidigare ha registrerats för Azure AD Multi-Factor Authentication innan de kan utlösa inloggnings risk principen.

### <a name="custom-conditional-access-policy"></a>Anpassad princip för villkorlig åtkomst

Administratörer kan också välja att skapa en anpassad princip för villkorlig åtkomst inklusive inloggnings risk som ett tilldelnings villkor. Mer information om risker som ett villkor i en princip för villkorlig åtkomst finns i artikeln, [villkorlig åtkomst: villkor](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)

![Anpassad princip för inloggnings risk för anpassad villkorlig åtkomst](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Princip för användarrisk

Identitets skydd kan beräkna vad det anser vara normalt för en användares beteende och använda det för att basera besluten på deras risk. Användar risken är en beräkning av sannolikheten att en identitet har komprometterats. Administratörer kan fatta ett beslut baserat på den här risk Poäng signalen för att upprätthålla organisationens krav. Administratörer kan välja att blockera åtkomst, tillåta åtkomst eller tillåta åtkomst, men kräver en lösen ords ändring med lösen ords [återställning i Azure AD via självbetjäning](../authentication/howto-sspr-deployment.md).

Om risken identifieras kan användarna utföra lösen ords återställning via självbetjäning för att själv åtgärda och stänga användar risk händelsen för att förhindra onödig brus för administratörer.

> [!NOTE] 
> Användare måste tidigare ha registrerats för lösen ords återställning via självbetjäning innan användaren kan utlösa principen.

## <a name="next-steps"></a>Nästa steg

- [Aktivera återställning av lösen ord för självbetjäning i Azure AD](../authentication/howto-sspr-deployment.md)

- [Aktivera Azure AD-multifaktorautentisering](../authentication/howto-mfa-getstarted.md)

- [Aktivera registrerings princip för Azure AD-Multi-Factor Authentication](howto-identity-protection-configure-mfa-policy.md)

- [Aktivera inloggnings-och användar risk principer](howto-identity-protection-configure-risk-policies.md)
