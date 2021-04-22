---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c0a77896099bac5d4b3d819cfbd17ed339cb1b72
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867093"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure HDInsight ska matas in i ett virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0ab5b05-1c98-40f7-bb9e-dc568e41b501) |Genom att Azure HDInsight kluster i ett virtuellt nätverk låses avancerade nätverks- och säkerhetsfunktioner i HDInsight upp och du får kontroll över nätverkssäkerhetskonfigurationen. |Granska, Inaktiverad, Neka |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_VNETInjection_Audit.json) |
|[Azure HDInsight ska använda kund hanterade nycklar för att kryptera vilodata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F64d314f6-6062-4780-a861-c23e8951bee5) |Använd kundhanterade nycklar för att hantera krypteringen i vila Azure HDInsight kluster. Som standard krypteras kunddata med tjänst hanterade nycklar, men kund hanterade nycklar krävs ofta för att uppfylla regelefterlevnadsstandarder. Kund hanterade nycklar gör att data kan krypteras med en Azure Key Vault som skapats och ägs av dig. Du har fullständig kontroll och ansvar för nyckellivscykeln, inklusive rotation och hantering. Läs mer på [https://aka.ms/hdi.cmk](https://aka.ms/hdi.cmk) . |Granska, Neka, Inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_CMK_Audit.json) |
|[Azure HDInsight ska använda kryptering på värden för att kryptera vilodata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fd32ebd-e4c3-4e13-a54a-d7422d4d95f6) |Genom att aktivera kryptering på värden kan du skydda dina data för att uppfylla organisationens säkerhets- och efterlevnadsåtaganden. När du aktiverar kryptering på värden krypteras data som lagras på den virtuella datorvärden i vila och flöden krypteras till lagringstjänsten. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionAtHost_Audit.json) |
|[Azure HDInsight ska använda kryptering under överföring för att kryptera kommunikationen mellan Azure HDInsight klusternoder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |Data kan manipuleras under överföringen mellan Azure HDInsight klusternoder. Aktivering av kryptering under överföring löser problem med missbruk och manipulering under överföringen. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
