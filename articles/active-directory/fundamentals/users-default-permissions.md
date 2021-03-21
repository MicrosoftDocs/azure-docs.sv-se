---
title: Standard användar behörigheter – Azure Active Directory | Microsoft Docs
description: Lär dig mer om de olika användar behörigheter som är tillgängliga i Azure Active Directory.
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
ms.openlocfilehash: eb01a3e0fc5bc11a4d3de62b16aafb7dd308e34a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724279"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Vilka är standard användar behörigheterna i Azure Active Directory?
Alla användare beviljas en uppsättning standardbehörigheter i Azure Active Directory (AD Azure). En användares åtkomst består av typen av användare, deras [roll tilldelningar](active-directory-users-assign-role-azure-portal.md)och deras ägarskap för enskilda objekt. Den här artikeln beskriver dessa standardbehörigheter och innehåller en jämförelse av standardinställningar för medlem och gäst. Standard användar behörigheter kan bara ändras i användar inställningar i Azure AD.

## <a name="member-and-guest-users"></a>Medlems- och gästanvändare
Uppsättningen standard behörigheter som tas emot beror på om användaren är en ursprunglig medlem i klienten (medlems användare) eller om användaren förs över från en annan katalog som ett B2B-samarbets gäst (gäst användare). Se [Vad är Azure AD B2B-samarbete?](../external-identities/what-is-b2b.md) mer information om att lägga till gäst användare.
* Medlemsanvändare kan registrera program, hantera sina egna profilbilder och mobila telefonnummer, ändra sina egna lösenord och bjuda in B2B gäster. Dessutom kan användare läsa all kataloginformation (med några få undantag). 
* Gäst användare har begränsad katalog behörighet. De kan hantera sin egen profil, ändra sina egna lösen ord och hämta information om andra användare, grupper och appar, men de kan inte läsa all katalog information. Gäst användare kan till exempel inte räkna upp listan över alla användare, grupper och andra katalog objekt. Gäster kan läggas till i administratörsroller som beviljar dem fullständig läs- och skrivbehörighet i rollen. Gäster kan också bjuda in andra gäster.

## <a name="compare-member-and-guest-default-permissions"></a>Jämför standardbehörigheter för medlemmar och gästanvändare

**Område** | **Behörigheter för medlemsanvändare** | **Standard behörigheter för gäst användare** | **Begränsad gäst användar behörighet (förhands granskning)**
------------ | --------- | ---------- | ----------
Användare och kontakter | <ul><li>Räkna upp lista över alla användare och kontakter<li>Läsa alla offentliga egenskaper om användare och kontakter</li><li>Bjuda in gäster<li>Ändra eget lösenord<li>Hantera eget mobilnummer<li>Hantera eget foto<li>Ogiltigförklara egna uppdateringstokens</li></ul> | <ul><li>Läsa egna egenskaper<li>Läsa visnings namn, e-post, inloggnings namn, Foto, User Principal Name och egenskaper för användar typ för andra användare och kontakter<li>Ändra eget lösenord<li>Sök efter en annan användare med ObjectId (om det är tillåtet)<li>Read Manager och information om den direkta rapporten för andra användare</li></ul> | <ul><li>Läsa egna egenskaper<li>Ändra eget lösenord</li></ul>
Grupper | <ul><li>Skapa säkerhetsgrupper<li>Skapa Microsoft 365 grupper<li>Räkna upp listan över alla grupper<li>Läsa alla icke-dolda egenskaper i grupper<li>Läsa icke-dolda gruppmeddelanden<li>Läs dolda Microsoft 365 grupp medlemskap för anslutet grupper<li>Hantera egenskaper, ägarskap och medlemskap i grupper som användaren äger<li>Lägga till gäster i egna grupper<li>Hantera inställningar för dynamiskt medlemskap<li>Ta bort egna grupper<li>Återställa ägda Microsoft 365 grupper</li></ul> | <ul><li>Läsa egenskaper för icke-dolda grupper, inklusive medlemskap och ägarskap (även icke-anslutna grupper)<li>Läs dolda Microsoft 365 grupp medlemskap för anslutna grupper<li>Sök efter grupper efter visnings namn eller ObjectId (om det är tillåtet)</li></ul> | <ul><li>Läsa objekt-ID för anslutna grupper<li>Läs medlemskap och ägarskap för anslutna grupper i vissa Microsoft 365 appar (om det är tillåtet)</li></ul>
Program | <ul><li>Registrera (Skapa) ett nytt program<li>Räkna upp listan över alla program<li>Skrivskyddade egenskaper för registrerade program och företagsprogram<li>Hantera egenskaper för program, tilldelningar och autentiseringsuppgifter för egna program<li>Skapa eller ta bort programlösenord för användare<li>Ta bort egna program<li>Återställ egna program</li></ul> | <ul><li>Skrivskyddade egenskaper för registrerade program och företagsprogram</li></ul> | <ul><li>Skrivskyddade egenskaper för registrerade program och företagsprogram
Enheter</li></ul> | <ul><li>Räkna upp listan över alla enheter<li>Läs alla enhetsegenskaper<li>Läs alla egenskaper för egna enheter</li></ul> | Inga behörigheter | Inga behörigheter
Katalog | <ul><li>Läs all företagsinformation<li>Läsa alla domäner<li>Läsa alla partnerkontrakt</li></ul> | <ul><li>Läs företagets visnings namn<li>Läsa alla domäner</li></ul> | <ul><li>Läs företagets visnings namn<li>Läsa alla domäner</li></ul>
Roller och omfattningar | <ul><li>Läsa alla administrativa roller och medlemskap<li>Läsa alla egenskaper och medlemskap i administrativa enheter</li></ul> | Inga behörigheter | Inga behörigheter
Prenumerationer | <ul><li>Läsa alla prenumerationer<li>Aktivera tjänsteplanmedlem</li></ul> | Inga behörigheter | Inga behörigheter
Principer | <ul><li>Läs alla principegenskaper<li>Hantera alla egenskaper för egna principer</li></ul> | Inga behörigheter | Inga behörigheter

## <a name="restrict-member-users-default-permissions"></a>Begränsa medlems användares standard behörigheter 

Standard behörigheter för medlems användare kan begränsas på följande sätt:

Behörighet | Förklaring av inställning
---------- | ------------
Användare kan registrera program | Om du ställer in det här alternativet på Nej förhindras användare från att skapa program registreringar. Möjligheten kan sedan ges tillbaka till specifika individer genom att lägga till dem i rollen programutvecklare.
Tillåt att användare ansluter arbets-eller skol konton med LinkedIn | Om du ställer in det här alternativet på Nej förhindras användare från att ansluta sina arbets-eller skol konton till deras LinkedIn-konto. Mer information finns i [LinkedIn konto anslutningar data delning och medgivande](../enterprise-users/linkedin-user-consent.md).
Möjlighet att skapa säkerhetsgrupper | När den här inställningen är inställd på Nej kan användare inte skapa säkerhetsgrupper. Globala administratörer och användar administratörer kan fortfarande skapa säkerhets grupper. Se [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../enterprise-users/groups-settings-cmdlets.md) för att lära dig hur du ska gå tillväga.
Möjlighet att skapa Microsoft 365 grupper | Om du ställer in det här alternativet på Nej förhindras användare från att skapa Microsoft 365 grupper. Om du ställer in det här alternativet på vissa kan en Välj uppsättning användare skapa Microsoft 365 grupper. Globala administratörer och användar administratörer kommer fortfarande att kunna skapa Microsoft 365 grupper. Se [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../enterprise-users/groups-settings-cmdlets.md) för att lära dig hur du ska gå tillväga.
Begränsa åtkomst till Azure AD-administrationsportalen | Om du ställer in det här alternativet på Nej kan icke-administratörer använda Azure AD-administrationskonsolen för att läsa och hantera Azure AD-resurser. Ja, begränsar alla icke-administratörer från att komma åt Azure AD-data i administrations portalen.<p>**Obs!** den här inställningen begränsar inte åtkomsten till Azure AD-data med hjälp av PowerShell eller andra klienter, till exempel Visual Studio. När värdet är ja, för att ge en särskild icke-administratör möjlighet att använda Azure AD-administrationskonsolen tilldelar du en administrativ roll, till exempel rollen katalog läsare.<p>Med den här rollen kan du läsa grundläggande katalog information som medlems användare har som standard (gäster och tjänstens huvud namn).
Möjlighet att läsa andra användare | Den här inställningen är endast tillgänglig i PowerShell. Om du ställer in den här flaggan på $false hindras alla icke-administratörer från att läsa användar information från katalogen. Den här flaggan förhindrar inte läsning av användar information i andra Microsoft-tjänster som Exchange Online. Den här inställningen är avsedd för särskilda omständigheter och vi rekommenderar att du inte anger den här flaggan till $false.

## <a name="restrict-guest-users-default-permissions"></a>Begränsa gäst användares standard behörigheter

Standard behörigheter för gäst användare kan begränsas på följande sätt:

>[!NOTE]
>Inställningen för åtkomst begränsningar för gäster som ersätter **gäst användarnas behörigheter är begränsad** . Vägledning om hur du använder den här funktionen finns i [begränsa åtkomst behörighet för gäst (för hands version) i Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).

Behörighet | Förklaring av inställning
---------- | ------------
Användar åtkomst begränsningar för gäster (för hands version) | Om du ställer in det här alternativet på **gäst användare har samma åtkomst som medlemmar** beviljar alla medlems användare behörigheter till gäst användare som standard.<p>Att ställa in det här alternativet på **gäst användares åtkomst är begränsad till egenskaper och medlemskap i sina egna katalog objekt** begränsar gäst åtkomsten till enbart sin egen användar profil som standard. Åtkomst till andra användare tillåts inte längre även när du söker efter användarens huvud namn, ObjectId eller visnings namn. Åtkomst till grupp information, inklusive grupp medlemskap, tillåts inte heller längre.<p>**Obs!** den här inställningen förhindrar inte åtkomst till anslutna grupper i vissa Microsoft 365-tjänster som Microsoft Teams. Läs mer i [Microsoft Teams gäst åtkomst](/MicrosoftTeams/guest-access) .<p>Gäst användare kan fortfarande läggas till i administratörs roller oavsett de här behörighets inställningarna.
Gäster kan bjuda in | Om du ställer in det här alternativet på Ja kan gäster bjuda in andra gäster. Mer information finns i [delegera inbjudningar för B2B-samarbete](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) .
Medlemmar kan bjuda in | Om du ställer in det här alternativet på Ja tillåts icke-administratörers medlemmar i katalogen att bjuda in gäster. Mer information finns i [delegera inbjudningar för B2B-samarbete](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) .
Administratörer och användare i gästinbjudarrollen kan bjuda in | Om du ställer in det här alternativet på Ja kan administratörer och användare i rollen "gäst inbjudare" bjuda in gäster. När det är inställt på Ja kommer användare i rollen gäst deltagare fortfarande att kunna bjuda in gäster, oavsett medlemmar kan bjuda in. Mer information finns i [delegera inbjudningar för B2B-samarbete](../external-identities/delegate-invitations.md#assign-the-guest-inviter-role-to-a-user) .

## <a name="object-ownership"></a>Objektägarskap

### <a name="application-registration-owner-permissions"></a>Ägarbehörigheter för programregistrering
När en användare registrerar ett program, läggs de automatiskt till som ägare till programmet. De kan hantera metadata för programmet som ägare, till exempel namn och behörigheter för appen. De kan också hantera klient-specifik konfiguration av program, till exempel SSO-konfiguration och användartilldelningar. En ägare kan också lägga till eller ta bort andra ägare. Till skillnad från globala administratörer kan ägare bara hantera program som de äger.

### <a name="enterprise-application-owner-permissions"></a>Behörigheter för företags program ägare
När en användare lägger till ett nytt företags program läggs de automatiskt till som en ägare. Som ägare kan de hantera klientens specifika konfiguration av programmet, till exempel SSO-konfigurationen, etablering och användar tilldelningar. En ägare kan också lägga till eller ta bort andra ägare. Till skillnad från globala administratörer kan ägarna bara hantera de program som de äger.

### <a name="group-owner-permissions"></a>Gruppägares behörigheter
När en användare skapar en grupp, läggs de automatiskt som en ägare till gruppen. Som ägare kan de hantera egenskaper för gruppen, till exempel namnet, samt hantera grupp medlemskap. En ägare kan också lägga till eller ta bort andra ägare. Till skillnad från globala administratörer och användar administratörer kan ägarna bara hantera grupper som de äger. Om du vill tilldela en gruppägare, se [Hantera ägare för en grupp](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Ägarskaps behörigheter
I följande tabeller beskrivs de olika behörigheterna i Azure Active Directory medlems användare har över ägda objekt. Användaren har bara dessa behörigheter för objekt som de äger.

#### <a name="owned-application-registrations"></a>Ägda program registreringar
Användare kan utföra följande åtgärder för ägda program registreringar.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. Directory/program/mål grupp/uppdatering | Uppdatera program. Audience-egenskapen i Azure Active Directory. |
| Microsoft. Directory/program/autentisering/uppdatering | Uppdatera program. Authentication-egenskapen i Azure Active Directory. |
| Microsoft. Directory/Applications/Basic/Update | Uppdatera grundläggande egenskaper för program i Azure Active Directory. |
| Microsoft. Directory/program/autentiseringsuppgifter/uppdatera | Uppdatera program. credentials-egenskapen i Azure Active Directory. |
| Microsoft. Directory/Applications/Delete | Ta bort program i Azure Active Directory. |
| Microsoft. Directory/program/ägare/uppdatering | Uppdatera program. Owners-egenskapen i Azure Active Directory. |
| Microsoft. Directory/program/behörigheter/uppdatera | Uppdatera program. Permissions-egenskapen i Azure Active Directory. |
| Microsoft. Directory/Applications/policies/Update | Uppdatera Applications. policys-egenskapen i Azure Active Directory. |
| Microsoft. Directory/program/Återställ | Återställa program i Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Ägda företags program
Användare kan utföra följande åtgärder på ägda företags program. Ett företags program består av tjänstens huvud namn, en eller flera program principer, och ibland ett program objekt i samma klient organisation som tjänstens huvud namn.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/Read | Läs alla egenskaper (inklusive privilegierade egenskaper) på auditLogs i Azure Active Directory. |
| Microsoft. Directory/policies/Basic/Update | Uppdatera grundläggande egenskaper för principer i Azure Active Directory. |
| Microsoft. Directory/principer/Delete | Ta bort principer i Azure Active Directory. |
| Microsoft. Directory/principer/ägare/uppdatering | Uppdatera policys. Owners-egenskapen i Azure Active Directory. |
| Microsoft. Directory/Service princip ALS/appRoleAssignedTo/Update | Uppdatera service princip ALS. appRoleAssignedTo-egenskapen i Azure Active Directory. |
| Microsoft. Directory/Service princip ALS/appRoleAssignments/Update | Uppdatera users. appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft. Directory/Service princip ALS/målgrupp/uppdatera | Uppdatera service princip ALS. Audience-egenskapen i Azure Active Directory. |
| Microsoft. Directory/Service princip ALS/Authentication/Update | Uppdatera service princip ALS. Authentication-egenskapen i Azure Active Directory. |
| Microsoft. Directory/Service princip ALS/Basic/Update | Uppdatera grundläggande egenskaper för service princip ALS i Azure Active Directory. |
| Microsoft. Directory/Service princip ALS/credentials/Update | Uppdatera service princip ALS. credentials-egenskapen i Azure Active Directory. |
| Microsoft. Directory/Service princip ALS/Delete | Ta bort service princip ALS i Azure Active Directory. |
| Microsoft. Directory/Service princip ALS/Owners/Update | Uppdatera service princip ALS. Owners-egenskapen i Azure Active Directory. |
| Microsoft. Directory/Service princip ALS/Permissions/Update | Uppdatera service princip ALS. Permissions-egenskapen i Azure Active Directory. |
| Microsoft. Directory/Service princip ALS/policies/Update | Uppdatera service princip ALS. policys-egenskapen i Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/Read | Läs alla egenskaper (inklusive privilegierade egenskaper) på signInReports i Azure Active Directory. |

#### <a name="owned-devices"></a>Ägda enheter
Användare kan utföra följande åtgärder på ägda enheter.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. Directory/Devices/bitLockerRecoveryKeys/Read | Läsa Devices. bitLockerRecoveryKeys-egenskapen i Azure Active Directory. |
| Microsoft. Directory/enheter/inaktivera | Inaktivera enheter i Azure Active Directory. |

#### <a name="owned-groups"></a>Ägda grupper
Användare kan utföra följande åtgärder i ägda grupper.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft. Directory/Groups/appRoleAssignments/Update | Uppdatera Groups. appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft. Directory/grupper/Basic/Update | Uppdatera grundläggande egenskaper för grupper i Azure Active Directory. |
| Microsoft. Directory/grupper/ta bort | Ta bort grupper i Azure Active Directory. |
| Microsoft. Directory/Groups/dynamicMembershipRule/Update | Uppdatera Groups. dynamicMembershipRule-egenskapen i Azure Active Directory. |
| Microsoft. Directory/grupper/medlemmar/uppdatera | Uppdatera Groups. members-egenskapen i Azure Active Directory. |
| Microsoft. Directory/grupper/ägare/uppdatera | Uppdatera Groups. Owners-egenskapen i Azure Active Directory. |
| Microsoft. Directory/grupper/Återställ | Återställ grupper i Azure Active Directory. |
| Microsoft. Directory/grupper/inställningar/uppdatera | Uppdatera Groups. Settings-egenskapen i Azure Active Directory. |

## <a name="next-steps"></a>Nästa steg

* Om du vill veta mer om inställningen för användar åtkomst begränsningar för gäster, se [begränsa åtkomst behörighet för gäst (för hands version) i Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).
* Mer information om hur du tilldelar administratörs roller för Azure AD finns i [tilldela en användare till administratörs roller i Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Mer information om hur resursåtkomsten hanteras i Microsoft Azure finns i [Förstå resursåtkomst i Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Mer information om hur Azure Active Directory relaterar till din Azure-prenumeration finns i [Hur Azure-prenumerationer är associerade med Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Hantera användare](add-users-azure-active-directory.md)