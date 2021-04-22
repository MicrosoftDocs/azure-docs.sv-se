---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7839d6be9eab20253c1518a550bcb2b6ebd3dcc4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867057"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Data Box ska aktivera dubbel kryptering för vilodata på enheten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |Aktivera ett andra lager av programvarubaserad kryptering för vilodata på enheten. Enheten är redan skyddad via Advanced Encryption Standard 256-bitars kryptering för vilodata. Det här alternativet lägger till ett andra lager med datakryptering. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[Azure Data Box ska använda en kund hanterad nyckel för att kryptera enhetens upplåsningslösenord](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |Använd en kund hanterad nyckel för att styra krypteringen av enhetens upplåsningslösenord för Azure Data Box. Kundhanterade nycklar hjälper också till att hantera åtkomsten till enhetens upplåsningslösenord via Data Box-enhet tjänsten för att förbereda enheten och kopiera data på ett automatiserat sätt. Data på själva enheten är redan krypterade i vila med Advanced Encryption Standard 256-bitars kryptering och lösenordet för upplåsning av enheten krypteras som standard med en hanterad Microsoft-nyckel. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
