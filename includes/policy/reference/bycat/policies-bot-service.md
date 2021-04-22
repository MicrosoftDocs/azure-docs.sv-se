---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1d7df6224dcd6533772734060f4c1cf32e3c972f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866986"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Bot Service ska vara en giltig HTTPS-URI](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Data kan manipuleras under överföringen. Det finns protokoll som tillhandahåller kryptering för att hantera problem med missbruk och manipulering. För att säkerställa att dina robotar endast kommunicerar över krypterade kanaler anger du slutpunkten till en giltig HTTPS-URI. Detta säkerställer att HTTPS-protokollet används för att kryptera dina data under överföring och ofta är ett krav för efterlevnad av regel- eller branschstandarder. Besök: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines) . |audit, deny, disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_ValidEndpoint_Audit.json) |
|[Bot Service krypteras med en kund hanterad nyckel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |Azure Bot Service krypterar automatiskt din resurs för att skydda dina data och uppfylla organisationens säkerhets- och efterlevnadsåtaganden. Som standard används Microsoft-hanterade krypteringsnycklar. Om du vill ha större flexibilitet när det gäller att hantera nycklar eller styra åtkomsten till din prenumeration väljer du kund hanterade nycklar, även kallat BYOK (Bring Your Own Key). Läs mer om Azure Bot Service kryptering: [https://docs.microsoft.com/azure/bot-service/bot-service-encryption](https://docs.microsoft.com/azure/bot-service/bot-service-encryption) . |audit, deny, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |
