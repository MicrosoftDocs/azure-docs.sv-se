---
title: Metodtips för dataläsning för dedikerade SQL-pooler
description: Rekommendationer och prestandaoptimeringar för inläsning av data med dedikerade SQL-pooler i Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 294b2a5188f0dfd8cb29f21bdbb29236b1740231
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565873"
---
# <a name="best-practices-for-loading-data-using-dedicated-sql-pools-in-azure-synapse-analytics"></a>Metodtips för att läsa in data med dedikerade SQL-pooler i Azure Synapse Analytics

I den här artikeln får du lära dig rekommendationer och prestandaoptimeringar för att läsa in data med dedikerad SQL-pool.

## <a name="preparing-data-in-azure-storage"></a>Förbereda data i Azure Storage

Om du vill minimera svarstiden kan du samplacera lagringslagret och din dedikerade SQL-pool.

När du exporterar data till ett ORC-filformat kan du råka ut för ”slut på minne”-fel i Java när det finns kolumner med mycket text. Du kan undvika denna begränsning genom att bara exportera en del av kolumnerna.

Alla filformat har olika prestandaegenskaper. Den snabbaste inläsningen får du om du använder komprimerade avgränsade textfiler. Skillnaden i prestanda mellan UTF-8 och UTF-16 är minimal.

Dela upp stora komprimerade filer i små komprimerade filer.

## <a name="running-loads-with-enough-compute"></a>Köra belastningar med tillräckligt med beräkning

För högsta hastighet för inläsning, kör du bara ett inläsningsjobb i taget. Om det inte är möjligt kör du ett minimalt antal belastningar samtidigt. Om du förväntar dig ett stort inläsningsjobb kan du överväga att skala upp din dedikerade SQL-pool före belastningen.

För att köra inläsningar med lämpliga beräkningsresurser skapar du inläsningsanvändare som är avsedda att köra inläsningar. Klassificera varje inläsningsanvändare till en specifik arbetsbelastningsgrupp. Om du vill köra en inläsning loggar du in som en av inläsningsanvändarna och kör sedan inläsningen. Inläsningen körs med användarens arbetsbelastningsgrupp.  

### <a name="example-of-creating-a-loading-user"></a>Exempel på att skapa en inläsningsanvändare

I det här exemplet skapas en inläsningsanvändare som klassificeras till en specifik arbetsbelastningsgrupp. Det första steget är att **ansluta till huvudservern** och skapa en inloggning.

```sql
   -- Connect to master
   CREATE LOGIN loader WITH PASSWORD = 'a123STRONGpassword!';
```

Anslut till den dedikerade SQL-poolen och skapa en användare. Följande kod förutsätter att du är ansluten till databasen mySampleDataWarehouse. Den visar hur du skapar en användare med namnet loader och ger användaren behörighet att skapa tabeller och läsa in med [copy-instruktionen](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true). Sedan klassificerar den användaren till arbetsbelastningsgruppen DataLoads med maximalt antal resurser. 

```sql
   -- Connect to the dedicated SQL pool
   CREATE USER loader FOR LOGIN loader;
   GRANT ADMINISTER DATABASE BULK OPERATIONS TO loader;
   GRANT INSERT ON <yourtablename> TO loader;
   GRANT SELECT ON <yourtablename> TO loader;
   GRANT CREATE TABLE TO loader;
   GRANT ALTER ON SCHEMA::dbo TO loader;
   
   CREATE WORKLOAD GROUP DataLoads
   WITH ( 
       MIN_PERCENTAGE_RESOURCE = 100
       ,CAP_PERCENTAGE_RESOURCE = 100
       ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 100
    );

   CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
   WITH (
         WORKLOAD_GROUP = 'DataLoads'
       ,MEMBERNAME = 'loader'
   );
```
<br><br>
>[!IMPORTANT] 
>Det här är ett extremt exempel på att allokera 100 % resurser i SQL-poolen till en enda belastning. Detta ger dig en maximal samtidighet på 1. Tänk på att detta endast ska användas för den första belastningen där du behöver skapa ytterligare arbetsbelastningsgrupper med egna konfigurationer för att minska resurserna i dina arbetsbelastningar. 

Om du vill köra en inläsning med resurser för arbetsbelastningsgruppen för inläsning loggar du in som inläsare och kör inläsningen.

## <a name="allowing-multiple-users-to-load-polybase"></a>Tillåta flera användare att läsa in (PolyBase)

Det finns ofta ett behov av att ha flera användare som läser in data till en dedikerad SQL-pool. Inläsning [med CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (PolyBase) kräver kontrollbehörighet för databasen.  CONTROL-behörigheten ger kontrollbehörighet till alla scheman.

Du kanske inte vill att alla användare som läser in ska ha behörighet för alla scheman. Om du vill begränsa behörigheten använder du DENY CONTROL-instruktionen.

Anta att du har följande databasscheman: schema_A för avdelning A och schema_B för avdelning B. Då låter du användare_A och användare_B vara användare för PolyBase-inläsning i avdelning A respektive avdelning B. Båda har beviljats kontrollbehörigheter till databasen. De som skapat schema_A och B låser nu deras scheman med DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A och user_B nu utelåsta från den andra deptens schema.

## <a name="loading-to-a-staging-table"></a>Inläsning i en mellanlagringstabell

Läs in data till en mellanlagringstabell för att uppnå den snabbaste inläsningshastigheten för att flytta data till en dedikerad SQL-pooltabell.  Definiera mellanlagringstabellen som en heap och använd resursallokering som distributionsalternativ.

Tänk på att inläsning vanligtvis är en tvåstegsprocess där du först läser in till en mellanlagringstabell och sedan infogar data i en dedikerad SQL-pooltabell för produktion. Om produktionstabellen använder en hash-distribution, kan den totala tiden för att läsa in och infoga bli snabbare om du definierar mellanlagringstabellen i hash-distributionen.

Inläsning till mellanlagringstabellen tar längre tid, men det andra steget i att infoga rader i produktionstabellen skapar inte dataförflyttning över distributioner.

## <a name="loading-to-a-columnstore-index"></a>Inläsning till ett columnstore-index

Kolumnlagringsindex kräver en stor mängd minne för att komprimera data i högkvalitativa radgrupper. För bästa komprimerings- och indexeffektivitet behöver kolumnlagringsindexet komprimera högst 1 048 576 rader till varje radgrupp.

Vid brist på minne kanske kolumnlagringsindexet inte kan uppnå den maximala komprimeringsgraden. Det här scenariot påverkar i sin tur frågeprestanda. Mer detaljer finns i [Minnesoptimering för kolumnlagring](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- För att säkerställa att inläsningsanvändaren har tillräckligt med minne för att uppnå maximal komprimeringsgrad ska du använda inläsningsanvändare som är medlemmar i en mellanstor eller stor resursklass.
- Läs in tillräckligt med rader för att helt fylla de nya radgrupperna. Under en massinläsning komprimeras var 1 048 576:e rad direkt till columnstore som en fullständig radgrupp. Belastningar med färre än 102 400 rader skickar raderna till deltastore där raderna förvaras i ett b-trädindex.

> [!NOTE]
> Om du läser in för få rader kan de dirigeras till deltastore och inte komprimeras direkt till columnstore-format.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Öka batchstorleken när du använder SqLBulkCopy API eller bcp

Inläsning med COPY-instruktionen ger det högsta dataflödet med dedikerade SQL-pooler. Om du inte kan använda COPY för att läsa in och måste använda [SqLBulkCopy-API:et](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) eller [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)bör du överväga att öka batchstorleken för bättre dataflöde.

> [!TIP]
> En batchstorlek mellan 100 K och 1 miljon rader är den rekommenderade baslinjen för att fastställa optimal batchstorlekskapacitet.

## <a name="handling-loading-failures"></a>Hantera inläsningsfel

Vid en inläsning med en extern tabell kan ett felmeddelande som ser ut ungefär så här visas: *"Frågan avbröts. Det högsta tröskelvärdet för avslag nåddes vid inläsning från en extern källa"*. Detta meddelande anger att dina externa data innehåller ändrade poster.

En datapost anses vara grov om den uppfyller något av följande villkor:

- Datatyperna och antalet kolumner matchar inte kolumndefinitionerna för den externa tabellen.
- Data överensstämmer inte med det angivna externa filformatet.

Du kan åtgärda de ändrade posterna genom att se till att definitionerna för den externa tabellen och det externa filformatet är korrekta och att dina externa data följer dessa definitioner.

Om en delmängd av externa dataposter är feländade kan du välja att avvisa dessa poster för dina frågor med hjälp av avslagsalternativen i [CREATE EXTERNAL TABLE (Transact-SQL).](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="inserting-data-into-a-production-table"></a>Infoga data i en produktionstabell

En engångsinläsning i en liten tabell med en [INSERT-instruktion](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) eller till och med en regelbunden inläsning på nytt av en sökning kan fungera tillräckligt bra för dina behov med en instruktion som `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Enskilda infogningar är emellertid inte lika effektiva som en massinläsning.

Om du har tusentals eller fler enskilda infogningar under dagen bör du gruppera dem så att du kan infoga dem med en massinläsning.  Utveckla dina processer så att enskilda infogningar bifogas i en fil och skapa sedan en annan process som regelbundet läser in filen.

## <a name="creating-statistics-after-the-load"></a>Skapa statistik efter inläsningen

För att få bättre frågeprestanda är det viktigt att skapa statistik på alla kolumner i alla tabeller efter den första inläsningen eller efter betydande dataändringar. Du kan skapa statistik manuellt eller aktivera [AUTO_CREATE_STATISTICS](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic).

En detaljerad förklaring av statistik finns i [Statistik](sql-data-warehouse-tables-statistics.md). I följande exempel visas hur du manuellt skapar statistik för fem kolumner i Customer_Speed tabellen.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys-polybase"></a>Rotera lagringsnycklar (PolyBase)

Det är en bra säkerhetsrutin att regelbundet ändra åtkomstnyckeln till din Blob Storage. Du har två lagringsnycklar för ditt blob storage-konto, som gör det möjligt att överföra nycklarna.

Så här roterar du Azure Storage-kontonycklar:

För varje lagringskonto vars nyckel har ändrats utfärdar du [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Exempel:

Den ursprungliga nyckeln skapas

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

Rotera nyckel från nyckel 1 till nyckel 2

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2'
```

Det behövs inga andra ändringar i underliggande externa datakällor.

## <a name="next-steps"></a>Nästa steg

- Mer information om COPY-instruktionen eller PolyBase när du utformar en ELT-process (extrahering, inläsning och transformering) finns i [Utforma ELT för Azure Synapse Analytics](design-elt-data-loading.md).
- För en självstudie om [inläsning använder du COPY-instruktionen för att läsa in data från Azure Blob Storage till Synapse SQL](./load-data-from-azure-blob-storage-using-copy.md).
- Om du vill övervaka datainläsningen läser du [Övervaka arbetsbelastningen med datahanteringsvyer](sql-data-warehouse-manage-monitor.md).