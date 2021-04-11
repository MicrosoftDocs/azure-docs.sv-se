---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c386cef450666072eab5ed6bec7394fc0e0a67cf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105032316"
---
|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Granska Azure våren Cloud-instanser där distribuerad spårning inte är aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f2d8593-4667-4932-acca-6a9f187af109) |Med distribuerade spårnings verktyg i Azure våren Cloud kan du felsöka och övervaka komplexa sammanlänkningar mellan mikrotjänster i ett program. Distribuerade spårnings verktyg bör vara aktiverade och i ett felfritt tillstånd. |Granskning, inaktive rad |[1.0.0 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_DistributedTracing_Audit.json) |
|[Azure våren Cloud ska använda nätverks inmatning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Azure våren Cloud-instanser bör använda virtuell nätverks inmatning i följande syfte: 1. Isolera Azure våren Cloud från Internet. 2. Aktivera Azure våren Cloud för att interagera med system i antingen lokala data Center eller Azure-tjänsten i andra virtuella nätverk. 3. Ger kunderna möjlighet att styra inkommande och utgående nätverkskommunikation för Azure våren Cloud. |Granskning, inaktive rad, neka |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
