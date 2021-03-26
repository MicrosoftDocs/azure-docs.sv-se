---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 2324e8d2689272b2548188ade0c8eca8d1d680e5
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105033535"
---
|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Bot service-slutpunkten måste vara en giltig HTTPS-URI](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Data kan manipuleras under överföringen. Det finns protokoll som tillhandahåller kryptering för att hantera problem med missbruk och manipulering. För att säkerställa att dina robotar endast kommunicerar via krypterade kanaler, ställer du in slut punkten på en giltig HTTPS-URI. Detta säkerställer att HTTPS-protokollet används för att kryptera dina data under överföringen och är också ofta ett krav på efterlevnad av regler eller bransch standarder. Besök: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines) . |granska, neka, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_ValidEndpoint_Audit.json) |
|[Bot-tjänsten ska vara krypterad med en kundhanterad nyckel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |Azure Bot Service krypterar automatiskt din resurs för att skydda dina data och uppfylla organisationens säkerhets-och efterlevnads åtaganden. Som standard används Microsoft-hanterade krypterings nycklar. För större flexibilitet vid hantering av nycklar eller för att kontrol lera åtkomst till din prenumeration väljer du Kundhanterade nycklar, som även kallas för att ta med din egen nyckel (BYOK). Läs mer om Azure Bot Service kryptering: [https://docs.microsoft.com/azure/bot-service/bot-service-encryption](https://docs.microsoft.com/azure/bot-service/bot-service-encryption) . |granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |
