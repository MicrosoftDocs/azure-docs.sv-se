---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: daac33f853d0f482c5a259e8135ed905d3154d08
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867030"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Data Explorer kryptering i vila ska använda en kund hanterad nyckel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |Aktivering av kryptering i vila med hjälp av en kund hanterad nyckel på Azure Data Explorer kluster ger ytterligare kontroll över nyckeln som används av krypteringen i vila. Den här funktionen är ofta tillämplig för kunder med särskilda efterlevnadskrav och kräver en Key Vault för att hantera nycklarna. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[Diskkryptering ska aktiveras på Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |Genom att aktivera diskkryptering kan du skydda dina data så att de uppfyller organisationens säkerhets- och efterlevnadskrav. |Granska, Neka, Inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[Dubbel kryptering ska aktiveras på Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |Aktivering av dubbel kryptering hjälper till att skydda dina data för att uppfylla organisationens säkerhets- och efterlevnadsåtaganden. När dubbel kryptering har aktiverats krypteras data i lagringskontot två gånger, en gång på servicenivå och en gång på infrastrukturnivå, med hjälp av två olika krypteringsalgoritmer och två olika nycklar. |Granska, Neka, Inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[Virtuell nätverksinjicering ska aktiveras för Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ad2fd1f-b25f-47a2-aa01-1a5a779e6413) |Skydda din nätverks perimeter med virtuell nätverksinjicering som gör att du kan framtvinga regler för nätverkssäkerhetsgrupp, ansluta lokalt och skydda dina dataanslutningskällor med tjänstslutpunkter. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_VNET_configured.json) |
