---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d72d4493b39d959c5429ed2ca9f5883831b9372a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867021"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Container Instance-containergruppen ska distribueras till ett virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8af8f826-edcb-4178-b35f-851ea6fea615) |Säker kommunikation mellan dina containrar med Azure Virtual Networks. När du anger ett virtuellt nätverk kan resurser i det virtuella nätverket kommunicera säkert och privat med varandra. |Granska, Inaktiverad, Neka |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_VNET_Audit.json) |
|[Azure Container Instance-containergruppen bör använda kund hanterad nyckel för kryptering](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0aa61e00-0a01-4a3c-9945-e93cffedf0e6) |Skydda dina containrar med större flexibilitet med hjälp av kund hanterade nycklar. När du anger en kundhanterad nyckel används nyckeln för att skydda och kontrollera åtkomsten till nyckeln som krypterar dina data. Användning av kund hanterade nycklar ger ytterligare funktioner för att styra rotation av nyckelkrypteringsnyckeln eller kryptografiskt radera data. |Granska, Inaktiverad, Neka |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_CMK_Audit.json) |
