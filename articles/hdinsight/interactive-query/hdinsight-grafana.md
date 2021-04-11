---
title: Använda Grafana i Azure HDInsight
description: Lär dig hur du kommer åt Grafana-instrumentpanelen med Apache Hadoop kluster i Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/27/2019
ms.openlocfilehash: 81fd3b368f9405192c164ed7a0638caad0cd75fc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869760"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Använda Grafana i Azure HDInsight

[Grafana](https://grafana.com/) är ett populärt diagram och instrument panels verktyg med öppen källkod. Grafana är funktionen Rich; Det innebär inte bara att användare kan skapa anpassningsbara och shareable-instrumentpaneler, men den erbjuder även mallbaserade och skriptbaserade instrument paneler, LDAP-integration, flera data källor med mera.

För närvarande stöds Grafana i Azure HDInsight med kluster typerna Spark, HBase, Kafka och interaktiv fråga. Det finns inte stöd för kluster med Enterprise Security Pack aktiverat.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-an-apache-hadoop-cluster"></a>Skapa ett Apache Hadoop-kluster

Se [skapa Apache Hadoop kluster med hjälp av Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md). För **kluster typ** väljer du **Spark**, **Kafka**, **HBase** eller **interaktiv fråga**.

## <a name="access-the-grafana-dashboard"></a>Öppna Grafana-instrumentpanelen

1. I en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net/grafana/` där kluster namn är namnet på klustret.

1. Ange användarens autentiseringsuppgifter för Hadoop-klustret.

1. Grafana-instrumentpanelen visas och ser ut som i det här exemplet:

    :::image type="content" source="./media/hdinsight-grafana/hdinsight-grafana-dashboard.png " alt-text="HDInsight Grafana-webbinstrumentpanel" border="true":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet, tar du bort det kluster som du skapade med följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. I rutan **Sök** längst upp skriver du **HDInsight**.

1. Välj **HDInsight-kluster** under **Tjänster**.

1. I listan med HDInsight-kluster som visas väljer du **...** bredvid det kluster som du skapade.

1. Välj **Ta bort**. Välj **Ja**.

## <a name="next-steps"></a>Nästa steg

Mer information om att analysera data med HDInsight finns i följande artiklar:

* [Använd Apache Hive med HDInsight](../hadoop/hdinsight-use-hive.md).

* [Använd MapReduce med HDInsight](../hadoop/hdinsight-use-mapreduce.md).

* [Kom igång med Visual Studio Hadoop-verktyg för HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
