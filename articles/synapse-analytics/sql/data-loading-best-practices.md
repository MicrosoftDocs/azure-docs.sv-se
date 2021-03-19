---
title: Metodtips för datainläsning
description: Rekommendationer och prestanda optimeringar för att läsa in data i en dedikerad Azure Synapse-analys i SQL-pool.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 33212d44fcb6be3f01cf968d00751d55e3bd7b8f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585459"
---
# <a name="best-practices-for-loading-data-into-a-dedicated-sql-pool-azure-synapse-analytics"></a>Metod tips för att läsa in data i en dedikerad SQL-pool Azure Synapse Analytics

I den här artikeln hittar du rekommendationer och prestanda optimeringar för att läsa in data.

## <a name="prepare-data-in-azure-storage"></a>Förbereda data i Azure Storage

Du kan minimera svars tiden genom att samplacera ditt lagrings lager och din dedikerade SQL-pool.

När du exporterar data till ett ORC-filformat kan du råka ut för ”slut på minne”-fel i Java när det finns kolumner med mycket text. Du kan undvika denna begränsning genom att bara exportera en del av kolumnerna.

PolyBase kan inte läsa in rader som har mer än 1 000 000 byte data. När du placerar data i textfiler i Azure Blob Storage eller Azure Data Lake Store måste dessa data vara mindre än 1 000 000 byte. Den här begränsningen av byte gäller oavsett tabellschemat.

Alla filformat har olika prestandaegenskaper. Den snabbaste inläsningen får du om du använder komprimerade avgränsade textfiler. Skillnaden i prestanda mellan UTF-8 och UTF-16 är minimal.

Dela upp stora komprimerade filer i små komprimerade filer.

## <a name="run-loads-with-enough-compute"></a>Kör belastningar med tillräckligt med beräkning

För högsta hastighet för inläsning, kör du bara ett inläsningsjobb i taget. Om detta inte är möjligt, kör du ett minimalt antal belastningar samtidigt. Om du förväntar dig ett stort inläsnings jobb kan du skala upp din dedikerade SQL-pool före belastningen.

För att köra inläsningar med lämpliga beräkningsresurser skapar du inläsningsanvändare som är avsedda att köra inläsningar. Tilldela varje inläsnings användare till en angiven resurs klass eller arbets belastnings grupp. Om du vill köra en inläsning loggar du in som en inläsnings användare och kör sedan belastningen. Inläsningen körs med användarens resursklass.  Den här metoden är enklare än att försöka ändra en användares resursklass så att den passar det aktuella behovet av resursklass.

### <a name="create-a-loading-user"></a>Skapa en inläsnings användare

I det här exemplet skapas en inläsningsanvändare för resursklassen staticrc20. Det första steget är att **ansluta till huvudservern** och skapa en inloggning.

```sql
   -- Connect to master
   CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
```

Anslut till informationslagret och skapa en användare. Följande kod förutsätter att du är ansluten till databasen mySampleDataWarehouse. Det visar hur du skapar en användare med namnet LoaderRC20 och ger användare kontrollbehörighet på en databas. Det lägger sedan till användaren som en medlem i staticrc20-databasrollen.  

```sql
   -- Connect to the database
   CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
   GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
   EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
```

Om du vill köra en belastning med resurser för resurs klasserna staticRC20 loggar du in som LoaderRC20 och kör belastningen.

Kör inläsningar under statiska i stället för dynamiska resursklasser. Att använda statiska resurs klasser garanterar samma resurser oavsett dina [informations lager enheter](resource-consumption-models.md). Om du använder en dynamisk resursklass varierar resurserna beroende på din servicenivå. För dynamiska klasser innebär en lägre servicenivå att du troligtvis behöver använda en större resursklass för din inläsningsanvändare.

## <a name="allow-multiple-users-to-load"></a>Tillåt att flera användare läser in

Det finns ofta ett behov av att ha flera användare som kan läsa in data i informationslagret. Inläsning med [CREATE TABLE as Select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) kräver kontroll behörigheter för databasen.  CONTROL-behörigheten ger kontrollbehörighet till alla scheman. Du kanske inte vill att alla användare som läser in ska ha behörighet för alla scheman. Om du vill begränsa behörigheten använder du DENY CONTROL-instruktionen.

Anta att du har följande databasscheman: schema_A för avdelning A och schema_B för avdelning B. Då låter du användare_A och användare_B vara användare för PolyBase-inläsning i avdelning A respektive avdelning B. Båda har beviljats kontrollbehörigheter till databasen. De som skapat schema_A och B låser nu deras scheman med DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A och user_B är nu utelåsta från det andra avd-schemat.

## <a name="load-to-a-staging-table"></a>Inläsning i en mellanlagringstabell

För att uppnå högsta inläsningshastighet vid flytt av data till en informationslagertabell, läs in data i en mellanlagringstabellen.  Definiera mellanlagringstabellen som en heap och använd resursallokering som distributionsalternativ.

Överväg att inläsning vanligtvis är en tvåstegsprocess där du först läser in till en mellanlagringstabellen och sedan infogar data i en informationslagertabell för produktion. Om produktionstabellen använder en hash-distribution, kan den totala tiden för att läsa in och infoga bli snabbare om du definierar mellanlagringstabellen i hash-distributionen. Inläsning till mellanlagringstabellen tar längre tid, men det andra steget i att infoga rader i produktionstabellen skapar inte dataförflyttning över distributioner.

## <a name="load-to-a-columnstore-index"></a>Läs in till ett columnstore-index

Kolumnlagringsindex kräver en stor mängd minne för att komprimera data i högkvalitativa radgrupper. För bästa komprimerings- och indexeffektivitet behöver kolumnlagringsindexet komprimera högst 1 048 576 rader till varje radgrupp. Vid brist på minne kanske kolumnlagringsindexet inte kan uppnå den maximala komprimeringsgraden. Detta påverkar frågans prestanda. Mer detaljer finns i [Minnesoptimering för kolumnlagring](data-load-columnstore-compression.md).

- För att säkerställa att inläsningsanvändaren har tillräckligt med minne för att uppnå maximal komprimeringsgrad ska du använda inläsningsanvändare som är medlemmar i en mellanstor eller stor resursklass.
- Läs in tillräckligt med rader för att helt fylla de nya radgrupperna. Vid en Mass inläsning komprimeras varje 1 048 576-rad direkt till columnstore-filen som en fullständig radgrupps. Belastningar med färre än 102 400 rader skickar raderna till deltastore där raderna förvaras i ett b-trädindex. Om du läser in för få rader kan alla rader hamna i deltalagringen och inte bli komprimerade direkt i kolumnlagringsformatet.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Öka batchstorleken när du använder SQLBulkCopy API eller BCP

Som nämnts tidigare ger inläsning med PolyBase det högsta data flödet med Synapse SQL-pool. Om du inte kan använda PolyBase för att läsa in och måste använda SQLBulkCopy-API: et (eller BCP), bör du överväga att öka batchstorleken för bättre data flöde – en bra tumregel är en batchstorlek mellan 100 000 och 1 miljon rader.

## <a name="manage-loading-failures"></a>Hantera inläsnings problem

Vid en inläsning med en extern tabell kan ett felmeddelande som ser ut ungefär så här visas: *"Frågan avbröts. Det högsta tröskelvärdet för avslag nåddes vid inläsning från en extern källa"*. Detta meddelande anger att dina externa data innehåller ändrade poster. En datapost anses vara ändrade om datatyperna och antalet kolumner inte matchar kolumndefinitionen för den externa tabellen eller om data inte följer det angivna externa filformatet.

Du kan åtgärda de ändrade posterna genom att se till att definitionerna för den externa tabellen och det externa filformatet är korrekta och att dina externa data följer dessa definitioner. Om en delmängd av de externa dataposterna är felaktiga kan du välja att avvisa dessa poster för dina frågor genom att använda avvisningsalternativen i CREATE EXTERNAL TABLE.

## <a name="insert-data-into-a-production-table"></a>Infoga data i en produktions tabell

En engångsinläsning i en liten tabell med en [INSERT-instruktion](/sql/t-sql/statements/insert-transact-sql?view=azure-sqldw-latest&preserve-view=true) eller till och med en regelbunden inläsning på nytt av en sökning kan fungera tillräckligt bra för dina behov med en instruktion som `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Singleton-infogningar är dock inte lika effektiva som att utföra en Mass inläsning.

Om du har tusentals eller fler enskilda infogningar under dagen bör du gruppera dem så att du kan infoga dem med en massinläsning.  Utveckla dina processer så att enskilda infogningar bifogas i en fil och skapa sedan en annan process som regelbundet läser in filen.

## <a name="create-statistics-after-the-load"></a>Skapa statistik efter inläsningen

För att förbättra prestanda för frågor är det viktigt att skapa statistik på alla kolumner i alla tabeller efter den första inläsningen, eller att större ändringar har inträffat i data. Skapa statistik kan göras manuellt eller så kan du aktivera [statistik för automatisk skapande](../sql-data-warehouse/sql-data-warehouse-tables-statistics.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

En detaljerad förklaring av statistik finns i [Statistik](develop-tables-statistics.md). I följande exempel visas hur du manuellt skapar statistik på fem kolumner i Customer_Speeds tabellen.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>Rotera lagringsnycklar

Det är en bra säkerhetsrutin att regelbundet ändra åtkomstnyckeln till din Blob Storage. Du har två lagringsnycklar för ditt blob storage-konto, som gör det möjligt att överföra nycklarna.

Så här roterar du Azure Storage-kontonycklar:

För varje lagringskonto vars nyckel har ändrats utfärdar du [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true).

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

- Mer information om PolyBase och design av en process för att extrahera, läsa in och transformera (ELT) finns i [design ELT for Azure Synapse Analytics](../sql-data-warehouse/design-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
- För en inläsnings kurs [använder du PolyBase för att läsa in data från Azure Blob Storage till Azure Synapse Analytics](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).
- Om du vill övervaka datainläsningen läser du [Övervaka arbetsbelastningen med datahanteringsvyer](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).