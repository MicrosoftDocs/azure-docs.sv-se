---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: fa009c08d57ddbe24e2a2ed1aba645a33096f815
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807350"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Datautforskaren kryptering i vila bör använda en kundhanterad nyckel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |Att aktivera kryptering i vila med en kundhanterad nyckel på ditt Azure Datautforskaren-kluster ger ytterligare kontroll över nyckeln som används av krypteringen i vila. Den här funktionen är ofta som gäller för kunder med särskilda krav på efterlevnad och kräver en Key Vault för att hantera nycklarna. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[Disk kryptering bör vara aktiverat på Azure Datautforskaren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |Genom att aktivera disk kryptering kan du skydda och skydda dina data så att de uppfyller organisationens säkerhets-och efterlevnads åtaganden. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[Double Encryption måste vara aktiverat på Azure Datautforskaren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |Genom att aktivera Double Encryption kan du skydda och skydda dina data så att de uppfyller organisationens säkerhets-och efterlevnads åtaganden. När Double Encryption har Aktiver ATS krypteras data i lagrings kontot två gånger, en gång på tjänst nivå och en gång på infrastruktur nivå, med hjälp av två olika krypteringsalgoritmer och två olika nycklar. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[Inmatning av virtuella nätverk ska vara aktiverat för Azure Datautforskaren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ad2fd1f-b25f-47a2-aa01-1a5a779e6413) |Skydda nätverks omkretsen med en virtuell nätverks inmatning som gör att du kan tillämpa regler för nätverks säkerhets grupper, ansluta lokalt och skydda dina data anslutnings källor med tjänst slut punkter. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_VNET_configured.json) |
