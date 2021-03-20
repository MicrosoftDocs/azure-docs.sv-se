---
title: Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster
description: Lär dig hur du använder Azure Data Lake Storage Gen2 med Azure HDInsight-kluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 04/24/2020
ms.openlocfilehash: 2bbfbd2d953ea663453f0092ff366e95f6dd5ea7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945371"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster

[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) är en moln lagrings tjänst avsedd för stor data analys som bygger på [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md). Data Lake Storage Gen2 kombinerar funktionerna i Azure Blob Storage och Azure Data Lake Storage Gen1. Den resulterande tjänsten erbjuder funktioner från Azure Data Lake Storage Gen1, inklusive: semantiska fil system, säkerhet på katalog nivå och filnivå samt anpassnings möjlighet. Tillsammans med låg kostnad, nivå lagring, hög tillgänglighet och katastrof återställning från Azure Blob Storage.

En fullständig jämförelse av alternativen för att skapa kluster med hjälp av Data Lake Storage Gen2 finns i [jämföra lagrings alternativ för användning med Azure HDInsight-kluster](hdinsight-hadoop-compare-storage-options.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 tillgänglighet

Data Lake Storage Gen2 är tillgängligt som ett lagrings alternativ för nästan alla Azure HDInsight-kluster typer som både standard och ytterligare lagrings konton. HBase kan dock ha endast ett konto med Data Lake Storage Gen2.

> [!Note]  
> När du har valt Data Lake Storage Gen2 som **primär lagrings typ** kan du inte välja en data Lake Storage gen1 som ytterligare lagrings utrymme.

## <a name="create-hdinsight-clusters-using-data-lake-storage-gen2"></a>Skapa HDInsight-kluster med hjälp av Data Lake Storage Gen2

Använd följande länkar om du vill ha detaljerade instruktioner om hur du skapar HDInsight-kluster med åtkomst till Data Lake Storage Gen2.

* [Använda portalen](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-portal.md)
* [Använda Azure CLI](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-azure-cli.md)
* PowerShell stöds för närvarande inte för att skapa ett HDInsight-kluster med Azure Data Lake Storage Gen2.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Åtkomst kontroll för Data Lake Storage Gen2 i HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Vilka typer av behörigheter Data Lake Storage Gen2 stöd för?

Data Lake Storage Gen2 använder en modell för åtkomst kontroll som stöder både Azure-rollbaserad åtkomst kontroll (Azure RBAC) och POSIX-liknande åtkomst kontrol listor (ACL: er). Data Lake Storage Gen1 stöder endast åtkomst kontrol listor för att kontrol lera åtkomst till data.

Azure RBAC använder roll tilldelningar för att effektivt tillämpa uppsättningar av behörigheter för användare, grupper och tjänstens huvud namn för Azure-resurser. Normalt är dessa Azure-resurser begränsade till resurser på den översta nivån (till exempel Azure Blob Storage-konton). För Azure Blob Storage, och även Data Lake Storage Gen2, har den här mekanismen utökats till fil system resursen.

Mer information om fil behörigheter med Azure RBAC finns i [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control).

Mer information om fil behörigheter med ACL: er finns i [åtkomst kontrol listor på filer och kataloger](../storage/blobs/data-lake-storage-access-control.md).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Hur gör jag för att kontrol lera åtkomst till mina data i Data Lake Storage Gen2?

Ditt HDInsight-klusters möjlighet att komma åt filer i Data Lake Storage Gen2 styrs genom hanterade identiteter. En hanterad identitet är en identitet som registrerats i Azure Active Directory (Azure AD) vars autentiseringsuppgifter hanteras av Azure. Med hanterade identiteter behöver du inte registrera tjänstens huvud namn i Azure AD. Eller Upprätthåll autentiseringsuppgifter, till exempel certifikat.

Azure-tjänster har två typer av hanterade identiteter: systemtilldelade och tilldelade användare. HDInsight använder användare tilldelade hanterade identiteter för att få åtkomst till Data Lake Storage Gen2. En `user-assigned managed identity` skapas som en fristående Azure-resurs. När den skapas skapar Azure en identitet i den Azure AD-klientorganisation som är betrodd av den prenumeration som används. När identiteten har skapats kan den tilldelas till en eller flera tjänstinstanser i Azure.

Livscykeln för en användartilldelad identitet hanteras separat från livscykeln för de Azure-tjänstinstanser som den är tilldelad till. Mer information om hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Hur gör jag för att ställer du in behörigheter för Azure AD-användare att fråga efter data i Data Lake Storage Gen2 med hjälp av Hive eller andra tjänster?

Om du vill ange behörigheter för användare för att fråga data använder du Azure AD-säkerhetsgrupper som tilldelat objekt i ACL: er. Tilldela inte fil åtkomst behörighet till enskilda användare eller tjänst huvud namn direkt. Med Azure AD-säkerhetsgrupper för att kontrol lera flödet av behörigheter kan du lägga till och ta bort användare eller tjänstens huvud namn utan att återställa ACL: er till en hel katalog struktur. Du behöver bara lägga till eller ta bort användarna från rätt Azure AD-säkerhetsgrupp. ACL: er ärvs inte, så om du tillämpar ACL: er igen måste du uppdatera ACL: en på alla filer och under kataloger.

## <a name="access-files-from-the-cluster"></a>Åtkomst till filer från klustret

Det finns flera sätt som du kan använda för att komma åt filerna i Data Lake Storage Gen2 från ett HDInsight-kluster.

* **Via det fullständiga namnet**. Med den här metoden kan du ange den fullständiga sökvägen till filen som du vill öppna.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Via det förkortade sökvägsformatet**. Med den här metoden ersätter du sökvägen upp till kluster roten med:

    ```
    abfs:///<file.path>/
    ```

* **Med den relativa sökvägen**. Med den här metoden anger du bara den relativa sökvägen till den fil som du vill öppna.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Exempel på data åtkomst

Exempel baseras på en [ssh-anslutning](./hdinsight-hadoop-linux-use-ssh-unix.md) till klustrets huvud nod. I exemplen används alla tre URI-scheman. Ersätt `CONTAINERNAME` och `STORAGEACCOUNT` med relevanta värden

#### <a name="a-few-hdfs-commands"></a>Några HDFS-kommandon

1. Skapa en fil på lokal lagrings plats.

    ```bash
    touch testFile.txt
    ```

1. Skapa kataloger i kluster lagringen.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Kopiera data från lokal lagring till kluster lagring.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Lista katalog innehåll i kluster lagringen.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Skapa en Hive-tabell

Tre fil platser visas i syfte att illustrera. Använd endast en av posterna för faktisk körning `LOCATION` .

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

* [Azure HDInsight-integrering med Data Lake Storage Gen2 för hands version – ACL och säkerhets uppdatering](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Självstudie: extrahera, transformera och läsa in data med hjälp av interaktiv fråga i Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
