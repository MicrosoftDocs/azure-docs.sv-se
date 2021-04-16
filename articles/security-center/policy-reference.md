---
title: Inbyggda principdefinitioner för Azure Security Center
description: Visar Azure Policy inbyggda principdefinitioner för Azure Security Center. Dessa inbyggda principdefinitioner tillhandahåller vanliga metoder för att hantera dina Azure-resurser.
ms.date: 04/14/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: 34a839eb6996f2b37c07bb2e54f81a1d21044fa9
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502682"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Inbyggda definitioner för Azure Policy i Azure Security Center

Den här sidan är ett index [Azure Policy](../governance/policy/overview.md) inbyggda principdefinitioner relaterade till Azure Security Center. Följande grupp av principdefinitioner är tillgängliga:

- [Initiativgruppen](#azure-security-center-initiatives) listar de Azure Policy initiativdefinitionerna i kategorin "Security Center".
- [Standardinitiativgruppen](#azure-security-center-initiatives) listar alla Azure Policy definitioner som ingår i Security Center standardinitiativ, [Azure Security Benchmark.](../security/benchmarks/introduction.md) Det här microsoftskapade, allmänt respekterade benchmark-måttet bygger på kontroller från [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) och National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) med fokus på molncentrerad säkerhet.
- [Kategorigruppen](#azure-security-center-category) listar alla Azure Policy definitioner i kategorin "Security Center".

Mer information om säkerhetsprinciper finns i [Arbeta med säkerhetsprinciper.](./tutorial-security-policy.md) Mer information Azure Policy inbyggda program för andra tjänster finns [i Azure Policy inbyggda definitioner](../governance/policy/samples/built-in-policies.md).

Namnet på varje inbyggd principdefinition länkar till principdefinitionen i Azure Portal. Använd länken i kolumnen **Version för** att visa källan på Azure Policy [GitHub-lagringsplatsen](https://github.com/Azure/azure-policy).

## <a name="azure-security-center-initiatives"></a>Azure Security Center initiativ

Mer information om inbyggda initiativ som övervakas av Security Center finns i följande tabell:

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="security-centers-default-initiative-azure-security-benchmark"></a>Security Center standardinitiativ (Azure Security Benchmark)

Mer information om inbyggda principer som övervakas av Security Center finns i följande tabell:

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Azure Security Center kategori

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig Azure Policy om säkerhetsprincipdefinitioner i Security Center. Mer information om initiativ, principer och hur de relaterar till Security Center rekommendationer finns i Vad är [säkerhetsprinciper, initiativ och rekommendationer?](security-policy-concept.md).
