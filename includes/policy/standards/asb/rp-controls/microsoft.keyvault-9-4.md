---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8a6d26eb0fb795f0ca047ff4a976a5b31b9493ee
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512646"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Rensningsskydd ska vara aktiverat för nyckelvalv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Skadlig borttagning av ett nyckelvalv kan leda till permanent dataförlust. En illvillig insider i din organisation kan potentiellt ta bort och rensa nyckelvalv. Rensningsskydd skyddar dig mot insiderattacker genom att framtvinga en obligatorisk kvarhållningsperiod för mjukt borttagna nyckelvalv. Ingen i din organisation eller Microsoft kommer att kunna rensa dina nyckelvalv under kvarhållningsperioden för mjuk borttagning. |Granska, Neka, Inaktiverad |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
