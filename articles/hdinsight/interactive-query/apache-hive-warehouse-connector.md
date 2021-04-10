---
title: Apache Spark & Hive – Hive lager koppling – Azure HDInsight
description: Lär dig hur du integrerar Apache Spark och Apache Hive med Hive Warehouse Connector på Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 57a3d76f24c33984a883e926a8d4c68736e9f121
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869896"
---
# <a name="integrate-apache-spark-and-apache-hive-with-hive-warehouse-connector-in-azure-hdinsight"></a>Integrera Apache Spark och Apache Hive med Hive Warehouse Connector i Azure HDInsight

Apache Hive Warehouse Connector (INSTANSEN) är ett bibliotek som gör att du enkelt kan arbeta med Apache Spark och Apache Hive. Det stöder åtgärder som att flytta data mellan Spark-DataFrames och Hive-tabeller. Du kan också genom att dirigera Spark strömma data till Hive-tabeller. Hive-lagrets koppling fungerar som en brygga mellan Spark och Hive. Det stöder också Scala, Java och python som programmeringsspråk för utveckling.

Med Hive-lagrets koppling kan du dra nytta av de unika funktionerna i Hive och Spark för att skapa kraftfulla Big data-program.

Apache Hive erbjuder stöd för databas transaktioner som är atomiska, konsekventa, isolerade och varaktiga (sur). Mer information om syra och transaktioner i Hive finns i [Hive-transaktioner](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Hive erbjuder också detaljerade säkerhets kontroller via Apache Ranger och analytisk bearbetning av låg latens (LLAP) som inte är tillgängliga i Apache Spark.

Apache Spark, har ett strukturerat strömnings-API som ger direkt uppspelnings funktioner som inte är tillgängliga i Apache Hive. Från och med HDInsight 4,0 är Apache Spark 2.3.1 och Apache Hive 3.1.0 separata metastores. De separata metastores kan göra det svårt att samverka. Hive-lager kopplingen gör det lättare att använda Spark och Hive tillsammans. INSTANSEN-biblioteket läser in data från LLAP-daemonar till Spark-körningar parallellt. Den här processen gör det mer effektivt och anpassningsbart än en standard JDBC-anslutning från Spark till Hive.

:::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png" alt-text="arkitektur för Hive-lager koppling" border="true":::

Några av de åtgärder som stöds av Hive-lager kopplingen är:

* Beskriva en tabell
* Skapa en tabell för ORC-formaterade data
* Välja Hive-data och hämta en DataFrame
* Skriver en DataFrame till Hive i batch
* Kör en uppdaterings instruktion för Hive
* Läsa tabell data från Hive, transformera dem i Spark och skriva till en ny Hive-tabell
* Skriva en DataFrame-eller Spark-dataström till Hive med HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Installation av Hive lager koppling

> [!IMPORTANT]
> - Den interaktiva HiveServer2-instansen som är installerad på Spark 2,4 Enterprise Security Package-kluster stöds inte för användning med Hive-lagrets koppling. I stället måste du konfigurera ett separat HiveServer2-interaktivt kluster som värd för dina HiveServer2-interaktiva arbets belastningar. Det finns inte stöd för att konfigurera en Hive-nätkoppling som använder ett enda Spark 2,4-kluster.
> - INSTANSEN-bibliotek (Hive Warehouse Connector) stöds inte för användning med interaktiva Query-kluster där funktionen för arbets belastnings hantering (WLM) är aktive rad. <br>
I ett scenario där du bara har Spark-arbetsbelastningar och vill använda INSTANSEN-bibliotek, ser du till att det inte finns någon aktive rad funktion för hantering av arbets belastning ( `hive.server2.tez.interactive.queue` konfigurationen har inte ställts in i Hive-konfigurationer). <br>
För ett scenario där både Spark-arbetsbelastningar (INSTANSEN) och LLAP-inhemska arbets belastningar finns måste du skapa två separata interaktiva Query-kluster med en delad metaarkiv-databas. Ett kluster för interna LLAP-arbetsbelastningar där WLM-funktionen kan aktive ras på behovs nivå och andra kluster för INSTANSEN arbets belastning där WLM-funktionen inte ska konfigureras.
Det är viktigt att Observera att du kan visa WLM resurs planer från båda klustren även om den är aktive rad i ett enda kluster. Gör inga ändringar i resurs planer i klustret där WLM-funktionen är inaktive rad eftersom den kan påverka WLM-funktionen i andra kluster.

Hive-lagerställen behöver separata kluster för Spark-och Interactive Query-arbetsbelastningar. Följ dessa steg om du vill konfigurera dessa kluster i Azure HDInsight.

### <a name="create-clusters"></a>Skapa kluster

1. Skapa ett HDInsight Spark **4,0** -kluster med ett lagrings konto och ett anpassat virtuellt Azure-nätverk. Information om hur du skapar ett kluster i ett virtuellt Azure-nätverk finns i [lägga till HDInsight i ett befintligt virtuellt nätverk](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).

1. Skapa ett LLAP-kluster (Interactive HDInsight Query) **4,0** med samma lagrings konto och virtuella Azure-nätverk som Spark-klustret.

### <a name="configure-hwc-settings"></a>Konfigurera INSTANSEN-inställningar

#### <a name="gather-preliminary-information"></a>Samla in preliminär information

1. I en webbläsare navigerar du till `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE` där LLAPCLUSTERNAME är namnet på ditt interaktiva fråga-kluster.

1. Gå till **Summary**  >  **HiveServer2 interaktiva JDBC URL** och anteckna värdet. Värdet kan vara ungefär så här: `jdbc:hive2://zk0-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181/;serviceDiscoveryMode=zooKeeper;zooKeeperNamespace=hiveserver2-interactive` .

1. Navigera till **config**  >  **Advanced**  >  **Advanced Hive-site**  >  **Hive. Zookeeper. kvorum** och anteckna värdet. Värdet kan vara ungefär så här: `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181` .

1. Navigera till **config**  >  **Advanced**  >  **General**  >  **Hive. metaarkiv. URI: er** och anteckna värdet. Värdet kan vara ungefär så här: `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083` .

1. Navigera till **config**  >  **Advanced**  >  **Advanced Hive-Interactive-site**  >  **Hive. LLAP. daemon. service. hosts** och anteckna värdet. Värdet kan vara ungefär så här: `@llap0` .

#### <a name="configure-spark-cluster-settings"></a>Konfigurera inställningar för Spark-kluster

1. I en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` där kluster namn är namnet på ditt Apache Spark-kluster.

1. Visa **anpassade spark2-standardvärden**.

    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png" alt-text="Apache Ambari Spark2-konfiguration" border="true":::

1. Välj **Lägg till egenskap...** för att lägga till följande konfigurationer:

    | Konfiguration | Värde |
    |----|----|
    |`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. <br> Ange en lämplig HDFS-kompatibel uppsamlings katalog. Om du har två olika kluster bör mellanlagringsplatsen vara en mapp i uppsamlings katalogen för LLAP-klustrets lagrings konto så att HiveServer2 har åtkomst till den.  Ersätt `STORAGE_ACCOUNT_NAME` med namnet på det lagrings konto som används av klustret och `STORAGE_CONTAINER_NAME` med namnet på lagrings behållaren. |
    |`spark.sql.hive.hiveserver2.jdbc.url`| Värdet som du hämtade tidigare från **HiveServer2-interaktiva JDBC-URL** |
    |`spark.datasource.hive.warehouse.metastoreUri`| Det värde som du hämtade tidigare från **Hive. metaarkiv. URI: er**. |
    |`spark.security.credentials.hiveserver2.enabled`|`true` för garn kluster läge och `false` för garn klient läge. |
    |`spark.hadoop.hive.zookeeper.quorum`| Värdet som du hämtade tidigare från **Hive. Zookeeper. kvorum**. |
    |`spark.hadoop.hive.llap.daemon.service.hosts`| Värdet du hämtade tidigare från **Hive. LLAP. daemon. service. hosts**. |

1. Spara ändringarna och starta om alla påverkade komponenter.

### <a name="configure-hwc-for-enterprise-security-package-esp-clusters"></a>Konfigurera INSTANSEN för Enterprise Security Package (ESP)-kluster

Enterprise Security Package (ESP) tillhandahåller funktioner i företags klass som Active Directory-baserad autentisering, stöd för flera användare och rollbaserad åtkomst kontroll för Apache Hadoop kluster i Azure HDInsight. Mer information om ESP finns i [använda Enterprise Security Package i HDInsight](../domain-joined/apache-domain-joined-architecture.md).

Förutom de konfigurationer som anges i föregående avsnitt lägger du till följande konfiguration för att använda INSTANSEN i ESP-klustren.

1. Från Ambari-webbgränssnittet för Spark-kluster navigerar du till **Spark2**  >  **configs**  >  **Custom Spark2-default**.

1. Uppdatera följande egenskap.

    | Konfiguration | Värde |
    |----|----|
    | `spark.sql.hive.hiveserver2.jdbc.url.principal`    | `hive/<llap-headnode>@<AAD-Domain>` |
    
    * I en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` där kluster namn är namnet på ditt interaktiva fråga-kluster. Klicka på **HiveServer2 Interactive**. Du ser det fullständigt kvalificerade domän namnet (FQDN) för Head-noden där LLAP körs som visas i skärm bilden. Ersätt `<llap-headnode>` med det här värdet.

        :::image type="content" source="./media/apache-hive-warehouse-connector/head-node-hive-server-interactive.png" alt-text="Hive lager kopplings huvud nod" border="true":::

    * Använd [SSH-kommandot](../hdinsight-hadoop-linux-use-ssh-unix.md) för att ansluta till ditt interaktiva fråga-kluster. Sök efter `default_realm` parameter i `/etc/krb5.conf` filen. Ersätt `<AAD-DOMAIN>` med det här värdet som en versal sträng, annars hittas inte autentiseringsuppgiften.

        :::image type="content" source="./media/apache-hive-warehouse-connector/aad-domain.png" alt-text="Hive-domän för Hive-dist." border="true":::

    * Till exempel `hive/hn0-ng36ll.mjry42ikpruuxgs2qy2kpg4q5e.cx.internal.cloudapp.net@PKRSRVUQVMAE6J85.D2.INTERNAL.CLOUDAPP.NET` .
    
1. Spara ändringar och starta om komponenter efter behov.

## <a name="hive-warehouse-connector-usage"></a>Användning av Hive lager koppling

Du kan välja mellan några olika metoder för att ansluta till ditt interaktiva Query-kluster och köra frågor med hjälp av Hive Warehouse-anslutningsprogrammet. De metoder som stöds är följande verktyg:

* [Spark-Shell/PySpark](../spark/apache-spark-shell.md)
* [Spark-Submit](#spark-submit)
* [Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)


Nedan visas några exempel på hur du ansluter till INSTANSEN från Spark.

### <a name="spark-shell"></a>Spark-Shell

1. Använd [SSH-kommandot](../hdinsight-hadoop-linux-use-ssh-unix.md) för att ansluta till ditt Apache Spark-kluster. Redigera kommandot nedan genom att ersätta kluster namn med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Kör följande kommando från SSH-sessionen för att anteckna `hive-warehouse-connector-assembly` versionen:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. Redigera koden nedan med den `hive-warehouse-connector-assembly` version som identifieras ovan. Kör sedan kommandot för att starta Spark-gränssnittet:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. När du har startat Spark-gränssnittet kan du starta en Hive-instans för Hive-lager med följande kommandon:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="spark-submit"></a>Spark-Submit

När du har skapat Scala/Java-koden tillsammans med beroenden till en sammansättning, använder du kommandot nedan för att starta ett Spark-program. Ersätt `<VERSION>` och `<APP_JAR_PATH>` med de faktiska värdena.

* GARN klient läge
    
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode client \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

* Kluster läge för garn
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode cluster \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=true
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

För python lägger du också till följande konfiguration. 

```python
--py-files /usr/hdp/current/hive_warehouse_connector/pyspark_hwc-<VERSION>.zip
```
    
## <a name="run-queries-on-enterprise-security-package-esp-clusters"></a>Kör frågor på Enterprise Security Package-kluster (ESP)

Använd `kinit` innan du startar Spark-Shell eller Spark-Submit. Ersätt USERNAME med namnet på ett domän konto med behörighet att komma åt klustret och kör sedan följande kommando:

```bash
kinit USERNAME
```

### <a name="securing-data-on-spark-esp-clusters"></a>Skydda data i Spark ESP-kluster

1. Skapa en tabell `demo` med några exempel data genom att ange följande kommandon:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Visa tabellens innehåll med följande kommando. Innan du tillämpar principen `demo` visas den fullständiga kolumnen i tabellen.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png" alt-text="demo tabell innan du använder en Ranger-princip" border="true":::

1. Använd en kolumn Maskerings princip som bara visar de sista fyra tecknen i kolumnen.  
    1. Gå till gränssnittet Ranger admin på `https://LLAPCLUSTERNAME.azurehdinsight.net/ranger/` .
    1. Klicka på Hive-tjänsten för klustret under **Hive**.
        :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png" alt-text="Ranger Service Manager" border="true":::
    1. Klicka på fliken **maskering** och **Lägg sedan till ny princip**

        :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png" alt-text="Hive-princip lista för Hive-lager kopplings Ranger" border="true":::

    1. Ange ett önskat princip namn. Välj databas: **standard**, Hive-tabell: **demo**, Hive-kolumn: **namn**, användare: **Rsadmin2**, åtkomst typer: **Select** och **partiell mask: Visa sista 4** från menyn **Välj masknings alternativ** . Klicka på **Lägg till**.
                :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png" alt-text="Skapa princip" border="true":::
1. Visa tabellens innehåll igen. När du har tillämpat Ranger-principen ser vi bara de sista fyra tecknen i kolumnen.

    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png" alt-text="demo tabell efter användning av Ranger-policy" border="true":::

## <a name="next-steps"></a>Nästa steg

* [HWC- och Apache Spark-åtgärder](./apache-hive-warehouse-connector-operations.md)
* [Använda Interactive Query i HDInsight](./apache-interactive-query-get-started.md)
* [HWC-integrering med Apache Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)
* [Exempel på interaktion med Hive-anslutningsprogrammet med Zeppelin, livy, Spark-Submit och pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
