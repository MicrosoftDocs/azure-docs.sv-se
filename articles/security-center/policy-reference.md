---
title: Inbyggda princip definitioner för Azure Security Center
description: Visar Azure Policy inbyggda princip definitioner för Azure Security Center. Dessa inbyggda princip definitioner tillhandahåller vanliga metoder för att hantera dina Azure-resurser.
ms.date: 03/17/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: d6afafef2bb09855fb50f67fcc1054043fa725ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596537"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Inbyggda definitioner för Azure Policy i Azure Security Center

Den här sidan är ett index över [Azure policy](../governance/policy/overview.md) inbyggda princip definitioner som är relaterade till Azure Security Center. Följande grupperingar av princip definitioner är tillgängliga:

- Gruppen [initiativ](#azure-security-center-initiatives) visar Azure policy initiativ definitioner i kategorin "Security Center".
- I gruppen [standard initiativ](#azure-security-center-initiatives) visas alla Azure policys definitioner som är en del av Security Centers standard initiativ, [Azure Security benchmark](../security/benchmarks/introduction.md). Den här Microsoft-baserade, mest respekterade benchmark-utbyggnaden av kontroller från [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) och [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) med fokus på molnbaserad säkerhet.
- [Kategori](#azure-security-center-category) gruppen visar alla Azure policys definitioner i kategorin "Security Center".

Mer information om säkerhets principer finns i [arbeta med säkerhets principer](./tutorial-security-policy.md). Ytterligare Azure Policy inbyggda program för andra tjänster finns i [Azure policy inbyggda definitioner](../governance/policy/samples/built-in-policies.md).

Namnet på varje inbyggd princip definition länkar till princip definitionen i Azure Portal. Använd länken i kolumnen **version** om du vill visa källan på [Azure policy GitHub-lagrings platsen](https://github.com/Azure/azure-policy).

## <a name="azure-security-center-initiatives"></a>Azure Security Center initiativ

Mer information om de inbyggda initiativ som övervakas av Security Center finns i följande tabell:

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="security-centers-default-initiative-azure-security-benchmark"></a>Security Center s standard initiativ (Azure Security benchmark)

Mer information om de inbyggda principer som övervakas av Security Center finns i följande tabell:

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Azure Security Center kategori

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig mer om Azure Policy säkerhets princips definitioner i Security Center. Om du vill veta mer om initiativ, principer och hur de relaterar till Security Centers rekommendationer, se [Vad är säkerhets principer, initiativ och rekommendationer?](security-policy-concept.md).
