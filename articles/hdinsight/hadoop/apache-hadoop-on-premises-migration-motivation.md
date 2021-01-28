---
title: 'Fördelar: migrera lokala Apache Hadoop till Azure HDInsight'
description: Lär dig motivation och fördelarna med att migrera lokala Hadoop-kluster till Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 11/15/2019
ms.openlocfilehash: 975d72df32027888e217d5da9171dba0ba61f257
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943256"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>Migrera lokala Apache Hadoop-kluster till Azure HDInsight – motivation och fördelar

Den här artikeln är den första i en serie med bästa praxis för migrering av lokala Apache Hadoop eko system distributioner till Azure HDInsight. Den här artikel serien är till för personer som är ansvariga för design, distribution och migrering av Apache Hadoop lösningar i Azure HDInsight. De roller som kan dra nytta av dessa artiklar är Cloud Architects, Hadoop-administratörer och DevOps-tekniker. Programutvecklare, data tekniker och data experter bör också dra nytta av förklaringen av hur olika typer av kluster fungerar i molnet.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Varför du ska migrera till Azure HDInsight

Azure HDInsight är en moln distribution av Hadoop-komponenter. Med Azure HDInsight kan du bearbeta stora mängder data på ett enkelt, snabbt och kostnadseffektivt sätt. HDInsight innehåller de mest populära ramverken med öppen källkod, till exempel:

- Apache Hadoop
- Apache Spark
- Apache Hive med LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>Fördelar med Azure HDInsight över lokala Hadoop

- Med **låga** kostnader kan du minska kostnaderna genom att [skapa kluster på begäran](../hdinsight-hadoop-create-linux-clusters-adf.md) och bara betala för det du använder. Frikopplad beräkning och lagring ger flexibilitet genom att hålla data volymen oberoende av kluster storleken.

- **Automatisk skapande av kluster** – automatisk generering av kluster kräver minimal konfiguration och konfiguration. Automation kan användas för kluster på begäran.

- **Hanterad maskin vara och konfiguration** – du behöver inte bekymra dig om den fysiska maskin varan eller infrastrukturen med ett HDInsight-kluster. Ange bara konfigurationen för klustret så konfigurerar Azure det.

- **Enkelt skalbara** – HDInsight gör att du kan [skala](../hdinsight-administer-use-portal-linux.md) upp eller ned arbets belastningar. Azure tar hand om data omdistribution och ombalansering av arbets belastning utan att avbryta data bearbetnings jobb.

- **Global tillgänglighet** – HDInsight är tillgängligt i fler [regioner](https://azure.microsoft.com/regions/services/) än något annat erbjudande för stor data analys. Azure HDInsight är också tillgängligt i Azure Government, Kina och Tyskland så att du kan uppfylla företagets behov i viktiga områden.

- **Säkert och kompatibelt** – HDInsight gör att du kan skydda företagets data till gångar med [Azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md), [kryptering](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)och integrering med [Azure Active Directory](../domain-joined/hdinsight-security-overview.md). HDInsight uppfyller också de vanligaste [efterlevnadskraven](https://azure.microsoft.com/overview/trusted-cloud) för olika branscher och myndigheter.

- **Förenklad versions hantering** – Azure HDInsight hanterar versionen av Hadoop-miljö-system komponenter och håller dem uppdaterad. Program uppdateringar är vanligt vis en komplex process för lokala distributioner.

- **Mindre kluster som är optimerade för vissa arbets belastningar med färre beroenden mellan komponenter** – en typisk lokal Hadoop-installation använder ett enda kluster som hanterar många ändamål. Med Azure HDInsight kan du skapa arbets belastnings bara kluster. Om du skapar kluster för vissa arbets belastningar tar du bort komplexiteten med att underhålla ett enda kluster med ökande komplexitet.

- **Produktivitet** – du kan använda olika verktyg för Hadoop och spark i din önskade utvecklings miljö.

- **Utöknings barhet med anpassade verktyg eller program från tredje part** – HDInsight-kluster kan utökas med installerade komponenter och kan också integreras med de andra Big data-lösningarna genom att använda distributioner med [en enda klickning](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) från Azures marknads plats.

- **Enkel hantering, administration och övervakning** – Azure HDInsight integreras med [Azure Monitor loggar](../hdinsight-hadoop-oms-log-analytics-tutorial.md) för att tillhandahålla ett enda gränssnitt som du kan använda för att övervaka alla dina kluster.

- **Integrering med andra Azure-tjänster** – HDInsight kan enkelt integreras med andra populära Azure-tjänster, till exempel följande:

    - Azure Data Factory (ADF)
    - Azure Blob Storage
    - Azure Data Lake Storage Gen2
    - Azure Cosmos DB
    - Azure SQL Database
    - Azure Analysis Services

- Själv återställnings **processer och-komponenter** – HDInsight kontrollerar ständigt infrastrukturen och komponenter med öppen källkod med hjälp av en egen övervaknings infrastruktur. Den återställer också kritiska problem som inte är tillgängliga för komponenter och noder med öppen källkod. Aviseringar utlöses i Ambari om några OSS-komponenter Miss lyckas.

Mer information finns i artikeln [Vad är Azure HDInsight och Apache Hadoop Technology stack](../hadoop/apache-hadoop-introduction.md).

## <a name="migration-planning-process"></a>Planerings process för migrering

Följande steg rekommenderas för att planera migrering av lokala Hadoop-kluster till Azure HDInsight:

1. Förstå den aktuella lokala distributionen och topologierna.
2. Förstå aktuell projekt omfattning, tids linjer och team kunskaper.
3. Förstå Azure-kraven.
4. Bygg ut en detaljerad plan utifrån metod tips.

## <a name="gathering-details-to-prepare-for-a-migration"></a>Samla in information för att förbereda för migrering

Det här avsnittet innehåller fråge formulär för mallar som hjälper dig att samla in viktig information om:

- Lokal distribution
- Projektinformation
- Krav för Azure

### <a name="on-premises-deployment-questionnaire"></a>Lokalt distributions fråge formulär

| **Fråga** | **Exempel** | **Svar** |
|---|---|---|
|**Ämne**: **miljö**|||
|Kluster distributions version|HDP 2.6.5, CDH 5,7|
|Big data miljö – system komponenter|HDFS, garn, Hive, LLAP, Impala, kudu, HBase, Spark, MapReduce, Kafka, Zookeeper, solr, Sqoop, Oozie, Ranger, Atlas, Falcon, Zeppelin, R|
|Kluster typer|Hadoop, Spark, Kafka, Storm, solr|
|Antal kluster|4|
|Antal huvudnoder|2|
|Antal arbetsnoder|100|
|Antal Edge-noder| 5|
|Totalt disk utrymme|100 TB|
|Konfiguration av huvud nod|d/y, CPU, disk osv.|
|Konfiguration av datanoder|d/y, CPU, disk osv.|
|Konfiguration av Edge-noder|d/y, CPU, disk osv.|
|HDFS-kryptering|Ja|
|Hög tillgänglighet|HDFS HA, Metaarkiv HA|
|Haveri beredskap/säkerhets kopiering|Säkerhetskopiera kluster?|  
|System som är beroende av kluster|SQL Server, Teradata, Power BI, MongoDB|
|Integration från tredje part|Tableau, GridGain, Qubole, Informatica, Splunk|
|**Ämne**: **säkerhet**|||
|Perimeter-säkerhet|Brandväggar|
|Auktorisering av &-autentisering i kluster|Active Directory, Ambari, Cloudera Manager, ingen autentisering|
|HDFS-Access Control|  Manuellt, SSH-användare|
|Auktorisering av Hive-autentisering &|Sentry, LDAP, AD med Kerberos, Ranger|
|Granskning|Ambari, Cloudera Navigator, Ranger|
|Övervakning|Grafsystem, insamling, statistik, teleympkvistar, InfluxDB|
|Aviseringar|Kapacitor, Prometheus, Datadog|
|Varaktighet för datakvarhållning| 3 år, 5 år|
|Kluster administratörer|Enskild administratör, flera administratörer|

### <a name="project-details-questionnaire"></a>Projekt informations enkät

|**Fråga**|**Exempel**|**Svar**|
|---|---|---|
|**Ämne**: **arbets belastningar och frekvens**|||
|MapReduce-jobb|10 jobb – två gånger per dag||
|Hive-jobb|100 jobb – varje timme||
|Spark batch-jobb|50 jobb – var 15: e minut||
|Spark streaming-jobb|5 jobb – var 3: e minut||
|Strukturerade strömmande jobb|5 jobb – varje minut||
|ML modell utbildnings jobb|2 jobb – en gång i veckan||
|Programmeringsspråk|Python, Scala, Java||
|Använda skript|Shell, python||
|**Ämne**: **data**|||
|Datakällor|Flata filer, JSON, Kafka, RDBMS||
|Data dirigering|Oozie-arbetsflöden, luft flöde||
|I minnes sökningar|Apache antändning, Redis||
|Data destinationer|HDFS, RDBMS, Kafka, MPP ||
|**Ämne**: **metadata-data**|||
|Hive DB-typ|MySQL, postgres||
|Antal Hive-metastores|2||
|Antal Hive-tabeller|100||
|Antal Ranger-principer|20||
|Antal Oozie-arbetsflöden|100||
|**Ämne**: **skala**|||
|Data volym, inklusive replikering|100 TB||
|Daglig inmatnings volym|50 GB||
|Data tillväxt takt|10% per år||
|Kluster noders tillväxt takt|5% per år
|**Ämne**: **kluster användning**|||
|Genomsnittlig CPU% som används|60 %||
|Genomsnittligt minne% använt|75 %||
|Använt disk utrymme|75 %||
|Genomsnittligt nätverk% använt|25 %
|**Ämne**: **personal**|||
|Antal administratörer|2||
|Antal utvecklare|10||
|Antal slutanvändare|100||
|Kompetens|Hadoop, Spark||
|Antal tillgängliga resurser för migrerings aktiviteter|2||
|**Ämne**: **begränsningar**|||
|Aktuella begränsningar|Svars tiden är hög||
|Aktuella utmaningar|Samtidiga problem||

### <a name="azure-requirements-questionnaire"></a>Enkät för Azure-krav

|**Fråga**|**Exempel**|**Svar**|
|---|---|---|
|**Ämne**: **infrastruktur** |||
| Önskad region|USA, Östra||
|Virtuellt nätverk?|Ja||
|Krävs/DR?|Ja||
|Integrering med andra moln tjänster?|ADF, CosmosDB||
|**Ämne**:   **data förflyttning**  |||
|Första inläsnings inställning|DistCp, data Box, ADF, WANDisco||
|Data överförings delta|DistCp, AzCopy||
|Kontinuerlig stegvis data överföring|DistCp, Sqoop||
|**Ämne**:   **övervaka & avisering** |||
|Använda Azure Monitoring & varningar vs integrera övervakning från tredje part|Använda Azure Monitoring &-avisering||
|**Ämne**:   **säkerhets inställningar** |||
|Privat och skyddad datapipeline?|Ja||
|Domänanslutna kluster (ESP)?|     Ja||
|Lokalt AD Sync till molnet?|     Ja||
|Antal AD-användare som ska synkroniseras?|          100||
|OK för att synkronisera lösen ord till molnet?|    Ja||
|Endast moln användare?|                 Ja||
|MFA krävs?|                       Inga|| 
|Krav för data auktorisation?|  Ja||
|Rollbaserad åtkomst kontroll?|        Ja||
|Krävs granskning?|                  Ja||
|Data kryptering i vila?|          Ja||
|Data kryptering under överföring?|       Ja||
|**Ämne**:   **åter arkitektur inställningar** |||
|Enstaka kluster och vissa kluster typer|Vissa kluster typer||
|Samplacerad lagring jämfört med Fjärrlagring?|Fjärrlagring||
|Mindre kluster storlek när data lagras via fjärr anslutning?|Mindre kluster storlek||
|Använd flera mindre kluster i stället för ett enda stort kluster?|Använd flera mindre kluster||
|Använd en fjärran sluten metaarkiv?|Ja||
|Vill du dela metastores mellan olika kluster?|Ja||
|Vill du skapa arbets belastningar?|Ersätt Hive-jobb med Spark-jobb||
|Använd ADF för data dirigering?|Nej||

## <a name="next-steps"></a>Nästa steg

Läs nästa artikel i den här serien:

- [Metod tips för arkitektur för lokal att Azure HDInsight Hadoop migrering](apache-hadoop-on-premises-migration-best-practices-architecture.md)
