---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 83f33626927d88ef9de8a18da5e6f66839704e08
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102429424"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Stack gräns enheter bör använda Double-Encryption](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |För att skydda data i vila på enheten, se till att den är dubbelt krypterad, åtkomsten till data styrs och när enheten har inaktiverats tas data bort på ett säkert sätt av data diskarna. Dubbel kryptering är användningen av två krypterings lager: BitLocker XTS-AES 256-bitars kryptering på data volymer och inbyggd kryptering av hård diskarna. Läs mer i dokumentationen om säkerhets översikt för den aktuella stack Edge-enheten. |granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
