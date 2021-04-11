---
title: 'Oracle till Azure Database for PostgreSQL: migrations guide'
titleSuffix: Azure Database for PostgreSQL
description: I den här guiden får du lära dig att migrera Oracle-schemat till Azure Database for PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: 931528ec415cabde8e862db17b9f8f26502f6788
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968942"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>Migrera Oracle till Azure Database for PostgreSQL

I den här guiden får du lära dig att migrera Oracle-schemat till Azure Database for PostgreSQL. 

Detaljerad vägledning för migrering finns i [resurser för migrerings guide](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf). 

## <a name="prerequisites"></a>Förutsättningar

Om du vill migrera Oracle-schemat till Azure Database for PostgreSQL måste du: 

- Kontrol lera att din käll miljö stöds. 
- Ladda ned den senaste versionen av [ora2pg](https://ora2pg.darold.net/). 
- Ha den senaste versionen av [DBD-modulen](https://www.cpan.org/modules/by-module/DBD/). 


## <a name="overview"></a>Översikt

PostgreSQL är en av världens mest avancerade databaser med öppen källkod. Den här artikeln beskriver hur du använder det kostnads fria ora2pg-verktyget för att migrera en Oracle-databas till PostgreSQL. Du kan använda ora2pg för att migrera en Oracle-databas eller MySQL-databas till ett PostgreSQL-kompatibelt schema. 

Verktyget ora2pg ansluter din Oracle-databas, skannar den automatiskt och extraherar dess struktur eller data. Sedan genererar ora2pg SQL-skript som du kan läsa in i PostgreSQL-databasen. Du kan använda ora2pg för uppgifter som bakåtkompilering av en Oracle-databas, migrera en stor företags databas eller helt enkelt replikera vissa Oracle-data till en PostgreSQL-databas. Verktyget är enkelt att använda och kräver ingen Oracle-databasens kunskap, förutom möjligheten att tillhandahålla de parametrar som krävs för att ansluta till Oracle-databasen.

> [!NOTE]
> Mer information om hur du använder den senaste versionen av ora2pg finns i [ora2pg-dokumentationen](https://ora2pg.darold.net/documentation.html).

### <a name="typical-ora2pg-migration-architecture"></a>Typisk arkitektur för ora2pg-migrering

![Skärm bild av ora2pg-migreringens arkitektur.](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

När du har etablerat den virtuella datorn och Azure Database for PostgreSQL behöver du två konfigurationer för att möjliggöra anslutning mellan dem: **Tillåt åtkomst till Azure-tjänster** och **Framtvinga SSL-anslutning**: 

- Bladet **anslutnings säkerhet** > **Tillåt åtkomst till Azure-tjänster**  >  **på**

- Blad för **anslutnings säkerhet** > **SSL-inställningar**  >  **tvinga SSL-anslutning**  >  **inaktive rad**

### <a name="recommendations"></a>Rekommendationer

- För att förbättra prestanda för utvärderingen eller export åtgärderna i Oracle-servern samlar du in statistik:

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- Exportera data med hjälp av `COPY` kommandot i stället för `INSERT` .

- Undvik att exportera tabeller med sina sekundär nycklar (FKs), begränsningar och index. Dessa element sakta ned processen med att importera data till PostgreSQL.

- Skapa materialiserade vyer med hjälp av *ingen data-sats*. Uppdatera sedan vyerna senare.

- Använd om möjligt unika index i materialiserade vyer. Dessa index kan påskynda uppdateringen när du använder syntaxen `REFRESH MATERIALIZED VIEW CONCURRENTLY` .


## <a name="premigration"></a>För migrering 

När du har kontrollerat att din käll miljö stöds och att du har åtgärdat alla krav, är du redo att starta fasen för migrering. Så här börjar du: 

1. Inventera de databaser som du behöver migrera. 
1. Utvärdera dessa databaser för potentiella problem med migrering eller blockerare.
1. Lös eventuella objekt som du har återställt. 
 
För heterogena-migreringar som Oracle till Azure Database for PostgreSQL, inbegriper det här steget även att göra käll databasens scheman kompatibla med mål miljön.

### <a name="discover"></a>Identifiera

Målet med identifierings fasen är att identifiera befintliga data källor och information om de funktioner som används. I den här fasen får du bättre förståelse och planera för migreringen. Processen omfattar att genomsöka nätverket för att identifiera alla organisationens Oracle-instanser tillsammans med den version och de funktioner som används.

Microsoft preassessment-skript för Oracle körs mot Oracle-databasen. För utvärderings skript frågar Oracle-metadata. Skripten ger:

- En databas inventering, inklusive antal objekt efter schema, typ och status.
- En grov uppskattning av rå data i varje schema baserat på statistik.
- Storleken på tabeller i varje schema.
- Antalet kod rader per paket, funktion, procedur och så vidare.

Ladda ned de relaterade skripten från [ora2pg-webbplatsen](https://ora2pg.darold.net/).

### <a name="assess"></a>Utvärdera

När du har inventerat Oracle-databaserna har du en uppfattning om databasens storlek och potentiella utmaningar. Nästa steg är att köra utvärderingen.

Det är enkelt att uppskatta kostnaden för en migrering från Oracle till PostgreSQL. För att utvärdera migrerings kostnaden kontrollerar ora2pg alla databas objekt, funktioner och lagrade procedurer för objekt och PL/SQL-kod som inte kan konverteras automatiskt.

Ora2pg-verktyget har ett innehålls analys läge som kontrollerar Oracle-databasen för att generera en text rapport. Rapporten beskriver vad Oracle-databasen innehåller och vad som inte går att exportera.

Om du vill aktivera *analys-och rapport* läget använder du den exporterade typen `SHOW_REPORT` som visas i följande kommando:

```
ora2pg -t SHOW_REPORT
```

Verktyget ora2pg kan konvertera SQL-och PL/SQL-kod från Oracle-syntax till PostgreSQL. När databasen har analyser ATS kan ora2pg uppskatta kod problem och den tid som krävs för att migrera en fullständig databas.

Ora2pg gör det möjligt att använda ett konfigurations direktiv som kallas för att beräkna migrerings kostnaden i human-dagar `ESTIMATE_COST` . Du kan också aktivera det här direktivet i en kommando tolk:

```
ora2pg -t SHOW_REPORT --estimate_cost
```

Standardenheten för migrering representerar cirka fem minuter för en PostgreSQL expert. Om migreringen är din första kan du öka standardenheten för migrering genom att använda konfigurations direktivet `COST_UNIT_VALUE` eller `--cost_unit_value` kommando rads alternativet.

Den sista raden i rapporten visar den totala uppskattade migrations koden i människa dagar. Uppskattningen följer antalet uppskattade migrations enheter för varje objekt.

Den här migrations enheten representerar cirka fem minuter för en PostgreSQL expert. Om migreringen är din första kan du öka standardvärdet genom att använda konfigurations direktivet `COST_UNIT_VALUE` eller kommando rads alternativet `--cost_unit_value` . 

I följande kod exempel visas några utvärderings variationer: 
* Tabell utvärdering
* Kolumn bedömning
* Schema utvärdering som använder en standardkostnads enhet på 5 minuter
* Schema utvärdering som använder en kostnads enhet på 10 minuter

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr
_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

Här är resultatet av schema utvärderingens migrerings nivå B-5:

* Migrations nivåer:

  * En-migrering som kan köras automatiskt
    
  * B-migrering med omskrivning av kod och en kostnad på personalavdelningen upp till fem dagar
    
  * C – migrering med omskrivning av kod och en kostnad på personalavdelningen över 5 dagar
    
* Tekniska nivåer:

   * 1 = trivial: inga lagrade funktioner och inga utlösare

   * 2 = enkelt: inga lagrade funktioner, men utlösare; ingen manuell omskrivning

   * 3 = enkel: lagrade funktioner och/eller utlösare; ingen manuell omskrivning

   * 4 = manuell: inga lagrade funktioner, men utlösare eller vyer med kod skrivning

   * 5 = svår: lagrade funktioner och/eller utlösare med kod skrivning

Utvärderingen består av: 
* En bokstav (A eller B) för att ange om migreringen behöver manuell omskrivning.

* En siffra mellan 1 och 5 för att ange de tekniska svårigheterna. 

Ett annat alternativ, `-human_days_limit` anger antalet mänskliga dagar. Här ställer du in migrations nivån på C för att ange att migreringen behöver en stor mängd arbete, fullständig projekt hantering och stöd för migrering. Standardvärdet är 10 dagar. Du kan använda konfigurations direktivet `HUMAN_DAYS_LIMIT` för att ändra det här standardvärdet permanent.

Den här schema utvärderingen utvecklades för att hjälpa användarna att bestämma vilken databas som ska migreras först och vilka team som ska mobilisera.

### <a name="convert"></a>Konvertera

I låg stillestånds tid ändras migreringens källa. Den riktar sig mot målet i termer av data och schema efter migreringen vid ett tillfälle. Under fasen *data synkronisering* ser du till att alla ändringar i källan fångas in och tillämpas på målet i nära real tid. När du har kontrollerat att alla ändringar har tillämpats på målet kan du *klippa över* från källan till mål miljön.

I det här steget av migreringen konverteras eller översätts Oracle-koden och DDL-skripten till PostgreSQL. Verktyget ora2pg exporterar Oracle-objekten i ett PostgreSQL-format automatiskt. Det går inte att kompilera några av de genererade objekten i PostgreSQL-databasen utan manuella ändringar.  

För att förstå vilka element som behöver manuella åtgärder, ska du först kompilera filerna som genereras av ora2pg mot PostgreSQL-databasen. Kontrol lera loggen och gör sedan nödvändiga ändringar tills schema strukturen är kompatibel med PostgreSQL-syntaxen.


#### <a name="create-a-migration-template"></a>Skapa en mall för migrering 

Vi rekommenderar att du använder den mall för migrering som ora2pg tillhandahåller. När du använder alternativen `--project_base` och `--init_project` skapar ora2pg en projektmall med ett arbets träd, en konfigurations fil och ett skript för att exportera alla objekt från Oracle-databasen. Mer information finns i ora2pg- [dokumentationen](https://ora2pg.darold.net/documentation.html).

Ange följande kommando: 

```
ora2pg --project_base /app/migration/ --init_project test_project

ora2pg --project_base /app/migration/ --init_project test_project
```

Här är exempel på utdata: 
   
```
Creating project test_project. /app/migration/test_project/ schema/ dblinks/ directories/ functions/ grants/ mviews/ packages/ partitions/ procedures/ sequences/ synonyms/    tables/ tablespaces/ triggers/ types/ views/ sources/ functions/ mviews/ packages/ partitions/ procedures/ triggers/ types/ views/ data/ config/ reports/

Generating generic configuration file

Creating script export_schema.sh to automate all exports.

Creating script import_all.sh to automate all imports.
```

`sources/`Katalogen innehåller Oracle-koden. `schema/`Katalogen innehåller koden som är portad för postgresql. Och `reports/` katalogen innehåller HTML-rapporter och utvärderingen av migreringen.


När projekt strukturen har skapats skapas en allmän konfigurations fil. Definiera Oracle Database-anslutningen och relevanta konfigurations parametrar i konfigurations filen. Mer information om konfigurations filen finns i ora2pg- [dokumentationen](https://ora2pg.darold.net/documentation.html).


#### <a name="export-oracle-objects"></a>Exportera Oracle-objekt

Exportera sedan Oracle-objekten som PostgreSQL-objekt genom att köra filen *export_schema. sh*.

```
cd /app/migration/mig_project
./export_schema.sh
```

Kör följande kommando manuellt.

```
SET namespace="/app/migration/mig_project"

ora2pg -t DBLINK -p -o dblink.sql -b %namespace%/schema/dblinks -c
%namespace%/config/ora2pg.conf
ora2pg -t DIRECTORY -p -o directory.sql -b %namespace%/schema/directories -c
%namespace%/config/ora2pg.conf
ora2pg -p -t FUNCTION -o functions2.sql -b %namespace%/schema/functions -c
%namespace%/config/ora2pg.conf ora2pg -t GRANT -o grants.sql -b %namespace%/schema/grants -c %namespace%/config/ora2pg.conf ora2pg -t MVIEW -o mview.sql -b %namespace%/schema/   mviews -c %namespace%/config/ora2pg.conf
ora2pg -p -t PACKAGE -o packages.sql
%namespace%/config/ora2pg.conf -b %namespace%/schema/packages -c
ora2pg -p -t PARTITION -o partitions.sql %namespace%/config/ora2pg.conf -b %namespace%/schema/partitions -c
ora2pg -p -t PROCEDURE -o procs.sql
%namespace%/config/ora2pg.conf -b %namespace%/schema/procedures -c
ora2pg -t SEQUENCE -o sequences.sql
%namespace%/config/ora2pg.conf -b %namespace%/schema/sequences -c
ora2pg -p -t SYNONYM -o synonym.sql -b %namespace%/schema/synonyms -c
%namespace%/config/ora2pg.conf
ora2pg -t TABLE -o table.sql -b %namespace%/schema/tables -c %namespace%/config/ora2pg.conf ora2pg -t TABLESPACE -o tablespaces.sql -b %namespace%/schema/tablespaces -c
%namespace%/config/ora2pg.conf
ora2pg -p -t TRIGGER -o triggers.sql -b %namespace%/schema/triggers -c
%namespace%/config/ora2pg.conf ora2pg -p -t TYPE -o types.sql -b %namespace%/schema/types -c %namespace%/config/ora2pg.conf ora2pg -p -t VIEW -o views.sql -b %namespace%/   schema/views -c %namespace%/config/ora2pg.conf
```

Om du vill extrahera data använder du följande kommando.

```
ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
```

#### <a name="compile-files"></a>Kompilera filer

Kompilera slutligen alla filer mot Azure Database for PostgreSQL-servern. Du kan välja att läsa in de manuellt genererade DDL-filerna eller använda det andra skriptet *IMPORT_ALL. sh* för att importera filerna interaktivt.

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-

server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l

%namespace%\ schema\sequences\create_sequences.log

psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l    %namespace%\schema\tables\create_table.log
```

Här är kommandot data import:

```
psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log

psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
```

När filerna kompileras, kontrol lera loggarna och korrigera alla syntaxer som ora2pg inte kunde konvertera på egen hand.

Mer information finns i [Oracle to Azure Database for PostgreSQL migration-lösningar](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf).

## <a name="migrate"></a>Migrera 

När du har de nödvändiga förutsättningarna och du har slutfört stegen för migrering kan du starta schemat och migreringen av data.

### <a name="migrate-schema-and-data"></a>Migrera schema och data

När du har gjort de nödvändiga korrigeringarna är en stabil version av databasen klar att distribuera. Kör `psql` import kommandona och peka på de filer som innehåller den ändrade koden. Den här aktiviteten sammanställer databas objekt mot PostgreSQL-databasen och importerar data.

I det här steget kan du implementera en nivå av parallellitet för att importera data.

### <a name="sync-data-and-cut-over"></a>Synkronisera data och klipp ut

I online-migrering (minimalt drift avbrott) fortsätter migreringen att ändras. Den riktar sig mot målet i termer av data och schema efter migreringen vid ett tillfälle. 

Under fasen *data synkronisering* ser du till att alla ändringar i källan fångas in och tillämpas på målet i nära real tid. När du har kontrollerat att alla ändringar har tillämpats kan du klippa ut från källan till mål miljön.

Kontakta supporten om du vill göra en online-migrering AskAzureDBforPostgreSQL@service.microsoft.com .

I en *delta-/stegvis* migrering som använder ora2pg för varje tabell använder du en fråga som filtrerar (*klipper*) efter datum, tid eller annan parameter. Slutför sedan migreringen genom att använda en andra fråga som migrerar återstående data.

I käll data tabellen, migrera alla historiska data först. Här är ett exempel:

```
select * from table1 where filter_data < 01/01/2019
```

Du kan fråga ändringarna sedan den första migreringen genom att köra ett kommando som detta:

```
select * from table1 where filter_data >= 01/01/2019
```

I det här fallet rekommenderar vi att du förbättrar valideringen genom att kontrol lera data paritet på båda sidor, källan och målet.

## <a name="postmigration"></a>Postmigration 

Efter *migreringen* slutför du postmigration-aktiviteterna för att se till att allt fungerar så smidigt och effektivt som möjligt.

### <a name="remediate-applications"></a>Åtgärda program

När data har migrerats till mål miljön måste alla program som tidigare förbrukade källan börja använda målet. Installations programmet kräver ibland ändringar i programmen.

### <a name="test"></a>Test

När data har migrerats till målet kan du köra tester mot databaserna för att kontrol lera att programmen fungerar bra med målet. Se till att källan och målet migreras korrekt genom att köra manuella data verifierings skript mot Oracle-källan och PostgreSQL-mål databaserna.

Vi rekommenderar att om käll-och mål databaserna har en nätverks Sök väg, ska ora2pg användas för data verifiering. Du kan använda `TEST` åtgärden för att se till att alla objekt från Oracle-databasen har skapats i postgresql. 

Kör följande kommando:

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>Optimera

Postmigration-fasen är avgörande för att stämma av data precisions problem och kontrol lera om det är klart. I den här fasen kan du också lösa prestanda problem med arbets belastningen.

## <a name="migration-assets"></a>Migrera till gångar 

Mer information om det här migrerings scenariot finns i följande resurser. De har stöd för att engagera projekt engagemang i Real världen.

| Resurs | Beskrivning    |
| -------------- | ------------------ |
| [Cookbook för Oracle till Azure PostgreSQL-migrering](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | Det här dokumentet hjälper arkitekter, konsulter, databas administratörer och relaterade roller att snabbt migrera arbets belastningar från Oracle till Azure Database for PostgreSQL med hjälp av ora2pg. |
| [Lösningar för PostgreSQL-migrering från Oracle till Azure](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | Det här dokumentet hjälper arkitekter, konsulter, databas administratörer och relaterade roller att snabbt åtgärda eller undvika problem när du migrerar arbets belastningar från Oracle till Azure Database for PostgreSQL. |
| [Steg för att installera ora2pg i Windows eller Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | Det här dokumentet innehåller en snabb installations guide för att migrera schema och data från Oracle till Azure Database for PostgreSQL med hjälp av ora2pg på Windows eller Linux. Mer information finns i ora2pg- [dokumentationen](http://ora2pg.darold.net/documentation.html). |

Data SQL Engineering-teamet utvecklade dessa resurser. Det här teamets kärn stadgan är att avblockera och påskynda komplexa modernisering för migrering av data plattformar till Microsoft Azure Data plattform.

## <a name="more-support"></a>Mer support

Om du vill ha hjälp utanför ora2pg-verktygets omfattning kontaktar du [ @Ask Azure dB för postgresql](mailto:AskAzureDBforPostgreSQL@service.microsoft.com).

## <a name="next-steps"></a>Nästa steg

För en matris med tjänster och verktyg för databas-och datamigrering och för specialiserade uppgifter, se [tjänster och verktyg för datamigrering](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

Dokumentation: 
- [Azure Database for PostgreSQL-dokumentation](https://docs.microsoft.com/azure/postgresql/)
- [dokumentation om ora2pg](https://ora2pg.darold.net/documentation.html)
- [PostgreSQL webbplats](https://www.postgresql.org/)
- [Stöd för autonom transaktion i PostgreSQL](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 
