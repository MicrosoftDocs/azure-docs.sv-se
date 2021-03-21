---
title: Kostnadshantering för serverlös SQL-pool
description: Det här dokumentet beskriver hur du hanterar kostnaden för SQL-poolen utan server och hur data som bearbetas beräknas när du frågar efter data i Azure Storage.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 8a26f8ced5e91810f8cadff0a27796dc817e6517
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94491592"
---
# <a name="cost-management-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Kostnads hantering för Server lös SQL-pool i Azure Synapse Analytics

Den här artikeln förklarar hur du kan beräkna och hantera kostnader för Server lös SQL-pool i Azure Synapse Analytics:
- Uppskatta mängden data som bearbetas innan en fråga utfärdas
- Använd funktionen kostnads kontroll för att ställa in budgeten

Ta reda på att kostnaderna för Server lös SQL-pool i Azure Synapse Analytics bara är en del av månads kostnaderna på din Azure-faktura. Om du använder andra Azure-tjänster debiteras du för alla Azure-tjänster och-resurser som används i din Azure-prenumeration, inklusive tjänster från tredje part. Den här artikeln beskriver hur du planerar för och hanterar kostnader för Server lös SQL-pool i Azure Synapse Analytics.

## <a name="data-processed"></a>Bearbetade data

*Bearbetade data* är mängden data som systemet lagrar tillfälligt medan en fråga körs. Bearbetade data består av följande kvantiteter:

- Mängden data som läses från lagringen. Detta belopp inkluderar:
  - Data lästes vid läsning av data.
  - Data lästes vid läsning av metadata (för fil format som innehåller metadata, t. ex. Parquet).
- Mängden data i mellanliggande resultat. Dessa data överförs mellan noder medan frågan körs. Den innehåller data överföringen till din slut punkt i ett okomprimerat format. 
- Mängden data som skrivs till lagringen. Om du använder CETAS för att exportera resultat uppsättningen till Storage, läggs mängden data som skrivs ut till i mängden data som bearbetas för den valda delen av CETAS.

Läsning av filer från lagring är starkt optimerat. Processen använder:

- För hämtning, som kan lägga till viss overhead till mängden data som läses. Om en fråga läser en hel fil finns det ingen omkostnader. Om en fil läses delvis, som i de x främsta frågorna, läses en bit mer data med hjälp av för hämtning.
- En optimerad CSV-parser (kommaavgränsade värden). Om du använder PARSER_VERSION = 2.0 för att läsa CSV-filer ökar mängden data som läses från lagrings utrymme något. En optimerad CSV-parser läser filer parallellt, i segment med samma storlek. Segment innehåller inte nödvändigt vis hela rader. För att se till att alla rader tolkas, läser den optimerade CSV-parsern också små fragment av intilliggande segment. Den här processen lägger till en liten del av omkostnaderna.

## <a name="statistics"></a>Statistik

SQL-poolens fråga optimering förlitar sig på statistik för att skapa optimala fråge körnings planer. Du kan skapa statistik manuellt. Annars skapar Server lös SQL-poolen automatiskt. I båda fallen skapas statistik genom att köra en separat fråga som returnerar en särskild kolumn i en angiven samplings frekvens. Den här frågan har en associerad mängd bearbetade data.

Om du kör samma eller någon annan fråga som skulle dra nytta av statistik som skapats, kommer statistik att återanvändas om möjligt. Det finns inga ytterligare data som bearbetas för att skapa statistik.

När statistik skapas för en Parquet-kolumn läses endast den relevanta kolumnen in från filer. När statistik skapas för en CSV-kolumn läses och tolkas hela filer.

## <a name="rounding"></a>Avrundning

Mängden data som bearbetas avrundas uppåt till närmaste MB per fråga. Varje fråga har minst 10 MB bearbetade data.

## <a name="what-data-processed-doesnt-include"></a>Vilka data som bearbetas omfattar inte

- Metadata på server nivå (t. ex. inloggningar, roller och autentiseringsuppgifter på server nivå).
- Databaser som du skapar i slut punkten. Dessa databaser innehåller endast metadata (t. ex. användare, roller, scheman, vyer, infogade tabell värdes funktioner [TVFs], lagrade procedurer, autentiseringsuppgifter för databas, externa data källor, externa fil format och externa tabeller).
  - Om du använder schema härledning, läses filfragmenten för att härleda kolumn namn och data typer och mängden data som läses läggs till i mängden data som bearbetas.
- DDL-uttryck (Data Definition Language), förutom CREATE STATISTICS-instruktionen, eftersom den bearbetar data från lagring baserat på den angivna exempel procenten.
- Frågor med enbart metadata.

## <a name="reducing-the-amount-of-data-processed"></a>Minska mängden bearbetade data

Du kan optimera mängden data som bearbetas per fråga och förbättra prestandan genom att partitionera och konvertera data till ett komprimerat kolumnbaserade format som Parquet.

## <a name="examples"></a>Exempel

Föreställ dig tre tabeller.

- Den population_csv tabellen backas upp med 5 TB CSV-filer. Filerna är ordnade i fem lika stora kolumner.
- Population_parquet tabellen har samma data som population_csvs tabellen. Den backas upp med 1 TB av Parquet-filer. Den här tabellen är mindre än föregående, eftersom data komprimeras i Parquet-format.
- Very_small_csv tabellen backas upp av 100 KB CSV-filer.

**Fråga 1**: Välj sum (population) från population_csv

Den här frågan läser och tolkar hela filer för att hämta värden för populations kolumnen. Noderna bearbetar fragmenten i den här tabellen och populations summan för varje fragment överförs mellan noder. Den sista summan överförs till din slut punkt. 

Den här frågan bearbetar 5 TB data plus ett litet antal omkostnader för att överföra summor av fragment.

**Fråga 2**: Välj sum (population) från population_parquet

När du frågar efter komprimerade och kolumnbaserade format som Parquet läses mindre data än i fråga 1. Du ser det här resultatet eftersom SQL-poolen utan Server läser en komprimerad kolumn i stället för hela filen. I det här fallet är 0,2 TB läst. (Fem lika stora kolumner är 0,2 TB vardera.) Noderna bearbetar fragmenten i den här tabellen och populations summan för varje fragment överförs mellan noder. Den sista summan överförs till din slut punkt. 

Den här frågan bearbetar 0,2 TB plus en liten del av omkostnader för att överföra summor av fragment.

**Fråga 3**: Välj * från population_parquet

Den här frågan läser alla kolumner och överför alla data i ett okomprimerat format. Om komprimerings formatet är 5:1, bearbetar frågan 6 TB eftersom den läser 1 TB och överför 5 TB okomprimerade data.

**Fråga 4**: Välj Count (*) från very_small_csv

Den här frågan läser hela filer. Den totala storleken på filer som lagras i den här tabellen är 100 KB. Noderna bearbetar fragment i den här tabellen och summan för varje fragment överförs mellan noder. Den sista summan överförs till din slut punkt. 

Den här frågan bearbetar något mer än 100 KB data. Mängden data som bearbetas för den här frågan avrundas uppåt till 10 MB, enligt vad som anges i avsnittet [Avrunda](#rounding) i den här artikeln.

## <a name="cost-control"></a>Kostnads kontroll

Med funktionen för kostnads kontroll i SQL-poolen utan server kan du ange budgeten för mängden bearbetade data. Du kan ställa in budgeten i TB data som bearbetas för en dag, vecka och månad. Samtidigt kan du ha en eller flera budget uppsättningar. Om du vill konfigurera kostnads styrning för SQL-poolen utan server kan du använda Synapse Studio eller T-SQL.

## <a name="configure-cost-control-for-serverless-sql-pool-in-synapse-studio"></a>Konfigurera kostnads kontroll för Server lös SQL-pool i Synapse Studio
 
Om du vill konfigurera kostnads kontrollen för Server lös SQL-poolen i Synapse Studio navigerar du till hantera objekt i menyn till vänster, än Välj objektet SQL-pool under Analytics-pooler. När du hovrar över en server utan SQL-pool, kommer du att märka en ikon för kostnads kontroll – klicka på den här ikonen.

![Navigering för kostnads kontroll](./media/data-processed/cost-control-menu.png)

När du klickar på ikonen för kostnads kontroll visas ett sido fält:

![Konfiguration av kostnads kontroll](./media/data-processed/cost-control-sidebar.png)

Om du vill ange en eller flera budgetar klickar du först på Aktivera alternativ knapp för en budget som du vill ange, än Ange heltal svärdet i text rutan. Enhet för värdet är TBs. När du har konfigurerat de budgetar som du ville Klicka på tillämpa-knappen längst ned i sido fältet. Då är det dags att du har angett budget.

## <a name="configure-cost-control-for-serverless-sql-pool-in-t-sql"></a>Konfigurera kostnads kontroll för Server lös SQL-pool i T-SQL

Om du vill konfigurera kostnads kontroll för en server lös SQL-pool i T-SQL måste du köra en eller flera av följande lagrade procedurer.

```sql
sp_set_data_processed_limit
    @type = N'daily',
    @limit_tb = 1

sp_set_data_processed_limit
    @type= N'weekly',
    @limit_tb = 2

sp_set_data_processed_limit
    @type= N'monthly',
    @limit_tb = 3334
```

Om du vill se den aktuella konfigurationen kör du följande T-SQL-instruktion:

```sql
SELECT * FROM sys.configurations
WHERE name like 'Data processed %';
```

Om du vill se hur mycket data som bearbetades under den aktuella dagen, veckan eller månaden kör du följande T-SQL-uttryck:

```sql
SELECT * FROM sys.dm_external_data_processed
```

## <a name="next-steps"></a>Nästa steg

Information om hur du optimerar dina frågor för prestanda finns i [metod tips för Server lös SQL-pool](best-practices-sql-on-demand.md).
