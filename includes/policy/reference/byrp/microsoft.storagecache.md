---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a9295290e8b0a26c407295c3b8f1ccb025159eac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504321"
---
|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[HPC Cache bör använda kund hanterad nyckel för kryptering](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |Hantera kryptering i vila Azure HPC Cache med kundhanterade nycklar. Som standard krypteras kunddata med tjänst hanterade nycklar, men kund hanterade nycklar krävs ofta för att uppfylla regelefterlevnadsstandarder. Kund hanterade nycklar gör att data kan krypteras med en Azure Key Vault nyckel som skapas och ägs av dig. Du har fullständig kontroll och ansvar för nyckellivscykeln, inklusive rotation och hantering. |Granska, inaktiverad, neka |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
