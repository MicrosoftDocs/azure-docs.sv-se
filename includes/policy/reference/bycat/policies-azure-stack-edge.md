---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c68188ec1ee0c0e3a8d174bcb8c19d8a450c22dc
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498919"
---
|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Stack Edge enheter ska använda dubbelkryptering](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |För att skydda vilodata på enheten måste du kontrollera att de är dubbelkrypterade, att åtkomsten till data styrs och att data raderas på ett säkert sätt från datadiskarna när enheten har inaktiverats. Dubbel kryptering är användningen av två krypteringslager: BitLocker XTS-AES 256-bitars kryptering på datavolymerna och inbyggd kryptering av hårddiskarna. Läs mer i dokumentationen för säkerhetsöversikten för den specifika Stack Edge-enheten. |audit, deny, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
