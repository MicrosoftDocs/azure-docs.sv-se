---
title: Utvärderings regler för migrering av SQL Server till SQL-hanterad instans
description: Utvärderings regler för att identifiera problem med käll SQL Servers instansen som måste åtgärdas innan migrering till en Azure SQL-hanterad instans.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: 760a6496ff297ae6328810589f780b430d55b18a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054964"
---
# <a name="assessment-rules-for-sql-server-to-sql-managed-instance-migration"></a>Utvärderings regler för migrering av SQL Server till SQL-hanterad instans
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Migreringsverktyg verifierar din käll SQL Server instans genom att köra ett antal utvärderings regler för att identifiera problem som måste åtgärdas innan du migrerar SQL Server-databasen till en Azure SQL-hanterad instans. 

Den här artikeln innehåller en lista över regler som används för att utvärdera möjligheten att migrera SQL Server databasen till en Azure SQL-hanterad instans. 

## <a name="analysiscommand-job"></a>AnalysisCommand-jobb<a id="AnalysisCommandJob"></a>

**Title: AnalysisCommand-jobb-steget stöds inte i Azure SQL-hanterad instans.**   
**Kategori**: varning   


**Beteckning**   
Det är ett jobb steg som kör ett Analysis Services-kommando. AnalysisCommand-jobb-steget stöds inte i Azure SQL-hanterad instans.

**Rekommenderade**     
Avsnittet granska berörda objekt i Azure Migrate om du vill visa alla jobb med hjälp av kommando jobb steget Analysis service och utvärdera om jobb steget eller det påverkade objektet kan tas bort. Du kan också migrera till SQL Server på en virtuell Azure-dator.

Mer information: [SQL Server Agent skillnader i Azure SQL Managed instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="analysisquery-job"></a>AnalysisQuery-jobb<a id="AnalysisQueryJob"></a>

**Title: AnalysisQuery-jobb-steget stöds inte i Azure SQL-hanterad instans.**   
**Kategori**: varning   

**Beteckning**   
Det är ett jobb steg som kör en Analysis Services fråga. AnalysisQuery-jobb-steget stöds inte i Azure SQL-hanterad instans.


**Rekommenderade**   
Avsnittet granska påverkade objekt i Azure Migrate för att se alla jobb som använder Analysis Service Query Job-steg och utvärdera om jobb steget eller det påverkade objektet kan tas bort. Du kan också migrera till SQL Server på en virtuell Azure-dator.

Mer information: [SQL Server Agent skillnader i Azure SQL Managed instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="assembly-from-file"></a>Sammansättning från fil<a id="AssemblyFromFile"></a>

**Title: CREATE ASSEMBLY och ALTER ASSEMBLY med en File-parameter stöds inte i Azure SQL-hanterad instans.**   
**Kategori**: varning   

**Beteckning**   
Azure SQL-hanterad instans kan inte komma åt fil resurser eller Windows-mappar. Se avsnittet "påverkade objekt" för den speciella användningen av BULK INSERT-uttryck som inte refererar till en Azure-blob. Objekt med BULK INSERT där källan inte är Azure Blob Storage fungerar inte efter migrering till Azure SQL-hanterad instans.


**Rekommenderade**   
Du måste konvertera BULK INSERT-instruktioner som använder lokala filer eller fil resurser för att använda filer från Azure Blob Storage i stället, när du migrerar till en Azure SQL-hanterad instans. Du kan också migrera till SQL Server på en virtuell Azure-dator. 

Mer information: [CLR-skillnader i Azure SQL-hanterad instans ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#clr)

## <a name="bulk-insert"></a>Massinläsning<a id="BulkInsert"></a>

**Title: BULK INSERT med en BLOB-datakälla som inte är Azure-datakälla stöds inte i Azure SQL-hanterad instans.**   
**Kategori**: problem   

**Beteckning**   
Azure SQL-hanterad instans kan inte komma åt fil resurser eller Windows-mappar. Se avsnittet "påverkade objekt" för den speciella användningen av BULK INSERT-uttryck som inte refererar till en Azure-blob. Objekt med BULK INSERT där källan inte är Azure Blob Storage fungerar inte efter migrering till Azure SQL-hanterad instans.


**Rekommenderade**   
Du måste konvertera BULK INSERT-instruktioner som använder lokala filer eller fil resurser för att använda filer från Azure Blob Storage i stället, när du migrerar till en Azure SQL-hanterad instans.

Mer information: [Mass infogning och OpenRowSet-skillnader i Azure SQL-hanterad instans ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="clr-security"></a>CLR-säkerhet<a id="ClrStrictSecurity"></a>

**Rubrik: CLR-sammansättningar markerade som säkra eller EXTERNAL_ACCESS betraktas som osäkra**   
**Kategori**: problem   

**Beteckning**   
Strikt säkerhets läge för CLR tillämpas i Azure SQL-hanterad instans. Det här läget är aktiverat som standard och introducerar rad brytnings ändringar för databaser som innehåller användardefinierade CLR-sammansättningar markerade som antingen säkra eller EXTERNAL_ACCESS.


**Rekommenderade**   
CLR använder kod åtkomst säkerhet (CAS) i .NET Framework, vilket inte längre stöds som säkerhets gränser. Från och med SQL Server 2017-databasmotorn (14. x) `sp_configure` är ett alternativ som kallas CLR strikt säkerhet introducerat för att förbättra säkerheten för CLR-sammansättningar. Strikt CLR-säkerhet är aktiverat som standard och behandlar säkra och EXTERNAL_ACCESS CLR-sammansättningar som om de marker ATS som osäkra. När CLR Strict Security är inaktiverat kan en CLR-sammansättning som skapats med PERMISSION_SET = säker åtkomst kunna komma åt externa system resurser, anropa ohanterad kod och hämta sysadmin-privilegier. När du har aktiverat strikt säkerhet går det inte att läsa in alla sammansättningar som inte är signerade. Om en databas har säkra eller EXTERNAL_ACCESS sammansättningar kan återställnings-eller KOPPLINGs instruktioner slutföras, men det kan hända att sammansättningarna inte kan läsas in. Om du vill läsa in sammansättningarna måste du antingen ändra eller ta bort och återskapa varje sammansättning så att den signeras med ett certifikat eller en asymmetrisk nyckel som har en motsvarande inloggning med behörigheten osäker sammansättning på servern.

Mer information: [CLR Strict Security](/sql/database-engine/configure-windows/clr-strict-security)

## <a name="compute-clause"></a>Compute-sats<a id="ComputeClause"></a>

**Title: Compute-satsen upphör att gälla och har tagits bort.**   
**Kategori**: varning   

**Beteckning**   
Compute-satsen genererar summor som visas som ytterligare sammanfattnings kolumner i slutet av resultat uppsättningen. Den här satsen stöds dock inte längre i Azure SQL-hanterad instans.



**Rekommenderade**   
T-SQL-modulen måste skrivas om med SAMMANSLAGNINGs operatorn i stället. Koden nedan visar hur beräkning kan ersättas med sammanslagning: 

```sql
USE AdventureWorks GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) 
BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP;
```

Mer information: utgångna [databas motor funktioner i SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="cryptographic-provider"></a>Kryptografiprovider<a id="CryptographicProvider"></a>

**Title: användningen av CREATE CRYPTOGRAPHIC PROVIDER eller ALTER CRYPTOGRAPHIC PROVIDER hittades, vilket inte stöds i Azure SQL-hanterad instans.**   
**Kategori**: problem   

**Beteckning**   
Den hanterade Azure SQL-instansen stöder inte CSP-instruktioner eftersom den inte kan komma åt filer. Se avsnittet påverkade objekt för de olika användnings områdena för KRYPTOGRAFIPROVIDERs. Objekt med "skapa KRYPTOGRAFIPROVIDER" eller "ALTER CRYPTOGRAPHIC PROVIDER" fungerar inte korrekt efter migrering till Azure SQL-hanterad instans.


**Rekommenderade**   
Granska objekt med "skapa KRYPTOGRAFIPROVIDER" eller "ALTER CRYPTOGRAPHIC PROVIDER". Ta bort användningen av dessa funktioner i alla sådana objekt som krävs. Du kan också migrera till SQL Server på en virtuell Azure-dator. 

Mer information: [skillnader mellan kryptografi leverantörer i Azure SQL-hanterad instans ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#cryptographic-providers)

## <a name="database-compatibility"></a>Databasens kompatibilitet<a id="DbCompatLevelLowerThan100"></a>

**Title: kompatibilitetsnivån för databas under 100 stöds inte**   
**Kategori**: varning   

**Beteckning**   
Kompatibilitetsnivån för databaser är ett värdefullt verktyg som hjälper dig i databas modernisering, genom att tillåta att SQL Server databas motorn uppgraderas, samtidigt som du behåller funktions status för att ansluta program genom att underhålla samma kompatibilitetsnivå som för hands versionen av databasen. Den hanterade Azure SQL-instansen stöder inte kompatibilitetsnivå under 100. När databasen med kompatibilitetsnivån under 100 återställs på den hanterade Azure SQL-instansen uppgraderas kompatibilitetsnivån till 100. 


**Rekommendation**... Utvärdera om programfunktionerna är intakta när kompatibilitetsnivån för databas uppgraderas till 100 på Azure SQL-hanterad instans. Du kan också migrera till SQL Server på en virtuell Azure-dator.

Mer information: [kompatibilitetsnivå som stöds i Azure SQL-hanterad instans ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#compatibility-levels)

## <a name="database-principal-alias"></a>Databasens huvud namn<a id="DatabasePrincipalAlias"></a>

**Title: SYS. DATABASE_PRINCIPAL_ALIASES upphör att gälla och har tagits bort.**   
**Kategori**: problem   

**Beteckning**   
SYS. DATABASE_PRINCIPAL_ALIASES upphör att gälla och har tagits bort i Azure SQL-hanterad instans.


**Rekommenderade**   
Använd roller i stället för alias.

Mer information: utgångna [databas motor funktioner i SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="disable_def_cnst_chk-option"></a>DISABLE_DEF_CNST_CHK alternativ<a id="DisableDefCNSTCHK"></a>

**Title: SET-alternativet DISABLE_DEF_CNST_CHK upphör att gälla och har tagits bort.**   
**Kategori**: problem   

**Beteckning**   
SET option DISABLE_DEF_CNST_CHK upphör att gälla och har tagits bort i Azure SQL-hanterad instans.


Mer information: utgångna [databas motor funktioner i SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>FASTFIRSTROW-tips<a id="FastFirstRowHint"></a>

**Title: FASTFIRSTROW-frågetipset upphör att gälla och har tagits bort.**   
**Kategori**: varning   

**Beteckning**   
FASTFIRSTROW-frågetipset har avbrutits och har tagits bort i Azure SQL-hanterad instans.


**Rekommenderade**   
I stället för FASTFIRSTROW-frågetipset använder du alternativ (FAST n).

Mer information: utgångna [databas motor funktioner i SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>-<a id="FileStream"></a>

**Title: FILESTREAM och FileTable stöds inte i Azure SQL-hanterad instans.**   
**Kategori**: problem   

**Beteckning**   
FILESTREAM-funktionen, som gör att du kan lagra ostrukturerade data, till exempel text dokument, bilder och videor i NTFS-filsystem, stöds inte i Azure SQL-hanterad instans. **Det går inte att migrera den här databasen eftersom säkerhets kopian som innehåller FILESTREAM-filgrupper inte kan återställas på Azure SQL Managed instance.**


**Rekommenderade**   
Överför de ostrukturerade filerna till Azure Blob Storage och lagra metadata som är relaterade till dessa filer (namn, typ, URL-plats, lagrings nyckel osv.) i Azure SQL-hanterad instans. Du kan behöva återanvända ditt program för att aktivera strömmande blobbar till och från Azure SQL-hanterad instans. Du kan också migrera till SQL Server på en virtuell Azure-dator.

Mer information: [strömma blobbar till och från SQL Azure blogg](https://azure.microsoft.com/en-in/blog/streaming-blobs-to-and-from-sql-azure/)

## <a name="heterogeneous-ms-dtc"></a>Heterogen MS DTC<a id="MIHeterogeneousMSDTCTransactSQL"></a>

**Title: starta DISTRIBUERAd transaktion med icke-SQL Server fjärrservern stöds inte i Azure SQL-hanterad instans.**   
**Kategori**: problem   

**Beteckning**   
Distribuerad transaktion som startas av Transact SQL BEGIN DISTRIBUTEd TRANSACTION och hanteras av Microsoft Distributed Transaction Coordinator (MS DTC) stöds inte i Azure SQL-hanterad instans om fjärrservern inte är SQL Server. 


**Rekommenderade**   
Avsnittet granska påverkade objekt i Azure Migrate för att se alla objekt som använder BEGIN utspridda-transaktion. Överväg att migrera deltagar databaserna till den Azure SQL-hanterade instans där distribuerade transaktioner över flera instanser stöds (för närvarande i för hands version). Du kan också migrera till SQL Server på en virtuell Azure-dator.

Mer information: [transaktioner över flera servrar för Azure SQL-hanterad instans ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)

## <a name="homogenous-ms-dtc"></a>Homogen MS DTC<a id="MIHomogeneousMSDTCTransactSQL"></a>

**Title: starta DISTRIBUERAd transaktion stöds över flera servrar för Azure SQL-hanterad instans.**   
**Kategori**: problem   

**Beteckning**   
Distribuerad transaktion som startas av Transact SQL BEGIN DISTRIBUTEd TRANSACTION och hanteras av Microsoft Distributed Transaction Coordinator (MS DTC) stöds över flera servrar för Azure SQL-hanterad instans.


**Rekommenderade**   
Avsnittet granska påverkade objekt i Azure Migrate för att se alla objekt som använder BEGIN utspridda-transaktion. Överväg att migrera deltagar databaserna till den Azure SQL-hanterade instans där distribuerade transaktioner över flera instanser stöds (för närvarande i för hands version). Du kan också migrera till SQL Server på en virtuell Azure-dator. 

Mer information: [transaktioner över flera servrar för Azure SQL-hanterad instans](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="linked-server-non-sql-provider"></a>Länkad server (icke-SQL-Provider)<a id="LinkedServerWithNonSQLProvider"></a>

**Title: den länkade servern med icke-SQL Server-providern stöds inte i Azure SQL-hanterad instans.**   
**Kategori**: problem   

**Beteckning**   
Länkade servrar gör det möjligt för SQL Server Database Engine att köra kommandon mot OLE DB data källor utanför instansen av SQL Server. Länkad server med icke-SQL Server-Provider stöds inte i Azure SQL-hanterad instans. 


**Rekommenderade**   
Den hanterade Azure SQL-instansen stöder inte länkade Server funktioner om fjärrservern inte är SQL Server som Oracle, Sybase osv. 

Följande åtgärder rekommenderas för att eliminera behovet av länkade servrar: 
- Identifiera beroende databaser från fjärranslutna icke-SQL-servrar och Överväg att flytta dem till den databas som migreras. 
- Migrera beroende databaser till mål som SQL-hanterad instans, SQL Database, Azure Synapse SQL och SQL Server instanser. 
- Överväg att skapa en länkad server mellan Azure SQL-hanterad instans och SQL Server på den virtuella Azure-datorn (SQL VM).  Sedan kan du skapa en länkad server till Oracle, Sybase osv i SQL VM. Den här metoden omfattar två hopp men kan användas som tillfällig lösning.  
- Du kan också migrera till SQL Server på en virtuell Azure-dator.

Mer information: [länkade Server skillnader i Azure SQL-hanterad instans](../../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)

## <a name="merge-job"></a>Sammanfoga jobb<a id="MergeJob"></a>

**Title: steget slå samman jobb stöds inte i Azure SQL-hanterad instans.**   
**Kategori**: varning   

**Beteckning**   
Det är ett jobb steg som aktiverar-agenten för Sammanslagningsreplikering. Agenten för Sammanslagningsreplikering är en körbar verktygs fil som använder den första ögonblicks bilden som lagras i databas tabellerna till prenumeranterna. Den sammanfogar också stegvisa data ändringar som inträffade i utgivaren efter det att den första ögonblicks bilden skapades, och stämmer antingen enligt de regler som du konfigurerar eller använder en anpassad lösare som du skapar. Steget för att slå samman jobb stöds inte i Azure SQL-hanterad instans.


**Rekommenderade**   
Avsnittet granska påverkade objekt i Azure Migrate för att se alla jobb med steget slå samman jobb och utvärdera om jobb steget eller det påverkade objektet kan tas bort. Du kan också migrera till SQL Server på en virtuell Azure-dator

Mer information: [SQL Server Agent skillnader i Azure SQL Managed instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="mi-database-size"></a>MI Database-storlek<a id="MIDatabaseSize<"></a>

**Title: den hanterade Azure SQL-instansen har inte stöd för större databas storlekar än 8 TB.**   
**Kategori**: problem   

**Beteckning**   
Databasens storlek är större än den maximala reserverade instans lagringen. **Det går inte att välja den här databasen för migrering eftersom storleken överskrider den tillåtna gränsen.**


**Rekommenderade**   
Utvärdera om data kan arkiveras komprimerade eller shardade i flera databaser. Du kan också migrera till SQL Server på en virtuell Azure-dator.

Mer information: [Egenskaper för maskin varu skapande för Azure SQL-hanterad instans ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)



## <a name="mi-instance-size"></a>MI-instans storlek<a id="MIInstanceSize<"></a>

**Title: maximal instans lagrings storlek i Azure SQL Managed instance får inte vara större än 8 TB.**   
**Kategori**: varning   

**Beteckning**   
Storleken på alla databaser är större än den maximala reserverade instans lagringen.  


**Rekommenderade**   
Överväg att migrera databaserna till olika Azure SQL-hanterade instanser eller till SQL Server på den virtuella Azure-datorn om alla databaser måste finnas på samma instans. 

Mer information: [Egenskaper för maskin varu skapande för Azure SQL-hanterad instans ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)


## <a name="multiple-log-files"></a>Flera loggfiler<a id="MultipleLogFiles<"></a>

**Title: den hanterade Azure SQL-instansen stöder inte flera loggfiler.**   
**Kategori**: problem   

**Beteckning**   
SQL Server tillåter att en databas loggar till flera filer. Den här databasen har flera loggfiler som inte stöds i Azure SQL-hanterad instans. * * Det går inte att migrera den här databasen eftersom säkerhets kopian inte kan återställas på en hanterad Azure SQL-instans. 
**

**Rekommenderade**   
Azure SQL Managed instance stöder bara en enskild logg per databas. Du måste ta bort alla filer utom en av loggfilerna innan du migrerar den här databasen till Azure: 

```sql
ALTER DATABASE [database_name] REMOVE FILE [log_file_name]
```

Mer information: [databas alternativ som inte stöds i Azure SQL-hanterad instans  ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#database-options)



## <a name="next-column"></a>Nästa kolumn<a id="NextColumn"></a>

**Title: tabeller och kolumner som heter nästa leder till ett fel i Azure SQL-hanterad instans.**   
**Kategori**: problem   

**Beteckning**   
Tabeller eller kolumner som heter NEXT har identifierats. Sekvenser, som introducerades i Microsoft SQL Server, använder ANSI-standardfunktionen NEXT VALUE FOR. Om en tabell eller kolumn heter nästa och kolumnen har ett alias som värde, och om ANSI-standarden som utelämnas, kan resulterande instruktion orsaka ett fel.


**Rekommenderade**   
Skriv om-instruktioner för att inkludera ANSI-standarden som nyckelord när en tabell eller kolumn aliasas. När en kolumn till exempel heter nästa och kolumnen har ett alias som värde, kommer frågan Välj nästa värde från tabellen att orsaka ett fel och bör skrivas om som Välj nästa som värde från tabellen. När en tabell heter nästa och tabellen har ett alias som värde, kommer frågan Välj Col1 från nästa värde att orsaka ett fel och bör skrivas om som SELECT Col1 FROM VALUE.



## <a name="non-ansi-style-left-outer-join"></a>Icke-ANSI-stil vänster yttre koppling<a id="NonANSILeftOuterJoinSyntax"></a>

**Title: icke-ANSI-stil vänster yttre koppling upphör att gälla och har tagits bort.**   
**Kategori**: varning   

**Beteckning**   
Vänster yttre koppling från icke-ANSI-stil upphör att gälla och har tagits bort i Azure SQL-hanterad instans. 


**Rekommenderade**   
Använd ANSI Join-syntax.

Mer information: utgångna [databas motor funktioner i SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="non-ansi-style-right-outer-join"></a>Icke-ANSI-stil höger yttre koppling<a id="NonANSIRightOuterJoinSyntax"></a>

**Title: icke-ANSI-stil höger yttre koppling är invecklad och har tagits bort.**   
**Kategori**: varning   

**Beteckning**   
Icke-ANSI-stil höger yttre koppling är invecklad och har tagits bort i Azure SQL-hanterad instans. 



Mer information: utgångna [databas motor funktioner i SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

**Rekommenderade**   
Använd ANSI Join-syntax.

## <a name="databases-exceed-100"></a>Databaserna överskrider 100 <a id="NumDbExceeds100"></a>

**Title: den hanterade Azure SQL-instansen stöder högst 100 databaser per instans.**   
**Kategori**: varning   

**Beteckning**   
Det maximala antalet databaser som stöds i den hanterade Azure SQL-instansen är 100, om inte storleks gränsen för instans lagring har uppnåtts.



**Rekommenderade**   
Överväg att migrera databaserna till olika Azure SQL-hanterade instanser eller till SQL Server på den virtuella Azure-datorn om alla databaser måste finnas på samma instans. 

Mer information: [resurs gränser för Azure SQL-hanterad instans ](../../managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="openrowset-non-blob-data-source"></a>OpenRowSet (data källa utan BLOB)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Title: OpenRowSet som används i Mass åtgärder med icke-Azure Blob Storage data källa stöds inte i Azure SQL-hanterad instans.**   
**Kategori**: problem   

**Beteckning**   
OpenRowSet stöder Mass åtgärder via en inbyggd Mass leverantör som gör det möjligt för data från en fil att läsas och returneras som en rad uppsättning. OpenRowSet med icke-Azure Blob Storage-datakälla stöds inte i Azure SQL-hanterad instans. 



**Rekommenderade**   
OpenRowSet-funktionen kan bara användas för att köra frågor på SQL Server instanser (antingen hanterade, lokalt eller i Virtual Machines). Endast SQLNCLI-, SQLNCLI11-och SQLOLEDB-värden stöds som Provider. Rekommendations åtgärden är därför att identifiera beroende databaser från fjärranslutna icke-SQL-servrar och överväga att flytta dem till den databas som migreras. Du kan också migrera till SQL Server på en virtuell Azure-dator

Mer information: [Mass infogning och OpenRowSet-skillnader i Azure SQL-hanterad instans ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)

## <a name="openrowset-non-sql-provider"></a>OpenRowSet (icke-SQL-Provider)<a id="OpenRowsetWithNonSQLProvider"></a>

**Title: OpenRowSet med icke-SQL-providern stöds inte i Azure SQL-hanterad instans.**   
**Kategori**: problem   

**Beteckning**   
Den här metoden är ett alternativ till att komma åt tabeller i en länkad server och en engångs ad hoc-metod för att ansluta och komma åt fjärrdata med hjälp av OLE DB. OpenRowSet med icke-SQL-providern stöds inte i Azure SQL-hanterad instans. 



**Rekommenderade**   
OpenRowSet-funktionen kan bara användas för att köra frågor på SQL Server instanser (antingen hanterade, lokalt eller i Virtual Machines). Endast SQLNCLI-, SQLNCLI11-och SQLOLEDB-värden stöds som Provider. Rekommendations åtgärden är därför att identifiera beroende databaser från fjärranslutna icke-SQL-servrar och överväga att flytta dem till den databas som migreras.

Mer information: [Mass infogning och OpenRowSet-skillnader i Azure SQL-hanterad instans ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="powershell-job"></a>PowerShell-jobb<a id="PowerShellJob"></a>

**Title: PowerShell-jobbets steg stöds inte i Azure SQL-hanterad instans.**   
**Kategori**: varning   

**Beteckning**   
Det är ett jobb steg som kör ett PowerShell-skript. PowerShell-jobb steg stöds inte i Azure SQL-hanterad instans. 



**Rekommenderade**   
Avsnittet granska berörda objekt i Azure Migrate för att se alla jobb med PowerShell-jobb steg och utvärdera om jobb steget eller det påverkade objektet kan tas bort.  Utvärdera om Azure Automation kan användas. Du kan också migrera till SQL Server på en virtuell Azure-dator

Mer information: [SQL Server Agent skillnader i Azure SQL Managed instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="queue-reader-job"></a>Queue Reader-jobb<a id="QueueReaderJob"></a>

**Title: Queue-jobb-steget stöds inte i Azure SQL-hanterad instans.**   
**Kategori**: varning   

**Beteckning**   
Det är ett jobb steg som aktiverar replikeringen Köläsaragent. Replikerings Köläsaragent är en körbar fil som läser meddelanden som lagras i en Microsoft SQL Server kö eller i en kö i Microsoft Messages och sedan tillämpar dessa meddelanden i utgivaren. Köläsaragent används med ögonblicks bilder och transaktionella publikationer som tillåter uppdatering i kö. Jobb steget i köhanteraren stöds inte i Azure SQL-hanterad instans.


**Rekommenderade**   
Avsnittet granska påverkade objekt i Azure Migrate för att se alla jobb med jobb i köhanteraren och utvärdera om jobb steget eller det påverkade objektet kan tas bort. Du kan också migrera till SQL Server på en virtuell Azure-dator.

Mer information: [SQL Server Agent skillnader i Azure SQL Managed instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**Title: äldre stil RAISERROR-anropen ska ersättas med moderna motsvarigheter.**   
**Kategori**: varning   

**Beteckning**   
RAISERROR-anrop som exemplet nedan kallas äldre format eftersom de inte innehåller kommatecken och parentesen. RAISERROR 50001 ' Detta är ett test '. Den här metoden för att anropa RAISERROR upphör att gälla och tas bort i Azure SQL-hanterad instans.



**Rekommenderade**   
Skriv om instruktionen med den aktuella RAISERROR-syntaxen eller utvärdera om den moderna metoden `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` är genomförbar.

Mer information: utgångna [databas motor funktioner i SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="service-broker"></a>Service Broker<a id="ServiceBrokerWithNonLocalAddress"></a>

**Title: Service Broker-funktionen stöds delvis i Azure SQL-hanterad instans.**   
**Kategori**: problem   

**Beteckning**   
SQL Server Service Broker tillhandahåller inbyggt stöd för meddelanden och köer i SQL Server databas motor. Den här databasen innehåller Service Broker över instansen som inte stöds i Azure SQL-hanterad instans. 


**Rekommenderade**   
Den hanterade Azure SQL-instansen har inte stöd för tjänst Broker för överinstanser, d.v.s. där adressen inte är lokal. Du måste inaktivera Service Broker med följande kommando innan du migrerar den här databasen till Azure: `ALTER DATABASE [database_name] SET DISABLE_BROKER` ; Dessutom kan du också behöva ta bort eller stoppa Service Broker slut punkten för att förhindra att meddelanden anländer till SQL-instansen. När databasen har migrerats till Azure kan du titta på Azure Service Bus-funktioner för att implementera ett allmänt, molnbaserad meddelande system i stället för Service Broker. Du kan också migrera till SQL Server på en virtuell Azure-dator. 

Mer information: [Service Broker skillnader i Azure SQL Managed instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#service-broker)

## <a name="sql-mail"></a>SQL-e-post<a id="SqlMail"></a>

**Title: SQL Mail har avbrutits.**   
**Kategori**: varning   


**Beteckning**   
SQL Mail har avbrutits och tagits bort i Azure SQL-hanterad instans.



**Rekommenderade**   
Använd Database Mail.

Mer information: utgångna [databas motor funktioner i SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>


**Title: identifierade instruktioner som hänvisar till borttagna system lagrade procedurer som inte är tillgängliga i Azure SQL-hanterad instans.**   
**Kategori**: varning   

**Beteckning**   
Följande system som inte stöds och utökade lagrade procedurer kan inte användas i Azure SQL Managed instance –,,,, `sp_dboption` `sp_addserver` `sp_dropalias` `sp_activedirectory_obj` `sp_activedirectory_scp` och `sp_activedirectory_start` . 




**Rekommenderade**   
Ta bort referenser till system procedurer som inte stöds och som har tagits bort i Azure SQL-hanterad instans.

Mer information: utgångna [databas motor funktioner i SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="transact-sql-job"></a>Transact-SQL-jobb<a id="TransactSqlJob"></a>

**Title: jobb steget TSQL innehåller kommandon som inte stöds i Azure SQL-hanterad instans**   
**Kategori**: varning   


**Beteckning**   
Det är ett jobb steg som kör TSQL-skript vid schemalagd tidpunkt. TSQL jobb steg innehåller kommandon som inte stöds och som inte stöds i Azure SQL-hanterad instans.



**Rekommenderade**   
Avsnittet granska påverkade objekt i Azure Migrate för att se alla jobb som innehåller kommandon som inte stöds i Azure SQL Managed instance och utvärdera om jobb steget eller det påverkade objektet kan tas bort. Du kan också migrera till SQL Server på en virtuell Azure-dator.

Mer information: [SQL Server Agent skillnader i Azure SQL Managed instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="trace-flags"></a>Spårnings flaggor<a id="TraceFlags"></a>

**Title: spårnings flaggor som inte stöds i den hanterade Azure SQL-instansen hittades**   
**Kategori**: varning   


**Beteckning**   
Azure SQL-hanterad instans har endast stöd för ett begränsat antal globala spårnings flaggor. Spårnings flaggor för sessioner stöds inte.



**Rekommenderade**   
Avsnittet granska påverkade objekt i Azure Migrate för att se alla spårnings flaggor som inte stöds i Azure SQL-hanterad instans och utvärdera om de kan tas bort. Du kan också migrera till SQL Server på en virtuell Azure-dator.

Mer information: [spårnings flaggor](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql#trace-flags)


## <a name="windows-authentication"></a>Windows-autentisering<a id="WindowsAuthentication"></a>

**Title: databas användare som mappas med Windows-autentisering (integrerad säkerhet) stöds inte i Azure SQL-hanterad instans**   
**Kategori**: varning   


**Beteckning**   
Azure SQL Managed instance stöder två typer av autentisering: 
- SQL-autentisering, som använder ett användar namn och lösen ord
- Azure Active Directory-autentisering, som använder identiteter som hanteras av Azure Active Directory och stöder hanterade och integrerade domäner. 

Databas användare som mappas med Windows-autentisering (integrerad säkerhet) stöds inte i Azure SQL-hanterad instans. 


**Rekommenderade**   
Federera den lokala Active Directory med Azure Active Directory. Windows-identiteten kan sedan ersättas med motsvarande Azure Active Directory identiteter. Du kan också migrera till SQL Server på en virtuell Azure-dator.

Mer information: [säkerhets funktioner för SQL-hanterad instans](../../database/security-overview.md#authentication)


## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**Title: xp_cmdshell stöds inte i Azure SQL-hanterad instans.**   
**Kategori**: problem   


**Beteckning**   
Xp_cmdshell som skapar ett Windows Command Shell och skickar i en sträng för körning stöds inte i Azure SQL-hanterad instans. 



**Rekommenderade**   
Avsnittet granska påverkade objekt i Azure Migrate för att se alla objekt med xp_cmdshell och utvärdera om referensen till xp_cmdshell eller det påverkade objektet kan tas bort. Överväg att utforska Azure Automation som levererar molnbaserad automatiserings-och konfigurations tjänst. Du kan också migrera till SQL Server på en virtuell Azure-dator.

Mer information: [skillnader i lagrade procedurer i Azure SQL Managed instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers)

## <a name="next-steps"></a>Nästa steg

Om du vill börja migrera din SQL Server till Azure SQL-hanterad instans kan du läsa [migreringsguiden för SQL Server till SQL-hanterad instans](sql-server-to-managed-instance-guide.md).

- För en matris med tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering samt särskilda uppgifter, se [tjänst och verktyg för datamigrering](../../../dms/dms-tools-matrix.md).

- Mer information om Azure SQL-hanterad instans finns i:
   - [Tjänst nivåer i Azure SQL-hanterad instans](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Skillnader mellan SQL Server och Azure SQL-hanterad instans](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Kostnad för total ägande kostnad för Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Mer information om ramverket och implementerings cykeln för molnbaserad migrering finns i
   -  [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Metod tips för kostnads-och storleks arbets belastningar migreras till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Information om hur du bedömer program åtkomst lagret finns i [Data Access Migration Toolkit (för hands version)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Mer information om hur du utför data åtkomst Layer A/B-testning finns [Database experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).