---
title: In-Memory OLTP förbättrar SQL-TXN prestanda
description: Anta In-Memory OLTP för att förbättra transaktions prestanda i en befintlig databas i Azure SQL Database och Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-database
ms.custom: sqldbrb=2
ms.subservice: development
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 11/07/2018
ms.openlocfilehash: e17e98e784b7453c87814c5cce5c03568f66b1cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91619754"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-azure-sql-database-and-azure-sql-managed-instance"></a>Använd In-Memory OLTP för att förbättra program prestandan i Azure SQL Database och Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

[Minnes intern OLTP](in-memory-oltp-overview.md) kan användas för att förbättra prestanda för transaktions bearbetning, data inmatning och tillfälliga data scenarier, i [Premium-och affärskritisk nivå](database/service-tiers-vcore.md) databaser utan att öka pris nivån.

> [!NOTE]
> Lär dig hur [kvorum dubblerar nyckel databasens arbets belastning och sänker DTU med 70% med Azure SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

Följ dessa steg för att anta In-Memory OLTP i den befintliga databasen.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Steg 1: kontrol lera att du använder en Premium-och Affärskritisk nivå databas

In-Memory OLTP stöds endast i Premium-och Affärskritisk-Tier-databaser. In-Memory stöds om det returnerade resultatet är 1 (inte 0):

```sql
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* står för *bearbetning av extrema transaktioner*

## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Steg 2: identifiera objekt som ska migreras till In-Memory OLTP

SSMS innehåller en **översikts rapport över transaktions prestanda analys** som du kan köra mot en databas med en aktiv arbets belastning. Rapporten identifierar tabeller och lagrade procedurer som är kandidater för migrering till In-Memory OLTP.

För att generera rapporten i SSMS:

* Högerklicka på noden databas i **Object Explorer**.
* Klicka på **rapporter**  >    >  **Översikt över prestanda analys av transaktioner** i standard rapporter.

Mer information finns i [bestämma om en tabell eller lagrad procedur ska hamna i In-Memory OLTP](/sql/relational-databases/in-memory-oltp/determining-if-a-table-or-stored-procedure-should-be-ported-to-in-memory-oltp).

## <a name="step-3-create-a-comparable-test-database"></a>Steg 3: skapa en jämförbar test databas

Anta att rapporten visar att databasen har en tabell som kan dra nytta av att konverteras till en minnesoptimerade tabell. Vi rekommenderar att du först testar att bekräfta indikeringen genom att testa.

Du behöver en test kopia av produktions data basen. Test databasen ska ha samma service nivå nivå som produktions databasen.

Du kan under lätta testningen genom att justera test databasen på följande sätt:

1. Anslut till test databasen med hjälp av SSMS.
2. För att undvika att du behöver alternativet WITH (ÖGONBLICKs bild) i frågor, anger du databas alternativet som det visas i följande T-SQL-uttryck:

   ```sql
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Steg 4: Migrera tabeller

Du måste skapa och fylla i en minnesoptimerade kopia av den tabell som du vill testa. Du kan skapa den med hjälp av antingen:

* Guiden för praktisk minnes optimering i SSMS.
* Manuell T-SQL.

### <a name="memory-optimization-wizard-in-ssms"></a>Guiden minnes optimering i SSMS

Använd detta alternativ för migrering:

1. Anslut till test databasen med SSMS.
2. Högerklicka på tabellen i **Object Explorer** och klicka sedan på **guiden för minnes optimering**.

   Guiden för **tabell minnes optimerings** guide visas.
3. I guiden klickar du på **migrerings verifiering** (eller **Nästa** ) för att se om tabellen har funktioner som inte stöds i minnesoptimerade tabeller. Mer information finns i:

   * *Check lista för minnes optimering* i [minnes optimerings Advisor](/sql/relational-databases/in-memory-oltp/memory-optimization-advisor).
   * [Transact-SQL-konstruktioner stöds inte av In-Memory OLTP](/sql/relational-databases/in-memory-oltp/transact-sql-constructs-not-supported-by-in-memory-oltp).
   * [Migrerar till In-Memory OLTP](/sql/relational-databases/in-memory-oltp/plan-your-adoption-of-in-memory-oltp-features-in-sql-server).
4. Om tabellen inte innehåller funktioner som inte stöds kan Advisor utföra det faktiska schemat och datamigreringen.

### <a name="manual-t-sql"></a>Manuell T-SQL

Använd detta alternativ för migrering:

1. Anslut till test databasen med hjälp av SSMS (eller ett liknande verktyg).
2. Hämta det fullständiga T-SQL-skriptet för din tabell och dess index.

   * I SSMS högerklickar du på noden tabell.
   * Klicka på **skript tabell som**  >  **skapa till**  >  **ny fråga**.
3. I fönstret skript lägger du till med (MEMORY_OPTIMIZED = på) i CREATE TABLE-instruktionen.
4. Om det finns ett GRUPPERat index ändrar du det till inklustrat.
5. Byt namn på den befintliga tabellen med hjälp av SP_RENAME.
6. Skapa den nya minnesoptimerade kopian av tabellen genom att köra det redigerade CREATE TABLE skriptet.
7. Kopiera data till den minnesoptimerade tabellen med hjälp av Infoga... VÄLJ * I:

```sql
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```

## <a name="step-5-optional-migrate-stored-procedures"></a>Steg 5 (valfritt): Migrera lagrade procedurer

Funktionen In-Memory kan också ändra en lagrad procedur för bättre prestanda.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Att tänka på med internt kompilerade lagrade procedurer

En internt kompilerad lagrad procedur måste ha följande alternativ i sin T-SQL WITH-sats:

* NATIVE_COMPILATION
* SCHEMABINDING: tabeller som den lagrade proceduren inte kan ha sina kolumn definitioner ändrade på något sätt som skulle påverka den lagrade proceduren, om du inte släpper den lagrade proceduren.

En ursprunglig modul måste använda ett Big [Atomic-block](/sql/relational-databases/in-memory-oltp/atomic-blocks-in-native-procedures) för transaktions hantering. Det finns ingen roll för en explicit BEGIN TRANSACTION eller för återställnings transaktion. Om din kod identifierar en överträdelse av en affärs regel kan den avsluta Atomic-blocket med en [Throw](/sql/t-sql/language-elements/throw-transact-sql) -instruktion.

### <a name="typical-create-procedure-for-natively-compiled"></a>Typisk skapa procedur för internt kompilerade

T-SQL för att skapa en internt kompilerad lagrad procedur liknar vanligt vis följande mall:

```sql
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

* För TRANSACTION_ISOLATION_LEVEL är ÖGONBLICKs bilden det vanligaste värdet för den internt kompilerade lagrade proceduren. En delmängd av de andra värdena stöds dock också:
  
  * UPPREPNINGS BAR LÄSNING
  * SERIALISERA
* Språkvärdet måste finnas i vyn sys. language.

### <a name="how-to-migrate-a-stored-procedure"></a>Så här migrerar du en lagrad procedur

Stegen för migreringen är:

1. Hämta skriptet för att skapa procedur till den vanliga tolkade lagrade proceduren.
2. Skriv om rubriken så att den matchar föregående mall.
3. Kontrol lera om den lagrade procedurens T-SQL-kod använder funktioner som inte stöds för internt kompilerade lagrade procedurer. Implementera lösningar vid behov.

   Mer information finns i [migrerings problem för internt kompilerade lagrade procedurer](/sql/relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables).
4. Byt namn på den gamla lagrade proceduren med hjälp av SP_RENAME. Eller helt enkelt släppa det.
5. Kör den redigerade CREATE PROCEDURe T-SQL-skriptet.

## <a name="step-6-run-your-workload-in-test"></a>Steg 6: kör din arbets belastning i testet

Kör en arbets belastning i test databasen som liknar den arbets belastning som körs i produktions databasen. Detta bör Visa prestanda som uppnås genom att använda In-Memory funktionen för tabeller och lagrade procedurer.

Större attribut för arbets belastningen är:

* Antal samtidiga anslutningar.
* Läs-/skriv förhållande.

För att skräddarsy och köra test arbets belastningen bör du överväga att använda det praktiska `ostress.exe` verktyget, som illustreras i den här [InMemory-](in-memory-oltp-overview.md) artikeln.

Du kan minimera nätverks fördröjningen genom att köra testet i samma Azure-geografiska region där databasen finns.

## <a name="step-7-post-implementation-monitoring"></a>Steg 7: övervakning efter implementering

Överväg att övervaka prestanda effekterna av dina In-Memory implementeringar i produktions miljön:

* [Övervaka In-Memory-lagring](in-memory-oltp-monitor-space.md).
* [Övervakning med hjälp av vyer för dynamisk hantering](database/monitoring-with-dmvs.md)

## <a name="related-links"></a>Relaterade länkar

* [Minnes intern OLTP (minnes intern optimering)](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
* [Introduktion till internt kompilerade lagrade procedurer](/sql/relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables)
* [Optimering av minnes optimering](/sql/relational-databases/in-memory-oltp/memory-optimization-advisor)
