---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b830d3d9143bb5b15292d1c0b14b04d8fee9b2ba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588349"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure File Sync ska använda privat länk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |Genom att skapa en privat slut punkt för den angivna resursen för synkroniseringstjänsten för lagring kan du adressera tjänsten Storage Sync service från det privata IP-adressutrymmet i din organisations nätverk, i stället för via Internet-tillgänglig offentlig slut punkt. Den offentliga slut punkten inaktive ras inte när du skapar en privat slut punkt. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Konfigurera Azure File Sync med privata slut punkter](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb35dddd9-daf7-423b-8375-5a5b86806d5a) |En privat slut punkt distribueras för den angivna resursen för Storage Sync-tjänsten. På så sätt kan du adressera tjänsten Storage Sync service från det privata IP-adressutrymmet i din organisations nätverk, i stället för via den offentliga Internet-slutpunkt som kan nås. Om det finns en eller flera privata slut punkter i sig inaktive ras inte den offentliga slut punkten. |DeployIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_DeployIfNotExists.json) |
|[Ändra-konfigurera Azure File Sync för att inaktivera offentlig nätverks åtkomst](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e07b2e9-6cd9-4c40-9ccb-52817b95133b) |Den Azure File Syncde offentliga slut punkten med Internet-åtkomst har inaktiverats av din organisations princip. Du kan fortfarande komma åt tjänsten för synkronisering av lagring via dess privata slut punkter. |Ändra, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_Modify.json) |
|[Åtkomst till offentligt nätverk ska inaktive ras för Azure File Sync](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21a8cd35-125e-4d13-b82d-2e19b7208bb7) |Genom att inaktivera den offentliga slut punkten kan du begränsa åtkomsten till resursen för Storage Sync-tjänsten till förfrågningar som är avsedda för godkända privata slut punkter i organisationens nätverk. Det finns inget som är osäkert med att tillåta begär anden till den offentliga slut punkten, men du kanske vill inaktivera den för att uppfylla regler för regler, juridik eller organisations principer. Du kan inaktivera den offentliga slut punkten för en synkroniseringstjänst för lagring genom att ange incomingTrafficPolicy för resursen till AllowVirtualNetworksOnly. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_AuditDeny.json) |
