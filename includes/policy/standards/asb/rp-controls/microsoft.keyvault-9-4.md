---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f3d63180219caa99fed22a5f8249de7e8f1a3eea
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097122"
---
|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rensnings skydd måste vara aktiverat för nyckel valv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Skadlig borttagning av ett nyckel valv kan leda till permanent data förlust. En skadlig Insider i din organisation kan potentiellt ta bort och rensa nyckel valv. Rensnings skyddet skyddar dig från insider attacker genom att tvinga fram en obligatorisk kvarhållningsperiod för mjuka borttagna nyckel valv. Ingen i din organisation eller Microsoft kommer att kunna rensa nyckel valven under den mjuka borttagnings perioden för kvarhållning. |Granska, neka, inaktive rad |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
