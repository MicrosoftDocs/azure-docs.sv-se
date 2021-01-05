---
title: Identifiera det aktuella läget för externt samarbete med Azure Active Directory
description: Lär dig metoder för att identifiera det aktuella läget för ditt samarbete.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff656887081681a804285e9a96352feef15fc675
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/23/2020
ms.locfileid: "97744225"
---
# <a name="discover-the-current-state-of-external-collaboration-in-your-organization"></a>Identifiera det aktuella läget för externt samarbete i din organisation 

Innan du identifierar det aktuella läget för ditt externa samarbete bör du [fastställa din önskade säkerhets position](1-secure-access-posture.md). Du anses vara organisationens behov av centraliserad vs. delegerad kontroll och alla relevanta styrnings-, reglerings-och efterlevnads mål. 

Personer i din organisation samarbetar förmodligen redan med användare från andra organisationer. Samarbetet kan ske via funktioner i produktivitets program som Microsoft 365, via e-post eller på annat sätt dela resurser med externa användare. De olika pelarna i din styrnings plan kommer att beformas när du identifierar 
*   användare som initierar externt samarbete.
*   de externa användare och organisationer som du samarbetar med.
*   åtkomst beviljas till externa användare.


## <a name="users-initiating-external-collaboration"></a>Användare som initierar externt samarbete

Användare som initierar externt samarbete förstår de program som är mest relevanta för externt samarbete och när den här åtkomsten ska sluta. Att förstå dessa användare kan hjälpa dig att avgöra vem som ska delegeras behörighet att bjuda in externa användare, skapa åtkomst paket och fullständig åtkomst granskningar.

Om du vill hitta användare som för närvarande samarbetar granskar du [Microsoft 365 gransknings loggen för att dela och komma åt aktiviteter för begäran](https://docs.microsoft.com/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance?view=o365-worldwide#sharing-and-access-request-activities). Du kan också granska [Azure AD audit-loggen för information om vem som bjudit in B2B](../external-identities/auditing-and-reporting.md) -användare i din katalog.

## <a name="find-current-collaboration-partners"></a>Hitta aktuella samarbets partners

Externa användare kan vara [Azure AD B2B-användare](../external-identities/what-is-b2b.md) (föredra) med partner hanterade autentiseringsuppgifter eller externa användare med lokalt etablerade autentiseringsuppgifter. Dessa användare är vanligt vis (men inte alltid) markerade med en UserType av gästen. Du kan räkna upp gäst användare via [Microsoft Graph API](https://docs.microsoft.com/graph/api/user-list?view=graph-rest-1.0&tabs=http), [PowerShell](https://docs.microsoft.com/graph/api/user-list?view=graph-rest-1.0&tabs=http)eller [Azure Portal](../enterprise-users/users-bulk-download.md).

### <a name="use-email-domains-and-companyname-property"></a>Använd e-postdomäner och egenskapen företags namn

Externa organisationer kan bestämmas av domän namn för externa användares e-postadresser. Om konsument identitets leverantörer som Google stöds, kanske detta inte är möjligt. I det här fallet rekommenderar vi att du skriver attributet företags namn för att tydligt identifiera användarens externa organisation.

### <a name="use-allow-or-deny-lists"></a>Använd listorna Tillåt eller neka

Ett annat sätt att identifiera vem du för närvarande samarbetar med, eller med vilka du har blockerat samarbete, är att se om du har lagt till några organisationer i [listorna över tillåtna eller nekade](../external-identities/allow-deny-list.md).

Överväg om organisationen vill tillåta samarbete med endast vissa organisationer. Tänk också på om din organisation vill blockera samarbete med vissa organisationer. De här inställningarna kan användas för allmän B2B-inlösen eller endast för ett speciellt åtkomst paket.

![Skärm bild av listan Tillåt neka i att skapa ett nytt Access-paket.](media/secure-external-access/2-new-access-package.png)


## <a name="find-access-being-granted-to-external-users"></a>Hitta åtkomst beviljas till externa användare

När du har en inventering av externa användare och organisationer kan du fastställa åtkomsten till dessa användare med hjälp av Microsoft Graph-API: et för att fastställa Azure AD- [gruppmedlemskap](https://docs.microsoft.com/graph/api/resources/groups-overview?view=graph-rest-1.0) eller [direkt program tilldelning](https://docs.microsoft.com/graph/api/resources/approleassignment?view=graph-rest-1.0) i Azure AD.


### <a name="enumerate-application-specific-permissions"></a>Räkna upp programspecifika behörigheter

Du kanske också kan utföra programspecifik behörighets uppräkning. Du kan till exempel program mässigt generera en behörighets rapport för SharePoint Online genom att använda [det här skriptet](https://gallery.technet.microsoft.com/office/SharePoint-Online-c9ec4f64).

Undersök särskilt åtkomst till alla dina verksamhets känsliga och affärs kritiska appar så att du är helt medveten om vilken extern åtkomst som helst.

### <a name="detect-ad-hoc-sharing"></a>Identifiera ad hoc-delning
Om dina e-post-och nätverks planer gör det kan du undersöka innehåll som delas via e-post eller obehöriga SaaS-appar (program vara som en tjänst). [Microsoft 365 data förlust skydd](https://docs.microsoft.com/microsoft-365/compliance/data-loss-prevention-policies?view=o365-worldwide) hjälper dig att identifiera, förhindra och övervaka oavsiktlig delning av känslig information i din Microsoft 365-infrastruktur. [Microsoft Cloud App Security](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/cloud-app-security) kan hjälpa dig att identifiera användningen av obehöriga SaaS-appar i din miljö.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om hur du skyddar extern åtkomst till resurser. Vi rekommenderar att du vidtar åtgärder i den angivna ordningen.

1. [Bestäm din säkerhets position för extern åtkomst](1-secure-access-posture.md)

2. [Identifiera ditt nuvarande tillstånd](2-secure-access-current-state.md) (du är här.)

3. [Skapa en styrnings plan](3-secure-access-plan.md)

4. [Använda grupper för säkerhet](4-secure-access-groups.md)

5. [Över gång till Azure AD B2B](5-secure-access-b2b.md)

6. [Säker åtkomst med hantering av rättigheter](6-secure-access-entitlement-managment.md)

7. [Säker åtkomst med principer för villkorlig åtkomst](7-secure-access-conditional-access.md)

8. [Säker åtkomst med känslighets etiketter](8-secure-access-sensitivity-labels.md)

9. [Säker åtkomst till Microsoft Teams, OneDrive och SharePoint](9-secure-access-teams-sharepoint.md)