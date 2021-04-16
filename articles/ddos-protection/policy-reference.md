---
title: Inbyggda principdefinitioner för Azure DDoS Protection Standard
description: Visar Azure Policy inbyggda principdefinitioner för Azure DDoS Protection Standard. Dessa inbyggda principdefinitioner ger vanliga metoder för att hantera dina Azure-resurser.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2021
ms.author: yitoh
ms.custom: subject-policy-reference
ms.topic: include
ms.openlocfilehash: 86fa383fe2456eabe0cce142bd4a9c665fb95a2a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505708"
---
# <a name="azure-policy-built-in-definitions-for-azure-ddos-protection-standard"></a>Azure Policy inbyggda definitioner för Azure DDoS Protection Standard

Den här sidan är ett index [Azure Policy](../governance/policy/overview.md) inbyggda principdefinitioner för Azure DDoS Protection Standard. Ytterligare Azure Policy inbyggda funktioner för andra tjänster finns i [Azure Policy inbyggda definitionerna](../governance/policy/samples/built-in-policies.md).

Namnet på varje inbyggd principdefinition länkar till principdefinitionen i Azure Portal. Använd länken i kolumnen **Version för** att visa källan på Azure Policy [GitHub-lagringsplatsen](https://github.com/Azure/azure-policy).

## <a name="azure-ddos-protection-standard"></a>Azure DDoS Protection Standard

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Virtuella nätverk bör skyddas av Azure DDoS Protection Standard](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94de2ad3-e0c1-4caf-ad78-5d47bbc83d3d)|Skydda dina virtuella nätverk mot volym- och protokollattacker med Azure DDoS Protection Standard. Mer information finns i [https://aka.ms/ddosprotectiondocs](https://aka.ms/ddosprotectiondocs) .|Ändra, granska, inaktiverad|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkDdosStandard_Audit.json)|
|[Offentliga IP-adresser ska ha resursloggar aktiverade för Azure DDoS Protection Standard](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F752154a7-1e0f-45c6-a880-ac75a7e4f648)|Aktivera resursloggar för offentliga IP-adresser i diagnostikinställningarna för att strömma till en Log Analytics-arbetsyta. Få detaljerad insyn i attacktrafik och åtgärder som vidtas för att minimera DDoS-attacker via meddelanden, rapporter och flödesloggar.|AuditIfNotExists, DeployIfNotExists, Disabled|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/PublicIpDdosLogging_Audit.json)|


## <a name="next-steps"></a>Nästa steg

- Se de inbyggda programmen på [Azure Policy GitHub-lagringsplatsen](https://github.com/Azure/azure-policy).
- Granska [Azure Policy-definitionsstrukturen](../governance/policy/concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../governance/policy/concepts/effects.md).
