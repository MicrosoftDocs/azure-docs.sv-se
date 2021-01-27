---
title: Villkorlig åtkomst – blockera äldre autentisering – Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst för att blockera bakåtkompatibla autentiseringsprotokoll
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/22/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 982c33c5c55e32ce48131a4b2f0911e4f23dd408
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98877296"
---
# <a name="conditional-access-block-legacy-authentication"></a>Villkorlig åtkomst: blockera äldre autentisering

På grund av den ökade risken som är associerad med äldre autentiseringsprotokoll rekommenderar Microsoft att organisationer blockerar autentiseringsbegäranden med dessa protokoll och kräver modern autentisering.

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

Följande steg hjälper dig att skapa en princip för villkorlig åtkomst för att blockera äldre autentiseringsbegäranden. Den här principen används endast i [rapport läge](howto-conditional-access-insights-reporting.md) för att starta så att administratörer kan bestämma vilken effekt de kommer att ha på befintliga användare. När administratörer är bekanta med att principen gäller när de avser, kan de växla till eller mellanlagra distributionen genom att **lägga till vissa** grupper och utesluta andra.

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Bläddra till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **tilldelningar** väljer **du användare och grupper**
   1. Under **Inkludera** väljer du **alla användare**.
   1. Under **exkludera** väljer **du användare och grupper** och väljer alla konton som måste upprätthålla möjligheten att använda äldre autentisering. Undanta minst ett konto för att förhindra att ditt konto blir utelåst. Om du inte utesluter något konto kommer du inte att kunna skapa den här principen.
   1. Välj **Klar**.
1. Under **molnappar eller åtgärder** väljer du **alla molnappar**.
   1. Välj **Klar**.
1.   >  Ange **Konfigurera** till **Ja** under villkor för **klient program**.
   1. Kontrol lera bara rutorna **Exchange ActiveSync-klienter** och **andra klienter**.
   1. Välj **Klar**.
1. Under **åtkomst kontroller**  >  **bevilja** väljer du **blockera åtkomst**.
   1. Välj **Välj**.
1. Bekräfta inställningarna och ange att **principen** endast ska **rapporteras**.
1. Välj **skapa** för att skapa för att aktivera principen.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Bestäm inverkan med endast rapport läge för villkorlig åtkomst](howto-conditional-access-insights-reporting.md)

[Simulera inloggnings beteende med hjälp av What If verktyget för villkorlig åtkomst](troubleshoot-conditional-access-what-if.md)

[Konfigurera en multifunktions enhet eller ett program för att skicka e-post med hjälp av Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)
