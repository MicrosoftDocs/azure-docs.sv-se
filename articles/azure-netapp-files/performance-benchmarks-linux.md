---
title: Azure NetApp Files prestanda mått för Linux | Microsoft Docs
description: Beskriver prestanda mått Azure NetApp Files levererar för Linux.
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
ms.date: 04/29/2020
ms.author: b-juche
ms.openlocfilehash: b763a734866dd5fed5bf0500d4d52b9324c92a79
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "82614595"
---
# <a name="azure-netapp-files-performance-benchmarks-for-linux"></a>Azure NetApp Files prestanda mått för Linux

Den här artikeln beskriver prestanda mått Azure NetApp Files levererar för Linux.

## <a name="linux-scale-out"></a>Linux-skalbarhet

I det här avsnittet beskrivs prestanda mått för arbets flöde för Linux-arbetsbelastning och belastnings-IOPS.

### <a name="linux-workload-throughput"></a>Data flöde för Linux-arbetsbelastning  

Diagrammet nedan visar en sekventiell arbets belastning på 64-kibibyte (KiB) och en 1-TiB-arbetsuppsättning. Det visar att en enda Azure NetApp Files volym kan hantera mellan ~ 1 600 MiB/s rent sekventiella skrivningar och ~ 4 500 MiB/s rent sekventiella läsningar.  

Grafen illustrerar minskningar i 10% i taget, från ren läsning till ren skrivning. Det visar vad du kan förväntar dig när du använder varierande Läs/skriv-förhållande (100%: 0%, 90%: 10%, 80%: 20% och så vidare).

![Data flöde för Linux-arbetsbelastning](../media/azure-netapp-files/performance-benchmarks-linux-workload-throughput.png)  

### <a name="linux-workload-iops"></a>Linux-arbetsbelastning IOPS  

Följande diagram representerar en slumpmässig arbets belastning på 4-kibibyte (KiB) och en 1-TiB-arbetsuppsättning. Grafen visar att en Azure NetApp Files volym kan hantera mellan ~ 130 000 rena slumpmässiga skrivningar och ~ 460 000 rena slumpmässiga läsningar.  

I den här grafen visas minskningar i 10% i taget, från ren läsning till ren skrivning. Det visar vad du kan förväntar dig när du använder varierande Läs/skriv-förhållande (100%: 0%, 90%: 10%, 80%: 20% och så vidare).

![Linux-arbetsbelastning IOPS](../media/azure-netapp-files/performance-benchmarks-linux-workload-iops.png)  

## <a name="linux-scale-up"></a>Linux-skalbarhet  

Linux 5,3-kernel möjliggör skalbart nätverk för enkel klient för NFS- `nconnect` . Graferna i det här avsnittet visar verifierings test resultaten för monterings alternativet på klient sidan med NFSv3. Funktionen är tillgänglig på SUSE (från och med SLES12SP4) och Ubuntu (från och med 19,10-versionen). Det påminner om både SMB Multichannel och Oracle Direct NFS.

Diagrammen jämför fördelarna med `nconnect` en icke ansluten monterad volym. I diagrammen genererade FIO arbets belastningen från en enda D32s_v3 instans i Azure-regionen USA – west2.

### <a name="linux-read-throughput"></a>Läs data flöde för Linux  

Följande diagram visar sekventiella läsningar av ~ 3 500 MiB/s-läsningar med `nconnect` , ungefär 2,3 x icke- `nconnect` .

![Läs data flöde för Linux](../media/azure-netapp-files/performance-benchmarks-linux-read-throughput.png)  

### <a name="linux-write-throughput"></a>Skriv data flöde för Linux  

I följande diagram visas sekventiella skrivningar. De anger att `nconnect` har ingen märkbar förmån för sekventiella skrivningar. 1 500 MiB/s är ungefär både den övre gränsen för sekventiell Skriv volym och den utgående gränsen för D32s_v3 instansen.

![Skriv data flöde för Linux](../media/azure-netapp-files/performance-benchmarks-linux-write-throughput.png)  

### <a name="linux-read-iops"></a>Linux Read IOPS  

Följande diagram visar slumpmässiga läsningar av ~ 200 000 Read IOPS med `nconnect` , ungefär 3x som inte är `nconnect` .

![Linux Read IOPS](../media/azure-netapp-files/performance-benchmarks-linux-read-iops.png)  

### <a name="linux-write-iops"></a>Linux Skriv IOPS  

Följande diagram visar slumpmässiga skrivningar på ~ 135 000 Skriv IOPS med `nconnect` , ungefär 3x som inte är `nconnect` .

![Linux Skriv IOPS](../media/azure-netapp-files/performance-benchmarks-linux-write-iops.png)  

## <a name="next-steps"></a>Nästa steg

- [Azure NetApp Files: få ut mesta möjliga av din moln lagring](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf?hsCtaTracking=f2f560e9-9d13-4814-852d-cfc9bf736c6a%7C764e9d9c-9e6b-4549-97ec-af930247f22f)
