---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 718f239e8e323e3e46545433840ac18a0a723557
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499058"
---
|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Tillåtna platser](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe56962a6-4747-49cd-b67b-bf8b01975c4c) |Med den här principen kan du begränsa vilka platser som organisationen kan ange när den distribuerar resurser. Den används för att genomdriva kraven på geo-efterlevnad. Resursgrupper, Microsoft.AzureActiveDirectory/b2cDirectories och resurser som använder regionen ”global” undantas. |Förneka |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedLocations_Deny.json) |
|[Tillåtna platser för resursgrupper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe765b5de-1225-4ba3-bd56-1ac6695af988) |Med den här principen kan du begränsa de platser som din organisation kan skapa resursgrupper i. Den används för att genomdriva kraven på geo-efterlevnad. |Förneka |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json) |
|[Tillåtna resurstyper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa08ec900-254a-4555-9bf5-e42af04b5c5c) |Med den här principen kan du ange de resurstyper som din organisation kan distribuera. Endast resurstyper som stöder "taggar" och "plats" påverkas av den här principen. Om du vill begränsa alla resurser duplicerar du den här principen och ändrar läget till Alla. |Förneka |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedResourceTypes_Deny.json) |
|[Granska resursplatsmatchning av resursgruppsplats](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a914e76-4921-4c19-b460-a2d36003525a) |Granska att resursplatsen matchar resursgruppens plats |Revision |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourcesInResourceGroupLocation_Audit.json) |
|[Granska användningen av anpassade RBAC-regler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Granska inbyggda roller som "Ägare, Bidragsägare, Läsare" i stället för anpassade RBAC-roller, som är felbenägna. Användning av anpassade roller behandlas som ett undantag och kräver en rigorös granskning och hotmodellering |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Anpassade prenumerationsägare bör inte finnas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Den här principen säkerställer att det inte finns några ägarroller för anpassade prenumerationer. |Granska, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |
|[Otillåtna resurstyper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6c112d4e-5bc7-47ae-a041-ea2d9dccd749) |Begränsa vilka resurstyper som kan distribueras i din miljö. Begränsning av resurstyper kan minska komplexiteten och angreppsytan i din miljö och samtidigt hjälpa till att hantera kostnader. Kompatibilitetsresultat visas endast för icke-kompatibla resurser. |Granska, Neka, Inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/InvalidResourceTypes_Deny.json) |
