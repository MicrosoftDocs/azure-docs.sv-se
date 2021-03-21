---
title: Använd min personal för att delegera användar hantering – Azure AD | Microsoft Docs
description: Delegera användar hantering med min personal och administrativa enheter
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.topic: how-to
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 03/11/2021
ms.author: rolyon
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 1a380c8a3d766c3c11d8cba1148383d924f65a1b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103225004"
---
# <a name="manage-your-users-with-my-staff"></a>Hantera dina användare med min personal

Med min personal kan du delegera behörigheter till en bild av auktoriteten, till exempel en butiks chef eller en grupp ledare, för att säkerställa att deras personal medlemmar kan komma åt sina Azure AD-konton. I stället för att lita på en central helpdesk kan organisationer delegera vanliga uppgifter, till exempel att återställa lösen ord eller ändra telefonnumret till en lokal team hanterare. Med min personal kan en användare som inte har åtkomst till sitt konto få åtkomst på bara några klick, utan supportavdelningen eller IT-personal som krävs.

Innan du konfigurerar min personal för din organisation rekommenderar vi att du läser igenom den här dokumentationen och [användar dokumentationen](../user-help/my-staff-team-manager.md) för att säkerställa att du förstår hur den fungerar och hur den påverkar användarna. Du kan använda användar dokumentationen för att träna och förbereda dina användare för den nya upplevelsen och hjälpa till att se till att distributionen lyckas.

## <a name="how-my-staff-works"></a>Så här fungerar min personal

Min personal är baserad på administrativa enheter, som är en behållare med resurser som kan användas för att begränsa omfattningen för en roll tilldelnings administrativ kontroll. Mer information finns i [hantering av administrativa enheter i Azure Active Directory](administrative-units.md). I min personal kan administrativa enheter användas för att innehålla en grupp användare i en butik eller avdelning. En team ansvarig kan sedan tilldelas en administrativ roll i en omfattning av en eller flera enheter.

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

När du har konfigurerat administrativa enheter kan du tillämpa det här omfånget på dina användare som har åtkomst till min personal. Endast användare som har tilldelats en administrativ roll har åtkomst till min personal. Utför följande steg för att aktivera min personal:

1. Logga in på Azure Portal som användar administratör.
2. Bläddra till **Azure Active Directory**  >  **användar inställningar**  >  **användar funktions** förhands  >  **granskningar hantera användar funktions förhands gransknings inställningar**.
3. Under **Administratörer kan komma åt min personal** kan du välja att aktivera för alla användare, valda användare eller ingen användar åtkomst.

> [!Note]
> Endast användare som har tilldelats en administratörs roll har åtkomst till min personal. Om du aktiverar min personal för en användare som inte har tilldelats någon administratörs roll kan de inte komma åt min personal.

## <a name="conditional-access"></a>Villkorlig åtkomst

Du kan skydda min personal Portal med hjälp av principen för villkorlig åtkomst för Azure AD. Använd den för aktiviteter som kräver multifaktorautentisering innan du får åtkomst till min personal.

Vi rekommenderar starkt att du skyddar min personal med [villkorliga åtkomst principer för Azure AD](../conditional-access/index.yml). Om du vill tillämpa en princip för villkorlig åtkomst för min personal måste du först gå till min personal webbplats en gång om några minuter för att automatiskt etablera tjänstens huvud namn i din klient organisation för användning av villkorlig åtkomst.

Du ser tjänstens huvud namn när du skapar en princip för villkorlig åtkomst som gäller för moln programmet min personal.

![Skapa en princip för villkorlig åtkomst för min personal-appen](./media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>Använda min personal

När en användare går till min personal visas namnen på de [administrativa enheter](administrative-units.md) som de har administratörs behörighet för. I [användar dokumentationen för min personal](../user-help/my-staff-team-manager.md)använder vi termen "plats" för att referera till administrativa enheter. Om en administratörs behörighet inte har någon definition av en administrativ enhet gäller behörigheterna i hela organisationen. När min personal har Aktiver ATS kan de användare som är aktiverade och har tilldelats en administrativ roll komma åt den via [https://mystaff.microsoft.com](https://mystaff.microsoft.com) . De kan välja en administrativ enhet för att visa användarna i enheten och välja en användare för att öppna profilen.

## <a name="reset-a-users-password"></a>Återställa ett användarlösenord

Innan du kan vila lösen ord för lokala användare måste du uppfylla följande krav. Detaljerade anvisningar finns i själv studie kursen [Aktivera självbetjäning för återställning av lösen ord](../authentication/tutorial-enable-sspr-writeback.md) .

* Konfigurera behörigheter för tillbakaskrivning av lösen ord
* Aktivera tillbakaskrivning av lösen ord i Azure AD Connect
* Aktivera tillbakaskrivning av lösen ord i Azure AD självbetjäning för återställning av lösen ord (SSPR)

Följande roller har behörighet att återställa en användares lösen ord:

* [Administratör för autentisering](permissions-reference.md#authentication-administrator)
* [Administratör för privilegie rad autentisering](permissions-reference.md#privileged-authentication-administrator)
* [Global administratör](permissions-reference.md#global-administrator)
* [Support administratör](permissions-reference.md#helpdesk-administrator)
* [Användaradministratör](permissions-reference.md#user-administrator)
* [Lösenordsadministratör](permissions-reference.md#password-administrator)

Öppna en användar profil från **min personal**. Välj **Återställ lösen ord**.

* Om användaren endast är molnbaserad kan du se ett tillfälligt lösen ord som du kan ge användaren.
* Om användaren har synkroniserats från lokala Active Directory kan du ange ett lösen ord som uppfyller dina lokala AD-principer. Du kan sedan ange lösen ordet för användaren.

    ![Förlopps indikator för lösen ords återställning och lyckad avisering](./media/my-staff-configure/reset-password.png)

Användaren måste ändra sitt lösen ord nästa gången de loggar in.

## <a name="manage-a-phone-number"></a>Hantera ett telefonnummer

Öppna en användar profil från **min personal**.

* Lägg till ett telefonnummer för användaren genom att välja **Lägg till telefonnummer**
* Välj **redigera telefonnummer** för att ändra telefonnumret
* Ta bort telefonnumret för användaren genom att välja **ta bort telefonnummer**

Beroende på dina inställningar kan användaren sedan använda det telefonnummer som du ställt in för att logga in med SMS, utföra Multi-Factor Authentication och utföra lösen ords återställning via självbetjäning.

Om du vill hantera en användares telefonnummer måste du ha tilldelats någon av följande roller:

* [Administratör för autentisering](permissions-reference.md#authentication-administrator)
* [Administratör för privilegie rad autentisering](permissions-reference.md#privileged-authentication-administrator)
* [Global administratör](permissions-reference.md#global-administrator)

## <a name="search"></a>Sök

Du kan söka efter administrativa enheter och användare i din organisation med hjälp av Sök fältet i min personal. Du kan söka i alla administrativa enheter och användare i din organisation, men du kan bara göra ändringar i användare som är i en administrativ enhet som du har fått administratörs behörighet för.

Du kan också söka efter en användare i en administrativ enhet. Det gör du genom att använda Sök fältet överst i användar listan.

## <a name="audit-logs"></a>Granskningsloggar

Du kan visa gransknings loggar för åtgärder som vidtagits i min personal på Azure Active Directory-portalen. Om en Gransknings logg har genererats av en åtgärd som vidtagits i min personal visas detta under ytterligare information i gransknings händelsen.

## <a name="next-steps"></a>Nästa steg

Dokumentation om min [personal användare](../user-help/my-staff-team-manager.md) 
 [Dokumentation om administrativa enheter](administrative-units.md)
