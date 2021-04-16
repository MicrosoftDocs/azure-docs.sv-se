---
title: Hantera extern åtkomst med Azure Active Directory Berättigandehantering
description: Hur du använder Azure Active Directory Berättigandehantering som en del av din övergripande säkerhetsplan för extern åtkomst.
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
ms.openlocfilehash: 89744b63a555cc02d35815b4066ce572b7f77e38
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531889"
---
# <a name="manage-external-access-with-entitlement-management"></a>Hantera extern åtkomst med berättigandehantering 


[Berättigandehantering](../governance/entitlement-management-overview.md) är en identitetsstyrningsfunktion som gör det möjligt för organisationer att hantera identitets- och åtkomstlivscykeln i stor skala genom att automatisera arbetsflöden för åtkomstbegäran, åtkomsttilldelningar, granskningar och förfallodatum. Med berättigandehantering kan delegerade icke-administratörer skapa [åtkomstpaket](../governance/entitlement-management-overview.md) som externa användare från andra organisationer kan begära åtkomst till. Ett arbetsflöde för godkännande i flera steg kan konfigureras för att utvärdera begäranden och etablera användare för tidsbegränsad åtkomst med återkommande granskningar. [](../governance/what-is-provisioning.md) Berättigandehantering möjliggör principbaserad etablering och avetablering av externa konton.

## <a name="key-concepts-for-enabling-entitlement-management"></a>Viktiga begrepp för att aktivera berättigandehantering

Följande viktiga begrepp är viktiga att förstå för berättigandehantering.

### <a name="access-packages"></a>Åtkomstpaket

Ett [åtkomstpaket](../governance/entitlement-management-overview.md) är grunden för berättigandehantering. Åtkomstpaket är gruppindelade principstyrda resurser som en användare behöver för att samarbeta i ett projekt eller utföra andra uppgifter. Ett åtkomstpaket kan till exempel innehålla:

* åtkomst till specifika SharePoint-webbplatser.

* företagsprogram, inklusive dina anpassade in-house- och SaaS-appar som Salesforce.

* Microsoft Teams-kanaler.

* Microsoft 365-grupper. 

### <a name="catalogs"></a>Kataloger

Åtkomstpaket finns [i kataloger](../governance/entitlement-management-catalog-create.md). Du skapar en katalog när du vill gruppera relaterade resurser och komma åt paket och delegera möjligheten att hantera dem. Först lägger du till resurser i en katalog och sedan kan du lägga till resurserna för att komma åt paket. Du kanske till exempel vill skapa en "ekonomikatalog" och [delegera dess hantering](../governance/entitlement-management-delegate.md) till en medlem i ekonomiteamet. Den personen kan sedan [lägga till resurser,](../governance/entitlement-management-catalog-create.md)skapa åtkomstpaket och hantera åtkomstgodkännande för dessa paket.

Följande diagram visar en typisk styrningslivscykel för en extern användare som får åtkomst till ett åtkomstpaket som har förfallotid.

![Ett diagram över styrningscykeln för externa användare.](media/secure-external-access/6-governance-lifecycle.png)

### <a name="self-service-external-access"></a>Extern självbetjäning

Du kan öppna åtkomstpaket via [Azure AD Min åtkomst portalen så](../governance/entitlement-management-request-access.md) att externa användare kan begära åtkomst. Principer avgör vem som kan begära ett åtkomstpaket. Du anger vem som får begära åtkomstpaketet:

* Specifika [anslutna organisationer](../governance/entitlement-management-organization.md)

* Alla konfigurerade anslutna organisationer

* Alla användare från valfri organisation

* Medlems- eller gästanvändare som redan finns i din klientorganisation

### <a name="approvals"></a>Godkännanden   
Åtkomstpaket kan innehålla obligatoriskt godkännande för åtkomst. **Implementera alltid godkännandeprocesser för externa användare.** Godkännanden kan vara ett godkännande i en eller flera steg. Godkännanden bestäms av principer. Om både interna och externa användare behöver åtkomst till samma paket kommer du troligen att konfigurera olika åtkomstprinciper för olika kategorier av anslutna organisationer och för interna användare.

### <a name="expiration"></a>Förfallodatum  
Åtkomstpaket kan innehålla ett förfallodatum. Förfallotiden kan anges till en viss dag eller ge användaren ett visst antal dagar för åtkomst. När åtkomstpaketet upphör att gälla och användaren inte har någon annan åtkomst kan B2B-gästanvändarobjektet som representerar användaren tas bort eller blockeras från att logga in. Vi rekommenderar att du framtvingar förfallotid för åtkomstpaket för externa användare. Alla åtkomstpaket har inte förfallotid. Se till att du utför åtkomstgranskningar för dem som inte gör det.

### <a name="access-reviews"></a>Åtkomstgranskningar

Åtkomstpaket kan kräva [periodiska åtkomstgranskningar,](../governance/manage-guest-access-with-access-reviews.md)vilket kräver att paketägaren eller en designmottagare kan intyga det återkommande behovet av användarnas åtkomst. 

Innan du ställer in granskningen bör du fastställa följande.

* Vem

   * Vilka är kriterierna för fortsatt åtkomst?

   * Vilka är de angivna granskarna?

* Hur ofta ska schemalagda granskningar ske?

   * Inbyggda alternativ omfattar månadsvis, kvartalsvis, varannan år eller varje år. 

   * Vi rekommenderar kvartalsvis eller oftare för paket som stöder extern åtkomst. 

 

> [!IMPORTANT]
> Åtkomstgranskningar av åtkomstpaket granskar endast åtkomst som beviljats via berättigandehantering. Du måste därför konfigurera andra processer för att granska all åtkomst som ges till externa användare utanför Rättighetshantering.

Mer information om åtkomstgranskningar finns i [Planera en distribution av Azure AD-åtkomstgranskningar.](../governance/deploy-access-reviews.md)

## <a name="using-automation-in-entitlement-management"></a>Använda automatisering i berättigandehantering

Du kan utföra [funktioner för berättigandehantering med hjälp Microsoft Graph](/graph/tutorial-access-package-api), inklusive

* [Hantera åtkomstpaket](/graph/api/resources/accesspackage?view=graph-rest-beta&preserve-view=true)

* [Hantera åtkomstgranskningar](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true)

* [Hantera anslutna organisationer](/graph/api/resources/connectedorganization?view=graph-rest-beta&preserve-view=true)

* [Hantera inställningar för berättigandehantering](/graph/api/resources/entitlementmanagementsettings?view=graph-rest-beta&preserve-view=true)

## <a name="recommendations"></a>Rekommendationer 

Vi rekommenderar metoder för att styra extern åtkomst med berättigandehantering.

**För projekt med en eller [](../governance/entitlement-management-access-package-create.md) flera affärspartner skapar** och använder du åtkomstpaket för att registrera och etablera dessa partners användare åtkomst till resurser. 

* Om du redan har B2B-användare i din katalog kan du också tilldela dem direkt till lämpliga åtkomstpaket.

* Du kan tilldela åtkomst i [Azure Portal](../governance/entitlement-management-access-package-assignments.md), eller via [Microsoft Graph](/graph/api/resources/accesspackageassignmentrequest?view=graph-rest-beta&preserve-view=true).

**Använd inställningarna för Identitetsstyrning för att ta bort användare från din katalog när deras åtkomstpaket upphör att gälla.**

![Skärmbild av hur du konfigurerar hantering av livscykeln för externa användare.](media/secure-external-access/6-manage-external-lifecycle.png)

De här inställningarna gäller endast för användare som har introducerats via berättigandehantering.

**[Delegera hantering av kataloger och åtkomstpaket till](../governance/entitlement-management-delegate.md) företagsägare som har mer information om vem som ska ha åtkomst till**.

![Skärmbild av att konfigurera en katalog.](media/secure-external-access/6-catalog-management.png)

**[Framtvinga förfallodatum för åtkomstpaket](../governance/entitlement-management-access-package-lifecycle-policy.md) som externa användare har åtkomst till.**


![Skärmbild av konfiguration av förfallotid för åtkomstpaket.](media/secure-external-access/6-access-package-expiration.png)

* Om du känner till slutdatumet för ett projektbaserat åtkomstpaket använder du Datumet för att ange det specifika datumet. 

* Annars rekommenderar vi att förfallotiden inte längre är 365 dagar, såvida det inte är känt som ett flerårsinteragemang.

* Tillåt användare att utöka åtkomsten.

* Kräv godkännande för att bevilja tillägget.

**[Framtvinga åtkomstgranskningar av](../governance/manage-guest-access-with-access-reviews.md) paket för att undvika olämplig åtkomst för gäster.**

![Skärmbild av att skapa ett nytt åtkomstpaket.](media/secure-external-access/6-new-access-package.png)

* Framtvinga granskningar kvartalsvis.

* För efterlevnadskänsliga projekt anger du att granskarna ska vara specifika granskare i stället för att vara självgranskning för externa användare. Användare som är åtkomstpakethanterare är ett bra ställe att börja på för granskare. 

* För mindre känsliga projekt kan användarnas självgranskning minska organisationens arbetsbörda med att ta bort åtkomst från användare som inte längre är med i hemorganisationen.

Mer information finns i Styra [åtkomst för externa användare i Azure AD-berättigandehantering](../governance/entitlement-management-external-users.md) 

### <a name="next-steps"></a>Nästa steg

Se följande artiklar om att skydda extern åtkomst till resurser. Vi rekommenderar att du vidta åtgärderna i den angivna ordningen.

1. [Fastställa din säkerhetsstatus för extern åtkomst](1-secure-access-posture.md)

2. [Identifiera ditt aktuella tillstånd](2-secure-access-current-state.md)

3. [Skapa en styrningsplan](3-secure-access-plan.md)

4. [Använda grupper för säkerhet](4-secure-access-groups.md)

5. [Övergång till Azure AD B2B](5-secure-access-b2b.md)

6. [Säker åtkomst med berättigandehantering](6-secure-access-entitlement-managment.md) (du är här.)

7. [Säker åtkomst med principer för villkorsstyrd åtkomst](7-secure-access-conditional-access.md)

8. [Säker åtkomst med känslighetsetiketter](8-secure-access-sensitivity-labels.md)

9. [Säker åtkomst till Microsoft Teams, OneDrive och SharePoint](9-secure-access-teams-sharepoint.md)

 

