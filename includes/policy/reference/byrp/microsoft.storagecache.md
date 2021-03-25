---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a8a9edb92ac01d49c521e770adde8f7bb04d8872
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105031669"
---
|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[HPC-cacheposter bör använda kundhanterad nyckel för kryptering](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |Hantera kryptering i resten av Azure HPC cache med Kundhanterade nycklar. Som standard krypteras kund information med tjänst hanterade nycklar, men Kundhanterade nycklar krävs ofta för att uppfylla gällande regler för efterlevnad. Kundhanterade nycklar gör det möjligt att kryptera data med en Azure Key Vault-nyckel som skapats och ägs av dig. Du har fullständig kontroll och ansvar för nyckel livs cykeln, inklusive rotation och hantering. |Granskning, inaktive rad, neka |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
