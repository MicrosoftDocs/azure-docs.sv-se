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
ms.openlocfilehash: 4162fe12ff54f16cd5f982f6a576905227c9a107
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107821042"
---
## <a name="disk-level-bursting"></a>Toppar på disknivå

### <a name="on-demand-bursting-preview"></a>Bursting på begäran (förhandsversion)

Diskar som använder burst-modellen på begäran av disk bursting kan burst-brytas bortom ursprungliga etablerade mål, så ofta som det behövs av arbetsbelastningen, upp till max burst-målet. På en 1-TiB P30-disk är till exempel den etablerade IOPS 5 000 IOPS. När disk bursting har aktiverats på den här disken kan dina arbetsbelastningar utfärda I/O till den här disken upp till maximal burst-prestanda på 30 000 IOPS och 1 000 MBps.

Om du förväntar dig att dina arbetsbelastningar ofta körs utanför det etablerade perf-målet är disk bursting inte kostnadseffektivt. I det här fallet rekommenderar vi att du ändrar diskens prestandanivå till en högre [nivå](../articles/virtual-machines/disks-performance-tiers.md) i stället, för bättre baslinjeprestanda. Granska din faktureringsinformation och utvärdera den mot trafikmönstret för dina arbetsbelastningar.

Innan du aktiverar burst-åtgärder på begäran bör du förstå följande:

[!INCLUDE [managed-disk-bursting-regions-limitations](managed-disk-bursting-regions-limitations.md)]

#### <a name="regional-availability"></a>Regional tillgänglighet

[!INCLUDE [managed-disk-bursting-availability](managed-disk-bursting-availability.md)]

#### <a name="billing"></a>Fakturering

Diskar som använder burst-modellen på begäran debiteras en fast avgift för burst-åtgärder per timme och transaktionskostnader tillämpas på alla burst-transaktioner utöver det etablerade målet. Transaktionskostnader debiteras med hjälp av modellen betala per användning, baserat på icke-frånkopplade disk-I/O:er, inklusive både läsningar och skrivningar som överskrider etablerade mål. Följande är ett exempel på disktrafikmönster under en faktureringstid:

Diskkonfiguration: Premium SSD – 1 TiB (P30), disk bursting aktiverat.

- 00:00:00 – 00:10:00 Disk-IOPS under etablerat mål på 5 000 IOPS 
- 00:10:01 – 00:10:10 Programmet utfärdade ett batchjobb som gjorde att diskens IOPS burst-fel var 6 000 IOPS i 10 sekunder 
- 00:10:11 – 00:59:00 Disk-IOPS under etablerat mål på 5 000 IOPS 
- 00:59:01 – 01:00:00 Ett annat batchjobb har utfärdats, vilket gör att diskens IOPS burst-fel är 7 000 IOPS i 60 sekunder 

Under den här faktureringstimmarna består kostnaden för bursting av två avgifter:

Den första avgiften är den fasta avgiften för burst-$X (bestäms av din region). Den här fasta avgiften debiteras alltid på disken utan hänsyn till anslutningstatusen tills den inaktiveras. 

Det andra är kostnaden för burst-transaktioner. Disk bursting inträffade i två tidpunkter. Från 00:10:01 – 00:10:10 är den ackumulerade burst-transaktionen (6 000 –5 000) X 10 = 10 000. Från 00:59:01 – 01:00:00 är den ackumulerade burst-transaktionen (7 000 –5 000) X 60 = 120 000. Det totala antalet burst-transaktioner är 10 000 + 120 000 = 130 000. Kostnaden för burst-transaktioner debiteras $Y baserat på 13 enheter av 10 000 transaktioner (baserat på regionala priser).

Med det är den totala kostnaden för disk bursting för den här faktureringstimmarna lika med $X + $Y. Samma beräkning gäller för bursting över etablerat mål för MBps. Vi översätter överstorleken på MB till transaktioner med en I/S-storlek på 256 kB. Om disktrafiken överskrider både det etablerade IOPS- och MBps-målet kan du referera till exemplet nedan för att beräkna burst-transaktionerna. 

Diskkonfiguration: Premium SSD – 1 TB (P30), disk bursting aktiverat.

- 00:00:01 – 00:00:05 Programmet utfärdade ett batchjobb som gjorde att diskens IOPS burst-fel var 10 000 IOPS och 300 MBps i fem sekunder.
- 00:00:06 – 00:00:10 Programmet utfärdade ett återställningsjobb som gjorde att diskens IOPS burst-fel var 6 000 IOPS och 600 MBps i fem sekunder.

Burst-transaktionen räknas som det maximala antalet transaktioner från antingen IOPS- eller MBps-burstning. Från 00:00:01 – 00:00:05 är den ackumulerade burst-transaktionen max ((10 000 – 5 000), (300-200) * 1024 /256)) * 5 = 25 000 transaktioner. Från 00:00:06 – 00:00:10 är den ackumulerade burst-transaktionen max ((6 000 – 5 000), (600–200) * 1024 /256)) * 5 = 8 000 transaktioner. Dessutom inkluderar du den fasta avgiften för burst-aktivering för att få den totala kostnaden för att aktivera disktoppar på begäran. 

Du kan gå till sidan [Managed Disks för information](https://azure.microsoft.com/pricing/details/managed-disks/) om priser och använda [Priskalkylatorn](https://azure.microsoft.com/pricing/calculator/?service=storage) för Azure för att göra utvärderingen för din arbetsbelastning. 


Om du vill aktivera bursting på begäran kan [du se Aktivera bursting på begäran.](../articles/virtual-machines/disks-enable-bursting.md)

### <a name="credit-based-bursting"></a>Kreditbaserad bursting

Kreditbaserad bursting är tillgänglig för diskstorlekarna P20 och mindre i alla regioner i Offentliga Azure-, Government- och China Clouds. Som standard är disk bursting aktiverat på alla nya och befintliga distributioner av diskstorlekar som stöds. Toppar på VM-nivå använder endast kreditbaserad bursting.

## <a name="virtual-machine-level-bursting"></a>Bursting på virtuell datornivå

Bursting på VM-nivå använder bara den kreditbaserade modellen för burst-ning. Den är aktiverad som standard för alla virtuella datorer som stöder det.

Bursting på VM-nivå är aktiverat i alla regioner i det offentliga Azure-molnet på följande storlekar som stöds: 
- [Lsv2-serien](../articles/virtual-machines/lsv2-series.md)
- [Dv3- och Dsv3-serien](../articles/virtual-machines/dv3-dsv3-series.md)
- [Ev3- och Esv3-serien](../articles/virtual-machines/ev3-esv3-series.md)

## <a name="bursting-flow"></a>Burst-flöde

Burst-kreditsystemet gäller på samma sätt på både VM-nivå och disknivå. Din resurs, antingen en virtuell dator eller disk, börjar med fullständigt lagerlade krediter i en egen burst-bucket. Med dessa krediter kan du burst-värdet i upp till 30 minuter med maximal burst-hastighet. Du ackumulerar krediter när resursens IOPS eller MB/s används under resursens prestandamål. Om din resurs har ackumulerat burst-krediter och din arbetsbelastning behöver extra prestanda kan din resurs använda dessa krediter för att gå över dess prestandagränser och öka dess prestanda för att uppfylla arbetsbelastningskraven.

![Bucketdiagram med bursting.](media/managed-disks-bursting/bucket-diagram.jpg)

Det är upp till dig hur du spenderar dina tillgängliga krediter. Du kan använda dina 30 minuters burst-krediter i följd eller sporadiskt under dagen. När resurser distribueras får de en fullständig allokering av krediter. När dessa tar slut tar det mindre än en dag att fylla på lager. Krediter kan spenderas efter eget gottfinnande. Burst-bucketen behöver inte vara full för att resurserna ska kunna burst-användas. Burst-ackumulering varierar beroende på varje resurs, eftersom den baseras på oanvändA IOPS och MB/s under deras prestandamål. Resurser med högre baslinjeprestanda kan ackumulera sina burst-krediter snabbare än resurser med lägre baslinjeprestanda. Till exempel påförs en P1-disk i tomgång 120 IOPS per sekund, medan en tomgång P20-disk påförs 2 300 IOPS per sekund.

## <a name="bursting-states"></a>Burst-tillstånd
Det finns tre tillstånd som din resurs kan vara i med burst-funktionen aktiverad:
- **Accruing** – Resursens I/O-trafik använder mindre än prestandamålet. Ackumulerade burst-krediter för IOPS och MB/s görs separat från varandra. Din resurs kan ha IOPS-krediter och MB/s-krediter eller tvärtom.
- **Bursting** – resursens trafik använder mer än prestandamålet. Burst-trafiken förbrukar krediter från IOPS eller bandbredd oberoende av varandra.
- **Konstant** – Resursens trafik ligger exakt vid prestandamålet.

## <a name="bursting-examples"></a>Burst-exempel

I följande exempel visas hur bursting fungerar med olika kombinationer av virtuella datorer och diskar. För att göra exemplen enkla att följa fokuserar vi på MB/s, men samma logik tillämpas oberoende av varandra på IOPS.

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>Virtuella datorer som inte kan burst-burst-bearbetas med burst-diskar
**Kombination av virtuell dator och disk:** 
- Standard_D8as_v4 
    - Frikopplad MB/s: 192
- P4 OS-disk
    - Etablerat MB/s: 25
    - Maximal burst MB/s: 170 
- 2 P10-datadiskar 
    - Etablerade MB/s: 100
    - Maximal burst MB/s: 170

 När den virtuella datorn startas hämtas data från OS-disken. Eftersom OS-disken är en del av en virtuell dator som startar, kommer OS-disken att vara full av burst-krediter. Med dessa krediter kan OS-disken burst-starta med 170 MB/s sekund.

![Den virtuella datorn skickar en begäran om 192 MB/s dataflöde till OS-disken, OS-disken svarar med 170 MB/s data.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-startup.jpg)

När starten är klar körs ett program på den virtuella datorn och har en icke-kritisk arbetsbelastning. Den här arbetsbelastningen kräver 15 MB/S som sprids jämnt över alla diskar.

![Programmet skickar en begäran på 15 MB/s av dataflödet till den virtuella datorn, den virtuella datorn tar begäran och skickar en begäran på 5 MB/s, varje disk returnerar 5 MB/s, vm returnerar 15 MB/s till programmet.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-idling.jpg)

Sedan måste programmet bearbeta ett batchjobb som kräver 192 MB/s. 2 MB/s används av OS-disken och resten är jämnt uppdelade mellan datadiskarna.

![Programmet skickar en begäran om 192 MB/s dataflöde till den virtuella datorn, den virtuella datorn tar begäran och skickar merparten av sin begäran till datadiskarna (95 MB/s vardera) och 2 MB/s till OS-disken, datadiskarna burst-överför för att möta efterfrågan och alla diskar returnerar det begärda dataflödet till den virtuella datorn, som returnerar det till programmet.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>Burstable virtual machine with non-burstable disks (Burstable virtual machine with non-burstable disks)
**Kombination av virtuell dator och disk:** 
- Standard_L8s_v2 
    - Frikopplad MB/s: 160
    - Maximal burst MB/s: 1 280
- P50 OS-disk
    - Etablerade MB/s: 250 
- 2 P10-datadiskar 
    - Etablerat MB/s: 250

 Efter den första starten körs ett program på den virtuella datorn och har en icke-kritisk arbetsbelastning. Den här arbetsbelastningen kräver 30 MB/s som sprids jämnt över alla diskar.
![Programmet skickar en begäran om 30 MB/s dataflöde till den virtuella datorn, den virtuella datorn tar begäran och skickar var och en av diskarna en begäran på 10 MB/s, varje disk returnerar 10 MB/s, vm returnerar 30 MB/s till programmet.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

Sedan måste programmet bearbeta ett batchjobb som kräver 600 MB/s. Den Standard_L8s_v2 ökar för att möta denna efterfrågan och sedan sprids begäranden till diskarna jämnt ut till P50-diskar.

![Programmet skickar en begäran om 600 MB/s dataflöde till den virtuella datorn, den virtuella datorn tar burst-fel för att ta begäran och skickar var och en av diskarna en begäran om 200 MB/s, varje disk returnerar 200 MB/s, VM-burstar för att returnera 600 MB/s till programmet.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>Burstable virtual machine with burstable disks (Burstable virtual machine with burstable disks)
**Kombination av virtuell dator och disk:** 
- Standard_L8s_v2 
    - Frikopplad MB/s: 160
    - Maximal burst MB/s: 1 280
- P4 OS-disk
    - Etablerat MB/s: 25
    - Maximal burst MB/s: 170 
- 2 P4-datadiskar 
    - Etablerat MB/s: 25
    - Maximal burst MB/s: 170 

När den virtuella datorn startar kommer den att burst-burst begära sin burst-gräns på 1 280 MB/s från OS-disken och OS-disken svarar med en burst-prestanda på 170 MB/s.

![Vid start burstar den virtuella datorn för att skicka en begäran på 1 280 MB/s till OS-disken, OS-disken bursts för att returnera 1 280 MB/s.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

Efter starten startar du ett program som har en icke-kritisk arbetsbelastning. Det här programmet kräver 15 MB/s som sprids jämnt över alla diskar.

![Programmet skickar en begäran på 15 MB/s dataflöde till den virtuella datorn, den virtuella datorn tar begäran och skickar en begäran på 5 MB/s, varje disk returnerar 5 MB/s svar, VM returnerar 15 MB/s till programmet.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

Sedan måste programmet bearbeta ett batchjobb som kräver 360 MB/s. Den Standard_L8s_v2 för att möta denna efterfrågan och sedan begäranden. Os-disken behöver bara 20 MB/s. Återstående 340 MB/s hanteras av de burstande P4-datadiskarna.

![Programmet skickar en begäran om 360 MB/s dataflöde till den virtuella datorn, den virtuella datorn tar burst-fel för att ta begäran och skickar var och en av sina datadiskar en begäran på 170 MB/s och 20 MB/s från OS-disken. Varje disk returnerar begärda MB/s, VM-burstar för att returnera 360 MB/s till programmet.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)
