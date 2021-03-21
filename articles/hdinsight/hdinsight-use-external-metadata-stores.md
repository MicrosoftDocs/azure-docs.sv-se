---
title: Använd externa metadata-butiker – Azure HDInsight
description: Använd externa metadata butiker med Azure HDInsight-kluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 08/06/2020
ms.openlocfilehash: d36c8f1f592bbe714a9e31cad8131523049f29ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931358"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Använda extern metadatalagring i Azure HDInsight

Med HDInsight kan du ta kontroll över dina data och metadata med externa data lager. Den här funktionen är tillgänglig för [Apache Hive metaarkiv](#custom-metastore), [Apache Oozie Metaarkiv](#apache-oozie-metastore)och [Apache Ambari Database](#custom-ambari-db).

Apache Hive metaarkiv i HDInsight är en viktig del av Apache Hadoop arkitekturen. En metaarkiv är central schema lagrings plats. Metaarkiv används av andra Big Data Access-verktyg som Apache Spark, Interactive Query (LLAP), Presto eller Apache gris. HDInsight använder en Azure SQL Database som Hive-metaarkiv.

![HDInsight Hive metadata lager arkitektur](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Det finns två sätt att konfigurera en metaarkiv för dina HDInsight-kluster:

* [Standard metaarkiv](#default-metastore)
* [Anpassad metaarkiv](#custom-metastore)

## <a name="default-metastore"></a>Standard metaarkiv

Som standard skapar HDInsight en metaarkiv med varje kluster typ. I stället kan du ange en anpassad metaarkiv. Standard-metaarkiv innehåller följande överväganden:

* Ingen ytterligare kostnad. HDInsight skapar en metaarkiv med varje kluster typ utan ytterligare kostnad för dig.

* Varje standard-metaarkiv ingår i kluster livs cykeln. När du tar bort ett kluster raderas även motsvarande metaarkiv och metadata.

* Du kan inte dela standard-metaarkiv med andra kluster.

* Standard-metaarkiv rekommenderas endast för enkla arbets belastningar. Arbets belastningar som inte kräver flera kluster och som inte behöver metadata bevaras utanför klustrets livs cykel.

> [!IMPORTANT]
> Standard-metaarkiv tillhandahåller en Azure SQL Database med en **grundläggande nivå 5-DTU-gräns (inte uppgraderings bara)**! Lämpligt för grundläggande testnings ändamål. För stora eller produktions arbets belastningar rekommenderar vi att du migrerar till en extern metaarkiv.

## <a name="custom-metastore"></a>Anpassad metaarkiv

HDInsight har också stöd för anpassade metastores, vilket rekommenderas för produktions kluster:

* Du anger din egen Azure SQL Database som metaarkiv.

* Livs cykeln för metaarkiv är inte knuten till en kluster livs cykel, så du kan skapa och ta bort kluster utan att förlora metadata. Metadata, till exempel Hive-scheman, sparas även när du har tagit bort och återskapat HDInsight-klustret.

* Med en anpassad metaarkiv kan du koppla flera kluster och kluster typer till den metaarkiv. Till exempel kan en enda metaarkiv delas mellan interaktiva Query-, Hive-och Spark-kluster i HDInsight.

* Du betalar för kostnaden för en metaarkiv (Azure SQL Database) enligt den prestanda nivå som du väljer.

* Du kan skala upp metaarkiv efter behov.

* Klustret och det externa metaarkiv måste finnas i samma region.

![Användnings fall för HDInsight Hive-metadatalagret](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Skapa och konfigurera Azure SQL Database för den anpassade metaarkiv

Skapa eller skapa en befintlig Azure SQL Database innan du konfigurerar en anpassad Hive-metaarkiv för ett HDInsight-kluster.  Mer information finns i [snabb start: skapa en enskild databas i Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal).

När du skapar klustret måste HDInsight-tjänsten ansluta till den externa metaarkiv och verifiera dina autentiseringsuppgifter. Konfigurera Azure SQL Database brand Väggs regler så att Azure-tjänster och-resurser får åtkomst till servern. Aktivera det här alternativet i Azure Portal genom att välja **Ange server brand vägg**. Välj **ingen** under **neka offentlig nätverks åtkomst** och **Ja** under **Tillåt Azure-tjänster och-resurser för att få åtkomst till den här servern** för Azure SQL Database. Mer information finns i [skapa och hantera IP-brandväggens regler](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

Privata slut punkter för SQL-arkiv stöds endast på kluster som skapats med `outbound` ResourceProviderConnection. Mer information finns i den här [dokumentationen](./hdinsight-private-link.md).

![knappen Ange server brand vägg](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![Tillåt åtkomst till Azure-tjänster](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Välj en anpassad metaarkiv när klustret skapas

Du kan när som helst peka klustret till en tidigare skapad Azure SQL Database. För att skapa kluster via portalen anges alternativet från **lagrings > metaarkiv inställningar**.

![HDInsight Hive-metadatalagret Azure Portal](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-guidelines"></a>Hive-metaarkiv rikt linjer

> [!NOTE]
> Använd en anpassad metaarkiv när det är möjligt, för att hjälpa till att separera beräknings resurser (ditt kluster som körs) och metadata (lagrade i metaarkiv). Börja med nivån S2, som tillhandahåller 50 DTU och 250 GB lagrings utrymme. Om du ser en Flask hals kan du skala databasen uppåt.

* Om du har flera HDInsight-kluster för åtkomst till separata data använder du en separat databas för metaarkiv på varje kluster. Om du delar en metaarkiv över flera HDInsight-kluster innebär det att klustren använder samma metadata och underliggande användar data filer.

* Säkerhetskopiera dina anpassade metaarkiv med jämna mellanrum. Azure SQL Database skapar säkerhets kopior automatiskt, men tids perioden för säkerhets kopieringen varierar. Mer information finns i [Läs mer om automatisk SQL Database säkerhets kopiering](../azure-sql/database/automated-backups-overview.md).

* Leta upp ditt metaarkiv-och HDInsight-kluster i samma region. Den här konfigurationen ger högsta möjliga prestanda och lägsta utgående kostnader för nätverket.

* Övervaka dina metaarkiv för prestanda och tillgänglighet med hjälp av Azure SQL Database övervaknings verktyg, eller Azure Monitor loggar.

* När en ny, högre version av Azure HDInsight skapas mot en befintlig anpassad metaarkiv-databas, uppgraderar systemet schemat för metaarkiv. Uppgraderingen kan inte ångras utan att återställa databasen från säkerhets kopian.

* Om du delar en metaarkiv över flera kluster ser du till att alla kluster har samma HDInsight-version. Olika Hive-versioner använder olika metaarkiv-databasschemat. Du kan till exempel inte dela en metaarkiv över Hive-kluster med Hive 2,1 och Hive 3,1-versioner.

* I HDInsight 4,0 använder Spark och Hive oberoende kataloger för åtkomst till SparkSQL-eller Hive-tabeller. En tabell som skapats av Spark bor i Spark-katalogen. En tabell som skapats av Hive finns i Hive-katalogen. Det här beteendet skiljer sig från HDInsight 3,6 där Hive och Spark delat gemensamt katalog. Hive-och Spark-integrering i HDInsight 4,0 förlitar sig på Hive Warehouse Connector (INSTANSEN). INSTANSEN fungerar som en brygga mellan Spark och Hive. [Lär dig mer om Hive lager koppling](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

* I HDInsight 4,0 om du vill dela metaarkiv mellan Hive och Spark kan du göra det genom att ändra egenskapen metaarkiv. Catalog. default till Hive i Spark-klustret. Du kan hitta den här egenskapen i Ambari Advanced spark2-Hive-site-override. Det är viktigt att förstå att delning av metaarkiv endast fungerar för externa Hive-tabeller, men detta fungerar inte om du har interna/hanterade Hive-tabeller eller syror tabeller.  

## <a name="apache-oozie-metastore"></a>Apache Oozie-metaarkiv

Apache Oozie är ett system för att koordinera arbetsflöden som hanterar Hadoop-jobb. Oozie stöder Hadoop-jobb för Apache MapReduce, gris, Hive och andra.  Oozie använder en metaarkiv för att lagra information om arbets flöden. Om du vill öka prestandan när du använder Oozie kan du använda Azure SQL Database som en anpassad metaarkiv. Metaarkiv ger åtkomst till Oozie-jobb data när du har tagit bort klustret.

Anvisningar om hur du skapar en Oozie-metaarkiv med Azure SQL Database finns i [använda Apache Oozie för arbets flöden](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>Anpassad Ambari-databas

Om du vill använda din egen externa databas med Apache Ambari på HDInsight, se [anpassad Apache Ambari-databas](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera](./hdinsight-hadoop-provision-linux-clusters.md)