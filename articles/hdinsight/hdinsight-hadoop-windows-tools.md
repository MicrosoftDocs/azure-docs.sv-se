---
title: Använda en Windows-dator med Hadoop på HDInsight – Azure
description: Arbeta från en Windows-dator i Hadoop på HDInsight. Hantera och fråga kluster med PowerShell-, Visual Studio-och Linux-verktyg. Utveckla Big data-lösningar med .NET.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/20/2019
ms.openlocfilehash: ceb83c8c1754f0bc298290f522a8ae532db32140
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434618"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Arbeta i Apache Hadoop eko system på HDInsight från en Windows-dator

Lär dig mer om utvecklings-och hanterings alternativ på Windows-datorn för att arbeta i Apache Hadoop eko systemet i HDInsight.

HDInsight baseras på Apache Hadoop-och Hadoop-komponenter, teknik med öppen källkod som utvecklats i Linux. HDInsight version 3,4 och högre använder Ubuntu Linux distribution som det underliggande operativ systemet för klustret. Du kan dock arbeta med HDInsight från en Windows-klient eller Windows utvecklings miljö.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Använda PowerShell för distributions-och hanterings uppgifter

Azure PowerShell är en skript miljö som du kan använda för att styra och automatisera distributions-och hanterings uppgifter i HDInsight från Windows.

Exempel på uppgifter som du kan utföra med PowerShell:

* [Skapa kluster med PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).
* [Kör Apache Hive frågor med PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).
* [Hantera kluster med PowerShell](hdinsight-administer-use-powershell.md).

Följ stegen för att [Installera och konfigurera Azure PowerShell](/powershell/azure/install-az-ps) för att få den senaste versionen.

## <a name="utilities-you-can-run-in-a-browser"></a>Verktyg som du kan köra i en webbläsare

Följande verktyg har ett webb gränssnitt som körs i en webbläsare:
* **[Azure Cloud Shell](../cloud-shell/overview.md)** är ett interaktivt kommando rads gränssnitt som körs i webbläsaren och inifrån Azure Portal.

* **[Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md)** är ett hanterings-och övervaknings verktyg som är tillgängligt i Azure Portal som kan användas för att hantera olika typer av jobb, t. ex.:
    * [Använd Apache Ambari med REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Apache Hive vy i Apache Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Apache Tez-vy i Apache Ambari](./index.yml)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Data Lake-verktyg (Hadoop) för Visual Studio

Använd Data Lake verktyg för Visual Studio för att distribuera och hantera Storm-topologier. Data Lake verktyg installerar även SCP.NET SDK, som gör att du kan utveckla C# Storm-topologier med Visual Studio.

Innan du går till följande exempel, [installerar du och försöker data Lake verktyg för Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Exempel på uppgifter som du kan utföra med Visual Studio och Data Lake verktyg för Visual Studio:
* [Distribuera och hantera Storm-topologier från Visual Studio](storm/apache-storm-deploy-monitor-topology-linux.md)
* [Utveckla C#-topologier för Storm med Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md). Bitarna innehåller exempel mallar för Storm-topologier som du kan ansluta till databaser, till exempel Azure Cosmos DB och SQL Database.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio och .NET SDK

Du kan använda Visual Studio med .NET SDK för att hantera kluster och utveckla Big data program. Du kan använda andra IDE: er för följande uppgifter, men exemplen visas i Visual Studio.

Exempel på uppgifter som du kan utföra med .NET SDK i Visual Studio:
* [Azure HDInsight SDK för .net](/dotnet/api/overview/azure/hdinsight).
* [Kör Apache Hive frågor med .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).
* [Använd C#-användardefinierade funktioner med Apache Hive och Apache gris streaming på Apache Hadoop](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>IntelliJ-idé och Sol förmörkelse IDE för Spark-kluster

Både [IntelliJ-idén](https://www.jetbrains.com/idea/download) och [sol förmörkelses IDE](https://www.eclipse.org/downloads/) kan användas för att:
* Utveckla och skicka in ett Scala Spark-program på ett HDInsight Spark-kluster.
* Åtkomst till Spark kluster resurser.
* Utveckla och kör ett Scala Spark-program lokalt.

De här artiklarna visar hur:
* IntelliJ idé: [skapa Apache Spark program med hjälp av Azure Toolkit för IntelliJ-plugin och Scala SDK.](spark/apache-spark-intellij-tool-plugin.md)
* Sol förmörkelse IDE eller Scala IDE för Sol förmörkelse: [skapa Apache Spark program och Azure Toolkit for Eclipse](spark/apache-spark-eclipse-tool-plugin.md)

## <a name="notebooks-on-spark-for-data-scientists"></a>Bärbara datorer i Spark för data experter

Apache Spark kluster i HDInsight innehåller Apache Zeppelin-anteckningsböcker och kärnor som kan användas med Jupyter-anteckningsböcker.

* [Lär dig hur du använder kärnor i Apache Spark kluster med Jupyter-anteckningsböcker för att testa Spark-program](spark/apache-spark-zeppelin-notebook.md)
* [Lär dig hur du använder Apache Zeppelin-anteckningsböcker på Apache Spark kluster för att köra Spark-jobb](spark/apache-spark-jupyter-notebook-kernels.md)

## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Köra Linux-baserade verktyg och tekniker i Windows

Om du kommer över en situation där du måste använda ett verktyg eller en teknik som bara är tillgänglig i Linux bör du tänka på följande alternativ:

* **Bash på Ubuntu i Windows 10** tillhandahåller ett Linux-undersystem i Windows. Med bash kan du köra Linux-verktyg direkt utan att behöva ha en särskild Linux-installation. Installations [Guide för Windows-undersystem för Linux finns i Windows 10](/windows/wsl/install-win10) för installations steg.  Andra [UNIX-gränssnitt](https://www.gnu.org/software/bash/) fungerar också.
* **Docker för Windows** ger till gång till många Linux-baserade verktyg och kan köras direkt från Windows. Du kan till exempel använda Docker för att köra Beeline-klienten för Hive direkt från Windows. Du kan också använda Docker för att köra en lokal Jupyter Notebook och fjärrans luta till Spark på HDInsight. [Kom igång med Docker för Windows](https://docs.docker.com/docker-for-windows/)
* Med **[MobaXTerm](https://mobaxterm.mobatek.net/)** kan du grafiskt bläddra i kluster fil systemet via en SSH-anslutning.

## <a name="cross-platform-tools"></a>Plattforms oberoende verktyg

Kommandoradsgränssnittet för Azure (CLI) är Microsofts plattformsoberoende kommandoradsmiljö för att hantera Azure-resurser.  Mer information finns i avsnittet om [Azure Command-Line Interface (CLI)](/cli/azure/).

## <a name="next-steps"></a>Nästa steg

Om du inte har arbetat med att arbeta i Linux-baserade kluster kan du läsa följande artiklar:
* [Konfigurera Apache Hadoop, Apache Kafka, Apache Spark eller andra kluster](hdinsight-hadoop-provision-linux-clusters.md)
* [Tips för HDInsight-kluster i Linux](hdinsight-hadoop-linux-information.md)