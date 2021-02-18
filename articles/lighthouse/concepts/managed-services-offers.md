---
title: Erbjudanden om hanterade tjänster på Azure Marketplace
description: Med hanterade tjänst erbjudanden kan du sälja resurs hanterings erbjudanden till kunder på Azure Marketplace.
ms.date: 02/17/2021
ms.topic: conceptual
ms.openlocfilehash: e66a87751e0375804031e777a40c5fc1d0e8a7e2
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092992"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Erbjudanden om hanterade tjänster på Azure Marketplace

I den här artikeln beskrivs typen av **hanterad tjänst** erbjudande på [Azure Marketplace](https://azuremarketplace.microsoft.com). Med hanterade tjänst erbjudanden kan du erbjuda kunder till resurs hanterings tjänster via [Azure Lighthouse](../overview.md). Du kan göra dessa erbjudanden tillgängliga för alla potentiella kunder, eller bara för en eller flera olika kunder. Eftersom du fakturerar kunder direkt för kostnader som rör dessa hanterade tjänster, finns det inga avgifter som debiteras av Microsoft.

## <a name="understand-managed-service-offers"></a>Förstå Managed Service-erbjudanden

Hanterade tjänster gör det enklare att integrera kunder i Azure Lighthouse. När en kund köper ett erbjudande på Azure Marketplace kan de ange vilka prenumerationer och/eller resurs grupper som ska registreras.

Efter det kommer användare i din organisation att kunna arbeta med dessa resurser i hanteringen av [Azure-delegerad resurs hantering](azure-delegated-resource-management.md), enligt den åtkomst som du definierade när du skapade erbjudandet. Detta görs via ett manifest som anger de Azure Active Directory (Azure AD) användare, grupper och tjänstens huvud namn som kommer att ha åtkomst till kund resurser, tillsammans med [roller](tenants-users-roles.md) som definierar deras åtkomst nivå.

> [!NOTE]
> Hanterade tjänst erbjudanden kanske inte är tillgängliga i Azure Government och andra nationella moln.

## <a name="public-and-private-offers"></a>Offentliga och privata erbjudanden

Varje hanterat tjänst erbjudande innehåller en eller flera planer. Planer kan vara antingen privata eller offentliga.

Om du vill begränsa ditt erbjudande till vissa kunder kan du publicera en privat plan. När du gör det kan planen bara köpas för de angivna prenumerations-ID: n som du anger. Mer information finns i [privata erbjudanden](../../marketplace/private-offers.md).

> [!NOTE]
> Privata erbjudanden stöds inte med prenumerationer som upprättats via en åter försäljare av CSP-programmet (Cloud Solution Provider).

Med offentliga planer kan du marknadsföra dina tjänster till nya kunder. Dessa är vanligt vis mer lämpliga när du bara behöver begränsad åtkomst till kundens klient organisation. När du har upprättat en relation med en kund kan du, om de bestämmer dig för att ge organisationen ytterligare åtkomst, göra detta genom att publicera en ny privat plan för den kunden eller genom [att registrera dem för ytterligare åtkomst med hjälp av Azure Resource Manager mallar](../how-to/onboard-customer.md).

Om det behövs kan du inkludera både offentliga och privata planer i samma erbjudande.

> [!IMPORTANT]
> När en plan har publicerats som offentlig kan du inte ändra den till privat. Använd en privat plan för att kontrol lera vilka kunder som kan acceptera ditt erbjudande och delegera resurser. Med en offentlig plan kan du inte begränsa tillgänglighet till vissa kunder eller till och med ett visst antal kunder (även om du kan sluta sälja planen helt om du väljer att göra det). Du kan [ta bort åtkomsten till en delegering efter att](../how-to/remove-delegation.md) en kund har accepterat ett erbjudande endast om du har inkluderat en **auktorisering** med **roll definitionen** för [tilldelning av hanterade tjänster för registrering av hanterade tjänster](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) när du publicerade erbjudandet. Du kan också kontakta kunden och be dem att [ta bort åtkomsten](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

## <a name="publish-managed-service-offers"></a>Publicera hanterade tjänst erbjudanden

Information om hur du publicerar ett hanterat tjänst erbjudande finns i [publicera ett hanterat tjänst erbjudande på Azure Marketplace](../how-to/publish-managed-services-offers.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure-delegerad resurs hantering](azure-delegated-resource-management.md) och [flera klient hanterings upplevelser](cross-tenant-management-experience.md).
- [Publicera Managed Service-erbjudanden](../how-to/publish-managed-services-offers.md) på Azure Marketplace.
