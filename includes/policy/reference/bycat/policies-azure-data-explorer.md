---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: db08f551331eaa9ed37368a36ce266efc38947ab
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498865"
---
|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Data Explorer kryptering i vila bör använda en kund hanterad nyckel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |Aktivering av kryptering i vila med hjälp av en kund hanterad nyckel på ditt Azure Data Explorer kluster ger ytterligare kontroll över nyckeln som används av krypteringen i vila. Den här funktionen är ofta tillämplig för kunder med särskilda efterlevnadskrav och kräver en Key Vault för att hantera nycklarna. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[Diskkryptering ska vara aktiverat på Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |Aktivering av diskkryptering hjälper till att skydda dina data så att de uppfyller organisationens säkerhets- och efterlevnadsåtaganden. |Granska, Neka, Inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[Dubbel kryptering ska aktiveras på Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |Aktivering av dubbel kryptering hjälper till att skydda dina data så att de uppfyller organisationens säkerhets- och efterlevnadsåtaganden. När dubbel kryptering har aktiverats krypteras data i lagringskontot två gånger, en gång på tjänstnivå och en gång på infrastrukturnivå, med hjälp av två olika krypteringsalgoritmer och två olika nycklar. |Granska, Neka, Inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[Virtuell nätverksinjektion ska aktiveras för Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ad2fd1f-b25f-47a2-aa01-1a5a779e6413) |Skydda din nätverks perimeter med virtuell nätverksinjektion som gör att du kan tillämpa regler för nätverkssäkerhetsgrupp, ansluta lokalt och skydda dina dataanslutningskällor med tjänstslutpunkter. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_VNET_configured.json) |
