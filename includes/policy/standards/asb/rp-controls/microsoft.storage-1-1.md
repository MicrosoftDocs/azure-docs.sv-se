---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5d0f73d4991f4f14460e569cfcae29722e77cd1e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107881113"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Lagringskonton bör begränsa nätverksåtkomsten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Nätverksåtkomsten till lagringskonton bör begränsas. Konfigurera nätverksregler så att endast program från tillåtna nätverk kan komma åt lagringskontot. För att tillåta anslutningar från specifika Internetklienter eller lokala klienter kan åtkomst beviljas för trafik från specifika virtuella Azure-nätverk eller till offentliga IP-adressintervall på Internet |Granska, Neka, Inaktiverad |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[Lagringskonton bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Den här principen granskar alla lagringskonto som inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
