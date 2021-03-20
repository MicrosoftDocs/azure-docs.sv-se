---
title: Azure-tjänster som stöder Azure Data Lake Storage Gen2 | Microsoft Docs
description: Lär dig mer om vilka Azure-tjänster som integreras med Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 36e1a8a288e1f9b2a8d65ab966b607b594d66f4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100653609"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Azure-tjänster som stöder Azure Data Lake Storage Gen2

Du kan använda Azure-tjänster för att mata in data, utföra analyser och skapa visuella representationer. Den här artikeln innehåller en lista över Azure-tjänster som stöds, utvärderar support nivån och innehåller länkar till artiklar som hjälper dig att använda de här tjänsterna med Azure Data Lake Storage Gen2.

## <a name="supported-azure-services"></a>Azure-tjänster som stöds

I den här tabellen visas de Azure-tjänster som du kan använda med Azure Data Lake Storage Gen2. De objekt som visas i tabellerna ändras med tiden då stödet fortsätter att expandera.

> [!NOTE]
> Support nivån avser endast hur tjänsten stöds med Data Lake Storage gen 2.

|Azure-tjänst |Support nivå |Azure AD |Delad nyckel| Relaterade artiklar |
|---------------|-------------------|---|---|---|
|Azure Data Factory|Allmänt tillgänglig|Ja|Ja|[Läs in data i Azure Data Lake Storage Gen2 med Azure Data Factory](../../data-factory/load-azure-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Allmänt tillgänglig|Ja|Ja|[Använda med Azure Databricks](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) <br> [Självstudie: Extrahera, transformera och läsa in data med hjälp av Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse) <br>[Självstudie: komma åt Data Lake Storage Gen2 data med Azure Databricks med Spark](data-lake-storage-use-databricks-spark.md)|
|Azure Event Hub|Allmänt tillgänglig|Inga|Ja|[Avbilda händelser via Azure Event Hubs i Azure Blob Storage eller Azure Data Lake Storage](../../event-hubs/event-hubs-capture-overview.md)|
|Azure Event Grid|Allmänt tillgänglig|Ja|Ja|[Självstudie: Implementera Data Lake Capture-mönstret för att uppdatera en Databricks delta tabell](data-lake-storage-events.md)|
|Azure Logic Apps|Allmänt tillgänglig|Inga|Ja|[Översikt – vad är Azure Logic Apps?](../../logic-apps/logic-apps-overview.md)|
|Azure Machine Learning|Allmänt tillgänglig|Ja|Ja|[Få åtkomst till data i Azure Storage-tjänster](../../machine-learning/how-to-access-data.md)|
|Azure Stream Analytics|Allmänt tillgänglig|Ja|Ja|[Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](../../stream-analytics/stream-analytics-quick-create-portal.md) <br> [Utgående till Azure Data Lake Gen2](../../stream-analytics/stream-analytics-define-outputs.md)|
|Data Box|Allmänt tillgänglig|Inga|Ja|[Använd Azure Data Box för att migrera data från en lokal HDFS-lagring till Azure Storage](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |Allmänt tillgänglig|Ja|Ja|[Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)<br>[Använda HDFS CLI med Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Självstudie: extrahera, transformera och läsa in data med Apache Hive på Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT Hub |Allmänt tillgänglig|Ja|Ja|[Använd IoT Hub meddelanderoutning för att skicka meddelanden från enheten till molnet till olika slut punkter](../../iot-hub/iot-hub-devguide-messages-d2c.md)|
|Power BI|Allmänt tillgänglig|Ja|Ja|[Analysera data i Data Lake Storage Gen2 med Power BI](/power-query/connectors/datalakestorage)|
|Azure Synapse Analytics (tidigare SQL Data Warehouse)|Allmänt tillgänglig|Ja|Ja|[Analysera data i ett lagrings konto](../../synapse-analytics/get-started-analyze-storage.md)|
|SQL Server Integration Services (SSIS)|Allmänt tillgänglig|Ja|Ja|[Azure Storage anslutnings hanteraren](/sql/integration-services/connection-manager/azure-storage-connection-manager)|
|Öppna Azure-datautforskaren|Allmänt tillgänglig|Ja|Ja|[Fråga efter data i Azure Data Lake med Azure Datautforskaren](/azure/data-explorer/data-lake-query-data)|
|Azure Cognitive Search|Förhandsgranskning|Ja|Ja|[Index och Sök Azure Data Lake Storage Gen2 dokument (förhands granskning)](../../search/search-howto-index-azure-data-lake-storage.md)|
|Azure Content Delivery Network|Stöds inte ännu|Inte tillämpligt|Inte tillämpligt|[Index och Sök Azure Data Lake Storage Gen2 dokument (förhands granskning)](../../cdn/cdn-overview.md)|
|Azure SQL Database|Stöds inte ännu|Inte tillämpligt|Inte tillämpligt|[Vad är Azure SQL Database?](../../azure-sql/database/sql-database-paas-overview.md)|

## <a name="see-also"></a>Se även

- [Kända problem med Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Bloblagringsfunktioner som är tillgängliga i Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Plattformar med öppen källkod som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Åtkomst till flera protokoll på Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)