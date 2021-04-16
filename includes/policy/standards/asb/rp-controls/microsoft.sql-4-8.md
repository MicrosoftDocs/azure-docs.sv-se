---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c5e2f35e91b6c5f4f976ab9d941cc328fb42be24
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107510672"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL-hanterade instanser bör använda kund hanterade nycklar för att kryptera vilodata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Genom transparent datakryptering (TDE) med din egen nyckel får du ökad transparens och kontroll över TDE-skyddet, ökad säkerhet med en extern tjänst som stöds av HSM och befordran av uppdelning av uppgifter. Den här rekommendationen gäller för organisationer med ett relaterat efterlevnadskrav. |AuditIfNotExists, inaktiverad |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL-servrar bör använda kund hanterade nycklar för att kryptera vilodata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Implementering transparent datakryptering (TDE) med din egen nyckel ger ökad transparens och kontroll över TDE-skyddet, ökad säkerhet med en HSM-backad extern tjänst och befordran av uppdelning av uppgifter. Den här rekommendationen gäller för organisationer med ett relaterat efterlevnadskrav. |AuditIfNotExists, inaktiverad |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[transparent datakryptering på SQL-databaser ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Transparent datakryptering ska aktiveras för att skydda vilodata och uppfylla efterlevnadskrav |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |
