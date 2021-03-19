---
title: Metod tips för inläsning av data för dedikerade SQL-pooler
description: Rekommendationer och prestanda optimeringar för att läsa in data med hjälp av dedikerade SQL-pooler i Azure Synapse Analytics.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 004084097e2f24a4ceb36f7a016049a574b80b42
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594843"
---
# <a name="best-practices-for-loading-data-using-dedicated-sql-pools-in-azure-synapse-analytics"></a>Metod tips för att läsa in data med dedikerade SQL-pooler i Azure Synapse Analytics

I den här artikeln får du lära dig rekommendationer och prestanda optimeringar för att läsa in data med hjälp av dedikerad SQL-pool.

## <a name="preparing-data-in-azure-storage"></a>Förbereda data i Azure Storage

Du kan minimera svars tiden genom att samplacera ditt lagrings lager och din dedikerade SQL-pool.

När du exporterar data till ett ORC-filformat kan du råka ut för ”slut på minne”-fel i Java när det finns kolumner med mycket text. Du kan undvika denna begränsning genom att bara exportera en del av kolumnerna.

Alla filformat har olika prestandaegenskaper. Den snabbaste inläsningen får du om du använder komprimerade avgränsade textfiler. Skillnaden i prestanda mellan UTF-8 och UTF-16 är minimal.

Dela upp stora komprimerade filer i små komprimerade filer.

## <a name="running-loads-with-enough-compute"></a>Köra belastningar med tillräckligt med beräkning

För högsta hastighet för inläsning, kör du bara ett inläsningsjobb i taget. Om det inte är möjligt kan du köra ett minimalt antal inläsningar samtidigt. Om du förväntar dig ett stort inläsnings jobb kan du skala upp din dedikerade SQL-pool före belastningen.

För att köra inläsningar med lämpliga beräkningsresurser skapar du inläsningsanvändare som är avsedda att köra inläsningar. Klassificera varje inläsnings användare till en speciell arbets belastnings grupp. Om du vill köra en inläsning loggar du in som en inläsnings användare och kör sedan belastningen. Belastningen körs med användarens arbets belastnings grupp.  

### <a name="example-of-creating-a-loading-user"></a>Exempel på att skapa en inläsningsanvändare

I det här exemplet skapas en inläsnings användare som klassificeras till en speciell arbets belastnings grupp Det första steget är att **ansluta till huvudservern** och skapa en inloggning.

```sql
   -- Connect to master
   CREATE LOGIN loader WITH PASSWORD = 'a123STRONGpassword!';
```

Anslut till den dedikerade SQL-poolen och skapa en användare. Följande kod förutsätter att du är ansluten till databasen som heter mySampleDataWarehouse. Det visar hur du skapar en användare som kallas Loader och ger användaren behörighet att skapa tabeller och läsa in med hjälp av [kopierings instruktionen](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true). Sedan klassificerar den användaren till arbets belastnings gruppen DataLoads med maximalt antal resurser. 

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
>Detta är ett Extreme-exempel på hur du allokerar 100%-resurser i SQL-poolen till en enda belastning. Detta ger dig maximal samtidighet på 1. Tänk på att detta endast ska användas för den första belastningen där du behöver skapa ytterligare arbets belastnings grupper med egna konfigurationer för att balanace resurser över dina arbets belastningar. 

Om du vill köra en belastning med resurser för belastnings arbets belastnings gruppen loggar du in som inläsare och kör belastningen.

## <a name="allowing-multiple-users-to-load-polybase"></a>Tillåta att flera användare läser in (PolyBase)

Det finns ofta ett behov av att flera användare ska kunna läsa in data i en dedikerad SQL-pool. Om du läser in med [CREATE TABLE som Select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (PolyBase) krävs kontroll behörigheter för databasen.  CONTROL-behörigheten ger kontrollbehörighet till alla scheman.

Du kanske inte vill att alla användare som läser in ska ha behörighet för alla scheman. Om du vill begränsa behörigheten använder du DENY CONTROL-instruktionen.

Anta att du har följande databasscheman: schema_A för avdelning A och schema_B för avdelning B. Då låter du användare_A och användare_B vara användare för PolyBase-inläsning i avdelning A respektive avdelning B. Båda har beviljats kontrollbehörigheter till databasen. De som skapat schema_A och B låser nu deras scheman med DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A och user_B är nu utelåsta från det andra avd-schemat.

## <a name="loading-to-a-staging-table"></a>Inläsning i en mellanlagringstabell

För att uppnå den snabbaste inläsnings hastigheten för att flytta data till en dedikerad SQL-adresspool, Läs in data i en mellanlagringsplats.  Definiera mellanlagringstabellen som en heap och använd resursallokering som distributionsalternativ.

Tänk på att inläsningen vanligt vis är en två stegs process där du först läser in till en mellanlagringsplats och sedan infogar data i en pool för produktion av dedikerade SQL-pooler. Om produktionstabellen använder en hash-distribution, kan den totala tiden för att läsa in och infoga bli snabbare om du definierar mellanlagringstabellen i hash-distributionen.

Inläsning till mellanlagringstabellen tar längre tid, men det andra steget i att infoga rader i produktionstabellen skapar inte dataförflyttning över distributioner.

## <a name="loading-to-a-columnstore-index"></a>Inläsning till ett columnstore-index

Kolumnlagringsindex kräver en stor mängd minne för att komprimera data i högkvalitativa radgrupper. För bästa komprimerings- och indexeffektivitet behöver kolumnlagringsindexet komprimera högst 1 048 576 rader till varje radgrupp.

Vid brist på minne kanske kolumnlagringsindexet inte kan uppnå den maximala komprimeringsgraden. Det här scenariot påverkar i sin tur prestandan för frågor. Mer detaljer finns i [Minnesoptimering för kolumnlagring](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- För att säkerställa att inläsningsanvändaren har tillräckligt med minne för att uppnå maximal komprimeringsgrad ska du använda inläsningsanvändare som är medlemmar i en mellanstor eller stor resursklass.
- Läs in tillräckligt med rader för att helt fylla de nya radgrupperna. Under en massinläsning komprimeras var 1 048 576:e rad direkt till columnstore som en fullständig radgrupp. Belastningar med färre än 102 400 rader skickar raderna till deltastore där raderna förvaras i ett b-trädindex.

> [!NOTE]
> Om du läser in för få rader kan det hända att de skickas till delta lagringen och inte komprimeras direkt till columnstore-format.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Öka batchstorleken när du använder SqLBulkCopy API eller BCP

Om du läser in med COPY-instruktionen får du högsta data flöde med dedikerade SQL-pooler. Om du inte kan använda KOPIERINGen för att läsa in och måste använda [SqLBulkCopy-API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) eller [BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)bör du fundera på att öka batchstorleken för bättre data flöde.

> [!TIP]
> En batchstorlek mellan 100 K och 1 miljon rader är den rekommenderade bas linjen för att fastställa den optimala storleken för batchstorlek.

## <a name="handling-loading-failures"></a>Hantera inläsningsfel

Vid en inläsning med en extern tabell kan ett felmeddelande som ser ut ungefär så här visas: *"Frågan avbröts. Det högsta tröskelvärdet för avslag nåddes vid inläsning från en extern källa"*. Detta meddelande anger att dina externa data innehåller ändrade poster.

En data post anses vara smutsig om den uppfyller något av följande villkor:

- Data typerna och antalet kolumner matchar inte kolumn definitionerna för den externa tabellen.
- Data överensstämmer inte med det angivna externa fil formatet.

Du kan åtgärda de ändrade posterna genom att se till att definitionerna för den externa tabellen och det externa filformatet är korrekta och att dina externa data följer dessa definitioner.

Om en delmängd av externa data poster är smutsig kan du välja att avvisa dessa poster för dina frågor genom att använda avvisnings alternativen i [skapa extern tabell (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="inserting-data-into-a-production-table"></a>Infoga data i en produktionstabell

En engångsinläsning i en liten tabell med en [INSERT-instruktion](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) eller till och med en regelbunden inläsning på nytt av en sökning kan fungera tillräckligt bra för dina behov med en instruktion som `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Enskilda infogningar är emellertid inte lika effektiva som en massinläsning.

Om du har tusentals eller fler enskilda infogningar under dagen bör du gruppera dem så att du kan infoga dem med en massinläsning.  Utveckla dina processer så att enskilda infogningar bifogas i en fil och skapa sedan en annan process som regelbundet läser in filen.

## <a name="creating-statistics-after-the-load"></a>Skapa statistik efter inläsningen

För att få bättre frågeprestanda är det viktigt att skapa statistik på alla kolumner i alla tabeller efter den första inläsningen eller efter betydande dataändringar. Att skapa statistik kan göras manuellt eller så kan du aktivera [AUTO_CREATE_STATISTICS](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic).

En detaljerad förklaring av statistik finns i [Statistik](sql-data-warehouse-tables-statistics.md). I följande exempel visas hur du manuellt skapar statistik på fem kolumner i Customer_Speeds tabellen.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys-polybase"></a>Rotera lagrings nycklar (PolyBase)

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

- Mer information om KOPIERINGs instruktionen eller polybasen när du utformar en process för att extrahera, läsa in och transformera (ELT) finns i [design ELT for Azure Synapse Analytics](design-elt-data-loading.md).
- För en inläsnings kurs [använder du kopierings instruktionen för att läsa in data från Azure Blob Storage till SYNAPSE SQL](./load-data-from-azure-blob-storage-using-copy.md).
- Om du vill övervaka datainläsningen läser du [Övervaka arbetsbelastningen med datahanteringsvyer](sql-data-warehouse-manage-monitor.md).