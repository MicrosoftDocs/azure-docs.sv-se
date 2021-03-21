---
title: Integrera Data Lake Storage Gen1 med andra Azure-tjänster
description: Förstå hur du kan integrera Azure Data Lake Storage Gen1 med andra Azure-tjänster.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 929853f4dbedca7034c8e2a51e6231651a2dd08f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96461661"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Integrera Azure Data Lake Storage Gen1 med andra Azure-tjänster
Azure Data Lake Storage Gen1 kan användas tillsammans med andra Azure-tjänster för att möjliggöra ett bredare utbud av scenarier. I följande artikel visas de tjänster som Data Lake Storage Gen1 kan integreras med.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Använda Data Lake Storage Gen1 med Azure HDInsight
Du kan etablera ett [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) -kluster som använder data Lake Storage GEN1 som HDFS-kompatibel lagring. I den här versionen av Hadoop-och Storm-kluster i Windows och Linux kan du använda Data Lake Storage Gen1 endast som ytterligare lagrings utrymme. Sådana kluster använder fortfarande Azure Storage (WASB) som standard lagring. För HBase-kluster i Windows och Linux kan du dock använda Data Lake Storage Gen1 som standard lagring, eller ytterligare lagring eller både och.

Instruktioner för hur du etablerar ett HDInsight-kluster med Data Lake Storage Gen1 finns i:

* [Etablera ett HDInsight-kluster med Data Lake Storage Gen1 att använda Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Etablera ett HDInsight-kluster med Data Lake Storage Gen1 som standard lagring med Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Etablera ett HDInsight-kluster med Data Lake Storage Gen1 som ytterligare lagrings utrymme med Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Använda Data Lake Storage Gen1 med Azure Data Lake Analytics
Med [Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) kan du arbeta med Big data i moln skala. Den dynamiskt etablerar resurser och gör att du kan analysera terabyte eller till och med exabyte av data som kan lagras i ett antal data källor som stöds, en av dem Data Lake Storage Gen1. Data Lake Analytics är särskilt optimerad för att fungera med Data Lake Storage Gen1 – vilket ger den högsta nivån av prestanda, data flöde och parallellisering för stora data arbets belastningar.

Instruktioner för hur du använder Data Lake Analytics med Data Lake Storage Gen1 finns i [komma igång med data Lake Analytics med hjälp av data Lake Storage gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Använda Data Lake Storage Gen1 med Azure Data Factory
Du kan använda [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) för att mata in data från Azure-tabeller, Azure SQL Database, Azure SQL DataWarehouse, Azure Storage blobbar och lokala databaser. För att vara en första klass som är medborgare i Azure-eko systemet kan Azure Data Factory användas för att dirigera inmatningen av data från dessa källor till Data Lake Storage Gen1.

Instruktioner för hur du använder Azure Data Factory med Data Lake Storage Gen1 finns i [Flytta data till och från data Lake Storage gen1 med Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Kopiera data från Azure Storage blobbar till Data Lake Storage Gen1
Azure Data Lake Storage Gen1 tillhandahåller ett kommando rads verktyg, AdlCopy, som gör att du kan kopiera data från Azure Blob Storage till ett Data Lake Storage Gen1-konto. Mer information finns i [Kopiera data från Azure Storage blobbar till data Lake Storage gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Kopiera data mellan Azure SQL Database och Data Lake Storage Gen1
Du kan använda Apache Sqoop för att importera och exportera data mellan Azure SQL Database och Data Lake Storage Gen1. Mer information finns i [Kopiera data mellan data Lake Storage gen1 och Azure SQL Database med Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Använda Data Lake Storage Gen1 med Stream Analytics
Du kan använda Data Lake Storage Gen1 som en av utdata för att lagra data strömmar med Azure Stream Analytics. Mer information finns i [strömma data från Azure Storage blob till data Lake Storage gen1 med Azure Stream Analytics](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Använda Data Lake Storage Gen1 med Power BI
Du kan använda Power BI för att importera data från ett Data Lake Storage Gen1-konto för att analysera och visualisera data. Mer information finns i [analysera data i data Lake Storage gen1 att använda Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Använda Data Lake Storage Gen1 med Data Catalog
Du kan registrera data från Data Lake Storage Gen1 i Azure Data Catalog för att göra data synliga i hela organisationen. Mer information finns i [registrera data från data Lake Storage gen1 i Azure Data Catalog](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Använda Data Lake Storage Gen1 med SQL Server Integration Services (SSIS)
Du kan använda Data Lake Storage Gen1 anslutnings hanteraren i SSIS för att ansluta ett SSIS-paket med Data Lake Storage Gen1. Mer information finns i [använda data Lake Storage gen1 med SSIS](/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-storage-gen1-with-azure-synapse-analytics"></a>Använda Data Lake Storage Gen1 med Azure Synapse Analytics
Du kan använda PolyBase för att läsa in data från Data Lake Storage Gen1 till Azure Synapse Analytics. Mer information finns i [använda data Lake Storage gen1 med Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Använda Data Lake Storage Gen1 med Azure Event Hubs
Du kan använda Azure Data Lake Storage Gen1 för att arkivera och avbilda data som tagits emot av Azure Event Hubs. Mer information finns i [använda data Lake Storage gen1 med Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Se även
* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Kom igång med Data Lake Storage Gen1 med hjälp av portalen](data-lake-store-get-started-portal.md)
* [Kom igång med Data Lake Storage Gen1 med PowerShell](data-lake-store-get-started-powershell.md)