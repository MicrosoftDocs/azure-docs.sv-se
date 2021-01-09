---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d276774ab21563f584d03056ce670a6d01398908
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98047511"
---
|Namn<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Bot service-slutpunkten måste vara en giltig HTTPS-URI](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Data kan manipuleras under överföringen. Det finns protokoll som tillhandahåller kryptering för att hantera problem med missbruk och manipulering. För att säkerställa att dina robotar endast kommunicerar via krypterade kanaler, ställer du in slut punkten på en giltig HTTPS-URI. Detta säkerställer att HTTPS-protokollet används för att kryptera dina data under överföringen och är också ofta ett krav på efterlevnad av regler eller bransch standarder. Besök: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines) . |granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Services/BotService_ValidEndpoint_Audit.json) |
