---
title: Risk principer – Azure Active Directory Identity Protection
description: Aktivera och konfigurera risk principer i Azure Active Directory Identity Protection
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
ms.openlocfilehash: 366d68be1a7f115980973015e363da6095876754
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997638"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>Gör så här: Konfigurera och aktivera risk principer

Som vi lärt dig i föregående artikel har [identitets skydds principer](concept-identity-protection-policies.md) två risk principer som vi kan aktivera i vår katalog. 

- Princip för inloggningsrisk
- Princip för användarrisk

![Sidan säkerhets översikt för att aktivera användar-och inloggnings risk principer](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Båda principerna arbetar för att automatisera svaret på risk identifieringar i din miljö och ge användare möjlighet att åtgärda problemet när risken upptäcks. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>Förutsättningar 

Om din organisation vill tillåta användare att själv åtgärda när risker upptäcks måste användarna registreras för lösen ords återställning via självbetjäning och Azure AD Multi-Factor Authentication. Vi rekommenderar att [du aktiverar den kombinerade registrerings upplevelsen för säkerhets information](../authentication/howto-registration-mfa-sspr-combined.md) för bästa möjliga upplevelse. Genom att tillåta användare att själv reparera får de tillbaka ett produktivt tillstånd snabbare utan att kräva administratörs åtgärder. Administratörer kan fortfarande se dessa händelser och undersöka dem efter faktumet. 

## <a name="choosing-acceptable-risk-levels"></a>Välja acceptabla risk nivåer

Organisationerna måste bestämma den risk nivå de behöver för att kunna godkänna användar upplevelsen och säkerhets position. 

Microsofts rekommendation är att ange tröskelvärdet för användar risk princip till **hög** och inloggnings risk policyn till **medium och över**.

Om du väljer ett **högt** tröskelvärde minskar antalet gånger som en princip utlöses och minimerar påverkan på användare. Den utesluter dock inte **låg** och **medelhög** risk identifiering från principen, som inte kan hindra en angripare från att utnyttja en komprometterad identitet. Om du väljer en **låg** tröskel införs ytterligare avbrott i användaren, men ökad säkerhet position.

## <a name="exclusions"></a>Undantag

Alla principer gör det möjligt att utesluta användare, till exempel ditt [nödfalls åtkomst-eller brytar-glas administratörs konton](../roles/security-emergency-access.md). Organisationer kan avgöra om de behöver utesluta andra konton från vissa principer baserat på hur kontona används. Alla undantag bör granskas regelbundet för att se om de fortfarande är tillämpliga.

Konfigurerade betrodda [nätverks platser](../conditional-access/location-condition.md) används av identitets skydd i vissa risk identifieringar för att minska antalet falska positiva identifieringar.

## <a name="enable-policies"></a>Aktivera principer

Utför följande steg för att aktivera användar risk-och inloggnings risk principer.

1. Navigera till [Azure Portal](https://portal.azure.com).
1. Bläddra till **Azure Active Directory**  >  **säkerhets**  >  **identitets skydd**  >  **– Översikt**.
1. Välj **princip för användar risk**.
   1. Under **tilldelningar**
      1. **Användare** – Välj **alla användare** eller **Välj personer och grupper** om du vill begränsa distributionen.
         1. Du kan också välja att exkludera användare från principen.
      1. **Villkor**  -  **Användar risk** Microsofts rekommendation är att ställa in det här alternativet på **hög**.
   1. Under **kontroller**
      1. **Åtkomst** – Microsofts rekommendation är att **tillåta åtkomst** och **Kräv lösen ords ändring**.
   1. **Tillämpa princip**  -  **På**
   1. **Spara** – den här åtgärden kommer att gå tillbaka till **översikts** sidan.
1. Välj **princip för inloggnings risk**.
   1. Under **tilldelningar**
      1. **Användare** – Välj **alla användare** eller **Välj personer och grupper** om du vill begränsa distributionen.
         1. Du kan också välja att exkludera användare från principen.
      1. **Villkor**  -  **Inloggnings risk** Microsofts rekommendation är att ställa in det här alternativet på **medel och över**.
   1. Under **kontroller**
      1. **Åtkomst** – Microsofts rekommendation är att **tillåta åtkomst** och **kräva Multi-Factor Authentication**.
   1. **Tillämpa princip**  -  **På**
   1. **Spara**

## <a name="next-steps"></a>Nästa steg

- [Aktivera registrerings princip för Azure AD-Multi-Factor Authentication](howto-identity-protection-configure-mfa-policy.md)

- [Vad är risk?](concept-identity-protection-risks.md)

- [Undersöka riskidentifieringar](howto-identity-protection-investigate-risk.md)

- [Simulera riskidentifieringar](howto-identity-protection-simulate-risk.md)
