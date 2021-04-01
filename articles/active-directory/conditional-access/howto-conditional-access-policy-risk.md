---
title: Inloggnings risk baserad villkorlig åtkomst-Azure Active Directory
description: Skapa principer för villkorlig åtkomst med inloggnings risk för identitets skydd
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
ms.openlocfilehash: a09c4513206bea3462577ecba49b5d77b655b0e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91628272"
---
# <a name="conditional-access-sign-in-risk-based-conditional-access"></a>Villkorlig åtkomst: inloggnings risk baserad villkorlig åtkomst

De flesta användare har ett normalt beteende som kan spåras. När de hamnar utanför den här normen kan det vara riskabelt att tillåta att de bara loggar in. Du kanske vill blockera användaren eller bara be dem att utföra Multi-Factor Authentication för att bevisa att de verkligen är de som de säger att de är. 

En inloggnings risk representerar sannolikheten att en begäran om autentisering inte har behörighet av identitets ägaren. Organisationer med Azure AD Premium P2-licenser kan skapa principer för villkorlig åtkomst [som innehåller Azure AD Identity Protection inloggnings risk identifieringar](../identity-protection/concept-identity-protection-risks.md#sign-in-risk).

Det finns två platser där denna policy kan tilldelas. Organisationer bör välja något av följande alternativ för att aktivera en risk-baserad princip för villkorlig åtkomst som kräver en säker lösen ords ändring.

## <a name="enable-with-conditional-access-policy"></a>Aktivera med princip för villkorlig åtkomst

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Bläddra till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **Tilldelningar** väljer du **Användare och grupper**.
   1. Under **Inkludera** väljer du **alla användare**.
   1. Under **exkludera** väljer **du användare och grupper** och väljer organisationens nödfalls åtkomst eller Bryt glas konton. 
   1. Välj **Klar**.
1. Under **molnappar eller åtgärder**  >  **inkluderar** väljer du **alla molnappar**.
1. Under **villkor**  >  **inloggnings risk** anger du **Konfigurera** till **Ja**. Under **Välj den inloggnings risk nivå som den här principen ska tillämpas på** 
   1. Välj **hög** och **medel**.
   1. Välj **Klar**.
1. Under **åtkomst kontroller**  >  **tilldelar** väljer du **bevilja åtkomst**, **kräver Multi-Factor Authentication** och väljer **Välj**.
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att skapa för att aktivera principen.

## <a name="enable-through-identity-protection"></a>Aktivera via identitets skydd

1. Logga in på **Azure-portalen**.
1. Välj **alla tjänster** och bläddra sedan till **Azure AD Identity Protection**.
1. Välj **princip för inloggnings risk**.
1. Under **tilldelningar** väljer **du användare**.
   1. Under **Inkludera** väljer du **alla användare**.
   1. Under **exkludera** väljer du **Välj exkluderade användare**, väljer din organisations nödfalls åtkomst eller rast glass konton och väljer **Välj**.
   1. Välj **Klar**.
1. Under **villkor** väljer du **inloggnings risk** och väljer sedan **medel och över**.
   1. Välj **Välj** och sedan **Slutför**.
1. Under **kontroller**  >  **åtkomst** väljer du **Tillåt åtkomst** och väljer sedan **Kräv Multi-Factor Authentication**.
   1. Välj **Välj**.
1. Ange **tillämpa principen** på **på**.
1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Risk- och villkorsbaserad åtkomst – användare](howto-conditional-access-policy-risk-user.md)

[Bestäm inverkan med endast rapport läge för villkorlig åtkomst](howto-conditional-access-insights-reporting.md)

[Simulera inloggnings beteende med hjälp av What If verktyget för villkorlig åtkomst](troubleshoot-conditional-access-what-if.md)

[Vad är Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)
