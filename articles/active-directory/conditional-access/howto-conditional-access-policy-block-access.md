---
title: Villkorlig åtkomst – blockera åtkomst Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst för
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb,
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e0801daa5bf83889be87987d440e377287b5ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92366198"
---
# <a name="conditional-access-block-access"></a>Villkorlig åtkomst: blockera åtkomst

För organisationer med en försiktigt molnbaserad metod för molnbaserad migrering är blockera alla-principer ett alternativ som kan användas. 

> [!CAUTION]
> Felaktig konfiguration av en blockerande princip kan leda till att organisationer blir utelåst från Azure Portal.

Principer som dessa kan ha oönskade sido effekter. Korrekt testning och validering är avgörande innan du aktiverar. Administratörer bör använda verktyg som t. ex. [endast rapport läge för villkorlig åtkomst](concept-conditional-access-report-only.md) och [What If verktyget i villkorlig åtkomst när du](what-if-tool.md) gör ändringar.

## <a name="user-exclusions"></a>Användar undantag

Principer för villkorlig åtkomst är kraftfulla verktyg, vi rekommenderar att du undantar följande konton från principen:

* **Nöd åtkomst** eller **Bryt** punkts konton för att förhindra konto utelåsning för hela klienten. I det osannolika scenariot är alla administratörer utelåsta från din klient, ditt administrations konto för nöd administration kan användas för att logga in på klienten och vidta åtgärder för att återställa åtkomsten.
   * Mer information finns i artikeln [Hantera nöd åtkomst konton i Azure AD](../roles/security-emergency-access.md).
* **Tjänst konton** och **tjänst huvud namn**, till exempel Azure AD Connect Sync-kontot. Tjänst konton är icke-interaktiva konton som inte är kopplade till någon viss användare. De används vanligt vis av backend-tjänster för att ge program mässig åtkomst till program, men de används också för att logga in på system för administrativa orsaker. Tjänst konton som dessa bör undantas eftersom MFA inte kan slutföras program mässigt. Anrop som görs av tjänstens huvud namn blockeras inte av villkorlig åtkomst.
   * Om din organisation har dessa konton som används i skript eller kod kan du ersätta dem med [hanterade identiteter](../managed-identities-azure-resources/overview.md). Som en tillfällig lösning kan du undanta dessa konton från bas linje principen.

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

Följande steg hjälper dig att skapa principer för villkorlig åtkomst för att blockera åtkomst till alla appar förutom [Office 365](concept-conditional-access-cloud-apps.md#office-365) om användarna inte är i ett betrott nätverk. Dessa principer sätts i [läget endast för rapporter](howto-conditional-access-insights-reporting.md) för att starta så att administratörer kan bestämma vilken effekt de kommer att ha på befintliga användare. När administratörer är van vid att principerna ska gälla som de ska, kan de byta till **på**.

Den första principen blockerar åtkomst till alla appar förutom Microsoft 365 program om de inte är på en betrodd plats.

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Bläddra till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **Tilldelningar** väljer du **Användare och grupper**.
   1. Under **Inkludera** väljer du **alla användare**.
   1. Under **exkludera** väljer **du användare och grupper** och väljer organisationens nödfalls åtkomst eller Bryt glas konton. 
   1. Välj **Klar**.
1. Under **molnappar eller åtgärder** väljer du följande alternativ:
   1. Under **Inkludera** väljer du **alla molnappar**.
   1. Under **exkludera** väljer du **Office 365**, väljer **Välj** och väljer sedan **Slutför**.
1. Under **villkor**:
   1. Under **villkor**  >  **plats**.
      1. **Konfigurera** till **Ja**
      1. Under **Inkludera** väljer du **valfri plats**.
      1. Under **exkludera** väljer du **alla betrodda platser**.
      1. Välj **Klar**.
   1. Under **klient program (för hands version)** anger du **Konfigurera** till **Ja** och **väljer sedan** **klart**.
1. Under **åtkomst kontroller**  >  **beviljas** väljer du **blockera åtkomst** och väljer sedan **Välj**.
1. Bekräfta inställningarna och ange att **principen** endast ska **rapporteras**.
1. Välj **skapa** för att skapa för att aktivera principen.

En andra princip skapas nedan för att kräva Multi-Factor Authentication eller en kompatibel enhet för användare av Microsoft 365.

1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **Tilldelningar** väljer du **Användare och grupper**.
   1. Under **Inkludera** väljer du **alla användare**.
   1. Under **exkludera** väljer **du användare och grupper** och väljer organisationens nödfalls åtkomst eller Bryt glas konton. 
   1. Välj **Klar**.
1. Under **molnappar eller åtgärder**  >  **inkluderar** väljer du **Välj appar**, sedan **Office 365** och väljer **Välj** och sedan **Slutför**.
1. Under **åtkomst kontroller**  >  **bevilja** väljer du **bevilja åtkomst**.
   1. Välj **Kräv Multi-Factor Authentication** och **Kräv att enheten är markerad som kompatibel** Välj **Välj**.
   1. Se till att **alla markerade kontroller** är markerat.
   1. Välj **Välj**.
1. Bekräfta inställningarna och ange att **principen** endast ska **rapporteras**.
1. Välj **skapa** för att skapa för att aktivera principen.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Bestäm inverkan med endast rapport läge för villkorlig åtkomst](howto-conditional-access-insights-reporting.md)

[Simulera inloggnings beteende med hjälp av What If verktyget för villkorlig åtkomst](troubleshoot-conditional-access-what-if.md)
