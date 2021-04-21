---
title: Azure-delegerad resurshantering
description: Azure-delegerad resurshantering är en viktig del av Azure Lighthouse, så att tjänstleverantörer kan hantera delegerade resurser i stor skala med flexibilitet och precision.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: 93a3de257fe88de4915eff3582ff38fc03ef380e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767771"
---
# <a name="azure-delegated-resource-management"></a>Azure-delegerad resurshantering

Azure-delegerad resurshantering är en av huvudkomponenterna i [Azure Lighthouse](../overview.md). Med Azure-delegerad resurshantering kan tjänstleverantörer förenkla kundengagemang och registrering, samtidigt som de hanterar delegerade resurser i stor skala med flexibilitet och precision. Kunderna har kontroll över vem som har åtkomst till deras klientorganisation, vilka resurser de kan komma åt och vilka åtgärder som kan vidtas.

## <a name="what-is-azure-delegated-resource-management"></a>Vad är Azure-delegerad resurshantering?

Azure-delegerad resurshantering möjliggör logisk projektion av resurser från en klientorganisation till en annan klientorganisation. Detta gör att behöriga användare i en Azure Active Directory-klientorganisation (Azure AD) kan utföra hanteringsåtgärder över olika Azure AD-klienter som tillhör deras kunder. Tjänstleverantörer kan logga in på sin egen Azure AD-klientorganisation och ha behörighet att arbeta i delegerade kundprenumerationer och resursgrupper. På så sätt kan de utföra hanteringsåtgärder åt sina kunder, utan att behöva logga in på varje enskild kundklientorganisation.

> [!TIP]
> Azure-delegerad resurshantering kan också användas i ett företag som har flera Azure AD-klientorganisationsklienter för att förenkla hanteringen mellan klientorganisationen. [](enterprise.md)

Med Azure-delegerad resurshantering kan behöriga användare arbeta direkt i kontexten för en kundprenumeration utan att ha ett konto i kundens klientorganisation eller vara medägare av kundens klientorganisation.

Med [hanteringsupplevelsen mellan klientorganisationen](cross-tenant-management-experience.md) kan du arbeta mer effektivt med Azure-hanteringstjänster som Azure Policy, Azure Security Center och mycket mer. All tjänstleverantörsaktivitet spåras i aktivitetsloggen, som lagras i kundens klientorganisation (och kan visas av användare i den hanterande klientorganisationen). Det innebär att användare i både den hanterade och den hanterade klientorganisationen enkelt kan identifiera den användare som är associerad med eventuella ändringar.

Du kan [publicera den nya erbjudandetypen hanterad tjänst för att Azure Marketplace](../how-to/publish-managed-services-offers.md) enkelt registrera kunder till Azure Lighthouse. Du kan också slutföra [onboarding-processen genom att distribuera Azure Resource Manager mallar](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Så här fungerar Azure-delegerad resurshantering

På en hög nivå fungerar Azure-delegerad resurshantering på följande sätt:

1. Först identifierar du den åtkomst (roller) som dina grupper, tjänstens huvudnamn eller användare behöver för att hantera kundens Azure-resurser. Åtkomstdefinitionen innehåller det hanterande klientorganisations-ID:t tillsammans med **principalId-identiteter** från din klientorganisation mappade till [inbyggda **roleDefinition-värden**](../../role-based-access-control/built-in-roles.md) (deltagare, VM-deltagare, läsare osv.).
2. Du anger den här åtkomsten och registrera kunden för Azure Lighthouse på något av två sätt:
   - [Publicera ett Azure Marketplace hanterat tjänsterbjudande](../how-to/publish-managed-services-offers.md) (privat eller offentligt) som kunden accepterar
   - [Distribuera en Azure Resource Manager mall till kundens klientorganisation för](../how-to/onboard-customer.md) en eller flera specifika prenumerationer eller resursgrupper

3. När kunden har blivit registrerad kan behöriga användare logga in på din hanterande klientorganisation och utföra uppgifter i det angivna kundomfånget, baserat på den åtkomst som du har definierat. Kunder kan granska tjänstleverantörens åtgärder och välja att ta bort åtkomsten om det behövs.

> [!NOTE]
> Du kan hantera delegerade resurser som finns i olika [regioner.](../../availability-zones/az-overview.md#regions) Delegering av prenumerationer i ett [nationellt moln](../../active-directory/develop/authentication-national-cloud.md) och det offentliga Azure-molnet, eller mellan två separata nationella moln, stöds dock inte.

## <a name="support-for-azure-delegated-resource-management"></a>Stöd för Azure-delegerad resurshantering

Om du behöver hjälp med Azure-delegerad resurshantering kan du öppna en supportbegäran i Azure Portal. För **Typ av problem** väljer du **Teknisk.** Välj en prenumeration och välj sedan **Lighthouse** (under **Övervakning & Hantering).**

## <a name="next-steps"></a>Nästa steg

- Lär dig [mer om hanteringsupplevelser mellan klientorganisationen.](cross-tenant-management-experience.md)
- Läs mer [om erbjudanden för hanterade tjänster i Azure Marketplace](managed-services-offers.md).
