---
title: Använda transaktioner
description: Tips för att implementera transaktioner med dedikerad SQL-pool i Azure Synapse Analytics för utveckling av lösningar.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 8af6802f785718ca6064a34c98d9f6dafc046a2c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594775"
---
# <a name="use-transactions-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Använda transaktioner med dedikerad SQL-pool i Azure Synapse Analytics

Tips för att implementera transaktioner med dedikerad SQL-pool i Azure Synapse Analytics för utveckling av lösningar.

## <a name="what-to-expect"></a>Vad du kan förvänta dig

Som förväntat stöder dedikerad SQL-pool transaktioner som en del av arbets belastningen för data lager. Men för att säkerställa att prestandan för dedikerad SQL-pool upprätthålls vid skalning är vissa funktioner begränsade jämfört med SQL Server. I den här artikeln beskrivs skillnaderna och en lista över de andra.

## <a name="transaction-isolation-levels"></a>Transaktions isolerings nivåer

Dedikerad SQL-pool implementerar syror-transaktioner. Isolerings nivån för transaktions stödet är som standard SKRIVSKYDDad.  Du kan ändra den för att läsa en ISOLERAd ÖGONBLICKs bild isolering genom att aktivera READ_COMMITTED_SNAPSHOT databas alternativ för en användar databas när du är ansluten till huvud databasen.  

När den är aktive rad körs alla transaktioner i den här databasen under den SKRIVSKYDDade ÖGONBLICKs bild ISOLERINGen och inställningen Läs upp ej ALLOKERAd på sessions nivå kommer inte att ske. Se [Alter Database set Options (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?preserve-view=true&view=azure-sqldw-latest) för mer information.

## <a name="transaction-size"></a>Transaktions storlek
En enda data ändrings transaktion är begränsad i storlek. Gränsen tillämpas per distribution. Den totala allokeringen kan därför beräknas genom att gränsen multipliceras med antalet distributioner. 

Om du vill approximera det maximala antalet rader i transaktionen dividerar du distributions gränsen med den totala storleken på varje rad. För kolumner med varierande längd kan du ta en genomsnittlig kolumn längd i stället för att använda den maximala storleken.

I tabellen nedan har följande antaganden gjorts:

* En jämn fördelning av data har inträffat
* Den genomsnittliga rad längden är 250 byte

## <a name="gen2"></a>Gen2

| [DWU](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Cap per distribution (GB) | Antal distributioner | MAXIMAL transaktions storlek (GB) | Antal rader per distribution | Maximalt antal rader per transaktion |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4 000 000 |240 000 000 |
| DW200c |1.5 |60 |90 |6,000,000 |360 000 000 |
| DW300c |2.25 |60 |135 |9 000 000 |540 000 000 |
| DW400c |3 |60 |180 |12 000 000 |720 000 000 |
| DW500c |3.75 |60 |225 |15 000 000 |900 000 000 |
| DW1000c |7.5 |60 |450 |30 000 000 |1 800 000 000 |
| DW1500c |11,25 |60 |675 |45 000 000 |2 700 000 000 |
| DW2000c |15 |60 |900 |60 000 000 |3 600 000 000 |
| DW2500c |18,75 |60 |1125 |75 000 000 |4 500 000 000 |
| DW3000c |22,5 |60 |1 350 |90 000 000 |5 400 000 000 |
| DW5000c |37,5 |60 |2 250 |150 000 000 |9 000 000 000 |
| DW6000c |45 |60 |2 700 |180 000 000 |10 800 000 000 |
| DW7500c |56,25 |60 |3 375 |225 000 000 |13 500 000 000 |
| DW10000c |75 |60 |4 500 |300,000,000 |18 000 000 000 |
| DW15000c |112,5 |60 |6 750 |450 000 000 |27 000 000 000 |
| DW30000c |225 |60 |13 500 |900 000 000 |54 000 000 000 |

## <a name="gen1"></a>Gen1

| [DWU](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Cap per distribution (GB) | Antal distributioner | MAXIMAL transaktions storlek (GB) | Antal rader per distribution | Maximalt antal rader per transaktion |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4 000 000 |240 000 000 |
| DW200 kl |1.5 |60 |90 |6,000,000 |360 000 000 |
| DW300 |2.25 |60 |135 |9 000 000 |540 000 000 |
| DW400 |3 |60 |180 |12 000 000 |720 000 000 |
| DW500 |3.75 |60 |225 |15 000 000 |900 000 000 |
| DW600 |4,5 |60 |270 |18 000 000 |1 080 000 000 |
| DW1000 |7.5 |60 |450 |30 000 000 |1 800 000 000 |
| DW1200 |9 |60 |540 |36 000 000 |2 160 000 000 |
| DW1500 |11,25 |60 |675 |45 000 000 |2 700 000 000 |
| DW2000 |15 |60 |900 |60 000 000 |3 600 000 000 |
| DW3000 |22,5 |60 |1 350 |90 000 000 |5 400 000 000 |
| DW6000 |45 |60 |2 700 |180 000 000 |10 800 000 000 |

Transaktions storleks gränsen tillämpas per transaktion eller åtgärd. Den används inte för alla samtidiga transaktioner. Varje transaktion tillåts därför att skriva denna data mängd till loggen.

Information om hur du optimerar och minimerar mängden data som skrivs till loggen finns i artikeln [metod tips för transaktioner](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

> [!WARNING]
> Den maximala transaktions storleken kan bara uppnås för HASH-värden eller ROUND_ROBIN distribuerade tabeller där data spridningen är jämnt. Om transaktionen skriver data i ett skevat sätt till distributionerna, kommer gränsen att nås före den maximala transaktions storleken.
> <!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>Transaktions tillstånd

Dedikerad SQL-pool använder funktionen XACT_STATE () för att rapportera en misslyckad transaktion med värdet-2. Det här värdet innebär att transaktionen har misslyckats och bara har marker ATS för återställning.

> [!NOTE]
> Användningen av-2 i XACT_STATE-funktionen för att beteckna en misslyckad transaktion representerar olika beteenden för SQL Server. SQL Server använder värdet-1 för att representera en allokerad-transaktion. SQL Server kan tolerera fel i en transaktion utan att den måste markeras som allokerad. Till exempel `SELECT 1/0` skulle orsaka ett fel, men inte framtvinga en transaktion i ett allokerad-tillstånd. SQL Server tillåter också läsningar i allokerad-transaktionen. Dedikerad SQL-pool tillåter dock inte detta. Om ett fel uppstår i en dedikerad transaktion i en SQL-pool, anges-2-tillstånd automatiskt och du kommer inte att kunna göra några fler SELECT-instruktioner förrän instruktionen har återställts. Det är därför viktigt att kontrol lera att program koden för att se om den använder XACT_STATE () som du kan behöva göra kod ändringar.

I SQL Server kan du till exempel se en transaktion som ser ut ungefär så här:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Föregående kod ger följande fel meddelande:

MSG 111233, nivå 16, tillstånd 1, rad 1 111233; Den aktuella transaktionen har avbrutits och alla väntande ändringar har återställts. Orsak: en transaktion i ett återställnings tillstånd återställs inte uttryckligen före en DDL-, DML-eller SELECT-instruktion.

Du får inte utdata från ERROR_ * functions.

I dedikerad SQL-pool måste koden ändras till något av följande:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Det förväntade beteendet observeras nu. Felet i transaktionen hanteras och ERROR_ *-funktioner ger värden som förväntat.

Allt som har ändrats är att återställningen av transaktionen måste ske innan fel informationen i CATCH-blocket lästes.

## <a name="error_line-function"></a>Error_Line ()-funktionen

Det är också värt att notera att dedikerad SQL-pool inte implementerar eller stöder funktionen ERROR_LINE (). Om du har den här funktionen i din kod måste du ta bort den för att vara kompatibel med en dedikerad SQL-pool. Använd fråge etiketter i koden i stället för att implementera motsvarande funktioner. Mer information finns i artikeln om [Etiketter](develop-label.md) .

## <a name="use-of-throw-and-raiserror"></a>Använda THROW-och RAISERROR

THROW är den mer moderna implementeringen för att generera undantag i dedikerad SQL-pool, men RAISERROR stöds också. Det finns några skillnader som är intressanta att betala.

* Användardefinierade fel meddelande nummer får inte ligga i intervallet 100 000-150 000 för THROW
* RAISERROR fel meddelanden åtgärdas med 50 000
* Det finns inte stöd för användning av sys. Messages

## <a name="limitations"></a>Begränsningar

Dedikerad SQL-pool har några andra begränsningar som relaterar till transaktioner. Det här är skillnaderna:

* Inga distribuerade transaktioner
* Inga kapslade transaktioner tillåts
* Inga sparade punkter tillåts
* Inga namngivna transaktioner
* Inga markerade transaktioner
* Inget stöd för DDL, till exempel CREATE TABLE i en användardefinierad transaktion

## <a name="next-steps"></a>Nästa steg

Mer information om hur du optimerar transaktioner finns i [metod tips för transaktioner](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Ytterligare metod tips finns också för [dedikerad SQL-pool](best-practices-dedicated-sql-pool.md) och [Server lös SQL-pool](best-practices-serverless-sql-pool.md).