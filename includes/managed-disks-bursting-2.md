---
title: ta med fil
description: ta med fil
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 03/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 3035b5d2803ff91e84bc6b47a99963185f9195d3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102623581"
---
## <a name="disk-level-bursting"></a>Burst-överföring på disk nivå

### <a name="on-demand-bursting-preview"></a>Burst-överföring på begäran (för hands version)

Diskar som använder burst-modellen på begäran för disk burst kan överföras bortom de ursprungliga etablerade målen, så ofta som det behövs för deras arbets belastning, upp till max burst-målet. På en 1-TiB P30-disk är den etablerade IOPS till exempel 5000 IOPS. När disk burst har Aktiver ATS på den här disken kan arbets belastningarna utfärda IOs till den här disken upp till max burst-prestanda på 30 000 IOPS och 1 000 Mbit/s.

Om du förväntar dig att arbets belastningarna ofta ska köras bortom det tillhandahållna prestanda målet kommer disk burst inte att vara kostnads effektivt. I det här fallet rekommenderar vi att du ändrar diskens prestanda nivå till en [högre nivå](../articles/virtual-machines/disks-performance-tiers.md) i stället för att få bättre bas linje prestanda. Granska din fakturerings information och utvärdera den mot trafik mönstret för dina arbets belastningar.

Innan du aktiverar burst på begäran kan du förstå följande:

[!INCLUDE [managed-disk-bursting-regions-limitations](managed-disk-bursting-regions-limitations.md)]

#### <a name="regional-availability"></a>Regional tillgänglighet

[!INCLUDE [managed-disk-bursting-availability](managed-disk-bursting-availability.md)]

#### <a name="billing"></a>Fakturering

Diskar som använder burst-modellen på begäran debiteras en fast avgift för en timmes burst-överföring och transaktionskostnader gäller för alla burst-transaktioner utöver det etablerade målet. Transaktionskostnader debiteras med hjälp av modellen betala per användning, baserat på cachelagrade disk-IOs, inklusive både läsningar och skrivningar som överskrider etablerade mål. Följande är ett exempel på disk trafik mönster under en fakturerings timme:

Disk konfiguration: Premium SSD – 1 TiB (P30), disk burst aktive rad.

- 00:00:00 – 00:10:00 disk IOPS under det etablerade målet av 5 000 IOPS 
- 00:10:01 – 00:10:10-programmet utfärdade ett batch-jobb som orsakar att disken IOPS överfördes till burst vid 6 000 IOPS i 10 sekunder 
- 00:10:11 – 00:59:00 disk IOPS under det etablerade målet av 5 000 IOPS 
- 00:59:01 – 01:00:00-program utfärdade ett annat batch-jobb som orsakar att disken IOPS skickas till burst vid 7 000 IOPS i 60 sekunder 

Under den här fakturerings tiden består kostnaden för burst-överföring av två kostnader:

Den första avgiften är den fasta $Xen för burst-aktivering (bestäms av din region). Denna fasta avgift debiteras alltid på disken bortse från status för att ansluta tills den är inaktive rad. 

Det andra är kostnaden för burst-transaktioner. Disk-burst inträffade under två tids kort platser. Från 00:10:01 – 00:10:10 är den ackumulerade burst-transaktionen (6 000 – 5 000) X 10 = 10 000. Från 00:59:01 – 01:00:00 är den ackumulerade burst-transaktionen (7 000 – 5 000) X 60 = 120 000. De totala burst-transaktionerna är 10 000 + 120 000 = 130 000. Kostnaden för burst-transaktioner debiteras enligt $Y baserat på 13 enheter om 10 000 transaktioner (baserat på regional prissättning).

Med detta är den totala kostnaden för disk burst för den här fakturerings tiden lika med $X + $Y. Samma beräkning gäller för burst-överföring över etablerade mål på MBps. Vi översätter över MB till transaktioner med en i/o-storlek på 256 kB. Om din disk trafik överskrider både allokerat IOPS och Mbit/s-mål kan du referera till exemplet nedan för att beräkna burst-transaktionerna. 

Disk konfiguration: Premium SSD – 1 TB (P30), disk burst aktive rad.

- 00:00:01 – 00:00:05-programmet utfärdade ett batch-jobb som orsakar att disken IOPS överfördes till 10 000 IOPS och 300 MBps i fem sekunder.
- 00:00:06 – 00:00:10-programmet utfärdade ett återställnings jobb som orsakar att disken IOPS överfördes till 6 000 IOPS och 600 MBps i fem sekunder.

Burst-transaktionen redovisas som det högsta antalet transaktioner från antingen IOPS-eller MBps-burst. Från 00:00:01 – 00:00:05 är den ackumulerade burst-transaktionen Max ((10 000 – 5 000), (300-200) * 1024/256)) * 5 = 25 000 transaktioner. Från 00:00:06 – 00:00:10 är den ackumulerade burst-transaktionen Max ((6 000 – 5 000), (600-200) * 1024/256)) * 5 = 8 000 transaktioner. Ovanpå detta inkluderar du en fast avgift för burst-aktivering för att få den totala kostnaden för att aktivera disk burst på begäran. 

På [sidan för Managed disks priser](https://azure.microsoft.com/pricing/details/managed-disks/) hittar du information om priser och använder [Azures pris kalkylator](https://azure.microsoft.com/pricing/calculator/?service=storage) för att utvärdera din arbets belastning. 

### <a name="credit-based-bursting"></a>Kredit baserad burst-överföring

Kreditbaserade burst-överföring är tillgängligt för disk storlekar P20 och mindre i alla regioner i molnet offentliga Azure, myndigheter och Kina. Som standard aktive ras disk-burst på alla nya och befintliga distributioner av disk storlekar som stöds. Burst-överföring på virtuell dator använder endast krediterad burst-överföring.

## <a name="virtual-machine-level-bursting"></a>Burst-överföring på virtuell dator

Burst-överföring på virtuell dator använder bara den kreditbaserade modellen för bursting, den är aktive rad som standard för alla virtuella datorer som har stöd för det.

Burst-överföring på virtuell dator har Aktiver ATS i alla regioner i det offentliga Azure-molnet med de storlekar som stöds: 
- [Lsv2-serien](../articles/virtual-machines/lsv2-series.md)
- [Dv3- och Dsv3-serien](../articles/virtual-machines/dv3-dsv3-series.md)
- [Ev3- och Esv3-serien](../articles/virtual-machines/ev3-esv3-series.md)

## <a name="bursting-flow"></a>Flödes flöde

Överförings kredit systemet gäller på samma sätt både på VM-nivå och disk nivå. Din resurs, antingen en virtuell dator eller en disk, kommer att börja med helt aktie krediter i sin egen burst-Bucket. Med dessa krediter kan du överföra upp till 30 minuter med högsta överföringshastighet. Du ackumulerar krediter när resursens IOPS eller MB/s används under resursens prestanda mål. Om din resurs har upplupna burst-krediter och din arbets belastning behöver extra prestanda, kan din resurs använda dessa krediter för att gå över dess prestanda gränser och öka prestandan för att uppfylla arbets Belastningens krav.

![Burst-Bucket.](media/managed-disks-bursting/bucket-diagram.jpg)

Hur du lägger upp dina tillgängliga krediter är upp till dig. Du kan använda dina 30 minuters burst-krediter i följd eller sporadiskt under dagen. När resurser distribueras levereras de med en fullständig allokering av krediter. När de bryter ned tar det mindre än en dag att ta på sig. Krediterna kan ägnas åt dig själv, burst-Bucket behöver inte vara full för att resurserna ska kunna överföras till burst. Burst-ackumulering varierar beroende på varje resurs, eftersom den baseras på oanvända IOPS och MB/s under deras prestanda mål. Högre prestanda resurser för bas linjen kan periodisera sina burst-krediter snabbare än lägre bas linje resurser. Till exempel kommer en tom disk tomgång att Periodisera 120 IOPS per sekund, medan en tom disk för P20 skulle leda till 2 300 IOPS per sekund.

## <a name="bursting-states"></a>Burst-tillstånd
Det finns tre tillstånd som din resurs kan vara aktive rad med burst-aktivering:
- **Periodisera** – RESURSens IO-trafik använder mindre än prestanda målet. Ackumulering av burst-krediter för IOPS och MB/s görs åtskilda från varandra. Din resurs kan skjuta upp IOPS-krediter och utgifter i MB/s-krediter eller vice versa.
- **Bursting** – resursens trafik använder mer än prestanda målet. Burst-trafiken förbrukar oberoende krediter från IOPS eller bandbredd.
- **Konstant** – resursens trafik är exakt i prestanda målet.

## <a name="bursting-examples"></a>Exempel på burst-överföring

I följande exempel visas hur burst fungerar med olika kombinationer av virtuella datorer och diskar. För att göra exemplen lätta att följa, kommer vi att fokusera på MB/s, men samma logik tillämpas oberoende av IOPS.

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>Icke-burst-virtuell dator med burst-diskar
**Kombination av virtuell dator och disk:** 
- Standard_D8as_v4 
    - Ej cachelagrade MB/s: 192
- P4 OS-disk
    - Etablerade MB/s: 25
    - Max burst MB/s: 170 
- 2 P10 data diskar 
    - Etablerade MB/s: 100
    - Max burst MB/s: 170

 När den virtuella datorn startar hämtas data från operativ system disken. Eftersom operativ system disken är en del av en virtuell dator som startas, är OS-disken full av de mellanliggande krediterna. Dessa krediter gör att OS-disken får en ny start på 170 MB/s sekund.

![VM skickar en begäran om 192 MB/s av data flödet till operativ system disken, OS-disken svarar med 170 MB/s-data.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-startup.jpg)

När starten är klar körs ett program på den virtuella datorn och har en icke-kritisk arbets belastning. Den här arbets belastningen kräver 15 MB/S som sprids jämnt över alla diskar.

![Programmet skickar en begäran om 15 MB/s av genomflödet till den virtuella datorn, den virtuella datorn tar emot begäran och skickar var och en av diskarna en begäran om 5 MB/s. varje disk returnerar 5 MB/s, och virtuella datorer returnerar 15 MB/s till programmet.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-idling.jpg)

Programmet måste sedan bearbeta ett batch-jobb som kräver 192 MB/s. 2 MB/s används av OS-disken och resten delas jämnt mellan data diskarna.

![Programmet skickar en begäran om 192 MB/s av data flödet till den virtuella datorn, den virtuella datorn tar emot begäran och skickar en begäran till data diskarna (95 MB/s varje) och 2 MB/s till OS-disken, data diskarna som är burst för att uppfylla efter frågan och alla diskar returnerar det begärda data flödet till den virtuella datorn, vilket returnerar det till programmet.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>Burst-baserad virtuell dator med icke-burst-diskar
**Kombination av virtuell dator och disk:** 
- Standard_L8s_v2 
    - Ej cachelagrade MB/s: 160
    - Max burst MB/s: 1 280
- P50 OS-disk
    - Etablerade MB/s: 250 
- 2 P10 data diskar 
    - Etablerade MB/s: 250

 Efter den första starten körs ett program på den virtuella datorn och har en icke-kritisk arbets belastning. Den här arbets belastningen kräver 30 MB/s som sprids jämnt över alla diskar.
![Programmet skickar en begäran om 30 MB/s av data flödet till den virtuella datorn, den virtuella datorn tar emot begäran och skickar var och en av diskarna en begäran om 10 MB/s, varje disk returnerar 10 MB/s. den virtuella datorn returnerar 30 MB/s till programmet.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

Programmet måste sedan bearbeta ett batch-jobb som kräver 600 MB/s. Standard_L8s_v2-bursts för att uppfylla det här behovet och sedan begär att diskarna ska spridas jämnt ut till P50 diskar.

![Programmet skickar en begäran om 600 MB/s av genomflödet till den virtuella datorn. den virtuella datorn tar emot burst-överföring för att ta begäran och skickar var och en av diskarna en begäran om 200 MB/s, varje disk returnerar 200 MB/s, VM-burst för att returnera 600 MB/s till programmet.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>Burst-virtuell dator med burst-diskar
**Kombination av virtuell dator och disk:** 
- Standard_L8s_v2 
    - Ej cachelagrade MB/s: 160
    - Max burst MB/s: 1 280
- P4 OS-disk
    - Etablerade MB/s: 25
    - Max burst MB/s: 170 
- 2 P4 data diskar 
    - Etablerade MB/s: 25
    - Max burst MB/s: 170 

När den virtuella datorn startar överförs den till en burst-gräns på 1 280 MB/s från operativ system disken och operativ system disken kommer att svara med sin burst-prestanda på 170 MB/s.

![Vid start är den virtuella datorn burst för att skicka en begäran till 1 280 MB/s till operativ system disken, OS-diskens burst för att returnera 1 280 MB/s.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

Efter starten startar du ett program som har en icke-kritisk arbets belastning. Det här programmet kräver 15 MB/s som sprids jämnt över alla diskar.

![Programmet skickar en begäran om 15 MB/s av data flödet till den virtuella datorn, den virtuella datorn tar emot begäran och skickar var och en av diskarna en begäran om 5 MB/s, varje disk returnerar 5 MB/s-svar, den virtuella datorn returnerar 15 MB/s till programmet.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

Programmet måste sedan bearbeta ett batch-jobb som kräver 360 MB/s. Standard_L8s_v2 bursts för att uppfylla det här behovet och begär sedan. Endast 20 MB/s krävs av OS-disken. De återstående 340 MB/s hanteras av data diskarna för burst-P4.

![Programmet skickar en begäran om 360 MB/s av data flödet till den virtuella datorn. den virtuella datorn tar över burst-överföring för att ta begäran och skickar var och en av dess data diskar en begäran om 170 MB/s och 20 MB/s från operativ system disken. varje disk returnerar de begärda MB/s, virtuella datorernas burst för att returnera 360 MB/s till program.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)
