---
title: Hantera extern åtkomst med Azure Active Directory rättighets hantering
description: Använda Azure Active Directory rättighets hantering som en del av din övergripande säkerhets plan för extern åtkomst.
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
ms.openlocfilehash: aeba704f315c6aabb2f9892777e483074e4bc90d
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/23/2020
ms.locfileid: "97744204"
---
# <a name="manage-external-access-with-entitlement-management"></a>Hantera extern åtkomst med hantering av rättigheter 


[Rättighets hantering](../governance/entitlement-management-overview.md) är en identitets styrnings funktion som gör det möjligt för organisationer att hantera identitets-och åtkomst livs cykeln i skala genom att automatisera åtkomst till begär ande arbets flöden, åtkomst tilldelningar, recensioner och förfallo datum. Rättighets hantering gör det möjligt för delegerade icke-administratörer att skapa [åtkomst paket](../governance/entitlement-management-overview.md) som externa användare från andra organisationer kan begära åtkomst till. Ett arbets flöde för godkännande med flera steg kan konfigureras för att utvärdera förfrågningar och [etablera](../governance/what-is-provisioning.md) användare för tidsbegränsad åtkomst med återkommande granskningar. Hantering av rättigheter möjliggör principbaserad etablering och avetablering av externa konton.

## <a name="key-concepts-for-enabling-entitlement-management"></a>Viktiga begrepp för att aktivera hantering av rättigheter

Följande viktiga begrepp är viktiga för att förstå hantering av rättigheter.

### <a name="access-packages"></a>Åtkomst paket

Ett [Access-paket](../governance/entitlement-management-overview.md) är grunden för rättighets hantering. Åtkomst paketen är grupper av reglerade resurser som en användare behöver för att samar beta med ett projekt eller utföra andra uppgifter. Till exempel kan ett Access-paket innehålla:

* åtkomst till vissa SharePoint-webbplatser.

* företags program inklusive dina anpassade interna och SaaS appar som Salesforce.

* Microsoft Teams-kanaler.

* Microsoft 365 grupper. 

### <a name="catalogs"></a>Kataloger

Åtkomst paket finns i [kataloger](../governance/entitlement-management-catalog-create.md). Du skapar en katalog när du vill gruppera relaterade resurser och få åtkomst till paket och delegera möjligheten att hantera dem. Först lägger du till resurser i en katalog och sedan kan du lägga till dessa resurser för att få åtkomst till paket. Du kanske till exempel vill skapa en "ekonomi"-katalog och [delegera dess hantering](../governance/entitlement-management-delegate.md) till en medlem i ekonomi teamet. Den personen kan sedan [lägga till resurser](../governance/entitlement-management-catalog-create.md), skapa åtkomst paket och hantera åtkomst godkännande till dessa paket.

Följande diagram visar en typisk styrnings livs cykel för en extern användare som får åtkomst till ett Access-paket som har ett förfallo datum.

![Ett diagram över den externa användar styrnings cykeln.](media/secure-external-access/6-governance-lifecycle.png)

### <a name="self-service-external-access"></a>Extern åtkomst till självbetjäning

Du kan få åtkomst till paket för Surface via [Azure AD My Access-portalen](../governance/entitlement-management-request-access.md) för att göra det möjligt för externa användare att begära åtkomst. Principer avgör vem som kan begära ett åtkomst paket. Du anger vem som tillåts att begära åtkomst paketet:

* Vissa [anslutna organisationer](../governance/entitlement-management-organization.md)

* Alla konfigurerade anslutna organisationer

* Alla användare från vilken organisation som helst

* Användare av medlemmar eller gäster som redan finns i din klient organisation

### <a name="approvals"></a>Godkännanden   
Åtkomst paket kan innehålla obligatoriskt godkännande för åtkomst. **Implementera alltid godkännande processer för externa användare**. Godkännanden kan vara ett enda eller ett godkännande av flera steg. Godkännanden bestäms av principer. Om både interna och externa användare behöver åtkomst till samma paket kommer du troligen att ha olika åtkomst principer för olika kategorier av anslutna organisationer och för interna användare.

### <a name="expiration"></a>Förfallodatum  
Åtkomst paket kan innehålla ett förfallo datum. Förfallo datum kan anges till en angiven dag eller ge användaren ett bestämt antal dagar för åtkomst. När åtkomst paketet upphör att gälla och användaren inte har någon annan åtkomst, kan det B2B-gäst användar objekt som representerar användaren tas bort eller blockeras från att logga in. Vi rekommenderar att du tillämpar förfallo datum på åtkomst paket för externa användare. Alla åtkomst paket har inte förfallit. Se till att du utför åtkomst granskningar för de som inte gör det.

### <a name="access-reviews"></a>Åtkomstgranskningar

Åtkomst paket kan kräva periodiska [åtkomst granskningar](../governance/manage-guest-access-with-access-reviews.md), som kräver att paket ägaren eller en formgivningsmall bestyrkar fortsatt behov av användarens åtkomst. 

Innan du ställer in din granskning ska du bestämma följande.

* Vem

   * Vilka är kriterierna för fortsatt åtkomst?

   * Vilka är de angivna granskarna?

* Hur ofta ska schemalagda granskningar ske?

   * Inbyggda alternativ omfattar varje månad, kvartals vis, två per år eller varje år. 

   * Vi rekommenderar kvartals vis eller oftare för paket som stöder extern åtkomst. 

 

> [!IMPORTANT]
> Åtkomst granskningar av åtkomst paket granska endast åtkomst som beviljats via rättighets hantering. Du måste därför konfigurera andra processer för att granska all åtkomst som erbjuds till externa användare utanför hantering av rättigheter.

Mer information om åtkomst granskningar finns i [Planera distribution av åtkomst granskningar för Azure AD](../governance/deploy-access-reviews.md).

## <a name="using-automation-in-entitlement-management"></a>Använda Automation i hantering av rättigheter

Du kan utföra [rättighets hanterings funktioner med hjälp av Microsoft Graph](https://docs.microsoft.com/graph/tutorial-access-package-api), inklusive

* [Hantera åtkomst paket](https://docs.microsoft.com/graph/api/resources/accesspackage?view=graph-rest-beta)

* [Hantera åtkomst granskningar](https://docs.microsoft.com/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta)

* [Hantera anslutna organisationer](https://docs.microsoft.com/graph/api/resources/connectedorganization?view=graph-rest-beta)

* [Hantera inställningar för hantering av rättigheter](https://docs.microsoft.com/graph/api/resources/entitlementmanagementsettings?view=graph-rest-beta)

## <a name="recommendations"></a>Rekommendationer 

Vi rekommenderar metoderna för att styra extern åtkomst med hantering av rättigheter.

**För projekt med en eller flera affärs partner kan du [skapa och använda Access-paket](../governance/entitlement-management-access-package-create.md) för att publicera och etablera dessa partners användares åtkomst till resurser**. 

* Om du redan har B2B-användare i din katalog kan du också tilldela dem direkt till lämpliga åtkomst paket.

* Du kan tilldela åtkomst i [Azure Portal](../governance/entitlement-management-access-package-assignments.md)eller via [Microsoft Graph](https://docs.microsoft.com/graph/api/resources/accesspackageassignmentrequest?view=graph-rest-beta).

**Använd dina inställningar för identitets styrning för att ta bort användare från din katalog när åtkomst paketen upphör att gälla**.

![Skärm bild av konfiguration hantera livs cykeln för externa användare.](media/secure-external-access/6-manage-external-lifecycle.png)

Dessa inställningar gäller endast för användare som har registrerats via rättighets hantering.

**[Delegera hantering av kataloger och åtkomst paket](../governance/entitlement-management-delegate.md) till företags ägare, som har mer information om vem som ska ha åtkomst**.

![Skärm bild som visar hur du konfigurerar en katalog.](media/secure-external-access/6-catalog-management.png)

**[Framtvinga förfallo datum för de åtkomst paket](../governance/entitlement-management-access-package-lifecycle-policy.md) som externa användare har åtkomst till.**


![Skärm bild över att konfigurera åtkomst paketets förfallo datum.](media/secure-external-access/6-access-package-expiration.png)

* Om du känner till slutdatumet för ett projekt baserat åtkomst paket använder du datumet för att ange ett visst datum. 

* Annars rekommenderar vi att giltighets tiden inte längre är 365 dagar, om det inte är känt att det är ett fyraårigt engagemang.

* Tillåt användare att utöka åtkomst.

* Kräv godkännande för att bevilja tillägget.

**[Framtvinga åtkomst granskningar av paket](../governance/manage-guest-access-with-access-reviews.md) för att undvika olämplig åtkomst för gäster.**

![Skärm bild som visar hur du skapar ett nytt Access-paket.](media/secure-external-access/6-new-access-package.png)

* Påtvinga granskningar kvartals vis.

* För kompatibilitets känsliga projekt anger du att granskarna ska vara enskilda granskare i stället för själv granskning för externa användare. De användare som har åtkomst till paket hanterare är en bra plats för att starta för granskare. 

* För mindre känsliga projekt kan användare med själv granskning minska belastningen på organisationen för att ta bort åtkomst från användare som inte längre har sin hem organisation.

Mer information finns i [styra åtkomsten för externa användare i hantering av Azure AD-rättigheter](../governance/entitlement-management-external-users.md) 

### <a name="next-steps"></a>Nästa steg

Se följande artiklar om hur du skyddar extern åtkomst till resurser. Vi rekommenderar att du vidtar åtgärder i den angivna ordningen.

1. [Bestäm din säkerhets position för extern åtkomst](1-secure-access-posture.md)

2. [Identifiera ditt nuvarande tillstånd](2-secure-access-current-state.md)

3. [Skapa en styrnings plan](3-secure-access-plan.md)

4. [Använda grupper för säkerhet](4-secure-access-groups.md)

5. [Över gång till Azure AD B2B](5-secure-access-b2b.md)

6. [Säker åtkomst med hantering av rättigheter](6-secure-access-entitlement-managment.md) (du är här.)

7. [Säker åtkomst med principer för villkorlig åtkomst](7-secure-access-conditional-access.md)

8. [Säker åtkomst med känslighets etiketter](8-secure-access-sensitivity-labels.md)

9. [Säker åtkomst till Microsoft Teams, OneDrive och SharePoint](9-secure-access-teams-sharepoint.md)

 

 
