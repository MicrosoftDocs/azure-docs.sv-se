---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1913d07957adf71761e6fe65cb5bc9247f44219d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867009"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Stack Edge enheter ska använda dubbel kryptering](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |För att skydda vilodata på enheten ska du se till att de är dubbelkrypterade, att åtkomsten till data styrs och att data raderas på ett säkert sätt från datadiskarna när enheten har inaktiverats. Dubbel kryptering är användningen av två krypteringslager: BitLocker XTS-AES 256-bitars kryptering på datavolymerna och inbyggd kryptering av hårddiskarna. Läs mer i dokumentationen för säkerhetsöversikten för den specifika Stack Edge-enheten. |audit, deny, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
