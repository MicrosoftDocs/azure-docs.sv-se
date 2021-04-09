---
title: 'SQL Server till Azure SQL-hanterad instans: prestanda analys'
description: Lär dig att skapa och jämföra en bas linje för prestanda när du migrerar SQL Server-databaser till Azure SQL-hanterad instans.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mokabiru
ms.date: 11/06/2020
ms.openlocfilehash: a97dabe36efb252b04c1b5c8fa741d33a6c92703
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023681"
---
# <a name="migration-performance-sql-server-to--azure-sql-managed-instance-performance-analysis"></a>Migrerings prestanda: SQL Server till prestanda analys av Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Skapa en bas linje för prestanda som jämför arbets Belastningens prestanda på en SQL-hanterad instans med din ursprungliga arbets belastning som körs på SQL Server. 

## <a name="create-a-baseline"></a>Skapa en bas linje

Vi rekommenderar att prestandan är liknande eller bättre efter migreringen, så det är viktigt att mäta och registrera bas linje prestanda värden på källan och sedan jämföra dem med mål miljön. En bas linje för prestanda är en uppsättning parametrar som definierar din genomsnittliga arbets belastning på din källa. 

Välj en uppsättning frågor som är viktiga för och som är representativ för din verksamhets arbets belastning. Mät och dokumentera minimi-/genomsnitts tiden och CPU-användningen för dessa frågor, samt prestanda mått på käll servern, t. ex. genomsnittlig/högsta CPU-användning, genomsnittlig/högsta disk-i/o-latens, data flöde, IOPS, Genomsnittligt/Max förväntad och genomsnittlig Max storlek på tempdb. 

Följande resurser kan hjälpa dig att definiera en bas linje för prestanda: 

   - [Övervaka CPU-användning ](https://techcommunity.microsoft.com/t5/azure-sql-database/monitor-cpu-usage-on-sql-server-and-azure-sql/ba-p/680777#M131)
   - [Övervaka minnes användning](/sql/relational-databases/performance-monitor/monitor-memory-usage)   och avgör hur mycket minne som används av olika komponenter, t. ex. buffertpooltillägget, planera cache, Column-Store-pool, [minnes intern OLTP](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage)osv. Dessutom bör du hitta de genomsnittliga och högsta värdena för sid livstidens förväntad minnes prestanda räknare. 
   - Övervaka diskens IO-användning på käll SQL Servers instansen med hjälp av [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)   Visa eller [prestanda räknare](/sql/relational-databases/performance-monitor/monitor-disk-usage). 
   - Övervaka prestanda för arbets belastning och frågor genom att undersöka vyer för dynamisk hantering (eller Query Store om du migrerar från SQL Server 2016 och senare). Identifiera Genomsnittlig varaktighet och CPU-användning för de viktigaste frågorna i din arbets belastning. 

Prestanda problem på käll SQL Server bör åtgärdas innan migreringen. Om du migrerar kända problem till nya system kan det leda till oväntade resultat och ogiltig prestanda jämförelse. 


## <a name="compare-performance"></a>Jämför prestanda 

När du har definierat en bas linje jämför du liknande arbets belastnings prestanda för den SQL-hanterade instansen. Av precision är det viktigt att den SQL-hanterade instans miljön är jämförbar med SQL Server miljön så mycket som möjligt. 

Det finns skillnader i infrastruktur för SQL-hanterade instanser som gör matchnings prestanda exakt osannolik. Vissa frågor kan köras snabbare än förväntat, medan andra kan vara långsammare. Syftet med den här jämförelsen är att kontrol lera att arbets belastnings prestandan i den hanterade instansen matchar prestanda på SQL Server (i genomsnitt) och för att identifiera kritiska frågor med prestanda som inte matchar dina ursprungliga prestanda. 

Prestanda jämförelsen kommer troligen att resultera i följande resultat: 

- Arbets belastnings prestanda på den hanterade instansen är anpassad eller bättre än arbets belastnings prestandan på din käll SQL Server. I det här fallet har du bekräftat att migreringen lyckades. 

- Majoriteten av prestanda parametrarna och frågorna i arbets belastningen fungerar som förväntat, med vissa undantag som resulterar i försämrade prestanda. I det här fallet identifierar du skillnaderna och deras prioritet. Om det finns några viktiga frågor med försämrade prestanda bör du undersöka om de underliggande SQL-planerna har ändrats eller om frågor har nått resurs gränser. Du kan minska detta genom att använda några tips på kritiska frågor (till exempel ändra kompatibilitetsnivå, tidigare kardinalation-uppskattning), antingen direkt eller med hjälp av plan guider. Se till att statistik och index är uppdaterade och motsvarar varandra i båda miljöerna. 

- De flesta frågor går långsammare på en hanterad instans jämfört med din käll SQL Server instans. I det här fallet kan du prova att identifiera rotor saken till skillnaden, till exempel för att [nå vissa resurs](../../managed-instance/resource-limits.md#service-tier-characteristics) gränser, till exempel IO-, minnes-eller instans logg frekvens gränser. Om det inte finns några resurs gränser som orsakar skillnaden kan du prova att ändra kompatibilitetsnivån för databasen eller ändra databas inställningar som bakåtkompatibelt kardinalitet och köra testet igen. Granska de rekommendationer som tillhandahålls av vyerna hanterad instans eller Frågearkivet för att identifiera frågor med försämrat-prestanda. 

SQL-hanterad instans har en inbyggd funktion för automatisk plan korrigering som är aktive rad som standard. Den här funktionen säkerställer att frågor som fungerat bra tidigare inte försämras i framtiden. Om den här funktionen inte är aktive rad kan du köra arbets belastningen med de gamla inställningarna så att SQL-hanterad instans kan lära sig prestanda bas linjen. Aktivera sedan funktionen och kör arbets belastningen igen med de nya inställningarna. 

Ändra parametrarna för testet eller uppgradera till högre tjänst nivåer för att uppnå den optimala konfigurationen för de arbets belastnings prestanda som passar dina behov. 

## <a name="monitor-performance"></a>Övervaka prestanda 

SQL-hanterad instans innehåller avancerade verktyg för övervakning och fel sökning, och du bör använda dem för att övervaka prestanda på din instans. Några av de nyckel mått som ska övervakas är: 

- CPU-användning på instansen för att avgöra om antalet virtuella kärnor som du har allokerat är rätt matchning för din arbets belastning. 
- Förväntad på din hanterade instans för att avgöra om du behöver [mer minne](https://techcommunity.microsoft.com/t5/azure-sql-database/do-you-need-more-memory-on-azure-sql-managed-instance/ba-p/563444).
-  Statistik som INSTANCE_LOG_GOVERNOR eller PAGEIOLATCH som identifierar lagrings-i/o-problem, särskilt på Generell användning nivå, där du kan behöva förallokera filer för att få bättre IO-prestanda. 


## <a name="considerations"></a>Överväganden  

Tänk på följande när du jämför prestanda: 

- Inställningarna överensstämmer mellan källa och mål. Kontrol lera att olika inställningar för instans, databas och tempdb är likvärdiga mellan de två miljöerna. Skillnader i konfiguration, kompatibilitetsnivå, krypterings inställningar, spårnings flaggor osv. kan ge alla sneda prestanda. 

- Lagringen konfigureras enligt [bästa praxis](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525). För Generell användning kan du till exempel behöva förallokera filernas storlek för att förbättra prestandan. 

- Det finns [viktiga skillnader i miljön](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) som kan orsaka prestanda skillnaderna mellan en hanterad instans och SQL Server. Identifiera risker som är relevanta för din miljö och som kan bidra till ett prestanda problem. 

- Frågearkivet och automatisk justering ska vara aktiverat på din SQL-hanterade instans eftersom de hjälper dig att mäta arbets belastnings prestanda och automatiskt minimera potentiella prestanda problem. 



## <a name="next-steps"></a>Nästa steg

Mer information om hur du optimerar din nya Azure SQL-hanterade instans miljö finns i följande resurser: 

- [Hur ska man identifiera varför arbets belastnings prestanda på en hanterad Azure SQL-instans skiljer sig från SQL Server?](https://medium.com/azure-sqldb-managed-instance/what-to-do-when-azure-sql-managed-instance-is-slower-than-sql-server-dd39942aaadd)
- [Viktiga orsaker till prestanda skillnader mellan SQL-hanterad instans och SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)
- [Bästa praxis och överväganden för lagrings prestanda för Azure SQL-hanterad instans (Generell användning)](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)
- [Prestanda övervakning i real tid för Azure SQL-hanterad instans (detta är arkiverat, är det avsedda målet?)](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)