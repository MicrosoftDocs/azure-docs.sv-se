---
title: Använda Azure Data Lake Storage Gen2 URI
description: 'Lär dig URI-syntax för ABFS-schema-ID: t som representerar driv rutinen för BLOB-filsystemet (Hadoop) för Azure Data Lake Storage Gen2).'
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: 948b5aa0ad015f9f3c693e13219ec034724687c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95913172"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Använda Azure Data Lake Storage Gen2 URI

[Hadoop-filsystem](https://www.aosabook.org/en/hdfs.html) -drivrutinen som är kompatibel med Azure Data Lake Storage Gen2 är känd av sitt schema `abfs` -ID (Azure Blob File System). ABFS-drivrutinen är konsekvent med andra Hadoop-filsystem-drivrutiner och använder ett URI-format för att adressera filer och kataloger inom ett Data Lake Storage Gen2-kompatibelt konto.

## <a name="uri-syntax"></a>URI-syntax

URI-syntaxen för Data Lake Storage Gen2 beror på om ditt lagrings konto har kon figurer ATS att ha Data Lake Storage Gen2 som standard fil system.

Om det Data Lake Storage Gen2-kapabla konto som du vill adressera **inte har** angetts som standard fil system när kontot skapas, är den stenografiska URI-syntaxen:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Schema-ID**: `abfs` protokollet används som schema-ID. Du kan välja att ansluta till eller utan en Transport Layer Security (TLS), tidigare kallat Secure Sockets Layer (SSL), anslutning. Använd `abfss` för att ansluta till en TLS-anslutning.

2. **Fil system**: den överordnade plats som innehåller filerna och mapparna. Detta är samma som behållare i tjänsten Azure Storage blobs.

3. **Konto namn**: det namn som angavs för ditt lagrings konto när det skapades.

4. **Sökvägar**: en omvänt snedstreck avgränsad `/` med en åter givning av katalog strukturen.

5. **Fil namn**: namnet på den enskilda filen. Den här parametern är valfri om du adresserar en katalog.

Men om det konto som du vill adressera anges som standard fil system när kontot skapas, är den stenografiska URI-syntaxen:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Sökväg**: en omvänt snedstreck ( `/` ) för katalog strukturen.

2. **Fil namn**: namnet på den enskilda filen.


## <a name="next-steps"></a>Nästa steg

- [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)