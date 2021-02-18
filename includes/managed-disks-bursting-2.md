---
title: ta med fil
description: ta med fil
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 801f0f03b49d20c84a4531bd0daad7630a0ed01d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585105"
---
## <a name="common-scenarios"></a>Vanliga scenarier
Följande scenarier kan dra nytta av burst-överföring:
- Att **förbättra start tiderna** – med burst-överföring kommer instansen att starta med en betydligt snabbare hastighet. Till exempel är standard operativ system disken för Premium-aktiverade virtuella datorer P4-disken, som är en etablerad prestanda på upp till 120 IOPS och 25 MB/s. Med bursting kan P4 gå upp till 3500 IOPS och 170 MB/s och tillåta en start tid att accelerera med 6X.
- **Hantera batchjobb** – vissa program arbets belastningar är cykliska och kräver en bas linje prestanda för det mesta av tiden och kräver högre prestanda under en kort tids period. Ett exempel på detta är ett redovisnings program som bearbetar transaktioner dagligen som kräver en liten mängd disk trafik. I slutet av månaden avstämr rapporter som behöver mycket större disk trafik.
- **Beredskap för trafik toppar** – webb servrar och deras program kan uppleva trafik toppar när som helst. Om webb servern backas upp av virtuella datorer eller diskar med burst-överföring är servrarna bättre utrustade för att hantera trafik toppar. 

## <a name="bursting-flow"></a>Flödes flöde
Överförings kredit systemet gäller på samma sätt både på den virtuella datorns nivå och på disk nivå. Din resurs, antingen en virtuell dator eller en disk, kommer att börja med helt aktie krediter. Med dessa krediter kan du överföra i 30 minuter till högsta överföringshastighet. De mellanliggande krediterna ackumuleras när din resurs körs under lagrings gränserna för disk utrymme. För alla IOPS och MB/s som resursen använder under den prestanda gräns som du börjar samla in krediter. Om din resurs har upplupna krediter som ska användas för burst och din arbets belastning behöver extra prestanda, kan din resurs använda dessa krediter för att gå över din prestanda gräns för att ge den den disk-IO-prestanda som krävs för att uppfylla behovet.



![Burst-Bucket-diagram](media/managed-disks-bursting/bucket-diagram.jpg)

Det är helt upp till dig hur du vill använda de 30 minuters burst-överföringarna. Du kan använda den i 30 minuter i följd eller sporadiskt under dagen. När produkten har distribuerats kommer den att bli klar med fullständiga krediter och när den tar slut i krediten tar det mindre än en dag för att helt få ut hela krediten igen. Du kan ackumulera och spendera sina mellanliggande krediter och den 30-minuters Bucket behöver inte vara full till burst. En sak att notera om burst-ackumulering är att den skiljer sig åt för varje resurs eftersom den baseras på oanvända IOPS och MB/s under deras prestanda belopp. Det innebär att högre bas linje prestanda produkter kan periodisera sina burst-mängder snabbare än att sänka bas linje produkter. Till exempel kommer en tom disk tom gång utan aktivitet att Periodisera 120 IOPS per sekund, medan en P20 disk påbörjar 2 300 IOPS per sekund medan Tom gång utan aktivitet.

## <a name="bursting-states"></a>Burst-tillstånd
Det finns tre tillstånd som din resurs kan vara aktive rad med burst-aktivering:
- **Periodisera** – RESURSens IO-trafik använder mindre än prestanda målet. Ackumulering av burst-krediter för IOPS och MB/s görs åtskilda från varandra. Din resurs kan skjuta upp IOPS-krediter och utgifter i MB/s-krediter eller vice versa.
- **Bursting** – resursens trafik använder mer än prestanda målet. Burst-trafiken förbrukar oberoende krediter från IOPS eller bandbredd.
- **Konstant** – resursens trafik är exakt i prestanda målet.

## <a name="examples-of-bursting"></a>Exempel på bursting

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

![VM skickar en begäran om 192 MB/s av data flödet till operativ system disken, OS-disken svarar med 170 MB/s-data.](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-startup.jpg)

När starten är klar körs ett program på den virtuella datorn och har en icke-kritisk arbets belastning. Den här arbets belastningen kräver 15 MB/S som sprids jämnt över alla diskar.

![Programmet skickar en begäran om 15 MB/s av genomflödet till den virtuella datorn, den virtuella datorn tar emot begäran och skickar var och en av diskarna en begäran om 5 MB/s. varje disk returnerar 5 MB/s, och virtuella datorer returnerar 15 MB/s till programmet.](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-idling.jpg)

Programmet måste sedan bearbeta ett batch-jobb som kräver 192 MB/s. 2 MB/s används av OS-disken och resten delas jämnt mellan data diskarna.

![Programmet skickar en begäran om 192 MB/s av data flödet till den virtuella datorn, den virtuella datorn tar emot begäran och skickar en begäran till data diskarna (95 MB/s varje) och 2 MB/s till OS-disken, data diskarna som är burst för att uppfylla efter frågan och alla diskar returnerar det begärda data flödet till den virtuella datorn, vilket returnerar det till programmet.](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-bursting.jpg)

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

![Programmet skickar en begäran om 15 MB/s av genomflödet till den virtuella datorn, den virtuella datorn tar emot begäran och skickar var och en av diskarna en begäran om 5 MB/s. varje disk returnerar 5 MB/s, och virtuella datorer returnerar 15 MB/s till programmet.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

Programmet måste sedan bearbeta ett batch-jobb som kräver 360 MB/s. Standard_L8s_v2 bursts för att uppfylla det här behovet och begär sedan. Endast 20 MB/s krävs av OS-disken. De återstående 340 MB/s hanteras av data diskarna för burst-P4.

![Programmet skickar en begäran om 360 MB/s av data flödet till den virtuella datorn. den virtuella datorn tar över burst-överföring för att ta begäran och skickar var och en av dess data diskar en begäran om 170 MB/s och 20 MB/s från operativ system disken. varje disk returnerar de begärda MB/s, virtuella datorernas burst för att returnera 360 MB/s till program.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)