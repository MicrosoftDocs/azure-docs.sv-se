---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0478e606cd5eb6652b992b647d5f69b347753341
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504313"
---
|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure File Sync bör använda private link](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |Genom att skapa en privat slutpunkt för den angivna storage sync Service-resursen kan du adressera resursen för synkroniseringstjänsten för lagring inifrån det privata IP-adressutrymmet i organisationens nätverk i stället för via den offentliga slutpunkten som är tillgänglig via Internet. Att skapa en privat slutpunkt på egen hand inaktiverar inte den offentliga slutpunkten. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Konfigurera Azure File Sync med privata slutpunkter](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb35dddd9-daf7-423b-8375-5a5b86806d5a) |En privat slutpunkt distribueras för den angivna resursen för tjänsten för synkronisering av lagring. På så sätt kan du adressera resursen för tjänsten för synkronisering av lagring inifrån det privata IP-adressutrymmet i organisationens nätverk, i stället för via den offentliga slutpunkten som är tillgänglig via Internet. Förekomsten av en eller flera privata slutpunkter i sig inaktiverar inte den offentliga slutpunkten. |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_DeployIfNotExists.json) |
|[Ändra – Konfigurera Azure File Sync inaktivera offentlig nätverksåtkomst](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e07b2e9-6cd9-4c40-9ccb-52817b95133b) |Den Azure File Sync internettillgängliga offentliga slutpunkten inaktiveras av din organisations princip. Du kan fortfarande komma åt tjänsten för synkronisering av lagring via dess privata slutpunkter. |Ändra, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_Modify.json) |
|[Offentlig nätverksåtkomst ska inaktiveras för Azure File Sync](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21a8cd35-125e-4d13-b82d-2e19b7208bb7) |Om du inaktiverar den offentliga slutpunkten kan du begränsa åtkomsten till resursen för tjänsten för synkronisering av lagring till begäranden som är avsedda för godkända privata slutpunkter i organisationens nätverk. Det finns inget som i sig är osäkert när det gäller att tillåta begäranden till den offentliga slutpunkten, men du kanske vill inaktivera den för att uppfylla regelkrav, juridiska krav eller organisationskrav. Du kan inaktivera den offentliga slutpunkten för en tjänst för synkronisering av lagring genom att ange incomingTrafficPolicy för resursen till AllowVirtualNetworksOnly. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_AuditDeny.json) |
