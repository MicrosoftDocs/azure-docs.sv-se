---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f14136e77bb0018ea5e15f6e07fa7a633addf49f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876032"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[HPC Cache bör använda kund hanterad nyckel för kryptering](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |Hantera kryptering i vila Azure HPC Cache med kundhanterade nycklar. Som standard krypteras kunddata med tjänst hanterade nycklar, men kund hanterade nycklar krävs ofta för att uppfylla regelefterlevnadsstandarder. Kund hanterade nycklar gör att data kan krypteras med en Azure Key Vault nyckel som skapas och ägs av dig. Du har fullständig kontroll och ansvar för nyckellivscykeln, inklusive rotation och hantering. |Granska, inaktiverad, neka |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
