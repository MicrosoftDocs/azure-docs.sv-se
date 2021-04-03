---
title: Azure-delegerad resurshantering
description: Azure-delegerad resurs hantering är en viktig del av Azure Lighthouse, vilket gör det möjligt för tjänst leverantörer att hantera delegerade resurser i stor skala med flexibilitet och precision.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: d484e61fc4ab3714eb362b26d64d449890065888
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92203865"
---
# <a name="azure-delegated-resource-management"></a>Azure-delegerad resurshantering

Azure-delegerad resurs hantering är en av de viktigaste komponenterna i [Azure DataLighthouses](../overview.md). Med Azure-delegerad resurs hantering kan tjänst leverantörer förenkla kund engagemang och onboarding-upplevelser, samtidigt som de delegerade resurserna hanteras i stor skala med flexibilitet och precision. Kunderna behåller kontrollen över vilka tjänst leverantörer som har åtkomst till sin klient, kunderna behåller kontroll över vem som har åtkomst till sin klient, vilka resurser de kan komma åt och vilka åtgärder som kan vidtas.

## <a name="what-is-azure-delegated-resource-management"></a>Vad är Azure-delegerad resurs hantering?

Azure-delegerad resurs hantering möjliggör logisk projektion av resurser från en klient organisation till en annan klient organisation. Detta gör det möjligt för behöriga användare i en Azure Active Directory (Azure AD) att utföra hanterings åtgärder för olika Azure AD-klienter som tillhör sina kunder. Tjänste leverantörer kan logga in på sin egen Azure AD-klient och ha behörighet att arbeta i delegerade kund prenumerationer och resurs grupper. Detta gör det möjligt för dem att utföra hanterings åtgärder på uppdrag av sina kunder, utan att behöva logga in på varje enskild kund klient.

> [!TIP]
> Azure-delegerad resurs hantering kan också användas [inom ett företag som har flera Azure AD-klienter för](enterprise.md) att förenkla hanteringen av flera innehavare.

Med Azure-delegerad resurs hantering kan behöriga användare arbeta direkt i kontexten för en kund prenumeration utan att ha ett konto i den kundens klient organisation eller som medägare till kundens klient.

Med [hanterings upplevelsen för flera innehavare](cross-tenant-management-experience.md) kan du arbeta effektivare med Azures hanterings tjänster som Azure Policy, Azure Security Center och mycket annat. Alla tjänste leverantörs aktiviteter spåras i aktivitets loggen, som lagras i kundens klient organisation (och kan visas av användare i hanterings klienten). Det innebär att användare i både hanteringen och den hanterade klienten enkelt kan identifiera den användare som är associerad med eventuella ändringar.

Du kan [publicera den nya hanterade tjänstens erbjudande typ till Azure Marketplace](../how-to/publish-managed-services-offers.md) för att enkelt publicera kunder till Azure Lighthouse. Du kan också [slutföra onboarding-processen genom att distribuera Azure Resource Manager mallar](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Så här fungerar Azure-delegerad resurs hantering

Så här fungerar Azure-delegerad resurs hantering på hög nivå:

1. Först ska du identifiera åtkomsten (roller) som dina grupper, tjänstens huvud namn eller användare behöver för att hantera kundens Azure-resurser. Åtkomst definitionen innehåller hantering av klient-ID tillsammans med **principalId** -identiteter från din klient mapp som är mappad till [inbyggda **roll definitions** -värden](../../role-based-access-control/built-in-roles.md) (deltagare, VM-deltagare, läsare osv.).
2. Du kan ange den här åtkomsten och publicera kunden till Azure-Lighthouse på något av två sätt:
   - [Publicera ett Azure Marketplace-hanterat tjänst erbjudande](../how-to/publish-managed-services-offers.md) (privat eller offentlig) som kunden accepterar
   - [Distribuera en Azure Resource Manager-mall till kundens klient](../how-to/onboard-customer.md) för en eller flera specifika prenumerationer eller resurs grupper

3. När kunden har registrerats kan behöriga användare logga in på den hanterande klienten och utföra uppgifter i den angivna kund omfattningen, baserat på den åtkomst som du har definierat. Kunderna kan granska tjänste leverantörs åtgärder och välja att ta bort åtkomst om det behövs.

> [!NOTE]
> Du kan hantera delegerade resurser som finns i olika [regioner](../../availability-zones/az-overview.md#regions). Delegering av prenumerationer i ett [nationellt moln](../../active-directory/develop/authentication-national-cloud.md) och det offentliga Azure-molnet, eller över två separata nationella moln, stöds dock inte.

## <a name="support-for-azure-delegated-resource-management"></a>Stöd för Azure-delegerad resurs hantering

Om du behöver hjälp som rör Azure-delegerad resurs hantering kan du öppna en supportbegäran i Azure Portal. Välj **teknisk** för **typ av problem**. Välj en prenumeration och välj sedan **Lighthouse** (under **övervakning & hantering**).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanterings upplevelser mellan flera innehavare](cross-tenant-management-experience.md).
- Lär dig mer om [Managed Services-erbjudanden på Azure Marketplace](managed-services-offers.md).
