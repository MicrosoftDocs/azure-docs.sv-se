---
title: Standardanvändarbehörigheter – Azure Active Directory | Microsoft Docs
description: Läs mer om de olika användarbehörigheter som är tillgängliga i Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18, contperf-fy21q1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 348f1b4e6182739b3afbc96597853a5b887877c1
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748780"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Vilka är standardanvändarbehörigheterna i Azure Active Directory?
Alla användare beviljas en uppsättning standardbehörigheter i Azure Active Directory (AD Azure). En användares åtkomst består av typen av användare, deras [rolltilldelningar](active-directory-users-assign-role-azure-portal.md)och deras ägarskap för enskilda objekt. Den här artikeln beskriver dessa standardbehörigheter och innehåller en jämförelse av standardinställningar för medlem och gäst. Standardanvändarbehörigheterna kan bara ändras i användarinställningarna i Azure AD.

## <a name="member-and-guest-users"></a>Medlems- och gästanvändare
Uppsättningen standardbehörigheter som tas emot beror på om användaren är en intern medlem i klientorganisationen (medlemsanvändare) eller om användaren tas över från en annan katalog som B2B-samarbetsgäst (gästanvändare). Mer information om hur du lägger till gästanvändare finns i Vad är [Azure AD B2B-samarbete?](../external-identities/what-is-b2b.md)
* Medlemsanvändare kan registrera program, hantera sina egna profilbilder och mobila telefonnummer, ändra sina egna lösenord och bjuda in B2B gäster. Dessutom kan användare läsa all kataloginformation (med några få undantag). 
* Gästanvändare har begränsade katalogbehörigheter. De kan hantera sin egen profil, ändra sitt eget lösenord och hämta viss information om andra användare, grupper och appar, men de kan inte läsa all kataloginformation. Gästanvändare kan till exempel inte räkna upp listan över alla användare, grupper och andra katalogobjekt. Gäster kan läggas till i administratörsroller som beviljar dem fullständig läs- och skrivbehörighet i rollen. Gäster kan också bjuda in andra gäster.

## <a name="compare-member-and-guest-default-permissions"></a>Jämför standardbehörigheter för medlemmar och gästanvändare

**Område** | **Behörigheter för medlemsanvändare** | **Standardbehörigheter för gästanvändare** | **Begränsade gästanvändarbehörigheter (förhandsversion)**
------------ | --------- | ---------- | ----------
Användare och kontakter | <ul><li>Räkna upp lista över alla användare och kontakter<li>Läsa alla offentliga egenskaper om användare och kontakter</li><li>Bjuda in gäster<li>Ändra eget lösenord<li>Hantera eget mobilnummer<li>Hantera eget foto<li>Ogiltigförklara egna uppdateringstokens</li></ul> | <ul><li>Läsa egna egenskaper<li>Läs visningsnamn, e-post, inloggningsnamn, foto, användarens huvudnamn och egenskaper för användartyp för andra användare och kontakter<li>Ändra eget lösenord<li>Sök efter en annan användare med ObjectId (om tillåts)<li>Läsa chef och direkt rapportinformation för andra användare</li></ul> | <ul><li>Läsa egna egenskaper<li>Ändra eget lösenord</li><li>Hantera eget mobilnummer</li></ul>
Grupper | <ul><li>Skapa säkerhetsgrupper<li>Skapa Microsoft 365 grupper<li>Räkna upp lista över alla grupper<li>Läsa alla icke-dolda egenskaper i grupper<li>Läsa icke-dolda gruppmeddelanden<li>Läsa dolda Microsoft 365 gruppmedlemskap för ansluten grupp<li>Hantera egenskaper, ägarskap och medlemskap i grupper som användaren äger<li>Lägga till gäster i egna grupper<li>Hantera inställningar för dynamiskt medlemskap<li>Ta bort egna grupper<li>Återställa ägda Microsoft 365 grupper</li></ul> | <ul><li>Läs egenskaper för icke-dolda grupper, inklusive medlemskap och ägarskap (även icke-ansluten grupper)<li>Läsa dolda Microsoft 365 gruppmedlemskap för grupper<li>Sök efter grupper efter visningsnamn eller ObjectId (om tillåts)</li></ul> | <ul><li>Läsa objekt-ID för sammanfogade grupper<li>Läsa medlemskap och ägarskap för grupper i vissa Microsoft 365 appar (om tillåts)</li></ul>
Program | <ul><li>Registrera (Skapa) ett nytt program<li>Räkna upp en lista över alla program<li>Skrivskyddade egenskaper för registrerade program och företagsprogram<li>Hantera egenskaper för program, tilldelningar och autentiseringsuppgifter för egna program<li>Skapa eller ta bort programlösenord för användare<li>Ta bort egna program<li>Återställ egna program</li></ul> | <ul><li>Skrivskyddade egenskaper för registrerade program och företagsprogram</li></ul> | <ul><li>Skrivskyddade egenskaper för registrerade program och företagsprogram
Enheter</li></ul> | <ul><li>Räkna upp en lista över alla enheter<li>Läs alla enhetsegenskaper<li>Läs alla egenskaper för egna enheter</li></ul> | Inga behörigheter | Inga behörigheter
Katalog | <ul><li>Läs all företagsinformation<li>Läsa alla domäner<li>Läsa alla partnerkontrakt</li></ul> | <ul><li>Läsa företagets visningsnamn<li>Läsa alla domäner</li></ul> | <ul><li>Läsa företagets visningsnamn<li>Läsa alla domäner</li></ul>
Roller och omfattningar | <ul><li>Läsa alla administrativa roller och medlemskap<li>Läsa alla egenskaper och medlemskap i administrativa enheter</li></ul> | Inga behörigheter | Inga behörigheter
Prenumerationer | <ul><li>Läsa alla prenumerationer<li>Aktivera tjänsteplanmedlem</li></ul> | Inga behörigheter | Inga behörigheter
Principer | <ul><li>Läs alla principegenskaper<li>Hantera alla egenskaper för egna principer</li></ul> | Inga behörigheter | Inga behörigheter

## <a name="restrict-member-users-default-permissions"></a>Begränsa standardbehörigheter för medlemsanvändare 

Standardbehörigheter för medlemsanvändare kan begränsas på följande sätt:

Behörighet | Förklaring av inställning
---------- | ------------
Användare kan registrera program | Om det här alternativet är Nej kan användarna inte skapa programregistreringar. Möjligheten kan sedan beviljas tillbaka till specifika personer genom att lägga till dem i rollen Programutvecklare.
Tillåt användare att ansluta arbets- eller skolkonto med LinkedIn | Om det här alternativet är Nej kan användarna inte ansluta sina arbets- eller skolkonto till sina LinkedIn-konton. Mer information finns i [Datadelning och medgivande för LinkedIn-kontoanslutningar.](../enterprise-users/linkedin-user-consent.md)
Möjlighet att skapa säkerhetsgrupper | När den här inställningen är inställd på Nej kan användare inte skapa säkerhetsgrupper. Globala administratörer och användaradministratörer kan fortfarande skapa säkerhetsgrupper. Se [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../enterprise-users/groups-settings-cmdlets.md) för att lära dig hur du ska gå tillväga.
Möjlighet att skapa Microsoft 365 grupper | Om det här alternativet är Nej kan användarna inte skapa Microsoft 365 grupper. Om du ställer in det här alternativet på Vissa kan en viss uppsättning användare skapa Microsoft 365 grupper. Globala administratörer och användaradministratörer kommer fortfarande att kunna skapa Microsoft 365 grupper. Se [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../enterprise-users/groups-settings-cmdlets.md) för att lära dig hur du ska gå tillväga.
Begränsa åtkomst till Azure AD-administrationsportalen | Om du ställer in det här alternativet på Nej kan icke-administratörer använda Azure AD-administrationsportalen för att läsa och hantera Azure AD-resurser. Ja begränsar alla icke-administratörer från att komma åt Azure AD-data i administrationsportalen.<p>**Obs!** Den här inställningen begränsar inte åtkomsten till Azure AD-data med hjälp av PowerShell eller andra klienter, till exempel Visual Studio.När inställningen är inställd på Ja ger du en specifik icke-administratörsanvändare möjlighet att använda Azure AD-administrationsportalen tilldela administrativa roller, till exempel rollen Katalogläsare.<p>Med den här rollen kan du läsa grundläggande kataloginformation, vilket medlemsanvändare har som standard (gäster och tjänstens huvudnamn har det inte).
Möjlighet att läsa andra användare | Den här inställningen är endast tillgänglig i PowerShell. Om den här flaggan $false förhindras alla icke-administratörer från att läsa användarinformation från katalogen. Den här flaggan förhindrar inte läsning av användarinformation i andra Microsoft-tjänster till exempel Exchange Online. Den här inställningen är avsedd för särskilda omständigheter och att ange den här flaggan till $false rekommenderas inte.

## <a name="restrict-guest-users-default-permissions"></a>Begränsa standardbehörigheter för gästanvändare

Standardbehörigheter för gästanvändare kan begränsas på följande sätt:

>[!NOTE]
>Inställningen för användarbegränsningar för gäster har ersatt **inställningen Gästanvändares behörigheter är begränsade.** Vägledning om hur du använder den här funktionen finns [i Begränsa gäståtkomstbehörigheter (förhandsversion) i Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).

Behörighet | Förklaring av inställning
---------- | ------------
Gästers åtkomstbegränsningar (förhandsversion) | Om du anger det här **alternativet till Gästanvändare får du samma åtkomst som medlemmar** som ger alla medlemsanvändarbehörigheter till gästanvändare som standard.<p>Att ställa in det **här alternativet på Gästanvändaråtkomst** är begränsat till egenskaper och medlemskap i sina egna katalogobjekt begränsar gäståtkomsten till endast sin egen användarprofil som standard. Åtkomst till andra användare tillåts inte längre även när du söker efter användarens huvudnamn, ObjectId eller visningsnamn. Åtkomst till gruppinformation, inklusive gruppmedlemskap, tillåts inte längre.<p>**Obs!** Den här inställningen förhindrar inte åtkomst till grupper i vissa Microsoft 365 tjänster som Microsoft Teams. Mer [information finns i Microsoft Teams-gäståtkomst.](/MicrosoftTeams/guest-access)<p>Gästanvändare kan fortfarande läggas till i administratörsroller oavsett dessa behörighetsinställningar.
Gäster kan bjuda in | Om du ställer in det här alternativet på Ja kan gäster bjuda in andra gäster. Mer [information finns i Delegera inbjudningar för B2B-samarbete.](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings)
Medlemmar kan bjuda in | Om du ställer in det här alternativet på Ja kan icke-administratörsmedlemmar i din katalog bjuda in gäster. Mer [information finns i Delegera inbjudningar för B2B-samarbete.](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings)
Administratörer och användare i gästinbjudarrollen kan bjuda in | Om du ställer in det här alternativet på Ja kan administratörer och användare i rollen Gästbjudare bjuda in gäster. Om inställningen är inställd på Ja kan användare Gästinbjudare rollen fortfarande bjuda in gäster, oavsett inställningen Medlemmar kan bjuda in. Mer [information finns i Delegera inbjudningar för B2B-samarbete.](../external-identities/delegate-invitations.md#assign-the-guest-inviter-role-to-a-user)

## <a name="object-ownership"></a>Objektägarskap

### <a name="application-registration-owner-permissions"></a>Ägarbehörigheter för programregistrering
När en användare registrerar ett program, läggs de automatiskt till som ägare till programmet. De kan hantera metadata för programmet som ägare, till exempel namn och behörigheter för appen. De kan också hantera klient-specifik konfiguration av program, till exempel SSO-konfiguration och användartilldelningar. En ägare kan också lägga till eller ta bort andra ägare. Till skillnad från globala administratörer kan ägare bara hantera program som de äger.

### <a name="enterprise-application-owner-permissions"></a>Behörigheter för företagsprogramägare
När en användare lägger till ett nytt företagsprogram läggs de automatiskt till som ägare. Som ägare kan de hantera programmets klientspecifika konfiguration, till exempel SSO-konfiguration, etablering och användartilldelningar. En ägare kan också lägga till eller ta bort andra ägare. Till skillnad från globala administratörer kan ägare bara hantera de program som de äger.

### <a name="group-owner-permissions"></a>Gruppägares behörigheter
När en användare skapar en grupp, läggs de automatiskt som en ägare till gruppen. Som ägare kan de hantera gruppens egenskaper, till exempel namn, samt hantera gruppmedlemskap. En ägare kan också lägga till eller ta bort andra ägare. Till skillnad från globala administratörer och användaradministratörer kan ägare bara hantera grupper som de äger. Om du vill tilldela en gruppägare, se [Hantera ägare för en grupp](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Ägarskapsbehörigheter
I följande tabeller beskrivs de specifika behörigheterna i Azure Active Directory medlemsanvändare har över ägda objekt. Användaren har bara dessa behörigheter för objekt som de äger.

#### <a name="owned-application-registrations"></a>Ägda programregistreringar
Användare kan utföra följande åtgärder på ägda programregistreringar.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/applications/audience/update | Uppdatera egenskapen applications.audience i Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Uppdatera egenskapen applications.authentication i Azure Active Directory. |
| microsoft.directory/applications/basic/update | Uppdatera grundläggande egenskaper för program i Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Uppdatera egenskapen applications.credentials i Azure Active Directory. |
| microsoft.directory/applications/delete | Ta bort program i Azure Active Directory. |
| microsoft.directory/applications/owners/update | Uppdatera egenskapen applications.owners i Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Uppdatera egenskapen applications.permissions i Azure Active Directory. |
| microsoft.directory/applications/policies/update | Uppdatera egenskapen applications.policies i Azure Active Directory. |
| microsoft.directory/applications/restore | Återställa program i Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Ägda företagsprogram
Användare kan utföra följande åtgärder på ägda företagsprogram. Ett företagsprogram består av tjänstens huvudnamn, en eller flera programprinciper och ibland ett programobjekt i samma klientorganisation som tjänstens huvudnamn.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Läsa alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| microsoft.directory/policies/basic/update | Uppdatera grundläggande egenskaper för principer i Azure Active Directory. |
| microsoft.directory/policies/delete | Ta bort principer i Azure Active Directory. |
| microsoft.directory/policies/owners/update | Uppdatera egenskapen policies.owners i Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera egenskapen servicePrincipals.appRoleAssignedTo i Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Uppdatera egenskapen users.appRoleAssignments i Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Uppdatera egenskapen servicePrincipals.audience i Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Uppdatera egenskapen servicePrincipals.authentication i Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för servicePrincipals i Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Uppdatera egenskapen servicePrincipals.credentials i Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Ta bort servicePrincipals i Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Uppdatera egenskapen servicePrincipals.owners i Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Uppdatera egenskapen servicePrincipals.permissions i Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Uppdatera egenskapen servicePrincipals.policies i Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Läs alla egenskaper (inklusive privilegierade egenskaper) på signInReports i Azure Active Directory. |

#### <a name="owned-devices"></a>Ägda enheter
Användare kan utföra följande åtgärder på ägda enheter.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Egenskapen Read devices.bitLockerRecoveryKeys i Azure Active Directory. |
| microsoft.directory/devices/disable | Inaktivera enheter i Azure Active Directory. |

#### <a name="owned-groups"></a>Ägda grupper
Användare kan utföra följande åtgärder på ägda grupper.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Uppdatera egenskapen groups.appRoleAssignments i Azure Active Directory. |
| microsoft.directory/groups/basic/update | Uppdatera grundläggande egenskaper för grupper i Azure Active Directory. |
| microsoft.directory/groups/delete | Ta bort grupper i Azure Active Directory. |
| microsoft.directory/groups/members/update | Uppdatera egenskapen groups.members i Azure Active Directory. |
| microsoft.directory/groups/owners/update | Uppdatera egenskapen groups.owners i Azure Active Directory. |
| microsoft.directory/groups/restore | Återställa grupper i Azure Active Directory. |
| microsoft.directory/groups/settings/update | Uppdatera egenskapen groups.settings i Azure Active Directory. |

## <a name="next-steps"></a>Nästa steg

* Mer information om inställningen begränsningar för gästanvändaråtkomst finns i [Begränsa behörigheter för gäståtkomst (förhandsversion) i Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).
* Mer information om hur du tilldelar Azure AD-administratörsroller finns i [Tilldela administratörsroller till en användare i Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Mer information om hur resursåtkomsten hanteras i Microsoft Azure finns i [Förstå resursåtkomst i Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Mer information om hur Azure Active Directory relaterar till din Azure-prenumeration finns i [Hur Azure-prenumerationer är associerade med Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Hantera användare](add-users-azure-active-directory.md)
