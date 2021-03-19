---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f342b9f284419d2a582fb69f5f06a0dc46d9c44a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606872"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Container grupp för Azure Container Instance ska distribueras till ett virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8af8f826-edcb-4178-b35f-851ea6fea615) |Säker kommunikation mellan dina behållare med virtuella Azure-nätverk. När du anger ett virtuellt nätverk kan resurser i det virtuella nätverket på ett säkert och privat sätt kommunicera med varandra. |Granskning, inaktive rad, neka |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_VNET_Audit.json) |
|[Container grupp för Azure Container Instance ska använda kundhanterad nyckel för kryptering](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0aa61e00-0a01-4a3c-9945-e93cffedf0e6) |Skydda dina behållare med större flexibilitet med Kundhanterade nycklar. När du anger en kundhanterad nyckel används nyckeln för att skydda och kontrollera åtkomsten till nyckeln som krypterar dina data. Att använda Kundhanterade nycklar ger ytterligare funktioner för att kontrol lera rotationen av nyckel krypterings nyckeln eller att rensa data kryptografiskt. |Granskning, inaktive rad, neka |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_CMK_Audit.json) |
