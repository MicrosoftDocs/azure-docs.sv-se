---
title: Inbyggda princip definitioner för Azure DDoS Protection standard
description: Visar Azure Policy inbyggda princip definitioner för Azure DDoS Protection standard. Dessa inbyggda princip definitioner tillhandahåller vanliga metoder för att hantera dina Azure-resurser.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2021
ms.author: yitoh
ms.custom: subject-policy-reference
ms.topic: include
ms.openlocfilehash: 6a0496740fbd82090ba7199ca3e064f5bd2bf7c5
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108396"
---
# <a name="azure-policy-built-in-definitions-for-azure-ddos-protection-standard"></a>Azure Policy inbyggda definitioner för Azure DDoS Protection standard

Den här sidan är ett index över [Azure policy](../governance/policy/overview.md) inbyggda princip definitioner för Azure DDoS Protection standard. Ytterligare Azure Policy inbyggda program för andra tjänster finns i [Azure policy inbyggda definitioner](../governance/policy/samples/built-in-policies.md).

Namnet på varje inbyggd princip definition länkar till princip definitionen i Azure Portal. Använd länken i kolumnen **version** om du vill visa källan på [Azure policy GitHub-lagrings platsen](https://github.com/Azure/azure-policy).

## <a name="azure-ddos-protection-standard"></a>Azure DDoS Protection Standard

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Virtuella nätverk bör skyddas med Azure DDoS Protection standard](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94de2ad3-e0c1-4caf-ad78-5d47bbc83d3d)|Skydda dina virtuella nätverk mot volym-och protokoll attacker med Azure DDoS Protection standard. Mer information finns på [https://aka.ms/ddosprotectiondocs](https://aka.ms/ddosprotectiondocs) .|Ändra, granska, inaktive rad|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkDdosStandard_Audit.json)|
|[De offentliga IP-adresserna ska ha resurs loggar aktiverade för Azure DDoS Protection standard](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F752154a7-1e0f-45c6-a880-ac75a7e4f648)|Aktivera resurs loggar för offentliga IP-adresser i diagnostikinställningar för att strömma till en Log Analytics-arbetsyta. Få detaljerad insyn i attack trafik och åtgärder som vidtas för att minimera DDoS-attacker via aviseringar, rapporter och flödes loggar.|AuditIfNotExists, DeployIfNotExists, inaktiverat|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/PublicIpDdosLogging_Audit.json)|


## <a name="next-steps"></a>Nästa steg

- Se de inbyggda programmen på [Azure Policy GitHub-lagringsplatsen](https://github.com/Azure/azure-policy).
- Granska [Azure Policy-definitionsstrukturen](../governance/policy/concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../governance/policy/concepts/effects.md).