---
title: Det gick inte att skapa Jupyter Notebook i Azure HDInsight
description: Lär dig mer om fel söknings stegen och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/11/2020
ms.openlocfilehash: d5f6612e22522452efb5eecd14c6c825bf29e6d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938680"
---
# <a name="unable-to-create-jupyter-notebook-in-azure-hdinsight"></a>Det gick inte att skapa Jupyter Notebook i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

När du startar en Jupyter Notebook får du ett fel meddelande som innehåller:

```error
Cannot convert notebook to v5 because that version doesn't exist
```

## <a name="cause"></a>Orsak

En versions konflikt.

## <a name="resolution"></a>Lösning

1. Använd [SSH-kommandot](../hdinsight-hadoop-linux-use-ssh-unix.md) för att ansluta till klustret. Redigera kommandot nedan genom att ersätta kluster namn med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Öppna `_version.py` genom att köra följande kommando:

    ```bash
    sudo nano /usr/bin/anaconda/lib/python2.7/site-packages/nbformat/_version.py
    ```

1. Ändra **5** till **4** så att den ändrade raden visas på följande sätt:

    ```python
    version_info = (4, 0, 3)
    ```

    Spara ändringarna genom att ange **CTRL + X**, **Y**, **RETUR**.

1. I en webbläsare går du till `https://CLUSTERNAME.azurehdinsight.net/#/main/services/JUPYTER` , där `CLUSTERNAME` är namnet på klustret.

1. Starta om Jupyter-tjänsten.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
