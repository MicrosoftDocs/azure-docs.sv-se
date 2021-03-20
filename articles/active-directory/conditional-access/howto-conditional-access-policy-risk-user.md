---
title: Användar risk-baserad villkorlig åtkomst-Azure Active Directory
description: Skapa principer för villkorlig åtkomst med användar risk för identitets skydd
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5423a61c8febee72f32935f3dee4e9f9e868630
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89049085"
---
# <a name="conditional-access-user-risk-based-conditional-access"></a>Villkorlig åtkomst: användar risk-baserad villkorlig åtkomst

Microsoft arbetar med forskare, polismyndigheter, olika säkerhets team på Microsoft och andra betrodda källor för att hitta läckta användar namn och lösen ords par. Organisationer med Azure AD Premium P2-licenser kan skapa principer för villkorlig åtkomst som innehåller [Azure AD Identity Protection identifieringar av användar risker](../identity-protection/concept-identity-protection-risks.md#user-risk). 

Det finns två platser där denna policy kan tilldelas. Organisationer bör välja något av följande alternativ för att aktivera en användar riskbaserade princip för villkorlig åtkomst som kräver en säker lösen ords ändring.

## <a name="enable-with-conditional-access-policy"></a>Aktivera med princip för villkorlig åtkomst

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Bläddra till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer. Mer information får du genom att [Ange namngivnings standarder för dina principer](./plan-conditional-access.md#set-naming-standards-for-your-policies).
1. Under **Tilldelningar** väljer du **Användare och grupper**.
   1. Under **Inkludera** väljer du **alla användare**.
   1. Under **exkludera** väljer **du användare och grupper** och väljer organisationens nödfalls åtkomst eller Bryt glas konton. 
   1. Välj **Klar**.
1. Under **molnappar eller åtgärder**  >  **inkluderar** väljer du **alla molnappar**.
1. Under **villkor**  >  **användar risk** ställer du in **Konfigurera** på **Ja**. Under **Konfigurera de användar risk nivåer som krävs för att principen ska tillämpas väljer du** är **hög** och väljer sedan **slutförd**.
1. Under **åtkomst kontroller**  >  **tilldelar** väljer du **bevilja åtkomst**, **kräver lösen ords ändring** och väljer **Välj**.
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att skapa för att aktivera principen.

## <a name="enable-through-identity-protection"></a>Aktivera via identitets skydd

1. Logga in på **Azure-portalen**.
1. Välj **alla tjänster** och bläddra sedan till **Azure AD Identity Protection**.
1. Välj **princip för användar risk**.
1. Under **tilldelningar** väljer **du användare**.
   1. Under **Inkludera** väljer du **alla användare**.
   1. Under **exkludera** väljer du **Välj exkluderade användare**, väljer din organisations nödfalls åtkomst eller rast glass konton och väljer **Välj**.
   1. Välj **Klar**.
1. Välj **användar risk** under **villkor** och välj sedan **hög**.
   1. Välj **Välj** och sedan **Slutför**.
1. Under **kontroller**  >  **åtkomst** väljer du **Tillåt åtkomst** och väljer sedan **Kräv lösen ords ändring**.
   1. Välj **Välj**.
1. Ange **tillämpa principen** på **på**.
1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Risk- och villkorsbaserad åtkomst – inloggning](howto-conditional-access-policy-risk.md)

[Bestäm inverkan med endast rapport läge för villkorlig åtkomst](howto-conditional-access-insights-reporting.md)

[Simulera inloggnings beteende med hjälp av What If verktyget för villkorlig åtkomst](troubleshoot-conditional-access-what-if.md)

[Vad är Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)