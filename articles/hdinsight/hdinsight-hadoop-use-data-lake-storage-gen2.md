---
title: Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster
description: Lär dig hur du använder Azure Data Lake Storage Gen2 med Azure HDInsight kluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: ea9dc2627d5a6498f69ca8c61a9cac8089816886
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378592"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster

[Azure Data Lake Storage Gen2 är](../storage/blobs/data-lake-storage-introduction.md) en molnlagringstjänst som är dedikerad för analys av stordata och som bygger [på Azure Blob Storage.](../storage/blobs/storage-blobs-introduction.md) Data Lake Storage Gen2 kombinerar funktionerna i Azure Blob Storage och Azure Data Lake Storage Gen1. Den resulterande tjänsten erbjuder funktioner Azure Data Lake Storage Gen1 bland annat: filsystemsemantik, säkerhet på katalog- och filnivå samt anpassningsbarhet. Tillsammans med funktioner för låg kostnad, nivåindelad lagring, hög tillgänglighet och haveriberedskap från Azure Blob Storage.

En fullständig jämförelse av alternativ för klusterskapande med Data Lake Storage Gen2 finns i [Jämför lagringsalternativ för användning med Azure HDInsight kluster.](hdinsight-hadoop-compare-storage-options.md)

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 tillgänglighet

Data Lake Storage Gen2 är tillgängligt som ett lagringsalternativ för nästan alla Azure HDInsight klustertyper som både standardkonto och ytterligare ett lagringskonto. HBase kan dock bara ha ett konto med Data Lake Storage Gen2.

> [!Note]  
> När du har Data Lake Storage Gen2 som primär **lagringstyp** kan du inte välja en Data Lake Storage Gen1 som ytterligare lagringsutrymme.

## <a name="create-hdinsight-clusters-using-data-lake-storage-gen2"></a>Skapa HDInsight-kluster med Data Lake Storage Gen2

Använd följande länkar för detaljerade anvisningar om hur du skapar HDInsight-kluster med åtkomst till Data Lake Storage Gen2.

* [Använda portalen](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-portal.md)
* [Använda Azure CLI](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-azure-cli.md)
* PowerShell stöds för närvarande inte för att skapa ett HDInsight-kluster med Azure Data Lake Storage Gen2.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Åtkomstkontroll för Data Lake Storage Gen2 i HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Vilka typer av behörigheter Data Lake Storage Gen2 stöd för?

Data Lake Storage Gen2 använder en åtkomstkontrollmodell som stöder både rollbaserad åtkomstkontroll i Azure (Azure RBAC) och POSIX-liknande åtkomstkontrollistor (ACL). Data Lake Storage Gen1 stöder endast åtkomstkontrolllistor för att kontrollera åtkomst till data.

Azure RBAC använder rolltilldelningar för att effektivt tillämpa uppsättningar av behörigheter för användare, grupper och tjänstens huvudnamn för Azure-resurser. Vanligtvis är dessa Azure-resurser begränsade till resurser på den översta nivån (till exempel Azure Blob Storage-konton). För Azure Blob Storage och även Data Lake Storage Gen2 har den här mekanismen utökats till filsystemresursen.

Mer information om filbehörigheter med Azure RBAC finns i [Rollbaserad åtkomstkontroll i Azure (Azure RBAC).](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control)

Mer information om filbehörigheter med ACL:er finns [i Åtkomstkontrollistor för filer och kataloger.](../storage/blobs/data-lake-storage-access-control.md)

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Hur gör jag för att styra åtkomsten till mina data i Data Lake Storage Gen2?

HDInsight-klustrets möjlighet att komma åt filer i Data Lake Storage Gen2 styrs via hanterade identiteter. En hanterad identitet är en identitet som registrerats i Azure Active Directory (Azure AD) vars autentiseringsuppgifter hanteras av Azure. Med hanterade identiteter behöver du inte registrera tjänstens huvudnamn i Azure AD. Eller behåll autentiseringsuppgifter, till exempel certifikat.

Azure-tjänster har två typer av hanterade identiteter: system-tilldelade och användar-tilldelade. HDInsight använder användar tilldelade hanterade identiteter för att komma åt Data Lake Storage Gen2. En `user-assigned managed identity` skapas som en fristående Azure-resurs. När den skapas skapar Azure en identitet i den Azure AD-klientorganisation som är betrodd av den prenumeration som används. När identiteten har skapats kan den tilldelas till en eller flera tjänstinstanser i Azure.

Livscykeln för en användartilldelad identitet hanteras separat från livscykeln för de Azure-tjänstinstanser som den är tilldelad till. Mer information om hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Hur gör jag för att ange behörigheter för Azure AD-användare att fråga efter data i Data Lake Storage Gen2 med Hive eller andra tjänster?

Om du vill ange behörigheter för användare att fråga efter data använder du Azure AD-säkerhetsgrupper som tilldelade huvudnamn i ACL:er. Tilldela inte behörigheter för filåtkomst direkt till enskilda användare eller tjänstens huvudnamn. Med Azure AD-säkerhetsgrupper för att styra flödet av behörigheter kan du lägga till och ta bort användare eller tjänstens huvudnamn utan att tillämpa ACL:er på nytt i en hel katalogstruktur. Du behöver bara lägga till eller ta bort användarna från lämplig Azure AD-säkerhetsgrupp. ACL:er ärvs inte, så att tillämpa ACL:er igen kräver att ACL:en uppdateras på varje fil och underkatalog.

## <a name="access-files-from-the-cluster"></a>Åtkomst till filer från klustret

Det finns flera sätt att komma åt filerna i Data Lake Storage Gen2 från ett HDInsight-kluster.

* **Via det fullständiga namnet**. Med den här metoden kan du ange den fullständiga sökvägen till filen som du vill öppna.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Via det förkortade sökvägsformatet**. Med den här metoden ersätter du sökvägen upp till klusterroten med:

    ```
    abfs:///<file.path>/
    ```

* **Med den relativa sökvägen**. Med den här metoden anger du bara den relativa sökvägen till den fil som du vill öppna.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Exempel på dataåtkomst

Exemplen baseras på en [SSH-anslutning](./hdinsight-hadoop-linux-use-ssh-unix.md) till klustrets huvudnod. I exemplen används alla tre URI-scheman. Ersätt `CONTAINERNAME` och med relevanta `STORAGEACCOUNT` värden

#### <a name="a-few-hdfs-commands"></a>Några hdfs-kommandon

1. Skapa en fil på lokal lagring.

    ```bash
    touch testFile.txt
    ```

1. Skapa kataloger i klusterlagring.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Kopiera data från lokal lagring till klusterlagring.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Lista kataloginnehåll på klusterlagring.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Skapa en Hive-tabell

Tre filplatser visas i illustrerande syfte. Använd bara en av posterna för den `LOCATION` faktiska körningen.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Nästa steg

* [Azure HDInsight integrering med Data Lake Storage Gen2 förhandsversion – ACL och säkerhetsuppdatering](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Självstudie: Extrahera, transformera och läsa in data med hjälp Interaktiv fråga i Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
