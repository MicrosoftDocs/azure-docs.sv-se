---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: bd25b94507dd6f124bb03bfb0fccc30adfab90e0
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091267"
---
|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Data Box jobb bör aktivera Double kryptering för data i vila på enheten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |Aktivera ett andra lager av programvarubaserad kryptering för data i vila på enheten. Enheten skyddas redan via Advanced Encryption Standard 256-bitars kryptering för vilande data. Det här alternativet lägger till ett andra lager med data kryptering. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[Azure Data Box jobb ska använda en kundhanterad nyckel för att kryptera enhetens upplåsnings lösen ord](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |Använd en kundhanterad nyckel för att kontrol lera krypteringen av enhetens upplåsnings lösen ord för Azure Data Box. Kundhanterade nycklar hjälper också till att hantera åtkomst till enhetens upplåsnings lösen ord av Data Box-enhet-tjänsten för att förbereda enheten och kopiera data på ett automatiserat sätt. Data på själva enheten är redan krypterade i vila med Advanced Encryption Standard 256-bitars kryptering, och lösen ordet för enhets upplåsning krypteras som standard med en Microsoft-hanterad nyckel. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
