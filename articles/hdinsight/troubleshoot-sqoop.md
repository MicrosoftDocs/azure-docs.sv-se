---
title: Sqoop import/export-kommandot Miss lyckas för vissa användare i ESP-kluster – Azure HDInsight
description: 'Apache Sqoop import/export-kommandot Miss lyckas med kommandot "import misslyckades: Java. io. IOException: ägarskapet i uppsamlings katalogen/User/YOURUSERNAME/.Staging är inte som förväntat" fel för vissa användare i Azure HDInsight ESP-kluster'
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/01/2021
ms.openlocfilehash: 79aae099e30bae7d5201fae3384f7b89b6c30bcf
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387372"
---
# <a name="scenario-sqoop-importexport-command-fails-for-usernames-greater-than-20-characters-in-azure-hdinsight-esp-clusters"></a>Scenario: Sqoop import/export-kommando Miss lyckas för användar namn som är större än 20 tecken i Azure HDInsight ESP-kluster

Den här artikeln beskriver ett känt problem och en lösning när du använder Azure HDInsight ESP (Enterprise Security Pack) aktiverade kluster med ADLS Gen2 (ABFS) lagrings konto.

## <a name="issue"></a>Problem

När du kör kommandot Import/export av Sqoop Miss lyckas felet nedan för vissa användare:

```
ERROR tool.ImportTool: Import failed: java.io.IOException:
The ownership on the staging directory /user/yourlongdomainuserna/.staging is not as expected. 
It is owned by yourlongdomainusername.
The directory must be owned by the submitter yourlongdomainuserna or yourlongdomainuserna@AADDS.CONTOSO.COM
```

I exemplet ovan `/user/yourlongdomainuserna/.staging` visar det trunkerade 20-siffriga användar namnet för användar namnet `yourlongdomainusername` .

## <a name="cause"></a>Orsak

Längden på användar namnet är längre än 20 tecken. 

Se [hur objekt och autentiseringsuppgifter synkroniseras i en Azure Active Directory Domain Services hanterad domän](../active-directory-domain-services/synchronization.md) för mer information.

## <a name="workaround"></a>Lösning

Använd ett användar namn som är mindre än eller lika med 20 tecken.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]
