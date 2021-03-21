---
title: 'Lagring: migrera lokala Apache Hadoop till Azure HDInsight'
description: Lär dig metod tips för lagring för migrering av lokala Hadoop-kluster till Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: 89a2a659e195afcae336c930a101ed0c0b470cb4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715419"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight"></a>Migrera lokala Apache Hadoop-kluster till Azure HDInsight

Den här artikeln innehåller rekommendationer för data lagring i Azure HDInsight-system. Den ingår i en serie som ger bästa praxis för att hjälpa till att migrera lokala Apache Hadoop system till Azure HDInsight.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>Välj rätt lagrings system för HDInsight-kluster

Katalog strukturen för den lokala Apache Hadoop fil systemet (HDFS) kan återskapas i Azure Blob Storage eller Azure Data Lake Storage. Du kan sedan på ett säkert sätt ta bort HDInsight-kluster som används för beräkning utan att förlora användar data. Båda tjänsterna kan användas både som standard fil system och ytterligare fil system för ett HDInsight-kluster. HDInsight-klustret och lagrings kontot måste finnas i samma region.

### <a name="azure-storage"></a>Azure Storage

HDInsight-kluster kan använda BLOB-behållaren i Azure Storage som antingen standard fil systemet eller ett ytterligare fil system. Lagrings kontot på standard nivån stöds för användning med HDInsight-kluster. Premier-nivån stöds inte. Standardcontainern lagrar klusterspecifik information, till exempel jobbhistorik och loggar. Delning av en BLOB-behållare som standard fil system för flera kluster stöds inte.

De lagrings konton som definieras i skapande processen och deras respektive nycklar lagras `%HADOOP_HOME%/conf/core-site.xml` på klusternoderna. De kan också nås under avsnittet "anpassad kärn webbplats" i HDFS-konfigurationen i Ambari-ANVÄNDARGRÄNSSNITTET. Lagrings konto nyckeln krypteras som standard och ett anpassat dekrypterings skript används för att dekryptera nycklarna innan de skickas vidare till Hadoop-daemon. Jobben, inklusive Hive, MapReduce, Hadoop-strömning och gris, har en beskrivning av lagrings konton och metadata med dem.

Azure Storage kan vara geo-replikerad. Även om geo-replikering ger geografisk återställning och dataredundans, påverkar en redundansväxling till den geo-replikerade platsen prestandan, och det kan medföra ytterligare kostnader. Rekommendationen är att välja geo-replikeringen och endast om värdet för data är värda den extra kostnaden.

Ett av följande format kan användas för att komma åt data som lagras i Azure Storage:

|Data åtkomst format |Beskrivning |
|---|---|
|`wasb:///`|Få åtkomst till standard lagring med okrypterad kommunikation.|
|`wasbs:///`|Få åtkomst till standard lagring med krypterad kommunikation.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|Används vid kommunikation med ett lagrings konto som inte är standard. |

[Skalbarhets mål för standard lagrings konton](../../storage/common/scalability-targets-standard-account.md) visar de aktuella gränserna för Azure Storage-konton. Om programmets behov överskrider skalbarhets målen för ett enda lagrings konto kan programmet byggas för att använda flera lagrings konton och sedan partitionera data objekt över dessa lagrings konton.

[Azure-lagringsanalys](../../storage/common/storage-analytics.md) tillhandahåller mått för alla lagrings tjänster och Azure Portal kan konfigureras samla in mått som ska visualiseras genom diagram. Aviseringar kan skapas för att meddela när tröskelvärden har nåtts för lagrings resurs mått.

Azure Storage erbjuder [mjuk borttagning för BLOB-objekt](../../storage/blobs/soft-delete-blob-overview.md) för att hjälpa till att återställa data när de av misstag har ändrats eller tagits bort av ett program eller en annan lagrings konto användare.

Du kan skapa [BLOB-ögonblicksbilder](/rest/api/storageservices/creating-a-snapshot-of-a-blob). En ögonblicks bild är en skrivskyddad version av en blob som tas vid en tidpunkt och är ett sätt att säkerhetskopiera en blob. När en ögonblicks bild har skapats kan den läsas, kopieras eller tas bort, men inte ändras.

> [!Note]
> För äldre versioner av lokala Hadoop-distributioner som inte har certifikatet "wasbs" måste de importeras till Java Trust Store.

Följande metoder kan användas för att importera certifikat till Java Trust Store:

Hämta Azure Blob TLS/SSL-certifikatet till en fil

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

Importera ovanstående fil till Java Trust Store på alla noder

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Kontrol lera att det tillagda certifikatet finns i förtroende arkivet

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

Mer information finns i följande artiklar:

- [Använda Azure Storage med Azure HDInsight-kluster](../hdinsight-hadoop-use-blob-storage.md)
- [Skalbarhets mål för standard lagrings konton](../../storage/common/scalability-targets-standard-account.md)
- [Skalbarhets-och prestanda mål för Blob Storage](../../storage/blobs/scalability-targets.md)
- [Prestanda och skalbarhetschecklista för Microsoft Azure Storage](../../storage/blobs/storage-performance-checklist.md)
- [Övervaka, diagnostisera och felsök Microsoft Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Övervaka ett lagringskonto i Azure-portalen](../../storage/common/manage-storage-analytics-logs.md)

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 implementerar HDFS-och POSIX-stilens åtkomst kontroll modell. Den ger den första klass integreringen med Azure AD för detaljerad åtkomst kontroll. Det finns ingen gräns för storleken på de data som kan lagras, eller dess förmåga att köra massivt parallell analys.

Mer information finns i följande artiklar:

- [Skapa HDInsight-kluster med Data Lake Storage Gen1 med hjälp av Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Använda Data Lake Storage Gen1 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen1.md)

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 är det senaste lagrings erbjudandet. Den kombinerar kärn funktionerna från den första generationen Azure Data Lake Storage Gen1 med en Hadoop-kompatibel fil system slut punkt som är direkt integrerad i Azure Blob Storage. Den här förbättringen kombinerar skalnings-och kostnads fördelarna med objekt lagring med tillförlitlighet och prestanda som vanligt vis endast är kopplade till lokala fil system.

Azure Data Lake Storage gen 2 bygger på [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md) och gör att du kan gränssnitt med data med hjälp av både fil systemet och objekt lagrings paradigm. Funktioner från [Azure Data Lake Storage gen1](../../data-lake-store/index.yml), till exempel semantiska fil system, säkerhet på filnivå och skala kombineras med låg kostnad, nivå lagring, hög tillgänglighet/haveri beredskap och ett stort eko system för SDK/verktyg från [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md). I Data Lake Storage Gen2 bibehålls alla kvaliteter för objekt lagring samtidigt som du lägger till fördelarna med ett fil system gränssnitt som är optimerat för analys arbets belastningar.

En grundläggande funktion i Data Lake Storage Gen2 är att lägga till ett [hierarkiskt namn område](../../storage/blobs/data-lake-storage-namespace.md) till Blob Storage-tjänsten, som organiserar objekt/filer i en hierarki med kataloger för att utföra data åtkomst. Den hierarkiska strukturen aktiverar åtgärder som att byta namn på eller ta bort en katalog för att vara enkla atomiska metadata-åtgärder i katalogen i stället för att räkna upp och bearbeta alla objekt som delar namn prefixet för katalogen.

Tidigare var molnbaserad analys en kompromiss i områden med prestanda, hantering och säkerhet. De viktigaste funktionerna i Azure Data Lake Storage (ADLS) Gen2 är följande:

- **Hadoop-kompatibel åtkomst**: Azure Data Lake Storage Gen2 gör att du kan hantera och komma åt data precis som med en [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Den nya [ABFS-drivrutinen](../../storage/blobs/data-lake-storage-abfs-driver.md) är tillgänglig i alla Apache Hadoop miljöer som ingår i [Azure HDInsight](../index.yml). Med den här driv rutinen kan du komma åt data som lagras i Data Lake Storage Gen2.

- **En supermängd av POSIX-behörigheter**: säkerhets modellen för data Lake Gen2 har fullt stöd för ACL-och POSIX-behörigheter tillsammans med viss detaljerad kornig het för data Lake Storage Gen2. Inställningarna kan konfigureras via administrations verktyg eller ramverk som Hive och Spark.

- **Kostnads effektiv**: data Lake Storage Gen2 funktioner för lagrings kapacitet och transaktioner med låg kostnad. Som data över gångar via hela livs cykeln ändras fakturerings taxan till minimerade kostnader via inbyggda funktioner, till exempel [Azure Blob Storage livs cykel](../../storage/blobs/storage-lifecycle-management-concepts.md).

- **Fungerar med Blob Storage-verktyg, ramverk och appar**: data Lake Storage Gen2 fortsätter att fungera med en bred uppsättning verktyg, ramverk och program som finns idag för Blob Storage.

- **Optimerad driv rutin**: Azure Blob filesystem-drivrutinen (ABFS) är [optimerad](../../storage/blobs/data-lake-storage-abfs-driver.md) för stor data analys. Motsvarande REST-API: er är anslutna via DFS-slutpunkten dfs.core.windows.net.

Ett av följande format kan användas för att komma åt data som lagras i ADLS Gen2:
- `abfs:///`: Åtkomst till standard Data Lake Storage för klustret.
- `abfs://file_system@account_name.dfs.core.windows.net`: Används vid kommunikation med ett Data Lake Storage som inte är standard.

Mer information finns i följande artiklar:

- [Introduktion till Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md)
- [Azure Blob filesystem-drivrutinen (ABFS.md)](../../storage/blobs/data-lake-storage-abfs-driver.md)
- [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>Skydda Azure Storage nycklar i konfiguration av lokalt Hadoop-kluster

Azure Storage nycklar som läggs till i Hadoop-konfigurationsfilerna och upprättar anslutningen mellan lokalt HDFS och Azure Blob Storage. Dessa nycklar kan skyddas genom att du krypterar dem med ramverket Hadoop Credential Provider. När krypteringen är krypterad kan de lagras och nås på ett säkert sätt.

**Så här etablerar du autentiseringsuppgifterna:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**Så här lägger du till sökvägen ovan till core-site.xml eller till Ambari-konfigurationen under anpassad Core-site:**

```xml
<property>
    <name>hadoop.security.credential.provider.path</name>
        <value>
        jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks
        </value>
    <description>Path to interrogate for protected credentials.</description>
</property>
```

> [!Note]
> Du kan också lägga till egenskapen för provider-sökvägen i Distcp-kommando raden i stället för att lagra nyckeln på kluster nivå på core-site.xml enligt följande:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>Begränsa Azure Storage data åtkomst med hjälp av SAS

HDInsight har som standard fullständig åtkomst till data i de Azure Storage konton som är kopplade till klustret. Signaturer för delad åtkomst (SAS) i BLOB-behållaren kan användas för att begränsa åtkomsten till data, t. ex. ge användare skrivskyddad åtkomst till data.

### <a name="using-the-sas-token-created-with-python"></a>Använda SAS-token som skapats med python

1. Öppna filen  [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) och ändra följande värden:

    |Egenskap för token|Beskrivning|
    |---|---|
    |policy_name|Namnet som ska användas för den lagrade principen som ska skapas.|
    |storage_account_name|Namnet på ditt lagringskonto.|
    |storage_account_key|Nyckeln för lagrings kontot.|
    |storage_container_name|Den behållare i lagrings kontot som du vill begränsa åtkomsten till.|
    |example_file_path|Sökvägen till en fil som överförs till behållaren.|

2. SASToken.py-filen innehåller `ContainerPermissions.READ + ContainerPermissions.LIST` behörigheter och kan justeras baserat på användnings fall.

3. Kör skriptet på följande sätt: `python SASToken.py`

4. Den visar SAS-token som liknar följande text när skriptet har slutförts: `sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. Om du vill begränsa åtkomsten till en behållare med signaturen för delad åtkomst lägger du till en anpassad post i konfigurationen för kärn platsen för klustret under Ambari HDFS configs Avancerat anpassad Core-plats Lägg till egenskap.

6. Använd följande värden för fälten **nyckel** och **värde** :

    **Nyckel**: `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **värde**: den SAS-nyckel som returnerades av python-programmet från steg 4 ovan.

7. Klicka på knappen **Lägg** till för att spara den här nyckeln och värdet och klicka sedan på knappen **Spara** för att spara konfigurations ändringarna. När du uppmanas till det, lägger du till en beskrivning av ändringen ("lägga till SAS-lagringsenhet" till exempel) och klickar sedan på **Spara**.

8. I Ambari-webbgränssnittet väljer du HDFS i listan till vänster och väljer sedan **starta om alla som påverkas** från List rutan service åtgärder till höger. När du uppmanas väljer du **Bekräfta omstart av alla**.

9. Upprepa den här processen för MapReduce2 och garn.

Det finns tre viktiga saker att komma ihåg om användningen av SAS-token i Azure:

1. När SAS-token skapas med behörigheterna Läs + lista kommer användare som har åtkomst till BLOB-behållaren med den SAS-token inte att kunna skriva och ta bort data. Användare som har åtkomst till BLOB-behållaren med den SAS-token och försöker utföra en Skriv-eller borttagnings åtgärd, kommer att få ett meddelande som `"This request is not authorized to perform this operation"` .

2. När SAS-token genereras med `READ + LIST + WRITE` behörigheter (begränsa `DELETE` endast), kommandon som `hadoop fs -put` första skrivning till en `\_COPYING\_` fil och försök sedan att byta namn på filen. Denna HDFS-åtgärd mappas till en `copy+delete` for WASB. Eftersom `DELETE` behörigheten inte angavs skulle "placering" att fungera. `\_COPYING\_`Åtgärden är en Hadoop-funktion som är avsedd att ge en viss samtidighets kontroll. För närvarande finns det inget sätt att begränsa bara åtgärden "ta bort" utan att även påverka "Skriv"-åtgärder.

3. Tyvärr fungerar inte ShellDecryptionKeyProvider-providern (Hadoop Credential Provider och dekrypteringsnyckel) med SAS-token och så den för närvarande inte kan skyddas från synlighet.

Mer information finns i [Azure Storage använda signaturer för delad åtkomst för att begränsa åtkomsten till data i HDInsight](../hdinsight-storage-sharedaccesssignature-permissions.md).

## <a name="use-data-encryption-and-replication"></a>Använd data kryptering och replikering

Alla data som skrivs till Azure Storage krypteras automatiskt med hjälp av [kryptering för lagringstjänst (SSE)](../../storage/common/storage-service-encryption.md). Data i Azure Storage-kontot replikeras alltid för hög tillgänglighet. När du skapar ett lagrings konto kan du välja något av följande replikeringsalternativ:

- [Lokalt redundant lagring (LRS)](../../storage/common/storage-redundancy.md#locally-redundant-storage)
- [Zonredundant lagring (ZRS)](../../storage/common/storage-redundancy.md#zone-redundant-storage)
- [Geo-redundant lagring (GRS)](../../storage/common/storage-redundancy.md#geo-redundant-storage)
- [Geo-redundant lagring med läsbehörighet (RA-GRS)](../../storage/common/storage-redundancy.md)

Azure Storage tillhandahåller lokalt redundant lagring (LRS), men du bör också kopiera viktiga data till ett annat Azure Storage konto i en annan region med en frekvens som är anpassad till behoven hos Disaster Recovery-planen. Det finns olika metoder för att kopiera data, inklusive [ADLCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md), [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html), [Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md)eller [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md). Vi rekommenderar också att du tillämpar åtkomst principer för Azure Storage konto för att förhindra oavsiktlig borttagning.

Mer information finns i följande artiklar:

- [Azure Storage-replikering](../../storage/common/storage-redundancy.md)
- [Katastrof vägledning för Azure Data Lake Storage Gen1 (ADLS)](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>Bifoga ytterligare Azure Storage konton till klustret

Under skapande processen av HDInsight kan ett Azure Storage konto, Azure Data Lake Storage Gen1 eller Azure Data Lake Storage Gen2 väljas som standard fil system. Förutom det här standard lagrings kontot kan ytterligare lagrings konton läggas till från samma Azure-prenumeration eller andra Azure-prenumerationer under skapande processen för klustret eller efter att ett kluster har skapats.

Du kan lägga till ytterligare lagrings konto på något av följande sätt:
- Ambari HDFS config Advanced Custom Core-site Lägg till lagrings kontots namn och nyckel som startar om tjänsterna
- Använd [skript åtgärd](../hdinsight-hadoop-add-storage.md) genom att skicka lagrings kontots namn och nyckel

> [!Note]
> I giltiga användnings fall kan gränserna för Azure-lagring ökas via en begäran till [Azure-supporten](https://azure.microsoft.com/support/faq/).

Mer information finns i [Add additional storage accounts to HDInsight](../hdinsight-hadoop-add-storage.md) (Lägga till fler lagringskonton till HDInsight).

## <a name="next-steps"></a>Nästa steg

Läs nästa artikel i den här serien: [metod tips för datamigrering för lokal migrering till Azure HDInsight Hadoop migrering](apache-hadoop-on-premises-migration-best-practices-data-migration.md).