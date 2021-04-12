---
title: Migrera Azure Data Lake Storage från gen1 till Gen2
description: Migrera Azure Data Lake Storage från gen1 till Gen2, som bygger på Azure Blob Storage och innehåller en uppsättning funktioner avsedda för stor data analys.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 9d160738208bcef7b066567137a3c3a9738c26a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727016"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Migrera Azure Data Lake Storage från gen1 till Gen2

Du kan migrera data, arbets belastningar och program från Data Lake Storage Gen1 till Data Lake Storage Gen2.

Azure Data Lake Storage Gen2 bygger på [Azure Blob Storage](storage-blobs-introduction.md) och innehåller en uppsättning funktioner som är avsedda för stor data analys. [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) kombinerar funktioner från [Azure Data Lake Storage gen1](../../data-lake-store/index.yml), till exempel semantiska fil system, katalog-och filnivå säkerhet och skalning med låg kostnad, nivå lagring, hög tillgänglighet/haveri beredskap från [Azure Blob Storage](storage-blobs-introduction.md).

> [!NOTE]
> För enklare läsning använder den här artikeln termen *gen1* för att referera till Azure Data Lake Storage gen1 och termen *Gen2* för att referera till Azure Data Lake Storage Gen2.

## <a name="recommended-approach"></a>Rekommenderad metod

För att migrera till Gen2 rekommenderar vi följande metod.

: heavy_check_mark: steg 1: utvärdera beredskap

: heavy_check_mark: steg 2: Förbered för migrering

: heavy_check_mark: steg 3: migrera data och program arbets belastningar

: heavy_check_mark: steg 4: Start punkt från gen1 till Gen2

> [!NOTE]
> Gen1 och Gen2 är olika tjänster, det finns ingen uppgradering på plats, avsiktlig migrering krävs. 

### <a name="step-1-assess-readiness"></a>Steg 1: utvärdera beredskap

1. Läs mer om det [data Lake Storage Gen2 erbjudandet](https://azure.microsoft.com/services/storage/data-lake-storage/). Det är fördelar, kostnader och allmän arkitektur. 

2. [Jämför funktionerna](#gen1-gen2-feature-comparison) i gen1 med Gen2. 

3. Granska en lista över [kända problem](data-lake-storage-known-issues.md) för att utvärdera eventuella luckor i funktionerna.

4. Gen2 har stöd för Blob Storage-funktioner som [diagnostisk loggning](../common/storage-analytics-logging.md), [åtkomst nivåer](storage-blob-storage-tiers.md)och [hanterings principer för Blob Storage-livscykler](storage-lifecycle-management-concepts.md). Om du är intressant att använda någon av dessa funktioner kan du läsa den [aktuella support nivån](./data-lake-storage-supported-blob-storage-features.md).

5. Granska det aktuella läget för [stöd för Azure eko system](./data-lake-storage-multi-protocol-access.md) för att säkerställa att Gen2 stöder alla tjänster som dina lösningar är beroende av.

### <a name="step-2-prepare-to-migrate"></a>Steg 2: Förbered för migrering

1. Identifiera de data uppsättningar som du ska migrera.

   Ta den här möjligheten att rensa data uppsättningar som du inte längre använder. Om du inte planerar att migrera alla dina data på en och samma tidpunkt, ska du ta den här tiden för att identifiera logiska grupper av data som du kan migrera i faser.
   
2. Avgör vilken effekt en migrering kommer att ha på din verksamhet.

   Anta till exempel att du får råd om eventuella stillestånds tider när migreringen äger rum. Dessa överväganden kan hjälpa dig att identifiera ett lämpligt migreringsarkiv och att välja de verktyg som passar bäst.

3. Skapa en migrerings plan. 

   Vi rekommenderar dessa [migrations mönster](#migration-patterns). Du kan välja något av dessa mönster, kombinera dem tillsammans eller skapa egna egna mönster.

### <a name="step-3-migrate-data-workloads-and-applications"></a>Steg 3: migrera data, arbets belastningar och program

Migrera data, arbets belastningar och program med hjälp av det mönster som du föredrar. Vi rekommenderar att du validerar scenarier stegvis.

1. [Skapa ett lagrings konto](../common/storage-account-create.md) och aktivera funktionen för hierarkiskt namn område. 

2. Migrera dina data. 

3. Konfigurera [tjänster i dina arbets belastningar](./data-lake-storage-supported-azure-services.md) så att de pekar på din Gen2-slutpunkt. 
   
4. Uppdatera program för att använda Gen2-API: er. Se handböcker för [.net](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [python](data-lake-storage-directory-file-acl-python.md), [Java Script](data-lake-storage-directory-file-acl-javascript.md) och [rest](/rest/api/storageservices/data-lake-storage-gen2). 
   
5. Uppdatera skript för att använda Data Lake Storage Gen2 [PowerShell-cmdletar](data-lake-storage-directory-file-acl-powershell.md)och [Azure CLI-kommandon](data-lake-storage-directory-file-acl-cli.md).
   
6. Sök efter URI-referenser som innehåller strängen `adl://` i kodfragment, eller i Databricks-anteckningsböcker, Apache HIVE HQL-filer eller andra filer som används som en del av dina arbets belastningar. Ersätt dessa referenser med [Gen2-formaterad URI](data-lake-storage-introduction-abfs-uri.md) för ditt nya lagrings konto. Till exempel: gen1-URI: n `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` kan bli `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile` . 

7. Konfigurera säkerheten på ditt konto för att inkludera [Azure-roller](../common/storage-auth-aad-rbac-portal.md), [säkerhet på fil-och mappnivå](data-lake-storage-access-control.md)och [Azure Storage brand väggar och virtuella nätverk](../common/storage-network-security.md).

### <a name="step-4-cutover-from-gen1-to-gen2"></a>Steg 4: Start punkt från gen1 till Gen2

När du är säker på att dina program och arbets belastningar är stabila på Gen2 kan du börja använda Gen2 för att uppfylla dina affärs scenarier. Stäng av eventuella återstående pipelines som körs på gen1 och inaktivera ditt gen1-konto. 

<a id="gen1-gen2-feature-comparison"></a>

## <a name="gen1-vs-gen2-capabilities"></a>Gen1 vs Gen2-funktioner

I den här tabellen jämförs funktionerna i gen1 med Gen2.

|Område |Gen1   |Gen2 |
|---|---|---|
|Data organisation|[Hierarkisk namnrymd](data-lake-storage-namespace.md)<br>Stöd för filer och mappar|[Hierarkisk namnrymd](data-lake-storage-namespace.md)<br>Stöd för behållare, filer och mappar |
|Geo-redundans| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage), [ZRS](../common/storage-redundancy.md#zone-redundant-storage), [GRS](../common/storage-redundancy.md#geo-redundant-storage), [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|Autentisering|[AAD-hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Tjänsters huvudnamn](../../active-directory/develop/app-objects-and-service-principals.md)|[AAD-hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Tjänsters huvudnamn](../../active-directory/develop/app-objects-and-service-principals.md)<br>[Delad åtkomst nyckel](/rest/api/storageservices/authorize-with-shared-key)|
|Auktorisering|Hantering – [Azure RBAC](../../role-based-access-control/overview.md)<br>Data – [ACL: er](data-lake-storage-access-control.md)|Hantering – [Azure RBAC](../../role-based-access-control/overview.md)<br>Data –  [ACL: er](data-lake-storage-access-control.md), [Azure RBAC](../../role-based-access-control/overview.md) |
|Kryptering – vilande data|Server sidan – med [Microsoft-hanterade](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) eller [kund hanterade](../common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) nycklar|Server sidan – med [Microsoft-hanterade](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) eller [kund hanterade](../common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) nycklar|
|VNET-stöd|[VNET-integration](../../data-lake-store/data-lake-store-network-security.md)|[Tjänst slut punkter](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [privata slut punkter](../common/storage-private-endpoints.md)|
|Utvecklings miljö|[Rest](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.net](../../data-lake-store/data-lake-store-data-operations-net-sdk.md), [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md), [python](../../data-lake-store/data-lake-store-data-operations-python.md), [PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), [Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|Allmänt tillgänglig- [rest](/rest/api/storageservices/data-lake-storage-gen2), [.net](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [python](data-lake-storage-directory-file-acl-python.md)<br>Offentlig för hands version – [Java Script](data-lake-storage-directory-file-acl-javascript.md), [POWERSHELL](data-lake-storage-directory-file-acl-powershell.md), [Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|Resursloggar|Klassiska loggar<br>[Azure Monitor integrerad](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[Klassiska loggar](../common/storage-analytics-logging.md) – allmänt tillgänglig<br>[Azure Monitor integrerad](monitor-blob-storage.md) – för hands version|
|Ekosystem|[HDInsight (3,6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md), [Azure Databricks (3,1 och senare)](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html), [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md), [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3,6, 4,0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md), [Azure Databricks (5,1 och senare)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2), [Azure Synapse Analytics](../../azure-sql/database/vnet-service-endpoint-rule-overview.md), [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns"></a>

## <a name="gen1-to-gen2-patterns"></a>Gen1 till Gen2-mönster

Välj ett migreringsarkiv och ändra sedan mönstret efter behov.

|Migrations mönster | Information |
|---|---|
|**Hiss och Shift**|Det enklaste mönstret. Idealisk om dina datapipelines kan ge stillestånds tid.|
|**Stegvis kopia**|Liknar *lyft och Shift*, men med mindre stillestånds tid. Perfekt för stora mängder data som tar längre tid att kopiera.|
|**Dubbel pipeline**|Perfekt för pipelines som inte har någon avbrotts tid.|
|**Dubbelriktad synkronisering**|Liknar *dubbel pipeline*, men med en mer stegvis metod som passar för mer komplexa pipeliner.|

Låt oss ta en närmare titt på varje mönster.
 
### <a name="lift-and-shift-pattern"></a>Mönster för hiss och Skift

Detta är det enklaste mönstret. 

1. Stoppa alla skrivningar till gen1.

2. Flytta data från gen1 till Gen2. Vi rekommenderar [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md). ACL: er kopieras med data.

3. Punkt inmatnings åtgärder och arbets belastningar till Gen2.

4. Inaktivera gen1.

Kolla in vår exempel kod för mönstret lyft och Shift i exemplet på [lyft och Shift-migreringen](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Lift%20and%20Shift/README.md).

> [!div class="mx-imgBorder"]
> ![mönster för hiss och Skift](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>Att tänka på när du använder mönster för hiss och Skift

: heavy_check_mark: Start punkt från gen1 till Gen2 för alla arbets belastningar samtidigt.

: heavy_check_mark: förväntar sig nedtid under migreringen och start punkt-perioden.

: heavy_check_mark: idealisk för pipelines som kan ge en nedtid och alla appar kan uppgraderas samtidigt.

### <a name="incremental-copy-pattern"></a>Mönster för stegvis kopiering

1. Börja flytta data från gen1 till Gen2. Vi rekommenderar [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md). ACL: er kopieras med data.

2. Kopiera nya data stegvis från gen1.

3. När alla data har kopierats stoppar du alla skrivningar till gen1 och pekar arbets belastningar till Gen2.

4. Inaktivera gen1.

Kolla in vår exempel kod för det stegvisa kopierings mönstret i vårt exempel på en [migrering av stegvis kopiering](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Incremental/README.md).


> [!div class="mx-imgBorder"]
> ![Mönster för stegvis kopiering](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>Att tänka på när du använder det stegvisa kopierings mönstret:

: heavy_check_mark: Start punkt från gen1 till Gen2 för alla arbets belastningar samtidigt.

: heavy_check_mark: förväntar sig nedtid under endast start punkt period.

: heavy_check_mark: idealisk för pipelines där alla appar uppgraderas samtidigt, men data kopieringen kräver mer tid.

### <a name="dual-pipeline-pattern"></a>Mönster för dubbel pipeline

1. Flytta data från gen1 till Gen2. Vi rekommenderar [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md). ACL: er kopieras med data.

2. Hämta in nya data till både gen1 och Gen2.

3. Point-arbetsbelastningar till Gen2.

4. Stoppa alla skrivningar till gen1 och inaktivera gen1.

Kolla in vår exempel kod för det dubbla pipeline-mönstret i vårt [dubbla pipeline-exempel för migrering](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Dual%20pipeline/README.md).

> [!div class="mx-imgBorder"]
> ![Mönster för dubbel pipeline](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>Att tänka på när du använder dubbla pipeline-mönster:

: heavy_check_mark: gen1-och Gen2-pipelines körs sida vid sida.

: heavy_check_mark: stöder noll stillestånds tid.

: heavy_check_mark: idealisk i situationer där dina arbets belastningar och program inte kan ge någon nedtid och du kan mata in i båda lagrings kontona.

### <a name="bi-directional-sync-pattern"></a>Dubbelriktat mönster för dubbelriktad synkronisering

1. Konfigurera dubbelriktad replikering mellan gen1 och Gen2. Vi rekommenderar [WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/). Den erbjuder en reparations funktion för befintliga data.

3. Stoppa alla skrivningar till gen1 när alla flyttningar är slutförda och inaktivera dubbelriktad replikering.

4. Inaktivera gen1.

Kolla in vår exempel kod för det dubbelriktade Sync-mönstret i vårt [dubbelriktade exempel på migrering](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Bi-directional/README.md).

> [!div class="mx-imgBorder"]
> ![Dubbelriktat mönster](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>Att tänka på när du använder dubbelriktat Sync-mönster:

: heavy_check_mark: idealisk för komplexa scenarier som omfattar ett stort antal pipelines och beroenden där en stegvis metod kan vara mer begriplig.  

: heavy_check_mark: migreringen är hög, men det ger stöd sida vid sida för gen1 och Gen2.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de olika delarna av att ställa in säkerhet för ett lagrings konto. Se [Azure Storage säkerhets guide](./security-recommendations.md).
- Optimera prestandan för din Data Lake Store. Se [optimera Azure Data Lake Storage Gen2 för prestanda](data-lake-storage-performance-tuning-guidance.md)
- Läs igenom metod tipsen för att hantera Data Lake Store. Se [metod tips för att använda Azure Data Lake Storage Gen2](data-lake-storage-best-practices.md)