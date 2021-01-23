---
title: Använda min personal för att delegera användar hantering (för hands version) – Azure AD | Microsoft Docs
description: Delegera användar hantering med min personal och administrativa enheter
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.topic: how-to
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 05/08/2020
ms.author: rolyon
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 304d88dfcf0c72769f27911186df04c66224419e
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740150"
---
# <a name="manage-your-users-with-my-staff-preview"></a>Hantera dina användare med min personal (förhands granskning)

Med min personal kan du delegera till en bild av auktoriteten, till exempel en butiks chef eller en grupp ledare, behörigheterna för att säkerställa att deras personal medlemmar kan komma åt sina Azure AD-konton. I stället för att lita på en central helpdesk kan organisationer delegera vanliga uppgifter, till exempel att återställa lösen ord eller ändra telefonnumret till en team chef. Med min personal kan en användare som inte har åtkomst till sitt konto få åtkomst på bara några klick, utan supportavdelningen eller IT-personal som krävs.

Innan du konfigurerar min personal för din organisation rekommenderar vi att du läser igenom den här dokumentationen och [användar dokumentationen](../user-help/my-staff-team-manager.md) för att se till att du förstår funktionerna och effekten av den här funktionen på användarna. Du kan använda användar dokumentationen för att träna och förbereda dina användare för den nya upplevelsen och hjälpa till att se till att distributionen lyckas.

SMS-baserad autentisering för användare är en offentlig förhands gransknings funktion i Azure Active Directory. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versionerna

## <a name="how-my-staff-works"></a>Så här fungerar min personal

Min personal är baserad på administrativa enheter (Australien), som är en behållare med resurser som kan användas för att begränsa omfattningen av en roll tilldelnings administrativ kontroll. I min personal används Australien för att definiera en delmängd av en organisations användare, till exempel en butik eller avdelning. Till exempel kan en team ansvarig tilldelas till en roll vars omfång är en eller flera Australien. I exemplet nedan har användaren beviljats den administrativa rollen för autentisering och de tre centrala mapparna är rollens omfattning. Mer information om administrativa enheter finns [i hantering av administrativa enheter i Azure Active Directory](administrative-units.md).

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra den här artikeln behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.

  * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration.

  * Om det behövs kan du [skapa en Azure Active Directory klient](../fundamentals/sign-up-organization.md) eller [associera en Azure-prenumeration med ditt konto](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
* Du behöver *Global administratörs* behörighet i Azure AD-klienten för att aktivera SMS-baserad autentisering.
* Varje användare som är aktive rad i principen för autentiseringsmetoden för SMS måste vara licensierad, även om de inte använder den. Varje aktive rad användare måste ha någon av följande Azure AD-eller Microsoft 365-licenser:

  * [Azure AD Premium P1 eller P2](https://azure.microsoft.com/pricing/details/active-directory/)
  * [Microsoft 365 (M365) F1 eller F3](https://www.microsoft.com/licensing/news/m365-firstline-workers)
  * [Enterprise Mobility + Security (EMS) E3 eller E5](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing) eller [Microsoft 365 (M365) E3 eller E5](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)

## <a name="how-to-enable-my-staff"></a>Så här aktiverar du min personal

När du har konfigurerat Australien kan du tillämpa det här omfånget på dina användare som har åtkomst till min personal. Endast användare som har tilldelats en administrativ roll har åtkomst till min personal. Utför följande steg för att aktivera min personal:

1. Logga in på Azure Portal som användar administratör.
2. Bläddra till **Azure Active Directory**  >  **användar inställningar**  >  **användar funktions** förhands  >  **granskningar hantera användar funktions förhands gransknings inställningar**.
3. Under **Administratörer kan komma åt min personal** kan du välja att aktivera för alla användare, valda användare eller ingen användar åtkomst.

> [!Note]
> Endast användare som har tilldelats en administratörs roll har åtkomst till min personal. Om du aktiverar min personal för en användare som inte har tilldelats någon administratörs roll kan de inte komma åt min personal.

## <a name="conditional-access"></a>Villkorlig åtkomst

Du kan skydda min personal Portal med hjälp av principen för villkorlig åtkomst för Azure AD. Använd den för aktiviteter som kräver multifaktorautentisering innan du får åtkomst till min personal.

Vi rekommenderar starkt att du skyddar min personal med [villkorliga åtkomst principer för Azure AD](../conditional-access/index.yml). Om du vill tillämpa en princip för villkorlig åtkomst för min personal måste du skapa tjänstens huvud namn för min personal manuellt med hjälp av PowerShell.

### <a name="apply-a-conditional-access-policy-to-my-staff"></a>Tillämpa en princip för villkorlig åtkomst för min personal

1. Installera [PowerShell-cmdletarna för Microsoft Graph beta](https://github.com/microsoftgraph/msgraph-sdk-powershell/blob/dev/samples/0-InstallModule.ps1).
1. Kör följande kommandon:

   ```powershell
   Connect-Graph -Scopes "Directory.AccessAsUser.All"
   New-MgServicePrincipal -DisplayName "My Staff" -AppId "ba9ff945-a723-4ab5-a977-bd8c9044fe61"
   ```
1. Skapa en princip för villkorlig åtkomst som gäller för moln programmet min personal.

    ![Skapa en princip för villkorlig åtkomst för min personal-appen](./media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>Använda min personal

När en användare går till min personal visas namnen på de [administrativa enheter](administrative-units.md) som de har administratörs behörighet för. I [användar dokumentationen för min personal](../user-help/my-staff-team-manager.md)använder vi termen "plats" för att referera till administrativa enheter. Om en administratörs behörighet inte har någon AU-omfattning gäller behörigheterna i hela organisationen. När min personal har Aktiver ATS kan de användare som är aktiverade och har tilldelats en administrativ roll komma åt den via [https://mystaff.microsoft.com](https://mystaff.microsoft.com) . De kan välja en AU för att visa användarna i den AU och välja en användare för att öppna profilen.

## <a name="reset-a-users-password"></a>Återställa ett användarlösenord

Följande roller har behörighet att återställa en användares lösen ord:

- [Administratör för autentisering](permissions-reference.md#authentication-administrator)
- [Administratör för privilegie rad autentisering](permissions-reference.md#privileged-authentication-administrator)
- [Global administratör](permissions-reference.md#global-administrator--company-administrator)
- [Support administratör](permissions-reference.md#helpdesk-administrator)
- [Användaradministratör](permissions-reference.md#user-administrator)
- [Lösenordsadministratör](permissions-reference.md#password-administrator)

Öppna en användar profil från **min personal**. Välj **Återställ lösen ord**.

- Om användaren endast är molnbaserad kan du se ett tillfälligt lösen ord som du kan ge användaren.
- Om användaren har synkroniserats från lokala Active Directory kan du ange ett lösen ord som uppfyller dina lokala AD-principer. Du kan sedan ange lösen ordet för användaren.

    ![Förlopps indikator för lösen ords återställning och lyckad avisering](./media/my-staff-configure/reset-password.png)

Användaren måste ändra sitt lösen ord nästa gången de loggar in.

## <a name="manage-a-phone-number"></a>Hantera ett telefonnummer

Öppna en användar profil från **min personal**.

- Lägg till ett telefonnummer för användaren genom att välja **Lägg till telefonnummer**
- Välj **redigera telefonnummer** för att ändra telefonnumret
- Ta bort telefonnumret för användaren genom att välja **ta bort telefonnummer**

Beroende på dina inställningar kan användaren sedan använda det telefonnummer som du ställt in för att logga in med SMS, utföra Multi-Factor Authentication och utföra lösen ords återställning via självbetjäning.

Om du vill hantera en användares telefonnummer måste du ha tilldelats någon av följande roller:

- [Administratör för autentisering](permissions-reference.md#authentication-administrator)
- [Administratör för privilegie rad autentisering](permissions-reference.md#privileged-authentication-administrator)
- [Global administratör](permissions-reference.md#global-administrator--company-administrator)

## <a name="search"></a>Sök

Du kan söka efter Australien och användare i din organisation med hjälp av Sök fältet i min personal. Du kan söka i alla Australien och användare i din organisation, men du kan bara göra ändringar i användare som är i en AU som du har fått administratörs behörighet för.

Du kan också söka efter en användare i AU. Det gör du genom att använda Sök fältet överst i användar listan.

## <a name="audit-logs"></a>Granskningsloggar

Du kan visa gransknings loggar för åtgärder som vidtagits i min personal på Azure Active Directory-portalen. Om en Gransknings logg har genererats av en åtgärd som vidtagits i min personal visas detta under ytterligare information i gransknings händelsen.

## <a name="next-steps"></a>Nästa steg

Dokumentation om min [personal användare](../user-help/my-staff-team-manager.md) 
 [Dokumentation om administrativa enheter](administrative-units.md)