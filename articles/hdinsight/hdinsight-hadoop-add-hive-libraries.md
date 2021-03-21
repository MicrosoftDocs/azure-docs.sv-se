---
title: Apache Hive bibliotek när klustret skapas – Azure HDInsight
description: Lär dig hur du lägger till Apache Hive bibliotek (jar-filer) i ett HDInsight-kluster när klustret skapas.
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 02/14/2020
ms.openlocfilehash: b6695e5e985a30d6f912095225c4899e1c910e34
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945953"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Lägg till anpassade Apache Hive-bibliotek när du skapar ett HDInsight-kluster

Lär dig hur du förinstallerar [Apache Hive](https://hive.apache.org/) bibliotek i HDInsight. Det här dokumentet innehåller information om hur du använder en skript åtgärd för att läsa in bibliotek när klustret skapas. Bibliotek som lagts till med hjälp av stegen i det här dokumentet är globalt tillgängliga i Hive – du behöver inte använda [Lägg till jar](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) för att läsa in dem.

## <a name="how-it-works"></a>Så här fungerar det

När du skapar ett kluster kan du använda en skript åtgärd för att ändra klusternoderna när de har skapats. Skriptet i det här dokumentet accepterar en enda parameter, som är platsen för biblioteken. Den här platsen måste vara i ett Azure Storage konto och biblioteken måste lagras som jar-filer.

När klustret skapas räknar skriptet upp filerna, kopierar dem till `/usr/lib/customhivelibs/` katalogen på Head-och Worker-noderna och lägger sedan till dem i `hive.aux.jars.path` egenskapen i `core-site.xml` filen. I Linux-baserade kluster uppdaterar det också `hive-env.sh` filen med filernas plats.

Genom att använda skript åtgärden i den här artikeln blir biblioteken tillgängliga när du använder en Hive-klient för **WebHCat** och **HiveServer2**.

## <a name="the-script"></a>Skriptet

**Skriptplats**

[https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

### <a name="requirements"></a>Krav

* Skripten måste användas både för **huvudnoderna** och **arbetsnoderna**.

* Jar v7 som du vill installera måste lagras i Azure Blob Storage i en **enda behållare**.

* Lagrings kontot som innehåller biblioteket för JAR-filer **måste** länkas till HDInsight-klustret när det skapas. Det måste antingen vara ett standard lagrings konto eller ett konto som läggs till via __inställningarna för lagrings kontot__.

* WASB-sökvägen till behållaren måste anges som en parameter för skript åtgärden. Om t. ex. jar v7 lagras i en behållare med namnet **libs** på ett lagrings konto med namnet **unstorage**, skulle parametern vara `wasbs://libs@mystorage.blob.core.windows.net/` .

  > [!NOTE]  
  > Det här dokumentet förutsätter att du redan har skapat ett lagrings konto, en BLOB-behållare och överfört filerna till det.
  >
  > Om du inte har skapat ett lagrings konto kan du göra det via [Azure Portal](https://portal.azure.com). Du kan sedan använda ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/) för att skapa en behållare i kontot och ladda upp filer till den.

## <a name="create-a-cluster-using-the-script"></a>Skapa ett kluster med hjälp av skriptet

1. Starta etableringen av ett kluster med hjälp av stegen i [Etablera HDInsight-kluster på Linux](hdinsight-hadoop-provision-linux-clusters.md), men Slutför inte etablering. Du kan också använda Azure PowerShell eller HDInsight .NET SDK för att skapa ett kluster med det här skriptet. Mer information om hur du använder dessa metoder finns i [Anpassa HDInsight-kluster med skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md). För Azure Portal, på fliken **konfiguration + prissättning** , väljer du **åtgärden + Lägg till skript**.

1. För **lagring**, om lagrings kontot som innehåller biblioteket för JAR-filer skiljer sig från det konto som används för klustret, slutför du **ytterligare lagrings konton**.

1. Ange följande information för **skript åtgärder**:

    |Egenskap |Värde |
    |---|---|
    |Skript typ|– Anpassad|
    |Name|Bibliotek |
    |Bash-skript-URI|`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`|
    |Node-typ (er)|Head, Worker|
    |Parametrar|Ange WASB-adressen till den behållare och det lagrings konto som innehåller jar v7. Till exempel `wasbs://libs@mystorage.blob.core.windows.net/`.|

    > [!NOTE]
    > Använd den här bash-skript-URI: n för Apache Spark 2,1: `https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v00.sh` .

1. Fortsätt att etablera klustret enligt beskrivningen i [Etablera HDInsight-kluster i Linux](hdinsight-hadoop-provision-linux-clusters.md).

När klustret har skapats kan du använda jar v7 som lagts till via det här skriptet från Hive utan att behöva använda `ADD JAR` instruktionen.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med Hive finns i [använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
