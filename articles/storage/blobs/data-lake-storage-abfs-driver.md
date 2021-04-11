---
title: Driv rutinen för Azure Blob-filsystem för Azure Data Lake Storage Gen2
description: Lär dig mer om Azure Blob filesystem-drivrutinen (ABFS), en dedikerad Azure Storage driv rutin för Hadoop. Få åtkomst till data i Azure Data Lake Storage Gen2 med den här driv rutinen.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 54c6245ea5290e2cab0efcd52f2208ba874c0848
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101720451"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Azure Blob filesystem-drivrutinen (ABFS): en dedikerad Azure Storage driv rutin för Hadoop

En av de primära åtkomst metoderna för data i Azure Data Lake Storage Gen2 är via [Hadoop-filsystem](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html). Data Lake Storage Gen2 ger användare av Azure Blob Storage åtkomst till en ny driv rutin, Azure Blob-filsystemets driv rutin eller `ABFS` . ABFS är en del av Apache Hadoop och ingår i många av de kommersiella distributionerna av Hadoop. Med den här driv rutinen kan många program och ramverk komma åt data i Azure Blob Storage utan kod som uttryckligen refererar till Data Lake Storage Gen2.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Tidigare funktion: Windows Azure Storage Blob-drivrutinen

Windows Azure Storage Blob driv rutinen eller [WASB-drivrutinen](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) tillhandahöll det ursprungliga stödet för Azure Blob Storage. Den här driv rutinen utförde den komplexa uppgiften att mappa fil systemets semantik (vilket krävs av Hadoop-filsystem-gränssnittet) till det objekt lagrings gränssnitt som exponeras av Azure Blob Storage. Den här driv rutinen fortsätter att stödja den här modellen, vilket ger hög prestanda åtkomst till data som lagras i blobbar, men som innehåller en betydande mängd kod som utför den här mappningen, vilket gör det svårt att underhålla. Dessutom kräver vissa åtgärder som [filesystem. Rename ()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) och [filesystem. Delete ()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) som tillämpas på kataloger att driv rutinen kan utföra ett stort antal åtgärder (på grund av att objekt inte innehåller stöd för kataloger) som ofta leder till försämrade prestanda. ABFS-drivrutinen har utformats för att lösa de olika bristerna i WASB.

## <a name="the-azure-blob-file-system-driver"></a>Azure Blob File System-drivrutinen

[Azure Data Lake Storage REST-gränssnittet](/rest/api/storageservices/data-lake-storage-gen2) har utformats för att stödja semantiska fil system över Azure Blob Storage. Eftersom Hadoop-filsystem också är utformat för att stödja samma semantik finns det inget krav på en komplex mappning i driv rutinen. Därför är Azure Blob-filsystemets driv rutin (eller ABFS) en klient-Shim för REST API.

Det finns dock vissa funktioner som driv rutinen fortfarande måste utföra:

### <a name="uri-scheme-to-reference-data"></a>URI-schema för referens data

ABFS-drivrutinen är konsekvent med andra fil Systems implementeringar i Hadoop och definierar det egna URI-schemat så att resurser (kataloger och filer) kan särskiljas tydligt. URI-schemat dokumenteras i [använd Azure Data Lake Storage Gen2-URI](./data-lake-storage-introduction-abfs-uri.md). URI-strukturen är: `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Med hjälp av ovanstående URI-format kan du använda vanliga Hadoop-verktyg och ramverk för att referera till dessa resurser:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/
```

Internt översätter ABFS-drivrutinen resurserna som anges i URI: n till filer och kataloger och anropar Azure Data Lake Storage REST API med dessa referenser.

### <a name="authentication"></a>Autentisering

ABFS-drivrutinen stöder två typer av autentisering så att Hadoop-programmet kan komma åt resurser som finns i ett Data Lake Storage Gen2 kompatibelt konto. Fullständig information om tillgängliga autentiseringsscheman finns i [säkerhets guiden för Azure Storage](security-recommendations.md). De är:

- **Delad nyckel:** Detta gör det möjligt för användare att få åtkomst till alla resurser i kontot. Nyckeln krypteras och lagras i Hadoop-konfigurationen.

- **Azure Active Directory OAuth Bearer-token:** Azure AD Bearer-token förvärvas och uppdateras av driv rutinen med antingen identiteten för slutanvändaren eller ett konfigurerat huvud namn för tjänsten. Med hjälp av den här autentiseringsmetoden auktoriseras all åtkomst per anrop med den identitet som är kopplad till den angivna token och utvärderas mot den tilldelade POSIX-Access Control listan (ACL).

   > [!NOTE]
   > Azure Data Lake Storage Gen2 stöder endast Azure AD v 1.0-slut punkter.

### <a name="configuration"></a>Konfiguration

All konfiguration för ABFS-drivrutinen lagras i <code>core-site.xml</code> konfigurations filen. På Hadoop-distributioner med [Ambari](https://ambari.apache.org/)kan konfigurationen också hanteras med hjälp av webb portalen eller Ambari REST API.

Information om alla konfigurations poster som stöds anges i den [officiella Hadoop-dokumentationen](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html).

### <a name="hadoop-documentation"></a>Hadoop-dokumentation

ABFS-drivrutinen är fullständigt dokumenterad i den [officiella Hadoop-dokumentationen](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html)

## <a name="next-steps"></a>Nästa steg

- [Skapa ett Azure Databricks-kluster](./data-lake-storage-use-databricks-spark.md)
- [Använda Azure Data Lake Storage Gen2 URI](./data-lake-storage-introduction-abfs-uri.md)