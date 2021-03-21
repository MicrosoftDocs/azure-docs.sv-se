---
title: PySpark interaktiv miljö med Azure HDInsight-verktyg
description: Lär dig hur du använder Azure HDInsight-verktyg för Visual Studio Code för att skapa och skicka frågor och skript.
keywords: VScode, Azure HDInsight tools, Hive, python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, interaktiv fråga
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/23/2020
ms.openlocfilehash: 337a9f3f2ea25e5a4d4fa4204a0f3fa4dcc9369b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98940642"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Konfigurera den interaktiva PySpark-miljön för Visual Studio Code

Följande steg visar hur du ställer in den interaktiva PySpark-miljön i VSCode. Det här steget är endast för användare som inte är Windows-användare.

Vi använder kommandot **python/pip** för att bygga en virtuell miljö i din Start Sök väg. Om du vill använda en annan version måste du ändra standard versionen av **python/pip** kommando manuellt. Mer information finns i [Update-alternativ](https://linux.die.net/man/8/update-alternatives).

1. Installera [python](https://www.python.org/downloads/) och [pip](https://pip.pypa.io/en/stable/installing/).

   * Installera python från [https://www.python.org/downloads/](https://www.python.org/downloads/) . 
   * Installera pip från [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) (om det inte är installerat från python-installationen).
   * Du kan också kontrol lera att python och pip har installerats med hjälp av kommandona `python --version` , `pip --version` respektive. 

     > [!NOTE]
     > Vi rekommenderar att du installerar python manuellt i stället för att använda macOS-standardversionen.

2. Installera **virtuell miljö** genom att köra kommandot nedan.

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>Andra paket

I Linux, om du kommer över fel meddelandet nedan, installerar du de nödvändiga paketen genom att köra följande två kommandon.

   ![Installera libkrb5-paket för python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

Starta om VSCode och gå sedan tillbaka till VSCode-redigeraren och kör **Spark: PySPark interaktiva** kommando.

## <a name="next-steps"></a>Nästa steg

### <a name="demo"></a>Demo

* HDInsight för VS Code: [video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Verktyg och tillägg

* [Använda Azure HDInsight-verktyget för Visual Studio Code](hdinsight-for-vscode.md)
* [Använd Azure Toolkit for IntelliJ för att skapa och skicka Apache Spark Scala-program](spark/apache-spark-intellij-tool-plugin.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](spark/apache-spark-jupyter-notebook-install-locally.md)
