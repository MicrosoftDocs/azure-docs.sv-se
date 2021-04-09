---
title: Fördelar med att använda Azure NetApp Files för SQL Server-distribution | Microsoft Docs
description: Visar en detaljerad kostnads analys av prestanda för delar om att använda Azure NetApp Files för SQL Server-distribution.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2021
ms.author: b-juche
ms.openlocfilehash: 46fe7570b7b9ea9446918d407dbe87596b8d0496
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863912"
---
#  <a name="benefits-of-using-azure-netapp-files-for-sql-server-deployment"></a>Fördelar med att använda Azure NetApp Files för SQL Server distribution

Azure NetApp Files minskar SQL Server totala ägande kostnaden (TCO) jämfört med block lagrings lösningar.  Med block lagring har virtuella datorer begränsningar för I/O och bandbredd för disk åtgärder, medan endast nätverks bandbredds gränser tillämpas på Azure NetApp Files.  Inga VM-I/O-gränser tillämpas på Azure NetApp Files. Utan de här I/O-gränserna kan SQL Server som körs på mindre virtuella datorer som är anslutna till Azure NetApp Files utföra såväl som SQL Server som körs på mycket större virtuella datorer. Genom att ändra storlek på instansen minskar du beräknings kostnaden till 25% av den förra pris tag gen.  *Du kan minska beräknings kostnaderna med Azure NetApp Files.*  

Beräknings kostnader är dock små jämfört med SQL Server licens kostnader.  Microsoft SQL Server- [licensiering](https://download.microsoft.com/download/B/C/0/BC0B2EA7-D99D-42FB-9439-2C56880CAFF4/SQL_Server_2017_Licensing_Datasheet.pdf) är bundet till det fysiska antalet kärnor. Därför medför minskning av instans storleken en ännu större kostnad för program varu licensiering. *Du kan minska kostnaderna för program varu licenser med Azure NetApp Files.*

Kostnaden för själva lagringen är variabel, beroende på databasens faktiska storlek. Oavsett vilket lagrings utrymme som valts är kapaciteten har kostnaden, oavsett om det är en hanterad disk eller fil resurs.  När databas storlekar ökar och lagringen ökar i kostnad, ökar lagrings utrymmet till TCO, vilket påverkar den totala kostnaden.  Därför justeras kontrollen enligt följande: *du kan minska kostnaderna för SQL Server distribution med Azure NetApp Files.* 

Den här artikeln innehåller en detaljerad kostnads analys och prestanda för delar om att använda Azure NetApp Files för SQL Server-distribution. Det är inte bara mindre instanser som har tillräckligt med CPU för att göra databasen bara möjlig med block på större instanser, *i många fall är de mindre instanserna ännu mer utförda än deras större, diskbaserade motsvarigheter på grund av Azure NetApp Files.* 

## <a name="detailed-cost-analysis"></a>Detaljerad kostnadsanalys 

De två bild uppsättningarna i det här avsnittet visar exempel på TCO.  Antalet och typen av hanterade diskar, Azure NetApp Files service nivå och kapaciteten för varje scenario har valts för att uppnå bästa möjliga pris kapacitets prestanda.  Varje bild består av grupperade datorer (D16 med Azure NetApp Files jämfört med D64 med hanterad disk med exempel) och priserna delas upp för varje typ av dator.  

Den första uppsättningen grafik visar den totala kostnaden för lösningen med en 1-TiB databas storlek, och jämför D16s_v3 med D64, D8 till D32 och D4 till D16. De planerade IOPs för varje konfiguration anges med en grön eller gul linje och motsvarar Y-axeln på höger sida.

[![Bild som visar den totala kostnaden för lösningen med en 1-TIB databas storlek. ](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png#lightbox)


Den andra uppsättningen grafik visar den totala kostnaden med en 50-TiB-databas. Jämförelserna är i övrigt samma – D16 jämfört med Azure NetApp Files jämfört med D64 med block som exempel. 

[![Bild som visar den totala kostnaden med en 50-TIB databas storlek. ](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png#lightbox)
 
## <a name="performance-and-lots-of-it"></a>Prestanda och massor av IT  

För att kunna leverera på den avsevärda kostnads minskningen krävs mycket prestanda – de största instanserna i den allmänna Azure Inventory support 80 000 disk IOPS med exempel. En enskild Azure NetApp Files volym kan uppnå 80 000 databas-IOPS, och instanser som D16 kan använda samma. D16, som normalt stöder 25 600-disk-IOPS, är 25% av storleken på D64.  D64s_v3 kan 80 000 disk-IOPS och utgör en utmärkt jämförelse punkt på hög nivå.

D16s_v3 kan köra en Azure NetApp Files volym till 80 000-databasen IOPS. Som beprövas av SQL Storage benchmark (SSB)-benchmarking-verktyget nådde D16-instansen en arbets belastning 125% större än den som kan nås av disk från D64-instansen.  Mer information om verktyget finns i avsnittet [SSB testing tool](#ssb-testing-tool) .

Om du använder en 1-TiB arbets uppsättnings storlek och en 80% Läs-, 20% uppdatering SQL Server arbets belastning, prestanda funktionerna för de flesta instanserna i D-instansen mättes. de flesta är inte alla, eftersom D2-och D64-instanserna inte var undantagna från testningen. Den tidigare slutade att fungera eftersom den inte har stöd för accelererat nätverk, och det senare eftersom det är jämförelse punkten. Se följande diagram för att förstå gränserna för D4s_v3, D8s_v3, D16s_v3 respektive D32s_v3.  Test av hanterade disk lagring visas inte i diagrammet. Jämförelse värden ritas direkt från [tabellen med gränser för virtuella Azure-datorer](../virtual-machines/dv3-dsv3-series.md) för instans typen D-klass.

Med Azure NetApp Files kan var och en av instanserna i D-klassen uppfylla eller överskrida disk prestanda funktionerna för instanser två gånger större.  *Du kan minska kostnaderna för program varu licenser avsevärt med Azure NetApp Files.*  

* CPU-belastningen för D4 vid 75% matchade disk funktionerna i D16.  
    * D16 är en hastighet som är begränsad till 25 600 disk-IOPS.  
* Den D8 vid 75% processor användning matchade disk funktionerna i D32.  
    * D32 är en hastighet som är begränsad till 51 200 disk-IOPS.  
* CPU-belastningen D16 vid 55% matchade disk funktionerna i D64.  
    * D64 är en hastighet som är begränsad till 80 000 disk-IOPS.  
* D32 med 15% processor användning matchade även disk funktionerna i D64.  
    * D64 som anges ovan är en hastighet som är begränsad till 80 000 disk-IOPS.  

### <a name="s3b-cpu-limits-test--performance-versus-processing-power"></a>S3B CPU begränsar testet – prestanda jämfört med bearbetnings kraften

I följande diagram sammanfattas testet S3B-CPU-gränser:

![Diagram som visar genomsnittlig processor procent för en instans SQL Server över Azure NetApp Files.](../media/azure-netapp-files/solution-sql-server-single-instance-average-cpu.png)

Skalbarhet är bara en del av artikeln. Den andra delen är en fördröjning.  Det är en sak för mindre virtuella datorer att kunna köra mycket högre I/O-priser, det är en annan sak att göra detta med låg latens latens enligt nedan.  

* D4 bidrog 26 000 IOPS mot Azure NetApp Files vid 2,3-MS-svars tid.  
* D8 bidrog 51 000 IOPS mot Azure NetApp Files vid 2,0-MS-svars tid.  
* D16 bidrog 88 000 IOPS mot Azure NetApp Files vid 2,8-MS-svars tid.
* D32 bidrog 80 000 IOPS mot Azure NetApp Files vid 2,4-MS-svars tid.  

### <a name="s3b-per-instance-type-latency-results"></a>Svars resultat för S3B per instans typ

Följande diagram visar svars tiden för en instans SQL Server över Azure NetApp Files:

![Diagram som visar svars tid för en instans SQL Server över Azure NetApp Files.](../media/azure-netapp-files/solution-sql-server-single-instance-latency.png)

## <a name="ssb-testing-tool"></a>SSB test verktyg 
 
[TPC-E-](http://www.tpc.org/tpce/) benchmarking-verktyget, efter design, betonar *beräkning* i stället för *lagring*. Test resultaten som visas i det här avsnittet baseras på ett stress test verktyg med namnet SQL Storage benchmark (SSB).  SQL Server lagrings-benchmark kan driva storskalig SQL-körning mot en SQL Server databas för att simulera en OLTP-arbets belastning, på liknande sätt som [SLOB2-verktyget för Oracle-prestandatest](https://kevinclosson.net/slob/). 

Verktyget SSB genererar en URVALs-och UPPDATERINGs driven arbets belastning som utfärdar dessa uttryck direkt till den SQL Server databasen som körs i den virtuella Azure-datorn.  För det här projektet är SSB-arbetsbelastningar som är rampiska från 1 till 100 SQL Server användare, med 10 eller 12 mellanliggande punkter vid 15 minuter per antal användare.  Alla prestanda mått från dessa körningar var från övervaknings punkten, för repeterbarhet SSB kördes tre gånger per scenario. 

Själva testerna har kon figurer ATS som 80% SELECT och 20% UPDATE-instruktion, och därför 90% slumpmässig läsning.  Själva databasen, som SSB skapades, var 1000 GB stor. Den består av 15 användar tabeller och 9 000 000 rader per användar tabell och 8192 byte per rad. 

SSB-Benchmark är ett verktyg med öppen källkod.  Den är gratis tillgänglig på [sidan benchmark-GitHub för SQL Storage](https://github.com/NetApp/SQL_Storage_Benchmark.git).  


## <a name="in-summary"></a>Sammanfattning  

Med Azure NetApp Files kan du öka SQL Server-prestandan samtidigt som du minskar den totala ägande kostnaden avsevärt. 

## <a name="next-steps"></a>Nästa steg

* [Skapa en SMB-volym för Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) 
* [Lösnings arkitekturer som använder Azure NetApp Files – SQL Server](azure-netapp-files-solution-architectures.md#sql-server) 

