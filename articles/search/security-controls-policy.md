---
title: Azure Policy för regelefterlevnad för Azure Cognitive Search
description: Visar Azure Policy för regelefterlevnad som är tillgängliga för Azure Cognitive Search. Dessa inbyggda principdefinitioner ger vanliga metoder för att hantera kompatibiliteten för dina Azure-resurser.
ms.date: 04/21/2021
ms.topic: sample
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: a8f452703826a68fde8c1b931ab8be7cbeeca6f1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861956"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-cognitive-search"></a>Azure Policy för regelefterlevnad för Azure Cognitive Search

Om du använder [Azure Policy](../governance/policy/overview.md) för att genomdriva rekommendationerna i [Azure Security Benchmark](../security/benchmarks/introduction.md)vet du förmodligen redan att du kan skapa principer för att identifiera och åtgärda icke-kompatibla tjänster. Dessa principer kan vara anpassade eller baseras på inbyggda definitioner som tillhandahåller efterlevnadskriterier och lämpliga lösningar för välförstdda metodtips.

För Azure Cognitive Search finns det för närvarande en inbyggd definition, som anges nedan, som du kan använda i en principtilldelning. Den inbyggda är för loggning och övervakning. Genom att använda den här inbyggda definitionen i en princip som du skapar [söker](../governance/policy/assign-policy-portal.md)systemet efter söktjänster som inte har diagnostisk loggning [och](search-monitor-logs.md)aktiverar det sedan.

[Regelefterlevnad i Azure Policy](../governance/policy/concepts/regulatory-compliance.md) tillhandahåller Microsoft-skapade och hanterade initiativdefinitioner,  så kallade  inbyggda , för _efterlevnadsdomäner_ och säkerhetskontroller relaterade till olika efterlevnadsstandarder. På den här sidan visas **efterlevnadsdomäner** **och säkerhetskontroller** för Azure Cognitive Search. Du kan tilldela de inbyggda för en säkerhetskontroll **individuellt för** att göra dina Azure-resurser kompatibla med den specifika standarden.

[!INCLUDE [azure-policy-compliancecontrols-introwarning](../../includes/policy/standards/intro-warning.md)]

[!INCLUDE [azure-policy-compliancecontrols-search](../../includes/policy/standards/byrp/microsoft.search.md)]

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Policy regelefterlevnad.](../governance/policy/concepts/regulatory-compliance.md)
- Se de inbyggda programmen på [Azure Policy GitHub-lagringsplatsen](https://github.com/Azure/azure-policy).
