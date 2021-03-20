---
title: Prestandaverktyg för Linux
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du använder Linux-prestandaräknare för att felsöka och lösa vanliga problem när du använder Azure Kubernetes service (AKS).
services: container-service
author: alexeldeib
ms.service: container-service
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: aleldeib
ms.openlocfilehash: 74f65780594c7bc938ed6d59437473c4363e5848
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90982030"
---
# <a name="linux-performance-troubleshooting"></a>Fel sökning av Linux-prestanda

Resurs överbelastning på Linux-datorer är ett vanligt problem och kan identifieras genom en mängd olika symtom. Det här dokumentet innehåller en översikt över de verktyg som är tillgängliga för att diagnosticera sådana problem.

Många av dessa verktyg accepterar ett intervall som du kan använda för att skapa rullande utdata. Det här utdataformatet gör vanligt vis att upptäcka mönster blir mycket enklare. Vid godkänd kommer exempel anrop att innehålla `[interval]` .

Många av dessa verktyg har en omfattande historik och en stor uppsättning konfigurations alternativ. Den här sidan innehåller bara en enkel delmängd av anrop för att markera vanliga problem. Den kanoniska informations källan är alltid referens dokumentationen för varje särskilt verktyg. Dokumentationen är mycket mer grundlig än vad som finns här.

## <a name="guidance"></a>Vägledning

Var systematisk i ditt tillvägagångs sätt för att undersöka prestanda problem. Två vanliga metoder är användning (användning, mättnad, fel) och rött (Rate, Errors, duration). RED används vanligt vis i samband med tjänster för begäran-baserad övervakning. Använd används vanligt vis för att övervaka resurser: för varje resurs på en dator, övervaka användning, mättnad och fel. De fyra huvudsakliga typerna av resurser på en dator är CPU, minne, disk och nätverk. Hög användning, mättnad eller fel frekvens för någon av dessa resurser tyder på ett möjligt problem med systemet. Undersök orsaken till att det finns ett problem: Varför är diskens IO-latens högt? Är diskarna eller SKU: n för virtuella datorer begränsade? Vilka processer skriver till enheterna och vilka filer?

Några exempel på vanliga problem och indikatorer för att diagnostisera dem:
- IOPS-begränsning: Använd iostat för att mäta IOPS per enhet. Se till att ingen enskild disk överskrider gränsen och summan för alla diskar är mindre än gränsen för den virtuella datorn.
- Bandbredds begränsning: Använd iostat som för IOPS, men mätning av Läs-och Skriv data flöde. Se till att både per enhet och mängd data flöde är lägre än bandbredds gränserna.
- SNAT-belastning: Detta kan manifesta som hög aktiva (utgående) anslutningar i SAR. 
- Paket förlust: Detta kan mätas via proxy via antal TCP-omsändningar i förhållande till antal skickade/mottagna. Både `sar` och `netstat` kan visa den här informationen.

## <a name="general"></a>Allmänt

Dessa verktyg är generella och beskriver grundläggande system information. De är en väl utgångs punkt för ytterligare undersökning.

### <a name="uptime"></a>drift tid

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

drift tid ger system drift tid och 1, 5 och 15 minuters belastnings genomsnitt. Dessa belastnings medelvärden motsvarar ungefär trådar som utför arbete eller väntar på att ett avbrotts klart arbete ska slutföras. I absoluta tal kan de vara svåra att tolka, men mäts över tiden som de kan berätta för oss användbar information:

- 1 minuters genomsnitt > 5-minuters genomsnitts belastning ökar.
- 1 minuters genomsnitt < 5-minuters genomsnitts belastning minskar.

drift tiden kan också belysa varför informationen inte är tillgänglig: problemet kan ha lösts manuellt eller efter en omstart innan användaren kan komma åt datorn.

Belastnings medelvärden som är högre än antalet tillgängliga processor trådar kan tyda på ett prestanda problem med en angiven arbets belastning.

### <a name="dmesg"></a>dmesg

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

dmesg dumpar kernel-bufferten. Händelser som OOMKill lägger till en post i kernel-bufferten. Att hitta en OOMKill eller andra resurs överbelastnings meddelanden i dmesg-loggar är en stark indikator på ett problem.

### <a name="top"></a>top

```
$ top
Tasks: 249 total,   1 running, 158 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.2 us,  1.3 sy,  0.0 ni, 95.4 id,  1.0 wa,  0.0 hi,  0.2 si,  0.0 st
KiB Mem : 65949064 total, 43415136 free,  2349328 used, 20184600 buff/cache
KiB Swap:        0 total,        0 free,        0 used. 62739060 avail Mem

   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
116004 root      20   0  144400  41124  27028 S  11.8  0.1 248:45.45 coredns
  4503 root      20   0 1677980 167964  89464 S   5.9  0.3   1326:25 kubelet
     1 root      20   0  120212   6404   4044 S   0.0  0.0  48:20.38 systemd
     ...
```

`top` ger en omfattande översikt över det aktuella system läget. Rubrikerna ger viss användbar sammanställd information:

- tillstånd för uppgifter: köra, vilande, stoppad.
- PROCESSOR användningen, i det här fallet visar inaktiv tid.
- Totalt, ledigt och använt system minne.

`top` kan missa processer för kortvariga processer; alternativ som `htop` och `atop` tillhandahålla liknande gränssnitt när du åtgärdar några av dessa brister.

## <a name="cpu"></a>Processor

Dessa verktyg tillhandahåller processor användnings information. Detta är särskilt användbart med rullande utdata, där mönstren blir enkla att upptäcka.

### <a name="mpstat"></a>mpstat

```
$ mpstat -P ALL [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

19:49:03     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
19:49:04     all    1.01    0.00    0.63    2.14    0.00    0.13    0.00    0.00    0.00   96.11
19:49:04       0    1.01    0.00    1.01   17.17    0.00    0.00    0.00    0.00    0.00   80.81
19:49:04       1    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
19:49:04       2    1.01    0.00    0.00    0.00    0.00    1.01    0.00    0.00    0.00   97.98
19:49:04       3    0.00    0.00    0.99    0.00    0.00    0.99    0.00    0.00    0.00   98.02
19:49:04       4    1.98    0.00    1.98    0.00    0.00    0.00    0.00    0.00    0.00   96.04
19:49:04       5    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       6    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       7    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
```

`mpstat` skriver ut liknande CPU-information överst, men uppdelad efter CPU-tråd. Att se alla kärnor på en gång kan vara användbart för att upptäcka hög obalanserad processor användning, till exempel när ett enda trådat program använder en kärna med 100% belastning. Det här problemet kan vara svårare att upptäcka när det sammanställs över alla processorer i systemet.

### <a name="vmstat"></a>vmstat

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat` innehåller liknande information `mpstat` och `top` räknar upp antalet processer som väntar på CPU (r-kolumnen), minnes statistik och procent andel CPU-tid som tillbringas i varje arbets tillstånd.

## <a name="memory"></a>Minne

Minnet är mycket viktigt och Thankfully enkelt att spåra. Vissa verktyg kan rapportera både processor och minne, t `vmstat` . ex.. Men verktyg som `free` kan ändå vara användbara för snabb fel sökning.

### <a name="free"></a>kostnads fria

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free` visar grundläggande information om totalt minne samt använt och ledigt minne. `vmstat` kan vara mer användbart även för grundläggande minnes analyser på grund av dess förmåga att tillhandahålla rullande utdata.

## <a name="disk"></a>Disk

Dessa verktyg mäter disk-IOPS, Waiting-köer och totalt genomflöde. 

### <a name="iostat"></a>iostat

```
$ iostat -xy [interval] [count]
$ iostat -xy 1 1
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           3.42    0.00    2.92    1.90    0.00   91.76

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
loop0             0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sdb               0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sda               0.00    56.00    0.00   65.00     0.00   504.00    15.51     0.01    3.02    0.00    3.02   0.12   0.80
scd0              0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
```

`iostat` ger djupgående insikter om disk användning. Det här anropet skickas `-x` för utökad statistik, `-y` för att hoppa över medelvärden för det inledande utskriften av utskrifter sedan start, och `1 1` för att ange att vi ska ha 1-sekunders intervall, slutar efter ett block med utdata. 

`iostat` exponerar många användbara statistik:

- `r/s` och `w/s` läsningar per sekund och skrivningar per sekund. Summan av dessa värden är IOPS.
- `rkB/s` och `wkB/s` är kilobyte Läs/skrivna per sekund. Summan av dessa värden är data flöde.
- `await` är den genomsnittliga iowait tiden i millisekunder för begär anden i kö.
- `avgqu-sz` är den genomsnittliga kös Tor lek under det angivna intervallet.

På en virtuell Azure-dator:

- summan av `r/s` och `w/s` för en enskild block enhet får inte överstiga DISKens SKU-gränser.
- summan av `rkB/s` och `wkB/s`  för en enskild block enhet får inte överstiga DISKens SKU-gränser
- summan av `r/s` och `w/s` för alla block enheter får inte överskrida gränserna för VM-SKU: n.
- summan av  `rkB/s` och ' wkB/s för alla block enheter får inte överskrida gränserna för VM SKU: n.

Observera att OS-disken räknas som en hanterad disk av den minsta SKU: n som motsvarar dess kapacitet. Till exempel motsvarar en 1024GB OS-disk en P30-disk. Tillfälliga OS-diskar och temporära diskar har inte enskilda disk gränser. de begränsas endast av de fullständiga gränserna för virtuella datorer.

Värden som inte är noll för await eller avgqu-sz är också effektiva indikatorer för IO-konkurrens.

## <a name="network"></a>Nätverk

Dessa verktyg mäter nätverks statistik som data flöde, överförings problem och användning. Djupare analyser kan exponera detaljerad TCP-statistik om överbelastning och avbrutna paket.

### <a name="sar"></a>SAR

```
$ sar -n DEV [interval]
22:36:57        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
22:36:58      docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azv604be75d832      1.00      9.00      0.06      1.04      0.00      0.00      0.00      0.00
22:36:58       azure0     68.00     79.00     27.79     52.79      0.00      0.00      0.00      0.00
22:36:58    azv4a8e7704a5b    202.00    207.00     37.51     21.86      0.00      0.00      0.00      0.00
22:36:58    azve83c28f6d1c     21.00     30.00     24.12      4.11      0.00      0.00      0.00      0.00
22:36:58         eth0    314.00    321.00     70.87    163.28      0.00      0.00      0.00      0.00
22:36:58    azva3128390bff     12.00     20.00      1.14      2.29      0.00      0.00      0.00      0.00
22:36:58    azvf46c95ddea3     10.00     18.00     31.47      1.36      0.00      0.00      0.00      0.00
22:36:58       enP1s1     74.00    374.00     29.36    166.94      0.00      0.00      0.00      0.00
22:36:58           lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azvdbf16b0b2fc      9.00     19.00      3.36      1.18      0.00      0.00      0.00      0.00
```

`sar` är ett kraftfullt verktyg för en mängd olika analyser. Även om det här exemplet använder sin möjlighet att mäta nätverks statistik, är det lika kraftfullt att mäta processor-och minnes förbrukning. I det här exemplet anropas `sar` med `-n` flagga för att ange `DEV` nyckelordet (nätverks enhet), som visar nätverks data flöde per enhet.

- Summan av `rxKb/s` och `txKb/s` är det totala data flödet för en specifik enhet. När det här värdet överskrider gränsen för det etablerade Azure-NÄTVERKSKORTet kommer arbets belastningarna på datorn att uppleva ökad nätverks fördröjning.
- `%ifutil` mäter användning för en specifik enhet. Eftersom det här värdet närmar sig 100% kommer arbets belastningarna att uppleva ökad nätverks fördröjning.

```
$ sar -n TCP,ETCP [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

22:50:08     active/s passive/s    iseg/s    oseg/s
22:50:09         2.00      0.00     19.00     24.00

22:50:08     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
22:50:09         0.00      0.00      0.00      0.00      0.00

Average:     active/s passive/s    iseg/s    oseg/s
Average:         2.00      0.00     19.00     24.00

Average:     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
Average:         0.00      0.00      0.00      0.00      0.00
```

Detta anrop av `sar` använder `TCP,ETCP` nyckelorden för att undersöka TCP-anslutningar. Den tredje kolumnen i den sista raden, "retrans", är antalet TCP-återsändningar per sekund. Höga värden för det här fältet indikerar en opålitlig nätverks anslutning. I den första och tredje raden betyder "aktiv" en anslutning från den lokala enheten, medan "Remote" indikerar en inkommande anslutning.  Ett vanligt problem i Azure är SNAT-portens belastning, vilket `sar` kan hjälpa dig att identifiera. SNAT-portens överbelastning skulle manifesta som höga "aktiva"-värden, eftersom problemet beror på en hög frekvens av utgående, lokalt initierade TCP-anslutningar.

`sar`Det tar ett intervall att skriva ut och sedan skriva ut slutliga rader med utdata som innehåller genomsnitts resultatet från anropet.

### <a name="netstat"></a>Netstat

```
$ netstat -s
Ip:
    71046295 total packets received
    78 forwarded
    0 incoming packets discarded
    71046066 incoming packets delivered
    83774622 requests sent out
    40 outgoing packets dropped
Icmp:
    103 ICMP messages received
    0 input ICMP message failed.
    ICMP input histogram:
        destination unreachable: 103
    412802 ICMP messages sent
    0 ICMP messages failed
    ICMP output histogram:
        destination unreachable: 412802
IcmpMsg:
        InType3: 103
        OutType3: 412802
Tcp:
    11487089 active connections openings
    592 passive connection openings
    1137 failed connection attempts
    404 connection resets received
    17 connections established
    70880911 segments received
    95242567 segments send out
    176658 segments retransmited
    3 bad segments received.
    163295 resets sent
Udp:
    164968 packets received
    84 packets to unknown port received.
    0 packet receive errors
    165082 packets sent
UdpLite:
TcpExt:
    5 resets received for embryonic SYN_RECV sockets
    1670559 TCP sockets finished time wait in fast timer
    95 packets rejects in established connections because of timestamp
    756870 delayed acks sent
    2236 delayed acks further delayed because of locked socket
    Quick ack mode was activated 479 times
    11983969 packet headers predicted
    25061447 acknowledgments not containing data payload received
    5596263 predicted acknowledgments
    19 times recovered from packet loss by selective acknowledgements
    Detected reordering 114 times using SACK
    Detected reordering 4 times using time stamp
    5 congestion windows fully recovered without slow start
    1 congestion windows partially recovered using Hoe heuristic
    5 congestion windows recovered without slow start by DSACK
    111 congestion windows recovered without slow start after partial ack
    73 fast retransmits
    26 retransmits in slow start
    311 other TCP timeouts
    TCPLossProbes: 198845
    TCPLossProbeRecovery: 147
    480 DSACKs sent for old packets
    175310 DSACKs received
    316 connections reset due to unexpected data
    272 connections reset due to early user close
    5 connections aborted due to timeout
    TCPDSACKIgnoredNoUndo: 8498
    TCPSpuriousRTOs: 1
    TCPSackShifted: 3
    TCPSackMerged: 9
    TCPSackShiftFallback: 177
    IPReversePathFilter: 4
    TCPRcvCoalesce: 1501457
    TCPOFOQueue: 9898
    TCPChallengeACK: 342
    TCPSYNChallenge: 3
    TCPSpuriousRtxHostQueues: 17
    TCPAutoCorking: 2315642
    TCPFromZeroWindowAdv: 483
    TCPToZeroWindowAdv: 483
    TCPWantZeroWindowAdv: 115
    TCPSynRetrans: 885
    TCPOrigDataSent: 51140171
    TCPHystartTrainDetect: 349
    TCPHystartTrainCwnd: 7045
    TCPHystartDelayDetect: 26
    TCPHystartDelayCwnd: 862
    TCPACKSkippedPAWS: 3
    TCPACKSkippedSeq: 4
    TCPKeepAlive: 62517
IpExt:
    InOctets: 36416951539
    OutOctets: 41520580596
    InNoECTPkts: 86631440
    InECT0Pkts: 14
```

`netstat` kan Introspect en mängd olika nätverks statistik, som här anropas med sammanfattning av utdata. Det finns många användbara fält här beroende på problemet. Ett användbart fält i avsnittet TCP är "misslyckade anslutnings försök". Detta kan vara en indikation på antalet SNAT-portar eller andra problem som gör utgående anslutningar. En hög hastighet för återsända segment (även under avsnittet TCP) kan tyda på problem med paket leverans. 
