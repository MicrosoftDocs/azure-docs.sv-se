---
title: Översikt över avdelningens kontroll-koppling
description: Den här artikeln beskriver de olika data lager och-funktioner som stöds i avdelningens kontroll
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 08b22af8743082bab1d547205e51917cb9d92a11
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695778"
---
# <a name="supported-data-stores"></a>Datalager som stöds

Avdelningens kontroll har stöd för följande data lager. Klicka på varje data lager för att lära dig vilka funktioner som stöds och motsvarande konfigurationer i detalj.

## <a name="purview-data-sources"></a>Avdelningens kontroll data källor

|**Kategori**|  **Data lager**  |**Extrahering av metadata**|**Fullständig sökning**|**Stegvis skanning**|**Sökning i omfång**|**Klassificering**|**Ursprung**|
|---|---|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure-datautforskaren](register-scan-azure-data-explorer.md)|Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)|Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)|Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure SQL Database](register-scan-azure-sql-database.md)|Ja| Ja| Inga| Ja| Ja| Ja|
||[Azure SQL Database Hanterad instans](register-scan-azure-sql-database-managed-instance.md)|Ja| Ja| Inga| Ja| Ja| Ja|
||[Azure Synapse Analytics (tidigare SQL DW)](register-scan-azure-synapse-analytics.md)|Ja| Ja| Inga| Ja| Ja| Ja|
|Databas|[Oracle DB](register-scan-oracle-source.md)|Ja| Ja| Inga| Inga| Inga| Ja|
||[SQL Server](register-scan-on-premises-sql-server.md)|Ja| Ja| Inga| Ja| Ja| Ja|
||[Teradata](register-scan-teradata-source.md)|Ja| Ja| Inga| Inga| Inga| Ja|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|Ja| Ja| Inga| Inga| Inga| Ja|
|Tjänster och appar|[SAP ECC](register-scan-sapecc-source.md)|Ja| Ja| Inga| Ja| Ja| Ja|
||[SAP-S4HANA](register-scan-saps4hana-source.md)|Ja| Ja| Inga| Ja| Ja| Ja|

## <a name="next-steps"></a>Nästa steg

- [Registrera och skanna Azure Blob Storage-källa](register-scan-azure-blob-storage-source.md)