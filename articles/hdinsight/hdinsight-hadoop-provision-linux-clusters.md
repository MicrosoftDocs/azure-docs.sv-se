---
title: Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera
description: Konfigurera Hadoop-, Kafka-, Spark-, HBase-, R Server- eller Storm-kluster för HDInsight från en webbläsare, klassiska Azure CLI, Azure PowerShell, REST eller SDK.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18, devx-track-azurecli
ms.date: 08/06/2020
ms.openlocfilehash: 3d1059ab46ff0e3722d1f6538bba61cdc4e8cb59
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482695"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Lär dig hur du konfigurerar Apache Hadoop, Apache Spark, Apache Kafka, Interaktiv fråga, Apache HBase, ML Services eller Apache Storm i HDInsight. Lär dig också hur du anpassar kluster och lägger till säkerhet genom att ansluta dem till en domän.

Ett Hadoop-kluster består av flera virtuella datorer (noder) som används för distribuerad bearbetning av uppgifter. Azure HDInsight hanterar implementeringsinformationen för installation och konfiguration av enskilda noder, så du behöver bara ange allmän konfigurationsinformation.

> [!IMPORTANT]  
> Debiteringen för HDInsight-klustret börjar när ett kluster skapas och stoppas när klustret tas bort. Debiteringen görs i förväg per minut, så du ska alltid ta bort ditt kluster när det inte används. Lär dig hur du [tar bort ett kluster.](hdinsight-delete-cluster.md)

Om du använder flera kluster tillsammans bör du skapa ett virtuellt nätverk, och om du använder ett Spark-kluster bör du också använda Hive Warehouse Connector. Mer information finns i [Planera ett virtuellt nätverk för Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md) och Integrera Apache Spark och Apache Hive med [Hive Warehouse Connector](interactive-query/apache-hive-warehouse-connector.md).

## <a name="cluster-setup-methods"></a>Metoder för klusterinstallation

I följande tabell visas de olika metoder som du kan använda för att konfigurera ett HDInsight-kluster.

| Kluster som skapats med | Webbläsare | Kommandorad | REST-API | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Azure-portalen](hdinsight-hadoop-create-linux-clusters-portal.md) |âé" |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |âé" |âé" |âé" |âé" |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |âé" |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |âé" |&nbsp; |&nbsp; |
| [Curl](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |âé" |âé" |&nbsp; |
| [Azure Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |âé" |&nbsp; |&nbsp; |

Den här artikeln går igenom konfigurationen i [Azure Portal](https://portal.azure.com), där du kan skapa ett HDInsight-kluster.

## <a name="basics"></a>Grundläggande inställningar

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png" alt-text="anpassad snabb för hdinsight-alternativ för att skapa":::

### <a name="project-details"></a>Projektinformation

[Azure Resource Manager](../azure-resource-manager/management/overview.md) hjälper dig att arbeta med resurserna i ditt program som en grupp, som kallas en [Azure-resursgrupp.](../azure-resource-manager/management/overview.md#resource-groups) Du kan distribuera, uppdatera, övervaka eller ta bort alla resurser för ditt program i en enda samordnad åtgärd.

### <a name="cluster-details"></a>Klusterinformation

#### <a name="cluster-name"></a>Klusternamn

HDInsight-klusternamn har följande begränsningar:

* Tillåtna tecken: a–z, 0–9, A–Z
* Maxlängd: 59
* Reserverade namn: appar
* Klusternamngivningsomfånget gäller för alla Azure-prenumerationer. Klusternamnet måste därför vara unikt globalt.
* De första sex tecknen måste vara unika i ett virtuellt nätverk

#### <a name="region"></a>Region

Du behöver inte uttryckligen ange klusterplatsen: Klustret finns på samma plats som standardlagringsplatsen. Om du vill se en lista över regioner som stöds väljer **du listrutan Region** i [HDInsight-priser.](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)

#### <a name="cluster-type"></a>Klustertyp

Azure HDInsight tillhandahåller för närvarande följande klustertyper, var och en med en uppsättning komponenter för att tillhandahålla vissa funktioner.

> [!IMPORTANT]  
> HDInsight-kluster är tillgängliga i olika typer, var och en för en enskild arbetsbelastning eller teknik. Det finns ingen metod som stöds för att skapa ett kluster som kombinerar flera typer, till exempel Storm och HBase i ett kluster. Om din lösning kräver tekniker som är utspridda över flera typer av HDInsight-kluster kan ett [virtuellt Azure-nätverk](../virtual-network/index.yml) ansluta de klustertyper som krävs.

| Klustertyp | Funktioner |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Batch-fråga och analys av lagrade data |
| [HBase](hbase/apache-hbase-overview.md) |Bearbetning för stora mängder schemalösa NoSQL-data |
| [Interaktiv fråga](./interactive-query/apache-interactive-query-get-started.md) |Minnescachelagring för interaktiva och snabbare Hive-frågor |
| [Kafka](kafka/apache-kafka-introduction.md) | En distribuerad strömningsplattform som kan användas för att skapa strömmande datapipelines och program i realtid |
| [ML Services](r-server/r-server-overview.md) |Olika stordatastatistik, förutsägelsemodellering och maskininlärningsfunktioner |
| [Spark](spark/apache-spark-overview.md) |Minneshantering, interaktiva frågor, bearbetning av mikrobatchströmmar |
| [Storm](storm/apache-storm-overview.md) |Händelsebearbetning i realtid |

#### <a name="version"></a>Version

Välj version av HDInsight för det här klustret. Mer information finns i [HDInsight-versioner som stöds.](hdinsight-component-versioning.md#supported-hdinsight-versions)

### <a name="cluster-credentials"></a>Autentiseringsuppgifter för kluster

Med HDInsight-kluster kan du konfigurera två användarkonton när klustret skapas:

* Användarnamn för klusterinloggning: Standardnamnet är *admin*. Den använder den grundläggande konfigurationen på Azure Portal. Ibland kallas den för "klusteranvändare" eller "HTTP-användare".
* SSH-användarnamn (Secure Shell): Används för att ansluta till klustret via SSH. Mer information finns i [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

HTTP-användarnamnet har följande begränsningar:

* Tillåtna specialtecken: `_` och `@`
* Tecken tillåts inte: #;."', \/ :'!*?$() {} []<>|&--=+%~^space
* Maxlängd: 20

SSH-användarnamnet har följande begränsningar:

* Tillåtna specialtecken: `_` och `@`
* Tecken tillåts inte: #;."', \/ :'!*?$() {} []<>|&--=+%~^space
* Maxlängd: 64
* Reserverade namn: hadoop, users, oozie, hive, mapred, ambari-qa, zookeeper, tez, hdfs, sqoop, yarn, hcat, ams, hbase, storm, administrator, admin, user, user1, test, user2, test1, user3, admin1, 1, 123, a, actuser, adm, admin2, aspnet, backup, console, david, guest, john, owner, root, server, sql, support, support_388945a0, sys, test2, test3, user4, user5, spark

## <a name="storage"></a>Storage

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png" alt-text="Inställningar för klusterlagring: HDFS-kompatibla slutpunkter":::

Även om en lokal installation av Hadoop använder HDFS (Hadoop Distributed File System) för lagring i klustret använder du lagringsslutpunkter som är anslutna till klustret i molnet. Med molnlagring kan du på ett säkert sätt ta bort HDInsight-kluster som används för beräkning samtidigt som du behåller dina data.

HDInsight-kluster kan använda följande lagringsalternativ:

* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1
* Azure Storage Generell användning v2
* Azure Storage Generell användning v1
* Azure Storage Blockblob **(stöds endast som sekundär lagring)**

Mer information om lagringsalternativ med HDInsight finns i [Jämför lagringsalternativ för användning med Azure HDInsight kluster](hdinsight-hadoop-compare-storage-options.md).

> [!WARNING]  
> Det går inte att använda ytterligare ett lagringskonto på en annan plats än HDInsight-klustret.

Under konfigurationen anger du för standardslutpunkten för lagring en blobcontainer för ett Azure Storage-konto eller Data Lake Storage. Standardlagringen innehåller program- och systemloggar. Du kan också ange ytterligare länkade Azure Storage och Data Lake Storage-konton som klustret kan komma åt. HDInsight-klustret och de beroende lagringskontona måste finnas på samma Azure-plats.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

> [!IMPORTANT]
> Att aktivera säker lagringsöverföring när du har skapat ett kluster kan resultera i fel med ditt lagringskonto och rekommenderas inte. Det är bättre att skapa ett nytt kluster med ett lagringskonto med säker överföring redan aktiverat.

> [!Note]  
> Azure HDInsight överför, flyttar eller kopierar inte automatiskt dina data som lagras i Azure Storage en region till en annan.

### <a name="metastore-settings"></a>Inställningar för metaarkiv

Du kan skapa valfria Hive- eller Apache Oozie-metaarkiv. Alla klustertyper stöder dock inte metaarkiv och Azure Synapse Analytics är inte kompatibla med metaarkiv.

Mer information finns i [Använda externa metadatalager i Azure HDInsight](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> När du skapar ett anpassat metaarkiv ska du inte använda bindestreck, bindestreck eller blanksteg i databasnamnet. Detta kan leda till att processen för att skapa klustret misslyckas.

#### <a name="sql-database-for-hive"></a>SQL-databas för Hive

Om du vill behålla Dina Hive-tabeller när du har tagit bort ett HDInsight-kluster använder du ett anpassat metaarkiv. Du kan sedan koppla metaarkivet till ett annat HDInsight-kluster.

Ett HDInsight-metaarkiv som har skapats för en HDInsight-klusterversion kan inte delas mellan olika HDInsight-klusterversioner. En lista över HDInsight-versioner finns i [HDInsight-versioner som stöds.](hdinsight-component-versioning.md#supported-hdinsight-versions)

> [!IMPORTANT]
> Standardmetaarkivet ger en Azure SQL Database med en **basic-nivå 5 DTU-gräns (kan inte uppgraderas)**! Lämplig för grundläggande testning. För stora arbetsbelastningar eller produktionsarbetsbelastningar rekommenderar vi att du migrerar till ett externt metaarkiv.

#### <a name="sql-database-for-oozie"></a>SQL-databas för Oozie

Om du vill öka prestandan när du använder Oozie använder du ett anpassat metaarkiv. Ett metaarkiv kan också ge åtkomst till Oozie-jobbdata när du har tagit bort klustret.

#### <a name="sql-database-for-ambari"></a>SQL-databas för Ambari

Ambari används för att övervaka HDInsight-kluster, göra konfigurationsändringar och lagra information om klusterhantering samt jobbhistorik. Med den anpassade Ambari DB-funktionen kan du distribuera ett nytt kluster och konfigurera Ambari i en extern databas som du hanterar. Mer information finns i Anpassad [Ambari DB.](./hdinsight-custom-ambari-db.md)

> [!IMPORTANT]  
> Du kan inte återanvända ett anpassat Oozie-metaarkiv. Om du vill använda ett anpassat Oozie-metaarkiv måste du ange en Azure SQL Database när du skapar HDInsight-klustret.

## <a name="security--networking"></a>Säkerhet och nätverk

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png" alt-text="hdinsight-alternativ för att skapa väljer enterprise security package":::

### <a name="enterprise-security-package"></a>Enterprise-säkerhetspaket

För klustertyperna Hadoop, Spark, HBase, Kafka och Interaktiv fråga kan du välja att aktivera **Enterprise Security Package**. Det här paketet ger möjlighet att ha en säkrare klusterkonfiguration med hjälp av Apache Ranger och integrering med Azure Active Directory. Mer information finns i [Översikt över företagssäkerhet i Azure HDInsight](./domain-joined/hdinsight-security-overview.md).

Med enterprise-säkerhetspaketet kan du integrera HDInsight med Active Directory och Apache Ranger. Flera användare kan skapas med hjälp av enterprise-säkerhetspaketet.

Mer information om hur du skapar domän-ansluten HDInsight-kluster finns [i Skapa domän-ansluten HDInsight sandbox-miljö](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).

### <a name="tls"></a>TLS

Mer information finns i [Transport Layer Security](./transport-layer-security.md)

### <a name="virtual-network"></a>Virtuellt nätverk

Om din lösning kräver tekniker som är utspridda över flera TYPER av HDInsight-kluster kan ett [virtuellt Azure-nätverk](../virtual-network/index.yml) ansluta de klustertyper som krävs. Med den här konfigurationen kan klustren och all kod som du distribuerar till dem kommunicera direkt med varandra.

Mer information om hur du använder ett virtuellt Azure-nätverk med HDInsight finns [i Planera ett virtuellt nätverk för HDInsight.](hdinsight-plan-virtual-network-deployment.md)

Ett exempel på hur du använder två klustertyper i ett virtuellt Azure-nätverk finns i [Använda Apache Spark Structured Streaming med Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). Mer information om hur du använder HDInsight med ett virtuellt nätverk, inklusive specifika konfigurationskrav för det virtuella nätverket, finns i Planera ett virtuellt [nätverk för HDInsight.](hdinsight-plan-virtual-network-deployment.md)

### <a name="disk-encryption-setting"></a>Inställning för diskkryptering

Mer information finns i [Diskkryptering med kund hanterad nyckel.](./disk-encryption.md)

### <a name="kafka-rest-proxy"></a>Kafka REST-proxy

Den här inställningen är endast tillgänglig för klustertypen Kafka. Mer information finns i Använda [en REST-proxy](./kafka/rest-proxy.md).

### <a name="identity"></a>Identitet

Mer information finns i [Hanterade identiteter i Azure HDInsight](./hdinsight-managed-identities.md).

## <a name="configuration--pricing"></a>Konfiguration och prissättning

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png" alt-text="HDInsight välj nodstorlek":::

Du debiteras för nodanvändning så länge klustret finns. Faktureringen startar när ett kluster skapas och stoppas när klustret tas bort. Kluster kan inte av allokeras eller stoppas.

### <a name="node-configuration"></a>Nodkonfiguration

Varje klustertyp har ett eget antal noder, terminologi för noder och standardstorlek för virtuella datorer. I följande tabell är antalet noder för varje nodtyp inom parentes.

| Typ | Noder | Diagram |
| --- | --- | --- |
| Hadoop |Huvudnod (2), arbetsnod (1+) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png" alt-text="HDInsight Hadoop-klusternoder" border="false"::: |
| HBase |Huvudserver (2), regionserver (1+), master/ZooKeeper-nod (3) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png" alt-text="Konfiguration av HDInsight HBase-klustertyp" border="false"::: |
| Storm |Nimbus-nod (2), övervakarserver (1+), ZooKeeper-nod (3) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png" alt-text="Installation av HDInsight Storm-klustertyp" border="false"::: |
| Spark |Huvudnod (2), arbetsnod (1+), ZooKeeper-nod (3) (kostnadsfritt för storlek på virtuell A1 ZooKeeper-dator) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png" alt-text="Installation av HDInsight Spark-klustertyp" border="false"::: |

Mer information finns i [Standardnodkonfiguration](hdinsight-supported-node-configuration.md) och storlekar på virtuella datorer för kluster i "Vad är Hadoop-komponenter och versioner i HDInsight?"

Kostnaden för HDInsight-kluster bestäms av antalet noder och de virtuella datorernas storlekar för noderna.

Olika klustertyper har olika nodtyper, antal noder och nodstorlekar:
* Standardtyp för Hadoop-kluster:
    * Två *huvudnoder*
    * Fyra *arbetsnoder*
* Standard för Storm-klustertyp:
    * Två *Nimbus-noder*
    * Tre *ZooKeeper-noder*
    * Fyra *övervakarnoder*

Om du bara provar HDInsight rekommenderar vi att du använder en arbetsnod. Mer information om HDInsight-priser finns i [HDInsight-priser.](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)

> [!NOTE]  
> Storleksgränsen för klustret varierar mellan Olika Azure-prenumerationer. Kontakta [Azure-faktureringssupporten](../azure-portal/supportability/how-to-create-azure-support-request.md) för att öka gränsen.

När du använder Azure Portal för att konfigurera klustret är nodstorleken tillgänglig via **fliken Konfiguration +** prissättning. I portalen kan du också se kostnaden för de olika nodstorlekarna.

### <a name="virtual-machine-sizes"></a>Storlekar för virtuella datorer

När du distribuerar kluster väljer du beräkningsresurser baserat på den lösning som du planerar att distribuera. Följande virtuella datorer används för HDInsight-kluster:

* Virtuella datorer i A- och D1-4-serien: [Storlekar på virtuella Linux-datorer för generell användning](../virtual-machines/sizes-general.md)
* Virtuella datorer i D11-14-serien: [Minnesoptimerade virtuella Linux-datorer](../virtual-machines/sizes-memory.md)

Om du vill ta reda på vilket värde du bör använda för att ange en VM-storlek när du skapar ett kluster med hjälp av olika SDK:er eller när du använder Azure PowerShell kan du se VM-storlekar som ska användas för [HDInsight-kluster.](../cloud-services/cloud-services-sizes-specs.md#size-tables) Från den här länkade artikeln använder du värdet i **kolumnen** Storlek i tabellerna.

> [!IMPORTANT]  
> Om du behöver fler än 32 arbetarnoder i ett kluster måste du välja en huvudnodstorlek med minst 8 kärnor och 14 GB RAM-minne.

Mer information finns i [Storlekar för virtuella datorer.](../virtual-machines/sizes.md) Information om priser för de olika storlekarna finns i [HDInsight-priser.](https://azure.microsoft.com/pricing/details/hdinsight)

### <a name="add-application"></a>Lägga till ett program

Ett HDInsight-program är ett program som användarna kan installera på ett Linux-baserat HDInsight-kluster. Du kan använda program från Microsoft, tredje part eller som du utvecklar själv. Mer information finns i [Installera Apache Hadoop-program från tredje part på Azure HDInsight](hdinsight-apps-install-applications.md).

De flesta HDInsight-programmen är installerade på en tom kantnod.  En tom kantnod är en virtuell Linux-dator med samma klientverktyg installerade och konfigurerade som på huvudnoden. Du kan använda gränsnoden för att komma åt klustret, testa dina klientprogram och vara värd för dina klientprogram. Mer information finns i Använda [tomma kantnoder i HDInsight.](hdinsight-apps-use-edge-node.md)

### <a name="script-actions"></a>Skriptåtgärder

Du kan installera ytterligare komponenter eller anpassa klusterkonfigurationen med hjälp av skript under skapandet. Sådana skript anropas via **skriptåtgärd**, vilket är ett konfigurationsalternativ som kan användas från cmdletarna Azure Portal, HDInsight Windows PowerShell eller HDInsight .NET SDK. Mer information finns i Anpassa [HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md).

Vissa inbyggda Java-komponenter, till exempel Apache Mahout och Sammanhängande, kan köras i klustret som JAR-filer (Java Archive). Dessa JAR-filer kan distribueras till Azure Storage och skickas till HDInsight-kluster med hadoop-jobbinskickningsmekanismer. Mer information finns i Skicka [Apache Hadoop-jobb programmatiskt.](hadoop/submit-apache-hadoop-jobs-programmatically.md)

> [!NOTE]  
> Om du har problem med att distribuera JAR-filer till HDInsight-kluster eller anropa JAR-filer på HDInsight-kluster kontaktar [du Microsoft Support](https://azure.microsoft.com/support/options/).
>
> Sammanhängande stöds inte av HDInsight och är inte berättigat till Microsoft Support. Listor över komponenter som stöds finns [i Vad är nytt i klusterversionerna som tillhandahålls av HDInsight.](hdinsight-component-versioning.md)

Ibland vill du konfigurera följande konfigurationsfiler under skapandeprocessen:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Mer information finns i Anpassa [HDInsight-kluster med Bootstrap.](hdinsight-hadoop-customize-cluster-bootstrap.md)

## <a name="next-steps"></a>Nästa steg

* [Felsöka fel vid skapande av kluster med Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [Vad är HDInsight, Apache Hadoop-ekosystemet och Hadoop-kluster?](hadoop/apache-hadoop-introduction.md)
* [Kom igång med Apache Hadoop i HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Arbeta i Apache Hadoop på HDInsight från en Windows-dator](hdinsight-hadoop-windows-tools.md)
