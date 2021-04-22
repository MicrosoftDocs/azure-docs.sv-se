---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 78694874819f8a90f0ab318fb96cda6b8816a689
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880080"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Rensningsskydd ska vara aktiverat för nyckelvalv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Skadlig borttagning av ett nyckelvalv kan leda till permanent dataförlust. En illvillig insider i din organisation kan potentiellt ta bort och rensa nyckelvalv. Rensningsskydd skyddar dig mot insiderattacker genom att framtvinga en obligatorisk kvarhållningsperiod för mjukt borttagna nyckelvalv. Ingen i din organisation eller Microsoft kommer att kunna rensa dina nyckelvalv under kvarhållningsperioden för mjuk borttagning. |Granska, Neka, Inaktiverad |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
