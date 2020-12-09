---
title: Burst-Virtual Machines i B-serien – Azure
description: Beskriver B-serien med burst-storlekar för virtuella Azure-datorer.
services: virtual-machines
ms.subservice: sizes
author: styli365
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: sttsinar
ms.openlocfilehash: ea812df825288eae3822cce01b24ebc82534c541
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928825"
---
# <a name="b-series-burstable-virtual-machine-sizes"></a>Storlekar för virtuella datorer i B-serien

De virtuella datorerna i B-serien är idealiska för arbets belastningar som inte behöver PROCESSORns fulla prestanda kontinuerligt, t. ex. webb servrar, bevis på begrepp, små databaser och utvecklings Bygg miljöer. Dessa arbets belastningar har vanligt vis höga prestanda krav. B-serien ger dig möjlighet att köpa en VM-storlek med bas linje prestanda som kan bygga upp krediter när den använder mindre än dess bas linje. När den virtuella datorn har ackumulerade krediter kan den virtuella datorn överföras över bas linjen med upp till 100% av vCPU när programmet kräver högre CPU-prestanda.

B-serien levereras i följande VM-storlekar:

[Azure Compute Unit (ACU)](./acu.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): varierar *<br>
[Premium Storage](premium-storage-performance.md): stöds<br>
[Premium Storage caching](premium-storage-performance.md): stöds inte<br>
[Direktmigrering](maintenance-and-updates.md): stöds<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds<br>
[Stöd för VM-generering](generation-2.md): generation 1 och 2<br>
<br>
* Virtuella datorer i B-serien är burst-baserade och därför kan ACU nummer variera beroende på arbets belastningar och kärn användning.

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Bas processor prestanda för virtuell dator | Högsta CPU-prestanda för virtuell dator | Inledande krediter | Krediter i bank/timme | Högsta antal krediter | Maximalt antal datadiskar | Maximalt cacheminne för cachelagrad och temporär lagring: IOPS/Mbit/s | Maximalt antal cachelagrade diskar: IOPS/MBps | Maximalt antal nätverkskort |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Standard_B1ls<sup>1</sup> | 1  | 0,5 | 4   | 5 %   | 100 %  | 30  | 3   | 72   | 2  | 200/10    | 160/10    | 2  |
| Standard_B1s              | 1  | 1   | 4   | 10 %  | 100 %  | 30  | 6   | 144  | 2  | 400/10    | 320/10    | 2  |
| Standard_B1ms             | 1  | 2   | 4   | 20%  | 100 %  | 30  | 12  | 288  | 2  | 800/10    | 640/10    | 2  |
| Standard_B2s              | 2  | 4   | 8   | 40 %  | 200 %  | 60  | 24  | 576  | 4  | 1600/15   | 1280/15   | 3  |
| Standard_B2ms             | 2  | 8   | 16  | 60 %  | 200 %  | 60  | 36  | 864  | 4  | 2400/22.5 | 1920/22.5 | 3  |
| Standard_B4ms             | 4  | 16  | 32  | 90 %  | 400%  | 120 | 54  | 1296 | 8  | 3600/35   | 2880/35   | 4  |
| Standard_B8ms             | 8  | 32  | 64  | 135% | 800%  | 240 | 81  | 1944 | 16 | 4320/50   | 4320/50   | 4  |
| Standard_B12ms            | 12 | 48  | 96  | 202% | 1200% | 360 | 121 | 2909 | 16 | 6480/75   | 4320/50   | 6  |
| Standard_B16ms            | 16 | 64  | 128 | 270% | 1600% | 480 | 162 | 3888 | 32 | 8640/100  | 4320/50   | 8  |
| Standard_B20ms            | 20 | 80  | 160 | 337% | 2000 % | 600 | 203 | 4860 | 32 | 10800/125 | 4320/50   | 8  |

<sup>1</sup> B1ls stöds bara på Linux

## <a name="workload-example"></a>Exempel på arbets belastning

Överväg ett check-in/ut-program för Office. Programmet behöver CPU-burst under kontors tid, men inte mycket data behandlings kraft under några timmar. I det här exemplet kräver arbets belastningen en virtuell 16vCPU-dator med 64GiB RAM-minne för att fungera effektivt.

Tabellen visar trafik data för varje timme och diagrammet är en visuell representation av trafiken.

B16 egenskaper:

Max CPU-prestanda: 16vCPU * 100% = 1600%

Bas linje: 270%

![Diagram över Tim trafik data](./media/b-series-burstable/office-workload.png)

| Scenario | Tid | CPU-användning (%) | Ackumulerade krediter<sup>1</sup> | Tillgängliga krediter |
| --- | --- | --- | --- | --- |
| B16ms-distribution | Distribution | Distribution  | 480 (inledande krediter) | 480 |
| Ingen trafik | 0:00 | 0 | 162 | 642 |
| Ingen trafik | 1:00 | 0 | 162 | 804 |
| Ingen trafik | 2:00 | 0 | 162 | 966 |
| Ingen trafik | 3:00 | 0 | 162 | 1128 |
| Ingen trafik | 4:00 | 0 | 162 | 1290 |
| Ingen trafik | 5:00 | 0 | 162 | 1452 |
| Låg trafik | 6:00 | 270 | 0 | 1452 |
| Anställda kommer till Office (appen behöver 80% vCPU) | 7:00 | 1280 | – 606 | 846 |
| Anställda fortsätter att komma till Office (appen behöver 80% vCPU) | 8:00 | 1280 | – 606 | 240 |
| Låg trafik | 9:00 | 270 | 0 | 240 |
| Låg trafik | 10:00 | 100 | 102 | 342 |
| Låg trafik | 11:00 | 50 | 132 | 474 |
| Låg trafik | 12:00 | 100 | 102 | 576 |
| Låg trafik | 13:00 | 100 | 102 | 678 |
| Låg trafik | 14:00 | 50 | 132 | 810 |
| Låg trafik | 15:00 | 100 | 102 | 912 |
| Låg trafik | 16:00 | 100 | 102 | 1014 |
| Anställda som checkar ut (appen behöver 100% vCPU) | 17:00 | 1600 | – 798 | 216 |
| Låg trafik | 18:00 | 270 | 0 | 216 |
| Låg trafik | 19:00 | 270 | 0 | 216 |
| Låg trafik | 20:00 | 50 | 132 | 348 |
| Låg trafik | 21:00 | 50 | 132 | 480 |
| Ingen trafik | 22:00 | 0 | 162 | 642 |
| Ingen trafik | 23:00 | 0 | 162 | 804 |

<sup>1</sup> krediter ackumulerade/krediter som används i en timme motsvarar: `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes` .  

För en D16s_v3 som har 16 virtuella processorer-och 64-GiB av minne är Tim priset $0,936 per timme (månatlig $673,92) och för B16ms med 16 virtuella processorer och 64 GiB-minne är priset $0,794 per timme (månatlig $547,86). <b> Detta resulterar i 15% besparingar!</b>

## <a name="q--a"></a>Frågor och svar

### <a name="q-what-happens-when-my-credits-run-out"></a>F: Vad händer när mina krediter körs?
**A**: när krediten är slut återgår den virtuella datorn till bas linje prestandan.

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>F: Hur får du 135% bas linje prestanda från en virtuell dator?

S **: 135**% delas mellan de 8 vCPU som utgör storleken på den virtuella datorn. Om ditt program exempelvis använder 4 av de 8 kärnor som arbetar vid batchbearbetning och var och en av dessa 4 vCPU körs med 30% användning, blir den totala mängden CPU-prestanda för virtuell dator lika med 120%.  Det innebär att din virtuella dator skulle bygga kredit tid baserat på 15% delta från bas linje prestanda.  Men det innebär också att när du har krediter som är tillgängliga för samma virtuella dator kan du använda 100% av alla 8 vCPU, vilket ger den högsta processor prestanda på 800%.

### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>F: Hur kan jag övervaka mitt kredit belopp och förbrukning?

**A**: **kredit** måttet gör att du kan se hur många krediter din virtuella dator har varit bank och **ConsumedCredit** -måttet visar hur många CPU-krediter din virtuella dator har förbrukat från banken.    Du kan visa dessa mått från fönstret mått i portalen eller via programmering via Azure Monitor API: er.

Mer information om hur du kommer åt mått data för Azure finns i [Översikt över mått i Microsoft Azure](../azure-monitor/platform/data-platform.md).

### <a name="q-how-are-credits-accumulated-and-consumed"></a>F: hur summeras och förbrukas krediter?

**A**: de virtuella datorerna för VM-insamlingen och förbruknings priserna är inställda så att en virtuell dator som körs på exakt dess bas prestanda nivå inte har något netto kapital eller en konsumtion av de olika krediterna.  En virtuell dator kommer att ha en netto ökning av krediterna när den körs under dess bas prestanda nivå och har en netto minskning av krediterna när den virtuella datorn använder processorn mer än dess grundläggande prestanda nivå.

**Exempel**: jag distribuerar en virtuell dator med B1ms-storlek för mitt lilla tid-och närvaro databas program. Den här storleken gör att mitt program kan använda upp till 20% av en vCPU som min bas linje, vilket är 0,2 krediter per minut som jag kan använda eller bank.

Mitt program är upptaget i början och slutet av mina anställdas arbets dag, mellan 7:00-9:00 och 4:00 – 6:12:00. Under de andra 20 timmarna av dagen är mitt program normalt i inaktivt, endast med 10% av vCPU. Vid perioder med låg belastning får jag 0,2 krediter per minut men får bara förbruka 0. l-krediter per minut, så att min virtuella dator bank 0,1 x 60 = 6 krediter per timme.  För de 20 timmarna som jag är låg, är jag bank 120-krediter.  

Under hög belastnings tid är mitt program genomsnitt 60% vCPU användning, jag får fortfarande 0,2-krediter per minut, men jag använder 0,6-krediter per minut för en netto kostnad av 0,4-krediter per minut eller 0,4 x 60 = 24 krediter per timme. Jag har 4 timmar per dag med högsta användning, så det kostar 4 x 24 = 96 krediter för min högsta användning.

Om jag tar de 120 krediterna som jag har fått från hög belastning och subtraherar de 96-krediter som jag använde för min belastnings tid, bankerar vi ytterligare 24 krediter per dag som jag kan använda för andra aktiviteter.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>F: Hur kan jag beräkna krediter som har samlats in och använts?

**A**: du kan använda följande formel:

(Grundläggande processor prestanda för VM-CPU-användning)/100 = kredit bank eller användning per minut

till exempel är din bas linje 20% och om du använder 10% av processorn som du ackumulerar (20%-10%)/100 = 0,1 kredit per minut.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>F: stöder B-serien Premium Storage data diskar?

S **: Ja**, alla storlekar i B-serien har stöd för Premium Storage data diskar.

### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>F: Varför är min återstående kredit inställd på 0 efter en omdistribution eller en stoppa/starta?

**A** : när en virtuell dator distribueras om och den virtuella datorn flyttas till en annan nod går den ackumulerade krediten förlorad. Om den virtuella datorn stoppas/startas, men förblir kvar på samma nod, behåller den virtuella datorn den ackumulerade krediten. När den virtuella datorn startar på nytt på en nod får den en inledande kredit, för Standard_B8ms den är 240.

### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>F: Vad händer om jag distribuerar en OS-avbildning som inte stöds på B1ls?

**A** : B1ls stöder bara Linux-avbildningar och om du distribuerar en annan OS-avbildning kanske du inte får den bästa kund upplevelsen.

## <a name="other-sizes-and-information"></a>Andra storlekar och information

- [Generell användning](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)

Pris kalkylator: [pris kalkylator](https://azure.microsoft.com/pricing/calculator/)

Mer information om disk typer: [disk typer](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
