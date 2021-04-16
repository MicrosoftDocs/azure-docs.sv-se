---
title: Mått för Azure NetApp Files | Microsoft Docs
description: Azure NetApp Files mått för allokerad lagring, faktisk lagringsanvändning, volym-IOPS och svarstider. Använd de här måtten för att förstå användning och prestanda.
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
ms.date: 04/12/2021
ms.author: b-juche
ms.openlocfilehash: b581470a886ff73739edfee7f45c64295eeeb1f0
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388622"
---
# <a name="metrics-for-azure-netapp-files"></a>Mått för Azure NetApp Files

Azure NetApp Files mått för allokerad lagring, faktisk lagringsanvändning, volym-IOPS och svarstider. Genom att analysera de här måtten kan du få en bättre förståelse för användningsmönster och volymprestanda för dina NetApp-konton.  

Du hittar mått för en kapacitetspool eller volym genom att välja **kapacitetspoolen** eller **volymen**.  Klicka sedan **på Mått** för att visa tillgängliga mått: 

[![Ögonblicksbild som visar hur du navigerar till pull-down-måttet. ](../media/azure-netapp-files/metrics-navigate-volume.png)](../media/azure-netapp-files/metrics-navigate-volume.png#lightbox)

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Användningsstatistik för kapacitetspooler

- *Allokerad storlek för pool*   
    Poolens etablerade storlek.

- *Pool allokerad till volymstorlek*  
    Den totala volymkvoten (GiB) i en viss kapacitetspool (det vill säga summan av volymerna etablerade storlekar i kapacitetspoolen).  
    Den här storleken är den storlek som du valde när du skapade volymen.  

- *Förbrukad poolstorlek*  
    Det totala logiska utrymmet (GiB) som används över volymer i en kapacitetspool.  

- *Total storlek på ögonblicksbilder för poolen*    
    Summan av ögonblicksbildstorleken från alla volymer i poolen.

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Användningsstatistik för volymer

- *Förbrukningsstorlek för procentvolym*    
    Procentandelen av den förbrukade volymen, inklusive ögonblicksbilder.  
- *Allokerad volymstorlek*   
    Den etablerade storleken på en volym
- *Volymkvotstorlek*    
    Den kvotstorlek (GiB) som volymen etableras med.   
- *Volymförbrukningsstorlek*   
    Volymens logiska storlek (använda byte).  
    Den här storleken omfattar logiskt utrymme som används av aktiva filsystem och ögonblicksbilder.  
- *Storlek på volymögonblicksbild*   
   Storleken på alla ögonblicksbilder i en volym.  

## <a name="performance-metrics-for-volumes"></a>Prestandamått för volymer

- *Genomsnittlig lässvarstid*   
    Genomsnittlig tid för läsningar från volymen i millisekunder.
- *Genomsnittlig skrivfördröjning*   
    Genomsnittlig tid för skrivningar från volymen i millisekunder.
- *Läsa IOPS*   
    Antalet läsningar till volymen per sekund.
- *Skriva IOPS*   
    Antalet skrivningar till volymen per sekund.
<!-- These two metrics are not yet available, until ~ 2020.09
- *Read MiB/s*   
    Read throughput in bytes per second.
- *Write MiB/s*   
    Write throughput in bytes per second.
--> 
<!-- ANF-4128; 2020.07
- *Pool Provisioned Throughput*   
    The total throughput a capacity pool can provide to its volumes based on "Pool Provisioned Size" and "Service Level".
- *Pool Allocated to Volume Throughput*   
    The total throughput allocated to volumes in a given capacity pool (that is, the total of the volumes' allocated throughput in the capacity pool).
-->

<!-- ANF-6443; 2020.11
- *Pool Consumed Throughput*    
    The total throughput being consumed by volumes in a given capacity pool.
-->


## <a name="volume-replication-metrics"></a><a name="replication"></a>Mått för volymreplikering

> [!NOTE] 
> * Nätverksöverföringens storlek (till  exempel måtten för total överföring av volymreplikering) kan skilja sig från käll- eller målvolymerna för en replikering mellan regioner. Det här beteendet beror på att en effektiv replikeringsmotor används för att minimera kostnaden för nätverksöverföring.
> * Mått för volymreplikering fylls för närvarande i för replikeringsmålvolymer och inte källan för replikeringsrelationen.

- *Är volymreplikeringsstatus felfri*   
    Villkoret för replikeringsrelationen. Ett felfritt tillstånd betecknas med `1` . Ett felaktigt tillstånd betecknas med `0` .

- *Överför volymreplikering*    
    Om statusen för volymreplikeringen är "överföring". 
 
- *Fördröjning för volymreplikering*   
    Hur lång tid i sekunder som data på speglingen ligger efter källan. 

- *Varaktighet för volymreplikering senaste överföring*   
    Hur lång tid i sekunder det tog för den senaste överföringen att slutföras. 

- *Volymreplikering, senaste överföringsstorlek*    
    Det totala antalet byte som överförts som en del av den senaste överföringen. 

- *Förlopp för volymreplikering*    
    Den totala mängden data som överförs för den aktuella överföringsåtgärden. 

- *Total överföring av volymreplikering*   
    Kumulativa byte som överförts för relationen. 

## <a name="next-steps"></a>Nästa steg

* [Förstå lagringshierarkin för Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)
* [Skapa en volym för Azure NetApp Files](azure-netapp-files-create-volumes.md)
