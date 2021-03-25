---
title: Azure Policy kontroll av efterlevnad för Azure Kognitiv sökning
description: Visar Azure Policy regler för regelefterlevnad som är tillgängliga för Azure Kognitiv sökning. Dessa inbyggda princip definitioner tillhandahåller vanliga metoder för att hantera kompatibiliteten för dina Azure-resurser.
ms.date: 03/24/2021
ms.topic: sample
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: af387d44caec333c2a320516d5c5714c803f0caa
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105033745"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-cognitive-search"></a>Azure Policy kontroll av efterlevnad för Azure Kognitiv sökning

Om du använder [Azure policy](../governance/policy/overview.md) för att genomdriva rekommendationerna i [Azure Security benchmark](../security/benchmarks/introduction.md)vet du förmodligen redan att du kan skapa principer för att identifiera och åtgärda icke-kompatibla tjänster. Dessa principer kan vara anpassade, eller så kan de baseras på inbyggda definitioner som innehåller villkor för efterlevnad och lämpliga lösningar för välförståde metod tips.

För Azure Kognitiv sökning finns det för närvarande en inbyggd definition, som visas nedan, som du kan använda i en princip tilldelning. Det inbyggda är för loggning och övervakning. Genom att använda den här inbyggda definitionen i en [princip som du skapar](../governance/policy/assign-policy-portal.md), söker systemet efter Sök tjänster som inte har [diagnostikloggning](search-monitor-logs.md)och aktiverar det därefter.

Regelefterlevnad som [följer Azure policy](../governance/policy/concepts/regulatory-compliance.md) tillhandahåller definitioner av Microsoft-skapade och hanterade initiativ, som kallas _inbyggda moduler_, för **domäner** och **säkerhets kontroller** som är relaterade till olika efterlevnads standarder. Den här sidan innehåller en lista över **domäner för efterlevnad** och **säkerhets kontroller** för Azure kognitiv sökning. Du kan tilldela de inbyggda programmen för en **säkerhets kontroll** individuellt för att hjälpa dina Azure-resurser att vara kompatibla med den aktuella standarden.

[!INCLUDE [azure-policy-compliancecontrols-introwarning](../../includes/policy/standards/intro-warning.md)]

[!INCLUDE [azure-policy-compliancecontrols-search](../../includes/policy/standards/byrp/microsoft.search.md)]

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att [Azure policy](../governance/policy/concepts/regulatory-compliance.md)regelefterlevnad.
- Se de inbyggda programmen på [Azure Policy GitHub-lagringsplatsen](https://github.com/Azure/azure-policy).
