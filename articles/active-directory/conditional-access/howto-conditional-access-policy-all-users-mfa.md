---
title: Villkorlig åtkomst – Kräv MFA för alla användare – Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst så att alla användare måste utföra Multi-Factor Authentication
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
ms.openlocfilehash: 6e6185c4bde71285fc163cae2af46f64ba052195
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994765"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Villkorlig åtkomst: Kräv MFA för alla användare

Som Alex-Weinert nämns katalogen med identitets säkerhet på Microsoft, som nämns i blogg inlägget [din pa $ $Word spelar ingen roll](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984):

> Ditt lösen ord spelar ingen roll, men MFA gör! Utifrån våra studier är ditt konto mer än 99,9% mindre troligt att bli komprometterat om du använder MFA.

Vägledningen i den här artikeln hjälper din organisation att skapa en balanserad MFA-princip för din miljö.

## <a name="user-exclusions"></a>Användar undantag

Principer för villkorlig åtkomst är kraftfulla verktyg, vi rekommenderar att du undantar följande konton från principen:

* **Nöd åtkomst** eller **Bryt** punkts konton för att förhindra konto utelåsning för hela klienten. I det osannolika scenariot är alla administratörer utelåsta från din klient, ditt administrations konto för nöd administration kan användas för att logga in på klienten och vidta åtgärder för att återställa åtkomsten.
   * Mer information finns i artikeln [Hantera nöd åtkomst konton i Azure AD](../roles/security-emergency-access.md).
* **Tjänst konton** och **tjänst huvud namn**, till exempel Azure AD Connect Sync-kontot. Tjänst konton är icke-interaktiva konton som inte är kopplade till någon viss användare. De används vanligt vis av backend-tjänster för att ge program mässig åtkomst till program, men de används också för att logga in på system för administrativa orsaker. Tjänst konton som dessa bör undantas eftersom MFA inte kan slutföras program mässigt. Anrop som görs av tjänstens huvud namn blockeras inte av villkorlig åtkomst.
   * Om din organisation har dessa konton som används i skript eller kod kan du ersätta dem med [hanterade identiteter](../managed-identities-azure-resources/overview.md). Som en tillfällig lösning kan du undanta dessa konton från bas linje principen.

## <a name="application-exclusions"></a>Program undantag

Organisationer kan ha många moln program som används. Inte alla dessa program kan kräva lika säkerhet. Löne-och närvaro programmen kan till exempel kräva MFA, men cafeteria är förmodligen inte det. Administratörer kan välja att undanta vissa program från sin princip.

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

Följande steg hjälper dig att skapa en princip för villkorlig åtkomst som kräver att alla användare utför Multi-Factor Authentication.

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Bläddra till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **tilldelningar** väljer **du användare och grupper**
   1. Under **Inkludera** väljer du **alla användare**
   1. Under **exkludera** väljer **du användare och grupper** och väljer organisationens nödfalls åtkomst eller Bryt glas konton. 
   1. Välj **Klar**.
1. Under **molnappar eller åtgärder**  >  **inkluderar** väljer du **alla molnappar**.
   1. Under **exkludera** väljer du alla program som inte kräver Multi-Factor Authentication.
1. Under **villkor**  >  **klient program (förhands granskning)** under **Välj de klient program som den här principen ska tillämpas på** lämna alla standardvärden markerade och välj **slutförd**.
1. Under **åtkomst kontroller**  >  **tilldelar** väljer du **bevilja åtkomst**, **kräver Multi-Factor Authentication** och väljer **Välj**.
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att skapa för att aktivera principen.

### <a name="named-locations"></a>Namngivna platser

Organisationer kan välja att inkludera kända nätverks platser som kallas **namngivna platser** i sina principer för villkorlig åtkomst. Dessa namngivna platser kan innehålla betrodda IPv4-nätverk som de för en huvud kontor. Mer information om hur du konfigurerar namngivna platser finns i artikeln [Vad är plats villkoret i Azure Active Directory villkorlig åtkomst?](location-condition.md)

I exempel principen ovan kan en organisation välja att inte kräva Multi-Factor Authentication om åtkomst till en molnbaserad app från företags nätverket. I det här fallet kan de lägga till följande konfiguration i principen:

1. Under **tilldelningar** väljer du **villkor**  >  **platser**.
   1. Konfigurera **Ja**.
   1. Ta med **vilken plats som helst**.
   1. Undanta **alla betrodda platser**.
   1. Välj **Klar**.
1. Välj **Klar**.
1. **Spara** ändringarna i principen.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Bestäm inverkan med endast rapport läge för villkorlig åtkomst](howto-conditional-access-insights-reporting.md)

[Simulera inloggnings beteende med hjälp av What If verktyget för villkorlig åtkomst](troubleshoot-conditional-access-what-if.md)
