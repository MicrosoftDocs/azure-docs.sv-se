---
title: Översikt över Azure Data Lake Storage Gen2 i HDInsight
description: Översikt över Data Lake Storage Gen2 i HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: e69838f18efc08d0f64dd9ea904f502617073a8f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938868"
---
# <a name="azure-data-lake-storage-gen2-overview-in-hdinsight"></a>Översikt över Azure Data Lake Storage Gen2 i HDInsight

Azure Data Lake Storage Gen2 använder kärn funktioner från Azure Data Lake Storage Gen1 och integrerar dem i Azure Blob Storage. Dessa funktioner innehåller ett fil system som är kompatibelt med Hadoop, Azure Active Directory (Azure AD) och POSIX-baserade åtkomst kontrol listor (ACL: er). Med den här kombinationen kan du dra nytta av prestanda för Azure Data Lake Storage Gen1. Även om du använder hantering av nivåer och data livs cykel hantering av Blob Storage.

Mer information om Azure Data Lake Storage Gen2 finns i [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Core-funktioner i Azure Data Lake Storage Gen2

* **Åtkomst som är kompatibel med Hadoop:** I Azure Data Lake Storage Gen2 kan du hantera och komma åt data precis som med en Hadoop Distributed File System (HDFS). ABFS-drivrutinen (Azure Blob File System) är tillgänglig i alla Apache Hadoop miljöer, inklusive Azure HDInsight och Azure Databricks. Använd ABFS för att komma åt data som lagras i Data Lake Storage Gen2.

* **En supermängd av POSIX-behörigheter:** Säkerhets modellen för Data Lake Gen2 stöder ACL-och POSIX-behörigheter tillsammans med viss extra detaljerad information för att Data Lake Storage Gen2. Inställningar kan konfigureras via administrations verktyg eller ramverk som Apache Hive och Apache Spark.

* **Kostnads effektivitet:** Data Lake Storage Gen2 erbjuder lagrings kapacitet och transaktioner med låg kostnad. Med Azure Blob Storages livs längd kan du minska kostnaderna genom att justera fakturerings taxan när data flyttas genom livs cykeln.

* **Kompatibilitet med Blob Storage-verktyg, ramverk och appar:** Data Lake Storage Gen2 fortsätter att arbeta med en bred uppsättning verktyg, ramverk och program för Blob Storage.

* **Optimerad driv rutin:** ABFS-drivrutinen optimeras specifikt för stor data analys. Motsvarande REST-API: er sker via DFS-slutpunkten (Distributed File System), dfs.core.windows.net.

## <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Vad är nytt för Azure Data Lake Storage gen 2

### <a name="managed-identities-for-secure-file-access"></a>Hanterade identiteter för säker fil åtkomst

Azure HDInsight använder hanterade identiteter för att skydda kluster åtkomsten till filer i Azure Data Lake Storage Gen2. Hanterade identiteter är en funktion i Azure Active Directory som tillhandahåller Azure-tjänster med en uppsättning automatiskt hanterade autentiseringsuppgifter. De här autentiseringsuppgifterna kan användas för att autentisera till en tjänst som stöder Active Directory autentisering. Om du använder hanterade identiteter behöver du inte lagra autentiseringsuppgifter i kod-eller konfigurationsfiler.

Mer information finns i [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-blob-file-system-driver"></a>Azure Blob File System-drivrutin

Apache Hadoop program förväntar sig att läsa och skriva data från lokal disk lagring. En Hadoop-fil system driv rutin som ABFS gör att Hadoop-program fungerar med moln lagring. Fungerar genom att emulera vanliga Hadoop-filsystem-åtgärder. Driv rutinen konverterar de kommandon som tas emot från programmet till åtgärder som den faktiska moln lagrings plattformen förstår.

Tidigare har Hadoop-filsystemets driv rutin konverterat alla fil system åtgärder för att Azure Storage REST API-anrop på klient sidan. Och anropar sedan REST API. Den här konverteringen på klient sidan ledde dock till flera REST API anrop för en enda fil system åtgärd som att byta namn på en fil. ABFS har flyttat Hadoop-filsystemets logik från klient sidan till Server sidan. Azure Data Lake Storage Gen2 API körs nu parallellt med BLOB-API: et. Den här migreringen förbättrar prestandan eftersom du kan utföra vanliga Hadoop-filsystem åtgärder med ett REST API-anrop.

Mer information finns i [Azure Blob filesystem-driv rutinen (ABFS): en dedikerad Azure Storage driv rutin för Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>URI-schema för Azure Data Lake Storage gen 2

Azure Data Lake Storage Gen2 använder ett nytt URI-schema för att komma åt filer i Azure Storage från HDInsight:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

URI-schemat ger SSL-krypterad åtkomst.

`<FILE_SYSTEM_NAME>` identifierar sökvägen till fil systemet Data Lake Storage Gen2.

`<ACCOUNT_NAME>` identifierar Azure Storage kontots namn. Ett fullständigt kvalificerat domännamn (FQDN) krävs.

`<PATH>` är sökvägen till filen eller katalogen HDFS.

Om värden för `<FILE_SYSTEM_NAME>` och `<ACCOUNT_NAME>` inte anges används standard fil systemet. För filerna i standard fil systemet använder du en relativ sökväg eller en absolut sökväg. Till exempel `hadoop-mapreduce-examples.jar` kan filen som medföljer HDInsight-kluster refereras till genom att använda någon av följande sökvägar:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]
> Fil namnet är `hadoop-examples.jar` i HDInsight-versionerna 2,1 och 1,6-kluster. När du arbetar med filer utanför HDInsight kan du se att de flesta verktyg inte känner igen ABFS-formatet utan förväntar sig ett grundläggande Sök vägs format, till exempel `example/jars/hadoop-mapreduce-examples.jar` .

Mer information finns i [använd Azure Data Lake Storage Gen2-URI](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introduktion till Azure Storage](../storage/common/storage-introduction.md)
* [Azure Data Lake Storage Gen1-översikt](./overview-data-lake-storage-gen1.md)