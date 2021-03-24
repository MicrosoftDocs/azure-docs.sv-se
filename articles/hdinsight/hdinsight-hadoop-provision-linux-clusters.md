---
title: Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera
description: Konfigurera Hadoop-, Kafka-, Spark-, HBase-, R Server-eller Storm-kluster för HDInsight från en webbläsare, den klassiska Azure CLI, Azure PowerShell, REST eller SDK.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.date: 08/06/2020
ms.openlocfilehash: 04da79509ac40ba870014995d8318a49c98e1ce5
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863419"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Lär dig hur du konfigurerar och konfigurerar Apache Hadoop, Apache Spark, Apache Kafka, interaktiv fråga, Apache HBase, ML-tjänster eller Apache Storm i HDInsight. Lär dig också hur du anpassar kluster och lägger till säkerhet genom att ansluta dem till en domän.

Ett Hadoop-kluster består av flera virtuella datorer (noder) som används för distribuerad bearbetning av aktiviteter. Azure HDInsight hanterar implementerings information om installation och konfiguration av enskilda noder, så du behöver bara ange allmän konfigurations information.

> [!IMPORTANT]  
> Debiteringen för HDInsight-klustret börjar när ett kluster skapas och stoppas när klustret tas bort. Debiteringen görs i förväg per minut, så du ska alltid ta bort ditt kluster när det inte används. Lär dig hur du [tar bort ett kluster.](hdinsight-delete-cluster.md)

Om du använder flera kluster tillsammans, vill du skapa ett virtuellt nätverk och om du använder ett Spark-kluster vill du också använda Hive-lagrets koppling. Mer information finns i [planera ett virtuellt nätverk för Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md) och [integrera Apache Spark och Apache Hive med Hive-lager kopplingen](interactive-query/apache-hive-warehouse-connector.md).

## <a name="cluster-setup-methods"></a>Kluster installations metoder

I följande tabell visas de olika metoder som du kan använda för att skapa ett HDInsight-kluster.

| Kluster som skapats med | Webbläsare | Kommandorad | REST-API | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Azure-portalen](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Klammerparentes](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Azure Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

Den här artikeln vägleder dig genom installationen i [Azure Portal](https://portal.azure.com), där du kan skapa ett HDInsight-kluster.

## <a name="basics"></a>Grundläggande inställningar

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png" alt-text="snabb alternativ för HDInsight-skapande":::

### <a name="project-details"></a>Projektinformation

[Azure Resource Manager](../azure-resource-manager/management/overview.md) hjälper dig att arbeta med resurserna i ditt program som en grupp, som kallas för en Azure- [resurs grupp](../azure-resource-manager/management/overview.md#resource-groups). Du kan distribuera, uppdatera, övervaka eller ta bort alla resurser för ditt program i en enda samordnad åtgärd.

### <a name="cluster-details"></a>Kluster information

#### <a name="cluster-name"></a>Klusternamn

HDInsight-kluster namn har följande begränsningar:

* Tillåtna tecken: a-z, 0-9, A-Z
* Max längd: 59
* Reserverade namn: appar
* Kluster namn området är för alla Azure, i alla prenumerationer. Kluster namnet måste vara unikt i hela världen.
* De första sex tecknen måste vara unika inom ett virtuellt nätverk

#### <a name="region"></a>Region

Du behöver inte ange kluster platsen explicit: klustret är på samma plats som standard lagringen. Om du vill ha en lista över regioner som stöds väljer du List rutan **region** i [priset för HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

#### <a name="cluster-type"></a>Kluster typ

Azure HDInsight tillhandahåller för närvarande följande kluster typer, var och en med en uppsättning komponenter för att tillhandahålla vissa funktioner.

> [!IMPORTANT]  
> HDInsight-kluster är tillgängliga i olika typer, var och en för en enskild arbets belastning eller teknik. Det finns ingen metod som stöds för att skapa ett kluster som kombinerar flera typer, till exempel storm och HBase på ett kluster. Om lösningen kräver tekniker som sprids över flera HDInsight-kluster typer kan ett [virtuellt Azure-nätverk](../virtual-network/index.yml) ansluta de kluster typer som krävs.

| Kluster typ | Funktioner |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Batch-fråga och analys av lagrade data |
| [HBase](hbase/apache-hbase-overview.md) |Bearbetning av stora mängder schema lösta, NoSQL data |
| [Interaktiv fråga](./interactive-query/apache-interactive-query-get-started.md) |Minnes intern cachelagring för interaktiva och snabbare Hive-frågor |
| [Kafka](kafka/apache-kafka-introduction.md) | En distribuerad strömmande plattform som kan användas för att bygga real tids strömnings data pipelines och program |
| [ML Services](r-server/r-server-overview.md) |Olika stor data statistik, förutsägande modellering och maskin inlärnings funktioner |
| [Spark](spark/apache-spark-overview.md) |Minnes intern bearbetning, interaktiva frågor, bearbetning av mikrobatch-dataström |
| [Storm](storm/apache-storm-overview.md) |Händelsebearbetning i realtid |

#### <a name="version"></a>Version

Välj HDInsight-versionen för det här klustret. Mer information finns i [HDInsight-versioner som stöds](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="cluster-credentials"></a>Autentiseringsuppgifter för kluster

Med HDInsight-kluster kan du konfigurera två användar konton när klustret skapas:

* Användar namn för kluster inloggning: standard användar namnet är *admin*. Den grundläggande konfigurationen används på Azure Portal. Ibland kallas det "" kluster användare "eller" HTTP-användare ".
* Secure Shell (SSH)-användar namn: används för att ansluta till klustret via SSH. Mer information finns i [använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

HTTP-användarnamnet har följande begränsningar:

* Tillåtna specialtecken: `_` och `@`
* Tecken som inte tillåts: #;. "', \/ : '! *? $ () {} [] <>|&--= +% ~ ^ Space
* Max längd: 20

SSH-användarnamnet har följande begränsningar:

* Tillåtna specialtecken: `_` och `@`
* Tecken som inte tillåts: #;. "', \/ : '! *? $ () {} [] <>|&--= +% ~ ^ Space
* Max längd: 64
* Reserverade namn: Hadoop, användare, Oozie, Hive, mapred, Ambari-frågor och svar, Zookeeper, tez, HDFS, Sqoop, garn, hcat, AMS, HBase, Storm, administratör, admin, användare, Användare1, test, användare2, TEST1, user3, admin1, 1, 123, actuser, ADM, admin2, ASPNET, säkerhets kopiering, konsol, David, gäst, John, ägare, rot, Server, SQL, support, support_388945a0, sys, TEST2, test3, user4, user5, Spark

## <a name="storage"></a>Storage

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png" alt-text="Kluster lagrings inställningar: HDFS-kompatibla slut punkter":::

Även om en lokal installation av Hadoop använder Hadoop Distributed File System (HDFS) för lagring i klustret, i molnet använder du lagrings slut punkter som är anslutna till klustret. Med hjälp av moln lagring kan du på ett säkert sätt ta bort HDInsight-kluster som används för beräkning samtidigt som dina data fortfarande behålls.

HDInsight-kluster kan använda följande lagrings alternativ:

* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1
* Azure Storage Generell användning v2
* Azure Storage Generell användning v1
* Azure Storage Block-Blob (**stöds endast som sekundär lagring**)

Mer information om lagrings alternativ med HDInsight finns i [jämföra lagrings alternativ för användning med Azure HDInsight-kluster](hdinsight-hadoop-compare-storage-options.md).

> [!WARNING]  
> Det finns inte stöd för att använda ett ytterligare lagrings konto på en annan plats från HDInsight-klustret.

Under konfigurationen för standard lagrings slut punkten anger du en BLOB-behållare för ett Azure Storage konto eller Data Lake Storage. Standard lagringen innehåller program-och system loggar. Alternativt kan du ange ytterligare länkade Azure Storage konton och Data Lake Storage konton som klustret har åtkomst till. HDInsight-klustret och beroende lagrings kontona måste finnas på samma Azure-plats.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

> [!IMPORTANT]
> Aktivering av säker lagrings överföring efter att ett kluster har skapats kan resultera i fel med ditt lagrings konto och rekommenderas inte. Det är bättre att skapa ett nytt kluster med ett lagrings konto med säker överföring redan aktiverat.

> [!Note]  
> Azure HDInsight överför inte automatiskt, flyttar eller kopierar data som lagras i Azure Storage från en region till en annan.

### <a name="metastore-settings"></a>Metaarkiv-inställningar

Du kan skapa valfria Hive-eller Apache Oozie-metastores. Alla kluster typer stöder dock inte metastores, och Azure Synapse Analytics är inte kompatibelt med metastores.

Mer information finns i [använda externa metadata butiker i Azure HDInsight](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> När du skapar en anpassad metaarkiv ska du inte använda bindestreck, bindestreck eller blank steg i databas namnet. Detta kan orsaka att klustrets skapande process kraschar.

#### <a name="sql-database-for-hive"></a>SQL-databas för Hive

Använd en anpassad metaarkiv om du vill behålla Hive-tabellerna när du har tagit bort ett HDInsight-kluster. Du kan sedan ansluta metaarkiv till ett annat HDInsight-kluster.

An-HDInsight metaarkiv som skapas för en HDInsight-kluster version kan inte delas mellan olika HDInsight-kluster versioner. En lista över HDInsight-versioner finns i [HDInsight-versioner som stöds](hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> Standard-metaarkiv tillhandahåller en Azure SQL Database med en **grundläggande nivå 5-DTU-gräns (inte uppgraderings bara)**! Lämpligt för grundläggande testnings ändamål. För stora eller produktions arbets belastningar rekommenderar vi att du migrerar till en extern metaarkiv.

#### <a name="sql-database-for-oozie"></a>SQL Database för Oozie

Använd en anpassad metaarkiv för att öka prestandan när du använder Oozie. En metaarkiv kan också ge åtkomst till Oozie-jobb data när du har tagit bort klustret.

#### <a name="sql-database-for-ambari"></a>SQL Database för Ambari

Ambari används för att övervaka HDInsight-kluster, göra konfigurations ändringar och lagra kluster hanterings information, samt jobb historik. Med den anpassade Ambari DB-funktionen kan du distribuera ett nytt kluster och konfigurera Ambari i en extern databas som du hanterar. Mer information finns i [Custom AMBARI DB](./hdinsight-custom-ambari-db.md).

> [!IMPORTANT]  
> Du kan inte återanvända en anpassad Oozie-metaarkiv. Om du vill använda en anpassad Oozie-metaarkiv måste du ange en tom Azure SQL Database när du skapar HDInsight-klustret.

## <a name="security--networking"></a>Säkerhet + nätverk

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png" alt-text="HDInsight-alternativ för skapande väljer Enterprise Security-paket":::

### <a name="enterprise-security-package"></a>Säkerhets paket för företag

För Hadoop-, Spark-, HBase-, Kafka-och interaktiv fråga-kluster typer kan du välja att aktivera **Enterprise Security Package**. Det här paketet ger möjlighet att ha en säkrare kluster konfiguration genom att använda Apache Ranger och integrera med Azure Active Directory. Mer information finns i [Översikt över företags säkerhet i Azure HDInsight](./domain-joined/hdinsight-security-overview.md).

Med säkerhets paketet för företag kan du integrera HDInsight med Active Directory och Apache Ranger. Flera användare kan skapas med hjälp av Enterprise Security-paketet.

Mer information om hur du skapar domänanslutna HDInsight-kluster finns i avsnittet [skapa domänansluten HDInsight-miljö](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).

### <a name="tls"></a>TLS

Mer information finns i [Transport Layer Security](./transport-layer-security.md)

### <a name="virtual-network"></a>Virtuellt nätverk

Om lösningen kräver tekniker som sprids över flera HDInsight-kluster typer kan ett [virtuellt Azure-nätverk](../virtual-network/index.yml) ansluta de kluster typer som krävs. Den här konfigurationen tillåter kluster och all kod som du distribuerar till dem för att kommunicera direkt med varandra.

Mer information om hur du använder ett virtuellt Azure-nätverk med HDInsight finns i [planera ett virtuellt nätverk för HDInsight](hdinsight-plan-virtual-network-deployment.md).

Ett exempel på hur du använder två kluster typer i ett virtuellt Azure-nätverk finns i [använda Apache Spark strukturerad strömning med Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). Mer information om hur du använder HDInsight med ett virtuellt nätverk, inklusive särskilda konfigurations krav för det virtuella nätverket, finns i [planera ett virtuellt nätverk för HDInsight](hdinsight-plan-virtual-network-deployment.md).

### <a name="disk-encryption-setting"></a>Disk krypterings inställning

Mer information finns i [kund hanterad nyckel disk kryptering](./disk-encryption.md).

### <a name="kafka-rest-proxy"></a>Kafka REST-proxy

Den här inställningen är endast tillgänglig för kluster typen Kafka. Mer information finns i [använda en rest-proxy](./kafka/rest-proxy.md).

### <a name="identity"></a>Identitet

Mer information finns i [hanterade identiteter i Azure HDInsight](./hdinsight-managed-identities.md).

## <a name="configuration--pricing"></a>Konfiguration + prissättning

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png" alt-text="HDInsight Välj Node-storlek":::

Du debiteras för användning av Node så länge klustret finns. Faktureringen startar när ett kluster skapas och stoppas när klustret tas bort. Kluster kan inte avallokeras eller spärras.

### <a name="node-configuration"></a>Nodkonfiguration

Varje kluster typ har sitt eget antal noder, terminologi för noder och standard storlek för virtuell dator. I följande tabell är antalet noder för varje nodtyp inom parentes.

| Typ | Noder | Diagram |
| --- | --- | --- |
| Hadoop |Head-nod (2), arbetsnoden (1 +) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png" alt-text="HDInsight Hadoop-klusternoder" border="false"::: |
| HBase |Huvud server (2), region Server (1 +), Master/ZooKeeper-nod (3) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png" alt-text="Konfiguration av HDInsight HBase-kluster typ" border="false"::: |
| Storm |Nimbus Node (2), övervaknings Server (1 +), ZooKeeper Node (3) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png" alt-text="Konfiguration av HDInsight Storm kluster typ" border="false"::: |
| Spark |Head Node (2), arbetsnoden (1 +), ZooKeeper Node (3) (kostnads fri för a1 ZooKeeper VM-storlek) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png" alt-text="Konfiguration av HDInsight Spark kluster typ" border="false"::: |

Mer information finns i [standard konfiguration av noder och virtuella dator storlekar för kluster](hdinsight-supported-node-configuration.md) i "Vad är Hadoop-komponenter och-versioner i HDInsight?"

Kostnaden för HDInsight-kluster bestäms av antalet noder och storlekarna för virtuella datorer för noderna.

Olika typer av kluster har olika nodtyper, antal noder och Node-storlekar:
* Standardvärde för Hadoop-kluster typ:
    * Två *Head-noder*
    * Fyra *arbetsnoder*
* Standard Storm kluster typ:
    * Två *Nimbus-noder*
    * Tre *ZooKeeper-noder*
    * Fyra *överordnade noder*

Om du bara har försökt med HDInsight rekommenderar vi att du använder en arbetsnod. Mer information om priser för HDInsight finns i avsnittet om [priser för HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]  
> Kluster storleks gränsen varierar mellan Azure-prenumerationer. Kontakta [Azures fakturerings support](../azure-portal/supportability/how-to-create-azure-support-request.md) för att öka gränsen.

När du använder Azure Portal för att konfigurera klustret, är nodens storlek tillgänglig via fliken **konfiguration + prissättning** . I portalen kan du också se kostnaden som är associerad med de olika nodernas storlekar.

### <a name="virtual-machine-sizes"></a>Storlekar för virtuella datorer

När du distribuerar kluster väljer du beräknings resurser baserat på den lösning som du planerar att distribuera. Följande virtuella datorer används för HDInsight-kluster:

* Virtuella datorer i och D1-4-serien: [allmänna Linux VM-storlekar](../virtual-machines/sizes-general.md)
* D11 – 14-seriens virtuella datorer: [MINNESOPTIMERADE virtuella Linux-storlekar](../virtual-machines/sizes-memory.md)

För att ta reda på vilket värde du ska använda för att ange en VM-storlek när du skapar ett kluster med hjälp av de olika SDK: erna eller när du använder Azure PowerShell, se [storleken på virtuella datorer som används för HDInsight-kluster](../cloud-services/cloud-services-sizes-specs.md#size-tables). I den här länkade artikeln använder du värdet i kolumnen **storlek** i tabellerna.

> [!IMPORTANT]  
> Om du behöver fler än 32 arbetsnoder i ett kluster måste du välja en huvudnods storlek med minst 8 kärnor och 14 GB RAM.

Mer information finns i [storlekar för virtuella datorer](../virtual-machines/sizes.md). Information om priser för de olika storlekarna finns i avsnittet om [priser för HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).

### <a name="add-application"></a>Lägga till ett program

Ett HDInsight-program är ett program som användarna kan installera på ett Linux-baserat HDInsight-kluster. Du kan använda program från Microsoft, tredje part eller som du utvecklar själv. Mer information finns i [installera Apache Hadoop program från tredje part i Azure HDInsight](hdinsight-apps-install-applications.md).

De flesta av HDInsight-programmen installeras på en tom Edge-nod.  En tom Edge-nod är en virtuell Linux-dator med samma klient verktyg installerade och konfigurerade som i head-noden. Du kan använda Edge-noden för att komma åt klustret, testa dina klient program och vara värd för dina klient program. Mer information finns i [använda tomma Edge-noder i HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="script-actions"></a>Skript åtgärder

Du kan installera ytterligare komponenter eller anpassa kluster konfigurationen genom att använda skript när du skapar. Sådana skript anropas via **skript åtgärd**, vilket är ett konfigurations alternativ som kan användas från Azure Portal, HDInsight Windows PowerShell-cmdletar eller HDInsight .NET SDK. Mer information finns i [Anpassa HDInsight-kluster med skript åtgärd](hdinsight-hadoop-customize-cluster-linux.md).

Vissa inbyggda Java-komponenter, t. ex. apache Mahout och överlappande, kan köras på klustret som filer för Java-arkiv (JAR). Dessa JAR-filer kan distribueras till Azure Storage och skickas till HDInsight-kluster med insändnings metoder för Hadoop-jobb. Mer information finns i [skicka Apache Hadoop jobb program mässigt](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]  
> Kontakta [Microsoft Support](https://azure.microsoft.com/support/options/)om du har problem med att distribuera jar-filer till HDInsight-kluster eller anropa jar-filer på HDInsight-kluster.
>
> Överlappande stöds inte av HDInsight och är inte giltigt för Microsoft Support. Listor över komponenter som stöds finns i [Nyheter i de kluster versioner som tillhandahålls av HDInsight](hdinsight-component-versioning.md).

Ibland vill du konfigurera följande konfigurationsfiler under skapande processen:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred – plats
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Mer information finns i [Anpassa HDInsight-kluster med hjälp av bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="next-steps"></a>Nästa steg

* [Felsöka kluster skapande fel med Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [Vad är HDInsight, Apache Hadoop eko system och Hadoop-kluster?](hadoop/apache-hadoop-introduction.md)
* [Kom igång med att använda Apache Hadoop i HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Arbeta i Apache Hadoop på HDInsight från en Windows-dator](hdinsight-hadoop-windows-tools.md)