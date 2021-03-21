---
title: Att tänka på med Cloud Solution Provider-programmet
description: För CSP-partner hjälper Azure delegerad resurs hantering att förbättra säkerheten och kontrollen genom att aktivera detaljerade behörigheter.
ms.date: 03/12/2021
ms.topic: conceptual
ms.openlocfilehash: 8736cf913739f2bd16fb519aed98fd336f6876a5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419396"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure-Lighthouse och Cloud Solution Provider-programmet

Om du är en [CSP-partner (Cloud Solution Provider)](/partner-center/csp-overview) kan du redan komma åt de Azure-prenumerationer som har skapats för dina kunder via CSP-programmet med hjälp av [administrate-funktionen (administrera på uppdrag av)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) . Med den här åtkomsten kan du direkt stödja, konfigurera och hantera dina kunders prenumerationer.

Med [Azure Lighthouse](../overview.md)kan du använda Azure-delegerad resurs hantering tillsammans med administrate. Detta bidrar till att förbättra säkerheten och minskar onödig åtkomst genom att aktivera mer detaljerade behörigheter för dina användare. Det ger också ökad effektivitet och skalbarhet, eftersom dina användare kan arbeta över flera kund prenumerationer med hjälp av en enda inloggning i din klient organisation.

> [!TIP]
> Du kan skydda kund resurser genom att granska och följa våra [rekommenderade säkerhets metoder](recommended-security-practices.md) tillsammans med [partner säkerhets kraven](/partner-center/partner-security-requirements).

## <a name="administer-on-behalf-of-aobo"></a>Administrera på uppdrag av (ADMINISTRATE)

Med ADMINISTRATE kommer alla användare med [Administratörs agent](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) rollen i din klient organisation ha administrate åtkomst till Azure-prenumerationer som du skapar via CSP-programmet. Alla användare som behöver åtkomst till kunders prenumerationer måste vara medlemmar i den här gruppen. ADMINISTRATE tillåter inte flexibiliteten att skapa distinkta grupper som fungerar med olika kunder, eller för att aktivera olika roller för grupper eller användare.

![Diagram som visar klient hantering med hjälp av ADMINISTRATE.](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Azure-delegerad resurshantering

Med Azure Lighthouse kan du tilldela olika grupper till olika kunder eller roller, som du ser i följande diagram. Eftersom användarna får rätt åtkomst nivå via Azure-delegerad resurs hantering kan du minska antalet användare som har rollen administratörs agent (och därmed ha fullständig ADMINISTRATE-åtkomst). Detta bidrar till att förbättra säkerheten genom att begränsa onödig åtkomst till dina kunders resurser. Det ger dig också större flexibilitet att hantera flera kunder i stor skala.

När du registrerar en prenumeration som du har skapat via CSP-programmet följer du stegen som beskrivs i [publicera en prenumeration på Azure Lighthouse](../how-to/onboard-customer.md). Alla användare som har rollen administratörs agent i din klient organisation kan utföra denna onboarding.

![Diagram som visar klient hantering med hjälp av ADMINISTRATE och Azure-delegerad resurs hantering.](../media/csp-2.jpg)

> [!TIP]
> [Hanterade tjänst erbjudanden](managed-services-offers.md) med privata planer stöds inte med prenumerationer som upprättats via en åter försäljare av program varan för Cloud Solution Provider (CSP). Du kan publicera dessa prenumerationer på Azure-Lighthouse med [hjälp av Azure Resource Manager mallar](../how-to/onboard-customer.md).

> [!NOTE]
> Sidan [ **Mina kunder** i Azure Portal](../how-to/view-manage-customers.md) innehåller nu en **moln lösnings leverantör (förhands granskning)** , som visar fakturerings information och resurser för CSP-kunder som har [undertecknat Microsofts kund avtal (MCA)](/partner-center/confirm-customer-agreement) och som ingår i [Azure-prenumerationen](/partner-center/azure-plan-get-started). Mer information finns i [Kom igång med ditt fakturerings konto för Microsoft partner avtal](../../cost-management-billing/understand/mpa-overview.md).
>
> CSP-kunder kan visas i det här avsnittet oavsett om de också har publicerats till Azure-Lighthouse. Om de har det visas de också i avsnittet **kunder** , enligt beskrivningen i [Visa och hantera kunder och delegerade resurser](../how-to/view-manage-customers.md). På samma sätt behöver inte en CSP-kund visas i avsnittet **Cloud Solution Provider (för hands version)** i **Mina kunder** för att du ska kunna publicera dem på Azure Lighthouse.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanterings upplevelser mellan flera innehavare](cross-tenant-management-experience.md).
- Lär dig att [publicera en prenumeration på Azure Lighthouse](../how-to/onboard-customer.md).
- Lär dig mer om [Cloud Solution Provider-programmet](/partner-center/csp-overview).
