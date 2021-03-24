---
title: Installera program från tredje part i Azure HDInsight
description: Lär dig hur du installerar Apache Hadoop program från tredje part i Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/17/2019
ms.openlocfilehash: 46a22b78ade3d137f25b14e5cbdaff3264208228
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868400"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Installera Apache Hadoop program från tredje part i Azure HDInsight

Lär dig hur du installerar ett [Apache Hadoop](https://hadoop.apache.org/) program från tredje part i Azure HDInsight. Anvisningar om hur du installerar ett eget program finns i [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).

An-HDInsight program är ett program som användare kan installera i ett HDInsight-kluster. Dessa program kan utvecklas av Microsoft, oberoende programvaruleverantörer och av dig själv.  

I följande lista visas de publicerade programmen:

|Program |Kluster typ (er) | Beskrivning |
|---|---|---|
|[AtScale intelligence-plattform](https://aws.amazon.com/marketplace/pp/AtScale-AtScale-Intelligence-Platform/B07BWWHH18) |Hadoop |AtScale vänder ditt HDInsight-kluster till en skalbar OLAP-server, så att du kan fråga miljarder rader med data interaktivt med de BI-verktyg som du redan känner till, äger och gillar – från Microsoft Excel, Power BI, Tableau-programvara till QlikView. |
|[CDAP för HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/cask.cdap-for-hdinsight) |HBase |CDAP är den första enhetliga integrerings plattformen för Big data som påskyndar tiden till värde för Hadoop och gör det möjligt att tillhandahålla självbetjänings data. Öppen källkod och utöknings bar CDAP tar bort hinder för innovation. Krav: 4 regionfiler, min D3 v2. |
|[Datameer](https://azuremarketplace.microsoft.com/marketplace/apps/datameer.datameer) |Hadoop |Datameers självbetjänings skalbara plattform för att förbereda, utforska och hantera dina data för analys påskyndar komplexa data som är klara att användas i värdefull företags information och ger snabbare och smartare insikter i företags skala. |
|[Dataiku DSS på HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |Dataiku DSS i en Enterprise data science-plattform som gör det möjligt för data experter och dataanalytiker att samar beta för att utforma och köra nya data produkter och tjänster mer effektivt, så att rå data blir mer effektiva. |
|[WANdisco fusion HDI-app](https://community.wandisco.com/s/article/Use-WANdisco-Fusion-for-parallel-operation-of-ADLS-Gen1-and-Gen2) |Hadoop, Spark, HBase, Storm, Kafka |Att hålla data konsekvent i en distribuerad miljö är en enorm data åtgärds utmaning. WANdisco fusion, en program plattform i företags klass, löser problemet genom att aktivera ostrukturerad data konsekvens i alla miljöer. |
|[H2O SparklingWater för HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O mousserande vatten har stöd för följande distribuerade algoritmer: GLM, naïve Bayes, distribuerad slumpmässig skog, tonings förstärknings dator, djup neurala nätverk, djup inlärning, K-medelvärde, PCA, generaliserade låg rang modeller, avvikelse identifiering, Autoencoders. |
|[Striims för Real-Time data integrering till HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/striim.striimbyol) |Hadoop, HBase, Storm, Spark, Kafka |Striims (uttalad "Stream") är en slut punkt till slut punkts data integrering + informations plattform, vilket möjliggör kontinuerlig inmatning, bearbetning och analys av olika data strömmar. |
|[Jumbune Enterprise-Accelerating BigData Analytics](https://azuremarketplace.microsoft.com/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |På en hög nivå hjälper Jumbune företag av, 1. Accelerera tez, MapReduce & Spark Engine-baserad Hive, Java, Scala arbets belastnings prestanda. 2. Proaktiv övervakning av Hadoop-kluster, 3. Upprätta data kvalitets hantering på distribuerat fil system. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/kyligence.kyligence) |Hadoop, HBase, Spark |Kyligence Enterprise drivs av Apache Kylin och gör det möjligt för BI på Big data. Som en Enterprise OLAP-motor på Hadoop ger Kyligence Enterprise en affärsanalytiker som konstruerar BI på Hadoop med data lager och BI-metodik som bransch standard. |
|[Stjärn explosion Presto för Azure HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/starburstdatainc1579800938563.starburst-presto?tab=Overview) |Hadoop |Presto är en snabb och skalbar distribuerad SQL-frågemotor. Presto är perfekt för att kunna fråga data i Azure Data Lake Storage, Azure Blob Storage-, SQL-och NoSQL-databaser och andra data källor. |
|[StreamSets data insamlare för HDInsight-moln](https://azuremarketplace.microsoft.com/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop, HBase, Spark, Kafka |StreamSets data Collector är en lätt, kraftfull motor som strömmar data i real tid. Använd data insamlaren för att dirigera och bearbeta data i dina data strömmar. Den levereras med en 30-dagars utvärderings licens. |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/trifacta.trifacta-db?tab=Overview) |Hadoop, Spark, HBase |Trifacta Wrangler Enterprise för HDInsight stöder data datatransformering i hela företaget för alla data skalor. Kostnaden för att köra Trifacta på Azure är en kombination av Trifacta prenumerations kostnader plus Azures infrastruktur kostnader för de virtuella datorerna. |
|[Förenar data plattform](https://www.crunchbase.com/organization/unifi-software) |Hadoop, HBase, Storm, Spark |Den smidiga data plattformen är ett sömlöst integrerat utbud av självbetjänings data verktyg som är utformade för att ge affärs användaren möjlighet att ta itu med data utmaningar som ökar intäkterna, minska kostnaderna eller drifts komplexiteten. |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |Unravel data app för HDInsight Spark-kluster. |
|[Vatten linje AI-Driven Data Catalog](https://azuremarketplace.microsoft.com/marketplace/apps/waterline_data.waterline_data) |Spark |Vatten linje kataloger, ordnar och styr data med hjälp av AI för att automatiskt tagga data med affärs villkor. Den översta kostnads fria katalogen är en viktig komponent för självbetjänings analys, efterlevnad och styrning och IT-hanterings initiativ. |

I artikelns instruktioner används Azure-portalen. Du kan också exportera Azure Resource Manager-mallen från portalen eller skaffa en kopia av Resource Manager-mallen från leverantörer och använda Azure PowerShell och den klassiska Azure-CLI för att distribuera mallen.  Se [skapa Apache Hadoop kluster i HDInsight med Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Förutsättningar
Om du vill installera HDInsight-program i ett befintligt HDInsight-kluster måste du ha ett HDInsight-kluster. Om du vill skapa ett läser du [Skapa kluster](hadoop/apache-hadoop-linux-tutorial-get-started.md). Du kan även installera HDInsight-program när du skapar ett HDInsight-kluster.

## <a name="install-applications-to-existing-clusters"></a>Installera program i befintliga kluster
I följande procedur beskriver vi hur du installerar HDInsight-program i ett befintligt HDInsight-kluster.

**Installera ett HDInsight-program**

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. I den vänstra menyn navigerar du till **alla tjänster**  >  **Analytics**  >  **HDInsight-kluster**.
3. Välj ett HDInsight-kluster i listan.  Om du inte har något måste du skapa ett först.  Mer information finns i [Skapa kluster](hadoop/apache-hadoop-linux-tutorial-get-started.md).
4. Under kategorin **Inställningar** väljer du **program**. Du kan se en lista över installerade program i huvud fönstret. 
   
    :::image type="content" source="./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png" alt-text="HDInsight-program Portal meny":::
5. Välj **+ Lägg till** på menyn. Du kan se en lista över tillgängliga program.  Om **+ Lägg till** är nedtonad, innebär det att det inte finns några program för den här versionen av HDInsight-klustret.
   
    :::image type="content" source="./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png" alt-text="HDInsight-program tillgängliga program":::
6. Välj ett av de tillgängliga programmen och följ sedan anvisningarna för att godkänna de juridiska villkoren.

Du kan se installations status från Portal meddelanden (Välj klock ikonen överst i portalen). När programmet har installerats visas programmet i listan installerade appar.

## <a name="install-applications-during-cluster-creation"></a>Installera program när du skapar ett kluster

Du kan välja att installera HDInsight-program när du skapar ett kluster. Under processen installeras HDInsight-program när klustret har skapats och är i körläge. Om du vill installera program när du skapar klustret med hjälp av Azure Portal väljer du **+ Lägg till program** på fliken **konfiguration + prissättning** .

:::image type="content" source="./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png" alt-text="Azure Portal kluster konfigurations program":::

## <a name="list-installed-hdinsight-apps-and-properties"></a>Visa en lista med installerade HDInsight-appar och deras egenskaper
På portalen kan du visa en lista över de installerade HDInsight-programmen för ett kluster och egenskaperna för varje installerat program.

**Visa en lista med HDInsight-program och visnings egenskaper**

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. I den vänstra menyn navigerar du till **alla tjänster**  >  **Analytics**  >  **HDInsight-kluster**.
3. Välj ett HDInsight-kluster i listan.
4. Under kategorin **Inställningar** väljer du **program**. Du kan se en lista över installerade program i huvud fönstret. 
   
    :::image type="content" source="./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png" alt-text="HDInsight-program installerade appar":::
5. Välj ett av de installerade programmen för att visa egenskapen. Egenskaps listorna:

    |Egenskap | Beskrivning |
    |---|---|
    |Appnamn |Program namn. |
    |Status |Program status. |
    |Historik |URL: en för det webb program som du har distribuerat till Edge-noden. Autentiseringsuppgifterna är samma som HTTP-autentiseringsuppgifterna som du har konfigurerat för klustret. |
    |SSH-slutpunkt |Du kan använda SSH för att ansluta till Edge-noden. SSH-autentiseringsuppgifterna är samma som SSH-autentiseringsuppgifterna som du har konfigurerat för klustret. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight). |
    |Beskrivning | Programbeskrivning. |

6. Om du vill ta bort ett program högerklickar du på programmet och klickar sedan på **ta bort** på snabb menyn.

## <a name="connect-to-the-edge-node"></a>Ansluta till gränsnoden
Du kan ansluta till gränsnoden med HTTP och SSH. Du kan hitta information om slutpunkten på [portalen](#list-installed-hdinsight-apps-and-properties). Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

Autentiseringsuppgifterna för HTTP-slutpunkten är HTTP-autentiseringsuppgifterna som du har konfigurerat för HDInsight-klustret. Autentiseringsuppgifterna för SSH-slutpunkten är SSH-autentiseringsuppgifterna som du har konfigurerat för HDInsight-klustret.

## <a name="troubleshoot"></a>Felsöka
Läs [Felsöka installationen](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Nästa steg
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md): Lär dig hur du distribuerar ett opublicerat HDInsight-program till HDInsight.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): information om hur du publicerar anpassade HDInsight-program på Azure Marketplace.
* [MSDN: Installera ett HDInsight-program](/rest/api/hdinsight/hdinsight-application): information om hur du definierar HDInsight-program.
* [Anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md): information om hur du använder skriptåtgärd till att installera fler program.
* [Skapa Linux-baserade Apache Hadoop kluster i HDInsight med Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Lär dig hur du anropar Resource Manager-mallar för att skapa HDInsight-kluster.
* [Använda tomma edge-noder i HDInsight](hdinsight-apps-use-edge-node.md): Information om hur du använder en tom edge-nod för att få åtkomst till HDInsight-kluster, testa HDInsight-program och vara värd för HDInsight-program.