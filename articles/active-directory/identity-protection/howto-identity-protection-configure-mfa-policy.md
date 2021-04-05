---
title: Konfigurera MFA-registrerings principen – Azure Active Directory Identity Protection
description: Lär dig hur du konfigurerar registrerings principen för Azure AD Identity Protection Multi-Factor Authentication.
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
ms.openlocfilehash: 072db1d47abd95844075aeedfeddc4f8cf6bf936
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835874"
---
# <a name="how-to-configure-the-azure-ad-multi-factor-authentication-registration-policy"></a>Gör så här: Konfigurera Azure AD-Multi-Factor Authentication registrerings princip

Azure AD Identity Protection hjälper dig att hantera distributionen av Azure AD Multi-Factor Authentication-registrering (MFA) genom att konfigurera en princip för villkorlig åtkomst för att kräva MFA-registrering oavsett vilken modern app för autentisering som du loggar in på.

## <a name="what-is-the-azure-ad-multi-factor-authentication-registration-policy"></a>Vad är registrerings principen för Azure AD-Multi-Factor Authentication?

Med Azure AD Multi-Factor Authentication kan du kontrol lera vem du använder mer än bara ett användar namn och lösen ord. Det innehåller ett andra säkerhets lager för användar inloggningar. För att användarna ska kunna svara på MFA-prompter måste de först registrera sig för Azure AD Multi-Factor Authentication.

Vi rekommenderar att du kräver Azure AD Multi-Factor Authentication för användar inloggningar eftersom det:

- Ger stark autentisering via en mängd verifierings alternativ.
- Spelar upp en viktig roll när du förbereder din organisation för att själv åtgärda problemet med att identifiera risker i identitets skyddet.

Mer information om Azure AD Multi-Factor Authentication finns i [Vad är Azure ad Multi-Factor Authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Principkonfiguration

1. Navigera till [Azure Portal](https://portal.azure.com).
1. Bläddra till **Azure Active Directory**  >  **säkerhets**  >  **identitets skydd**  >  **MFA registrerings princip**.
   1. Under **tilldelningar**
      1. **Användare** – Välj **alla användare** eller **Välj personer och grupper** om du vill begränsa distributionen.
         1. Du kan också välja att exkludera användare från principen.
   1. Under **kontroller**
      1. Se till att kryss rutan **Kräv Azure AD MFA-registrering** är markerad och välj **Välj**.
   1. **Tillämpa princip**  -  **På**
   1. **Spara**

## <a name="user-experience"></a>Användarupplevelse

Azure Active Directory Identity Protection uppmanas användarna att registrera sig nästa gången de loggar in interaktivt och de har 14 dagar på sig att slutföra registreringen. Under den här 14-dagars perioden kan de kringgå registreringen, men i slutet av perioden måste de registreras innan inloggnings processen kan slutföras.

En översikt över den relaterade användar upplevelsen finns i:

- [Inloggnings upplevelser med Azure AD Identity Protection](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Nästa steg

- [Aktivera inloggnings-och användar risk principer](howto-identity-protection-configure-risk-policies.md)

- [Aktivera återställning av lösen ord för självbetjäning i Azure AD](../authentication/howto-sspr-deployment.md)

- [Aktivera Azure AD-multifaktorautentisering](../authentication/howto-mfa-getstarted.md)
