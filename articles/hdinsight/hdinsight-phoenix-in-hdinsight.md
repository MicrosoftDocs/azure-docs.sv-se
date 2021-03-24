---
title: Apache Phoenix i HDInsight – Azure HDInsight
description: Översikt över Apache Phoenix
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: cb5230ae42703d19726fb8ea0d6c88aa70e589a8
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864150"
---
# <a name="apache-phoenix-in-azure-hdinsight"></a>Apache Phoenix i Azure HDInsight

[Apache Phoenix](https://phoenix.apache.org/) är ett stort parallellt Relations databas lager som bygger på [Apache HBase](hbase/apache-hbase-overview.md). Med Phoenix kan du använda SQL-liknande frågor via HBase. Phoenix använder JDBC-drivrutiner för att göra det möjligt för användare att skapa, ta bort, ändra SQL-tabeller, index, vyer och sekvenser och upsert-rader individuellt och i grupp. Phoenix använder noSQL intern kompilering i stället för att använda MapReduce för att kompilera frågor, vilket gör det möjligt att skapa låg latens program ovanpå HBase. Phoenix lägger till processorer som stöder körning av kundlevererad kod i serverns adress utrymme och kör koden som samplacerade med data. Den här metoden minimerar överföring av klient/server-data.

Apache Phoenix öppnar stora data frågor till icke-utvecklare som kan använda en SQL-liknande syntax i stället för programmering. Phoenix är starkt optimerat för HBase, till skillnad från andra verktyg som [Apache Hive](hadoop/hdinsight-use-hive.md) och Apache Spark SQL. Fördelarna med utvecklare skriver hög presterande frågor med mycket mindre kod.

När du skickar en SQL-fråga kompilerar Phoenix frågan till HBase-interna anrop och kör genomsökningen (eller planen) parallellt för optimering. Det här skiktet abstraktion frigör utvecklaren från att skriva MapReduce-jobb, för att fokusera i stället på affärs logiken och arbets flödet för sitt program runt Phoenix: s stora data lagring.

## <a name="query-performance-optimization-and-other-features"></a>Fråga prestanda optimering och andra funktioner

Apache Phoenix lägger till flera prestanda förbättringar och-funktioner för att HBase frågor.

### <a name="secondary-indexes"></a>Sekundära index

HBase har ett enda index som är lexicographically sorterat på den primära rad nyckeln. Dessa poster kan endast nås via rad nyckeln. Om du använder poster via en annan kolumn än rad nyckeln måste du genomsöka alla data när du använder filtret som krävs. I ett sekundärt index, de kolumner eller uttryck som är indexerade utgör en alternativ rad nyckel, vilket gör det möjligt att söka efter och söka efter detta index.

Skapa ett sekundärt index med `CREATE INDEX` kommandot:

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Den här metoden kan ge en betydande prestanda ökning för att köra enstaka indexerade frågor. Den här typen av sekundärt index är ett överordnat **index** som innehåller alla kolumner som ingår i frågan. Därför krävs inte tabells ökningen och indexet uppfyller hela frågan.

### <a name="views"></a>Vyer

Phoenix-vyer ger ett sätt att lösa en HBase begränsning, där prestanda börjar försämras när du skapar fler än cirka 100 fysiska tabeller. Phoenix-vyer gör det möjligt för flera *virtuella tabeller* att dela en underliggande fysisk HBase-tabell.

Att skapa en Phoenix-vy liknar att använda standardsyntaxen för SQL-syntax. En skillnad är att du kan definiera kolumner för vyn, förutom kolumnerna som ärvts från bas tabellen. Du kan också lägga till nya `KeyValue` kolumner.

Här är till exempel en fysisk tabell med namnet `product_metrics` med följande definition:

```sql
CREATE  TABLE product_metrics (
    metric_type CHAR(1),
    created_by VARCHAR,
    created_date DATE,
    metric_id INTEGER
    CONSTRAINT pk PRIMARY KEY (metric_type, created_by, created_date, metric_id));
```

Definiera en vy över den här tabellen med ytterligare kolumner:

```sql
CREATE VIEW mobile_product_metrics (carrier VARCHAR, dropped_calls BIGINT) AS
SELECT * FROM product_metrics
WHERE metric_type = 'm';
```

Om du vill lägga till fler kolumner senare använder du `ALTER VIEW` instruktionen.

### <a name="skip-scan"></a>Hoppa över sökning

Hoppa över sökning använder en eller flera kolumner i ett sammansatt index för att hitta distinkta värden. Till skillnad från en intervalls ökning implementerar Skip-genomsökningen inläsning inom rad, vilket ger [bättre prestanda](https://phoenix.apache.org/performance.html#Skip-Scan). Vid genomsökningen hoppas det första matchade värdet över med indexet tills nästa värde påträffas.

En Skip-genomsökning använder `SEEK_NEXT_USING_HINT` uppräkningen av HBase-filtret. Med hjälp av `SEEK_NEXT_USING_HINT` Skip-genomsökningen kan du spåra vilken uppsättning nycklar eller intervall med nycklar som söks i varje kolumn. Skip-genomsökningen tar sedan en nyckel som skickades till den under filter utvärderingen och avgör om den är en av kombinationerna. Annars utvärderar Skip-skanningen nästa högsta nyckel att gå till.

### <a name="transactions"></a>Transaktioner

Medan HBase tillhandahåller transaktioner på radnivå integreras Phoenix med [Tephra](https://tephra.io/) för att lägga till stöd för transaktioner mellan rader och tabeller över tabeller med fullständig [syra](https://en.wikipedia.org/wiki/ACID) semantik.

Precis som med traditionella SQL-transaktioner kan du med hjälp av transaktions hanteraren i Linköping Se till att en atomisk enhet med data upserted lyckas, återställa transaktionen om upsert-åtgärden Miss lyckas i någon transaktions aktive rad tabell.

Information om hur du aktiverar Phoenix-transaktioner finns i [dokumentationen för Apache Phoenix transaktion](https://phoenix.apache.org/transactions.html).

Om du vill skapa en ny tabell med aktiverade transaktioner anger du `TRANSACTIONAL` egenskapen till `true` i en `CREATE` instruktion:

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Om du vill ändra en befintlig tabell som ska vara transaktionell använder du samma egenskap i en `ALTER` instruktion:

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]  
> Det går inte att ändra en transaktions tabell tillbaka till en icke-transaktionell åtgärd.

### <a name="salted-tables"></a>Saltade tabeller

*Region Server-Hotspotting* kan uppstå när du skriver poster med sekventiella nycklar till HBase. Även om du kan ha flera region servrar i klustret, sker dina skrivningar bara på en. Den här koncentrationen skapar Hotspotting-problemet där, i stället för din Skriv arbets belastning som distribueras på alla tillgängliga region servrar, bara en hanterar belastningen. Eftersom varje region har en fördefinierad Max storlek, delas den upp i två små regioner när en region når den storleks gränsen. När detta sker tar en av dessa nya regioner emot alla nya poster och blir den nya aktiva punkten.

För att minimera det här problemet och uppnå bättre prestanda, så dela tabeller så att alla region servrar används lika bra. Phoenix tillhandahåller *saltade tabeller* och lägger transparent till saltning-byte i rad nyckeln för en viss tabell. Tabellen är i förväg delad på salt byte-gränserna för att säkerställa lika belastnings fördelning mellan region servrar under den inledande fasen i tabellen. Den här metoden distribuerar Skriv arbets belastningen över alla tillgängliga region servrar, vilket förbättrar Skriv-och Läs prestanda. Om du vill salt a tabell anger du `SALT_BUCKETS` tabell egenskapen när tabellen skapas:

```sql
CREATE TABLE Saltedweblogs (
    transactionid varchar(500) Primary Key,
    transactiondate Date NULL,
    customerid varchar(50) NULL,
    bookid varchar(50) NULL,
    purchasetype varchar(50) NULL,
    orderid varchar(50) NULL,
    bookname varchar(50) NULL,
    categoryname varchar(50) NULL,
    invoicenumber varchar(50) NULL,
    invoicestatus varchar(50) NULL,
    city varchar(50) NULL,
    state varchar(50) NULL,
    paymentamount DOUBLE NULL,
    quantity INTEGER NULL,
    shippingamount DOUBLE NULL) SALT_BUCKETS=4;
```

## <a name="enable-and-tune-phoenix-with-apache-ambari"></a>Aktivera och finjustera Phoenix med Apache Ambari

An-HDInsight HBase-kluster innehåller [Ambari-gränssnittet](hdinsight-hadoop-manage-ambari.md) för att göra konfigurations ändringar.

1. Om du vill aktivera eller inaktivera Phoenix och kontrol lera appens timeout-inställningar loggar du in på Ambari-webbgränssnittet ( `https://YOUR_CLUSTER_NAME.azurehdinsight.net` ) med dina Hadoop-användarautentiseringsuppgifter.

2. Välj **HBase** i listan över tjänster i den vänstra menyn och välj fliken **konfigurationer** .

    :::image type="content" source="./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config1.png" alt-text="Apache Ambari HBase-konfigurationer":::

3. Leta upp avsnittet **Phoenix SQL** -konfiguration för att aktivera eller inaktivera Phoenix och ange tids gränsen för frågan.

    :::image type="content" source="./media/hdinsight-phoenix-in-hdinsight/apache-ambari-phoenix.png" alt-text="Ambari Phoenix SQL konfigurations avsnitt":::

## <a name="see-also"></a>Se även

* [Använda Apache Phoenix med Linux-baserade HBase-kluster i HDInsight](hbase/apache-hbase-query-with-phoenix.md)

* [Använda Apache Zeppelin för att köra Apache Phoenix frågor över Apache HBase i Azure HDInsight](./hbase/apache-hbase-phoenix-zeppelin.md)
