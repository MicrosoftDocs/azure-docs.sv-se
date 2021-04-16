---
title: Överför Azure-prenumerationer mellan prenumeranter och molnlösningsleverantörer
description: Lär dig hur du överför Azure-prenumerationer mellan prenumeranter och molnlösningsleverantörer.
author: bandersmsft
ms.reviewer: dhgandhi
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: 687db46ea2f6c9c4fae6e5355e3236cde3d7a401
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567255"
---
# <a name="transfer-azure-subscriptions-between-subscribers-and-csps"></a>Överför Azure-prenumerationer mellan prenumeranter och molnlösningsleverantörer

Den här artikeln innehåller enkla steg för att överföra Azure-prenumerationer till och från molnlösningsleverantörer (CSP) och deras kunder. Informationen här är avsedd att hjälpa Azure-prenumeranten att samarbeta med sin partner. Information som Microsoft-partner använder för överföringsprocessen dokumenteras i [Läs om hur du överför en Azure-prenumeration för en kund till en annan partner](/partner-center/switch-azure-subscriptions-to-a-different-partner).

Innan du startar en överföringsbegäran bör du ladda ned eller exportera eventuell kostnads- och faktureringsinformation som du vill behålla. Information om fakturering och användning överförs inte med prenumerationen. Mer information om export av kostnadshanteringsdata finns i [Skapa och hantera exporterade data](../costs/tutorial-export-acm-data.md). Mer information om hur du laddar ned din faktura och dina användningsdata finns i [Ladda ned eller visa din Azure-faktura och information om den dagliga användningen](download-azure-invoice-daily-usage-date.md).

Om du har några befintliga reservationer upphör de att tillämpas 90 dagar efter att du har överfört en prenumeration. Se till att [avbryta eventuella reservationer och återbetala dem innan](../reservations/exchange-and-refund-azure-reservations.md) du överför en prenumeration för att undvika avgifter efter respitperioden på 90 dagar.

## <a name="transfer-ea-subscriptions-to-a-csp-partner"></a>Överföra EA-prenumerationer till en CSP-partner

CSP-partner som är certifierade som [Azure Expert-leverantörer av hanterade tjänster (MSP)](https://partner.microsoft.com/membership/azure-expert-msp) kan begära överföring av Azure-prenumerationer för kunder som har ett direkt Enterprise-avtal (EA). Prenumerationsöverföringar tillåts bara för kunder som har godkänt ett Microsoft-kundavtal (MCA) och köpt en Azure-plan med CSP-programmet.

När begäran har godkänts kan CSP:n tillhandahålla en kombinerad faktura till sina kunder. Mer information om hur CSP:er överför prenumerationer finns i [Få faktureringsägarskap för Azure-prenumerationer för ditt MPA-konto](mpa-request-ownership.md).

>[!IMPORTANT]
> När du har överfört en EA-prenumeration till en CSP-partner återställs alla kvotökningar som tidigare tillämpats för EA-prenumerationen till standardvärdet. Om du behöver öka kvoten efter att prenumerationen har överförts ber du molnlösningsleverantören skicka en begäran om [ökad kvot](../../azure-portal/supportability/regional-quota-requests.md). 

## <a name="other-subscription-transfers-to-a-csp-partner"></a>Andra prenumerationsöverföringar till en CSP-partner

För att kunna överföra andra Azure-prenumerationer till en CSP-partner måste prenumeranten flytta resurser från källprenumerationer till CSP-prenumerationer. Använd följande vägledning när du flyttar resurser mellan prenumerationer.

1. Upprätta en [återförsäljarrelation](/partner-center/request-a-relationship-with-a-customer) med kunden. Granska [översikten över CSP regional auktorisering](/partner-center/regional-authorization-overview) för att se till att både kund- och partnerklientorganisationen finns i samma auktoriserade regioner.
1. Samarbeta med din CSP-partner när du skapar Azure CSP-målprenumerationer.
1. Se till att käll- och målprenumerationerna finns i samma Azure Active Directory-klient (Azure AD).  
    Du kan inte ändra Azure AD-klienten för en Azure CSP-prenumeration. I stället måste du lägga till eller associera källprenumerationen till CSP Azure AD-klienten. Mer information finns i [Lägga till eller associera en Azure-prenumeration till Azure Active Directory-klienten](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    > [!IMPORTANT]
    > - När du associerar en prenumeration till en annan Azure AD-katalog förlorar de användare som har tilldelats roller med [rollbaserad åtkomstkontroll (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) sin åtkomst. Klassiska prenumerationsadministratörer, inklusive tjänstadministratörer och medadministratörer, förlorar också åtkomsten.
    > - Principtilldelningar tas också bort från en prenumeration när prenumerationen associeras med en annan katalog.
1. Det användarkonto som du använder när du gör överföringen måste ha [Azure RBAC](add-change-subscription-administrator.md)-ägaråtkomst på båda prenumerationerna.
1. Innan du börjar måste du [verifiera](/rest/api/resources/resources/validatemoveresources) att alla Azure-resurser kan flyttas från källprenumerationen till målprenumerationen.  
    Vissa Azure-resurser kan inte flyttas mellan prenumerationer. En fullständig lista över Azure-resurser som kan flyttas finns i [Stöd för flytt av resurser](../../azure-resource-manager/management/move-support-resources.md).
    > [!IMPORTANT]
    >  - Azure CSP stöder endast Azure Resource Manager-resurser. Om det finns Azure-resurser i källprenumerationen som har skapats med den klassiska distributionsmodellen i Azure måste du först migrera dem till [Azure Resource Manager](/azure/cloud-solution-provider/migration/ea-payg-to-azure-csp/ea-open-direct-asm-to-arm) innan du flyttar mellan prenumerationerna. För att kunna visa webbsidan måste du vara en partner.

1. Kontrollera att alla källprenumerationstjänster använder Azure Resource Manager-modellen. Överför sedan resurser från källprenumerationen till målprenumerationen med hjälp av [Azure Resource Move](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - När du flyttar Azure-resurser mellan prenumerationer kan det uppstå avbrott i tjänsten, beroende på vilka resurser som ingår i prenumerationerna.

## <a name="transfer-csp-subscription-to-other-offer"></a>Överföra CSP-prenumeration till ett annat erbjudande

För att kunna överföra andra Azure-prenumerationer till en CSP-partner måste prenumeranten flytta resurser mellan källprenumerationer och målprenumerationer. Detta görs av en partner och en kund – det utförs inte av en Microsoft-representant.

1. Kunden skapar Azure-målprenumerationer.
1. Se till att käll- och målprenumerationerna finns i samma Azure Active Directory-klient (Azure AD). Mer information om hur du ändrar en Azure AD-klient finns i [Lägga till eller associera en Azure-prenumeration till Azure Active Directory-klienten](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    Observera att alternativet för att byta katalog inte stöds med CSP-prenumerationen. Du överförs till exempel från en CSP till en prenumeration där du betalar per användning. Du måste ändra katalog för prenumerationen med betalning per användning så att katalogen matchar.

    > [!IMPORTANT]
    >  - När du associerar en prenumeration med en annan katalog förlorar de användare som har tilldelats roller med [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) sin åtkomst. Klassiska prenumerationsadministratörer, inklusive tjänstadministratörer och medadministratörer, förlorar också åtkomsten.
    >  - Principtilldelningar tas också bort från en prenumeration när prenumerationen associeras med en annan katalog.

1. Det kundanvändarkonto som du använder för att göra överföringen måste ha [Azure RBAC-ägaråtkomst](add-change-subscription-administrator.md) för båda prenumerationerna.
1. Innan du börjar måste du [verifiera](/rest/api/resources/resources/validatemoveresources) att alla Azure-resurser kan flyttas från källprenumerationen till målprenumerationen.
    > [!IMPORTANT]
    >  - Vissa Azure-resurser kan inte flyttas mellan prenumerationer. En fullständig lista över Azure-resurser som kan flyttas finns i [Stöd för flytt av resurser](../../azure-resource-manager/management/move-support-resources.md).

1. Överför resurser från källprenumerationen till målprenumerationen med hjälp av [Azure Resource Move](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - När du flyttar Azure-resurser mellan prenumerationer kan det uppstå avbrott i tjänsten, beroende på vilka resurser som ingår i prenumerationerna.

## <a name="next-steps"></a>Nästa steg
- [Få faktureringsägarskap för Azure-prenumerationer för ditt MPA-konto](mpa-request-ownership.md).
- Läs om hur du kan [hantera konton och prenumerationer med Azure-fakturering](../index.yml).
