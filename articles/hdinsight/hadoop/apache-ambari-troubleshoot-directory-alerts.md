---
title: Apache Ambari Directory-aviseringar i Azure HDInsight
description: Diskussion och analys av möjliga orsaker och lösningar för Apache Ambari Directory-aviseringar i HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 47d1f9797a44d7dc918677c21ffc7a124808525d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943286"
---
# <a name="scenario-apache-ambari-directory-alerts-in-azure-hdinsight"></a>Scenario: Apache Ambari Directory-aviseringar i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Du får fel meddelanden från Apache Ambari som liknar:

```
1/1 local-dirs have errors: [ /mnt/resource/hadoop/yarn/local : Cannot create directory: /mnt/resource/hadoop/yarn/local ]
1/1 log-dirs have errors: [ /mnt/resource/hadoop/yarn/log : Cannot create directory: /mnt/resource/hadoop/yarn/log ]
```

## <a name="cause"></a>Orsak

De angivna katalogerna från Ambari-aviseringen saknas på arbetsnoder som påverkas.

## <a name="resolution"></a>Lösning

Skapa saknade kataloger manuellt på de berörda arbetsnoderna.

1. SSH till relevant Worker-nod.

1. Hämta rot användare: `sudo su` .

1. Skapa de kataloger som behövs rekursivt.

1. Ändra ägare och grupp för dessa kataloger.

    ```bash
    chown -R yarn /mnt/resource/hadoop/yarn/local
    chgrp -R hadoop /mnt/resource/hadoop/yarn/local
    chown -R yarn /mnt/resource/hadoop/yarn/log
    chgrp -R hadoop /mnt/resource/hadoop/yarn/log
    ```

1. Inaktivera och aktivera avisering från Apache Ambari UI.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]