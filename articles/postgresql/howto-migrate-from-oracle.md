---
title: Migrera från Oracle
titleSuffix: Azure Database for PostgreSQL
description: I den här guiden får du lära dig att migrera Oracle-schemat till Azure Database for PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: ec6cf87b3fd326c905b4843dc30ae6ce15379305
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609956"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>Migrera Oracle till Azure Database for PostgreSQL

I den här guiden får du lära dig att migrera Oracle-schemat till Azure Database for PostgreSQL. 

Detaljerad vägledning för migrering finns i [resurser för migrerings guide](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf). 

## <a name="prerequisites"></a>Förutsättningar

Om du vill migrera Oracle-schemat till Azure Database for PostgreSQL måste du: 

- Kontrol lera att din käll miljö stöds. 
- Ladda ned den senaste versionen av [ora2pg](https://ora2pg.darold.net/). 
- Den senaste versionen av [DBD-modulen](https://www.cpan.org/modules/by-module/DBD/). 


## <a name="overview"></a>Översikt

PostgreSQL är en av världens mest avancerade databaser med öppen källkod. Den här artikeln beskriver hur du använder det kostnads fria ora2pg-verktyget för att migrera en Oracle-databas till PostgreSQL. Du kan använda ora2pg, ett kostnads fritt verktyg för att migrera en Oracle-eller MySQL-databas till ett PostgreSQL-kompatibelt schema. Verktyget ansluter Oracle-databasen, skannar den automatiskt och extraherar dess struktur eller data. Därefter genererar ora2pg SQL-skript som du kan läsa in i PostgreSQL-databasen. ora2pg kan användas för att utföra åtgärder från omvänd kompilering av en Oracle-databas, utföra en stor migrering av företagets databas, eller helt enkelt replikera vissa Oracle-data till en PostgreSQL-databas. Det är enkelt att använda och kräver inte någon annan Oracle Database-kunskap än möjligheten att tillhandahålla de parametrar som krävs för att ansluta till Oracle-databasen.

> [!NOTE]
> Mer information om hur du använder den senaste versionen av ora2pg finns i [ora2pg-dokumentationen](https://ora2pg.darold.net/documentation.html).

### <a name="typical-ora2pg-migration-architecture"></a>Typisk arkitektur för ora2pg-migrering

![Skärm bild av ora2pg-migreringens arkitektur.](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

När du har slutfört etableringen av den virtuella datorn och Azure Database for PostgreSQL behövs två konfigurationer för att aktivera anslutningar mellan dem: **Tillåt Azure-tjänster** och **Använd SSL-anslutning**, som illustreras på följande sätt:

- Bladet **anslutnings säkerhet** – > **Tillåt åtkomst till Azure-tjänster** – > på

- Bladet **anslutnings säkerhet** – > **SSL-inställningar**  ->  **tvinga SSL-anslutning** -> inaktiverat

### <a name="recommendations"></a>Rekommendationer

- För att förbättra prestandan för utvärderings-eller export åtgärderna i Oracle-servern samlar du in statistik enligt följande:

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- Exportera data med kommandot COPY i stället för infoga.

- Undvik att exportera tabeller med sina FKs, begränsningar och index – om du gör det blir det långsammare att importera data till PostgreSQL.

- Skapa materialiserade vyer med **instruktionen ingen data** och uppdatera den senare.

- Om möjligt implementerar du unika index i materialiserade vyer. Detta gör att uppdateringen går snabbare med syntaxen `REFRESH MATERIALIZED VIEW CONCURRENTLY` .



## <a name="pre-migration"></a>Före migrering 

När du har verifierat att din käll miljö stöds och säkerställer att du har åtgärdat alla krav, är du redo att starta fasen före migrering. I den här delen av processen ingår att utföra en inventering av de databaser som du behöver migrera, utvärdera dessa databaser för potentiella problem med migrering eller blockerare, och sedan matcha objekt som du kanske har återställt. För heterogena-migreringar som Oracle till Azure Database for PostgreSQL, inbegriper det här steget även konvertering av schema (erna) i käll databaserna som ska vara kompatibla med mål miljön.

### <a name="discover"></a>Identifiera

Målet med identifierings fasen är att identifiera befintliga data källor och information om de funktioner som används för att få en bättre förståelse för och planera för migreringen. Den här processen omfattar genomsökning av nätverket för att identifiera alla organisationens Oracle-instanser tillsammans med den version och de funktioner som används.

Microsoft Oracle-skript för för utvärdering körs mot Oracle-databasen. Skript för för utvärdering är en uppsättning frågor som träffar Oracle-metadata och ger följande:

- Databas inventering, inklusive antal objekt efter schema, typ och status.

- En grov uppskattning av rå data i varje schema (baserat på statistik).

- Storleks ändring för tabeller i varje schema.

- Antalet rader med kod per paket, funktion, procedur osv.

Ladda ned de relaterade skripten från [ora2pg-webbplatsen](http://ora2pg.darold.net/).

### <a name="assess"></a>Utvärdera

När du har slutfört inventeringen av Oracle-databaserna för att få en uppfattning om databasens storlek och hur utmaningarna är, är nästa steg att köra utvärderingen.

Det är inte enkelt att uppskatta kostnaden för en migreringsprocessen från Oracle till PostgreSQL. För att få en bättre bedömning av den här migreringen kommer ora2pg att inspektera alla databas objekt, alla funktioner och lagrade procedurer för att identifiera om det fortfarande finns några objekt och PL/SQL-kod som inte kan konverteras automatiskt av ora2pg.

ora2pg har ett innehålls analys läge som kontrollerar Oracle-databasen för att generera en text rapport om vad Oracle-databasen innehåller och vad som inte går att exportera.

Om du vill aktivera **analys-och rapport** läget använder du den exporterade typen `SHOW_REPORT` som visas i följande kommando:

```
ora2pg -t SHOW_REPORT
```

När databasen har analyser ATS kan ora2pg, med möjligheten att konvertera SQL-och PL/SQL-kod från Oracle-syntax till PostgreSQL, gå vidare genom att uppskatta kod problem och den tid som krävs för att utföra en fullständig databas migrering.

Ora2pg gör att du kan använda ett konfigurations direktiv som heter ESTIMATE_COST, som också kan aktive ras på kommando raden, för att beräkna migrerings kostnaden på man-dagar.

```
ora2pg -t SHOW_REPORT --estimate_cost
```

Standardenheten för migrering representerar cirka fem minuter för en PostgreSQL expert. Om det här är din första migrering kan du få den högre upp med konfigurations direktivet COST_UNIT_VALUE eller kommando rads alternativet--cost_unit_value.

Den sista raden i rapporten visar den totala uppskattade migreringsprocessen på man dagar efter antalet uppskattade migrations enheter för varje objekt.

Den här migrations enheten representerar cirka fem minuter för en PostgreSQL expert. Om det här är din första migrering kan du öka standardvärdet med konfigurations direktivet COST_UNIT_VALUE eller kommando rads alternativet--cost_unit_value. Nedan följer några variationer i utvärderingen a)-tabeller. b) kolumner bedömning c) schema utvärdering med standard cost_unit (5 min) d) schema utvärdering med 10 min som kostnads enhet.

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr
_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

Resultatet av schema utvärderingen illustreras enligt nedan:

**Migrations nivå: B-5**

Migrations nivåer:

En-migrering som kan köras automatiskt

B-migrering med omskrivning av kod och en kostnad på personalavdelningen upp till fem dagar

C – migrering med kod omarbetning och kostnad på en dag över 5 dagar

Tekniska nivåer:

1 = trivial: inga lagrade funktioner och inga utlösare

2 = enkelt: inga lagrade funktioner, men med utlösare, ingen manuell skrivning

3 = enkel: lagrade funktioner och/eller utlösare, ingen manuell skrivning

4 = manuell: inga lagrade funktioner, men med utlösare eller vyer med kod skrivning

5 = svår: lagrade funktioner och/eller utlösare med kod skrivning

Utvärderingen består av en bokstav (A eller B) för att ange om migreringen behöver manuell omskrivning eller inte, och en siffra mellan 1 och 5 för att ange den tekniska svårighets nivån. Du har ytterligare ett alternativ-human_days_limit för att ange antalet mänskliga dagar där migrerings nivån ska ställas in på C för att ange att den behöver en enorm mängd arbete och en fullständig projekt hantering med stöd för migrering. Standardvärdet är 10 dagar. Du kan använda konfigurations direktivet HUMAN_DAYS_LIMIT för att ändra det här standardvärdet permanent.

Den här funktionen har utvecklats för att hjälpa till att avgöra vilken databas som kunde migreras först och vad är det team som behöver anskaffas.

### <a name="convert"></a>Konvertera

Vid migreringen med låg stillestånds tid fortsätter källan som du migrerar att ändra, genom att gå från målet i termer av data och schema, efter migreringen vid ett tillfälle. Under fasen **data synkronisering** måste du se till att alla ändringar i källan fångas in och tillämpas på målet i nära real tid. När du har kontrollerat att alla ändringar i källan har tillämpats på målet kan du Start punkt från källan till mål miljön.

I det här steget av migreringen sker konverteringen eller översättningen av Oracle-koden + DDLS till PostgreSQL. Verktyget ora2pg exporterar Oracle-objekten i ett PostgreSQL-format automatiskt. För de objekt som skapas kompileras inte vissa i PostgreSQL-databasen utan manuella ändringar.  
Processen att förstå vilka element som behöver manuella åtgärder består i att kompilera filerna som genereras av ora2pg mot PostgreSQL-databasen, kontrol lera loggen och göra nödvändiga ändringar tills alla schema strukturen är kompatibla med PostgreSQL-syntaxen.


#### <a name="create-migration-template"></a>Skapa mall för migrering 

Först rekommenderar vi att du skapar en mall för migrering som finns i rutan med ora2pg. De två alternativen--project_base och--init_project när de används anger att ora2pg ska skapa en projektmall med ett arbets träd, en konfigurations fil och ett skript för att exportera alla objekt från Oracle-databasen. Mer information finns i ora2pg- [dokumentationen](https://ora2pg.darold.net/documentation.html).

   Ange följande kommando: 

   ```
   ora2pg --project_base /app/migration/ --init_project test_project
   
   ora2pg --project_base /app/migration/ --init_project test_project
   ```

Exempel på utdata: 
   
   ```
   Creating project test_project. /app/migration/test_project/ schema/ dblinks/ directories/ functions/ grants/ mviews/ packages/ partitions/ procedures/ sequences/ synonyms/    tables/ tablespaces/ triggers/ types/ views/ sources/ functions/ mviews/ packages/ partitions/ procedures/ triggers/ types/ views/ data/ config/ reports/
   
   Generating generic configuration file
   
   Creating script export_schema.sh to automate all exports.
   
   Creating script import_all.sh to automate all imports.
   ```

Källorna/katalogen innehåller Oracle-koden, schemat/katalogen innehåller koden som är portad till PostgreSQL. Rapporterna/katalogen innehåller HTML-rapporter med utvärderingen av migrerings kostnad.


När projekt strukturen har skapats skapas en allmän konfigurations fil. Definiera Oracle Database-anslutningen och relevanta konfigurations parametrar i konfigurationen.  Se ora2pg-dokumentationen för att förstå vad som kan konfigureras i konfigurations filen och hur.


#### <a name="export-oracle-objects"></a>Exportera Oracle-objekt

Exportera sedan Oracle-objekten som PostgreSQL-objekt genom att köra filen export_schema. sh.

   ```
   cd /app/migration/mig_project
   ./export_schema.sh
   
   Run the following command manually:
   
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

   Om du vill extrahera data använder du följande kommando:

   ```
   ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
   ```

#### <a name="compile-files"></a>Kompilera filer

Till sist kompilerar du alla filer mot Azure Database for PostgreSQL-servern. Du kan nu välja att läsa in de DDL-filer som genererats manuellt eller använda det andra skriptet import_all. sh för att importera filerna interaktivt.

   ```
   psql -f %namespace%\schema\sequences\sequence.sql -h server1-
   
   server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l
   
   %namespace%\ schema\sequences\create_sequences.log
   
   psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l    %namespace%\schema\tables\create_table.log
   ```

   Kommandot data import:

   ```
   psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log
   
   psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
   ```

Under kompileringen av filer, kontrol lera loggarna och åtgärda de nödvändiga syntaxerna som ora2pg inte kunde konvertera ut från rutan.

Läs om hur du kan lösa problemet genom att använda white paper [Oracle för att Azure Database for PostgreSQL migrering](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) .

## <a name="migrate"></a>Migrera 

När du har de nödvändiga förutsättningarna och har slutfört de uppgifter som är kopplade till fasen **innan migreringen** , är du redo att utföra schemat och datamigreringen.

### <a name="migrate-schema-and-data"></a>Migrera schema och data

När korrigeringarna är på plats är en stabil version av databasen klar för distribution.

Allt det krävs för att köra *psql* -import kommandona, peka på de filer som innehåller den ändrade koden för att kompilera databas objekt mot PostgreSQL-databasen och importera data.

I det här steget kan du implementera en viss nivå av parallellitet vid import av data.

### <a name="data-sync-and-cutover"></a>Data synkronisering och start punkt

Med migrering online (minimalt drift avbrott) fortsätter källan som du migrerar att ändra, genom att gå från målet vad gäller data och schema, efter migreringen vid ett tillfälle. Under fasen **data synkronisering** måste du se till att alla ändringar i källan fångas in och tillämpas på målet i nära real tid. När du har kontrollerat att alla ändringar i källan har tillämpats på målet kan du Start punkt från källan till mål miljön.

Från och med mars 2019, om du vill utföra en online-migrering, bör du överväga att använda Attunity-replikering för Microsoft-migreringar eller Striims.

För *delta/stegvis* migrering med ora2pg, består tekniken i att tillämpa för varje tabell a-fråga som tillämpar ett filter (klipp) efter datum eller tid osv. efter det att migreringen har slutförts tillämpas en andra fråga som kommer att migrera resten av data (överblivna).

I käll data tabellen, migrera alla historiska data först. Ett exempel på detta är:

```
select * from table1 where filter_data < 01/01/2019
```

Du kan fråga de ändringar som har gjorts sedan den första migreringen genom att köra ett kommando som liknar följande:

```
select * from table1 where filter_data >= 01/01/2019
```

I det här fallet rekommenderar vi att verifieringen har förbättrats genom att kontrol lera data paritet på både sidor, källa och mål.

## <a name="post-migration"></a>Efter migreringen 

När du har slutfört **migreringen** måste du gå igenom en serie uppgifter efter migreringen för att se till att allt fungerar så smidigt och effektivt som möjligt.

### <a name="remediate-applications"></a>Åtgärda program

När data har migrerats till mål miljön måste alla program som tidigare förbrukade källan börja använda målet. Om du gör detta måste du i vissa fall göra ändringar i programmen.

### <a name="perform-tests"></a>Utför tester

När data har migrerats till målet utför du tester mot databaserna för att kontrol lera att programmen fungerar bra mot målet efter migreringen.

För att garantera att källan och målet är korrekt migrerade kör du de manuella data verifierings skripten mot Oracle-källan och PostgreSQL-mål databaserna.

Vi rekommenderar att om käll-och mål databaserna har en nätverks Sök väg, ska ora2pg användas för data verifiering. Med hjälp av TEST åtgärden kan du kontrol lera att alla objekt från Oracle-databasen har skapats under PostgreSQL. Kör kommandot som det visas:

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>Optimera

Fasen efter migreringen är avgörande för att kunna stämma av data precisions problem och kontrol lera att de är klara, samt att lösa prestanda problem med arbets belastningen.

## <a name="migration-assets"></a>Migrera till gångar 

Mer hjälp om hur du slutför det här migreringsprocessen finns i följande resurser, som har utvecklats för att ge stöd för ett verkligt migrerings projekt.

| **Rubrik länk** | **Beskrivning**    |
| -------------- | ------------------ |
| [Cookbook för Oracle till Azure PostgreSQL-migrering](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | Detta dokument syfte är att tillhandahålla arkitekter, konsulter, databas administratörer och relaterade roller med en guide för att snabbt migrera arbets belastningar från Oracle till Azure Database for PostgreSQL med ora2pg-verktyget. |
| [Lösningar för PostgreSQL-migrering från Oracle till Azure](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | Syftet med det här dokumentet är att tillhandahålla arkitekter, konsulter, databas administratörer och relaterade roller med en vägledning för snabb korrigering/hantering av problem när du migrerar arbets belastningar från Oracle till Azure Database for PostgreSQL. |
| [Steg för att installera ora2pg i Windows eller Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | Det här dokumentet är avsett att användas som en snabb installations guide för att aktivera migrering av schema & data från Oracle till Azure Database for PostgreSQL med hjälp av ora2pg-verktyget i Windows eller Linux. Fullständig information om verktyget finns på http://ora2pg.darold.net/documentation.html . |

Dessa resurser har utvecklats som en del av data SQL-Ninja program, som sponsras av Azure Data Group Engineering-teamet. Huvud stadgan för data SQL Ninja-programmet är att avblockera och påskynda komplexa modernisering och konkurrera med data plattformens migrering till Microsofts Azure-dataplattform. Om du tror att organisationen är intresse rad av att delta i data SQL Ninja-programmet, kontaktar du ditt konto team och ber dem att skicka in en nominerad.


### <a name="contact-support"></a>Kontakta supporten

Om du behöver hjälp med migreringar utöver ora2pg-verktyget kan du kontakta [ @Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) -aliaset för information om andra flyttnings alternativ.

## <a name="next-steps"></a>Nästa steg

- En matris med tjänster/verktyg från Microsoft och tjänster/verktyg från tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering (och särskilda uppgifter) finns i artikel [tjänsten och verktyg för migrering av data](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

Mer information finns i: 
- [Azure Database for PostgreSQL-dokumentation](https://docs.microsoft.com/azure/postgresql/)
- [dokumentation om ora2pg](https://ora2pg.darold.net/documentation.html)
- [PostgreSQL webbplats](https://www.postgresql.org/)
- [Stöd för autonom transaktion i PostgreSQL](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 

För video innehåll: 
- [Översikt över migrerings resan och de verktyg/tjänster som rekommenderas för utvärdering och migrering](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).