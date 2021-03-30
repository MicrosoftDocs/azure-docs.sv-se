---
title: Villkorlig åtkomst – Kräv MFA för Azure-hantering – Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst för att kräva Multi-Factor Authentication för Azure-hanterings uppgifter
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e2b6b3e9a6bdead4e4da7f1a829698d86cfbf52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92366181"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>Villkorlig åtkomst: Kräv MFA för Azure-hantering

Organisationer använder en mängd olika Azure-tjänster och hanterar dem från Azure Resource Manager baserade verktyg som:

* Azure Portal
* Azure PowerShell
* Azure CLI

Dessa verktyg kan ge hög privilegie rad åtkomst till resurser, som kan ändra konfigurationer för hela konfigurationen, tjänst inställningar och fakturering av prenumerationer. För att skydda dessa privilegierade resurser rekommenderar Microsoft att du kräver Multi-Factor Authentication för alla användare som har åtkomst till dessa resurser.

## <a name="user-exclusions"></a>Användar undantag

Principer för villkorlig åtkomst är kraftfulla verktyg, vi rekommenderar att du undantar följande konton från principen:

* **Nöd åtkomst** eller **Bryt** punkts konton för att förhindra konto utelåsning för hela klienten. I det osannolika scenariot är alla administratörer utelåsta från din klient, ditt administrations konto för nöd administration kan användas för att logga in på klienten och vidta åtgärder för att återställa åtkomsten.
   * Mer information finns i artikeln [Hantera nöd åtkomst konton i Azure AD](../roles/security-emergency-access.md).
* **Tjänst konton** och **tjänst huvud namn**, till exempel Azure AD Connect Sync-kontot. Tjänst konton är icke-interaktiva konton som inte är kopplade till någon viss användare. De används vanligt vis av backend-tjänster för att ge program mässig åtkomst till program, men de används också för att logga in på system för administrativa orsaker. Tjänst konton som dessa bör undantas eftersom MFA inte kan slutföras program mässigt. Anrop som görs av tjänstens huvud namn blockeras inte av villkorlig åtkomst.
   * Om din organisation har dessa konton som används i skript eller kod kan du ersätta dem med [hanterade identiteter](../managed-identities-azure-resources/overview.md). Som en tillfällig lösning kan du undanta dessa konton från bas linje principen.

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

Följande steg hjälper dig att skapa en princip för villkorlig åtkomst som kräver att de har åtkomst till den [Microsoft Azure hanterings](concept-conditional-access-cloud-apps.md#microsoft-azure-management) appen för att utföra Multi-Factor Authentication.

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Bläddra till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **tilldelningar** väljer **du användare och grupper**
   1. Under **Inkludera** väljer du **alla användare**.
   1. Under **exkludera** väljer **du användare och grupper** och väljer organisationens nödfalls åtkomst eller Bryt glas konton. 
   1. Välj **Klar**.
1. Under **molnappar eller åtgärder**  >  **inkluderar** väljer du **Välj appar**, Välj **Microsoft Azure hantering** och välj sedan **Välj** . 
1. Under **villkor**  >  **klient program (förhands granskning)** under **Välj de klient program som den här principen ska tillämpas på** lämna alla standardvärden markerade och välj **slutförd**.
1. Under **åtkomst kontroller**  >  **tilldelar** väljer du **bevilja åtkomst**, **kräver Multi-Factor Authentication** och väljer **Välj**.
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att skapa för att aktivera principen.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Bestäm inverkan med endast rapport läge för villkorlig åtkomst](howto-conditional-access-insights-reporting.md)

[Simulera inloggnings beteende med hjälp av What If verktyget för villkorlig åtkomst](troubleshoot-conditional-access-what-if.md)
