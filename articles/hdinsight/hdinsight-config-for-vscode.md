---
title: Referens för konfigurations inställningar för Azure HDInsight
description: Presentera konfigurationen av Azure HDInsight-tillägget.
ms.service: hdinsight
ms.topic: how-to
ms.date: 04/07/2021
ms.custom: devx-track-python
ms.openlocfilehash: a9a444c2557ea17114123cbd5084cbbd9fe6dac6
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107260018"
---
# <a name="azure-hdinsight-configuration-settings-reference"></a>Referens för konfigurations inställningar för Azure HDInsight

Spark & Hive tools-tillägget för Visual Studio Code är mycket konfigurerbart. Den här sidan beskriver de nyckel inställningar som du kan arbeta med.  

Allmän information om hur du arbetar med inställningar i VS Code finns i [Inställningar för användare och arbets ytor](https://code.visualstudio.com/docs/getstarted/settings)och [variabel referens](https://code.visualstudio.com/docs/editor/variables-reference) för information om fördefinierat variabel stöd.

## <a name="open-the-azure-hdinsight-configuration"></a>Öppna Azure HDInsight-konfigurationen

1. Öppna en mapp först för att skapa inställningar för arbets ytan.
2. Tryck på **Ctrl + Shift + P** eller navigera till **Visa**  ->  **kommando palett...** om du vill visa alla kommandon.
3. Konfiguration av Sök **uppsättning**.
4. Expandera **tillägg** i den vänstra katalogen och välj **HDInsight-konfiguration**.

 ![HDI konfigurations avbildning](./media/HDInsight-config-for-vscode/HDInsight-config-for-vscode.png)

## <a name="general-settings"></a>Allmänna inställningar   

|  Egenskap   | Standardvärde | Beskrivning   |
| ----- | ----- |----- |
| HDInsight: Azure-miljö | Azure | Azure-miljö |
| HDInsight: inaktivera öppen undersöknings länk | Markerad | Aktivera/inaktivera inledande HDInsight-undersökning |
| HDInsight: Aktivera hoppa över Pyspark-installation | Avmarkerat | Aktivera/inaktivera hoppa över pyspark-installation |
| HDInsight: Aktivera inloggnings tips | Avmarkerat | När det här alternativet är markerat visas en prompt när du loggar in på Azure |
| HDInsight: tidigare tilläggs version | Visa versions numret för det aktuella tillägget | Visa föregående tilläggs version|
| HDInsight: teckensnitts familj för resultat | -Apple-system, BlinkMacSystemFont, Segoe WPC, Segoe UI, HelveticaNeue-Light, Ubuntu, Droid San, sans-serif | Ange teckensnitts familjen för resultat rutnätet. Ange som tom om du vill använda redigerarens teckensnitt |
| HDInsight: resultat tecken storlek | 13 |Ange tecken storleken för resultatet GIRD; Ange som tom om du vill använda storleken på redigeraren |
| HDInsight-kluster: länkat kluster | -- | URL: er för länkade kluster. Du kan också redigera JSON-filen för att ange |
| HDInsight Hive: Använd lokalisering | Avmarkerat | Valfritt Konfigurations alternativ för att lokalisera i VSCode konfigurerade nationella inställningar (måste starta om VSCode för att inställningarna ska börja gälla)|
| HDInsight Hive: kopiera include-rubriker | Avmarkerat | Valfritt Konfigurations alternativ för att kopiera resultat från vyn resultat |
| HDInsight-Hive: kopiera ta bort ny rad | Markerad | Valfritt Konfigurations alternativ för att kopiera flera rad resultat från vyn resultat |
| HDInsight Hive ›-format: justera kolumn definitioner i kolumner | Avmarkerat | Ska kolumn definitionen vara justerad |
| HDInsight Hive ›-format: datatype-versaler | inget | Ska data typer formateras som VERSALer, gemener eller none (inte formaterade) |
| HDInsight Hive ›-format: nyckelords Skift läge | inget | Ska nyckelord formateras som VERSALer, gemener eller none (inte formaterade) |
| HDInsight Hive ›-format: placera kommatecken före nästa instruktion | Avmarkerat | Ska kommatecken placeras i början av varje instruktion i en lista till exempel ', mycolumn2 ' i stället för i slutet ' mycolumn1 '|
| HDInsight Hive ›-format: placera Select-instruktions referenser på ny rad | Avmarkerat | Ska referenser till objekt i en SELECT-instruktion delas upp i separata rader? Till exempel, for ' SELECT C1, C2 från T1 ' både C1 och C2 kommer att finnas på separata linjer
| HDInsight Hive: logg fel söknings information | Avmarkerat | Valfritt Logga fel söknings utdata i VS Code-konsolen (hjälp-> växla Utvecklarverktyg) 
| HDInsight Hive: meddelanden standard är öppen | Markerad | Sant för att meddelande fönstret ska öppnas som standard. falskt för stängd|
| HDInsight Hive: resultat teckensnitts familj | -Apple-system, BlinkMacSystemFont, Segoe WPC, Segoe UI, HelveticaNeue-Light, Ubuntu, Droid San, sans-serif | Ange teckensnitts familjen för resultat rutnätet. Ange som tom om du vill använda redigerarens teckensnitt |
| HDInsight-Hive: resultat tecken storlek | 13 | Ange tecken storleken för resultat rutnätet. Ange som tom om du vill använda storleken på redigeraren |
| HDInsight Hive-› Spara som CSV: inkludera rubriker | Markerad | Valfritt När värdet är true inkluderas kolumn rubriker när du sparar resultat som CSV |
| HDInsight Hive: genvägar | -- | Genvägar som är relaterade till resultat fönstret |
| HDInsight Hive: Visa batch-tid| Avmarkerat | Valfritt Ska körnings tid visas för enskilda batchar |
| HDInsight-Hive: delnings fönstret är markerat | nästa | Valfritt Konfigurations alternativ för vilka kolumner nya resultat fönster ska öppnas i |
| Sändning av HDInsight-jobb: kluster conf | -- | Kluster konfiguration |
| Inlämning av HDInsight-jobb: livy conf | -- | Livy-konfiguration. POST/batchar |
| HDInsight-Jupyter: Lägg till resultat| Markerad | Om resultatet ska läggas till i resultat fönstret, annars raderas och visas. |
| HDInsight-Jupyter: språk | -- | Standardinställningar per språk. |
| HDInsight Jupyter › log: verbose | Avmarkerat | Aktivera utförlig loggning |
| HDInsight Jupyter › Notebook: Start argument | Kan lägga till objekt | kommando rads argument för Jupyter Notebook. Varje argument är ett separat objekt i matrisen. För en fullständig List typ "Jupyter Notebook – Help" i ett terminalfönster. |
| HDInsight Jupyter › Notebook: startup-mapp | $ {workspaceRoot} |-- |
| HDInsight-Jupyter: python-tillägget aktiverat | Markerad | Använd python-interaktiva fönster i MS-python-tillägget när du skickar pySpark-interaktiva jobb. Annars använder du vårt egna Jupyter-fönster |
| HDInsight-Spark.NET: 7z | C:\Program Files\7-Zip | <sökväg till 7z.exe> |
| HDInsight-Spark.NET: HADOOP_HOME | D:\winutils | <sökväg till bin\winutils.exe> endast Windows-operativsystem |
| HDInsight-Spark.NET: JAVA_HOME | C:\Program Files\Java\ jdk1.8.0_201 \ | Sökväg till Java Home|
| HDInsight-Spark.NET: SCALA_HOME | C:\Program Files (x86) \scala\ | Sökväg till Scala Home |
| HDInsight-Spark.NET: SPARK_HOME | D:\spark-2.3.3-bin-hadoop2.7\ | Sökväg till Spark Home |
| Hive: flikar för persist-frågeresultat | Avmarkerat | Hive-PersistQueryResultTabs |
| Hive: delnings fönster val | nästa | Valfritt Konfigurations alternativ för vilka kolumner nya resultat fönster ska öppnas i |
| Hive-interaktiv: kopiera körbar mapp | Avmarkerat | Om kopiera den interaktiva Hive-mappen för tjänst körning till användarens tmp-mapp |
| HQL interaktiv Server: omslutnings port | 13424 | Den interaktiva Hive-tjänsteporten |
| HQL språk Server: language wrapper-port | 12342 | Hive-språktjänstens port servrar lyssnar på. |
| HQL-språk Server: högsta antal problem | 100 | Styr det maximala antalet problem som skapas av servern. |
| Synapse Spark Compute: Synapse Spark Compute Azure-miljö | tomt | Synapse Spark Compute Azure-miljö |
| Synapse för Spark-jobb: livy conf | -- | Livy-konfiguration. POST/batchar
| Synapse för Spark-jobb: Synapse Spark pool Cluster conf | -- | Konfiguration av Synapse Spark-pool |


## <a name="next-steps"></a>Nästa steg

- Information om Azure HDInsight för VSCode finns i [Spark & Hive för Visual Studio Code tools](https://docs.microsoft.com/sql/big-data-cluster/spark-hive-tools-vscode).
- En video som visar hur man använder Spark & Hive för Visual Studio Code finns i [Spark & Hive för Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).