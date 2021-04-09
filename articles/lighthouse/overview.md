---
title: Vad är Azure Lighthouse?
description: Med Azure Lighthouse kan tjänst leverantörer leverera hanterade tjänster för sina kunder med högre automatisering och effektivitet i stor skala.
ms.date: 12/16/2020
ms.topic: overview
ms.openlocfilehash: b9215f42e3ad3b7517d14fdaee710a31680ce453
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97617161"
---
# <a name="what-is-azure-lighthouse"></a>Vad är Azure Lighthouse?

Med Azure Lighthouse kan du hantera flera klientorganisationer, vilket ger högre automatisering, skalbarhet och förbättrad styrning av olika resurser och klientorganisationer.

Med Azure Lighthouse kan tjänst leverantörer leverera hanterade tjänster med hjälp av omfattande och robusta hanterings verktyg som är inbyggda i Azure-plattformen. Kunderna behåller kontrollen över vem som har åtkomst till sin klient, vilka resurser de kan komma åt och vilka åtgärder som kan vidtas. Det här erbjudandet kan också dra nytta av [företagets IT-organisationer](concepts/enterprise.md) och hantera resurser över flera klienter.

![Översikts diagram över Azure-Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Fördelar

Azure Lighthouse hjälper tjänst leverantörer att effektivt bygga och leverera hanterade tjänster. Fördelarna innefattar:

- **Hantering i skala**: kund engagemang och livs cykel åtgärder för att hantera kund resurser är enklare och mer skalbar. Befintliga API: er, hanterings verktyg och arbets flöden kan användas med delegerade resurser, inklusive datorer som är placerade utanför Azure, oavsett vilka regioner de finns i.
- **Bättre synlighet och kontroll för kunder**: kunderna har exakt kontroll över de omfattningar som de delegerar för hantering och de behörigheter som tillåts. De kan granska Service Provider-åtgärder och ta bort åtkomsten helt om du vill.
- **Omfattande och enhetlig plattforms verktyg**: vår verktygs upplevelse behandlar scenarier för nyckel tjänst leverantörer, inklusive flera licensierings modeller som EA, CSP och betala per användning. Azure Lighthouse fungerar med befintliga verktyg och API: er, licensierings modeller, [Azure-hanterade program](concepts/managed-applications.md)och partner program som [Cloud Solution Provider-program (CSP)](/partner-center/csp-overview). Du kan integrera Azure-Lighthouse i dina befintliga arbets flöden och program och spåra din påverkan på kund engagemang genom [att länka ditt partner-ID](./how-to/partner-earned-credit.md).

Det finns inga ytterligare kostnader för att använda Azure-Lighthouse för att hantera Azure-resurser. Alla Azure-kunder och-partner kan använda Azure-Lighthouse.

## <a name="capabilities"></a>Funktioner

Azure Lighthouse innehåller flera sätt att effektivisera engagemang och hantering:

- **Azure-delegerad resurs hantering**: [Hantera dina kunders Azure-resurser på ett säkert sätt i din klient](concepts/azure-delegated-resource-management.md), utan att behöva byta kontext-och kontroll plan. Kund prenumerationer och resurs grupper kan delegeras till angivna användare och roller i hanterings klienten, med möjlighet att ta bort åtkomst vid behov.
- **Nya Azure Portal upplevelser**: Visa information om flera innehavare på sidan [ **Mina kunder**](how-to/view-manage-customers.md) i Azure Portal. På sidan [ **tjänst leverantörer**](how-to/view-manage-service-providers.md) kan kunder se och hantera sin tjänst leverantörs åtkomst.
- **Azure Resource Manager mallar**: Använd arm-mallar för att [publicera delegerade kund resurser](how-to/onboard-customer.md) och [utföra hanterings uppgifter för flera innehavare](samples/index.md).
- **Hanterade tjänst erbjudanden i Azure Marketplace**: [erbjud dina tjänster till kunder](concepts/managed-services-offers.md) via privata eller offentliga erbjudanden och publicera dem automatiskt till Azure-Lighthouse.

> [!TIP]
> Ett liknande erbjudande, [Microsoft 365 Lighthouse](https://techcommunity.microsoft.com/t5/small-and-medium-business-blog/announcing-microsoft-365-lighthouse-for-managed-service/ba-p/1698181), hjälper IT-partner att registrera sig, övervaka och hantera sina Microsoft 365 kunder i stor skala. Microsoft 365 Lighthouse finns för närvarande i privat för hands version.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure-delegerad resurshantering](concepts/azure-delegated-resource-management.md).
- Utforska [hanterings upplevelser mellan flera innehavare](concepts/cross-tenant-management-experience.md).
- Se hur du [använder Azure Lighthouse i ett företag](concepts/enterprise.md).
- Visa information om [tillgänglighet](https://azure.microsoft.com/global-infrastructure/services/?products=azure-lighthouse&regions=all) och [FEDRAMP och DoD CC SRG](../azure-government/compliance/azure-services-in-fedramp-auditscope.md) för Azure Lighthouse.
