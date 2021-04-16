---
title: 'SQL Server till Azure SQL Managed Instance: Prestandabaslinje'
description: Lär dig att skapa och jämföra en prestandabaslinje när du migrerar dina SQL Server-databaser till Azure SQL Managed Instance.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: mokabiru
ms.date: 11/06/2020
ms.openlocfilehash: a47684bf29f1f34b8c9c59c04b7d33d234505cc2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389715"
---
# <a name="migration-performance-sql-server-to--azure-sql-managed-instance-performance-baseline"></a>Migreringsprestanda: SQL Server för Azure SQL Managed Instance prestandabaslinje
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Skapa en prestandabaslinje för att jämföra prestandan för din arbetsbelastning på en SQL Managed Instance med din ursprungliga arbetsbelastning som körs SQL Server. 

## <a name="create-a-baseline"></a>Skapa en baslinje

Vi rekommenderar att prestandan är liknande eller bättre efter migreringen, så det är viktigt att mäta och registrera baslinjens prestandavärden på källan och sedan jämföra dem med målmiljön. En prestandabaslinje är en uppsättning parametrar som definierar din genomsnittliga arbetsbelastning på källan. 

Välj en uppsättning frågor som är viktiga för och representativa för din affärsarbetsbelastning. Mät och dokumentera min/genomsnittlig/maximal varaktighet och CPU-användning för dessa frågor, samt prestandamått på källservern, till exempel genomsnittlig/max CPU-användning, genomsnittlig/maximal disk-I/O-svarstid, dataflöde, IOPS, genomsnittlig/maximal förväntad sidlivslängd och genomsnittlig maximal storlek för tempdb. 

Följande resurser kan hjälpa dig att definiera en prestandabaslinje: 

   - [Övervaka CPU-användning ](https://techcommunity.microsoft.com/t5/azure-sql-database/monitor-cpu-usage-on-sql-server-and-azure-sql/ba-p/680777#M131)
   - [Övervaka minnesanvändning](/sql/relational-databases/performance-monitor/monitor-memory-usage)   och fastställ mängden minne som används av olika komponenter, till exempel buffertpool, plancache, kolumnlagringspool, minnes [in memory OLTP](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage)osv. Dessutom bör du hitta genomsnittliga och högsta värden för prestandaräknaren för förväntad sidlivslängd. 
   - Övervaka disk-I/S-användning SQL Server källinstansen [med sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)   eller [prestandaräknare.](/sql/relational-databases/performance-monitor/monitor-disk-usage) 
   - Övervaka arbetsbelastnings- och frågeprestanda genom att undersöka dynamiska hanteringsvyer (eller Query Store om du migrerar från SQL Server 2016 och senare). Identifiera genomsnittlig varaktighet och CPU-användning för de viktigaste frågorna i din arbetsbelastning. 

Eventuella prestandaproblem på SQL Server bör åtgärdas före migreringen. Migrering av kända problem till ett nytt system kan orsaka oväntade resultat och göra prestandajämförelser ogiltiga. 


## <a name="compare-performance"></a>Jämför prestanda 

När du har definierat en baslinje kan du jämföra liknande arbetsbelastningsprestanda på SQL Managed Instance. För noggrannhetens del är det viktigt att SQL Managed Instance miljö är jämförbar med den SQL Server miljön så mycket som möjligt. 

Det finns SQL Managed Instance infrastrukturskillnader som gör det helt osannolikt att prestandan matchar. Vissa frågor kan köras snabbare än förväntat, medan andra kan vara långsammare. Målet med den här jämförelsen är att verifiera att arbetsbelastningens prestanda i den hanterade instansen matchar prestanda för SQL Server (i genomsnitt) och att identifiera kritiska frågor med prestanda som inte matchar din ursprungliga prestanda. 

Prestandajämförelse resulterar troligen i följande resultat: 

- Arbetsbelastningsprestanda på den hanterade instansen är justerad eller bättre än arbetsbelastningens prestanda på SQL Server. I det här fallet har du bekräftat att migreringen lyckades. 

- De flesta prestandaparametrar och frågor i arbetsbelastningen fungerar som förväntat, med vissa undantag som resulterar i försämrad prestanda. I det här fallet identifierar du skillnaderna och deras betydelse. Om det finns några viktiga frågor med försämrad prestanda kan du undersöka om de underliggande SQL-planerna har ändrats eller om frågorna har stött på resursgränserna. Du kan åtgärda detta genom att använda några tips på kritiska frågor (till exempel ändra kompatibilitetsnivå, äldre kardinalitetsestimator) antingen direkt eller med hjälp av planguider. Se till att statistik och index är uppdaterade och likvärdiga i båda miljöerna. 

- De flesta frågor är långsammare på en hanterad instans jämfört med din SQL Server instans. I det här fallet kan du försöka identifiera [](../../managed-instance/resource-limits.md#service-tier-characteristics) rotorsaken till skillnaden, till exempel att nå en viss resursgräns, till exempel gränser för I/O, minne eller instanslogghastighet. Om det inte finns några resursgränser som orsakar skillnaden kan du prova att ändra kompatibilitetsnivån för databasen eller ändra databasinställningarna, till exempel beräkning av äldre kardinalitet, och köra testet igen. Granska rekommendationerna från den hanterade instansen eller Query Store-vyerna för att identifiera frågor med regrederade prestanda. 

SQL Managed Instance har en inbyggd funktion för automatisk plankorrigering som är aktiverad som standard. Den här funktionen säkerställer att frågor som fungerat bra tidigare inte försämras i framtiden. Om den här funktionen inte är aktiverad kör du arbetsbelastningen med de gamla inställningarna så SQL Managed Instance kan lära dig prestandabaslinjen. Aktivera sedan funktionen och kör arbetsbelastningen igen med de nya inställningarna. 

Gör ändringar i parametrarna för testet eller uppgradera till högre tjänstnivåer för att uppnå den optimala konfigurationen för den arbetsbelastningsprestanda som passar dina behov. 

## <a name="monitor-performance"></a>Övervaka prestanda 

SQL Managed Instance innehåller avancerade verktyg för övervakning och felsökning, och du bör använda dem för att övervaka prestanda på din instans. Några av de viktigaste måtten att övervaka är: 

- CPU-användning på instansen för att avgöra om antalet virtuella kärnor som du har etablerat är rätt matchning för din arbetsbelastning. 
- Förväntad sidlivslängd på din hanterade instans för att avgöra om du [behöver ytterligare minne.](https://techcommunity.microsoft.com/t5/azure-sql-database/do-you-need-more-memory-on-azure-sql-managed-instance/ba-p/563444)
-  Statistik som INSTANCE_LOG_GOVERNOR eller PAGEIOLATCH som identifierar problem med lagrings-I/O, särskilt på Generell användning-nivån, där du kan behöva allokera filer i förväg för att få bättre I/O-prestanda. 


## <a name="considerations"></a>Överväganden  

Tänk på följande när du jämför prestanda: 

- Inställningarna matchar mellan källa och mål. Kontrollera att olika inställningar för instans, databas och tempdb är likvärdiga mellan de två miljöerna. Skillnader i konfiguration, kompatibilitetsnivåer, krypteringsinställningar, spårningsflaggor osv. kan ha olika prestanda. 

- Lagring konfigureras enligt [bästa praxis.](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525) Du kan till Generell användning behöva allokera filernas storlek i förväg för att förbättra prestandan. 

- Det finns [viktiga miljöskillnader](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) som kan orsaka prestandaskillnader mellan en hanterad instans och SQL Server. Identifiera risker som är relevanta för din miljö och som kan bidra till ett prestandaproblem. 

- Query Store och automatisk justering bör aktiveras på din SQL Managed Instance eftersom de hjälper dig att mäta arbetsbelastningens prestanda och automatiskt minimera potentiella prestandaproblem. 



## <a name="next-steps"></a>Nästa steg

Mer information om hur du optimerar din Azure SQL Managed Instance miljö finns i följande resurser: 

- [Hur identifierar jag varför arbetsbelastningsprestanda på Azure SQL Managed Instance skiljer sig från SQL Server?](https://medium.com/azure-sqldb-managed-instance/what-to-do-when-azure-sql-managed-instance-is-slower-than-sql-server-dd39942aaadd)
- [Viktiga orsaker till prestandaskillnader mellan SQL Managed Instance och SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)
- [Metodtips och överväganden för lagringsprestanda för Azure SQL Managed Instance (Generell användning)](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)
- [Prestandaövervakning i realtid för Azure SQL Managed Instance (detta är arkiverat, är detta det avsedda målet?)](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)