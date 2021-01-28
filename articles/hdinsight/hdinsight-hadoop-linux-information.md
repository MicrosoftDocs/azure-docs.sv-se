---
title: Tips för att använda Hadoop på Linux-baserade HDInsight – Azure
description: Få implementerings tips för att använda Linux-baserade HDInsight-kluster (Hadoop) i en välbekant Linux-miljö som körs i Azure-molnet.
ms.service: hdinsight
ms.custom: hdinsightactive,seoapr2020
ms.topic: conceptual
ms.date: 04/29/2020
ms.openlocfilehash: 2d2619c7bd7bc09eeab3845599758db7134b4134
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945654"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Information om hur du använder HDInsight på Linux

Azure HDInsight-kluster ger Apache Hadoop på en välbekant Linux-miljö, som körs i Azure-molnet. För de flesta saker bör det fungera exakt som vilken annan Hadoop-on-Linux-installation som helst. Det här dokumentet anropar vissa skillnader som du bör vara medveten om.

## <a name="prerequisites"></a>Förutsättningar

Många av stegen i det här dokumentet använder följande verktyg, som kan behöva installeras i systemet.

* [sväng](https://curl.haxx.se/) -används för att kommunicera med webbaserade tjänster.
* **JQ**, en JSON-processor med kommando rad.  Se [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) .
* [Azure CLI](/cli/azure/install-azure-cli) – används för att fjärrhantera Azure-tjänster.
* **En SSH-klient**. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="users"></a>Användare

Om [den inte är domänansluten bör](./domain-joined/hdinsight-security-overview.md)HDInsight betraktas som ett system med en **användare** . Ett enda SSH-användarkonto skapas med klustret med behörighet som administratörs nivå. Ytterligare SSH-konton kan skapas, men de har också administratörs åtkomst till klustret.

Domänanslutna HDInsight stöder flera användare och mer detaljerade behörigheter och roll inställningar. Mer information finns i [Hantera domänanslutna HDInsight-kluster](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Domännamn

Det fullständigt kvalificerade domän namnet (FQDN) som ska användas vid anslutning till klustret från Internet är `CLUSTERNAME.azurehdinsight.net` eller `CLUSTERNAME-ssh.azurehdinsight.net` (endast för SSH).

Internt har varje nod i klustret ett namn som tilldelas under kluster konfigurationen. Du hittar kluster namnen på sidan **värdar** i Ambari-webbgränssnittet. Du kan också använda följande för att returnera en lista över värdar från Ambari-REST API:

```console
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'
```

Ersätt `CLUSTERNAME` med namnet på klustret. När du uppmanas till det anger du lösen ordet för administratörs kontot. Det här kommandot returnerar ett JSON-dokument som innehåller en lista över värdarna i klustret. [JQ](https://stedolan.github.io/jq/) används för att extrahera `host_name` elementets värde för varje värd.

Om du behöver hitta namnet på noden för en speciell tjänst kan du fråga Ambari för den komponenten. Om du till exempel vill hitta värdarna för noden HDFS Name, använder du följande kommando:

```console
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'
```

Det här kommandot returnerar ett JSON-dokument som beskriver tjänsten och hämtar [](https://stedolan.github.io/jq/) sedan bara `host_name` värdet för värdarna JQ.

## <a name="remote-access-to-services"></a>Fjärråtkomst till tjänster

* **Ambari (webb)** - `https://CLUSTERNAME.azurehdinsight.net`

    Autentisera med hjälp av kluster administratörs användare och lösen ord och logga sedan in på Ambari.

    Autentisering är klartext – Använd alltid HTTPS för att säkerställa att anslutningen är säker.

    > [!IMPORTANT]  
    > Några av de webb UIs som är tillgängliga via Ambari-åtkomst-noder med hjälp av ett internt domän namn. Interna domän namn är inte offentligt tillgängliga via Internet. Fel meddelandet "servern hittades inte" visas när du försöker få åtkomst till vissa funktioner via Internet.
    >
    > Om du vill använda alla funktioner i Ambari-webbgränssnittet använder du en SSH-tunnel för att dirigera webb trafik till klustrets huvud nod. Se [använda SSH-tunnlar för att komma åt Apache Ambari Web UI, ResourceManager, JobHistory, NameNode, Oozie och andra webb-UIS](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** - `https://CLUSTERNAME.azurehdinsight.net/ambari`

    > [!NOTE]  
    > Autentisera med hjälp av kluster administratörs användare och lösen ord.
    >
    > Autentisering är klartext – Använd alltid HTTPS för att säkerställa att anslutningen är säker.

* **WebHCat (Templeton)** - `https://CLUSTERNAME.azurehdinsight.net/templeton`

    > [!NOTE]  
    > Autentisera med hjälp av kluster administratörs användare och lösen ord.
    >
    > Autentisering är klartext – Använd alltid HTTPS för att säkerställa att anslutningen är säker.

* **SSH** -CLUSTERNAME-SSH.azurehdinsight.net på port 22 eller 23. Port 22 används för att ansluta till den primära huvudnoden, medan 23 används för att ansluta till den sekundära. Mer information om huvudnoderna finns i [tillgänglighet och tillförlitlighet för Apache Hadoop kluster i HDInsight](./hdinsight-business-continuity.md).

    > [!NOTE]  
    > Du kan bara komma åt kluster huvud-noder via SSH från en klient dator. När du är ansluten kan du sedan komma åt arbetsnoderna med hjälp av SSH från en huvudnoden.

Mer information finns i [portarna som används av Apache Hadoop Services i HDInsight](hdinsight-hadoop-port-settings-for-services.md) -dokument.

## <a name="file-locations"></a>Sökvägar

Hadoop-relaterade filer hittar du på klusternoderna på `/usr/hdp` . Den här katalogen innehåller följande under kataloger:

* **2.6.5.3009 – 43**: Katalog namnet är den version av Hadoop-plattformen som används av HDInsight. Antalet på klustret kan vara ett annat än det som anges här.
* **aktuell**: den här katalogen innehåller länkar till under kataloger i **2.6.5.3009-43-** katalogen. Katalogen finns så att du inte behöver komma ihåg versions numret.

Du hittar exempel data och JAR-filer på Hadoop Distributed File System på `/example` och `/HdiSamples` .

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS, Azure Storage och Data Lake Storage

I de flesta Hadoop-distributioner lagras data i HDFS. HDFS backas upp av lokal lagring på datorerna i klustret. Användning av lokala lagrings enheter kan vara kostsamt för en molnbaserad lösning där du debiteras per timme eller per minut för beräknings resurser.

När du använder HDInsight lagras datafilerna på ett anpassningsbart och flexibelt sätt i molnet med hjälp av Azure Blob Storage och även Azure Data Lake Storage Gen1/Gen2. Dessa tjänster ger följande fördelar:

* Korttids långsiktig lagring.
* Hjälpmedel från externa tjänster som webbplatser, verktyg för fil uppladdning/hämtning, olika språk-SDK: er och webbläsare.
* Stor fil kapacitet och stort anpassningsbart lagrings utrymme.

Mer information finns i [Azure Blob Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-overview.md)eller [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

När du använder antingen Azure Blob Storage eller Data Lake Storage Gen1/Gen2 behöver du inte göra något särskilt från HDInsight för att komma åt data. Följande kommando listar till exempel filer i `/example/data` mappen, oavsett om de lagras på Azure Storage eller data Lake Storage:

```console
hdfs dfs -ls /example/data
```

I HDInsight frigörs data lagrings resurserna (Azure Blob Storage och Azure Data Lake Storage) från beräknings resurser. Du kan skapa HDInsight-kluster för beräkning efter behov och senare ta bort klustret när arbetet är klart. Samtidigt behåller dina datafiler säkert i moln lagring så länge du behöver.

### <a name="uri-and-scheme"></a><a name="URI-and-scheme"></a>URI och schema

Vissa kommandon kan kräva att du anger schemat som en del av URI: n vid åtkomst till en fil. Till exempel kräver Storm-HDFS-komponenten att du anger schemat. När du använder lagrings utrymme som inte är standard (lagring läggs till som ytterligare lagrings utrymme i klustret), måste du alltid använda schemat som en del av URI: n.

Använd något av följande URI-scheman när du använder [**Azure Storage**](./hdinsight-hadoop-use-blob-storage.md):

* `wasb:///`: Åtkomst till standard lagring med okrypterad kommunikation.

* `wasbs:///`: Åtkomst till standard lagring med krypterad kommunikation.  Wasbs-schemat stöds endast från HDInsight version 3,6 och senare.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Används vid kommunikation med ett lagrings konto som inte är standard. Till exempel när du har ett ytterligare lagrings konto eller när du har åtkomst till data som lagras i ett offentligt tillgängligt lagrings konto.

Använd följande URI-schema när du använder [**Azure Data Lake Storage Gen2**](./hdinsight-hadoop-use-data-lake-storage-gen2.md):

* `abfs://`: Åtkomst till standard lagring med krypterad kommunikation.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: Används vid kommunikation med ett lagrings konto som inte är standard. Till exempel när du har ett ytterligare lagrings konto eller när du har åtkomst till data som lagras i ett offentligt tillgängligt lagrings konto.

Använd något av följande URI-scheman när du använder [**Azure Data Lake Storage gen1**](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md):

* `adl:///`: Åtkomst till standard Data Lake Storage för klustret.

* `adl://<storage-name>.azuredatalakestore.net/`: Används vid kommunikation med ett Data Lake Storage som inte är standard. Används också för att komma åt data utanför rot katalogen i ditt HDInsight-kluster.

> [!IMPORTANT]  
> När du använder Data Lake Storage som standard Arkiv för HDInsight måste du ange en sökväg i arkivet som ska användas som roten för HDInsight-lagring. Standard Sök vägen är `/clusters/<cluster-name>/` .
>
> När `/` du använder eller `adl:///` för att komma åt data, kan du bara komma åt data som lagras i roten (till exempel `/clusters/<cluster-name>/` ) i klustret. Använd formatet för att komma åt data var som helst i arkivet `adl://<storage-name>.azuredatalakestore.net/` .

### <a name="what-storage-is-the-cluster-using"></a>Vilket lagrings utrymme är klustret använder

Du kan använda Ambari för att hämta standard lagrings konfigurationen för klustret. Använd följande kommando för att hämta information om HDFS-konfiguration med hjälp av sväng och filtrera den med [JQ](https://stedolan.github.io/jq/):

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> Det här kommandot returnerar den första konfigurationen som tillämpas på servern ( `service_config_version=1` ) som innehåller den här informationen. Du kan behöva visa en lista över alla konfigurations versioner för att hitta den senaste versionen.

Det här kommandot returnerar ett värde som liknar följande URI: er:

* `wasb://<container-name>@<account-name>.blob.core.windows.net` Om du använder ett Azure Storage-konto.

    Konto namnet är namnet på Azure Storage kontot. Behållar namnet är BLOB-behållaren som är roten i kluster lagringen.

* `adl://home` Om du använder Azure Data Lake Storage. Använd följande REST-anrop för att hämta Data Lake Storage namn:

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    Det här kommandot returnerar följande värdnamn: `<data-lake-store-account-name>.azuredatalakestore.net` .

    Använd följande REST-anrop för att hämta katalogen i arkivet som är roten för HDInsight:

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    Det här kommandot returnerar en sökväg som liknar följande sökväg: `/clusters/<hdinsight-cluster-name>/` .

Du kan också hitta lagrings informationen med hjälp av Azure Portal med hjälp av följande steg:

1. Välj ditt HDInsight-kluster från [Azure Portal](https://portal.azure.com/).

2. I avsnittet **Egenskaper** väljer du **lagrings konton**. Lagrings informationen för klustret visas.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Hur gör jag för att komma åt filer från externa HDInsight

Det finns olika sätt att komma åt data utanför HDInsight-klustret. Följande är några länkar till verktyg och SDK: er som kan användas för att arbeta med dina data:

Om du använder __Azure Blob Storage__ kan du läsa följande länkar för hur du kan komma åt dina data:

* [Azure CLI](/cli/azure/install-az-cli2): Command-Line gränssnitts kommandon för att arbeta med Azure. När du har installerat använder du `az storage` kommandot för att få hjälp med att använda lagring, eller `az storage blob` för BLOB-/regionsspecifika kommandon.
* [blobxfer.py](https://github.com/Azure/blobxfer): ett Python-skript för att arbeta med blobbar i Azure Storage.
* Olika SDK: er:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [Lagring REST API](/rest/api/storageservices/Blob-Service-REST-API)

Om du använder __Azure Data Lake Storage gen1__, se följande länkar för hur du kan komma åt dina data:

* [Webbläsare](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure CLI](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [WebHDFS REST API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Data Lake verktyg för Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling-your-cluster"></a><a name="scaling"></a>Skala klustret

Med funktionen för kluster skalning kan du dynamiskt ändra antalet datanoder som används av ett kluster. Du kan göra skalnings åtgärder medan andra jobb eller processer körs i ett kluster.  Se [skala HDInsight-kluster](./hdinsight-scaling-best-practices.md)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Hur gör jag för att installera nyans (eller en annan Hadoop-komponent)?

HDInsight är en hanterad tjänst. Om Azure identifierar ett problem med klustret kan det ta bort noden som Miss lyckas och skapa en nod för att ersätta den. När du installerar saker manuellt i klustret behålls de inte när den här åtgärden utförs. Använd i stället [HDInsight-skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md). En skript åtgärd kan användas för att göra följande ändringar:

* Installera och konfigurera en tjänst eller webbplats.
* Installera och konfigurera en komponent som kräver konfigurations ändringar på flera noder i klustret.

Skript åtgärder är Bash-skript. Skripten körs när klustret skapas och används för att installera och konfigurera ytterligare komponenter. Information om hur du utvecklar dina egna skriptåtgärder finns i [Utveckling av skriptåtgärder med HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>Jar-filer

Vissa Hadoop-tekniker tillhandahåller fristående jar-filer. De här filerna innehåller funktioner som används som en del av ett MapReduce-jobb eller inifrån gris eller Hive. De kräver ofta inte några inställningar och kan laddas upp till klustret när de har skapats och använts direkt. Om du vill se till att komponenten överleva avbildningen av klustret lagrar du jar-filen i klustrets standard lagring.

Om du till exempel vill använda den senaste versionen av [Apache DataFu](https://datafu.incubator.apache.org/)kan du ladda ned en jar-fil som innehåller projektet och ladda upp den till HDInsight-klustret. Följ sedan DataFu-dokumentationen om hur du använder den från gris eller Hive.

> [!IMPORTANT]  
> Vissa komponenter som är fristående jar-filer tillhandahålls med HDInsight, men finns inte i sökvägen. Om du letar efter en enskild komponent kan du söka efter den i klustret med hjälp av följande:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Det här kommandot returnerar sökvägen till alla matchande jar-filer.

Om du vill använda en annan version av en komponent laddar du upp den version du behöver och använder den i dina jobb.

> [!IMPORTANT]
> Komponenter som ingår i HDInsight-klustret stöds fullt ut och Microsoft Support hjälper till att isolera och lösa problem som rör dessa komponenter.
>
> Anpassade komponenter får kommersiellt rimlig support för att hjälpa dig att ytterligare felsöka problemet. Detta kan resultera i att lösa problemet eller be dig att engagera tillgängliga kanaler för tekniken med öppen källkod där djupgående expertis för tekniken hittas. Det finns till exempel många community-platser som kan användas, till exempel: [Microsoft Q&en fråge sida för HDInsight](/answers/topics/azure-hdinsight.html) [https://stackoverflow.com](https://stackoverflow.com) . Apache-projekt har även projekt webbplatser på [https://apache.org](https://apache.org) , till exempel: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>Nästa steg

* [Hantera HDInsight-kluster med hjälp av Apache Ambari REST API](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda MapReduce-jobb med HDInsight](hadoop/hdinsight-use-mapreduce.md)